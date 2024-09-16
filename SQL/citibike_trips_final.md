# Limpieza de tabla principal de citibike y creación de nuevas variables

### Descripción de la Query

Esta query realiza un análisis exhaustivo de los datos de Citi Bike, preparando los datos para un análisis descriptivo mediante la creación de nuevas variables y el filtrado de información relevante.

1. **Filtrado de viajes:**
    - Solo se incluyen los viajes con una duración superior a 1 minuto (60 segundos) y menores a 2 horas (7200 segundos), eliminando outliers.
    - Se filtran los datos geográficamente, asegurando que solo se incluyan viajes dentro de los límites de Nueva York (latitudes y longitudes específicas).
2. **Creación de nuevas variables:**
    - **Fecha del viaje**: Se extrae la fecha del viaje basándose en el tiempo de parada (`stoptime`) y restando la duración del viaje en segundos.
    - **Duración del viaje**: La duración del viaje (`tripduration`) se transforma de segundos a minutos.
    - **Edad del usuario**: Se calcula la edad de los usuarios en función de su año de nacimiento (`birth_year`), utilizando 2018 como año de referencia.
    - **Generación**: Los usuarios se clasifican en grupos generacionales (Generation Z, Millennials, Generation X, Baby Boomers) según su edad.
    - **Viajes de ida y vuelta**: Se determina si el usuario ha realizado un viaje de ida y vuelta a la misma estación.
    - **Categoría de duración del viaje**: Los viajes se agrupan en categorías (corto, medio, largo) según su duración en minutos.
    - **Distancia del viaje**: Se calcula la distancia en metros entre la estación de inicio y la de finalización utilizando las coordenadas geográficas.
    - **Estaciones coordenadas**: Se concatenan las latitudes y longitudes de las estaciones de inicio y finalización para identificar su ubicación exacta.
3. **Segmentación temporal:**
    - Se crean variables para segmentar los viajes por año, mes, día del mes, día de la semana y hora.
    - Además, se asignan categorías según la parte del día (madrugada, mañana, tarde, noche) y la temporada del año (primavera, verano, otoño, invierno).
4. **Segmentación por grupos de edad:**
    - Los usuarios se agrupan en diferentes rangos de edad: adolescentes, jóvenes adultos, adultos, adultos de mediana edad y seniors, facilitando el análisis demográfico.
5. **Orden de los resultados:**
    - Los datos se ordenan por la duración del viaje en minutos, de mayor a menor, permitiendo identificar los viajes más largos.

Este conjunto de operaciones permite obtener un dataset limpio y estructurado, ideal para realizar análisis descriptivos y explorar patrones de comportamiento en el uso de Citi Bike.

