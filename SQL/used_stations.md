# Tabla con todas las estaciones usadas

### Descripción de la Query

Esta query se utiliza para obtener una lista única de todas las estaciones de Citi Bike (tanto las estaciones de inicio como las de finalización de viajes) junto con sus coordenadas geográficas. El objetivo es identificar todas las estaciones utilizadas en el dataset y obtener su información de ubicación.

1. **Extracción de las estaciones de inicio y fin:**
    - Se seleccionan los nombres y coordenadas (`start_station_name`, `start_station_coor`) de las estaciones de inicio de los viajes que no sean nulos.
    - De manera similar, se seleccionan los nombres y coordenadas (`end_station_name`, `end_station_coor`) de las estaciones de finalización de los viajes que no sean nulos.
2. **Uso de `UNION DISTINCT`:**
    - La cláusula `UNION DISTINCT` se utiliza para combinar las dos selecciones (de estaciones de inicio y de fin), eliminando duplicados. Esto asegura que cada estación aparezca una sola vez, ya sea como estación de inicio o de fin.
3. **Resultado final:**
    - Se genera una tabla que contiene el nombre de cada estación (`station_name`) y sus respectivas coordenadas geográficas (`station_coor`).
    - Este resultado proporciona un conjunto limpio y consolidado de todas las estaciones presentes en el dataset de Citi Bike, junto con su ubicación precisa.

Esta query es útil para analizar la distribución geográfica de las estaciones y comprender qué estaciones están activas tanto para iniciar como para finalizar viajes.

```sql
WITH station_coords AS (
    SELECT DISTINCT 
        start_station_name AS station_name, 
        start_station_coor AS station_coor
    FROM 
        `laboratoria-433402.new_york_citibike.citibike_trips`
    WHERE 
        start_station_name IS NOT NULL
    UNION DISTINCT
    SELECT DISTINCT 
        end_station_name AS station_name, 
        end_station_coor AS station_coor
    FROM 
        `laboratoria-433402.new_york_citibike.citibike_trips`
    WHERE 
        end_station_name IS NOT NULL
)

SELECT 
    station_name,
    station_coor
FROM 
    station_coords

```
