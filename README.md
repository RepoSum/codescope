# CodeScope  
### IA para entender repositorios rápido

CodeScope es una aplicación web diseñada para analizar repositorios públicos de GitHub utilizando inteligencia artificial y generar un informe técnico estructurado.

El objetivo es reducir el tiempo que un desarrollador necesita para entender qué hace un proyecto, cómo está organizado y cuál es su nivel técnico.

---

## 🎯 Problema que resuelve

Entender un repositorio desconocido puede llevar horas:

- Leer múltiples archivos.
- Inferir arquitectura.
- Detectar problemas potenciales.
- Evaluar calidad del código.

CodeScope automatiza ese proceso generando un análisis que responde preguntas clave:

- ¿Qué hace el repositorio?
- ¿Está bien estructurado?
- ¿Cuál es el nivel técnico estimado?
- ¿Cuáles son los riesgos o debilidades?
- ¿Qué mejoras podrían implementarse?

---

## 🧠 Alcance del análisis

El informe generado por IA incluye:

### Summary general
- Descripción del propósito del repositorio.
- Evaluación estructural.
- Nivel técnico estimado.
- Recomendación general.

### Arquitectura detectada
Identificación de posibles capas como:
- Controllers  
- Services  
- Repositories  
- Models  
- Utils  

### Riesgos probables
Ejemplos:
- Falta de validación.
- Manejo insuficiente de errores.
- Endpoints sin autenticación.
- Posibles null references.

### Observaciones por archivo
- Top 3 observaciones por archivo.
- En repositorios grandes, solo archivos críticos.
- No se realiza análisis línea por línea.

El análisis completo se genera en formato JSON y el JSON crudo se almacena en base de datos.

---

## 🏗 Arquitectura General

CodeScope está diseñado con separación clara de responsabilidades entre frontend y backend.

### Frontend
- React (Vite)
- TypeScript
- TailwindCSS

Responsable de:
- Interfaz de usuario
- Manejo de estados (loading, error, done)
- Consumo de API
- Renderizado del análisis estructurado

---

### Backend
- FastAPI (Python)
- SQLAlchemy 2.0
- MySQL

Responsable de:
- Validación de requests
- Obtención de estructura del repositorio (GitHub)
- Integración con IA (Gemini)
- Generación del análisis estructurado
- Persistencia del JSON crudo
- Gestión de autenticación

Estructura modular prevista:

```bash
app/
  main.py
  routers/
  services/
  repositories/
  models/
  schemas/
  core/

```

### Autenticación

- JWT Access Token (15 minutos)
- Refresh Token (14 días)
- Refresh almacenado en cookie HttpOnly
- Rotación de refresh tokens
- Hash persistido en MySQL

Esto permite:
- Seguridad básica para entorno real
- Protección de endpoints
- Asociación análisis ↔ usuario

---

### Integración con IA

- Modelo: Gemini
- Integrado como módulo interno del backend
- Manejo de límites de tokens
- Manejo de errores externos (rate limit, fallos de API)

---

## 🔄 Flujo principal del sistema

1. Usuario ingresa URL de un repositorio público.
2. Frontend envía request al backend.
3. Backend:
   - Valida la entrada.
   - Obtiene la estructura del repositorio.
   - Ejecuta módulo de IA.
   - Genera análisis estructurado en JSON.
4. Backend devuelve respuesta al frontend.
5. Usuario puede consultar historial y favoritos.   

---

## 🧱 Desarrollo incremental por MVPs

CodeScope no se desarrolló como un proyecto monolítico desde el inicio.  
Fue diseñado y planificado mediante entregas incrementales (MVPs), permitiendo validar cada etapa antes de avanzar.

### MVP 0 — Demo sin IA real
- Endpoint mock `/analysis/mock`
- Validaciones básicas
- Renderizado de respuesta simulada en frontend
- Manejo simple de estados (loading / error)

Objetivo: validar arquitectura frontend ↔ backend antes de integrar IA real.

---

### MVP 1 — Integración real con GitHub e IA
- Obtención real de estructura del repositorio
- Integración con modelo Gemini
- Generación de JSON estructurado
- Persistencia del análisis en MySQL
- Renderizado real en frontend

Objetivo: construir el flujo funcional completo.

---

### MVP 2 — Autenticación e historial por usuario
- Registro e inicio de sesión (JWT)
- Access token (15 min)
- Refresh token (14 días) con rotación
- Protección de endpoints
- Asociación análisis ↔ usuario
- Historial individual

Objetivo: agregar seguridad y persistencia por usuario.

---

### MVP 3 — Calidad, errores y observabilidad
- Manejo explícito de errores externos (GitHub / IA)
- Reintentos controlados
- Límite de tokens
- Logs útiles y trazabilidad
- Estados claros en UI (PROCESSING / DONE / ERROR)
- Test cases manuales definidos
- Candidatos a automatización para detección de regresiones

