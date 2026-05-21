# 🎬 MoodFlix

> **Proyecto Integrador - Unidad 12: Fundamentos del Proyecto (Post 1)**
>
> MoodFlix es una plataforma móvil innovadora enfocada en la recomendación inteligente de películas y series basada en el estado emocional del usuario.

---

# Autor

**Nombre:** Jhoseth Esneider Rozo Carrillo  
**Código:** 02230131027  
**Programa:** Ingeniería de Sistemas  
**Unidad:** Unidad 12 – Proyecto Integrador  
**Actividad:** Post-Contenido 1  
**Fecha:** 20/05/2026  

---

## Integrantes del Equipo
* **Jhoseth Rozo**
* **Dylan Garzon**

---

# Descripción del Proyecto

MoodFlix busca transformar la experiencia de descubrimiento de contenido audiovisual mediante recomendaciones personalizadas basadas en emociones y estados de ánimo.

A diferencia de los sistemas tradicionales de recomendación, MoodFlix considera el contexto emocional inmediato del usuario para ofrecer contenido alineado con cómo se siente en el momento.

La aplicación utiliza una arquitectura moderna basada en Clean Architecture y desarrollo Android nativo con Kotlin y Jetpack Compose.

---

# Objetivo

Implementar la base técnica y arquitectónica del proyecto integrador mediante:

- Configuración profesional del repositorio GitHub
- Definición de arquitectura modular
- Configuración de Version Catalog en Gradle
- Redacción de ADRs principales
- Creación del prototipo navegable en Figma
- Definición inicial del Design System
- Aplicación de convenciones profesionales de desarrollo

---

# Problemática

## La Paradoja de la Elección en el Streaming

Actualmente, los usuarios de plataformas de streaming enfrentan fatiga de decisión debido al exceso de contenido disponible.

Los algoritmos tradicionales únicamente consideran:

- Historial de visualización
- Tendencias globales
- Preferencias anteriores

Sin embargo, ignoran un factor crítico:

## El estado emocional actual del usuario

Un usuario puede querer contenido completamente diferente dependiendo de cómo se siente en ese momento.

MoodFlix resuelve este problema mediante recomendaciones emocionales inteligentes.

---

## 1. Justificación y Problemática

### El Problema: La Paradoja de la Elección en el Streaming
En la era digital actual, los usuarios de plataformas de streaming se enfrentan diariamente a la "paradoja de la elección". Con miles de títulos disponibles al alcance de la mano, la toma de decisiones se vuelve abrumadora y a menudo resulta en "fatiga por decisión". 

Los algoritmos de recomendación tradicionales (utilizados por gigantes como Netflix o Amazon Prime Video) dependen casi exclusivamente del historial de visualización pasado o de tendencias demográficas generales. Sin embargo, este enfoque ignora un factor crucial en la toma de decisiones humana: **el estado emocional inmediato del usuario**. 

Un usuario que ha tenido un día difícil puede no querer ver un drama denso, a pesar de que sea su género favorito en su historial. Por el contrario, puede buscar una comedia reconfortante o un documental tranquilo. Los sistemas actuales no capturan este contexto emocional en tiempo real.

### La Solución: Recomendación Basada en Estados de Ánimo
**MoodFlix** nace para llenar este vacío crítico. Al centrar la experiencia del usuario en su estado de ánimo inmediato, la aplicación elimina la fricción de la elección. Los usuarios seleccionan cómo se sienten en el momento y MoodFlix les presenta al instante una selección premium de películas y series de televisión cuidadosamente curadas y filtradas que coinciden exactamente con esa vibra emocional.

---

## Prototipo Figma
El diseño de la experiencia de usuario (UX/UI) ha sido conceptualizado con un enfoque moderno, minimalista y de alta fidelidad, asegurando transiciones suaves y microinteracciones que deleitan al usuario.

