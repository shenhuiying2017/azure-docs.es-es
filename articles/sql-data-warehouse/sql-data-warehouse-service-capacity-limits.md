---
title: "Límites de capacidad de SQL Data Warehouse | Microsoft Docs"
description: "Valores máximos para las conexiones, bases de datos, tablas y consultas de Almacenamiento de datos SQL."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e1eac122-baee-4200-a2ed-f38bfa0f67ce
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess;jrj
translationtype: Human Translation
ms.sourcegitcommit: 3a9ea64c464a74c70e75634a3e5c1e49862a74e7
ms.openlocfilehash: c6b44392c0b3a241d41ae55bd6bb3f544d867e9e
ms.lasthandoff: 02/22/2017


---
# <a name="sql-data-warehouse-capacity-limits"></a>Límites de capacidad de Almacenamiento de datos SQL
Las siguientes tablas contienen los valores máximos permitidos para los distintos componentes de Almacenamiento de datos SQL de Azure.

## <a name="workload-management"></a>Administración de cargas de trabajo
| Categoría | Descripción | Máxima |
|:--- |:--- |:--- |
| [Unidades de almacenamiento de datos (DWU)][Data Warehouse Units (DWU)] |Máximo de DWU para una sola instancia de SQL Data Warehouse |6000 |
| [Unidades de almacenamiento de datos (DWU)][Data Warehouse Units (DWU)] |Máximo de DWU para un único servidor SQL |6000 de forma predeterminada<br/><br/> De forma predeterminada, cada servidor SQL (por ejemplo, myserver.database.windows.net) tiene una cuota de DTU de 45 000, lo que permite un máximo de 6000 DWU. Esta cuota es simplemente un límite de seguridad. Puede aumentar la cuota; para ello, [cree una incidencia de soporte técnico][creating a support ticket] y seleccione *Cuota* como el tipo de solicitud.  Para calcular las necesidades de DTU, multiplique 7,5 por el total de DWU necesario. Puede ver el consumo de DTU actual en la hoja del servidor SQL en el portal. El recuento de las bases de datos, tanto en pausa como continuo, hacia la cuota de DTU. |
| Conexión de base de datos |Sesiones abiertas simultáneas |1024<br/><br/>Se admite un máximo de 1024 conexiones activas, cada una de las cuales puede enviar solicitudes a una base de datos de Almacenamiento de datos SQL de forma simultánea. Tenga en cuenta que hay límites en el número de consultas que se pueden ejecutar a la vez. Cuando se supera el límite de simultaneidad, la solicitud entra en una cola interna donde espera para su proceso. |
| Conexión de base de datos |Memoria máxima para instrucciones preparadas |20 MB |
| [Administración de cargas de trabajo][Workload management] |N.º máximo de consultas simultáneas |32<br/><br/> De forma predeterminada, SQL Data Warehouse puede ejecutar un máximo de 32 consultas simultáneas y pone en cola las restantes.<br/><br/>El nivel de simultaneidad se puede reducir cuando se asigna a los usuarios a una clase de recurso superior o cuando el Almacenamiento de datos SQL está configurado con una baja unidad de almacenamiento de datos. Algunas consultas, como las consultas DMV, siempre se pueden ejecutar. |
| [Tempdb][Tempdb] |Tamaño máximo de Tempdb |399 GB por DW100. Por lo tanto, en DWU1000 el tamaño de Tempdb es de 3,99 TB. |

## <a name="database-objects"></a>Objetos de base de datos
| Categoría | Descripción | Máxima |
|:--- |:--- |:--- |
| Base de datos |Tamaño máximo |240 TB comprimidos en un disco<br/><br/>Este espacio es independiente del espacio de tempdb o de registro y, por tanto, está dedicado a tablas permanentes.  La compresión del almacén de columnas en clúster se estima en 5X.  Esta compresión permite que la base de datos crezca a aproximadamente 1 PB cuando todas las tablas tienen el almacén de columnas en clúster (el tipo de tabla predeterminada). |
| Tabla |Tamaño máximo |60 TB comprimidos en disco |
| Tabla |Tablas por base de datos |2 mil millones |
| Tabla |Columnas por tabla |1024 columnas |
| Tabla |Bytes por columna |Depende de la columna de [tipo de datos][data type].  El límite es 8000 para los tipos de datos char, 4000 para nvarchar o 2 GB para los tipos de datos MAX. |
| Tabla |Bytes por fila, tamaño definido |8060 bytes<br/><br/>El número de bytes por fila se calcula de la misma forma que para SQL Server con la compresión de página. Al igual que SQL Server, Almacenamiento de datos SQL admite el almacenamiento con desbordamiento de fila, lo que permite insertar **columnas de longitud variable** de forma no consecutiva. Cuando se insertan filas de longitud variable, solo se almacena la raíz de 24 bytes en el registro principal. Para más información, vea el artículo de MSDN [Datos de desbordamiento de fila superiores a 8 KB][Row-Overflow Data Exceeding 8 KB]. |
| Tabla |Particiones por tabla |15 000<br/><br/>Para obtener un alto rendimiento, se recomienda reducir al mínimo el número de particiones que necesita, pero sin perder de vista sus requisitos empresariales. A medida que crece el número de particiones, la sobrecarga de operaciones de lenguaje de definición de datos (DDL) y lenguaje de manipulación de datos (DML) crece y da lugar a un rendimiento más lento. |
| Tabla |Caracteres por valor de límite de partición |4000 |
| Índice |Índices no agrupados por tabla |999<br/><br/>Solo se aplica a tablas de almacén de filas. |
| Índice |Índices agrupados por tabla |1<br><br/>Se aplica a tablas de almacén de filas y de almacén de columnas. |
| Índice |Tamaño de clave de índice |900 bytes<br/><br/>Solo se aplica a los índices de almacén de filas.<br/><br/>Si los datos existentes en las columnas no superan los 900 bytes cuando se crea el índice, pueden crearse índices en columnas varchar con un tamaño máximo de más de 900 bytes. Sin embargo, las posteriores acciones INSERT o UPDATE en las columnas que hacen que el número total supere los 900 bytes darán error. |
| Índice |Columnas de clave por índice |16<br/><br/>Solo se aplica a los índices de almacén de filas. Los índices de almacén de columnas agrupados incluyen todas las columnas. |
| Estadísticas |Tamaño de los valores de columna combinados |900 bytes |
| Estadísticas |Columnas por objeto de estadísticas |32 |
| Estadísticas |Estadísticas creadas en columnas por tabla |30&000; |
| Procedimientos almacenados |Niveles máximos de anidamiento |8 |
| Ver |Columnas por vista |1024 |