Objetivo: robustecer el sistema y prepararlo para uso real.

---

## 📋 Gestión del proyecto (Azure DevOps)

El desarrollo está organizado formalmente mediante Azure Boards.

La estructura incluye:

- Epic general del producto.
- Features organizadas por MVP.
- Historias de usuario con criterios de aceptación.
- Tareas técnicas descompuestas (backend, frontend, base de datos).
- Test Cases creados antes de la implementación.

Ejemplos de historias definidas:

- US-01 — Generar análisis mock  
- US-02 — Guardar análisis generado  
- US-08 — Registro e inicio de sesión (JWT)  
- US-14 — Manejo de errores externos y reintentos  
- US-16 — Observabilidad y trazabilidad  

Se incluyen capturas del backlog y de los Test Cases como evidencia del proceso de planificación.   

### Backlog definido en Azure DevOps

El proyecto fue planificado utilizando Azure Boards,
organizando el trabajo mediante User Stories y tareas técnicas
antes de comenzar la implementación.

![Backlog](docs/screenshots/backlog.png)

### Ejemplo de desglose de una historia de usuario

Cada funcionalidad se divide en tareas pequeñas separando
responsabilidades de frontend y backend.

![User Story Tasks](docs/screenshots/user-story.png)

---

## 🔎 Enfoque de calidad y QA

Desde la etapa de planificación, CodeScope fue diseñado considerando calidad del sistema y validación funcional antes de comenzar la implementación.

El objetivo no fue únicamente desarrollar funcionalidades, sino reducir riesgos técnicos desde el diseño.

### QA desde la planificación

Antes de escribir código se definieron:

- Historias de usuario con criterios de aceptación.
- Casos de prueba manuales.
- Escenarios de error.
- Validaciones de API.
- Flujos end-to-end del sistema.

Los Test Cases fueron gestionados en Azure DevOps utilizando Work Items de tipo **Test Case**, permitiendo validar el comportamiento esperado del sistema desde etapas tempranas.

---

### Tipos de pruebas consideradas

#### QA Manual
Validación del flujo completo del usuario:

- Generación de análisis.
- Manejo de URLs inválidas.
- Respuestas de error.
- Protección de endpoints.
- Estados de interfaz (loading / error / success).

El objetivo es verificar que el sistema funcione correctamente desde la perspectiva del usuario final.

---

#### QA Automatizada (nivel inicial)

Se definieron pruebas automatizables orientadas a detectar regresiones rápidamente:

- Smoke tests de endpoints principales.
- Validación de autenticación JWT.
- Respuestas HTTP esperadas.
- Manejo básico de seguridad.

Estas pruebas permiten detectar cuando un cambio rompe funcionalidades previamente estables.

---

### Quality mindset aplicado

El diseño del sistema contempla:

- Validación explícita de inputs.
- Manejo de errores externos (GitHub / IA).
- Observabilidad mediante logs.
- Control de estados del sistema.
- Separación clara de responsabilidades.
- Testing como parte del diseño, no como paso final.

Este enfoque busca aproximarse a prácticas utilizadas en entornos reales de desarrollo.

---

## 🧠 Más allá de un CRUD tradicional

Aunque CodeScope presenta una interfaz simple para el usuario, el proyecto aborda problemas que van más allá de una aplicación CRUD clásica.

Un CRUD típico generalmente consiste en:
- Crear, leer, actualizar y eliminar datos.
- Persistencia directa en base de datos.
- Lógica de negocio limitada.

CodeScope introduce desafíos adicionales propios de sistemas reales:

### Integración con servicios externos
El sistema depende de múltiples componentes externos:

- API de GitHub para obtención de repositorios.
- Modelo de inteligencia artificial (Gemini).
- Manejo de fallos y respuestas impredecibles.

Esto obliga a considerar:
- Timeouts
- errores externos
- reintentos controlados
- validación de respuestas

---

### Procesamiento no determinístico

A diferencia de un CRUD tradicional, el resultado del análisis depende de un modelo de IA.

El sistema debe:
- estructurar respuestas generadas dinámicamente,
- validar resultados,
- almacenar el JSON crudo para trazabilidad,
- permitir futuras mejoras del análisis.

---

### Diseño orientado a escalabilidad conceptual

Desde el diseño se contemplan aspectos como:

- separación frontend / backend,
- arquitectura modular,
- manejo de límites de tokens,
- observabilidad mediante logs,
- autenticación segura mediante JWT + refresh tokens.

---

### Enfoque incremental real

El proyecto fue desarrollado mediante MVPs progresivos:

1. Validación de arquitectura.
2. Integración funcional.
3. Seguridad por usuario.
4. Calidad, testing y observabilidad.

Este enfoque replica prácticas comunes en equipos de desarrollo profesionales.

---

## 📊 Estado actual del proyecto

