# ADR-002: Estructura de Módulos del Proyecto (Módulo Único vs Multi-módulo)

## Estado
Aceptado

## Contexto
En proyectos profesionales de Android, la modularización por características (features) o por capas es una práctica común para mejorar los tiempos de compilación incremental, la separación de responsabilidades y permitir que equipos grandes trabajen en paralelo de manera eficiente. Para MoodFlix, se debe definir si la aplicación se estructurará desde el inicio como un proyecto multi-módulo de Gradle o si se utilizará un diseño de módulo único con una estricta separación de capas a través de paquetes de código.

## Alternativas Consideradas

### 1. Proyecto Multi-módulo Completo (por capas o por features)
* **Pros**: Aislamiento estricto de dependencias a nivel de Gradle, compilación paralela más veloz en proyectos gigantes, y reutilización óptima de código.
* **Contras**: Gran sobrecarga de configuración inicial en Gradle (`build.gradle.kts` por cada módulo), complejidad en la gestión de navegación e inyección de dependencias entre módulos no acoplados, y velocidad de desarrollo inicial reducida para un equipo de solo 2 personas con un tiempo de entrega ajustado (6 semanas).

### 2. Módulo Único (`:app`) con Estructura de Capas por Paquetes (Clean Architecture)
* **Pros**: Configuración extremadamente sencilla de Gradle y Version Catalog, inyección de dependencias con Hilt directa, navegación sin sobrecarga de abstracciones, y máxima velocidad de desarrollo inicial.
* **Contras**: No hay barreras físicas del compilador para evitar acoplamientos incorrectos (e.g., que la capa de datos acceda directamente a elementos específicos de UI si el desarrollador comete un error de importación).

## Decisión Aceptada
Se opta por una **estructura de Módulo Único (`:app`) estructurada estrictamente por capas y características mediante paquetes Kotlin**, pero dejando preparada la estructura física de directorios en el repositorio (`core/` y `feature/` como carpetas raíz vacías con `.gitkeep`) para una futura modularización.

La separación de paquetes se establece firmemente bajo los principios de Clean Architecture:
* `com.rozo_garzon.moodflix.domain`: Contiene modelos puros de negocio e interfaces de repositorio sin dependencias de Android o librerías externas.
* `com.rozo_garzon.moodflix.data`: Implementación física de repositorios, Room Database, DAOs, servicios Retrofit y DTOs.
* `com.rozo_garzon.moodflix.ui`: Pantallas y componentes Compose, organizados por feature (`login`, `home`, `detail`) junto con sus respectivos ViewModels.
* `com.rozo_garzon.moodflix.di`: Módulos de Hilt que conectan las dependencias del sistema de datos a la aplicación.

## Consecuencias

### Positivas:
* **Velocidad de entrega**: El equipo puede enfocarse al 100% en las características del MVP sin perder tiempo en la compleja coreografía de navegación y dependencias Gradle multi-módulo.
* **Navegación Simple**: Se utiliza `navigation-compose` estándar sobre el MainActivity, facilitando el paso de parámetros simples.
* **Preparación para el futuro**: El código se escribe tan limpio y desacoplado por paquetes que migrarlo a módulos físicos de Gradle en el futuro será una tarea trivial de mover archivos, sin necesidad de reescribir lógica.

### Negativas / Riesgos:
* **Disciplina obligatoria**: El equipo debe ser riguroso en revisiones de código (a través de Pull Requests guiadas por la plantilla) para evitar importaciones que rompan las reglas de dependencia de Clean Architecture (e.g., usar elementos de datos o UI dentro de la capa de dominio).
