# Jarvis — Sistema Personal de IA Agéntica

[English](README.md) · [Español](README.es.md) · [Deutsch](README.de.md)

> **Repositorio de solo lectura / portafolio.** Este repositorio documenta la arquitectura de Jarvis, un asistente de IA agéntico personal. No es el sistema real en producción: es una versión pública y sanitizada, pensada para mostrar decisiones de diseño, no para ejecutarse. La implementación privada se mantiene en un repositorio separado. Ver [SECURITY.md](SECURITY.md) para el detalle de qué se publica y qué no.

## Tabla de contenido

- [Qué es Jarvis](#qué-es-jarvis)
- [Qué problema resuelve](#qué-problema-resuelve)
- [Qué estoy explorando](#qué-estoy-explorando)
- [Arquitectura de alto nivel](#arquitectura-de-alto-nivel)
- [Decisiones de arquitectura destacadas](#decisiones-de-arquitectura-destacadas)
- [Documentación técnica](#documentación-técnica)
- [Qué contiene y qué no contiene este repositorio](#qué-contiene-y-qué-no-contiene-este-repositorio)
- [Filosofía de documentación](#filosofía-de-documentación)
- [Herramientas](#herramientas)
- [Estado actual y aviso](#estado-actual-y-aviso)

## Qué es Jarvis

Jarvis es un sistema personal de IA agéntica que estoy construyendo como entorno práctico para aprender a diseñar agentes de IA confiables: capaces de mantener contexto, trabajar con memoria persistente, usar herramientas externas y ejecutar flujos de trabajo de varios pasos sin supervisión constante.

El proyecto nació de una pregunta concreta:

> **¿Cómo puede un asistente de IA volverse más útil con el tiempo sin depender únicamente de prompts cada vez más grandes, ni de un comportamiento no controlado del modelo?**

## Qué problema resuelve

La mayoría de los asistentes basados en LLM pierden contexto entre sesiones, no distinguen entre "recordar algo" y "tenerlo en la ventana de contexto actual", y delegan al modelo decisiones que deberían ser deterministas (cálculos, validaciones, permisos). Jarvis explora cómo separar esas responsabilidades: qué debe resolver el modelo con razonamiento probabilístico, y qué debe resolver código determinista con reglas explícitas.

## Qué estoy explorando

Jarvis funciona como un entorno de aprendizaje y experimentación activo sobre:

- memoria persistente
- recuperación estructurada de contexto
- separación entre memoria y contexto activo
- ejecución de herramientas
- flujos de trabajo de varios pasos
- comportamiento determinista frente a probabilístico
- permisos y límites del agente
- validación y evaluación
- patrones arquitectónicos reutilizables

## Arquitectura de alto nivel

```
Usuario
  ↓
Capa de Interacción
  ↓
Recuperación de Contexto
  ↓
Sistema de Memoria
  ↓
Capa de Razonamiento / Decisión
  ↓
Ejecución de Herramientas
  ↓
Validación
  ↓
Resultado
```

La arquitectura exacta continúa evolucionando a medida que el sistema se prueba en la práctica. El detalle completo, capa por capa, está en [`docs/architecture/system-overview.md`](docs/architecture/system-overview.md).

## Decisiones de arquitectura destacadas

Para quien evalúa el nivel técnico del proyecto, estas son las decisiones que más definen a Jarvis:

- **El modelo no es responsable de toda garantía del sistema.** La aritmética, las reglas de negocio y los límites de permisos los ejecuta código determinista; el LLM propone, el código decide. Ver [`docs/05-deterministic-boundaries.md`](docs/05-deterministic-boundaries.md).
- **Memoria separada del contexto activo.** Persistir algo no significa que el modelo lo "recuerde" en cada turno: hay una capa explícita de recuperación que decide qué entra al prompt y por qué. Ver [`docs/02-memory-architecture.md`](docs/02-memory-architecture.md) y [`docs/03-context-retrieval.md`](docs/03-context-retrieval.md).
- **Los secretos nunca llegan al modelo.** El LLM recibe acceso a una *capacidad* (una herramienta), no al secreto que esa herramienta necesita para ejecutarse. Ver la sección de secretos en [`docs/04-tool-execution.md`](docs/04-tool-execution.md) y en [SECURITY.md](SECURITY.md).
- **Todo lo que decide, predice o ejecuta se valida antes de confiar en él.** Enfoques de evaluación y criterios de aceptación explícitos antes de dar por buena una capacidad nueva. Ver [`docs/06-evaluations.md`](docs/06-evaluations.md).
- **Los fallos se documentan, no se ocultan.** Cada lección aprendida —incluyendo lo que no funcionó— queda registrada como insumo para la siguiente decisión. Ver [`docs/07-lessons-learned.md`](docs/07-lessons-learned.md).

## Documentación técnica

| Documento | Contenido |
|---|---|
| [`docs/01-overview.md`](docs/01-overview.md) | Visión general de la arquitectura |
| [`docs/02-memory-architecture.md`](docs/02-memory-architecture.md) | Arquitectura de memoria persistente |
| [`docs/03-context-retrieval.md`](docs/03-context-retrieval.md) | Recuperación estructurada de contexto |
| [`docs/04-tool-execution.md`](docs/04-tool-execution.md) | Ejecución de herramientas y manejo de secretos |
| [`docs/05-deterministic-boundaries.md`](docs/05-deterministic-boundaries.md) | Límites entre lo determinista y lo probabilístico |
| [`docs/06-evaluations.md`](docs/06-evaluations.md) | Métodos de evaluación y criterios de aceptación |
| [`docs/07-lessons-learned.md`](docs/07-lessons-learned.md) | Lecciones aprendidas (documento vivo) |
| [`docs/architecture/system-overview.md`](docs/architecture/system-overview.md) | Arquitectura completa del sistema, capa por capa |

## Qué contiene y qué no contiene este repositorio

Este repositorio público contiene documentación arquitectónica, decisiones de diseño, ejemplos sanitizados, enfoques de evaluación, lecciones aprendidas y patrones reutilizables.

De forma intencional, **no** contiene memorias personales, datos privados de usuarios, credenciales o API keys, configuración de producción, prompts privados ni logs sensibles.

El detalle completo de qué se publica, qué no, y por qué, está en [SECURITY.md](SECURITY.md).

## Filosofía de documentación

Para cada componente importante intento documentar:

1. El problema que resuelve
2. Por qué existe el componente
3. Alternativas consideradas
4. El enfoque elegido
5. Qué debe ser determinista
6. Qué puede permanecer probabilístico
7. Modos de fallo
8. Pruebas y evaluaciones
9. Criterios de aceptación
10. Cuándo el patrón debería y no debería reutilizarse

## Herramientas

El proyecto se desarrolla y explora utilizando herramientas como:

- Claude Code
- OpenAI Codex
- Git
- GitHub
- Python y otras herramientas de soporte cuando son necesarias

## Estado actual y aviso

Jarvis se encuentra en desarrollo activo. El objetivo de este repositorio no es presentar un producto terminado, sino documentar la arquitectura, las decisiones, los experimentos, los errores y las lecciones aprendidas durante la construcción del sistema.

Jarvis es un proyecto personal y experimental. En su estado actual no debe considerarse un agente autónomo listo para producción, ni este repositorio debe interpretarse como una auditoría de seguridad formal (ver [SECURITY.md](SECURITY.md)).
