---
title: Todos los temas de SQL Data Warehouse | Microsoft Docs
description: "Tabla de todos los temas del servicio SQL Data Warehouse que se encuentra en http://azure.microsoft.com/documentation/articles/, con títulos y descripciones."
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
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Todos los temas del servicio Azure SQL Data Warehouse
En este documento se muestran todos los temas relacionados directamente con el servicio **SQL Data Warehouse** de Azure. Puede buscar en esta página web las palabras clave mediante **Ctrl+F**, para encontrar los temas de interés.

## <a name="new"></a>Nuevo
| &nbsp; | Título | DESCRIPCIÓN |
| ---:|:--- |:--- |
| 1 |[Copias de seguridad de SQL Data Warehouse](sql-data-warehouse-backups.md) |Aprenda sobre las copias de seguridad de base de datos integradas de SQL Data Warehouse que permiten restaurar una instancia de Azure SQL Data Warehouse a un punto de restauración o una región geográfica distinta. |

## <a name="updated-articles-sql-data-warehouse"></a>Artículos actualizados, SQL Data Warehouse
En esta sección se enumeran los artículos que se han actualizado recientemente. Concretamente, aquellos en los que la actualización fuera grande o considerable. Para cada artículo actualizado, se muestra un fragmento del texto agregado. Los artículos se actualizaron dentro de este intervalo de fechas: del **22-08-2016** al **05-10-2016**.

