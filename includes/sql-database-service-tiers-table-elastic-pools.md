<!--
Used in:
sql-database-elastic-pool.md   
sql-database-resource-limits.md
sql-database-service-tiers.md  
-->
 
### <a name="basic-elastic-pool-limits"></a>Límites de grupo elástico básico

| Tamaño del grupo (eDTU)  | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Almacenamiento máximo de datos por grupo* | 5 GB | 10 GB | 20 GB | 29 GB | 39 GB | 78 GB | 117 GB | 156 GB |
| Almacenamiento máximo de OLTP en memoria por grupo | N/D | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| Máximo número de bases de datos por grupo | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Cantidad máxima de trabajos (solicitudes) simultáneos por grupo | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Cantidad máxima de inicios de sesión simultáneos por grupo | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Cantidad máxima de sesiones simultáneas por grupo | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Cantidad mínima de eDTU por base de datos | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Cantidad máxima de eDTU por base de datos | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Almacenamiento máximo de datos por base de datos | 2 GB | 2 GB | 2 GB | 2 GB | 2 GB | 2 GB | 2 GB | 2 GB | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Límites de grupo elástico estándar

| Tamaño del grupo (eDTU)  | **50** | **100** | **200**** | **300**** | **400**** | **800****| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Almacenamiento máximo de datos por grupo* | 50 GB| 100 GB| 200 GB | < 300 GB| 400 GB | 800 GB | 
| Almacenamiento máximo de OLTP en memoria por grupo | N/D | N/D | N/D | N/D | N/D | N/D | 
| Máximo número de bases de datos por grupo | 100 | 200 | 500 | 500 | 500 | 500 | 
| Cantidad máxima de trabajos (solicitudes) simultáneos por grupo | 100 | 200 | 400 | 600 |  800 | 1600 |
| Cantidad máxima de inicios de sesión simultáneos por grupo | 100 | 200 | 400 | 600 |  800 | 1600 |
| Cantidad máxima de sesiones simultáneas por grupo | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Cantidad mínima de eDTU por base de datos** | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Cantidad máxima de eDTU por base de datos** | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Almacenamiento máximo de datos por base de datos | 50 GB | 100 GB | 200 GB | 250 GB | 250 GB | 250 GB |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Límites de grupo elástico estándar (continuación) 

| Tamaño del grupo (eDTU)  |  **1200**** | **1600**** | **2000**** | **2500**** | **3000**** |
|:---|---:|---:|---:| ---: | ---: |
| Almacenamiento máximo de datos por grupo* | 1,2 TB | 1,6 TB | 2 TB | 2,4 TB | 2,9 TB | 
| Almacenamiento máximo de OLTP en memoria por grupo | N/D | N/D | N/D | N/D | N/D | 
| Máximo número de bases de datos por grupo | 500 | 500 | 500 | 500 | 500 | 
| Cantidad máxima de trabajos (solicitudes) simultáneos por grupo |  2400 | 3200 | 4000 | 5000 | 6000 |
| Cantidad máxima de inicios de sesión simultáneos por grupo |  2400 | 3200 | 4000 | 5000 | 6000 |
| Cantidad máxima de sesiones simultáneas por grupo | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Cantidad mínima de eDTU por base de datos** | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Cantidad máxima de eDTU por base de datos** | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Almacenamiento máximo de datos por base de datos | 250 GB | 250 GB | 250 GB | 250 GB | 250 GB | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Límites de grupo elástico premium

| Tamaño del grupo (eDTU)  | **125** | **250** | **500** | **1000** | **1500*****| 
|:---|---:|---:|---:| ---: | ---: | 
| Almacenamiento máximo de datos por grupo* | 250 GB | 500 GB | 750 GB | 1 TB | 1,5 TB | 
| Almacenamiento máximo de OLTP en memoria por grupo | 1 GB| 2 GB| 4 GB| 10 GB| 12 GB| 
| Máximo número de bases de datos por grupo | 50 | 100 | 100 | 100 | 100 |  
| Cantidad máxima de trabajos simultáneos por grupo (solicitudes) | 200 | 400 | 800 | 1600 |  2400 | 
| Cantidad máxima de inicios de sesión simultáneos por grupo | 200 | 400 | 800 | 1600 |  2400 |
| Cantidad máxima de sesiones simultáneas por grupo | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Cantidad mínima de eDTU por base de datos | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000 | 
| Cantidad máxima de eDTU por base de datos | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000 |
| Almacenamiento máximo de datos por base de datos | 250 GB | 500 GB | 500 GB | 500 GB | 500 GB | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Límites de grupo elástico premium (continuación) 

