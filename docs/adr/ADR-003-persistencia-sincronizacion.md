# ADR-003: Estrategia de Persistencia Local y Sincronización Offline-first

## Estado
Aceptado

## Contexto
MoodFlix depende de APIs REST públicas externas (TMDB y OMDb) para obtener películas y series recomendadas según el estado de ánimo. El acceso a APIs web en dispositivos móviles está intrínsecamente sujeto a problemas de inestabilidad de red, latencia o ausencia total de conexión. Para garantizar una experiencia de usuario fluida e ininterrumpida, es fundamental definir cómo persistir los datos de manera local y cuándo actualizarlos o sincronizarlos con los servidores remotos.

## Alternativas Consideradas

### 1. Consumo Directo de la API (Sin persistencia local)
* **Pros**: Datos 100% actualizados en tiempo real y código extremadamente simple sin necesidad de base de datos ni mapeadores.
* **Contras**: Si el dispositivo pierde la conexión, la aplicación queda completamente inoperable (pantalla de error). Además, consume un ancho de banda innecesario realizando llamadas de red idénticas cada vez que el usuario navega de ida y vuelta a la pantalla de inicio.

### 2. Caché Simple en Memoria
* **Pros**: Respuestas instantáneas y fácil de implementar con variables estáticas o singletons de Kotlin.
* **Contras**: Los datos desaparecen instantáneamente si el sistema operativo destruye el proceso de la aplicación para liberar memoria en segundo plano, y no soluciona el problema de arrancar la aplicación sin conexión a internet.

### 3. Persistencia Offline-First con Room Database y TTL (Time-To-Live)
* **Pros**: Room sirve como única fuente de verdad para la UI. Permite el funcionamiento de la aplicación en modo offline total mostrando el último contenido cargado. Reduce la latencia de carga inicial a milisegundos y optimiza el uso de la API remota.
* **Contras**: Incrementa la complejidad en la capa de datos. Requiere la creación de esquemas de base de datos, entidades, DAOs, mapeadores (`MovieMapper`) y lógica de sincronización para validar si el caché local ha expirado.

## Decisión Aceptada
Se adopta la **Persistencia Offline-First con Room Database y TTL de 24 horas**. 

### Detalles de la Implementación:
* **Entidades Locales**: Se crean dos entidades principales en Room:
  * `MoodCacheEntity`: Almacena el estado de la sincronización de un estado de ánimo (`moodId`), la fecha de la última actualización exitosa (`lastUpdated`), los IDs de géneros asociados y los metadatos.
  * `MovieEntity`: Almacena la información de cada película individual mapeada localmente, con una clave foránea que la asocia a un `MoodCacheEntity` (eliminándose en cascada si la categoría se invalida).
* **Lógica del Repositorio (`getMoviesByMood`)**:
  1. Emite inmediatamente el estado `Loading`.
  2. Consulta la base de datos local y, si existen películas cacheadas para el mood, las emite como un éxito temporal o definitivo.
  3. Compara el timestamp actual con `lastUpdated` del caché.
  4. Si la red está disponible y el caché ha expirado (o no existe localmente), realiza la petición de red a la API remota.
  5. Si la petición de red es exitosa:
     * Elimina el caché local antiguo para ese mood.
     * Inserta las nuevas películas y actualiza el `lastUpdated` en `MoodCacheEntity` dentro de una transacción de Room.
     * Emite el nuevo resultado actualizado.
  6. Si la petición de red falla o no hay conexión:
     * Si había datos locales, emite los datos del caché como exitosos (modo offline).
     * Si no había datos locales anteriores, emite un estado de `Error` que describe la falta de conexión.

## Consecuencias

### Positivas:
* **Experiencia de Usuario Premium**: La pantalla principal carga de forma instantánea al consultar la base de datos local antes de intentar descargar los datos de internet.
* **Resiliencia Offline**: Los usuarios pueden abrir la aplicación en el metro o en zonas con poca cobertura y seguir explorando sus recomendaciones pasadas.
* **Uso Inteligente de Recursos**: Reduce el volumen de peticiones API a TMDB, previniendo el bloqueo por límites de consumo.

### Negativas / Riesgos:
* **Complejidad de Código**: Mayor esfuerzo inicial al escribir mapeadores de datos (`toDomain`, `fromDto`), manejar transacciones de base de datos y controlar las excepciones de red.
* **Consumo de Almacenamiento**: La base de datos local consumirá unos pocos megabytes de almacenamiento en el dispositivo, lo cual es negligible para las capacidades de los teléfonos modernos.
