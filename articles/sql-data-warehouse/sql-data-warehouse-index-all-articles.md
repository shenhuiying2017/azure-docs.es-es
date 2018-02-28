---
title: Todos los temas de SQL Data Warehouse | Microsoft Docs
description: "Tabla de todos los temas del servicio Almacenamiento de datos SQL que se encuentra en http://azure.microsoft.com/documentation/articles/, con títulos y descripciones."
services: sql-data-warehouse
documentationcenter: 
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a26a6dec-9c08-4415-8f58-4ee1dd41f718
ms.service: sql-data-warehouse
ms.workload: sql-data-warehouse
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: reference
ms.date: 03/30/2017
ms.author: barbkess
ms.openlocfilehash: 9fe41f12960dc099700e01573b4f03ebf63f8749
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Todos los temas del servicio Almacenamiento de datos SQL de Azure
En este documento se muestran todos los temas relacionados directamente con el servicio **Almacenamiento de datos SQL** de Azure. Puede buscar en esta página web las palabras clave mediante **Ctrl+F**, para encontrar los temas de interés.

## <a name="new"></a>Nuevo
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 1 |[Copias de seguridad de SQL Data Warehouse](sql-data-warehouse-backups.md) |Aprenda sobre las copias de seguridad de base de datos integradas de SQL Data Warehouse que permiten restaurar una instancia de Azure SQL Data Warehouse a un punto de restauración o una región geográfica distinta. |

## <a name="updated-articles-sql-data-warehouse"></a>Artículos actualizados, SQL Data Warehouse
En esta sección se enumeran los artículos que se han actualizado recientemente. Concretamente, aquellos en los que la actualización fuera grande o considerable. Para cada artículo actualizado, se muestra un fragmento del texto agregado. Los artículos se actualizaron dentro de este intervalo de fechas: del **22-08-2016** al **05-10-2016**.

