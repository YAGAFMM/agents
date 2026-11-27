# Caso práctico: asistente personal de tareas con IA

Se plantea un sistema integrado por ChatGPT, Notion y Google Calendar, cuyo objetivo es gestionar y planificar tareas personales mediante lenguaje natural.

## Participantes

- **Usuario:** solicita la planificación de sus tareas.
- **ChatGPT:** interpreta, analiza, prioriza y distribuye las tareas.
- **Notion:** almacena y gestiona las tareas.
- **Google Calendar:** gestiona la disponibilidad y los bloques de trabajo.

## Diagrama de secuencia

```mermaid
sequenceDiagram
    autonumber
    actor U as Usuario
    participant C as ChatGPT
    participant N as Notion
    participant G as Google Calendar

    U->>C: Organizar las próximas 2 horas
    activate C

    C->>N: Consultar tareas pendientes
    N-->>C: Lista de tareas

    C->>G: Consultar disponibilidad
    G-->>C: Horarios libres

    C->>C: Priorizar y distribuir tareas

    C->>G: Crear bloques de trabajo
    G-->>C: Eventos creados

    C->>N: Actualizar tareas programadas
    N-->>C: Confirmación

    C-->>U: Presentar planificación final
    deactivate C
```

## Procesamiento interno

La autollamada de ChatGPT representa las operaciones internas necesarias para elaborar la planificación:

- Analizar las tareas pendientes.
- Evaluar prioridades y fechas límite.
- Estimar la duración de las tareas.
- Seleccionar las tareas compatibles con el tiempo disponible.
- Determinar el orden de ejecución.
- Elaborar la distribución temporal.

Estas operaciones pueden representarse mediante una única autollamada o mediante varias, dependiendo del nivel de detalle requerido.

El procesamiento interno es la operación que se realiza, mientras que el mensaje a sí mismo es el elemento gráfico que permite representarla.
