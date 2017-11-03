---
title: "Información general sobre las consultas elásticas de Azure SQL Database | Microsoft Docs"
description: "La consulta elástica permite ejecutar una consulta de Transact-SQL que abarca varias bases de datos."
services: sql-database
documentationcenter: 
manager: jhubbard
author: MladjoA
ms.assetid: a8bf0e2c-bc74-44d0-9b1e-bcc9a6aa2e33
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2016
ms.author: mlandzic
ms.openlocfilehash: 6389702b1be5e52c7191e6e57d17b48289e800b2
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Información general sobre las consultas elásticas de Azure SQL Database (versión preliminar)
La característica de consulta elástica (en versión preliminar) le permite ejecutar una consulta de Transact-SQL que abarca varias bases de datos en Azure SQL Database. Permite realizar consultas entre bases de datos para acceder a tablas remotas, así como conectar herramientas de Microsoft y de terceros (Excel, PowerBI, Tableau, etc.) para hacer consultas en capas de datos con varias bases de datos. Con esta característica, puede escalar consultas horizontalmente a capas de datos de gran tamaño en Base de datos SQL y visualizar los resultados en los informes de inteligencia empresarial (BI).


## <a name="why-use-elastic-queries"></a>¿Por qué usar consultas elásticas?

**Base de datos SQL de Azure**

Realice consultas entre bases de datos SQL de Azure completamente en T-SQL. lo que permite realizar consultas de solo lectura en bases de datos remotas. Esto proporciona una opción para que los clientes actuales de SQL Server local migren las aplicaciones que usan nombres de tres y cuatro partes o un servidor vinculado a la base de datos SQL.

**Disponible en el nivel Estándar**

La consulta elástica se admite en el nivel de rendimiento Estándar además de en el Premium. Vea la sección Limitaciones de versión preliminar más adelante, que trata sobre las limitaciones del rendimiento para los niveles de rendimiento inferiores.

**Inserción en bases de datos remotas**

Ahora las consultas elásticas pueden insertar parámetros SQL en las bases de datos remotas para su ejecución.

**Ejecución de un procedimiento almacenado**

Ejecute llamadas a procedimientos almacenados remotos o funciones remotas mediante [sp\_execute\_remote](https://msdn.microsoft.com/library/mt703714).

**Flexibilidad**

Las tablas externas con consulta elástica ahora pueden hacer referencia a tablas remotas con un nombre de tabla o un esquema diferente.

## <a name="elastic-query-scenarios"></a>Escenarios de consulta elástica

El objetivo es facilitar escenarios de consulta en los que varias bases de datos aportan filas a un único resultado global. El usuario o la aplicación pueden componer la consulta directamente, o también se puede conseguir de forma indirecta mediante las herramientas que están conectadas a la base de datos. Esto resulta especialmente útil cuando se crean informes, mediante herramientas de integración de datos o de BI comerciales, o bien cualquier aplicación que no se pueda cambiar. Con una consulta elástica, puede consultar varias bases de datos por medio de la conocida experiencia de conectividad de SQL Server en herramientas como Excel, PowerBI, Tableau o Cognos.
Una consulta elástica facilita el acceso a toda una colección de bases de datos a través de las consultas emitidas por SQL Server Management Studio o Visual Studio. Asimismo, permite las consultas entre bases de datos desde Entity Framework u otros entornos de ORM. En la Figura 1 se muestra un escenario donde una aplicación en la nube existente (que usa la [biblioteca de cliente de bases de datos elásticas](sql-database-elastic-database-client-library.md)) se compila en una capa de datos escalada horizontalmente y se usa una consulta elástica para informes entre bases de datos.

**Figura 1** Consulta usada en la capa de datos de escala horizontal

![Consulta usada en la capa de datos de escala horizontal][1]

Los escenarios de clientes para la consulta elástica se caracterizan por las siguientes topologías:

* **Particiones verticales: consultas entre bases de datos** (topología 1): los datos se particionan en vertical entre varias bases de datos en una capa de datos. Normalmente, los distintos conjuntos de tablas residen en bases de datos diferentes. Esto significa que el esquema es diferente en las distintas bases de datos. Por ejemplo, todas las tablas de inventario se encuentran en una base de datos mientras que todas las relacionadas con la contabilidad se encuentran en otra. En los casos de uso habituales con esta topología, se requiere uno para realizar una consulta o compilar informes en todas las tablas de varias bases de datos.
* **Particiones horizontales: Particionamiento** (topología 2): los datos se particionan en horizontal para distribuir las filas en una capa de datos de escala horizontal. Con este enfoque, el esquema es idéntico en todas las bases de datos participantes. Este enfoque también se denomina simplemente "particionamiento". El particionamiento se puede realizar y administrar mediante 1) las bibliotecas de herramientas de bases de datos elásticas o 2) el particionamiento automático. Se usa una consulta elástica para realizar consultas o compilar informes en muchas particiones.