```sql
WITH prepared_data AS (
  SELECT 
    EXTRACT(DATE FROM TIMESTAMP_SUB(stoptime, INTERVAL tripduration SECOND)) AS date_trip,
    EXTRACT(YEAR FROM stoptime) AS year,
    EXTRACT(MONTH FROM TIMESTAMP_SUB(stoptime, INTERVAL tripduration SECOND)) AS trip_month,
    EXTRACT(DAY FROM TIMESTAMP_SUB(stoptime, INTERVAL tripduration SECOND)) AS trip_day,
    EXTRACT(DAYOFWEEK FROM TIMESTAMP_SUB(stoptime, INTERVAL tripduration SECOND)) AS trip_day_of_week,
    EXTRACT(TIME FROM TIMESTAMP_SUB(stoptime, INTERVAL tripduration SECOND)) AS starttime,
    EXTRACT(TIME FROM stoptime) AS stoptime,
    tripduration / 60 AS tripduration_minutes,
    start_station_name,
    end_station_name,
    bikeid,
    usertype,
    2018 - birth_year AS age, --Sacar la edad de los usuarios en el 2018 ya que ese es el año más reciente de la dataset
    CASE
      WHEN birth_year >= 1997 THEN 'Generation Z'
      WHEN birth_year BETWEEN 1981 AND 1996 THEN 'Millennial'
      WHEN birth_year BETWEEN 1965 AND 1980 THEN 'Generation X'
      WHEN birth_year BETWEEN 1946 AND 1964 THEN 'Baby Boomer'
      ELSE 'Other'
    END AS generation,
    gender,
    CASE
      WHEN start_station_name = end_station_name THEN 'Yes'
      ELSE 'No'
    END AS round_trip,
    CASE 
      WHEN tripduration / 60 <= 5 THEN 'Short'
      WHEN tripduration / 60 BETWEEN 5 AND 20 THEN 'Medium'
      ELSE 'Long'
    END AS trip_duration_category,
    ST_DISTANCE(ST_GEOGPOINT(start_station_longitude, start_station_latitude), 
                ST_GEOGPOINT(end_station_longitude, end_station_latitude)) AS trip_distance_meters,
    CONCAT(start_station_latitude, "," , start_station_longitude) AS start_station_coor,
    CONCAT(end_station_latitude, "," , end_station_longitude) AS end_station_coor
  FROM 
    `bigquery-public-data.new_york_citibike.citibike_trips`
  WHERE 
    birth_year IS NOT NULL 
    AND tripduration IS NOT NULL
    AND tripduration > 60  -- Filtrar viajes extremadamente cortos (menos de 1 minuto)
    AND tripduration <= 7200  -- Filtrar viajes que duren más de 2 horas (7200 segundos)
    AND EXTRACT(YEAR FROM CURRENT_DATE()) - birth_year BETWEEN 16 AND 90 -- Filtrar edades razonables
    AND start_station_latitude BETWEEN 40.4774 AND 40.9176  -- Filtrar por latitud de Nueva York
    AND start_station_longitude BETWEEN -74.2591 AND -73.7004  -- Filtrar por longitud de Nueva York
    AND end_station_latitude BETWEEN 40.4774 AND 40.9176  -- Filtrar por latitud de Nueva York
    AND end_station_longitude BETWEEN -74.2591 AND -73.7004  -- Filtrar por longitud de Nueva York
)

SELECT 
  date_trip,
  year,
  trip_month,
  trip_day,
  trip_day_of_week,
  starttime,
  stoptime,
  tripduration_minutes,
  start_station_name,
  end_station_name,
  bikeid,
  usertype,
  age,
  CASE
    WHEN age BETWEEN 16 AND 17 THEN 'Adolescent'
    WHEN age BETWEEN 18 AND 34 THEN 'Young Adult'
    WHEN age BETWEEN 35 AND 54 THEN 'Adult'
    WHEN age BETWEEN 55 AND 64 THEN 'Middle Aged Adult'
    ELSE 'Senior'
  END AS age_group,
  generation,
  gender,
  round_trip,
  trip_duration_category,
  CASE 
    WHEN (EXTRACT(MONTH FROM date_trip) = 3 AND EXTRACT(DAY FROM date_trip) >= 21)
      OR EXTRACT(MONTH FROM date_trip) IN (4, 5)
      OR (EXTRACT(MONTH FROM date_trip) = 6 AND EXTRACT(DAY FROM date_trip) <= 20)
    THEN 'Spring'
    WHEN (EXTRACT(MONTH FROM date_trip) = 6 AND EXTRACT(DAY FROM date_trip) >= 21)
      OR EXTRACT(MONTH FROM date_trip) IN (7, 8)
      OR (EXTRACT(MONTH FROM date_trip) = 9 AND EXTRACT(DAY FROM date_trip) <= 22)
    THEN 'Summer'
    WHEN (EXTRACT(MONTH FROM date_trip) = 9 AND EXTRACT(DAY FROM date_trip) >= 23)
      OR EXTRACT(MONTH FROM date_trip) IN (10, 11)
      OR (EXTRACT(MONTH FROM date_trip) = 12 AND EXTRACT(DAY FROM date_trip) <= 20)
    THEN 'Fall'
    ELSE 'Winter'
  END AS season,
  CASE 
    WHEN EXTRACT(HOUR FROM starttime) BETWEEN 0 AND 5 THEN 'Dawn'
    WHEN EXTRACT(HOUR FROM starttime) BETWEEN 6 AND 11 THEN 'Morning'
    WHEN EXTRACT(HOUR FROM starttime) BETWEEN 12 AND 17 THEN 'Afternoon'
    ELSE 'Night'
  END AS part_of_day,
  trip_distance_meters,
  start_station_coor,
  end_station_coor
FROM 
  prepared_data
ORDER BY 
  tripduration_minutes DESC
```
<aside>
💡 Tabla original 58937715 filas. 
Nueva tabla 47409844 filas.

</aside>
