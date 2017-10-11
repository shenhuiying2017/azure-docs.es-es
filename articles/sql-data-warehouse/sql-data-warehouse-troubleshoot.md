---
title: "Solución de problemas de Azure SQL Data Warehouse | Microsoft Docs"
description: "Cómo solucionar los problemas de Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/30/2017
ms.author: kevin;barbkess
ms.openlocfilehash: d269e62b8d49a6c96ce40c2e31c4096e16e07793
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Solución de problemas de Azure SQL Data Warehouse
En este tema se describen algunas de las preguntas de solución de problemas más comunes que oímos de nuestros clientes.

## <a name="connecting"></a>Conexión
| Problema | Resolución |
|:--- |:--- |
| Error de inicio de sesión del usuario 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, error: 18456) |Este error se produce cuando un usuario de AAD intenta conectarse a la base de datos maestra, pero no tiene un usuario ahí.  Para corregir este problema, especifique la instancia de SQL Data Warehouse con la que desea conectar en el momento de la conexión o agregue el usuario a la base de datos maestra.  Consulte el artículo [Información general sobre seguridad][Security overview] para obtener más detalles. |
| La entidad de seguridad del servidor "MyUserName" no puede obtener acceso a la base de datos "maestra" en el contexto de seguridad actual. No se puede abrir la base de datos predeterminada del usuario. Error de inicio de sesión. Error de inicio de sesión del usuario 'MyUserName'. (Microsoft SQL Server, error: 916) |Este error se produce cuando un usuario de AAD intenta conectarse a la base de datos maestra, pero no tiene un usuario ahí.  Para corregir este problema, especifique la instancia de SQL Data Warehouse con la que desea conectar en el momento de la conexión o agregue el usuario a la base de datos maestra.  Consulte el artículo [Información general sobre seguridad][Security overview] para obtener más detalles. |
| Error CTAIP |Este error puede producirse cuando se ha creado un inicio de sesión en la base de datos maestra de SQL Server, pero no en la base de datos de SQL Data Warehouse.  Si se produce este error, eche un vistazo al artículo sobre la [información general de seguridad][Security overview].  En este artículo se explica cómo crear un inicio de sesión y un usuario en una base de datos maestra y luego cómo crear un usuario en la base de datos de SQL Data Warehouse. |
| Bloqueado por el firewall |Las bases de datos SQL de Azure están protegidas por firewalls de nivel de servidor y base de datos para garantizar que solo las direcciones IP conocidas tienen acceso a una base de datos. Los firewalls están protegidos de manera predeterminada, lo que significa que debe habilitar explícitamente una dirección IP o un intervalo de direcciones para poder conectarse.  Para configurar el firewall para el acceso, siga los pasos de la sección de [configuración del acceso de nivel de firewall para el cliente IP][Configure server firewall access for your client IP] en las [instrucciones de aprovisionamiento][Provisioning instructions]. |
| No se puede conectar con una herramienta o un controlador |SQL Data Warehouse recomienda usar [SSMS][SSMS], [SSDT para Visual Studio][SSDT for Visual Studio] o [sqlcmd][sqlcmd] para consultar los datos. Para obtener más detalles sobre los controladores y cómo conectarse a SQL Data Warehouse, consulte los artículos [Controladores de SQL Data Warehouse de Azure][Drivers for Azure SQL Data Warehouse] y [Conexión a SQL Data Warehouse][Connect to Azure SQL Data Warehouse]. |

## <a name="tools"></a>Herramientas
| Problema | Resolución |
|:--- |:--- |
| El Explorador de objetos de Visual Studio no muestra usuarios de AAD |Este es un problema conocido.  Como solución alternativa, vea los usuarios de [sys.database_principals][sys.database_principals].  Consulte [Autenticación a Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse] para obtener más información sobre Azure Active Directory con SQL Data Warehouse. |
|El scripting manual, mediante el Asistente para scripting, o la conexión a través de SSMS es lenta, se ha bloqueado o genera errores| Asegúrese de que se han creado usuarios en la base de datos maestra. En las opciones de scripting, asegúrese también de que la edición del motor se ha establecido como "Microsoft Azure SQL Data Warehouse Edition" y que el tipo de motor es "Microsoft Azure SQL Database".|