> [!NOTE]
> La consulta elástica funciona mejor en escenarios de informes ocasionales, en los que la mayor parte del procesamiento se puede realizar en la capa de datos. Para grandes cargas de trabajo de informes o escenarios de almacenamiento de datos con consultas más complejas, considere también usar [Almacenamiento de datos SQL de Azure](https://azure.microsoft.com/services/sql-data-warehouse/).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Particionamiento vertical: consultas entre bases de datos

Para empezar a codificar, consulte la [introducción a la consulta entre bases de datos (creación de particiones verticales)](sql-database-elastic-query-getting-started-vertical.md).

Se puede usar una consulta elástica para que los datos de una base de datos SQL estén disponibles para otras bases de datos SQL. Esto permite que las consultas desde una base de datos hagan referencia a las tablas de cualquier otra base de datos SQL remota. El primer paso consiste en definir un origen de datos externo para cada base de datos remota. El origen de datos externo se define en la base de datos local desde la que desee obtener acceso a las tablas que residen en la base de datos remota. No es necesario realizar cambios en la base de datos remota. En los escenarios típicos de particionamiento vertical en los que las diferentes bases de datos tienen esquemas distintos, se pueden usar consultas elásticas para implementar casos de uso habituales, como el acceso a datos de referencia y la consulta entre bases de datos.

> [!IMPORTANT]
> Se debe poseer el permiso ALTER ANY EXTERNAL DATA SOURCE. Este permiso está incluido en el permiso ALTER DATABASE. Se necesitan permisos ALTER ANY EXTERNAL DATA SOURCE para hacer referencia al origen de datos subyacente.
>

**Datos de referencia**: la topología se usa para la administración de datos de referencia. En la ilustración siguiente, se mantienen dos tablas (T1 y T2) con datos de referencia en una base de datos dedicada. Con una consulta elástica, ahora puede acceder a las tablas T1 y T2 de forma remota desde otras bases de datos, como se muestra en la ilustración. Use la topología 1 si las tablas de referencia son pequeñas o si las consultas remotas en la tabla de referencia tienen predicados selectivos.

**Ilustración 2** Particionamiento vertical: usar una consulta elástica para consultar datos de referencia

![Particionamiento vertical: usar una consulta elástica para consultar datos de referencia][3]

**Consulta entre bases de datos**: las consultas elásticas hacen posibles los casos de uso que requieren realizar consultas en varias bases de datos SQL. En la ilustración 3, se muestran cuatro bases de datos diferentes: CRM, Inventory, HR y Products. Las consultas realizadas en una de las bases de datos también necesitan acceder a una de las otras bases de datos o a todas ellas. Mediante una consulta elástica, puede configurar la base de datos para este caso ejecutando unas pocas instrucciones DDL sencillas en cada una de las cuatro bases de datos. Después de esta configuración única, el acceso a una tabla remota es tan sencillo como hacer referencia a una tabla local desde las consultas T-SQL o las herramientas de BI. Se recomienda este enfoque si las consultas remotas no devuelven resultados de gran tamaño.

**Ilustración 3** Particionamiento vertical: usar una consulta elástica para consultar en varias bases de datos

![Particionamiento vertical: usar una consulta elástica para consultar en varias bases de datos][4]

Con los siguientes pasos, se configuran consultas de bases de datos elásticas para escenarios de particionamiento vertical que requieren acceso a tablas ubicadas en una base de datos SQL remota con el mismo esquema:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) miClaveMaestra
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) miCredencial
* [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx) miOrigenDeDatos de tipo **RDBMS**
* [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) miTabla

