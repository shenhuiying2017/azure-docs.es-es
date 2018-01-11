---
title: "Tecnologías en memoria de Azure SQL Database | Microsoft Docs"
description: "Las tecnologías en memoria de Azure SQL Database mejoran notablemente el rendimiento de las cargas de trabajo transaccionales y de análisis."
services: sql-database
documentationCenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: 250ef341-90e5-492f-b075-b4750d237c05
ms.service: sql-database
ms.custom: develop databases
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jodebrui
ms.openlocfilehash: 23b313a473b93ba0eab7fc4cf97a5d26bfa31505
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Optimización del rendimiento mediante las tecnologías en memoria de SQL Database

Mediante el uso de tecnologías en memoria en Azure SQL Database, puede lograr mejoras de rendimiento con diversas cargas de trabajo: transaccionales (procesamiento de transacciones en línea [OLTP]), analíticas (procesamiento analítico en línea [OLAP]) y mixtas (procesamiento analítico y transaccional híbrido [HTAP]). Gracias al procesamiento más eficiente de las consultas y las transacciones, las tecnologías en memoria también lo ayudan a reducir costos. Normalmente no necesita actualizar el plan de tarifa de la base de datos para lograr mejoras de rendimiento. En algunos casos, tal vez pueda reducir incluso el plan de tarifa sin dejar de observar mejoras de rendimiento con las tecnologías en memoria.

A continuación se muestran dos ejemplos de cómo OLTP en memoria ayudó significativamente a mejorar el rendimiento:

- Gracias al uso de OLTP en memoria, [Quorum Business Solutions pudo duplicar la carga de trabajo al mismo tiempo que mejoró las DTU en un 70 %](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).
    - DTU significa *Unidad de transmisión de datos*, e incluye una medida del consumo de recursos.
