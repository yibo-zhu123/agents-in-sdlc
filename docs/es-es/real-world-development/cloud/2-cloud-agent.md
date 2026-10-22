---
title: "Ejercicio 2: Agente de GitHub Copilot en la nube"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Lección anterior: instrucciones personalizadas][previous-lesson] |
|:--|

Es probable que pocas organizaciones estén libres de deuda técnica. Puede tratarse de problemas de seguridad sin resolver, código heredado que necesita actualizarse o solicitudes de funcionalidad que llevan tiempo en el backlog porque no había tiempo para implementarlas. El agente de GitHub Copilot en la nube está diseñado para tareas como actualizar código y añadir funciones de forma autónoma. Cuando termina, crea una solicitud de incorporación de cambios (PR) en borrador para que la revise un desarrollador. Así puedes delegar tareas tediosas y acelerar el desarrollo, mientras el equipo se centra en objetivos más amplios.

Con el agente de Copilot en la nube explorarás:

- cómo personalizar el entorno para generar código;
- cómo asegurarte de que las operaciones se realicen de forma segura;
- la importancia de delimitar claramente las incidencias;
- cómo asignar incidencias a Copilot.

## Escenarios

Tailspin Toys tiene deuda técnica que quiere resolver. Los contratistas que desarrollaron la primera versión del sitio dejaron la documentación en mal estado; en concreto, no hay documentación. Como primer paso, el equipo quiere añadir comentarios TSDoc a todas las funciones exportadas de la aplicación.

Además, el equipo de diseño quiere mejorar la búsqueda de juegos. Le gustaría que la página de detalles de cada juego mostrara otros títulos de la misma categoría para que los usuarios puedan seguir explorando. Por ahora no hace falta una implementación pulida: basta con algo que se pueda usar para aceptar y probar la experiencia de usuario. Aunque es un impedimento, en este momento hay otras incidencias más prioritarias.

Estas tareas suelen quedar relegadas y son buenos ejemplos de trabajo que se puede asignar al agente de Copilot en la nube. El agente puede trabajar en ellas de forma asíncrona mientras el desarrollador se ocupa de otras tareas y, más adelante, revisar el trabajo de Copilot.

## Introducción al agente de GitHub Copilot en la nube

El [agente de GitHub Copilot en la nube](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) puede realizar tareas en segundo plano de forma parecida a un desarrollador. Puedes hacerlo de distintas maneras, por ejemplo, [asignando una incidencia de GitHub a Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions). Una vez asignada, Copilot crea una solicitud de incorporación de cambios en borrador para seguir el progreso, configura un entorno y empieza a trabajar. Puedes consultar la sesión mientras se ejecuta o después de que termine. Cuando la solución esté lista para revisarse, Copilot te mencionará en la PR.

## La importancia de delimitar bien las instrucciones

GitHub Copilot no hace magia: no puedes saltarte el trabajo de análisis. Incluso las tareas que parecen sencillas pueden volverse complejas al examinarlas a fondo, por lo que debes tener cuidado al [delimitar las tareas para el agente de Copilot en la nube][cloud-agent-best-practices]. Trátalo como a un compañero de programación con IA: trabaja por etapas, aprende, experimenta y adapta el enfoque. Los fundamentos del desarrollo de software no cambian al incorporar IA generativa.

## Instrucciones personalizadas en este repositorio

En ejercicios anteriores has conocido las instrucciones personalizadas y cómo orientan a Copilot. Si ya has completado un ejercicio práctico de instrucciones personalizadas, habrás visto cómo funciona `.github/copilot-instructions.md`; si no, puedes consultarlo ahora. Antes de asignar trabajo al agente de Copilot en la nube, revisa en modo de solo lectura los archivos de instrucciones incluidos en el repositorio para que después puedas observar su efecto.

Abre los siguientes archivos en la interfaz web de GitHub o en un codespace que ya esté en ejecución:

