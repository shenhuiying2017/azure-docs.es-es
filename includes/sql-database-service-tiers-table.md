<!--
Used in:
sql-database-performance-guidance.md  
sql-database-resource-limits.md
sql-database-service-tiers.md  
-->

### <a name="basic-service-tier"></a>Nivel de servicio Básico
| **Nivel de rendimiento** | **Básico** |
| --- | :---: |
| DTU máx. | 5 |
| Tamaño máximo de base de datos* |2 GB|
| Almacenamiento máximo de OLTP en memoria |N/D |
| Cantidad máxima de trabajos (solicitudes) simultáneos |30 |
| Máximo de inicios de sesión simultáneos |30 |
| N.º máximo de sesiones simultáneas |300 |
|||

### <a name="standard-service-tier"></a>Nivel de servicio Estándar
| **Nivel de rendimiento** | **S0** | **S1** | **S2** | **S3** |
| --- |---:| ---:|---:|---:|---:|
| DTU máx. | 10 | 20 | | 50 | 100 |
| Tamaño máximo de base de datos* | 250 GB| 250 GB | 250 GB | 250 GB |
| Almacenamiento máximo de OLTP en memoria | N/D | N/D | N/D | N/D |
| Cantidad máxima de trabajos (solicitudes) simultáneos| 60 | 90 | 120 | 200 |
| Máximo de inicios de sesión simultáneos | 60 | 90 | 120 | 200 |
| N.º máximo de sesiones simultáneas |600 | 900 | 1200 | 2400 |
||||||

### <a name="premium-service-tier"></a>Nivel de servicio Premium 
| **Nivel de rendimiento** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| --- |---:|---:|---:|---:|---:|---:|
| DTU máx. | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Tamaño máximo de base de datos* | 500 GB | 500 GB | 500 GB | 500 GB | 4 TB | 4 TB |
| Almacenamiento máximo de OLTP en memoria | 1 GB | 2 GB | 4 GB | 8 GB | 14 GB | 32 GB |
| Cantidad máxima de trabajos (solicitudes) simultáneos| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Máximo de inicios de sesión simultáneos | 200 | 400| 800| 1600| 2400| 6400 |
| N.º máximo de sesiones simultáneas | 30000| 30000| 30000| 30000| 30000| 30000 |
|||||||

### <a name="premium-rs-service-tier"></a>Nivel de servicio RS Premium 
| **Nivel de rendimiento** | **PRS1** | **PRS2** | **PRS4** | **PRS6** |
| --- |---:|---:|---:|---:|---:|---:|
| DTU máx. | 125 | 250 | 500 | 1000 |
| Tamaño máximo de base de datos* | 500 GB | 500 GB | 500 GB | 500 GB |
| Almacenamiento máximo de OLTP en memoria | 1 GB | 2 GB | 4 GB | 8 GB |
| Cantidad máxima de trabajos (solicitudes) simultáneos| 200 | 400 | 800 | 1600 |
| Máximo de inicios de sesión simultáneos | 200 | 400| 800| 1600|
| N.º máximo de sesiones simultáneas | 30000| 30000| 30000| 30000|
|||||||

\* El tamaño máximo de la base de datos se refiere al tamaño máximo de los datos en la base de datos. 