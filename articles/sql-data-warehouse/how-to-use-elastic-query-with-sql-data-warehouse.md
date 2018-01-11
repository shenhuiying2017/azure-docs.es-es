---
title: "Conceptos de consulta elástica con Azure SQL Data Warehouse | Microsoft Docs"
description: "Conceptos de consulta elástica con Azure SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 09/18/2017
ms.author: elbutter
ms.openlocfilehash: 4c351d88b31adfa3443dd2231f67bb442f2b8fe0
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2017
---
# <a name="how-to-use-elastic-query-with-sql-data-warehouse"></a>Uso de consulta elástica con SQL Data Warehouse



La consulta elástica con Azure SQL Data Warehouse le permite escribir Transact-SQL en SQL Database que se envía de forma remota a una instancia de Azure SQL Data Warehouse mediante el uso de tablas externas. El uso de esta característica proporciona ahorros en los costos y arquitecturas más efectivas según el escenario.

Esta característica habilita dos escenarios principales:

1. Aislamiento de dominios
2. Ejecución de consultas remotas

### <a name="domain-isolation"></a>Aislamiento de dominios

El aislamiento de dominios hace referencia al escenario clásico de data mart. En determinados escenarios, puede que quiera proporcionar un dominio lógico de datos a los usuarios de bajada que están aislados del resto del almacenamiento de datos, por diversas razones, entre las que se incluyen:

1. Aislamiento de recursos: la base de datos SQL está optimizada para atender una gran base de usuarios simultáneos que sirven cargas de trabajo ligeramente diferentes a las grandes consultas analíticas para las que está reservado el almacenamiento de datos. El aislamiento garantiza que las herramientas adecuadas atiendan las cargas de trabajo correctas.
2. Aislamiento de seguridad: para separar un subconjunto de datos autorizado de forma selectiva mediante determinados esquemas.
3. Espacio aislado: para proporcionar un conjunto de datos de ejemplo como "sitio de pruebas" para explorar consultas de producción, etc.

La consulta elástica puede ofrecer la posibilidad de seleccionar fácilmente subconjuntos de datos de almacenamiento de datos SQL y moverlos a una instancia de base de datos SQL. Además, este aislamiento no descarta la posibilidad de habilitar también la ejecución de consultas remotas, lo que permite escenarios de "caché" más interesantes.

### <a name="remote-query-execution"></a>Ejecución de consultas remotas

La consulta elástica permite la ejecución de consultas remotas en una instancia de almacenamiento de datos SQL. Es posible usar lo mejor de la base de datos SQL y del almacenamiento de datos SQL mediante la separación de los datos activos e inactivos entre las dos bases de datos. Los usuarios pueden mantener los datos más recientes en una base de datos SQL, que puede atender informes y un gran número de usuarios empresariales promedio. Sin embargo, cuando se necesiten más datos o cálculos, un usuario puede descargar parte de la consulta en una instancia de almacenamiento de datos SQL donde pueden procesarse agregados a gran escala de manera más rápida y eficiente.



## <a name="elastic-query-overview"></a>Información general sobre la consulta elástica

Una consulta elástica se puede usar para que los datos ubicados dentro de un almacenamiento de datos SQL estén disponibles para las instancias de almacenamiento de datos SQL. La consulta elástica permite que las consultas de una base de datos SQL hagan referencia a tablas de una instancia remota de almacenamiento de datos SQL. 

El primer paso es crear una definición de origen de datos externos que haga referencia a la instancia de almacenamiento de datos SQL, que usa las credenciales de usuario existentes en dicho almacenamiento. No es necesario ningún cambio en la instancia remota de almacenamiento de datos SQL. 

> [!IMPORTANT] 
> 
> Se debe poseer el permiso ALTER ANY EXTERNAL DATA SOURCE. Este permiso está incluido en el permiso ALTER DATABASE. Se necesitan permisos ALTER ANY EXTERNAL DATA SOURCE para hacer referencia a los orígenes de datos remotos.