- `.github/copilot-instructions.md`: revisa la sección **Code standards**, en especial las convenciones de TypeScript y los comentarios TSDoc.
- `.github/instructions/unit-tests.instructions.md`: observa el campo `applyTo`, que aplica estas instrucciones a los archivos `**/*.test.ts`.

Cuando asignes la incidencia `Code lacks documentation` al agente en la nube en la siguiente sección, observa en la PR los comentarios TSDoc, las convenciones de TypeScript y los encabezados de comentario. Estos requisitos proceden de los archivos de instrucciones. Volveremos a indicarlo al revisar las PR en un ejercicio posterior.

## Configura el entorno de desarrollo del agente de Copilot en la nube

Para crear código, normalmente se necesita un entorno específico y ejecutar algunos scripts de configuración que garanticen que todo está listo. Lo mismo ocurre al asignar tareas a Copilot, que trabaja de forma similar a un ingeniero de software.

El agente en la nube usa [GitHub Actions][github-actions] para configurar su entorno. Puedes personalizarlo con un [flujo de trabajo de configuración][setup-workflow] especial, definido en `.github/workflows/copilot-setup-steps.yml`, que se ejecuta antes de que empiece a trabajar. Así se le proporcionan las herramientas y dependencias necesarias para el desarrollo. El flujo ya está configurado para este laboratorio: instala Node.js y las dependencias del proyecto, los binarios del navegador para las pruebas de extremo a extremo y la base de datos SQLite migrada y poblada de la aplicación Astro:

```yaml
name: "Copilot Setup Steps"

# Permite probar los pasos de configuración desde la pestaña Actions del repositorio
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Establece los permisos mínimos necesarios para estos pasos. Copilot recibirá su propio token para sus operaciones.
    permissions:
      # Si los pasos de configuración clonan el repositorio, por ejemplo, para instalar dependencias, necesitarás el permiso contents: read. Si no lo clonas, Copilot lo hará automáticamente cuando terminen los pasos.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # Configuración de la aplicación: Node.js (la aplicación completa ahora es Astro + Drizzle/libSQL)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # Migra y carga datos iniciales en la base de datos SQLite local para que haya datos disponibles en compilaciones y pruebas.
      - name: Set up the database
        run: npm run db:setup
```

Este archivo se parece a cualquier otro flujo de GitHub, pero incluye varios puntos importantes:

- Tiene un único trabajo llamado `copilot-setup-steps`. Se ejecuta en GitHub Actions antes de que Copilot empiece a trabajar en la solicitud de incorporación de cambios.
- Observa el activador `workflow_dispatch`, que permite ejecutar el flujo manualmente desde la pestaña **Actions** del repositorio. Resulta útil para probarlo sin esperar a que Copilot lo ejecute.

## Añade documentación

Todos sabemos que la documentación es importante, pero muchos proyectos tienen información obsoleta o carecen de ella. Esta deuda técnica reduce la productividad y dificulta el mantenimiento del código o la incorporación de desarrolladores nuevos. Copilot puede ayudar a generar documentación, por lo que esta es una buena incidencia para asignarle. Trabajará en segundo plano; en un ejercicio posterior revisarás el resultado.

1. Abre una nueva pestaña del navegador y ve al repositorio en github.com.
2. Selecciona la pestaña **Issues**.
3. Selecciona **New issue** para abrir el cuadro de diálogo de incidencias.
4. Selecciona **Blank issue**.
5. En **Title**, escribe `Code lacks documentation`.
6. En **Description**, escribe:

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. Selecciona **Create** para crear la incidencia.
8. En el lado derecho, selecciona **Assign to Copilot** para abrir el cuadro de asignación.

   ![Asignación de Copilot a una incidencia](../../../_images/shared-assign-copilot.png)

9. Selecciona **Assign**.
10. Selecciona la pestaña **Pull Requests**.
11. Abre la PR recién generada, cuyo título será parecido a `[WIP]: Code lacks documentation`. Si no aparece ninguna PR nueva, espera uno o dos minutos y actualiza el navegador.
12. En unos minutos, Copilot debería crear una lista de tareas.