**[Ver Prototipo Interactivo en Figma](https://www.figma.com/design/moodflix-prototype-u12)**

---

## Arquitectura del Proyecto

MoodFlix está diseñado siguiendo los principios de **Clean Architecture** y la guía oficial de arquitectura de Android. Esto asegura que la aplicación sea altamente escalable, testeable y mantenible a largo plazo.

### Principios Clave:
* **Separación de Capas**: División clara entre la interfaz de usuario, la lógica de negocio y las fuentes de datos.
* **Offline-First**: La aplicación utiliza una base de datos Room local como fuente de verdad para el contenido seleccionado, ofreciendo una experiencia fluida incluso sin conexión a internet, con sincronización inteligente y fallback a la API de TMDB.
* **Inyección de Dependencias**: Acoplamiento débil facilitado por Hilt.

### Diagrama de Arquitectura de Capas
El proyecto se organiza bajo una arquitectura modular limpia dentro del módulo principal:

```
[ Capa de UI (Jetpack Compose / ViewModels) ]
                     │
                     ▼
  [ Capa de Dominio (Modelos / Interfaces de Repositorio) ]
                     ▲
                     │
  [ Capa de Datos (Room DB / Retrofit API / Repositories) ]
```

El diagrama detallado se puede encontrar en:
**[Diagrama de Arquitectura Detallado (docs/architecture-diagram.png)](docs/architecture-diagram.png)**

---

## Stack Tecnológico
* **Lenguaje principal**: [Kotlin](https://kotlinlang.org/) (100% moderno)
* **UI**: [Jetpack Compose](https://developer.android.com/compose) (UI declarativa con Material Design 3)
* **Inyección de Dependencias**: [Hilt](https://developer.android.com/training/dependency-injection/hilt-android)
* **Base de Datos Local**: [Room Database](https://developer.android.com/training/data-storage/room) (Caché offline inteligente)
* **Conexión de Red**: [Retrofit](https://square.github.io/retrofit/) & [OkHttp](https://square.github.io/okhttp/) (Integración con TMDB y OMDb APIs)
* **Gestión de Corrutinas y Flujos**: Kotlin Coroutines & Flows (Programación reactiva asíncrona)
* **Carga de Imágenes**: [Coil Compose](https://coil-kt.github.io/coil/)
* **Gestión de Dependencias**: [Gradle Version Catalog](https://developer.android.com/build/migrate-to-catalogs) (Gestión centralizada de versiones)

---

# ADRs Implementados

## ADR-001 — Stack Tecnológico

### Decisión
Se adopta Kotlin + Jetpack Compose + Hilt + Room + Retrofit.

### Justificación
- Desarrollo moderno Android
- Arquitectura escalable
- Compatibilidad con Clean Architecture
- Mejor mantenibilidad

### Trade-Offs
- Mayor curva de aprendizaje
- Mayor complejidad inicial

---

## ADR-002 — Arquitectura Multi-Módulo

### Decisión
Se adopta arquitectura Multi-Módulo:

- :app
- :feature:*
- :core:domain
- :core:data
- :core:ui

### Justificación
- Builds incrementales
- Mejor separación de responsabilidades
- Escalabilidad

### Trade-Offs
- Configuración inicial compleja
- Mayor mantenimiento Gradle

---

## ADR-003 — Persistencia y Sincronización

### Decisión
Se utiliza Room Database como fuente local principal y Retrofit para sincronización remota.

### Justificación
- Soporte Offline First
- Caché persistente
- Mejor experiencia de usuario

### Trade-Offs
- Manejo de sincronización más complejo
- Mayor lógica de consistencia de datos

#  Ejecución del Proyecto

## 1. Clonar repositorio

```bash
git clone https://github.com/jerc31/rozo-garzon-post1-u12
```

## 2. Abrir en Android Studio

Abrir el proyecto desde Android Studio Hedgehog.

## 3. Sincronizar Gradle

```bash
./gradlew build
```

## 4. Compilar aplicación

```bash
./gradlew assembleDebug
```

---

# Checkpoints Verificados

## ✔ Checkpoint 1 — Estructura Profesional del Repositorio

Se verifica:

- Estructura modular
- README completo
- ADRs en docs/adr/
- Conventional Commits

Commit inicial:

```text
chore: inicializar estructura del proyecto integrador
```

---

## ✔ Checkpoint 2 — ADRs Completos

Se implementaron:

- ADR-001 Stack Tecnológico
- ADR-002 Arquitectura Modular
- ADR-003 Persistencia y Sincronización

Cada ADR incluye:

- Estado
- Contexto
- Alternativas
- Decisión
- Consecuencias

---

## ✔ Checkpoint 3 — Version Catalog

Verificación:

```bash
./gradlew assembleDebug
```

Resultado:

- Proyecto compila correctamente
- No existen versiones hardcodeadas
- Version Catalog centralizado

---

## ✔ Checkpoint 4 — Prototipo Figma

El prototipo contiene:

- 5+ pantallas
- Navegación interactiva
- Design System reutilizable
- Tokens coherentes con Compose Theme