A continuación, se creará una definición de tabla externa remota en una instancia de base de datos SQL que apunta a una tabla remota del almacenamiento de datos SQL. Cuando se usa una consulta que emplea una tabla externa, la parte de la consulta que hace referencia a la tabla externa se envía a la instancia de almacenamiento de datos SQL para su procesamiento. Cuando se ha completado la consulta, el conjunto de resultados se envía de vuelta a la instancia de base de datos SQL que realiza la llamada. Para obtener un breve tutorial sobre la configuración de una consulta elástica entre la base de datos SQL y el almacenamiento de datos SQL, consulte [Configure Elastic Query with SQL Data Warehouse ][Configure Elastic Query with SQL Data Warehouse] (Configuración de consultas elásticas con SQL Data Warehouse).

Para más información sobre la consulta elástica con SQL Database, consulte [Información general sobre las consultas elásticas de Azure SQL Database][Azure SQL Database elastic query overview].



## <a name="best-practices"></a>Prácticas recomendadas

### <a name="general"></a>General

- Cuando use la ejecución de consultas remotas, asegúrese de que solo selecciona las columnas necesarias y aplica los filtros correctos. De esta forma, no solo aumenta el proceso necesario, sino que también aumenta el tamaño del conjunto de resultados y, por lo tanto, la cantidad de datos que es necesario mover entre las dos instancias.
- Los datos que sea necesario analizar manténgalos en SQL Data Warehouse y SQL Database en el almacén de columnas en clúster para un mejor rendimiento.
- Asegúrese de que las tablas de origen estén particionadas para el movimiento de datos y las consultas.
- Asegúrese de que las instancias de base de datos SQL utilizadas como caché estén particionadas para permitir actualizaciones más pormenorizadas y una administración más sencilla. 
- Lo mejor es usar bases de datos PremiumRS, ya que proporcionan las ventajas de análisis de la indexación de almacén de columnas en clúster, pero con un enfoque sobre las cargas de trabajo intensivas de E/S con un descuento sobre las bases de datos Premium.
- Después de las cargas, use columnas de identificación de fecha efectiva o carga para las operaciones upserts en las instancias de SQL Database a fin de mantener la integridad del origen de la caché. 
- Cree un inicio de sesión y un usuario independientes en la instancia de almacenamiento de datos SQL para las credenciales de inicio de sesión remoto de base de datos SQL definidas en el origen de datos externos. 

### <a name="elastic-querying"></a>Consulta elástica

- En muchos casos, alguien podría querer administrar un tipo de tabla ajustada, donde una parte de la tabla se encuentre dentro de SQL Database como datos almacenados en caché para el rendimiento con el resto de los datos almacenados en SQL Data Warehouse. Necesita tener dos objetos en SQL Database: una tabla externa en SQL Database que haga referencia a la tabla base de SQL Data Warehouse y la parte "en caché" de la tabla en SQL Database. Considere la posibilidad de crear una vista en la parte superior de la parte en caché de la tabla y la tabla externa que una ambas tablas y aplique filtros que separen los datos materializados en SQL Database y los datos de SQL Data Warehouse expuestos a través de tablas externas.

  Imagine que quisiéramos mantener el año de datos más reciente en una instancia de base de datos SQL. Tenemos dos tablas **ext Orders**, que hace referencia a las tablas de pedidos de almacenamiento de datos, y **dbo. Orders** que representa los años más recientes de datos dentro de la instancia de la base de datos SQL. En lugar de pedir a los usuarios que decidan si consultar una tabla u otra, se crea una vista en la parte superior de las dos tablas en el punto de partición del año más reciente.

  ```sql
  CREATE VIEW dbo.Orders_Elastic AS
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]
  FROM
    [dbo].[Orders]
  WHERE 
    YEAR([o_orderdate]) >= '<Most Recent Year>'
  UNION
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]         
  FROM
    [ext].[Orders]
  WHERE
    YEAR([o_orderdate]) < '<Most Recent Year>'
  ```

  Una vista producida de tal manera, permite que el compilador de consultas determine si necesita usar la instancia de almacenamiento de datos para responder a la consulta de los usuarios. 

  Existe una sobrecarga de envío, compilación, ejecución y movimiento de datos asociada a cada consulta elástica en la instancia de almacenamiento de datos. Tenga en cuenta que cada consulta elástica va en detrimento de los intervalos de simultaneidad y utiliza recursos.  