Después de ejecutar las instrucciones DDL, puede acceder a la tabla remota "miTabla" como si fuera una tabla local. Base de datos SQL de Azure abre automáticamente una conexión con la base de datos remota, procesa la solicitud en la base de datos remota y devuelve los resultados.

## <a name="horizontal-partitioning---sharding"></a>Particiones horizontales (particionamiento)
El uso de una consulta elástica para realizar tareas de informes en una capa de datos particionada, es decir, con particiones horizontales, requiere un [mapa de particiones de bases de datos elásticas](sql-database-elastic-scale-shard-map-management.md) para representar las bases de datos de la capa de datos. Normalmente, se usa un solo mapa de particiones en este escenario y una base de datos dedicada con funcionalidades de consulta elástica (nodo principal) sirve de punto de entrada para las consultas de informes. Esta base de datos dedicada es la única que necesita acceder al mapa de particiones. La ilustración 4 muestra esta topología y su configuración con la base de datos de consulta elástica y el mapa de particiones. Las bases de datos en la capa de datos pueden ser de cualquier edición o versión de Base de datos SQL de Azure. Para obtener más información acerca de la biblioteca de cliente de bases de datos elásticas y crear mapas de particiones, consulte [Administración de mapas de particiones](sql-database-elastic-scale-shard-map-management.md).

**Ilustración 4** Particionamiento horizontal: usar una consulta elástica para informes en capas de datos particionadas

![Particionamiento horizontal: usar una consulta elástica para informes en capas de datos particionadas][5]

> [!NOTE]
> La base de datos de consulta elástica (nodo principal) puede ser una independiente o la misma que hospeda el mapa de particiones. Sea cual sea la configuración que elija, asegúrese de que ese servicio y el nivel de rendimiento de esa base de datos es suficientemente alto como para controlar la cantidad de solicitudes de inicio de sesión/consulta esperada.

Con los siguientes pasos, se configuran consultas de bases de datos elásticas para escenarios de particionamiento horizontal que requieren acceso a un conjunto de tablas ubicadas, por lo general, en varias bases de datos SQL remotas:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) miClaveMaestra
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) miCredencial
* Cree un [mapa de particiones](sql-database-elastic-scale-shard-map-management.md) que represente su capa de datos mediante la biblioteca de cliente de bases de datos elásticas.   
* [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx) miOrigenDeDatos de tipo **SHARD_MAP_MANAGER**
* [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) miTabla

Una vez que realice estos pasos, puede acceder a la tabla con partición horizontal "miTabla" como si fuera una tabla local. Base de datos SQL de Azure abre automáticamente varias conexiones paralelas con las bases de datos remotas donde se almacenan las tablas, procesa las solicitudes en las bases de datos remotas y devuelve los resultados.
Obtenga más información sobre los pasos necesarios para el escenario de particionamiento horizontal en [Consulta de bases de datos elásticas para particionamiento horizontal](sql-database-elastic-query-horizontal-partitioning.md).

Para empezar a codificar, consulte [Introducción a las consultas elásticas para particionamiento horizontal (particionamiento)](sql-database-elastic-query-getting-started.md).

