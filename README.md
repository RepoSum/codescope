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
