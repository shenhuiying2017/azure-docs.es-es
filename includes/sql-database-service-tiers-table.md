<!--
Used in:
sql-database-performance-guidance.md 
sql-database-single-database-resources.md 
-->

### <a name="basic-service-tier"></a>Nivel de servicio Básico
| **Nivel de rendimiento** | **Básico** |
| :--- | --: |
| DTU máx. | 5 |
| Almacenamiento incluido (GB) | 2 |
| Opciones de almacenamiento máximo (GB) | 2 |
| Almacenamiento máximo de OLTP en memoria (GB) |N/D |
| Cantidad máxima de trabajos (solicitudes) simultáneos | 30 |
| Máximo de inicios de sesión simultáneos | 30 |
| N.º máximo de sesiones simultáneas | 300 |
|||

### <a name="standard-service-tier"></a>Nivel de servicio Estándar
| **Nivel de rendimiento** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| DTU máx.** | 10 | 20  | 50 | 100 |
| Almacenamiento incluido (GB) | 250 | 250 | 250 | 250 |
| Opciones de almacenamiento máximo (GB)* | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Almacenamiento máximo de OLTP en memoria (GB) | N/D | N/D | N/D | N/D |
| Cantidad máxima de trabajos (solicitudes) simultáneos| 60 | 90 | 120 | 200 |
| Máximo de inicios de sesión simultáneos | 60 | 90 | 120 | 200 |
| N.º máximo de sesiones simultáneas |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Nivel de servicio Estándar (continuación)
| **Nivel de rendimiento** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| DTU máx.** | 200 | 400 | 800 | 1600 | 3000 |
| Almacenamiento incluido (GB) | 250 | 250 | 250 | 250 | 250 |
| Opciones de almacenamiento máximo (GB)* | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Almacenamiento máximo de OLTP en memoria (GB) | N/D | N/D | N/D | N/D |N/D |
| Cantidad máxima de trabajos (solicitudes) simultáneos| 400 | 800 | 1600 | 3200 |6000 |
| Máximo de inicios de sesión simultáneos | 400 | 800 | 1600 | 3200 |6000 |
| N.º máximo de sesiones simultáneas |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Nivel de servicio Premium 
| **Nivel de rendimiento** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| DTU máx. | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Almacenamiento incluido (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Opciones de almacenamiento máximo (GB)* | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Almacenamiento máximo de OLTP en memoria (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Cantidad máxima de trabajos (solicitudes) simultáneos| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Máximo de inicios de sesión simultáneos | 200 | 400 | 800 | 1600 | 2400 | 6400 |
| N.º máximo de sesiones simultáneas | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

### <a name="premium-rs-service-tier"></a>Nivel de servicio RS Premium 
| **Nivel de rendimiento** | **PRS1** | **PRS2** | **PRS4** | **PRS6** |
| :--- |---:|---:|---:|---:|---:|---:|
| DTU máx. | 125 | 250 | 500 | 1000 |
| Almacenamiento incluido (GB) | 500 | 500 | 500 | 500 |
| Opciones de almacenamiento máximo (GB)* | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 |
| Almacenamiento máximo de OLTP en memoria (GB) | 1 | 2 | 4 | 8 |
| Cantidad máxima de trabajos (solicitudes) simultáneos| 200 | 400 | 800 | 1600 |
| Máximo de inicios de sesión simultáneos | 200 | 400 | 800 | 1600 |
| N.º máximo de sesiones simultáneas | 30000 | 30000 | 30000 | 30000 |
|||||||

> [!IMPORTANT]
> \* Los tamaños de almacenamiento mayores que la cantidad de almacenamiento incluida están en su versión preliminar y pueden generar costos adicionales. Para obtener información detallada, vea [Precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
>
>\* En el nivel Premium, más de 1 TB de almacenamiento se encuentra actualmente disponible en las siguientes regiones: este de Australia, sureste de Australia, centro de Canadá, este de Canadá, centro de Francia, centro de Alemania, este de Japón, centro de Corea, centro y sur de EE. UU., sudeste de Asia, este de EE. UU. 2, oeste de EE. UU., Virginia Gob. EE. UU. y Europa Occidental. Consulte [Limitaciones actuales P11-P15](../articles/sql-database/sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 
>\*\* La cantidad máx. de DTU por base de datos a partir de 200 DTU en Estándar está en versión preliminar.
>
