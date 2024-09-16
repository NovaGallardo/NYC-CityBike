# Reto Técnico: Análisis Descriptivo de Datos de Citi Bike

## Enlaces y Recursos

- [Bitácora](https://www.notion.so/Citi-Bike-bdf8a11c07e34f909eb94e7d5c009896?pvs=4)
- [Dashboard Looker Studio](https://lookerstudio.google.com/reporting/7257e3dc-50bf-4a65-aabd-ab76c4c5f4f4)
- [Presentación en Google Slides](https://docs.google.com/presentation/d/1noCawZlbVGIypoZyTsGIU4vLKrM1MlcusPJo1Otkb-4/edit?usp=sharing)

## Introducción

Este proyecto se centra en la realización de un análisis descriptivo y exploratorio de datos del programa de bicicletas compartidas Citi Bike en la ciudad de Nueva York. El objetivo principal es entender los patrones de uso, realizar análisis descriptivos y comunicar los hallazgos en un reporte o dashboard que pueda ser presentado a la nueva CEO de la empresa.

## La Situación

Eres un/a analista de datos que trabaja en el equipo de datos de una empresa que brinda el servicio de bicicletas compartidas. La empresa ha contratado una nueva CEO, y como parte de su proceso de onboarding, se ha solicitado un informe detallado que incluya las siguientes métricas clave:

1. **Métricas de uso de un día promedio**:
   - Número promedio de viajes diarios.
   - Duración mínima, máxima, promedio y desviación estándar de los viajes.

2. **Métricas históricas**:
   - Total de viajes.
   - Crecimiento del número de viajes diarios a lo largo del tiempo.
   - Total de viajes por usuarios, segmentados por género, edad y/o tipo de suscripción.

El objetivo de este análisis es identificar patrones y tendencias clave que permitan generar recomendaciones para la nueva CEO sobre cómo mejorar el servicio de Citi Bike y optimizar su operación en la ciudad de Nueva York.

## Objetivo del Proyecto

- **Análisis descriptivo**: Realizar un análisis descriptivo de los datos de viajes y usuarios, identificando patrones clave.
- **Segmentación de usuarios**: Analizar el comportamiento de los usuarios según el género, la edad y el tipo de suscripción.
- **Crecimiento histórico**: Evaluar el crecimiento del servicio de Citi Bike a lo largo del tiempo.
- **Visualización**: Crear visualizaciones que faciliten la comunicación de los resultados de manera clara y efectiva.

## Herramientas y Tecnologías Utilizadas

- **BigQuery**: Consulta y análisis de datos a gran escala del conjunto público de Citi Bike.
- **SQL**: Creación de nuevas variables, limpieza de datos y preparación de los mismos para análisis descriptivo.
- **Looker Studio**: Generación de dashboards interactivos para la presentación de resultados.
- **Google Slides**: Presentación de los resultados a la nueva CEO.

## Hipótesis

- Las estaciones ubicadas en áreas céntricas de Nueva York presentan una mayor demanda de uso.
- Los usuarios más jóvenes tienden a realizar viajes más cortos.
- El número de viajes aumenta considerablemente en primavera y verano debido al clima favorable.

## Composición del Dataset

### Descripción de las Variables del Dataset `citibike_trips`

| Nombre de la Columna        | Descripción                                                                                                                    |
|-----------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| tripduration                | Duración del viaje (en segundos).                                                                                               |
| starttime                   | Hora de inicio del viaje, en la hora local de NYC.                                                                              |
| stoptime                    | Hora de finalización del viaje, en la hora local de NYC.                                                                        |
| start_station_id            | ID de la estación de inicio.                                                                                                    |
| start_station_name          | Nombre de la estación de inicio.                                                                                                |
| start_station_latitude      | Latitud de la estación de inicio.                                                                                               |
| start_station_longitude     | Longitud de la estación de inicio.                                                                                              |
| end_station_id              | ID de la estación de finalización.                                                                                              |
| end_station_name            | Nombre de la estación de finalización.                                                                                          |
| end_station_latitude        | Latitud de la estación de finalización.                                                                                         |
| end_station_longitude       | Longitud de la estación de finalización.                                                                                        |
| bikeid                      | ID de la bicicleta utilizada.                                                                                                   |
| usertype                    | Tipo de usuario (Customer = usuario de pase de 24 horas o 7 días, Subscriber = miembro anual).                                   |
| birth_year                  | Año de nacimiento del usuario.                                                                                                  |
| gender                      | Género del usuario (desconocido, masculino, femenino).                                                                          |

### Descripción de las Variables del Dataset `citibike_stations`

| Nombre de la Columna        | Descripción                                                                                                                    |
|-----------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| station_id                  | Identificador único de la estación.                                                                                            |
| name                        | Nombre público de la estación.                                                                                                |
| short_name                  | Nombre corto o identificador utilizado por el publicador de datos.                                                             |
| latitude                    | Latitud de la estación. Debe ser un valor válido en formato decimal de grados WGS 84.                                           |
| longitude                   | Longitud de la estación. Debe ser un valor válido en formato decimal de grados WGS 84.                                          |
| region_id                   | ID de la región donde se encuentra la estación.                                                                                |
| rental_methods              | Métodos de pago aceptados en esta estación.                                                                                    |
| capacity                    | Número total de puntos de acoplamiento instalados en esta estación, disponibles o no disponibles.                              |
| eightd_has_key_dispenser     | Si la estación tiene un dispensador de llaves 8D.                                                                              |
| num_bikes_available          | Número de bicicletas disponibles para alquiler.                                                                                |
| num_bikes_disabled           | Número de bicicletas discapacitadas en la estación.                                                                            |
| num_docks_available          | Número de puntos de acoplamiento disponibles para devolver bicicletas.                                                         |
| num_docks_disabled           | Número de puntos de acoplamiento vacíos pero deshabilitados en la estación.                                                    |
| is_installed                 | Indica si la estación está actualmente instalada en la calle.                                                                  |
| is_renting                   | Indica si la estación está alquilando bicicletas en ese momento.                                                               |
| is_returning                 | Indica si la estación está aceptando devoluciones de bicicletas.                                                               |
| eightd_has_available_keys    | Indica si la estación tiene llaves disponibles del sistema 8D.                                                                 |
| last_reported                | Marca de tiempo que indica la última vez que la estación reportó su estado al backend, en la hora local de NYC.                |

## Proceso

### 1. Preparación y Limpieza de Datos

El primer paso consistió en realizar una limpieza profunda de los datos de `citibike_trips` y la creación de nuevas variables que permitan un análisis más detallado. Se llevó a cabo lo siguiente:

- **Filtrado por duración del viaje**: Se eliminaron los viajes extremadamente cortos (menos de 1 minuto) y aquellos que excedían las 2 horas. Esto asegura que los viajes incluidos en el análisis sean representativos y no distorsionen las métricas con valores atípicos.
  
- **Filtrado por ubicación**: Se limitó el análisis a las estaciones ubicadas dentro de los límites geográficos de la ciudad de Nueva York, utilizando latitud y longitud válidas.

- **Filtrado por edad razonable**: Se seleccionaron usuarios cuyas edades estén entre 16 y 90 años, eliminando valores poco confiables o erróneos.

- **Cálculo de la duración de los viajes**: La variable original `tripduration` se transformó de segundos a minutos para facilitar la interpretación y visualización de los resultados.

- **Creación de variables temporales**: Se extrajeron nuevas variables de la fecha de finalización del viaje (`stoptime`), tales como:
  - Fecha exacta del viaje.
  - Año, mes y día del viaje.
  - Día de la semana.
  - Hora de inicio y fin del viaje.
  
- **Clasificación de la duración del viaje**: Los viajes se categorizaron en tres grupos:
  - **Corto**: Duración menor a 5 minutos.
  - **Medio**: Duración entre 5 y 20 minutos.
  - **Largo**: Duración superior a 20 minutos.

- **Cálculo de la distancia del viaje**: Utilizando las coordenadas de las estaciones de inicio y finalización, se calculó la distancia recorrida en metros entre las dos estaciones, lo cual es fundamental para evaluar la eficiencia del servicio.

- **Identificación de viajes de ida y vuelta**: Se creó una variable que identifica si un viaje comenzó y terminó en la misma estación, lo que puede reflejar patrones de uso recreativo o logístico.

- **Clasificación generacional**: Los usuarios se agruparon por generaciones (Generation Z, Millennials, Generation X, Baby Boomers) en función de su año de nacimiento, lo cual es útil para analizar el comportamiento de diferentes segmentos demográficos.

- **Creación de categorías estacionales y diarias**: Se generaron variables adicionales para identificar la temporada del año (primavera, verano, otoño, invierno) y la parte del día (madrugada, mañana, tarde, noche) en la que ocurrieron los viajes, lo que permite observar cómo el uso de Citi Bike varía en diferentes momentos.

### 2. Análisis Exploratorio

Una vez preparados los datos, se llevó a cabo un análisis descriptivo para obtener las métricas clave:

- **Duración del viaje**: Se calcularon estadísticas como duración promedio, mínima, máxima y desviación estándar para entender mejor los patrones de uso.
  
- **Segmentación por edad y género**: Se segmentaron los viajes según la edad, el género y el tipo de suscripción de los usuarios, con el fin de identificar patrones demográficos.

- **Análisis de rutas**: Se investigaron las rutas más comunes y las estaciones más utilizadas, lo que es esencial para identificar puntos de alta demanda.

- **Análisis temporal**: Se analizaron las tendencias de uso a lo largo del tiempo, observando el comportamiento por temporada y por día de la semana.

### 3. Visualización de Datos

Las visualizaciones generadas incluyeron gráficos que mostraban:

- La distribución de la duración de los viajes.
- Los patrones de uso por hora del día y temporada.
- La segmentación demográfica por edad y género.
- Los puntos geográficos más utilizados para tomar y devolver bicicletas.

Este enfoque permite obtener una comprensión clara y completa del comportamiento de los usuarios de Citi Bike y proporcionar recomendaciones a la nueva CEO sobre cómo optimizar el servicio.

## Resultados

1. **Patrones de uso en estaciones**:
   - De las 2223 estaciones disponibles, solo 994 están en uso. Las estaciones no utilizadas representan una oportunidad significativa de optimización en la distribución del servicio. Las estaciones más activas están principalmente ubicadas en áreas céntricas y con alta densidad de tráfico peatonal.
   - **Estación más usada:** E 17 ST Broadway se destaca como la estación más frecuentada, lo que sugiere que las estaciones en ubicaciones estratégicas tienen un rendimiento considerablemente mayor.

2. **Tendencias estacionales y temporales**:
   - El uso de Citi Bike se incrementa considerablemente durante las estaciones de primavera y verano, con los meses de mayo y septiembre mostrando picos de actividad.
   - **Día más activo:** El día miércoles presenta el mayor volumen de viajes, lo que puede estar relacionado con patrones laborales y de transporte.
   - **Hora del día:** La mayoría de los viajes se realizan en la tarde, seguido por la mañana, lo cual sugiere que los usuarios utilizan Citi Bike principalmente para desplazamientos hacia y desde el trabajo o para actividades recreativas durante horas no laborales.

3. **Duración y distancia promedio de los viajes**:
   - La mayoría de los viajes tienen una duración de entre 5 y 20 minutos, lo cual está dentro del tiempo estándar de uso que otorga el servicio de Citi Bike.
   - La distancia promedio recorrida es de **1,761.57 metros**, lo que sugiere que los usuarios utilizan las bicicletas principalmente para trayectos cortos dentro de la ciudad.

4. **Diferencias por género**:
   - Solo el 23.9% de los viajes fueron realizados por mujeres, lo que indica una disparidad de género significativa en el uso del servicio. Esto resalta una oportunidad para incrementar la participación femenina mediante estrategias de marketing y mejora de la experiencia del servicio para este segmento.

5. **Segmentación etaria**:
   - Los usuarios adolescentes y seniors representan una baja proporción del total de viajes. Esto sugiere la necesidad de desarrollar estrategias para atraer a estos grupos etarios mediante suscripciones personalizadas o tarifas especiales.

Este análisis ofrece una visión completa de los patrones de uso del servicio Citi Bike, destacando áreas de mejora tanto en la distribución de estaciones como en la segmentación de usuarios y sus patrones de comportamiento.

## Recomendaciones

1. **Aumentar el uso en estaciones no utilizadas:**
   - **Acciones:** Dado que un 55% de las estaciones no están en uso, es esencial optimizar su distribución. Se recomienda realizar un análisis geoespacial más profundo para identificar posibles reubicaciones en zonas con mayor demanda. Alternativamente, se podrían implementar campañas promocionales específicas en las áreas con estaciones no utilizadas para incentivar el uso.
   - **Impacto esperado:** Incremento del uso de estaciones actualmente inactivas, mejorando la eficiencia del sistema de bicicletas compartidas.

2. **Campaña de marketing dirigida a mujeres:**
   - **Acciones:** El 23.9% de los viajes fueron realizados por mujeres, lo que indica una baja participación femenina. Se recomienda realizar estudios cualitativos para comprender las barreras que enfrentan las mujeres en el uso de Citi Bike, y con base en los hallazgos, diseñar campañas que resalten los beneficios del servicio para este segmento.
   - **Impacto esperado:** Aumento en la participación femenina, diversificando la base de usuarios y equilibrando la proporción de género en los viajes.

3. **Nuevos planes para adolescentes y seniors:**
   - **Acciones:** Crear suscripciones especiales dirigidas a adolescentes y adultos mayores para aumentar el uso entre estos grupos etarios. El enfoque puede incluir tarifas reducidas y beneficios adicionales, como viajes gratuitos los fines de semana o eventos comunitarios que promuevan el uso de bicicletas entre estos grupos.
   - **Impacto esperado:** Aumento en la cantidad de viajes realizados por adolescentes y adultos mayores, lo que diversifica aún más la base de usuarios.

4. **Optimización de estaciones en desuso:**
   - **Acciones:** Se deben analizar las 1229 estaciones no utilizadas para determinar si alguna debe ser cerrada o redistribuida en zonas de mayor demanda. En caso de que no se opte por su cierre, se recomienda crear campañas que incentiven su uso mediante descuentos o promociones específicas en las áreas donde están ubicadas.
   - **Impacto esperado:** Mayor utilización de estaciones actualmente en desuso o una redistribución más eficiente que favorezca el acceso a estaciones en zonas con mayor tráfico.

5. **Optimización temporal del servicio:**
   - **Acciones:** Dado que la mayor parte de los viajes se realizan en primavera y verano, y principalmente en la tarde, se podrían ajustar las estrategias operativas (p. ej., aumentando la disponibilidad de bicicletas) para cubrir mejor la demanda en estos periodos y horarios. Además, se podrían ofrecer incentivos para aumentar el uso en temporadas de menor demanda.
   - **Impacto esperado:** Un servicio más eficiente y rentable, que se ajuste mejor a los picos de demanda estacionales y temporales.