## <a name="performance"></a>Rendimiento
| Problema | Resolución |
|:--- |:--- |
| Solución de problemas de rendimiento de consultas |Si está tratando de solucionar los problemas de una consulta determinada, comience por [aprender a supervisar las consultas][Learning how to monitor your queries]. |
| Un bajo rendimiento de las consultas y unos planes mal diseñados suelen ser el resultado de la falta de estadísticas |La causa más común del rendimiento ineficiente es la falta de estadísticas en las tablas.  Para obtener más información sobre cómo crear estadísticas y por qué son tan importantes para el rendimiento, consulte [Mantenimiento de estadísticas de tablas][Statistics]. |
| Baja simultaneidad o consultas en cola |Para comprender el modo de equilibrar la asignación de memoria con la simultaneidad, es importante entender la [administración de la carga de trabajo][Workload management]. |
| Implementación de procedimientos recomendados |El mejor lugar para empezar a aprender formas de mejorar el rendimiento de las consultas es el artículo [Procedimientos recomendados para SQL Data Warehouse][SQL Data Warehouse best practices]. |
| Mejora del rendimiento con el escalado |En ocasiones, la solución para mejorar el rendimiento consiste simplemente en agregar más potencia de proceso a las consultas mediante el [escalado de SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Bajo rendimiento de las consultas como resultado de poca calidad del índice |A veces la velocidad de las consultas se puede reducir debido a la [baja calidad del índice de almacén de columnas][Poor columnstore index quality].  Para obtener más información al respecto y conocer el modo de [volver a generar los índices para mejorar la calidad del segmento][Rebuild indexes to improve segment quality], consulte este artículo. |

## <a name="system-management"></a>Administración del sistema
| Problema | Resolución |
|:--- |:--- |
| Mens. 40847: No se pudo realizar la operación porque el servidor superaría la cuota de la unidad de transacción de la base de datos permitida de 45000. |Reduzca la unidad [DWU][DWU] de la base de datos que intenta crear o[ solicite un aumento de la cuota][request a quota increase]. |
| Investigación del uso del espacio |Consulte los [tamaños de tabla][Table sizes] para comprender el uso del espacio del sistema. |
| Ayuda con la administración de tablas |Para obtener ayuda con la administración de las tablas, consulte la [información general sobre las tablas][Overview].  Este artículo también incluye vínculos a temas más detallados como [tipos de datos de tabla][Data types], [distribución de una tabla][Distribute], [indexación de una tabla][Index], [creación de particiones de una tabla][Partition], [mantenimiento de estadísticas de tabla][Statistics] y [tablas temporales][Temporary]. |
|La barra de progreso de Cifrado de datos transparente (TDE) no se actualiza en Azure Portal|El estado de TDE se puede ver a través de [powershell](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption).|

## <a name="polybase"></a>PolyBase
| Problema | Resolución |
|:--- |:--- |
| Se produce un error en la carga porque hay filas de gran tamaño |Actualmente la compatibilidad con filas de gran tamaño no está disponible en Polybase.  Esto significa que si su tabla contiene VARCHAR(MAX), NVARCHAR(MAX) o VARBINARY(MAX), no se pueden utilizar tablas externas para cargar los datos.  Las cargas de filas de gran tamaño solo se admiten mediante Azure Data Factory (con BCP), Azure Stream Analytics, SSIS, BCP o la clase SQLBulkCopy de .NET. La compatibilidad con filas de gran tamaño en PolyBase se agregará en una versión futura. |
| La carga de la tabla bcp con el tipo de datos MAX está dando error |Existe un problema conocido que requiere la colocación de VARCHAR(MAX), NVARCHAR(MAX) o VARBINARY(MAX) al final de la tabla en algunos escenarios.  Intente mover las columnas MAX al final de la tabla. |

## <a name="differences-from-sql-database"></a>Diferencias con respecto a SQL Database
| Problema | Resolución |
|:--- |:--- |
| Características de SQL Database no admitidas |Consulte [Características no compatibles de las tablas][Unsupported table features]. |
| Tipos de datos de SQL Database no admitidos |Consulte [Tipos de datos no admitidos][Unsupported data types]. |
| Limitaciones de DELETE y UPDATE |Consulte las [soluciones alternativas para UPDATE][UPDATE workarounds], las [soluciones alternativas para DELETE][DELETE workarounds] y el [uso de CTAS para resolver la sintaxis de UPDATE y DELETE no admitida][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| No se admite la instrucción MERGE |Consulte las [soluciones alternativas para MERGE][MERGE workarounds]. |
| Limitaciones de procedimientos almacenados |Consulte [Limitaciones de procedimientos almacenados][Stored procedure limitations] para conocer algunas de las limitaciones de los procedimientos almacenados. |
| Los UDF no admiten instrucciones SELECT |Se trata de una limitación actual de nuestros UDF.  Consulte [CREATE FUNCTION][CREATE FUNCTION] para comprobar la sintaxis que se admite. |

## <a name="next-steps"></a>Pasos siguientes
Si no ha podido encontrar una solución a su problema con estos pasos, estos son otros recursos que puede probar.

* [Blogs]
* [Solicitud de función]
* [Vídeos]
* [Blogs del equipo de CAT]
* [Creación de una incidencia de soporte técnico]
* [Foro de MSDN]
* [Foro Stack Overflow]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT for Visual Studio]: ./sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: ./sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Creación de una incidencia de soporte técnico]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md
[request a quota increase]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Learning how to monitor your queries]: ./sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: ./sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: ./sql-data-warehouse-get-started-provision.md#create-a-server-level-firewall-rule-in-the-azure-portal
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[Table sizes]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: ./sql-data-warehouse-develop-concurrency.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[UPDATE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md
[Working around the PolyBase UTF-8 requirement]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs del equipo de CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitud de función]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Foro de MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Foro Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
