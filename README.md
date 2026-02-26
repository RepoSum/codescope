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
   - Guarda el resultado en base de datos.
4. Backend devuelve respuesta al frontend.
5. Usuario puede consultar historial y favoritos.