| &nbsp; | Artículo | Texto actualizado, fragmento | Se actualiza cuando |
| ---:|:--- |:--- |:--- |
| 2 |[Load data from Azure blob storage into SQL Data Warehouse (PolyBase) [Carga de datos de Almacenamiento de blobs de Azure en Almacenamiento de datos SQL (PolyBase)]](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |/- To track bytes and files SELECT  r.command,  s.request_id,  r.status,  count(distinct input_name) as nbr_files,  sum(s.bytes_processed)/1024/1024 as gb_processed FROM  sys.dm_pdw_exec_requests r  inner join sys.dm_pdw_dms_external_work s  on r.request_id = s.request_id WHERE  r. label  = 'CTAS : Load  cso . DimProduct  '  OR r. label  = 'CTAS : Load  cso . FactOnlineSales  ' GROUP BY  r.command,  s.request_id,  r.status ORDER BY  nbr_files desc,  gb_processed desc; |2016-09-07 |
| 3 |[SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md) |** ¿Puedo restaurar un almacenamiento de datos en pausa? ** Para restaurar un almacenamiento de datos que esté en pausa, primero debe ponerlo en línea. Después de que el almacén de datos vuelve a estar en línea, dispone de siete días de puntos de restauración para elegir. ** Restauración a una región con redundancia geográfica ** Si usa el almacenamiento con redundancia geográfica, puede restaurar el almacenamiento de datos a su centro de datos emparejado de otra región geográfica. El almacenamiento de datos se restaura desde la última copia de seguridad diaria. ** Restaurar la escala de tiempo ** Puede restaurar una base de datos a cualquier punto de restauración de los últimos siete días. Las instantáneas comienzan cada cuatro a ocho horas y están disponibles durante siete días. Cuando una instantánea tiene una antigüedad superior a siete días, caduca y su punto de restauración ya no está disponible. ** Costos de restauración ** Los cargos de almacenamiento por el almacenamiento de datos restaurado se facturan con la tarifa de Azure Premium Storage. Si se pausa un almacenamiento de datos restaurado, se le cobrará por el almacenamiento según la tarifa de Azure Premium Storage. La ventaja de pausar es que no se le cobra |2016-09-29 |

## <a name="get-started"></a>Primeros pasos
| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 4 |[Autenticación a Almacenamiento de datos SQL de Azure](sql-data-warehouse-authentication.md) |Autenticación de Azure Active Directory (AAD) y SQL Server a Almacenamiento de datos SQL de Azure |
| 5 |[Procedimientos recomendados para Almacenamiento de datos SQL de Azure](sql-data-warehouse-best-practices.md) |Recomendaciones y procedimientos recomendados que debe saber para desarrollar soluciones de Almacenamiento de datos SQL de Azure. Esto le ayudará a tener éxito. |
| 6 |[Controladores de Almacenamiento de datos SQL de Azure](sql-data-warehouse-connection-strings.md) |Cadenas de conexión y controladores de Almacenamiento de datos SQL |
| 7 |[Conexión a Almacenamiento de datos SQL de Azure](sql-data-warehouse-connect-overview.md) |Cómo encontrar el nombre de servidor y la cadena de conexión de su instancia de Azure SQL Data Warehouse |
| 8 |[Análisis de datos con Aprendizaje automático de Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) |Use Aprendizaje automático de Azure para crear un aprendizaje automático predictivo con los datos almacenados en Almacenamiento de datos SQL de Azure. |
| 9 |[Consulta de Almacenamiento de datos SQL de Azure (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) |Consulta de Almacenamiento de datos SQL Azure con las utilidades de la línea de comandos sqlcmd. |
| 10 |[Creación de una base de datos de Almacenamiento de datos SQL mediante Transact-SQL (TSQL)](sql-data-warehouse-get-started-create-database-tsql.md) |Aprenda a crear una base de datos de Almacenamiento de datos SQL de Azure con TSQL |
| 11 |[Creación de una incidencia de soporte técnico para Almacenamiento de datos SQL](sql-data-warehouse-get-started-create-support-ticket.md) |Creación de una incidencia de soporte técnico en Almacenamiento de datos SQL de Azure. |
| 12 |[Carga de datos con la Factoría de datos de Azure](sql-data-warehouse-get-started-load-with-azure-data-factory.md) |Más información sobre Factoría de datos de Azure |
| 13 |[Carga de datos con PolyBase en Almacenamiento de datos SQL](sql-data-warehouse-get-started-load-with-polybase.md) |Obtenga información sobre qué es PolyBase y cómo usarlo en escenarios de almacenamiento de datos. |
| 14 |[Creación de una instancia de Almacenamiento de datos SQL de Azure](sql-data-warehouse-get-started-provision.md) |Aprenda a crear un almacenamiento de datos SQL de Azure en el Portal de Azure |
| 15 |[Creación de Almacenamiento de datos SQL con Powershell](sql-data-warehouse-get-started-provision-powershell.md) |Creación de Almacenamiento de datos SQL con Powershell |
| 16 |[Visualización de datos con Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) |Visualización de datos de Almacenamiento de datos SQL con Power BI |
| 17 |[Consultas en Almacenamiento de datos SQL de Azure (Visual Studio)](sql-data-warehouse-query-visual-studio.md) |Consultas en Almacenamiento de datos SQL con Visual Studio. |

## <a name="develop"></a>Desarrollo
| &nbsp; | Título | Description |
| ---:|:--- |:--- |
| 18 |[Optimización de transacciones para Almacenamiento de datos SQL](sql-data-warehouse-develop-best-practices-transactions.md) |Guía de procedimientos recomendados sobre la escritura de actualizaciones de transacciones eficientes en Almacenamiento de datos SQL de Azure |
| 19 |[Simultaneidad y administración de cargas de trabajo en Almacenamiento de datos SQL](sql-data-warehouse-develop-concurrency.md) |Obtenga información sobre la simultaneidad y la administración de cargas de trabajo en Almacenamiento de datos SQL de Azure para el desarrollo de soluciones. |
| 20 | |[Create Table As Select (CTAS) en Almacenamiento de datos SQL](sql-data-warehouse-develop-ctas.md) |Sugerencias para la codificación con la instrucción Create table as select (CTAS) en Almacenamiento de datos SQL de Azure para el desarrollo de soluciones. |
| 21 |[SQL dinámico en Almacenamiento de datos SQL](sql-data-warehouse-develop-dynamic-sql.md) |Sugerencias para usar SQL dinámico en Almacenamiento de datos SQL Azure para desarrollar soluciones. |
| 22 |[Opciones de Group by en Almacenamiento de datos SQL](sql-data-warehouse-develop-group-by-options.md) |Sugerencias para implementar opciones de Group by en Almacenamiento de datos SQL de Azure para el desarrollo de soluciones. |
| 23 |[Uso de etiquetas para instrumentar consultas en Almacenamiento de datos SQL](sql-data-warehouse-develop-label.md) |Sugerencias para usar etiquetas para instrumentar las consultas en Almacenamiento de datos SQL de Azure para el desarrollo de soluciones. |
| 24 |[Bucles en Almacenamiento de datos SQL](sql-data-warehouse-develop-loops.md) |Sugerencias para los bucles de Transact-SQL en Almacenamiento de datos SQL de Azure para el desarrollo de soluciones. |
| 25 |[Procedimientos almacenados en el Almacenamiento de datos SQL](sql-data-warehouse-develop-stored-procedures.md) |Sugerencias para implementar procedimientos almacenados en el Almacenamiento de datos SQL Azure para el desarrollo de soluciones. |
| 26 |[Transacciones en el Almacenamiento de datos SQL](sql-data-warehouse-develop-transactions.md) |Sugerencias para implementar transacciones en el Almacenamiento de datos SQL Azure para el desarrollo de soluciones. |
| 27 |[Esquemas definidos por el usuario en el Almacenamiento de datos SQL](sql-data-warehouse-develop-user-defined-schemas.md) |Sugerencias para usar los esquemas Transact-SQL en el Almacenamiento de datos SQL Azure para desarrollar soluciones. |
| 28 |[Asignación de variables en el Almacenamiento de datos SQL](sql-data-warehouse-develop-variable-assignment.md) |Sugerencias para la asignación de variables de Transact-SQL en el Almacenamiento de datos SQL Azure para desarrollar soluciones. |
| 29 |[Vistas en el Almacenamiento de datos SQL](sql-data-warehouse-develop-views.md) |Sugerencias para usar las vistas Transact-SQL en el Almacenamiento de datos SQL Azure para desarrollar soluciones. |
| 30 |[Decisiones de diseño y técnicas de codificación para el Almacenamiento de datos SQL](sql-data-warehouse-overview-develop.md) |Conceptos de desarrollo, decisiones de diseño, recomendaciones y técnicas de codificación para el Almacenamiento de datos SQL. |

## <a name="manage"></a>Manage
| &nbsp; | Título | Description |
| ---:|:--- |:--- |
| 31 |[Administración de la potencia de proceso en Almacenamiento de datos SQL de Azure (información general)](sql-data-warehouse-manage-compute-overview.md) |Funcionalidades de escalado horizontal del rendimiento en Almacenamiento de datos SQL de Azure. Realice el escalado horizontal ajustando las DWU o pause y reanude los recursos de proceso para ahorrar costos. |
| 32 |[Administración de la potencia de proceso en Almacenamiento de datos SQL de Azure (Portal de Azure)](sql-data-warehouse-manage-compute-portal.md) |Tareas del Portal de Azure para administrar la potencia de proceso. Escalado de los recursos de proceso ajustando DWU. Pausar y reanudar recursos de proceso para ahorrar costos. |
| 33 |[Administración de la potencia de proceso en Almacenamiento de datos SQL de Azure (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) |Tareas de PowerShell para administrar la potencia de proceso. Escalado de los recursos de proceso ajustando DWU. Pausar y reanudar recursos de proceso para ahorrar costos. |
| 34 |[Administración de la potencia de proceso en Almacenamiento de datos SQL de Azure (REST)](sql-data-warehouse-manage-compute-rest-api.md) |Tareas de PowerShell para administrar la potencia de proceso. Escalado de los recursos de proceso ajustando DWU. Pausar y reanudar recursos de proceso para ahorrar costos. |
| 35 |[Administración de la potencia de proceso en Almacenamiento de datos SQL de Azure (T-SQL)](sql-data-warehouse-manage-compute-tsql.md) |Tareas de Transact-SQL (T-SQL) para el escalado horizontal del rendimiento ajustando DWU. Ahorre costes reduciendo el escalado fuera de horas punta. |
| 36 |[Supervisión de la carga de trabajo mediante DMV](sql-data-warehouse-manage-monitor.md) |Obtenga información sobre cómo supervisar la carga de trabajo mediante DMV. |
| 37 |[Administración de base datos en Almacenamiento de datos SQL de Azure](sql-data-warehouse-overview-manage.md) |Información general de administración de bases de datos de Almacenamiento de datos SQL Incluye herramientas de administración, DWU y rendimiento de escalado horizontal, solución de problemas de rendimiento de las consultas, el establecimiento de directivas de seguridad y la restauración una base de datos de daños en los datos o de un apagón regional. |
| 38 |[Supervisión de las consultas de usuario en Almacenamiento de datos SQL de Azure](sql-data-warehouse-overview-manage-user-queries.md) |Información general de las consideraciones, prácticas recomendadas y tareas para supervisar las consultas de usuario en Almacenamiento de datos SQL de Azure. |
| 39 |[SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md) |Información general de las opciones de restauración de bases de datos para recuperar una base de datos en Almacenamiento de datos SQL de Azure. |
| 40 |[Restauración de instancias de Almacenamiento de datos SQL de Azure (Portal)](sql-data-warehouse-restore-database-portal.md) |Tareas del Portal de Azure para restaurar una instancia de Almacenamiento de datos SQL de Azure. |
| 41 |[Restauración de instancias de Almacenamiento de datos SQL de Azure (PowerShell)](sql-data-warehouse-restore-database-powershell.md) |Tareas de PowerShell para restaurar una instancia de Almacenamiento de datos SQL de Azure. |
| 42 |[Restauración de instancias de Almacenamiento de datos SQL de Azure (API de REST)](sql-data-warehouse-restore-database-rest-api.md) |Tareas de la API de REST para restaurar una instancia de Almacenamiento de datos SQL de Azure. |

## <a name="tables-and-indexes"></a>Tablas e índices
| &nbsp; | Título | Description |
| ---:|:--- |:--- |
| 43 |[Tipos de datos de las tablas en el Almacenamiento de datos SQL](sql-data-warehouse-tables-data-types.md) |Introducción a los tipos de datos de Almacenamiento de datos SQL de Azure. |
| 44 |[Distribución de tablas en Almacenamiento de datos SQL](sql-data-warehouse-tables-distribute.md) |Introducción a la distribución de tablas en Almacenamiento de datos SQL de Azure. |
| 45 |[Indexación de tablas en Almacenamiento de datos SQL](sql-data-warehouse-tables-index.md) |Introducción a la indexación de tablas en Almacenamiento de datos SQL de Azure. |
| 46 |[Información general de tablas en Almacenamiento de datos SQL](sql-data-warehouse-tables-overview.md) |Introducción a las tablas de Almacenamiento de datos SQL de Azure. |
| 47 |[Creación de particiones de tablas en Almacenamiento de datos SQL](sql-data-warehouse-tables-partition.md) |Introducción a la creación de particiones en tablas en Almacenamiento de datos SQL de Azure. |
| 48 |[Administración de estadísticas en tablas en Almacenamiento de datos SQL](sql-data-warehouse-tables-statistics.md) |Introducción a las estadísticas de tablas en Almacenamiento de datos SQL de Azure. |
| 49 |[Tablas temporales en el Almacenamiento de datos SQL](sql-data-warehouse-tables-temporary.md) |Introducción a las tablas temporales en Almacenamiento de datos SQL de Azure. |

## <a name="integrate"></a>Integrate
| &nbsp; | Título | Description |
| ---:|:--- |:--- |
| 50 |[Uso de Factoría de datos de Azure con Almacenamiento de datos SQL](sql-data-warehouse-integrate-azure-data-factory.md) |Sugerencias para usar Factoría de datos de Azure (ADF) con Almacenamiento de datos SQL para el desarrollo de soluciones. |
| 51 |[Uso de Aprendizaje automático de Azure con Almacenamiento de datos SQL](sql-data-warehouse-integrate-azure-machine-learning.md) |Tutorial para usar Aprendizaje automático de Azure con Almacenamiento de datos SQL de Azure para el desarrollo de soluciones. |
| 52 |[Uso de Análisis de transmisiones de Azure con Almacenamiento de datos SQL](sql-data-warehouse-integrate-azure-stream-analytics.md) |Sugerencias para usar Análisis de transmisiones de Azure con Almacenamiento de datos SQL de Azure para el desarrollo de soluciones. |
| 53 |[Uso de Power BI con Almacenamiento de datos SQL](sql-data-warehouse-integrate-power-bi.md) |Sugerencias para usar Power BI con Almacenamiento de datos SQL de Azure para el desarrollo de soluciones. |
| 54 |[Aprovechamiento de otros servicios con Almacenamiento de datos SQL](sql-data-warehouse-overview-integrate.md) |Herramientas y asociados con soluciones que se integran con Almacenamiento de datos SQL. |

## <a name="load"></a>Carga
| &nbsp; | Título | Description |
| ---:|:--- |:--- |
| 55 |[Carga de datos del Almacenamiento de blobs de Azure en Almacenamiento de datos SQL de Azure (Data Factory de Azure)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) |Más información sobre Factoría de datos de Azure |
| 56 |[Load data from Azure blob storage into SQL Data Warehouse (PolyBase) [Carga de datos de Almacenamiento de blobs de Azure en Almacenamiento de datos SQL (PolyBase)]](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |Aprenda a usar PolyBase para cargar datos de Almacenamiento de blobs de Azure en Almacenamiento de datos SQL. Cargue algunas tablas de datos públicos en el esquema Contoso Retail Data Warehouse. |
| 57 |[Carga de datos de SQL Server en Almacenamiento de datos SQL de Azure (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) |Usa bcp para exportar datos de SQL Server a archivos planos, AZCopy para importar datos al Almacenamiento de blobs de Azure y PolyBase para introducir los datos en Almacenamiento de datos SQL de Azure. |
| 58 |[Carga de datos de SQL Server en Almacenamiento de datos SQL de Azure (archivos planos)](sql-data-warehouse-load-from-sql-server-with-bcp.md) |Para datos de tamaño pequeño, utiliza bcp para exportar datos de SQL Server a archivos planos e importar los datos directamente en Almacenamiento de datos SQL de Azure. |
| 59 |[Carga de datos de SQL Server en Almacenamiento de datos SQL de Azure (SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) |Se muestra cómo crear un paquete de SQL Server Integration Services (SSIS) para mover datos desde una gran variedad de orígenes de datos a Almacenamiento de datos SQL. |
| 60 |[Carga de datos con PolyBase en Almacenamiento de datos SQL](sql-data-warehouse-load-from-sql-server-with-polybase.md) |Usa bcp para exportar datos de SQL Server a archivos planos, AZCopy para importar datos al Almacenamiento de blobs de Azure y PolyBase para introducir los datos en Almacenamiento de datos SQL de Azure. |
| 61 |[Guía para el uso de PolyBase en Almacenamiento de datos SQL](sql-data-warehouse-load-polybase-guide.md) |Directrices y recomendaciones para el uso de PolyBase en escenarios de Almacenamiento de datos SQL. |
| 62 |[Carga de datos de ejemplo en Almacenamiento de datos SQL](sql-data-warehouse-load-sample-databases.md) |Carga de datos de ejemplo en Almacenamiento de datos SQL |
| 63 |[Carga de datos con bcp](sql-data-warehouse-load-with-bcp.md) |Obtenga información sobre qué es bcp y cómo usarlo en escenarios de almacenamiento de datos. |
| 64 |[Carga de datos en Almacenamiento de datos SQL de Azure](sql-data-warehouse-overview-load.md) |Conozca los escenarios comunes para la carga de datos en Almacenamiento de datos SQL. Estos incluyen el uso de PolyBase, el almacenamiento de blobs de Azure, archivos planos y el envío de discos. También puede usar herramientas de otros fabricantes. |

## <a name="migrate"></a>Migrar
| &nbsp; | Título | Description |
| ---:|:--- |:--- |
| 65 |[Migración del código SQL a Almacenamiento de datos SQL](sql-data-warehouse-migrate-code.md) |Sugerencias para migrar el código SQL a Almacenamiento de datos SQL de Azure a fin de desarrollar soluciones. |
| 66 |[Migración de los datos](sql-data-warehouse-migrate-data.md) |Sugerencias para migrar los datos a Almacenamiento de datos SQL de Azure a fin de desarrollar soluciones. |
| 67 |[Utilidad de migración de Almacenamiento de datos (vista previa)](sql-data-warehouse-migrate-migration-utility.md) |Migración a Almacenamiento de datos SQL |
| 68 |[Migración del esquema a Almacenamiento de datos SQL](sql-data-warehouse-migrate-schema.md) |Sugerencias para migrar el esquema a Almacenamiento de datos SQL de Azure a fin de desarrollar soluciones. |
| 69 |[Migración de una solución a Almacenamiento de datos SQL](sql-data-warehouse-overview-migrate.md) |Guía de migración para llevar una solución a la plataforma Almacenamiento de datos SQL de Azure. |

## <a name="partners"></a>Asociados
| &nbsp; | Título | Description |
| ---:|:--- |:--- |
| 70 |[Partners de inteligencia empresarial de Almacenamiento de datos SQL](sql-data-warehouse-partner-business-intelligence.md) |Lista de los asociados de inteligencia empresarial con soluciones compatibles con Almacenamiento de datos SQL. |
| 71 |[Socios de integración de datos de Almacenamiento de datos SQL](sql-data-warehouse-partner-data-integration.md) |Lista de terceros asociados con soluciones de integración de datos compatibles con Almacenamiento de datos SQL de Azure. |
| 72 |[Partners de administración de datos de Almacenamiento de datos SQL](sql-data-warehouse-partner-data-management.md) |Lista de los asociados de administración de datos externos con soluciones compatibles con Almacenamiento de datos SQL. |

## <a name="reference"></a>Referencia
| &nbsp; | Título | Description |
| ---:|:--- |:--- |
| 73 |[Temas de referencia de Almacenamiento de datos SQL](sql-data-warehouse-overview-reference.md) |Vínculos al contenido de referencia de Almacenamiento de datos SQL. |
| 74 |[Cmdlets de PowerShell y API de REST para Almacenamiento de datos SQL](sql-data-warehouse-reference-powershell-cmdlets.md) |Busque los principales cmdlets de PowerShell para Almacenamiento de datos SQL de Azure, incluidos aquellos para pausar y reanudar una base de datos. |
| 75 |[Elementos de lenguaje](sql-data-warehouse-reference-tsql-language-elements.md) |Lista de vínculos a contenido de referencia para los elementos del lenguaje Transact-SQL utilizados para Almacenamiento de datos SQL. |
| 76 |[Temas de Transact-SQL](sql-data-warehouse-reference-tsql-statements.md) |Vínculos a contenido de referencia para los temas de lenguaje Transact-SQL utilizados por Almacenamiento de datos SQL. |
| 77 |[Vistas de sistema](sql-data-warehouse-reference-tsql-system-views.md) |Vínculos al contenido de las vistas de sistema para Almacenamiento de datos de SQL. |

## <a name="security"></a>Seguridad
| &nbsp; | Título | Description |
| ---:|:--- |:--- |
| 78 |[SQL Data Warehouse: compatibilidad con clientes de nivel inferior para enmascaramiento de datos dinámicos y auditoría](sql-data-warehouse-auditing-downlevel-clients.md) |Obtenga información sobre compatibilidad de Almacenamiento de datos SQL con clientes de nivel inferior para auditoría de datos. |
| 79 |[Auditoría en Almacenamiento de datos SQL de Azure](sql-data-warehouse-auditing-overview.md) |Introducción a la auditoría en Almacenamiento de datos SQL de Azure |
| 80 |[Introducción al cifrado de datos transparente (TDE) en Almacenamiento de datos SQL](sql-data-warehouse-encryption-tde.md) |Cifrado de datos transparente (TDE) en SQL Data Warehouse |
| 81 |[Introducción al cifrado de datos transparente (TDE)](sql-data-warehouse-encryption-tde-tsql.md) |Cifrado de datos transparente (TDE) en SQL Data Warehouse (T-SQL) |
| 82 |[Proteger una base de datos en Almacenamiento de datos SQL](sql-data-warehouse-overview-manage-security.md) |Sugerencias para proteger una base de datos en Almacenamiento de datos SQL de Azure para desarrollar soluciones. |

## <a name="miscellaneous"></a>Varios
| &nbsp; | Título | Description |
| ---:|:--- |:--- |
| 83 |[Instalación de Visual Studio y SSDT para SQL Data Warehouse](sql-data-warehouse-install-visual-studio.md) |Instalación de herramientas de desarrollo de Visual Studio y SQL Server Data Tools (SSDT) para Almacenamiento de datos SQL de Azure |
| 84 |[Información de migración al Almacenamiento premium](sql-data-warehouse-migrate-to-premium-storage.md) |Instrucciones para migrar un almacenamiento de datos SQL existente a almacenamiento premium |
| 85 |[Introducción a la detección de amenazas](sql-data-warehouse-security-threat-detection.md) |Cómo empezar a trabajar con la detección de amenazas |
| 86 |[Límites de capacidad de Almacenamiento de datos SQL](sql-data-warehouse-service-capacity-limits.md) |Valores máximos para las conexiones, bases de datos, tablas y consultas de Almacenamiento de datos SQL. |
| 87 |[Solución de problemas de Almacenamiento de datos SQL de Azure](sql-data-warehouse-troubleshoot.md) |Cómo solucionar los problemas de Almacenamiento de datos SQL de Azure. |