| &nbsp; | Artículo | Texto actualizado, fragmento | Se actualiza cuando |
| ---:|:--- |:--- |:--- |
| 2 |[Load data from Azure blob storage into SQL Data Warehouse (PolyBase) [Carga de datos de Almacenamiento de blobs de Azure en Almacenamiento de datos SQL (PolyBase)]](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |/- To track bytes and files SELECT  r.command,  s.request_id,  r.status,  count(distinct input_name) as nbr_files,  sum(s.bytes_processed)/1024/1024 as gb_processed FROM  sys.dm_pdw_exec_requests r  inner join sys.dm_pdw_dms_external_work s  on r.request_id = s.request_id WHERE  r. label  = 'CTAS : Load  cso . DimProduct  '  OR r. label  = 'CTAS : Load  cso . FactOnlineSales  ' GROUP BY  r.command,  s.request_id,  r.status ORDER BY  nbr_files desc,  gb_processed desc; |2016-09-07 |
| 3 |[SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md) |** ¿Puedo restaurar un almacenamiento de datos en pausa? ** Para restaurar un almacenamiento de datos que esté en pausa, primero debe ponerlo en línea. Después de que el almacén de datos vuelve a estar en línea, dispone de siete días de puntos de restauración para elegir. ** Restauración a una región con redundancia geográfica ** Si usa el almacenamiento con redundancia geográfica, puede restaurar el almacenamiento de datos a su centro de datos emparejado de otra región geográfica. El almacenamiento de datos se restaura desde la última copia de seguridad diaria. ** Restaurar la escala de tiempo ** Puede restaurar una base de datos a cualquier punto de restauración de los últimos siete días. Las instantáneas comienzan cada cuatro a ocho horas y están disponibles durante siete días. Cuando una instantánea tiene una antigüedad superior a siete días, caduca y su punto de restauración ya no está disponible. ** Costos de restauración ** Los cargos de almacenamiento por el almacenamiento de datos restaurado se facturan con la tarifa de Azure Premium Storage. Si se pausa un almacenamiento de datos restaurado, se le cobrará por el almacenamiento según la tarifa de Azure Premium Storage. La ventaja de pausar es que no se le cobra |2016-09-29 |

## <a name="get-started"></a>Introducción
| &nbsp; | Título | DESCRIPCIÓN |
| ---:|:--- |:--- |
| 4 |[Autenticación a Azure SQL Data Warehouse](sql-data-warehouse-authentication.md) |Autenticación de Azure Active Directory (AAD) y SQL Server a Azure SQL Data Warehouse |
| 5 |[Procedimientos recomendados para Azure SQL Data Warehouse](sql-data-warehouse-best-practices.md) |Recomendaciones y procedimientos recomendados que debe saber para desarrollar soluciones de Azure SQL Data Warehouse. Esto le ayudará a tener éxito. |
| 6 |[Controladores de Azure SQL Data Warehouse](sql-data-warehouse-connection-strings.md) |Cadenas de conexión y controladores de SQL Data Warehouse |
| 7 |[Conexión a Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md) |Cómo encontrar el nombre de servidor y la cadena de conexión de su instancia de Azure SQL Data Warehouse |
| 8 |[Análisis de datos con Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) |Use Azure Machine Learning para crear un modelo de aprendizaje automático predictivo con los datos almacenados en Azure SQL Data Warehouse. |
| 9 |[Consulta de Azure SQL Data Warehouse (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) |Consulta de Azure SQL Data Warehouse con las utilidades de la línea de comandos sqlcmd. |
| 10 |[Creación de una base de datos de SQL Data Warehouse mediante Transact-SQL (TSQL)](sql-data-warehouse-get-started-create-database-tsql.md) |Aprenda a crear una base de datos de Azure SQL Data Warehouse con TSQL |
| 11 |[Creación de una incidencia de soporte técnico para SQL Data Warehouse](sql-data-warehouse-get-started-create-support-ticket.md) |Creación de una incidencia de soporte técnico en Azure SQL Data Warehouse. |
| 12 |[Carga de datos con Azure Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md) |Más información sobre Azure Data Factory |
| 13 |[Carga de datos con PolyBase en SQL Data Warehouse](sql-data-warehouse-get-started-load-with-polybase.md) |Obtenga información sobre qué es PolyBase y cómo usarlo en escenarios de almacenamiento de datos. |
| 14 |[Creación de una instancia de Azure SQL Data Warehouse](sql-data-warehouse-get-started-provision.md) |Aprenda a crear una instancia de Azure SQL Data Warehouse en Azure Portal |
| 15 |[Creación de SQL Data Warehouse con Powershell](sql-data-warehouse-get-started-provision-powershell.md) |Creación de SQL Data Warehouse con Powershell |
| 16 |[Visualización de datos con Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) |Visualización de datos de SQL Data Warehouse con Power BI |
| 17 |[Consultas en Azure SQL Data Warehouse (Visual Studio)](sql-data-warehouse-query-visual-studio.md) |Consultas en SQL Data Warehouse con Visual Studio. |

## <a name="develop"></a>Desarrollo
| &nbsp; | Título | DESCRIPCIÓN |
| ---:|:--- |:--- |
| 18 |[Optimización de transacciones para SQL Data Warehouse](sql-data-warehouse-develop-best-practices-transactions.md) |Guía de procedimientos recomendados sobre la escritura de actualizaciones de transacciones eficientes en Azure SQL Data Warehouse |
| 19 |[Simultaneidad y administración de cargas de trabajo en SQL Data Warehouse](sql-data-warehouse-develop-concurrency.md) |Obtenga información sobre la simultaneidad y la administración de cargas de trabajo en Azure SQL Data Warehouse para el desarrollo de soluciones. |
| 20  |[Create Table As Select (CTAS) en SQL Data Warehouse](sql-data-warehouse-develop-ctas.md) |Sugerencias para la codificación con la instrucción Create table as select (CTAS) en Azure SQL Data Warehouse para el desarrollo de soluciones. |
| 21 |[SQL dinámico en SQL Data Warehouse](sql-data-warehouse-develop-dynamic-sql.md) |Sugerencias para usar SQL dinámico en Azure SQL Data Warehouse para desarrollar soluciones. |
| 22 |[Opciones de Group by en SQL Data Warehouse](sql-data-warehouse-develop-group-by-options.md) |Sugerencias para implementar opciones de Group by en Azure SQL Data Warehouse para el desarrollo de soluciones. |
| 23 |[Uso de etiquetas para instrumentar consultas en SQL Data Warehouse](sql-data-warehouse-develop-label.md) |Sugerencias para usar etiquetas para instrumentar las consultas en Azure SQL Data Warehouse para el desarrollo de soluciones. |
| 24 |[Bucles en SQL Data Warehouse](sql-data-warehouse-develop-loops.md) |Sugerencias para los bucles de Transact-SQL en Azure SQL Data Warehouse para el desarrollo de soluciones. |
| 25 |[Procedimientos almacenados en SQL Data Warehouse](sql-data-warehouse-develop-stored-procedures.md) |Sugerencias para implementar procedimientos almacenados en Azure SQL Data Warehouse para el desarrollo de soluciones. |
| 26 |[Transacciones en SQL Data Warehouse](sql-data-warehouse-develop-transactions.md) |Sugerencias para implementar transacciones en Azure SQL Data Warehouse para el desarrollo de soluciones. |
| 27 |[Esquemas definidos por el usuario en SQL Data Warehouse](sql-data-warehouse-develop-user-defined-schemas.md) |Sugerencias para usar los esquemas Transact-SQL en Azure SQL Data Warehouse para desarrollar soluciones. |
| 28 |[Asignación de variables en SQL Data Warehouse](sql-data-warehouse-develop-variable-assignment.md) |Sugerencias para la asignación de variables de Transact-SQL en Azure SQL Data Warehouse para desarrollar soluciones. |
| 29 |[Vistas en SQL Data Warehouse](sql-data-warehouse-develop-views.md) |Sugerencias para usar las vistas Transact-SQL en Azure SQL Data Warehouse para desarrollar soluciones. |
| 30 |[Decisiones de diseño y técnicas de codificación para SQL Data Warehouse](sql-data-warehouse-overview-develop.md) |Conceptos de desarrollo, decisiones de diseño, recomendaciones y técnicas de codificación para SQL Data Warehouse. |

## <a name="manage"></a>administración
| &nbsp; | Título | DESCRIPCIÓN |
| ---:|:--- |:--- |
| 31 |[Administración de la potencia de proceso en Azure SQL Data Warehouse (información general)](sql-data-warehouse-manage-compute-overview.md) |Funcionalidades de escalado horizontal del rendimiento en Azure SQL Data Warehouse. Realice el escalado horizontal ajustando las DWU o pause y reanude los recursos de proceso para ahorrar costos. |
| 32 |[Administración de la potencia de proceso en Azure SQL Data Warehouse (Azure Portal)](sql-data-warehouse-manage-compute-portal.md) |Tareas del Portal de Azure para administrar la potencia de proceso. Escalado de los recursos de proceso ajustando DWU. Pausar y reanudar recursos de proceso para ahorrar costos. |
| 33 |[Administración de la potencia de proceso en Azure SQL Data Warehouse (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) |Tareas de PowerShell para administrar la potencia de proceso. Escalado de los recursos de proceso ajustando DWU. Pausar y reanudar recursos de proceso para ahorrar costos. |
| 34 |[Administración de la potencia de proceso en Azure SQL Data Warehouse (REST)](sql-data-warehouse-manage-compute-rest-api.md) |Tareas de PowerShell para administrar la potencia de proceso. Escalado de los recursos de proceso ajustando DWU. Pausar y reanudar recursos de proceso para ahorrar costos. |
| 35 |[Administración de la potencia de proceso en Azure SQL Data Warehouse (T-SQL)](sql-data-warehouse-manage-compute-tsql.md) |Tareas de Transact-SQL (T-SQL) para el escalado horizontal del rendimiento ajustando DWU. Ahorre costes reduciendo el escalado fuera de horas punta. |
| 36 |[Supervisión de la carga de trabajo mediante DMV](sql-data-warehouse-manage-monitor.md) |Obtenga información sobre cómo supervisar la carga de trabajo mediante DMV. |
| 37 |[Administración de base datos en Azure SQL Data Warehouse](sql-data-warehouse-overview-manage.md) |Información general de administración de bases de datos de SQL Data Warehouse Incluye herramientas de administración, DWU y rendimiento de escalado horizontal, solución de problemas de rendimiento de las consultas, el establecimiento de directivas de seguridad y la restauración una base de datos de daños en los datos o de un apagón regional. |
| 38 |[Supervisión de las consultas de usuario en Azure SQL Data Warehouse](sql-data-warehouse-overview-manage-user-queries.md) |Información general de las consideraciones, prácticas recomendadas y tareas para supervisar las consultas de usuario en Azure SQL Data Warehouse. |
| 39 |[SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md) |Información general de las opciones de restauración de bases de datos para recuperar una base de datos en Azure SQL Data Warehouse. |
| 40 |[Restauración de instancias de Azure SQL Data Warehouse (Portal)](sql-data-warehouse-restore-database-portal.md) |Tareas de Azure Portal para restaurar una instancia de Azure SQL Data Warehouse. |
| 41 |[Restauración de instancias de SQL Data Warehouse (PowerShell)](sql-data-warehouse-restore-database-powershell.md) |Tareas de PowerShell para restaurar una instancia de Azure SQL Data Warehouse. |
| 42 |[Restauración de instancias de Azure SQL Data Warehouse (API de REST)](sql-data-warehouse-restore-database-rest-api.md) |Tareas de la API de REST para restaurar una instancia de Azure SQL Data Warehouse. |

## <a name="tables-and-indexes"></a>Tablas e índices
| &nbsp; | Título | DESCRIPCIÓN |
| ---:|:--- |:--- |
| 43 |[Tipos de datos de las tablas en SQL Data Warehouse](sql-data-warehouse-tables-data-types.md) |Introducción a los tipos de datos de Azure SQL Data Warehouse. |
| 44 |[Distribución de tablas en SQL Data Warehouse](sql-data-warehouse-tables-distribute.md) |Introducción a la distribución de tablas en SQL Data Warehouse. |
| 45 |[Indexación de tablas en SQL Data Warehouse](sql-data-warehouse-tables-index.md) |Introducción a la indexación de tablas en Azure SQL Data Warehouse. |
| 46 |[Información general de tablas en SQL Data Warehouse](sql-data-warehouse-tables-overview.md) |Introducción a las tablas de Azure SQL Data Warehouse. |
| 47 |[Creación de particiones de tablas en SQL Data Warehouse](sql-data-warehouse-tables-partition.md) |Introducción a la creación de particiones en tablas en Azure SQL Data Warehouse. |
| 48 |[Administración de estadísticas en tablas en SQL Data Warehouse](sql-data-warehouse-tables-statistics.md) |Introducción a las estadísticas de tablas en Azure SQL Data Warehouse. |
| 49 |[Tablas temporales en SQL Data Warehouse](sql-data-warehouse-tables-temporary.md) |Introducción a las tablas temporales en Azure SQL Data Warehouse. |

## <a name="integrate"></a>Integrate
| &nbsp; | Título | DESCRIPCIÓN |
| ---:|:--- |:--- |
| 50 |[Uso de Azure Data Factory con SQL Data Warehouse](sql-data-warehouse-integrate-azure-data-factory.md) |Sugerencias para usar Azure Data Factory (ADF) con SQL Data Warehouse para el desarrollo de soluciones. |
| 51 |[Uso de Azure Machine Learning con Almacenamiento de datos SQL](sql-data-warehouse-integrate-azure-machine-learning.md) |Tutorial para usar Azure Machine Learning con Almacenamiento de datos SQL de Azure para el desarrollo de soluciones. |
| 52 |[Uso de Azure Stream Analytics con SQL Data Warehouse](sql-data-warehouse-integrate-azure-stream-analytics.md) |Sugerencias para usar Azure Stream Analytics con Azure SQL Data Warehouse para el desarrollo de soluciones. |
| 53 |[Uso de Power BI con SQL Data Warehouse](sql-data-warehouse-integrate-power-bi.md) |Sugerencias para usar Power BI con Azure SQL Data Warehouse para el desarrollo de soluciones. |
| 54 |[Aprovechamiento de otros servicios con SQL Data Warehouse](sql-data-warehouse-overview-integrate.md) |Herramientas y asociados con soluciones que se integran con SQL Data Warehouse. |

## <a name="load"></a>Carga
| &nbsp; | Título | DESCRIPCIÓN |
| ---:|:--- |:--- |
| 55 |[Carga de datos de Azure Blob Storage en Azure SQL Data Warehouse (Azure Data Factory)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) |Más información sobre Azure Data Factory |
| 56 |[Load data from Azure blob storage into SQL Data Warehouse (PolyBase) [Carga de datos de Almacenamiento de blobs de Azure en Almacenamiento de datos SQL (PolyBase)]](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |Aprenda a usar PolyBase para cargar datos de Azure Blob Storage en SQL Data Warehouse. Cargue algunas tablas de datos públicos en el esquema Contoso Retail Data Warehouse. |
| 57 |[Carga de datos de SQL Server en Azure SQL Data Warehouse (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) |Usa bcp para exportar datos de SQL Server a archivos planos, AZCopy para importar datos a Azure Blob Storage y PolyBase para introducir los datos en Azure SQL Data Warehouse. |
| 58 |[Carga de datos de SQL Server en Azure SQL Data Warehouse (archivos planos)](sql-data-warehouse-load-from-sql-server-with-bcp.md) |Para datos de tamaño pequeño, utiliza bcp para exportar datos de SQL Server a archivos planos e importar los datos directamente en Azure SQL Data Warehouse. |
| 59 |[Carga de datos de SQL Server en Azure SQL Data Warehouse (SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) |Se muestra cómo crear un paquete de SQL Server Integration Services (SSIS) para mover datos desde una gran variedad de orígenes de datos a SQL Data Warehouse. |
| 60 |[Carga de datos con PolyBase en SQL Data Warehouse](sql-data-warehouse-load-from-sql-server-with-polybase.md) |Usa bcp para exportar datos de SQL Server a archivos planos, AZCopy para importar datos a Azure Blob Storage y PolyBase para introducir los datos en Azure SQL Data Warehouse. |
| 61 |[Guía para el uso de PolyBase en SQL Data Warehouse](sql-data-warehouse-load-polybase-guide.md) |Directrices y recomendaciones para el uso de PolyBase en escenarios de SQL Data Warehouse. |
| 62 |[Carga de datos de ejemplo en SQL Data Warehouse](sql-data-warehouse-load-sample-databases.md) |Carga de datos de ejemplo en SQL Data Warehouse |
| 63 |[Carga de datos con bcp](sql-data-warehouse-load-with-bcp.md) |Obtenga información sobre qué es bcp y cómo usarlo en escenarios de almacenamiento de datos. |
| 64 |[Carga de datos en Azure SQL Data Warehouse](sql-data-warehouse-overview-load.md) |Conozca los escenarios comunes para la carga de datos en SQL Data Warehouse. Estos incluyen el uso de PolyBase, el almacenamiento de blobs de Azure, archivos planos y el envío de discos. También puede usar herramientas de otros fabricantes. |

## <a name="migrate"></a>Migrar
| &nbsp; | Título | DESCRIPCIÓN |
| ---:|:--- |:--- |
| 65 |[Migración del código SQL a SQL Data Warehouse](sql-data-warehouse-migrate-code.md) |Sugerencias para migrar el código SQL a Azure SQL Data Warehouse a fin de desarrollar soluciones. |
| 66 |[Migración de los datos](sql-data-warehouse-migrate-data.md) |Sugerencias para migrar los datos a Azure SQL Data Warehouse a fin de desarrollar soluciones. |
| 67 |[Utilidad de migración de Almacenamiento de datos (vista previa)](sql-data-warehouse-migrate-migration-utility.md) |Migración a SQL Data Warehouse |
| 68 |[Migración del esquema a SQL Data Warehouse](sql-data-warehouse-migrate-schema.md) |Sugerencias para migrar el esquema a Azure SQL Data Warehouse a fin de desarrollar soluciones. |
| 69 |[Migración de una solución a SQL Data Warehouse](sql-data-warehouse-overview-migrate.md) |Guía de migración para llevar una solución a la plataforma Azure SQL Data Warehouse. |

## <a name="partners"></a>Asociados
| &nbsp; | Título | DESCRIPCIÓN |
| ---:|:--- |:--- |
| 70 |[Partners de inteligencia empresarial de SQL Data Warehouse](sql-data-warehouse-partner-business-intelligence.md) |Lista de los asociados de inteligencia empresarial con soluciones compatibles con SQL Data Warehouse. |
| 71 |[Socios de integración de datos de SQL Data Warehouse](sql-data-warehouse-partner-data-integration.md) |Lista de terceros asociados con soluciones de integración de datos compatibles con Azure SQL Data Warehouse. |
| 72 |[Partners de administración de datos de SQL Data Warehouse](sql-data-warehouse-partner-data-management.md) |Lista de los asociados de administración de datos externos con soluciones compatibles con SQL Data Warehouse. |

## <a name="reference"></a>Referencia
| &nbsp; | Título | DESCRIPCIÓN |
| ---:|:--- |:--- |
| 73 |[Temas de referencia de SQL Data Warehouse](sql-data-warehouse-overview-reference.md) |Vínculos al contenido de referencia de SQL Data Warehouse. |
| 74 |[Cmdlets de PowerShell y API de REST para SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md) |Busque los principales cmdlets de PowerShell para Azure SQL Data Warehouse, incluidos aquellos para pausar y reanudar una base de datos. |
| 75 |[Elementos de lenguaje](sql-data-warehouse-reference-tsql-language-elements.md) |Lista de vínculos a contenido de referencia para los elementos del lenguaje Transact-SQL utilizados para SQL Data Warehouse. |
| 76 |[Temas de Transact-SQL](sql-data-warehouse-reference-tsql-statements.md) |Vínculos a contenido de referencia para los temas de lenguaje Transact-SQL utilizados por SQL Data Warehouse. |
| 77 |[Vistas de sistema](sql-data-warehouse-reference-tsql-system-views.md) |Vínculos al contenido de las vistas de sistema para SQL Data Warehouse. |

## <a name="security"></a>Seguridad
| &nbsp; | Título | DESCRIPCIÓN |
| ---:|:--- |:--- |
| 78 |[SQL Data Warehouse: compatibilidad con clientes de nivel inferior para enmascaramiento de datos dinámicos y auditoría](sql-data-warehouse-auditing-downlevel-clients.md) |Obtenga información sobre compatibilidad de SQL Data Warehouse con clientes de nivel inferior para auditoría de datos. |
| 79 |[Auditoría en Azure SQL Data Warehouse](sql-data-warehouse-auditing-overview.md) |Introducción a la auditoría en Azure SQL Data Warehouse |
| 80 |[Introducción al cifrado de datos transparente (TDE) en SQL Data Warehouse](sql-data-warehouse-encryption-tde.md) |Cifrado de datos transparente (TDE) en SQL Data Warehouse |
| 81 |[Introducción al cifrado de datos transparente (TDE)](sql-data-warehouse-encryption-tde-tsql.md) |Cifrado de datos transparente (TDE) en SQL Data Warehouse (T-SQL) |
| 82 |[Proteger una base de datos en SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md) |Sugerencias para proteger una base de datos en Azure SQL Data Warehouse para desarrollar soluciones. |

## <a name="miscellaneous"></a>Varios
| &nbsp; | Título | DESCRIPCIÓN |
| ---:|:--- |:--- |
| 83 |[Instalación de Visual Studio y SSDT para SQL Data Warehouse](sql-data-warehouse-install-visual-studio.md) |Instalación de herramientas de desarrollo de Visual Studio y SQL Server Data Tools (SSDT) para Azure SQL Data Warehouse |
| 84 |[Información de migración al Premium Storage](sql-data-warehouse-migrate-to-premium-storage.md) |Instrucciones para migrar una instancia de SQL Data Warehouse existente a Premium Storage |
| 85 |[Introducción a la detección de amenazas](sql-data-warehouse-security-threat-detection.md) |Cómo empezar a trabajar con la detección de amenazas |
| 86 |[Límites de capacidad de SQL Data Warehouse](sql-data-warehouse-service-capacity-limits.md) |Valores máximos para las conexiones, bases de datos, tablas y consultas de SQL Data Warehouse. |
| 87 |[Solución de problemas de Azure SQL Data Warehouse](sql-data-warehouse-troubleshoot.md) |Cómo solucionar los problemas de Azure SQL Data Warehouse. |

