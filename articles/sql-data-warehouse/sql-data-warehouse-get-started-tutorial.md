---
title: 'Azure SQL Data Warehouse: tutorial introductorio | Microsoft Docs'
description: "Este tutorial le enseña cómo aprovisionar y cargar datos en Azure SQL Data Warehouse. También aprenderá los conceptos básicos sobre el escalado, la pausa y la optimización."
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
ms.custom: quickstart
ms.date: 01/26/2017
ms.author: elbutter;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9d3029817cae6570ff8871fbcb068250544595d7
ms.contentlocale: es-es
ms.lasthandoff: 03/21/2017


---
# <a name="get-started-with-sql-data-warehouse"></a>Introducción a SQL Data Warehouse

Este tutorial muestra cómo aprovisionar y cargar datos en Azure SQL Data Warehouse. También aprenderá los conceptos básicos sobre el escalado, la pausa y la optimización. Cuando haya terminado, estará preparado para consultar y explorar su almacenamiento de datos.

**Tiempo estimado para completarlo:** se trata de un tutorial completo que incluye código de ejemplo; se tarda aproximadamente 30 minutos en completarlo una vez cumplidos los requisitos previos. 

## <a name="prerequisites"></a>Requisitos previos

En el tutorial se da por hecho que está familiarizado con los conceptos básicos de SQL Data Warehouse. Si necesita una introducción, consulte [¿Qué es SQL Data Warehouse?](sql-data-warehouse-overview-what-is.md) 

### <a name="sign-up-for-microsoft-azure"></a>Suscribirse a Microsoft Azure
Si no dispone de ninguna cuenta de Microsoft Azure, deberá registrarse para utilizar este servicio. Si ya tiene una cuenta, puede omitir este paso. 

