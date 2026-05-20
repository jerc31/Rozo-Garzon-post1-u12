# ADR-001: Selección del Stack Tecnológico Principal

## Estado
Aceptado

## Contexto
MoodFlix es una aplicación móvil de streaming de información y recomendaciones personalizadas basada en el estado de ánimo del usuario. El proyecto requiere un desarrollo ágil, una interfaz de usuario fluida, atractiva y moderna, persistencia local eficiente (caché offline-first), integración fluida con APIs REST externas (TMDB y OMDb) y un sistema robusto de inyección de dependencias para asegurar la testeabilidad de la lógica de negocio.

## Alternativas Consideradas

### 1. Desarrollo con Vistas XML tradicionales y DataBinding
* **Pros**: Tecnología madura, amplio soporte heredado.
* **Contras**: Requiere gran cantidad de código repetitivo (boilerplate), las vistas se definen en XML separadas del código de lógica de UI, y no es el estándar de desarrollo moderno recomendado por Google.

### 2. SQLite Nativo (sin Room)
* **Pros**: Control absoluto de las consultas SQL, sin sobrecarga de bibliotecas adicionales.
* **Contras**: Falta de mapeo directo de objetos a tablas (ORM), propenso a errores en tiempo de ejecución debido a consultas en texto plano y ausencia de soporte nativo para programación reactiva (Flows).

### 3. Ktor Client (en lugar de Retrofit)
* **Pros**: Multiplataforma nativo, diseño moderno en Kotlin.
* **Contras**: Menos maduro que Retrofit en el ecosistema puramente Android, requiere más configuración manual para interceptores, logs y mapeos Gson de forma estructurada en comparación con la integración ya establecida de Retrofit.

### 4. Inyección de Dependencias Manual o Koin
* **Pros**: Koin es más fácil de aprender y no requiere procesamiento de anotaciones (KSP/KAPT).
* **Contras**: Koin no realiza validación en tiempo de compilación de las dependencias, lo que puede causar fallas en tiempo de ejecución. Hilt está construido sobre Dagger 2, ofreciendo validación en tiempo de compilación y una integración perfecta con los componentes de Jetpack (ViewModels, Navigation, etc.).

## Decisión Aceptada
Se selecciona el siguiente stack tecnológico unificado:
* **Lenguaje**: **Kotlin** por su expresividad, seguridad frente a nulos y excelente soporte para Corrutinas y Flujos.
* **UI**: **Jetpack Compose** para un desarrollo ágil de componentes de UI reactivos y modernos, eliminando XML.
* **Base de Datos**: **Room Database** por su robustez, verificación de consultas SQL en tiempo de compilación y retorno directo de datos en forma de reactiva (`Flow<List<T>>`).
* **Red**: **Retrofit** y **OkHttp** por ser el estándar de la industria, con facilidad de configuración de interceptores para tokens y logging.
* **DI**: **Dagger Hilt** para inyección de dependencias robusta, rápida e integrada nativamente con Android Jetpack.

## Consecuencias

### Positivas:
* **Productividad**: Jetpack Compose reduce significativamente el tiempo de desarrollo de interfaces complejas y animaciones de estados de ánimo.
* **Robustez**: Hilt y Room garantizan fallas tempranas en tiempo de compilación en lugar de fallas silenciosas en tiempo de ejecución.
* **Mantenibilidad**: La separación limpia facilitada por DI y Corrutinas hace que el código sea modular y fácil de testear.

### Negativas / Riesgos:
* **Curva de aprendizaje**: Se requiere familiaridad del equipo con la UI declarativa y la inyección de dependencias compleja.
* **Tiempos de compilación**: El procesamiento de anotaciones de KSP con Hilt y Room incrementa levemente el tiempo de compilación inicial.