> [!NOTE]
> La lista de tareas de Copilot puede tardar varios minutos en aparecer en la PR. Copilot está creando su entorno (ejecuta el flujo que se ha descrito), analizando el proyecto y determinando la mejor forma de resolver la incidencia.

13. Revisa la lista de tareas y las acciones que Copilot tiene previsto realizar.
14. Desplázate por la actividad de la PR; deberías ver una actualización que indica que Copilot ha empezado a trabajar en la incidencia.
15. Selecciona el botón **View session**.

   ![Vista de la sesión de Copilot](../../../_images/ex4-view-session.png)

> [!CAUTION]
> Es posible que tengas que actualizar la ventana para ver el indicador actualizado.

16. Observa la sesión en directo y cómo Copilot resuelve el problema: explora el código, analiza el estado, se detiene para pensar y decidir el plan adecuado, y crea código.

El proceso probablemente tardará varios minutos. Una de las principales ventajas del agente de Copilot en la nube es que puede trabajar de forma asíncrona mientras tú te ocupas de otras tareas. Aprovecharemos esa función asignándole otra tarea y, después, nos centraremos en añadir funciones a la aplicación.

## Añade una sección de juegos relacionados a los detalles de cada juego

Una de las grandes ventajas del agente de Copilot en la nube es que permite repartir el trabajo. Mientras el agente se ocupa de una tarea, tú puedes trabajar en otra. Aunque añadir una sección de juegos relacionados quizá no tarde mucho, ese tiempo se puede dedicar a otra tarea. Asignemos la incidencia al agente.

1. Vuelve al repositorio en github.com.
2. Selecciona la pestaña **Issues**.
3. Selecciona **New issue** para abrir el cuadro de diálogo de incidencias.
4. Selecciona **Blank issue**.
5. En **Title**, escribe `Show related games on the game details page`.
6. En **Description**, escribe:

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. Selecciona **Create** para crear la incidencia.
8. En el lado derecho, selecciona **Assign to Copilot** para abrir el cuadro de asignación.

   ![Asignación de Copilot a una incidencia](../../../_images/shared-assign-copilot.png)

9. Selecciona **Assign**.

Poco después, aparecerán varios 👀 en el primer comentario de la incidencia para indicar que Copilot está trabajando.

![Copilot usa el emoji de ojos para indicar que está trabajando en la incidencia](../../../_images/ex4-issue-eyes-emoji.png)

Copilot ya trabaja en tu segunda solicitud. El agente en la nube trabaja de forma similar a un ingeniero de software, así que no tienes que supervisarlo continuamente; puedes revisar el resultado cuando termine. Ahora, vamos a crear y usar agentes personalizados.

## Resumen y siguientes pasos

En esta lección has explorado el [agente de GitHub Copilot en la nube][copilot-agents]. Puedes asignar incidencias a Copilot para que trabaje en ellas de forma asíncrona y aborde deuda técnica, cree funciones o ayude a migrar código de un framework a otro.

Has explorado estos conceptos:

- cómo personalizar el entorno para generar código;
- cómo asegurarte de que las operaciones se realicen de forma segura;
- la importancia de delimitar claramente las incidencias;
- cómo asignar incidencias a Copilot.

Mientras el agente trabaja, puedes crear y usar agentes personalizados. El [agente de Copilot en la nube también puede usar servidores MCP][cloud-agent-mcp] y tiene acceso a las instrucciones personalizadas que exploraste en módulos anteriores.

## Recursos

- [Información sobre los agentes de Copilot][copilot-agents]
- [Asignar incidencias de GitHub a Copilot][assign-issue]
- [Prácticas recomendadas para el flujo de configuración del agente en la nube][cloud-agent-best-practices]

| [Siguiente lección: agentes personalizados →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