- El vídeo siguiente muestra la mejora significativa del consumo de recursos con una carga de trabajo de ejemplo: [In-Memory OLTP in Azure SQL Database Video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (Vídeo sobre OLTP en memoria en Azure SQL Database).
    - Para más información, consulte la entrada de blog: [In-Memory OLTP in Azure SQL Database Blog Post](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/) (OLTP en memoria en la entrada de Blog de base de datos de SQL de Azure).

Las tecnologías en memoria están disponibles en todas las bases de datos del plan Premium, incluidas las de grupos elásticos Premium.

En el siguiente vídeo se explican posibles mejoras de rendimiento obtenidas con las tecnologías en memoria de Azure SQL Database. Tenga presente que la mejora de rendimiento que obtenga depende siempre de muchos factores, como la naturaleza de la carga de trabajo y los datos, los patrones de acceso de la base de datos, etc.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Azure SQL Database cuenta con las siguientes tecnologías en memoria:

- *OLTP en memoria* aumenta el rendimiento y reduce la latencia del procesamiento de transacciones. Estas son las situaciones en las que se obtienen ventajas con OLTP en memoria: procesamiento de transacciones de alto rendimiento, como operaciones comerciales y juegos, ingesta de datos de eventos o dispositivos de IoT, almacenamiento en caché, carga de datos y escenarios de tablas temporales y variables de tablas.
- Los *índices de almacén de columnas en clúster* reducen el espacio de almacenamiento necesario (hasta 10 veces) y mejoran el rendimiento de las consultas de análisis e informes. Puede usarlos con las tablas de hechos de sus data marts para incluir más datos en la base de datos y mejorar el rendimiento. También puede usarlos con los datos históricos de la base de datos operativa para archivar hasta 10 veces más datos, así como para disfrutar de un incremento equivalente en el número de consultas realizadas sobre ellos.
- Con los *índices de almacén de columnas no clúster* para HTAP, podrá obtener información en tiempo real sobre su negocio realizando consultas directamente a la base de datos operativa, sin necesidad de ejecutar un caro proceso de extracción, transformación y carga (ETL) ni esperar a que se rellene el almacén de datos. Los índices de almacén de columnas no clúster permiten una ejecución muy rápida de las consultas de análisis en la base de datos OLTP y, a la vez, reducen el impacto en la carga de trabajo operativa.
- También puede tener la combinación de una tabla optimizada para memoria con un índice de almacén de columnas. Esta combinación le permite realizar el procesamiento de transacciones de manera muy rápida y ejecutar consultas de análisis *simultáneamente* de manera muy rápida en los mismos datos.

Las opciones de índices de almacén de columnas y OLTP en memoria forman parte de SQL Server desde 2012 y 2014, respectivamente. Azure SQL Database y SQL Server comparten la misma implementación de tecnologías en memoria. A partir de ahora, las nuevas funciones para estas tecnologías se publican primero en Azure SQL Database y después, en SQL Server.

En este tema se describen aspectos de OLTP en memoria y los índices de almacén de columnas específicos de Azure SQL Database; además, se incluyen ejemplos:
- Veremos la repercusión de estas tecnologías en el almacenamiento, así como en los límites de tamaño de los datos.
- Después trataremos cómo administrar el movimiento de bases de datos que usan estas tecnologías entre los distintos planes de tarifa.
- Y también eremos dos ejemplos que ilustran el uso de OLTP en memoria y de los índices del almacén de columnas en Azure SQL Database.

Para obtener más información, consulte los siguientes recursos.

Información detallada sobre las tecnologías:

- [Información general y escenarios de uso de OLTP en memoria](https://msdn.microsoft.com/library/mt774593.aspx) (incluye referencias a información y casos prácticos de clientes para familiarizarse)
- [Documentación de In-Memory OLTP](http://msdn.microsoft.com/library/dn133186.aspx)
- [Descripción de los índices de almacén de columnas](https://msdn.microsoft.com/library/gg492088.aspx)
- Procesamiento analítico y transaccional híbrido (HTAP), también conocido como [análisis operativo en tiempo real](https://msdn.microsoft.com/library/dn817827.aspx)

Una guía rápida sobre OLTP en memoria: [Inicio rápido 1: Tecnologías OLTP en memoria para acelerar el rendimiento de Transact-SQL](http://msdn.microsoft.com/library/mt694156.aspx) (es otro artículo que lo ayudará a empezar a trabajar)

Vídeos detallados sobre las tecnologías:

- [In-Memory OLTP in Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (OLTP en memoria en Azure SQL Database), que contiene una demostración de las ventajas de rendimiento y los pasos para reproducir estos resultados
- [In-Memory OLTP Videos: What it is and When/How to use it](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/) (Vídeos de OLTP en memoria: qué es y cuándo/cómo usarlo)
- [Columnstore Index: In-Memory Analytics Videos from Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/) (Índice de almacén de columnas: vídeos sobre análisis en memoria de Ignite 2016)

## <a name="storage-and-data-size"></a>Almacenamiento y tamaño de datos

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Límite de almacenamiento y tamaño de datos para OLTP en memoria

OLTP en memoria incluye tablas optimizadas para memoria, que se usan para almacenar los datos de los usuarios. Estas tablas deben caber en la memoria. Dado que administra la memoria directamente en el servicio de SQL Database, tenemos el concepto de una cuota para datos de usuario. Esta idea se conoce como *almacenamiento de OLTP en memoria*.

Cada plan de tarifa de grupo elástico y de base de datos independiente admitido incluye una cantidad determinada de almacenamiento de OLTP en memoria. En el momento de escribir este artículo, obtiene un gigabyte de almacenamiento por cada 125 unidades de transacción de base de datos (DTU) o unidades de transacción de base de datos elástica (eDTU). Para obtener más información, consulte los [límites de recursos](sql-database-resource-limits.md).

Los siguientes elementos cuentan para su límite de almacenamiento de OLTP en memoria:

- Las filas de datos de usuarios activos en tablas optimizadas para memoria y variables de tabla. Tenga en cuenta que las versiones antiguas de las filas no cuentan para el límite.
- Los índices de tablas optimizadas para memoria.
- La sobrecarga operacional de operaciones ALTER TABLE.

Si alcanza el límite, recibirá un error que le notificará que se ha quedado sin cuota y no podrá volver a insertar o actualizar datos. Para mitigar este error, elimine datos o aumente el plan de tarifa de la base de datos o del grupo.

Para obtener más información sobre cómo supervisar la utilización del almacenamiento de OLTP en memoria y configurar alertas que se activen cuando casi haya alcanzado el límite, consulte [Supervisión del almacenamiento en memoria](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Acerca de los grupos elásticos

Con grupos elásticos, el almacenamiento de OLTP en memoria se comparte entre todas las bases de datos del grupo. Por lo tanto, el uso de una base de datos puede afectar a otras bases de datos. Existen dos formas de mitigar este problema:

- Establecer un valor de eDTU máx. para las bases de datos que sea inferior al número de eDTU del grupo en su conjunto. De este modo, se limita la utilización del almacenamiento de OLTP en memoria en cualquier base de datos del grupo al tamaño correspondiente al número de eDTU.
- Configurar un valor de eDTU mín. que sea mayor que 0. Este mínimo garantiza que cada base de datos del grupo tenga la cantidad de almacenamiento de OLTP en memoria disponible que corresponde al valor configurado para eDTU mín.

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Almacenamiento y tamaño de datos para los índices de almacén de columnas

No se requiere que los índices de almacén de columnas quepan en la memoria. Por lo tanto, el único límite del tamaño de los índices es el tamaño máximo global de la base de datos, que está documentado en el artículo sobre los [niveles de servicio de SQL Database](sql-database-service-tiers.md).

Al utilizar los índices de almacén de columnas en clúster, se emplea una compresión de columnas para el almacenamiento de la tabla base. Esta compresión puede reducir considerablemente el consumo de almacenamiento de sus datos de usuario, lo que significa que la base de datos podrá albergar más información. Y es posible aumentar este compresión aún más con la [compresión de archivo de columnas](https://msdn.microsoft.com/library/cc280449.aspx#Using Columnstore and Columnstore Archive Compression). La cantidad de compresión que puede lograr depende de la naturaleza de los datos, pero no es raro obtener una compresión que reduzca el tamaño en 10 veces.

Por ejemplo, si tiene una base de datos con el tamaño máximo de 1 terabyte (TB) y logra una compresión de 10 veces con índices de almacén de columnas, puede incluir un total de 10 TB de datos de usuario en la base de datos.

Al utilizar índices de almacén de columnas no agrupados, la tabla base sigue almacenada en el formato de almacenamiento de filas tradicional. Por lo tanto, el ahorro de almacenamiento no es tan grande como con los índices de almacén de columnas agrupados. Pero si sustituye diversos índices no agrupados tradicionales por un único índice de almacén de columnas, aún podrá obtener un ahorro global en el espacio de almacenamiento de la tabla.

## <a name="moving-databases-that-use-in-memory-technologies-between-pricing-tiers"></a>Movimiento de bases de datos que usan tecnologías en memoria entre planes de tarifa

Nunca hay incompatibilidades u otros problemas al actualizar a un plan de tarifas superior, como de Estándar a Premium. Solo aumenta la funcionalidad y los recursos disponibles.

Pero cambiar a un plan de tarifas inferior puede repercutir negativamente en la base de datos. El impacto es especialmente evidente al cambiar de Premium a Estándar o Básico cuando la base de datos contiene objetos de OLTP en memoria. Las tablas optimizadas para memoria y los índices de almacén de columnas no están disponibles después del cambio a una versión anterior (aunque sigan estando visibles). Lo mismo se aplica al reducir el plan de tarifa de un grupo elástico o mover bases de datos con tecnologías en memoria a un grupo elástico Estándar o Básico.

### <a name="in-memory-oltp"></a>In-Memory OLTP

*Reducción a Básico o Estándar*: OLTP en memoria no se admite en bases de datos que se encuentren en los planes Estándar o Básico. Además, no es posible mover una base de datos con objetos de OLTP en memoria a los planes Estándar o Básico.

No existe ningún mecanismo de programación para comprender si una base de datos específica admite OLTP en memoria. Puede ejecutar la siguiente consulta de Transact-SQL:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Si la consulta devuelve **1**, OLTP en memoria se admite en esta base de datos.

Antes de degradar el plan de tarifa de una base de datos a Estándar o Básico, quite todos los tipos de tabla y las tablas optimizadas para memoria, así como todos los módulos de Transact-SQL compilados de forma nativa. Las siguientes consultas identifican todos los objetos que deben quitarse antes de que se una base de datos de nivel Premium pase al nivel Estándar/Básico:

```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

*Reducción a un plan Premium inferior*: los datos de las tablas optimizadas para memoria deben caber en el almacenamiento de OLTP en memoria asociado al plan de tarifa de la base de datos o disponible en el grupo elástico. Si trata de reducir el plan de tarifa o mover la base de datos a un grupo que no disponga de almacenamiento de OLTP en memoria suficiente, la operación no se desarrolla correctamente.

### <a name="columnstore-indexes"></a>Índices de almacén de columnas

*Cambiar a Básico o Estándar*: los índices de almacén de columnas solo se admiten en el plan de tarifa Premium y no en los planes Estándar o Básico. Cuando se cambie la base de datos a Estándar o Básico, el índice de almacén de columnas no estará disponible. El sistema mantiene el índice de almacén de columnas, pero no aprovecha el índice. Si después actualiza a Premium, el índice de almacén de columnas está listo para volver a sacar el máximo partido.

Si tiene un índice de almacén de columnas **en clúster**, toda la tabla deja de estar disponible después del cambio a un nivel inferior. Por lo tanto, se recomienda quitar todos los índices de almacén de columnas *en clúster* antes de cambiar la base de datos por debajo del nivel Premium.

*Cambio a un plan Premium inferior*: este cambio se realiza correctamente si toda la base de datos se adapta al tamaño máximo de la base de datos del plan de tarifa objetivo o al almacenamiento disponible en el grupo elástico. Los índices de almacén de columnas no tienen ningún impacto concreto en este caso.


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Instalación del ejemplo de In-Memory OLTP.

La base de datos de ejemplo AdventureWorksLT se puede crear con unos pocos clics en el [Azure Portal](https://portal.azure.com/). Los pasos descritos en esta sección explican cómo puede enriquecer la base de datos AdventureWorksLT con objetos de OLTP en memoria y muestran las ventajas de rendimiento.

Si desea ver una demostración más simple, pero más atractiva visualmente, del rendimiento de OLTP en memoria, consulte:

- Versión: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Código fuente: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Pasos de instalación

1. En el [Azure Portal](https://portal.azure.com/), cree una base de datos Premium en un servidor. Establezca el **Origen** en la base de datos de ejemplo de AdventureWorksLT. Para obtener instrucciones detalladas, consulte [Creación de la primera Base de datos SQL de Azure](sql-database-get-started-portal.md).

2. Conéctese a la base de datos con SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copie el [script Transact-SQL de In-Memory OLTP](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) en el Portapapeles. El script T-SQL crea los objetos en memoria necesarios en la base de datos de ejemplo AdventureWorksLT que se creó en el paso 1.

4. Pegue el script T-SQL en SSMS.exe y, luego, ejecútelo. Es crucial la instrucción CREATE TABLE de la cláusula `MEMORY_OPTIMIZED = ON`. Por ejemplo:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Error 40536


Si recibe el error 40536 cuando ejecuta el script T-SQL, ejecute el siguiente script de T-SQL para comprobar que la base de datos admite In-Memory:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Un resultado de **0** significa que no se admite en memoria, mientras que un resultado de **1** significa que se admite. Para diagnosticar el problema, asegúrese de que la base de datos se encuentra en el nivel de servicio Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Acerca de los elementos creados optimizados para memoria

**Tablas**: el ejemplo contiene las siguientes tablas optimizadas para memoria:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Puede inspeccionar las tablas optimizadas para memoria a través del **Explorador de objetos** en SSMS. Haga clic con el botón derecho en **Tablas** > **Filtro** > **Configuración de filtro** > **Con optimización para memoria**. El valor es igual a 1.


O bien puede consultar las vistas de catálogo como:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Procedimiento almacenado compilado de forma nativa**: puede inspeccionar SalesLT.usp_InsertSalesOrder_inmem mediante una consulta de la vista de catálogo:


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Ejecución de la carga de trabajo de OLTP de ejemplo

La única diferencia entre los dos *procedimientos almacenados* siguientes es que el primer procedimiento usa las versiones de las tablas optimizadas para memoria, mientras que el segundo procedimiento usa las tablas en disco habituales:

- SalesLT**.**usp_InsertSalesOrder**_inmem**
- SalesLT**.**usp_InsertSalesOrder**_ondisk**


En esta sección verá cómo usar la práctica utilidad **ostress.exe** para ejecutar los dos procedimientos almacenados a niveles de esfuerzo. Puede comparar el tiempo que tardan en completarse las dos ejecuciones de esfuerzo.


Cuando se ejecuta ostress.exe, le recomendamos pasar los valores de parámetros diseñados para estos dos casos:

- Ejecute un gran número de conexiones simultáneas, mediante el uso de -n100.
- Haga que cada conexión se repita en bucle cientos de veces, mediante el uso de -r500.


Pero es posible que quiera comenzar con valores mucho más pequeños, como -n10 y -r50, para garantizar que todo está funcionando.


### <a name="script-for-ostressexe"></a>Script para ostress.exe


En esta sección se muestra el script de T-SQL que se inserta en la línea de comandos de ostress.exe. El script usa elementos creados por el script de T-SQL que instaló antes.


El siguiente script inserta un pedido de ventas de ejemplo con cinco elementos de línea en las siguientes *tablas* optimizadas para memoria:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Para hacer que la versión *_ondisk* del script T-SQL anterior sirva para ostress.exe, hay que reemplazar ambas repeticiones de la subcadena *_inmem* por *_ondisk*. Estos reemplazos afectan a los nombres de tablas y procedimientos almacenados.


### <a name="install-rml-utilities-and-ostress"></a>Instalación de ostress y utilidades de RML


Lo ideal sería planear la ejecución de ostress.exe en una máquina virtual de Azure (VM). Se crearía una [VM de Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) en la misma región geográfica de Azure en que reside la base de datos AdventureWorksLT. Pero puede ejecutar si lo desea ostress.exe en el equipo portátil.


En la VM, o en cualquier host que elija, instale las utilidades de Replay Markup Language (RML). Las utilidades incluyen ostress.exe.

Para más información, consulte:
- La explicación de ostress.exe en [Base de datos de ejemplo para OLTP en memoria](http://msdn.microsoft.com/library/mt465764.aspx).
- [Base de datos de ejemplo para OLTP en memoria](http://msdn.microsoft.com/library/mt465764.aspx).
- El [blog para instalar ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Ejecute de la carga de trabajo de esfuerzo *_inmem* en primer lugar


Puede usar una ventana *del símbolo del sistema de RML* para ejecutar la línea de comandos de ostress.exe. Los parámetros de la línea de comandos dirigen ostress para:

- Ejecutar 100 conexiones simultáneamente (-n100).
- Hacer que cada conexión ejecute el script de T-SQL 50 veces (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Para ejecutar la línea de comandos ostress.exe anterior:


1. Restablezca el contenido de los datos de la base de datos mediante la ejecución del siguiente comando en SSMS para eliminar todos los datos que se insertaron en las ejecuciones anteriores:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Copie el texto de la línea de comandos ostress.exe anterior en el Portapapeles.

3. Reemplace el `<placeholders>` de los parámetros -S -U -P -d por los valores reales correctos.

4. Ejecute la línea de comandos modificada en una ventana del símbolo del sistema de RML.


#### <a name="result-is-a-duration"></a>El resultado es una duración


Cuando finaliza ostress.exe, escribe la duración de la ejecución como la última línea de la salida en la ventana de símbolo del sistema de RML. Por ejemplo, una ejecución de prueba más corta duró aproximadamente 1,5 minutos:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Restablezca y edite *_ondisk* y, después, vuelva a ejecutarlo


Una vez que tenga el resultado de la ejecución de *_inmem*, realice los pasos siguientes para la ejecución de *_ondisk*:


1. Restablezca la base de datos mediante la ejecución del siguiente comando en SSMS para eliminar todos los datos que insertó la ejecución anterior:
```
EXECUTE Demo.usp_DemoReset;
```

2. Edite la línea de comandos de ostress.exe para reemplazar todos los *_inmem* con *_ondisk*.

3. Ejecute ostress.exe por segunda vez y capture el resultado de la duración.

4. Vuelva a restablecer la base de datos (para la eliminación responsable de lo que puede constituir una gran cantidad de datos de prueba).


#### <a name="expected-comparison-results"></a>Resultados de la comparación esperados

Las pruebas de en memoria demostraron tener un rendimiento **9 veces** mejor en esta carga de trabajo simplista, con ostress ejecutándose en una VM de Azure ubicada en la misma región de Azure que la base de datos.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Instalación del ejemplo de In-Memory Analytics.


En esta sección, compara los resultados de optimización de infraestructura y de estadísticas cuando usa un índice del almacén de columnas en lugar de un índice de árbol b tradicional.


Para realizar análisis en tiempo real en una carga de trabajo de OLTP, suele ser mejor usar un índice del almacén de columnas no agrupado. Para obtener más información, consulte [Guía de índices de almacén de columnas](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Preparación de la prueba de análisis del almacén de columnas


1. Use el Portal de Azure para crear una nueva base de datos AdventureWorksLT a partir del ejemplo.
 - Use ese nombre exacto.
 - Elija un nivel de servicio Premium.

2. Copie [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) en el Portapapeles.
 - El script T-SQL crea los objetos en memoria necesarios en la base de datos de ejemplo AdventureWorksLT que se creó en el paso 1.
 - El script crea la tabla de dimensiones y dos tablas de hechos. Las tablas de hechos se rellenan con 3,5 millones de filas cada una.
 - El script podría tardar 15 minutos en completarse.

3. Pegue el script T-SQL en SSMS.exe y, luego, ejecútelo. La palabra clave **COLUMNSTORE** es crucial en la instrucción **CREATE INDEX**, como en:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Establezca AdventureWorksLT en un nivel de compatibilidad 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    El nivel 130 no está directamente relacionado con las características de In-Memory. Pero el nivel 130 suele ofrecer un rendimiento de consultas más rápido que el nivel 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Tablas e índices de almacén de columnas clave


- dbo.FactResellerSalesXL_CCI es una tabla con un índice de almacén de columnas agrupado que tiene una compresión avanzada a nivel de *datos*.

- dbo.FactResellerSalesXL_PageCompressed es una tabla con un índice agrupado equivalente normal, que se comprime solo a nivel de *página*.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Consultas cruciales para comparar el índice de almacén de columnas


Aquí hay [varios tipos de consultas de T-SQL que se pueden ejecutar](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) para ver las mejoras de rendimiento. En el paso 2 del script T-SQL, preste atención a estas dos consultas. Difieren solo en una línea:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Un índice de almacén de columnas en clúster se encuentra en la tabla FactResellerSalesXL\_CCI.

El siguiente fragmento de script de T-SQL imprime las estadísticas de IO y TIME para la consulta de cada tabla.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

En una base de datos con el plan de tarifa P2, puede esperar un rendimiento nueve veces superior, aproximadamente, para esta consulta al usar el índice de almacén de columnas en clúster, en comparación con el índice tradicional. Con P15, puede esperar, aproximadamente, un rendimiento 57 veces superior gracias al uso de índice de almacén de columnas.



## <a name="next-steps"></a>Pasos siguientes

- [Inicio rápido 1: Tecnologías OLTP en memoria para acelerar el rendimiento de Transact-SQL](http://msdn.microsoft.com/library/mt694156.aspx)

- [Uso de OLTP en memoria para mejorar el rendimiento de las aplicaciones en Azure SQL](sql-database-in-memory-oltp-migration.md)

- [Supervisión del almacenamiento de OLTP en memoria](sql-database-in-memory-oltp-monitoring.md)


## <a name="additional-resources"></a>Recursos adicionales

#### <a name="deeper-information"></a>Información más detallada

- [Más información sobre cómo Quorum duplica cargas de trabajo clave de las bases de datos a la vez que reduce las DTU en un 70 % con OLTP en memoria en SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [In-Memory OLTP in Azure SQL Database Blog Post](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/) (OLTP en memoria en la entrada de Blog de base de datos de SQL de Azure)

- [Más información sobre OLTP en memoria](http://msdn.microsoft.com/library/dn133186.aspx)

- [Más información sobre los índices de almacén de columnas](https://msdn.microsoft.com/library/gg492088.aspx)

- [Más información sobre los análisis operativos en tiempo real](http://msdn.microsoft.com/library/dn817827.aspx)

- Consulte [Common Workload Patterns and Migration Considerations](http://msdn.microsoft.com/library/dn673538.aspx) (Patrones de cargas de trabajo comunes y consideraciones de migración), que describe los patrones de carga de trabajo donde In-Memory OLTP normalmente proporciona importantes mejoras de rendimiento.

#### <a name="application-design"></a>Diseño de aplicación

- [In-Memory OLTP (optimización In-Memory)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Uso de OLTP en memoria para mejorar el rendimiento de las aplicaciones en Azure SQL](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Herramientas

- [Portal de Azure](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)