## <a name="t-sql-querying"></a>Consultas T-SQL
Una vez que defina los orígenes de datos externos y las tablas externas, puede usar cadenas de conexión de SQL Server normales para conectarse a las bases de datos en las que definió las tablas externas. A continuación, puede ejecutar instrucciones T-SQL en las tablas externas de esa conexión, con las limitaciones que se describen a continuación. Puede encontrar más información y ejemplos de consultas de T-SQL en los temas de documentación sobre [particiones horizontales](sql-database-elastic-query-horizontal-partitioning.md) y [particiones verticales](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Conectividad para herramientas
Puede usar cadenas de conexión de SQL Server normales para conectar sus aplicaciones y herramientas de integración de datos o de BI a bases de datos con tablas externas. Asegúrese de que SQL Server se admite como origen de datos para la herramienta. Una vez conectadas, consulte la base de datos de consulta elástica y las tablas externas de esa base de datos como haría con cualquier otra base de datos de SQL Server a la que se conecte con su herramienta.

> [!IMPORTANT]
> Actualmente no se admite la autenticación usando Azure Active Directory con consultas elásticas.
> 
> 

## <a name="cost"></a>Coste
La consulta elástica se incluye en el costo de las bases de datos de Base de datos SQL de Azure. Tenga en cuenta que se admiten las topologías en las que las bases de datos remotas se encuentran en un centro de datos diferente al punto de conexión de la consulta elástica, pero la salida de datos de las bases de datos remotas se cobra a [tarifas de Azure](https://azure.microsoft.com/pricing/details/data-transfers/)normales.

## <a name="preview-limitations"></a>Limitaciones de vista previa
* Ejecutar una consulta elástica por primera vez puede tardar unos minutos en el nivel de rendimiento Estándar. Esta vez es necesario cargar la funcionalidad de consulta elástica; el rendimiento de carga mejora con niveles superiores de rendimiento.
* Aún no se admite el scripting de orígenes de datos externos o tablas externas desde SSMS o SSDT.
* La función Importación/Exportación para bases de datos SQL aún no admite orígenes de datos externos ni tablas externas. Si necesita usar Importación/Exportación, quite estos objetos antes de exportar y después vuelva a crearlos después de importar.
* Actualmente, la consulta elástica solo es compatible con el acceso de solo lectura a tablas externas. Sin embargo, puede usar toda la funcionalidad de T-SQL en la base de datos donde se define la tabla externa. Esto puede ser útil, por ejemplo, para conservar resultados temporales mediante por ejemplo SELECT <column_list> INTO <local_table>, o para definir procedimientos almacenados en la base de datos de consulta elástica que hacen referencia a tablas externas.
* A excepción de nvarchar(max), los tipos LOB no se admiten en las definiciones de tabla externa. Como alternativa, cree una vista en la base de datos remota que convierta el tipo LOB en nvarchar(max), defina la tabla externa en la vista en lugar de la tabla base y después conviértala en el tipo LOB original en las consultas.
* Actualmente no se admiten estadísticas de columna con tablas externas. Se admiten las estadísticas de tabla, pero se deben crear manualmente.

## <a name="feedback"></a>Comentarios
Comparta sus comentarios sobre su experiencia con las consultas elásticas en Livefyre más abajo, los foros de MSDN o Stackoverflow. Estamos interesados en todo tipo de comentarios sobre el servicio (defectos, particularidades, deficiencias de característica, etc.).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener un tutorial sobre la creación de particiones verticales, consulte [Introducción a las consultas entre bases de datos (particiones verticales)](sql-database-elastic-query-getting-started-vertical.md).
* Para ver la sintaxis y consultas de ejemplo para los datos con particionamiento vertical, consulte [Consulta de datos particionados verticalmente](sql-database-elastic-query-vertical-partitioning.md)
* Para obtener un tutorial sobre la creación de particiones horizontales (particionamiento), consulte [Introducción a las consultas elásticas para las particiones horizontales (particionamiento)](sql-database-elastic-query-getting-started.md).
* Para ver la sintaxis y consultas de ejemplo para los datos con particionamiento horizontal, consulte [Consulta de datos particionados horizontalmente.](sql-database-elastic-query-horizontal-partitioning.md)
* Consulte [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714) para ver un procedimiento almacenado que ejecuta una instrucción de Transact-SQL en una sola instancia remota de Azure SQL Database o un conjunto de bases de datos que actúan como particiones en un esquema de particiones horizontales.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
