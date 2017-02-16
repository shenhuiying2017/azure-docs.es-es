---
title: 'Azure SQL Data Warehouse: tutorial introductorio | Microsoft Docs'
description: Tutorial introductorio sobre Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 12/21/2016
ms.author: elbutter
translationtype: Human Translation
ms.sourcegitcommit: 5bb75bf36892c737568b8129b30bb30b02d01bf2
ms.openlocfilehash: 1227903652a944d9d144917922fe36a4f149f820


---
# <a name="get-started-with-sql-data-warehouse"></a>Introducción a SQL Data Warehouse

Tutorial introductorio sobre Azure SQL Data Warehouse. Este tutorial le enseñará los fundamentos de aprovisionamiento y carga de datos en una instancia de SQL Data Warehouse, así como algunos conceptos básicos sobre el escalado, pausa y ajuste. 

**Tiempo estimado para completar:** 75 minutos

## <a name="prerequisites"></a>Requisitos previos


### <a name="sign-up-for-microsoft-azure"></a>Suscribirse a Microsoft Azure
Si no dispone de ninguna cuenta de Microsoft Azure, deberá registrarse para utilizar este servicio. Si ya tiene una cuenta, puede omitir este paso. 

1. Vaya a las páginas de cuentas [https://azure.microsoft.com/account/](https://azure.microsoft.com/account/)
2. Cree una cuenta de Azure gratis o compre una cuenta.
3. Siga las instrucciones.

### <a name="install-appropriate-sql-client-driver-and-tools"></a>Instalación de las herramientas y los controladores de cliente SQL adecuados

La mayoría de las herramientas de cliente SQL se pueden conectar a Azure SQL Data Warehouse mediante JDBC, ODBC o ADO.net. Debido a la complejidad del producto y al gran número de características de T-SQL que admite SQL Data Warehouse, no todas las aplicaciones cliente pueden ser totalmente compatibles con SQL Data Warehouse.

Si está ejecutando un sistema operativo Windows, se recomienda usar [Visual Studio] o [SQL Server Management Studio].


[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-an-azure-sql-data-warehouse"></a>Creación de una instancia de Almacenamiento de datos SQL de Azure

> [!NOTE]
> La creación de una instancia de Almacenamiento de datos SQL puede dar lugar a un nuevo servicio facturable.  Para más información, consulte [Precios de Azure SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>


### <a name="create-a-sql-data-warehouse"></a>Creación de Almacenamiento de datos SQL
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Nuevo** > **Bases de datos** > **SQL Data Warehouse**.

    ![NewBlade](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![SelectDW](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. Rellene los detalles de la implementación

    **Nombre de la base de datos**: elija el que desee. Si tiene varias instancias de SQL DW, se recomienda que el nombre incluya detalles como su región, entorno, etc., por ejemplo, *mydw-westus-1-test*

    **Suscripción**: su suscripción a Azure

    **Grupo de recursos**: cree uno nuevo (o utilice el existente si pretende usar Azure SQL Data Warehouse con otros servicios)
    > [!NOTE]
    > Los servicios de un grupo de recursos deben tener el mismo ciclo de vida. Los grupos de recursos son útiles para realizar tareas de administración de recursos como el control de acceso y la implementación de plantillas. Consulte más información sobre los grupos de recursos de Azure [aquí](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups).
    >

    **Origen**: base de datos en blanco

    **Servidor**: seleccione el servidor que creó en los [requisitos previos].

    **Intercalación**: mantenga la intercalación predeterminada SQL_Latin1_General_CP1_CI_AS

    **Seleccione rendimiento**: se recomienda mantener el estándar 400DWU

4. Seleccione **Anclar al panel**
    ![Anclar al panel](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png)

5. Póngase cómodo y espere a que Azure SQL Data Warehouse se implemente. Lo normal es que este proceso tarde varios minutos. El portal le notificará cuando la implementación haya terminado. 

## <a name="connect-to-azure-sql-data-warehouse-through-sql-server-logical-server"></a>Conexión a Azure SQL Data Warehouse mediante la opción SQL Server (servidor lógico).

Este tutorial usa SQL Server Management Studio (SSMS) para conectarse a SQL Data Warehouse. Otras herramientas que se pueden usar para conectarse a SQL Data Warehouse a través de conectores compatibles: ADO.NET, JDBC, ODBC y PHP. Tenga en cuenta que la funcionalidad podría ser limitada para herramientas no compatibles con Microsoft.


### <a name="get-connection-information"></a>Obtención de información sobre la conexión

Para conectarse a SQL Data Warehouse, debe conectarse mediante la instancia de SQL Server (servidor lógico) que creó en los [requisitos previos].

1. Seleccione SQL Data Warehouse en el panel o búsquelo en los recursos.

    ![Panel de SQL Data Warehouse](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. Busque el nombre completo del servidor lógico.

    ![Seleccionar nombre del servidor](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. Abra SSMS y utilice el Explorador de objetos para conectarse a este servidor mediante las credenciales que creó en los [requisitos previos]

    ![Conectarse con SSMS](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

Si todo funciona correctamente, ahora deberá conectarse a la instancia de SQL Server (servidor lógico). Puede usar las credenciales del servidor para autenticarse en cualquier base de datos de este como propietario. No obstante, se recomienda que cree inicios de sesión y usuarios diferentes para cada base de datos. La creación de usuarios se trata en [Creación de un usuario de SQL Data Warehouse](./sql-data-warehouse-get-started-tutorial.md#create-a-user-for-sql-data-warehouse). 

## <a name="create-a-user-for-sql-data-warehouse"></a>Creación de un usuario de SQL Data Warehouse

### <a name="why-create-a-separate-user"></a>¿Por qué crear un usuario diferente?

Se usa la conexión a la instancia de SQL Server (servidor lógico), con las credenciales de servidor del paso anterior, para crear un nuevo usuario de SQL Data Warehouse. Hay dos razones principales por las que es aconsejable crear un usuario o inicio de sesión independiente para SQL DW.

1.  Los usuarios de su organización deben usar una cuenta diferente para autenticarse. De este modo, puede limitar los permisos que se conceden a la aplicación y reducir los riesgos causados por actividades malintencionadas.

2. De forma predeterminada, el inicio de sesión de administrador del servidor con el que está conectado actualmente usa una clase de recursos más pequeña. Las clases de recursos le permiten controlar la asignación de memoria y los ciclos de CPI que se asignan a una consulta. Los usuarios en **smallrc** tienen una menor cantidad de memoria y pueden aprovechar una mayor simultaneidad. Por el contrario, los usuarios asignados a **xlargerc** reciben grandes cantidades de memoria y, por tanto, son menos las consultas que se pueden ejecutar de manera simultánea. Para cargar los datos de forma que la compresión sea óptima, debe asegurarse de que el usuario que carga los datos forma parte de una clase de recursos más grande. Obtenga más información sobre las clases de recursos [aquí](./sql-data-warehouse-develop-concurrency.md#resource-classes):

### <a name="creating-a-user-of-a-larger-resource-class"></a>Creación de un usuario de una clase de recursos más grande

1. Cree una nueva consulta en la base de datos **maestra** del servidor

    ![Nueva consulta en Master](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![Nueva consulta en Master1](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. Cree un inicio de sesión y un usuario para el servidor

    ```sql
    CREATE LOGIN XLRCLOGIN WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

3. Cree un usuario de base de datos basado en el inicio de sesión del servidor
    ```sql
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

4. Conceda al usuario el control de la base de datos
    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > Si el nombre de la base de datos contiene guiones asegúrese de incluirlo entre corchetes. 
    >

5. Agregue el usuario de la base de datos al rol de la clase de recurso **xlargerc**
    ```sql
    EXEC sp_addrolememeber 'xlargerc', 'LoadingUser';
    ```

6. Inicie sesión en la base de datos con las nuevas credenciales

    ![Inicie sesión con el nuevo inicio de sesión](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="loading-data"></a>Carga de datos

### <a name="defining-external-data"></a>Definición de datos externos
1. Defina un origen de datos externo

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
    TYPE = Hadoop
    , LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```


2. Definición de los formatos de archivo externos

    El comando ```CREATE EXTERNAL FILE FORMAT``` se utiliza para especificar el formato de los datos externos que se van a cargar. Para los datos de taxis públicos de Nueva York, hemos utilizado los dos formatos para almacenar los datos en Azure Blob Storage

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = ','
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = '|'
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

3.  Cree un esquema para el formato de archivo externo

    ```sql
    CREATE SCHEMA ext;
    GO
    ```

4. Creación de la tablas externas Estas tablas hacen referencia a los datos almacenados en HDFS o Azure Blob Storage. 

    ```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
    (
    [DateID] int NOT NULL,
    [Date] datetime NULL,
    [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FirstDayOfMonth] date NULL,
    [LastDayOfMonth] date NULL,
    [FirstDayOfQuarter] date NULL,
    [LastDayOfQuarter] date NULL,
    [FirstDayOfYear] date NULL,
    [LastDayOfYear] date NULL,
    [IsHolidayUSA] bit NULL,
    [IsWeekday] bit NULL,
    [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Date'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    CREATE EXTERNAL TABLE [ext].[Geography]
    (
    [GeographyID] int NOT NULL,
    [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Geography'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0 
    )
    ;
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
    [HackneyLicenseID] int NOT NULL,
    [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'HackneyLicense'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
    [MedallionID] int NOT NULL,
    [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Medallion'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Time]
    (
    [TimeID] int NOT NULL,
    [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HourNumber] tinyint NOT NULL,
    [MinuteNumber] tinyint NOT NULL,
    [SecondNumber] tinyint NOT NULL,
    [TimeInSecond] int NOT NULL,
    [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [DayTimeBucketGroupKey] int NOT NULL,
    [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
    LOCATION = 'Time'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Trip]
    (
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
    )
    WITH
    (
    LOCATION = 'Trip2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = compressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
    [DateID] int NOT NULL,
    [GeographyID] int NOT NULL,
    [PrecipitationInches] float NOT NULL,
    [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
    LOCATION = 'Weather2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    ```

### <a name="create-table-as-select-ctas"></a>CREATE TABLE AS SELECT (CTAS)

5. Cargue los datos de tablas externas en la instancia de SQL Data Warehouse. 
    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

    > [!NOTE]
    > Está cargando varios gigabytes de datos y comprimiéndolos en índices de almacén de columnas en clúster. Ejecute la siguiente consulta DMV y, a continuación, tómese un café o algún aperitivo mientras Azure SQL Data Warehouse se encarga del trabajo duro.
    >

6. Cree una nueva consulta y observe cómo aparecen los datos mediante esta vista de administración dinámica (DMV)

    ```sql
    SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024 as gb_processed
    FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
    on r.request_id = s.request_id
    WHERE
    r.[label] = 'CTAS : Load [dbo].[Date]' OR
    r.[label] = 'CTAS : Load [dbo].[Geography]' OR
    r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
    r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
    r.[label] = 'CTAS : Load [dbo].[Time]' OR
    r.[label] = 'CTAS : Load [dbo].[Weather]' OR
    r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
    r.command,
    s.request_id,
    r.status
    ORDER BY
    nbr_files desc, gb_processed desc;
    ```

7. Vea todas las consultas del sistema

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

8. Disfrute con la visión de la carga correcta de los datos en Azure SQL Data Warehouse

    ![Consulte los datos cargados](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)



## <a name="querying-data"></a>Consulta de los datos 

### <a name="scan-query-with-scaling"></a>Detección de consultas con escalado

Obtenga una idea clara acerca de cómo afecta el escalado a la velocidad de las consultas.

Antes de comenzar, vamos a reducir verticalmente las operaciones hasta 100 DWU para que podamos obtener una idea del rendimiento de un nodo de proceso por sí mismo.

1. Vaya al portal y seleccione la instancia de SQL Data Warehouse

2. Seleccione la escala en la hoja SQL Data Warehouse. 

    ![Escalado de Data Warehouse desde el portal](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. Reduzca el rendimiento mediante la barra a 100 DWU y pulse Guardar.

    ![Escalar y guardar](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. Espere a que finalice su operación de escalado.

    > [!NOTE]
    > Tenga en cuenta que las operaciones de escalado **terminarán** con las consultas que se están ejecutando en este momento e impedirá la ejecución de otras nuevas.
    >
    
5. Realice un examen de los datos de los trayectos y seleccione el primer millón de entradas de todas las columnas. Si desea avanzar rápidamente, seleccione menos filas.

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```

Tome nota del tiempo que tardó en ejecutar esta operación.

6. Escale verticalmente su instancia a 400 DWU. Tenga en cuenta que cada 100 DWU se agrega otro nodo de proceso a Azure SQL Data Warehouse.

7. Ejecute la consulta de nuevo. Debe observar una diferencia significativa. 

> [!NOTE]
> Azure SQL Data Warehouse es una plataforma de procesamiento paralelo masivo (MPP). Las consultas y operaciones que puedan distribuir el trabajo en paralelo entre varios nodos experimentarán la principal ventaja de Azure SQL Data Warehouse.
>

### <a name="join-query-with-statistics"></a>Combinación de consultas con estadísticas

1. Ejecute una consulta que combine la tabla Date (Fecha) con la tabla Trip (Trayecto)

    ```sql
    SELECT TOP (1000000) dt.[DayOfWeek]
    ,tr.[MedallionID]
    ,tr.[HackneyLicenseID]
    ,tr.[PickupTimeID]
    ,tr.[DropoffTimeID]
    ,tr.[PickupGeographyID]
    ,tr.[DropoffGeographyID]
    ,tr.[PickupLatitude]
    ,tr.[PickupLongitude]
    ,tr.[PickupLatLong]
    ,tr.[DropoffLatitude]
    ,tr.[DropoffLongitude]
    ,tr.[DropoffLatLong]
    ,tr.[PassengerCount]
    ,tr.[TripDurationSeconds]
    ,tr.[TripDistanceMiles]
    ,tr.[PaymentType]
    ,tr.[FareAmount]
    ,tr.[SurchargeAmount]
    ,tr.[TaxAmount]
    ,tr.[TipAmount]
    ,tr.[TollsAmount]
    ,tr.[TotalAmount]
    FROM [dbo].[Trip] as tr
    join
    dbo.[Date] as dt
    on tr.DateID = dt.DateID
    ```

    Como cabría esperar, la consulta tarda mucho más tiempo si distribuye aleatoriamente los datos entre los nodos, especialmente en un escenario de combinación similar a este.

2. Veamos cómo difiere esto a la hora de crear estadísticas en la columna que vamos a combinar ejecutando lo siguiente:

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

    > [!NOTE]
    > SQL Data Warehouse no administra automáticamente las estadísticas para usted. Las estadísticas son importantes para el rendimiento de las consultas, por lo que se recomienda encarecidamente crearlas y actualizarlas.
    > 
    > **Sacará el máximo provecho con las estadísticas en columnas relacionadas con combinaciones, columnas que se usan en la cláusula WHERE y columnas de GROUP BY.**
    >

3. Ejecute de nuevo la consulta desde los requisitos previos y observe las diferencias de rendimiento. Aunque las diferencias en el rendimiento de las consultas no serán tan importantes como con el escalado vertical, debería notar un aumento de la velocidad de manera perceptible. 

## <a name="next-steps"></a>Pasos siguientes

Ahora está listo para realizar consultas y explorar. Consulte nuestras mejores prácticas o sugerencias.

Si ha terminado de explorar por hoy, asegúrese de pausar la instancia. En un entorno de producción, puede experimentar grandes ahorros si realiza el pausado y escalado adecuados para sus necesidades empresariales.

![Pausar](./media/sql-data-warehouse-get-started-tutorial/pause.png)

## <a name="useful-readings"></a>Lecturas útiles

[Simultaneidad y administración de cargas de trabajo]

[Procedimientos recomendados para Almacenamiento de datos SQL de Azure]

[Supervisión de consultas]

[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse] (Los 10 mejores procedimientos para compilar un almacén de datos relacionales a gran escala)

[Migración de datos a Azure SQL Data Warehouse]


[Simultaneidad y administración de cargas de trabajo]: sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example
[Procedimientos recomendados para Almacenamiento de datos SQL de Azure]: sql-data-warehouse-best-practices.md#hash-distribute-large-tables
[Supervisión de consultas]: sql-data-warehouse-manage-monitor.md
[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2013/09/16/top-10-best-practices-for-building-a-large-scale-relational-data-warehouse/ (Los 10 mejores procedimientos para compilar un almacén de datos relacionales a gran escala)
[Migración de datos a Azure SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/



[!INCLUDE [Additional Resources](../../includes/sql-data-warehouse-article-footer.md)]

<!-- Internal Links -->
[requisitos previos]: sql-data-warehouse-get-started-tutorial.md#prerequisites

<!--Other Web references-->
[Visual Studio]: https://www.visualstudio.com/
[SQL Server Management Studio]: https://msdn.microsoft.com/en-us/library/mt238290.aspx



<!--HONumber=Jan17_HO2-->