- Si planea profundizar más en el conjunto de resultados desde la instancia de almacenamiento de datos, considere la posibilidad de materializarlo en una tabla temporal en SQL Database para un mejor rendimiento y evitar así el uso innecesario de recursos.

### <a name="moving-data"></a>Movimiento de datos 

- Si es posible, simplifique la administración de datos con tablas de origen solo de anexión de forma que las actualizaciones se puedan mantener fácilmente entre las instancias de almacenamiento de datos y de base de datos.
- Mueva los datos en el nivel de partición con la semántica de vacío y relleno para minimizar el costo de las consultas en el nivel de almacenamiento de datos y la cantidad de datos movidos a fin de mantener actualizada la instancia de base de datos. 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>Cuándo elegir Azure Analysis Services frente a SQL Database

#### <a name="azure-analysis-services"></a>Azure Analysis Services

- Si tiene pensado usar su caché con una herramienta BI que envía grandes cantidades de consultas pequeñas
- Si necesita latencia de consulta de subsegundos
- Si tiene experiencia con la administración o el desarrollo de modelos para Analysis Services 

#### <a name="sql-database"></a>SQL Database

- Si quiere consultar sus datos en caché con SQL
- Si necesita la ejecución remota de determinadas consultas
- Si tiene requisitos de caché más grandes



## <a name="faq"></a>P+F

P: ¿Puedo usar bases de datos en un grupo elástico con una consulta elástica?

R: Sí. Las bases de datos SQL de un grupo elástico pueden usar consultas elásticas. 

P: ¿Existe un límite en cuanto al número de bases de datos que pueden utilizarse para la consulta elástica?

P: No existe ningún límite en cuanto al número de bases de datos que pueden utilizarse para la consulta elástica. Sin embargo, cada consulta elástica (consultas que lleguen a SQL Data Warehouse) contará para los límites de simultaneidad normales.

P: ¿Hay límites de DTU relacionados con las consultas elásticas?

R: Los límites de DTU no se imponen de forma distinta para consultas elásticas. La directiva estándar establece que los servidores lógicos tienen límites de DTU para evitar que los clientes gasten de manera excesiva de forma accidental. Si va a habilitar varias bases de datos para consulta elástica junto con una instancia de SQL Data Warehouse, podría alcanzar el límite inesperadamente. Si esto ocurre, envíe una solicitud para aumentar el límite de DTU en su servidor lógico. Puede aumentar su cuota mediante la [creación de una incidencia de soporte técnico](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) y la selección de *Cuota* como el tipo de solicitud

P: ¿Puedo usar la seguridad de nivel de fila o el enmascaramiento dinámico de datos con la consulta elástica?

R: Los clientes que quieran usar características de seguridad más avanzadas con SQL Database pueden hacerlo, pero primero deben mover y almacenar los datos en la instancia de SQL Database. Actualmente no se puede aplicar seguridad de nivel de fila o DDM en datos consultados mediante tablas externas. 

P: ¿Puedo escribir desde mi instancia de base de datos SQL en la instancia de almacenamiento de datos?

R: En la actualidad no se admite esta característica. Visite nuestra [página de comentarios][Feedback page] para crear esta funcionalidad o votar por ella si es una característica que quiera ver en el futuro. 

P: ¿Puedo usar tipos espaciales como geometría o geografía?

R: Puede almacenar tipos espaciales en SQL Data Warehouse como valores varbinary(max). Al consultar estas columnas mediante la consulta elástica, puede convertirlas en los tipos adecuados en tiempo de ejecución.

![tipos espaciales](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)





<!--Image references-->

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop/
[Configure Elastic Query with SQL Data Warehouse]: ./tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md

<!--MSDN references-->

<!--Other Web references-->