| Tamaño del grupo (eDTU) | **2000***** | **2500***** | **3000***** | **3500***** | **4000*****|
|:---|---:|---:|---:| ---: | ---: | 
| Almacenamiento máximo de datos por grupo* | 2 TB | 2,5 TB | 3 TB | 3,5 TB | 4 TB |
| Almacenamiento máximo de OLTP en memoria por grupo | 16 GB | 20 GB | 24 GB | 28 GB | 32 GB |
| Máximo número de bases de datos por grupo | 100 | 100 | 100 | 100 | 100 | 
| Cantidad máxima de trabajos (solicitudes) simultáneos por grupo |  3200 | 4000 | 4800 | 5600 | 6400 |
| Cantidad máxima de inicios de sesión simultáneos por grupo |  3200 | 4000 | 4800 | 5600 | 6400 |
| Cantidad máxima de sesiones simultáneas por grupo | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Cantidad mínima de eDTU por base de datos | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 |  0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Cantidad máxima de eDTU por base de datos | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Almacenamiento máximo de datos por base de datos | 500 GB | 500 GB | 500 GB | 500 GB | 500 GB | 
||||||||

### <a name="premium-rs-elastic-pool-limits"></a>Límites de grupo elástico RS Premium

| Tamaño del grupo (eDTU)  | **125** | **250** | **500** | **1000** |
|:---|---:|---:|---:| ---: | ---: | 
| Almacenamiento máximo de datos por grupo* | 250 GB| 500 GB | 750 GB | 750 GB |
| Almacenamiento máximo de OLTP en memoria por grupo | 1 GB | 2 GB | 4 GB | 10 GB |
| Máximo número de bases de datos por grupo | 50 | 100 | 100 | 100 |
| Cantidad máxima de trabajos (solicitudes) simultáneos por grupo | 200 | 400 | 800 | 1600 |
| Cantidad máxima de inicios de sesión simultáneos por grupo | 200 | 400 | 800 | 1600 |
| Cantidad máxima de sesiones simultáneas por grupo | 30000 | 30000 | 30000 | 30000 |
| Cantidad mínima de eDTU por base de datos | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 |
| Cantidad máxima de eDTU por base de datos | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 
| Almacenamiento máximo de datos por base de datos | 250 GB | 500 GB | 500 GB | 500 GB | 
||||||||

> [!IMPORTANT]
>\* Las bases de datos agrupadas comparten almacenamiento de grupo, por lo que el almacenamiento de los datos en un grupo elástico se limita al almacenamiento de grupo restante o al almacenamiento máximo por base de datos, el que sea menor. 
>
>
>\*\*La cantidad mín. o máx. de eDTU por base de datos, a partir de 200 eDTU, está en versión preliminar pública.
>
>\*\*\*El almacenamiento de datos máximo predeterminado por grupo en el caso de los grupos Premium con 500 eDTU, o más, es 750 GB. Para obtener el mayor tamaño de almacenamiento de datos máximo por grupo Premium para 1000 o más eDTU, este tamaño debe seleccionarse explícitamente mediante Azure Portal, [PowerShell](../articles/sql-database/sql-database-elastic-pool.md#manage-sql-database-elastic-pools-using-powershell), la [CLI de Azure](../articles/sql-database/sql-database-elastic-pool.md#manage-sql-database-elastic-pools-using-the-azure-cli) o la [API de REST](../articles/sql-database/sql-database-elastic-pool.md#manage-sql-database-elastic-pools-using-the-rest-api). Los grupos Premium con más de 1 TB de almacenamiento se encuentran actualmente en versión preliminar pública en las siguientes regiones: este de EE. UU. 2, oeste de EE. UU., Virginia Gob. EE. UU., Europa Occidental, centro de Alemania, Asia Suroriental, Japón Oriental, este de Australia, centro de Canadá y este de Canadá. En la actualidad, el almacenamiento máximo por grupo en las restantes regiones está limitado a 750 GB.
>
