# ChatGPT, Work y Codex

**Contexto:** usuario con plan ChatGPT Plus.

## Diferencias entre ChatGPT ordinario, Work y Codex

| Aspecto | ChatGPT ordinario (Chat) | ChatGPT Work | Codex |
|---|---|---|---|
| **Propósito** | Resolver preguntas y conversar | Completar tareas de varios pasos y entregar resultados revisables | Desarrollar software y realizar trabajo técnico |
| **Tareas típicas** | Explicar un concepto, buscar información, redactar o comparar opciones | Preparar un informe, analizar archivos, crear documentos o seguir un flujo de trabajo | Crear o modificar código, trabajar con repositorios y ejecutar herramientas de desarrollo |
| **Archivos** | Puede analizar archivos que adjuntes | Puede trabajar con archivos y producir entregables. En la app de escritorio también puede acceder a archivos locales cuando está habilitado | Trabaja con archivos de un proyecto o repositorio en su entorno |
| **Herramientas** | Herramientas de conversación disponibles, como búsqueda o aplicaciones conectadas | Herramientas, aplicaciones y permisos disponibles para completar la tarea | Terminal, repositorios y herramientas de programación disponibles en el entorno |
| **Dónde está disponible** | Web, móvil y aplicación de escritorio | Web, móvil y aplicación de escritorio, según el acceso de la cuenta | Aplicación de escritorio y Codex Cloud en web y móvil para tareas compatibles |
| **Ejemplo** | «¿Qué diferencia hay entre estos dos teléfonos?» | «Compara estos modelos y prepara una tabla con precios y fuentes» | «Crea una app .NET MAUI y ejecuta el proyecto» |

En resumen: Chat responde, Work realiza tareas amplias con resultados para revisar y Codex se especializa en desarrollo de software. Las funciones concretas dependen del plan, los permisos y el entorno.

## Diferencias entre ChatGPT web, ChatGPT Desktop y Codex

| Aspecto | ChatGPT en la web | ChatGPT Desktop | Codex |
|---|---|---|---|
| **Dónde se usa** | En el navegador | En la aplicación de escritorio | Como sección de desarrollo dentro de la aplicación de escritorio |
| **Modos disponibles** | Chat y Work | Chat, Work y Codex | Codex |
| **Uso principal** | Preguntas, búsqueda y conversación | Chat, trabajo con archivos y tareas más largas | Desarrollo de software y trabajo técnico |
| **Archivos locales y aplicaciones** | Work usa archivos y herramientas conectadas según disponibilidad | Work puede acceder a archivos y aplicaciones locales si están habilitados | Puede trabajar con archivos locales, repositorios, terminal y herramientas de desarrollo |
| **Crear un repositorio con `gh`** | No accede por sí solo a la terminal de tu computadora | Codex puede usar la terminal local cuando el entorno lo permite | Es una opción adecuada si `gh` está instalado, autenticado y disponible |
| **Continuar desde el móvil** | Chat y Work están disponibles en web móvil o la app | Work se sincroniza. El acceso remoto a Codex de escritorio está disponible para chats compatibles | Codex Cloud permite iniciar o continuar tareas compatibles desde web y móvil |
| **Depende de** | Plan, herramientas y configuración de la cuenta | Plan, permisos y herramientas habilitadas en la aplicación | Entorno de desarrollo, acceso al repositorio y herramientas disponibles |

## Crear un repositorio con ChatGPT Work, MCP de GitHub y Codex

| Opción | ¿Puede crear un repositorio? | Qué hace falta | ¿Usa CUA? |
|---|---|---|---|
| **ChatGPT Work con la app/conector GitHub disponible en esta sesión** | No con las herramientas que vimos aquí. El conector permitía consultar repositorios y modificar archivos, pero no ofrecía una acción de creación de repositorios | Una app MCP/conector de GitHub que exponga explícitamente la operación de crear repositorios y tenga permiso para hacerlo. Que sea MCP no garantiza por sí solo que esa operación esté disponible | No, si Work puede llamar directamente a esa herramienta |
| **ChatGPT Work con un MCP propio para GitHub** | Sí, si el servidor implementa la creación de repositorios y el usuario conecta y autoriza la cuenta | Servidor MCP accesible según los requisitos del plan y del espacio de trabajo, autenticación de GitHub y permisos de escritura adecuados | No, la operación se realiza mediante la herramienta MCP |
| **Codex en la computadora con GitHub CLI** | Sí, si Codex puede usar la terminal local y `gh` está instalado y autenticado con la cuenta correcta | Instalar GitHub CLI, ejecutar `gh auth login` y pedir a Codex crear el repositorio con el propietario y la visibilidad deseados | No hace falta. Codex usa el comando local `gh repo create` |
| **CUA en la interfaz de GitHub** | Sí, mediante los controles del sitio web | Sesión de navegador iniciada en GitHub y manejo de la interfaz | Sí |

En esta cuenta, la conexión inspeccionada apuntaba a `YAGAFMM`. En esta sesión, el conector disponible no mostró ninguna herramienta `create_repository` ni `create_repo`. Por eso no se podía crear el repositorio mediante MCP desde Work con esa conexión. Una integración MCP diferente, que incluya esa acción, podría hacerlo sin CUA.

### ¿CUA consume más tokens o procesamiento?

No se puede afirmar que CUA, por el solo hecho de usar la interfaz, consuma siempre más tokens. Las interacciones por computadora pueden incluir capturas de pantalla y pasos adicionales, así que una tarea puede requerir más contexto o más ciclos del agente que una llamada directa a una API o a MCP. El uso depende del modelo, la tarea y la información procesada. La documentación de uso de ChatGPT y Codex mide el uso en tokens, pero no publica una tarifa fija por acción de CUA que permita cuantificar esa diferencia.

### Elección para este caso

Para crear repositorios desde la computadora, la preferencia es **Codex con GitHub CLI**. Así Codex puede ejecutar `gh repo create` en la terminal, tras comprobar que `gh` está disponible y autenticado en la cuenta correcta. En la sesión actual, `gh` no estaba instalado o no era visible en la terminal revisada. No se instaló ni se configuró durante esta conversación.

**Nota sobre ChatGPT Plus:** Plus da acceso a funciones según disponibilidad y despliegue de la cuenta. Tener Plus por sí solo no añade una acción de creación al conector MCP de GitHub. Las herramientas habilitadas y los permisos siguen determinando lo que Work puede realizar.

## Referencias oficiales

- [ChatGPT Work y Codex](https://help.openai.com/en/articles/20001275-chatgpt-work-and-codex)
- [Conectar servidores MCP personalizados en ChatGPT](https://help.openai.com/en/articles/12584461-connecting-custom-mcp-servers-in-chatgpt)
- [Entender los límites de uso de ChatGPT y Codex](https://help.openai.com/en/articles/20001625-understanding-chatgpt-and-codex-usage-limits)