1. Vaya a las páginas de cuentas [https://azure.microsoft.com/account/](https://azure.microsoft.com/account/)
2. Cree una cuenta de Azure gratis o compre una cuenta.
3. Siga las instrucciones.

### <a name="install-appropriate-sql-client-drivers-and-tools"></a>Instalación de las herramientas y los controladores del cliente SQL adecuados

La mayoría de las herramientas del cliente SQL se pueden conectar a SQL Data Warehouse mediante JDBC, ODBC o ADO.NET. Debido al gran número de características de T-SQL que admite SQL Data Warehouse, algunas aplicaciones cliente no son totalmente compatibles con SQL Data Warehouse.

Si está ejecutando un sistema operativo Windows, se recomienda usar [Visual Studio] o [SQL Server Management Studio].

[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-a-sql-data-warehouse"></a>Creación de Almacenamiento de datos SQL

SQL Data Warehouse es un tipo especial de base de datos diseñado para el procesamiento paralelo masivo. La base de datos se distribuye en varios nodos y procesa las consultas en paralelo. SQL Data Warehouse tiene un nodo de control que orquesta las actividades de todos los nodos. Los propios nodos usan SQL Database para administrar los datos.  

> [!NOTE]
> La creación de una instancia de Almacenamiento de datos SQL puede dar lugar a un nuevo servicio facturable.  Para más información, consulte [Precios de Azure SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>

### <a name="create-a-data-warehouse"></a>Creación del almacenamiento de datos

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Nuevo** > **Bases de datos** > **SQL Data Warehouse**.

    ![NewBlade](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![SelectDW](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. Rellene los detalles de la implementación

    **Nombre de la base de datos**: elija el que desee. Si tiene varios almacenamientos de datos, es aconsejable que los nombres incluyan detalles como su región, entorno, etc.; por ejemplo, *mydw-westus-1-test*.

    **Suscripción**: su suscripción a Azure

    **Grupo de recursos**: cree un grupo de recursos o seleccione uno existente.
    > [!NOTE]
    > Los grupos de recursos son útiles para realizar tareas de administración de recursos como el control de acceso y la implementación de plantillas. Consulte más información sobre los grupos de recursos de Azure [aquí](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups).

    **Origen**: base de datos en blanco

    **Servidor**: seleccione el servidor que creó en los [requisitos previos].

    **Intercalación**: mantenga la intercalación predeterminada: SQL_Latin1_General_CP1_CI_AS.

    **Select performance** (Seleccionar rendimiento): se recomienda mantener el estándar 400DWU.

4. Seleccione **Anclar al panel**
    ![Anclar al panel](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png)

5. Póngase cómodo y espere a que el almacenamiento de datos se implemente. Lo normal es que este proceso tarde varios minutos. El portal le avisa cuando está el almacenamiento de datos está listo para usarse. 

## <a name="connect-to-sql-data-warehouse"></a>Conexión a Almacenamiento de datos SQL

Este tutorial usa SQL Server Management Studio (SSMS) para conectarse al almacenamiento de datos. Puede conectarse a SQL Data Warehouse a través de los siguientes conectores compatibles: ADO.NET, JDBC, ODBC y PHP. Recuerde que la funcionalidad podría ser limitada para herramientas no compatibles con Microsoft.


### <a name="get-connection-information"></a>Obtención de información sobre la conexión

Para conectarse a su almacenamiento de datos, debe conectarse mediante el servidor lógico de SQL Server que creó en los [requisitos previos].

1. Seleccione su almacenamiento de datos en el panel o búsquelo en los recursos.

    ![Panel de SQL Data Warehouse](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. Busque el nombre completo del servidor lógico de SQL Server.

    ![Seleccionar nombre del servidor](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. Abra SSMS y utilice el explorador de objetos para conectarse a este servidor con las credenciales del administrador del servidor que creó en los [requisitos previos].

    ![Conectarse con SSMS](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

Si todo funciona correctamente, ahora deberá estar conectado al servidor lógico de SQL Server. Puesto que ha iniciado sesión como administrador del servidor, puede conectarse a cualquier base de datos hospedada por el servidor, incluida la base de datos maestra. 

Solo hay una cuenta de administrador en el servidor, y tiene más privilegios de cualquier usuario. Tenga cuidado de no permitir que demasiadas personas de su organización conozcan la contraseña de administrador. 

También puede tener una cuenta de administrador de Azure Active Directory, pero no proporcionamos los detalles aquí. Si desea más información acerca del uso de autenticación de Azure Active Directory, consulte [Autenticación de Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).

A continuación, veremos la creación de usuarios e inicios de sesión adicionales.


## <a name="create-a-database-user"></a>Creación de un usuario de base de datos

En este paso, creará una cuenta de usuario para acceder a su almacenamiento de datos. También le mostramos cómo otorgar a ese usuario la capacidad de ejecutar consultas con una gran cantidad de memoria y recursos de CPU.

### <a name="notes-about-resource-classes-for-allocating-resources-to-queries"></a>Notas acerca de las clases de recursos para asignar recursos a las consultas

- Para mantener los datos seguros, no use la cuenta de administrador del servidor para ejecutar consultas en las bases de datos de producción. Tiene más privilegios que cualquier usuario y usarlo para realizar operaciones en los datos de usuario pone los datos en peligro. Además, puesto que el administrador del servidor se ha diseñado para realizar operaciones de administración, ejecuta operaciones con solo una pequeña asignación de memoria y recursos de CPU. 

- SQL Data Warehouse usa roles de base de datos predefinidos, llamados clases de recursos, para asignar diferentes cantidades de memoria, recursos de CPU y espacios de simultaneidad a los usuarios. Cada usuario puede pertenecer a una clase de recursos pequeña, mediana, grande o extragrande. La clase de recurso del usuario determina los recursos que tiene el usuario para ejecutar consultas y operaciones de carga.

- Para lograr una compresión de datos óptima, es posible que el usuario necesite realizar cargas con asignaciones de recursos grandes o extragrandes. Obtenga más información sobre las clases de recursos [aquí](./sql-data-warehouse-develop-concurrency.md#resource-classes):

### <a name="create-an-account-that-can-control-a-database"></a>Creación de una cuenta que puede controlar una base de datos

Puesto que ha iniciado sesión como administrador del servidor, tiene permisos para crear inicios de sesión y usuarios.

1. Con SSMS u otro cliente de consulta, abra una nueva consulta para **master**.

    ![Nueva consulta en Master](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![Nueva consulta en Master1](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. En la ventana de consulta, ejecute este comando T-SQL para crear un inicio de sesión llamado MedRCLogin y un usuario llamado LoadingUser. Este inicio de sesión puede conectarse al servidor lógico de SQL Server.

    ```sql
    CREATE LOGIN MedRCLogin WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN MedRCLogin;
    ```

3. Ahora, consultando la *base de datos de SQL Data Warehouse*, cree un usuario de base de datos basado en el inicio de sesión que creó para acceder a la base de datos y realizar operaciones en ella.

    ```sql
    CREATE USER LoadingUser FOR LOGIN MedRCLogin;
    ```

4. Conceda permisos de control de usuario para la base de datos NYT. 

    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > Si el nombre de la base de datos contiene guiones asegúrese de incluirlo entre corchetes. 
    >

### <a name="give-the-user-medium-resource-allocations"></a>Asignaciones de recursos de tamaño medio al usuario

1. Ejecute este comando T-SQL para convertirlo en miembro de la clase de recursos de tamaño medio, denominada mediumrc. 

    ```sql
    EXEC sp_addrolemember 'mediumrc', 'LoadingUser';
    ```
    > [!NOTE]
    > Haga clic [aquí](sql-data-warehouse-develop-concurrency.md#resource-classes) para más información sobre las clases de simultaneidad y recursos. 
    >

2. Conexión al servidor lógico con las nuevas credenciales

    ![Inicie sesión con el nuevo inicio de sesión](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="load-data-from-azure-blob-storage"></a>Carga de datos del almacenamiento de blobs de Azure

Ahora está listo para cargar datos en el almacenamiento de datos. Este paso muestra cómo cargar datos de los taxis de Nueva York procedentes de una instancia de Azure Storage Blob público. 

- Una forma habitual de cargar datos en SQL Data Warehouse es mover los datos a Azure Blob Storage y, después, cargarlos en su almacenamiento de datos. Para que sea más fácil entender cómo se realiza la carga, tenemos datos de los taxis de Nueva York ya hospedados en una instancia de Azure Storage Blob público. 

- Para consultas futuras y aprender cómo obtener los datos en Azure Blob Storage o cómo cargarlos directamente desde el origen en SQL Data Warehouse, consulte la [introducción a la carga](sql-data-warehouse-overview-load.md).


### <a name="define-external-data"></a>Definición de datos externos

1. Cree una clave maestra. Solo necesita crear una clave maestra una vez para cada base de datos. 

    ```sql
    CREATE MASTER KEY;
    ```

2. Defina la ubicación del blob de Azure que contiene los datos de los taxis.  

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
        TYPE = Hadoop,
        LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```

3. Definición de los formatos de archivo externos

    El comando ```CREATE EXTERNAL FILE FORMAT``` se utiliza para especificar el formato de los archivos que contienen los datos externos. Dichos archivos contienen texto separado por uno o más caracteres, denominados delimitadores. Con fines de demostración, los datos de los taxis se almacenan como datos sin comprimir y como datos comprimidos con gzip.

    Ejecute estos comandos T-SQL para definir dos formatos diferentes: sin comprimir y comprimido.

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( 
            FIELD_TERMINATOR = ',',
            STRING_DELIMITER = '',
            DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH ( 
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = '|',
            STRING_DELIMITER = '',
        DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        ),
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

4.  Cree un esquema para el formato de archivos externos. 

    ```sql
    CREATE SCHEMA ext;
    ```
5. Creación de la tablas externas Estas tablas hacen referencia a los datos almacenados en Azure Blob Storage. Ejecute los siguientes comandos T-SQL para crear varias tablas externas que apuntan al blob de Azure que hemos definido previamente en nuestro origen de datos externo.

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
        LOCATION = 'Date',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    
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
        LOCATION = 'Geography',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0 
    );
        
    
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'HackneyLicense',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
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
        LOCATION = 'Medallion',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
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
        LOCATION = 'Time',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
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
        LOCATION = 'Trip2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = compressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
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
        LOCATION = 'Weather2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
```

### <a name="import-the-data-from-azure-blob-storage"></a>Importe los datos desde Azure Blob Storage.

SQL Data Warehouse admite una instrucción clave denominada CREATE TABLE AS SELECT (CTAS). Esta instrucción crea una nueva tabla en función de los resultados de una instrucción select. La nueva tabla tiene las mismas columnas y los mismos tipos de datos que los resultados de la instrucción select.  Es una manera elegante de importar datos de Azure Blob Storage en SQL Data Warehouse.

1. Ejecute este script para importar los datos.

    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    
    CREATE TABLE [dbo].[Geography]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    
    CREATE TABLE [dbo].[Medallion]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    
    CREATE TABLE [dbo].[Time]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    
    CREATE TABLE [dbo].[Weather]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    
    CREATE TABLE [dbo].[Trip]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

2. Consulte los datos mientras se carga.

   Está cargando varios gigabytes de datos y comprimiéndolos en índices de almacén de columnas en clúster de alto rendimiento. Ejecute la siguiente consulta, que usa vistas de administración dinámica (DMV) para mostrar el estado de la carga. Después de iniciar la consulta, tómese un café y coma algo mientras SQL Data Warehouse hace el trabajo duro.
    
    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024/1024 as gb_processed
    FROM 
        sys.dm_pdw_exec_requests r
        INNER JOIN sys.dm_pdw_dms_external_work s
        ON r.request_id = s.request_id
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
        nbr_files desc, 
        gb_processed desc;
    ```

3. Consulte todas las consultas del sistema.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Disfrute viendo cómo los datos se cargan ordenadamente en Azure SQL Data Warehouse.

    ![Consulte los datos cargados](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)


## <a name="improve-query-performance"></a>Mejora del rendimiento de las consultas

Hay varias maneras de mejorar el rendimiento de las consultas y de lograr el rendimiento de alta velocidad que el diseño de SQL Data Warehouse ofrece.  

### <a name="see-the-effect-of-scaling-on-query-performance"></a>Visualización del efecto del escalado en el rendimiento de las consultas 

Una manera de mejorar el rendimiento de las consultas es escalar los recursos cambiando el nivel de servicio de DWU para el almacenamiento de datos. Cada nivel de servicio cuesta más que el anterior, pero se puede reducir la escala o pausar recursos en cualquier momento. 

En este paso se compara el rendimiento de dos configuraciones de DWU distintas.

Primero vamos a reducir verticalmente hasta 100 DWU para que hacernos una idea del rendimiento de los nodos de proceso por separado.

1. Vaya al portal y seleccione la instancia de SQL Data Warehouse.

2. Seleccione la escala en la hoja SQL Data Warehouse. 

    ![Escalado de Data Warehouse desde el portal](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. Reduzca el rendimiento mediante la barra a 100 DWU y pulse Guardar.

    ![Escalar y guardar](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. Espere a que finalice su operación de escalado.

    > [!NOTE]
    > No se pueden ejecutar consultas mientras se cambia la escala. El escalado **elimina** las consultas actualmente en ejecución. Puede reiniciarlas una vez finalizada la operación.
    >
    
5. Realice un examen de los datos de los trayectos y seleccione el primer millón de entradas de todas las columnas. Si desea avanzar rápidamente, seleccione menos filas. Tome nota del tiempo que tarda esta operación en ejecutarse.

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```
6. Escale el almacenamiento de datos de nuevo a 400 DWU. Tenga en cuenta que cada 100 DWU se agrega otro nodo de proceso a Azure SQL Data Warehouse.

7. Ejecute la consulta de nuevo. Debe observar una diferencia significativa. 

> [!NOTE]
> Como SQL Data Warehouse utiliza el procesamiento paralelo masivo, las consultas que examinen o realicen funciones analíticas en millones de filas experimentarán la auténtica potencia de Azure SQL Data Warehouse.
>

### <a name="see-the-effect-of-statistics-on-query-performance"></a>Visualización del efecto de las estadísticas en el rendimiento de las consultas

1. Ejecute una consulta que combine la tabla Date (Fecha) con la tabla Trip (Trayecto)

    ```sql
    SELECT TOP (1000000) 
        dt.[DayOfWeek],
        tr.[MedallionID],
        tr.[HackneyLicenseID],
        tr.[PickupTimeID],
        tr.[DropoffTimeID],
        tr.[PickupGeographyID],
        tr.[DropoffGeographyID],
        tr.[PickupLatitude],
        tr.[PickupLongitude],
        tr.[PickupLatLong],
        tr.[DropoffLatitude],
        tr.[DropoffLongitude],
        tr.[DropoffLatLong],
        tr.[PassengerCount],
        tr.[TripDurationSeconds],
        tr.[TripDistanceMiles],
        tr.[PaymentType],
        tr.[FareAmount],
        tr.[SurchargeAmount],
        tr.[TaxAmount],
        tr.[TipAmount],
        tr.[TollsAmount],
        tr.[TotalAmount]
    FROM [dbo].[Trip] as tr
        JOIN dbo.[Date] as dt
        ON  tr.DateID = dt.DateID
    ```

    Esta consulta tarda algún tiempo, ya que SQL Data Warehouse tiene que distribuir aleatoriamente los datos para la combinación. Las combinaciones no necesitan la distribución aleatoria de los datos si están diseñadas para unirlos de la misma manera en la que se distribuyen. Esto es un asunto más complicado. 

2. Las estadísticas marcan la diferencia. 
3. Ejecute esta instrucción para crear estadísticas en las columnas de combinación.

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

    > [!NOTE]
    > SQL Data Warehouse no administra automáticamente las estadísticas para usted. Las estadísticas son importantes para el rendimiento de las consultas, por lo que se recomienda encarecidamente crearlas y actualizarlas.
    > 
    > **Sacará el máximo provecho con las estadísticas en columnas relacionadas con combinaciones, columnas que se usan en la cláusula WHERE y columnas de GROUP BY.**
    >

3. Ejecute de nuevo la consulta desde los requisitos previos y observe las diferencias de rendimiento. Aunque las diferencias de rendimiento de las consultas no serán tan importantes como con el escalado vertical, notará un aumento de la velocidad. 

## <a name="next-steps"></a>Pasos siguientes

Ahora está listo para realizar consultas y explorar. Consulte nuestras mejores prácticas o sugerencias.

Si ha terminado de explorar por hoy, asegúrese de pausar la instancia. En un entorno de producción, puede experimentar grandes ahorros si realiza el pausado y escalado adecuados para sus necesidades empresariales.

![Pausar](./media/sql-data-warehouse-get-started-tutorial/pause.png)

## <a name="useful-readings"></a>Lecturas útiles

[Simultaneidad y administración de cargas de trabajo][]

[Procedimientos recomendados para Almacenamiento de datos SQL de Azure][]

[Supervisión de consultas][]

[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse][] (Los 10 mejores procedimientos para compilar un almacén de datos relacionales a gran escala)

[Migración de datos a Azure SQL Data Warehouse][]

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

