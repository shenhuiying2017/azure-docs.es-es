<properties
   pageTitle="Solución de problemas de Almacenamiento de datos SQL de Azure | Microsoft Azure"
   description="Cómo solucionar los problemas de Almacenamiento de datos SQL de Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/01/2016"
   ms.author="sonyama;barbkess"/>

# Solución de problemas de Almacenamiento de datos SQL de Azure

En este tema se describen algunas de las preguntas de solución de problemas más comunes que oímos de nuestros clientes.

## Conexión

| Problema | Resolución |
| :----------------------------------| :---------------------------------------------- |
| Error CTAIP | Este error puede producirse cuando se ha creado un inicio de sesión en la base de datos maestra de SQL Server, pero no en la base de datos de Almacenamiento de datos SQL. Si se produce este error, eche un vistazo al artículo sobre la [información general de seguridad][]. En este artículo se explica cómo crear un inicio de sesión en una base de datos maestra y luego cómo crear un usuario en la base de datos de Almacenamiento de datos SQL.|
| Bloqueado por el firewall |Las bases de datos SQL de Azure están protegidas por firewalls de nivel de servidor y base de datos para garantizar que solo las direcciones IP conocidas tienen acceso a una base de datos. Los firewalls están protegidos de manera predeterminada, lo que significa que debe habilitar explícitamente una dirección IP o un intervalo de direcciones para poder conectarse. Para configurar el firewall para el acceso, siga los pasos de la sección de [configuración del acceso de nivel de firewall para el cliente IP][] en las [instrucciones de aprovisionamiento][].|
| No se puede conectar con una herramienta o un controlador | Almacenamiento de datos SQL recomienda el uso de [Visual Studio 2013 o 2015][] para consultar los datos. Para la conectividad de cliente, se recomienda [SQL Server Native Client 10/11 (ODBC)][].|


## Herramientas

| Problema | Resolución |
| :----------------------------------| :---------------------------------------------- |
| El Explorador de objetos de Visual Studio no muestra usuarios de AAD | Este es un problema conocido. Como solución alternativa, vea los usuarios en [sys.database\_principals][]. Consulte [Autenticación a Almacenamiento de datos SQL de Azure][] para más información sobre Azure Active Directory con Almacenamiento de datos SQL.|

## Rendimiento

| Problema | Resolución |
| :----------------------------------| :---------------------------------------------- |
| Solución de problemas de rendimiento de consultas | Si está intentando solucionar los problemas de una consulta determinada, comience por [aprender a supervisar las consultas][].|
| Un bajo rendimiento de las consultas y unos planes mal diseñados suelen ser el resultado de la falta de estadísticas | La causa más común del rendimiento ineficiente es la falta de estadísticas en las tablas. Para más información sobre cómo crear estadísticas y por qué son tan importantes para el rendimiento, consulte [Mantenimiento de estadísticas de tablas][Statistics].|
| Baja simultaneidad o consultas en cola | Para comprender el modo de equilibrar la asignación de memoria con la simultaneidad, es importante entender la [administración de la carga de trabajo][].|
| Implementación de procedimientos recomendados | El mejor lugar para empezar a aprender formas de mejorar el rendimiento de las consultas es el artículo [Procedimientos recomendados para Almacenamiento de datos SQL de Azure][].|
| Mejora del rendimiento con el escalado | En ocasiones, la solución para mejorar el rendimiento consiste simplemente en agregar más potencia de proceso a las consultas mediante el [escalado de Almacenamiento de datos SQL][].|
| Bajo rendimiento de las consultas como resultado de poca calidad del índice | A veces la velocidad de las consultas se puede reducir debido a la [baja calidad del índice de almacén de columnas][]. Para más información al respecto y conocer el modo de [volver a generar los índices para mejorar la calidad del segmento][], consulte este artículo.|

## Administración del sistema

| Problema | Resolución |
| :----------------------------------| :---------------------------------------------- |
| Investigación del uso del espacio | Consulte los [tamaños de tabla][] para comprender el uso del espacio del sistema.|
| Ayuda con la administración de tablas | Para obtener ayuda con la administración de las tablas, consulte la [información general sobre las tablas][Overview]. Este artículo también incluye vínculos a temas más detallados como [tipos de datos de tabla][Data types], [distribución de una tabla][Distribute], [indexación de una tabla][Index], [creación de particiones de una tabla][Partition], [mantenimiento de estadísticas de tabla][Statistics] y [tablas temporales][Temporary].|

## PolyBase

