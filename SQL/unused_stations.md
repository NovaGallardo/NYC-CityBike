# Tabla con las estaciones no usadas

### Descripción de la Query

Esta query tiene como objetivo identificar las estaciones de Citi Bike que están registradas en el sistema pero que no han sido utilizadas en el dataset analizado. Se busca obtener una lista de estaciones inactivas junto con sus coordenadas geográficas.

1. **Selección de estaciones de la tabla `citibike_stations`:**
    - La tabla `citibike_stations` (`s`) contiene información sobre todas las estaciones de Citi Bike, incluyendo su nombre (`s.name`) y sus coordenadas geográficas (`s.latitude`, `s.longitude`).
    - Se utiliza la función `CONCAT` para combinar la latitud y la longitud en una única columna llamada `station_coor`, que muestra la ubicación geográfica de cada estación en formato de texto.
2. **Operación `LEFT JOIN` con las estaciones utilizadas:**
    - La tabla `citibike_stations_used` (`u`) contiene información sobre las estaciones que han sido utilizadas en los viajes.
    - Se realiza un `LEFT JOIN` entre las tablas `citibike_stations` y `citibike_stations_used`, para asegurar que todas las estaciones de la tabla `citibike_stations` estén presentes en el resultado, independientemente de si han sido utilizadas o no.
    - La combinación de los nombres de las estaciones se hace utilizando las funciones `TRIM` y `LOWER` para normalizar los nombres, eliminando espacios en blanco y garantizando que las comparaciones de los nombres de las estaciones no sean sensibles a mayúsculas y minúsculas.
3. **Filtrado de estaciones no utilizadas:**
    - La cláusula `WHERE u.station_name IS NULL` filtra las estaciones que no tienen un registro en la tabla de estaciones utilizadas (`citibike_stations_used`). Esto significa que la query seleccionará solo aquellas estaciones que no han sido utilizadas en ningún viaje.
4. **Resultado final:**
    - El resultado es una lista de estaciones inactivas (`station_name`) junto con sus coordenadas geográficas (`station_coor`), lo que permite identificar cuáles son las estaciones que no han registrado actividad en el dataset.

Esta query es útil para detectar estaciones que están instaladas pero no se están utilizando, lo que puede servir para análisis de optimización y redistribución de estaciones.

```sql
SELECT 
  s.name AS station_name,
  CONCAT(s.latitude, ",", s.longitude) AS station_coor
FROM 
  `bigquery-public-data.new_york_citibike.citibike_stations` s
LEFT JOIN
  `laboratoria-433402.new_york_citibike.citibike_stations_used` u
ON 
  TRIM(LOWER(s.name)) = TRIM(LOWER(u.station_name))
WHERE 
  u.station_name IS NULL

```