## <a name="loads"></a>Cargas
| Categoría | Descripción | Máxima |
|:--- |:--- |:--- |
| Cargas de PolyBase |MB por fila |1<br/><br/>Las cargas de PolyBase se limitan a la carga de filas más pequeñas que 1 MB y no pueden cargar en VARCHR(MAX), NVARCHAR(MAX) ni VARBINARY(MAX).<br/><br/> |

## <a name="queries"></a>Consultas
| Categoría | Descripción | Máxima |
|:--- |:--- |:--- |
| Consultar |Consultas en cola en tablas de usuario |1000 |
| Consultar |Consultas simultáneas en vistas de sistema |100 |
| Consultar |Consultas en cola en vistas de sistema |1000 |
| Consultar |Parámetros máximos |2098 |
| Lote |Tamaño máximo |65&536;*4096 |
| Resultados de SELECT |Columnas por fila |4096<br/><br/>Nunca se pueden tener más de 4096 columnas por fila en el resultado de SELECT. No hay ninguna garantía de que pueda tener siempre 4096. Si el plan de consulta requiere una tabla temporal, se podría aplicar el máximo de 1024 columnas por tabla. |
| SELECT |Subconsultas anidadas |32<br/><br/>Nunca se pueden tener más de 32 subconsultas anidadas en una instrucción SELECT. No hay ninguna garantía de que siempre pueda tener 32. Por ejemplo, una instrucción JOIN puede introducir una subconsulta en el plan de consulta. El número de subconsultas también puede estar limitado por la memoria disponible. |
| SELECT |Columnas por JOIN |1024 columnas<br/><br/>Nunca se pueden tener más de 1024 columnas en la instrucción JOIN. No hay ninguna garantía de que siempre pueda tener 1024. Si el plan JOIN requiere una tabla temporal con más columnas que el resultado de JOIN, se aplica el límite de 1024 a la tabla temporal. |
| SELECT |Bytes por columnas GROUP BY |8060<br/><br/>Las columnas de la cláusula GROUP BY pueden tener como máximo 8060 bytes. |
| SELECT |Bytes por columnas ORDER BY |8060 bytes<br/><br/>Las columnas de la cláusula ORDER BY pueden tener como máximo 8060 bytes. |
| Identificadores y constantes por instrucción |Número de identificadores y constantes de referencia. |65 535<br/><br/>Almacenamiento de datos SQL limita el número de identificadores y constantes que pueden incluirse en una única expresión de una consulta. Este límite es 65 535 GB. Si se supera este número se produce el error de SQL Server 8632. Para más información, vea [Internal error: An expression services limit has been reached][Internal error: An expression services limit has been reached] (Error interno: se ha alcanzado el límite de servicios de una expresión). |

## <a name="metadata"></a>Metadatos
| Vista de sistema | Número máximo de filas |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10.000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Número total de trabajadores de DMS para las 1000 solicitudes de SQL más recientes. |
| sys.dm_pdw_errors |10.000 |
| sys.dm_pdw_exec_requests |10.000 |
| sys.dm_pdw_exec_sessions |10.000 |
| sys.dm_pdw_request_steps |Número total de pasos para las 1000 solicitudes SQL más recientes que se almacenan en sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10.000 |
| sys.dm_pdw_sql_requests |Las 1000 solicitudes SQL más recientes que se almacenan en sys.dm_pdw_exec_requests. |

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información de referencia, vea [Información general de referencia de Almacenamiento de datos SQL][SQL Data Warehouse reference overview].

<!--Image references-->

<!--Article references-->
[Data Warehouse Units (DWU)]: ./sql-data-warehouse-overview-what-is.md
[SQL Data Warehouse reference overview]: ./sql-data-warehouse-overview-reference.md
[Workload management]: ./sql-data-warehouse-develop-concurrency.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[data type]: ./sql-data-warehouse-tables-data-types.md
[creating a support ticket]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Row-Overflow Data Exceeding 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[Internal error: An expression services limit has been reached]: https://support.microsoft.com/kb/913050

