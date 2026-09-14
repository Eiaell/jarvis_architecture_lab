# Jarvis — Sistema Personal de IA Agéntica

[English](README.md) · [Español](README.es.md) · [Deutsch](README.de.md)

> Proyecto en desarrollo.

Jarvis es un sistema personal de IA agéntica que estoy construyendo como entorno práctico para aprender cómo diseñar agentes de IA confiables capaces de mantener contexto, trabajar con memoria, utilizar herramientas y ejecutar flujos de trabajo de varios pasos.

El proyecto comenzó a partir de una pregunta:

**¿Cómo puede un asistente de IA volverse más útil con el tiempo sin depender únicamente de prompts cada vez más grandes o de un comportamiento no controlado del LLM?**

## Qué estoy explorando

Jarvis funciona actualmente como un entorno de aprendizaje y experimentación para trabajar con:

- memoria persistente
- recuperación estructurada de contexto
- separación entre memoria y contexto activo
- ejecución de herramientas
- flujos de trabajo de varios pasos
- comportamiento determinista frente a probabilístico
- permisos y límites del agente
- validación y evaluación
- patrones arquitectónicos reutilizables

## Estado actual

Jarvis se encuentra en desarrollo activo.

El objetivo de este repositorio no es presentar un producto terminado.

Aquí documento la arquitectura, las decisiones, los experimentos, los errores y las lecciones aprendidas durante la construcción del sistema.

## Idea central

Un LLM no debería ser responsable de todas las garantías de un sistema agéntico.

Algunas tareas son adecuadas para el razonamiento probabilístico.

Otras necesitan software determinista, validaciones explícitas y ejecución controlada.

Una parte importante de este proyecto consiste en aprender dónde debe estar esa frontera.

## Arquitectura de alto nivel

User  
↓  
Interaction Layer  
↓  
Context Retrieval  
↓  
Memory System  
↓  
Reasoning / Decision Layer  
↓  
Tool Execution  
↓  
Validation  
↓  
Result

La arquitectura exacta continúa evolucionando a medida que el sistema es probado.

## Qué contiene este repositorio

Este repositorio público contiene:

- documentación arquitectónica
- decisiones de diseño
- ejemplos sanitizados
- enfoques de evaluación
- lecciones aprendidas
- patrones reutilizables

De forma intencional, **no contiene**:

- memorias personales
- datos privados de usuarios
- credenciales o API keys
- configuraciones de producción
- prompts privados
- logs sensibles

## Por qué estoy construyendo Jarvis

Estoy orientando cada vez más mi perfil hacia la inteligencia artificial, la automatización y los sistemas digitales.

En lugar de aprender únicamente mediante cursos, utilizo Jarvis como un proyecto práctico en el que puedo enfrentar problemas arquitectónicos reales, probar diferentes enfoques y documentar qué funciona y qué no.

Mi objetivo es convertir progresivamente lo aprendido con Jarvis en un blueprint reutilizable para construir agentes personales o especializados en diferentes dominios.

## Herramientas

El proyecto se desarrolla y explora utilizando herramientas como:

- Claude Code
- OpenAI Codex
- Git
- GitHub
- Python y otras herramientas cuando son necesarias

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

## Aviso

Jarvis es un proyecto personal y experimental y, en su estado actual, no debe considerarse un agente autónomo listo para producción.
