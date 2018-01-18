<!--
Used in:
sql-database-elastic-pool.md 
-->

 
### <a name="basic-elastic-pool-limits"></a>Límites de grupo elástico básico

| eDTU por grupo | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Almacenamiento incluido por grupo (GB) | 5 | 10 | 20  | 29 | 39 | 78 | 117 | 156 |
| Opciones de almacenamiento máximo por grupo (GB) | 5 | 10 | 20  | 29 | 39 | 78 | 117 | 156 |
| Almacenamiento máximo de OLTP en memoria por grupo (GB) | N/D | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| Máximo número de bases de datos por grupo | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Cantidad máxima de trabajos (solicitudes) simultáneos por grupo | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Cantidad máxima de inicios de sesión simultáneos por grupo | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Cantidad máxima de sesiones simultáneas por grupo | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Opciones de cantidad mínima de eDTU por base de datos | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Opciones de cantidad máxima de eDTU por base de datos | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Almacenamiento máximo por base de datos (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Límites de grupo elástico estándar

| eDTU por grupo | **50** | **100** | **200** | **300** | **400** | **800**| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Almacenamiento incluido por grupo (GB) | 50 | 100 | 200 | 300 | 400 | 800 | 
| Opciones de almacenamiento máximo por grupo (GB)* | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 | 
| Almacenamiento máximo de OLTP en memoria por grupo (GB) | N/D | N/D | N/D | N/D | N/D | N/D | 
| Máximo número de bases de datos por grupo | 100 | 200 | 500 | 500 | 500 | 500 | 
| Cantidad máxima de trabajos (solicitudes) simultáneos por grupo | 100 | 200 | 400 | 600 | 800 | 1600 |
| Cantidad máxima de inicios de sesión simultáneos por grupo | 100 | 200 | 400 | 600 | 800 | 1600 |
| Cantidad máxima de sesiones simultáneas por grupo | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Opciones de cantidad mínima de eDTU por base de datos** | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Opciones de cantidad máxima de eDTU por base de datos** | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Almacenamiento máximo por base de datos (GB)* | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Límites de grupo elástico estándar (continuación) 

| eDTU por grupo | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Almacenamiento incluido por grupo (GB) | 1200 | 1600 | 2000 | 2.500 | 3000 | 
| Opciones de almacenamiento máximo por grupo (GB)* | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Almacenamiento máximo de OLTP en memoria por grupo (GB) | N/D | N/D | N/D | N/D | N/D | 
| Máximo número de bases de datos por grupo | 500 | 500 | 500 | 500 | 500 | 
| Cantidad máxima de trabajos (solicitudes) simultáneos por grupo | 2400 | 3200 | 4000 | 5000 | 6000 |
| Cantidad máxima de inicios de sesión simultáneos por grupo | 2400 | 3200 | 4000 | 5000 | 6000 |
| Cantidad máxima de sesiones simultáneas por grupo | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Opciones de cantidad mínima de eDTU por base de datos** | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Opciones de cantidad máxima de eDTU por base de datos** | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Opciones de almacenamiento máximo por base de datos (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Límites de grupo elástico premium

| eDTU por grupo | **125** | **250** | **500** | **1000** | **1500**| 
|:---|---:|---:|---:| ---: | ---: | 
| Almacenamiento incluido por grupo (GB) | 250 | 500 | 750 | 1024 | 1536 | 
| Opciones de almacenamiento máximo por grupo (GB)* | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Almacenamiento máximo de OLTP en memoria por grupo (GB) | 1 | 2 | 4 | 10 | 12 | 
| Máximo número de bases de datos por grupo | 50 | 100 | 100 | 100 | 100 | 
| Cantidad máxima de trabajos simultáneos por grupo (solicitudes) | 200 | 400 | 800 | 1600 | 2400 | 
| Cantidad máxima de inicios de sesión simultáneos por grupo | 200 | 400 | 800 | 1600 | 2400 |
| Cantidad máxima de sesiones simultáneas por grupo | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Cantidad mínima de eDTU por base de datos | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 | 
| Cantidad máxima de eDTU por base de datos | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Almacenamiento máximo por base de datos (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Límites de grupo elástico premium (continuación) 

| eDTU por grupo | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: | 
| Almacenamiento incluido por grupo (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Opciones de almacenamiento máximo por grupo (GB)* | 2048 | 2560 | 3072 | 3548 | 4096|
| Almacenamiento máximo de OLTP en memoria por grupo (GB) | 16 | 20  | 24 | 28 | 32 |
| Máximo número de bases de datos por grupo | 100 | 100 | 100 | 100 | 100 | 
| Cantidad máxima de trabajos (solicitudes) simultáneos por grupo | 3200 | 4000 | 4800 | 5600 | 6400 |
| Cantidad máxima de inicios de sesión simultáneos por grupo | 3200 | 4000 | 4800 | 5600 | 6400 |
| Cantidad máxima de sesiones simultáneas por grupo | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Opciones de cantidad mínima de eDTU por base de datos | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Opciones de cantidad máxima de eDTU por base de datos | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Almacenamiento máximo por base de datos (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-rs-elastic-pool-limits"></a>Límites de grupo elástico RS Premium

| eDTU por grupo | **125** | **250** | **500** | **1000** |
|:---|---:|---:|---:| ---: | ---: | 
| Almacenamiento incluido por grupo (GB) | 250 | 500 | 750 | 750 |
| Opciones de almacenamiento máximo por grupo (GB)* | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 
| Almacenamiento máximo de OLTP en memoria por grupo (GB) | 1 | 2 | 4 | 10 |
| Máximo número de bases de datos por grupo | 50 | 100 | 100 | 100 |
| Cantidad máxima de trabajos (solicitudes) simultáneos por grupo | 200 | 400 | 800 | 1600 |
| Cantidad máxima de inicios de sesión simultáneos por grupo | 200 | 400 | 800 | 1600 |
| Cantidad máxima de sesiones simultáneas por grupo | 30000 | 30000 | 30000 | 30000 |
| Opciones de cantidad mínima de eDTU por base de datos | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 |
| Opciones de cantidad máxima de eDTU por base de datos | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 
| Almacenamiento máximo por base de datos (GB)* | 1024 | 1024 | 1024 | 1024 | 
||||||||

> [!IMPORTANT]
> \* Los tamaños de almacenamiento mayores que la cantidad de almacenamiento incluida están en su versión preliminar y pueden generar costos adicionales. Para obtener información detallada, consulte la [página de precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Los tamaños de almacenamiento mayores que la cantidad de almacenamiento incluida están en su versión preliminar y pueden generar costos adicionales. Para obtener información detallada, consulte la [página de precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
>
> \* En el nivel Premium, más de 1 TB de almacenamiento se encuentra actualmente disponible en las siguientes regiones: este de Australia, sureste de Australia, centro de Canadá, este de Canadá, centro de Francia, centro de Alemania, este de Japón, centro de Corea, centro y sur de EE. UU., sudeste de Asia, este de EE. UU. 2, oeste de EE. UU., Virginia Gob. EE. UU. y Europa Occidental. 
>
>\*\* La cantidad mín. o máx. de eDTU por base de datos, a partir de 200 eDTU en los grupos **Estándar**, está en versión preliminar.
>