| Problema | Resolución |
| :----------------------------------| :---------------------------------------------- |
| Error de UTF-8 | Actualmente, PolyBase solo admite la carga de archivos de datos que se han codificado con UTF-8. Consulte [Evitar el requisito UTF-8 de PolyBase][] para obtener instrucciones sobre cómo superar esta limitación.|
| Se produce un error en la carga porque hay filas de gran tamaño | Actualmente la compatibilidad con filas de gran tamaño no está disponible en Polybase. Esto significa que si su tabla contiene VARCHAR(MAX), NVARCHAR(MAX) o VARBINARY(MAX), no se pueden utilizar tablas externas para cargar los datos. Las cargas de filas de gran tamaño solo se admiten mediante Data Factory de Azure (con BCP), Análisis de transmisiones de Azure, SSIS, BCP o la clase SQLBulkCopy de .NET. La compatibilidad con filas de gran tamaño en PolyBase se agregará en una versión futura.|
| La carga de la tabla bcp con el tipo de datos MAX está dando error | Existe un problema conocido que requiere la colocación de VARCHAR(MAX), NVARCHAR(MAX) o VARBINARY(MAX) al final de la tabla en algunos escenarios. Intente mover las columnas MAX al final de la tabla.|

## Diferencias con respecto a Base de datos SQL

| Problema | Resolución |
| :----------------------------------| :---------------------------------------------- |
| Características de Base de datos SQL no admitidas | Consulte la [tabla de características no admitidas][].|
| Tipos de datos de Base de datos SQL no admitidos | Consulte los [tipos de datos no admitidos][].|
| Limitaciones de DELETE y UPDATE | Consulte las [soluciones alternativas de UPDATE][], las [soluciones alternativas de DELETE][] y el [uso de CTAS para resolver la sintaxis de UPDATE y DELETE no admitida][]. |
| No se admite la instrucción MERGE | Consulte las [soluciones alternativas para MERGE][].|
| Limitaciones de procedimientos almacenados | Consulte las [limitaciones de los procedimientos almacenados][] para conocer algunas de las limitaciones de los procedimientos almacenados.|
| Los UDF no admiten instrucciones SELECT | Se trata de una limitación actual de nuestros UDF. Consulte [CREATE FUNCTION][] para comprobar la sintaxis que se admite. |

## Pasos siguientes

Si no ha podido encontrar una solución a su problema con estos pasos, estos son otros recursos que puede probar.

- [Blogs]
- [Solicitud de función]
- [Vídeos]
- [Blogs del equipo de CAT]
- [Creación de una incidencia de soporte técnico]
- [Foro de MSDN]
- [Foro Stack Overflow]
- [Twitter]

<!--Image references-->

<!--Article references-->
[información general de seguridad]: ./sql-data-warehouse-overview-manage-security.md
[Creación de una incidencia de soporte técnico]: ./sql-data-warehouse-get-started-create-support-ticket.md
[escalado de Almacenamiento de datos SQL]: ./sql-data-warehouse-manage-compute-overview.md
[aprender a supervisar las consultas]: ./sql-data-warehouse-manage-monitor.md
[instrucciones de aprovisionamiento]: ./sql-data-warehouse-get-started-provision.md
[configuración del acceso de nivel de firewall para el cliente IP]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[Visual Studio 2013 o 2015]: ./sql-data-warehouse-get-started-connect.md
[Procedimientos recomendados para Almacenamiento de datos SQL de Azure]: ./sql-data-warehouse-best-practices.md
[tamaños de tabla]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[tabla de características no admitidas]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[tipos de datos no admitidos]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[baja calidad del índice de almacén de columnas]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[volver a generar los índices para mejorar la calidad del segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[administración de la carga de trabajo]: ./sql-data-warehouse-develop-concurrency.md
[uso de CTAS para resolver la sintaxis de UPDATE y DELETE no admitida]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[soluciones alternativas de UPDATE]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[soluciones alternativas de DELETE]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[soluciones alternativas para MERGE]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[limitaciones de los procedimientos almacenados]: /sql-data-warehouse-develop-stored-procedures/#limitations
[Autenticación a Almacenamiento de datos SQL de Azure]: ./sql-data-warehouse-authentication.md
[Evitar el requisito UTF-8 de PolyBase]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[SQL Server Native Client 10/11 (ODBC)]: https://msdn.microsoft.com/library/ms131415.aspx
[sys.database\_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs del equipo de CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitud de función]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Foro de MSDN]: https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureSQLDataWarehouse
[Foro Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

<!---HONumber=AcomDC_0706_2016-->