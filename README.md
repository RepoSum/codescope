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