Actualmente CodeScope se encuentra en desarrollo activo siguiendo el roadmap definido.

Estado general:

- ✅ Arquitectura definida
- ✅ Backlog estructurado en Azure DevOps
- ✅ Historias de usuario y tareas técnicas definidas
- ✅ Test Cases manuales y automatizables planificados
- ⏳ Implementación en progreso (MVP 0)
- 🔜 Integración real con repositorios e IA

El objetivo actual es validar completamente el flujo técnico antes de optimizar performance y experiencia de usuario.

---

## 🗺 Roadmap técnico

### Próximos pasos

- Implementar endpoint mock de análisis.
- Conectar frontend con backend.
- Persistir resultados en base de datos.
- Integrar análisis real mediante IA.
- Incorporar autenticación JWT.
- Implementar historial por usuario.
- Mejorar manejo de errores externos.
- Agregar observabilidad y logging.

### Evolución futura posible

- Soporte para múltiples proveedores de repositorios.
- Comparación entre versiones de análisis.
- Métricas automáticas de calidad de código.
- Caché de análisis para repositorios frecuentes.
- Optimización de costos y uso de tokens IA.

---

## 📦 Estructura del proyecto

El sistema está organizado en múltiples repositorios dentro de una organización de GitHub.

### Repositorios principales

**codescope**
Repositorio central de documentación y arquitectura del proyecto.

**codescope-backend**
Backend desarrollado con FastAPI responsable de:
- lógica de negocio,
- integración con GitHub,
- análisis mediante IA,
- autenticación,
- persistencia de datos.

**codescope-frontend**
Aplicación web desarrollada con React + Vite encargada de:
- interacción con el usuario,
- visualización del análisis,
- gestión de estados de interfaz.

---

## 👨‍💻 Sobre el proyecto

CodeScope es un proyecto personal orientado a explorar el diseño de sistemas modernos que integran inteligencia artificial dentro de flujos reales de desarrollo de software.

El foco principal del proyecto es:

- diseño incremental,
- separación de responsabilidades,
- calidad desde la planificación,
- integración con servicios externos,
- construcción de un sistema mantenible.

El desarrollo se documenta públicamente como parte de un proceso de aprendizaje y crecimiento profesional.

---

## ⚖️ Decisiones técnicas y trade-offs

Durante el diseño de CodeScope se tomaron decisiones conscientes considerando simplicidad inicial, aprendizaje y posibilidad de evolución futura.

### Backend en FastAPI (Python)

**Decisión**
Utilizar FastAPI como base del backend.

**Motivo**
- Integración natural con modelos de IA.
- Alto rendimiento para APIs.
- Tipado mediante Pydantic.
- Simplicidad para iterar rápidamente.

**Trade-off**
Python no siempre es la opción más común en arquitecturas enterprise tradicionales, pero reduce significativamente la complejidad al trabajar con IA.

---

### IA integrada dentro del backend

**Decisión**
Inicialmente integrar el módulo de IA dentro del mismo backend en lugar de un microservicio separado.

**Motivo**
- Reducir complejidad operativa inicial.
- Simplificar despliegue.
- Validar primero el flujo funcional.

**Trade-off**
A futuro podría separarse como servicio independiente si el sistema escala.

---

### Persistencia del JSON crudo del análisis

**Decisión**
Guardar la respuesta completa generada por la IA.

**Motivo**
- Trazabilidad.
- Debugging.
- Posibilidad de reprocesar análisis en el futuro.
- Auditoría de resultados.

**Trade-off**
Mayor uso de almacenamiento frente a guardar solo datos resumidos.

---

### Autenticación JWT + Refresh Tokens

**Decisión**
Implementar access token corto y refresh token rotativo.

**Motivo**
- Modelo utilizado en aplicaciones reales.
- Mejora de seguridad.
- Separación entre sesión y autenticación.

**Trade-off**
Mayor complejidad respecto a autenticación básica.

---

### Desarrollo guiado por MVPs

**Decisión**
Construir el sistema mediante entregas incrementales.

**Motivo**
- Reducir riesgo técnico.
- Validar arquitectura temprano.
- Evitar sobreingeniería inicial.

**Trade-off**
Algunas partes del sistema evolucionan y se refactorizan entre MVPs.


---

## 🧭 Nota personal

CodeScope forma parte de mi proceso de aprendizaje mientras busco consolidar habilidades en desarrollo backend, testing y diseño de sistemas.

Antes de comenzar a programar, decidí planificar el proyecto como se trabaja en entornos reales: definiendo MVPs, backlog, historias de usuario y casos de prueba desde el inicio.

El objetivo no es solo construir una aplicación funcional, sino aprender a pensar cómo se diseña, organiza y mejora un sistema de software paso a paso.

El proyecto continúa evolucionando a medida que avanzo en la implementación y aprendizaje.
