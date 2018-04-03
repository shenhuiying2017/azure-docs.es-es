---
title: 'Tutorial: Realización de operaciones de ETL mediante Azure Databricks | Microsoft Docs'
description: Aprenda a extraer datos de Data Lake Store a Azure Databricks, transformarlos y luego cargarlos en Azure SQL Data Warehouse.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.custom: mvc
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: c3aa87f2c74175d1b61a8db6a9c7a0318a408658
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-extract-transform-and-load-data-using-azure-databricks"></a>Tutorial: Extracción, transformación y carga de datos mediante Azure Databricks

En este tutorial, realizará una operación de ETL (extracción, transformación y carga de datos) mediante Azure Databricks. Extraerá datos de Azure Data Lake Store a Azure Databricks, donde ejecutará transformaciones en ellos y, luego, cargará los datos transformados en Azure SQL Data Warehouse. 

En los pasos de este tutorial se usa el conector SQL Data Warehouse para Azure Databricks para transferir los datos a Azure Databricks. Este conector usa, a su vez, Azure Blob Storage como almacenamiento temporal para los datos transferidos entre un clúster de Azure Databricks y Azure SQL Data Warehouse.

La siguiente ilustración muestra el flujo de la aplicación:

![Azure Databricks con Data Lake Store y SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks with Data Lake Store and SQL Data Warehouse")

En este tutorial se describen las tareas siguientes: 

> [!div class="checklist"]
> * Creación de un área de trabajo de Azure Databricks
> * Creación de un clúster de Spark en Azure Databricks
> * Creación de una cuenta de Almacén de Azure Data Lake
> * Carga de datos en Azure Data Lake Store
> * Creación de un cuaderno en Azure Databricks
> * Extracción de datos de Data Lake Store
> * Transformación de datos en Azure Databricks
> * Carga de datos en Azure SQL Data Warehouse

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>requisitos previos

Antes de comenzar este tutorial, asegúrese de que se cumplen los requisitos siguientes:
- Ha creado una instancia de Azure SQL Data Warehouse y una regla de firewall de nivel de servidor y se ha conectado al servidor como administrador. Siga las instrucciones que se indican en [Guía de inicio rápido: Creación de una instancia de Azure SQL Data Warehouse](../sql-data-warehouse/create-data-warehouse-portal.md).
- Ha creado una clave maestra de base de datos para la instancia de Azure SQL Data Warehouse. Siga las instrucciones que se indican en [Crear la clave maestra de una base de datos](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
- Ha creado una cuenta de Azure Blob Storage y, dentro de ella, un contenedor. Además, ha recuperado la clave de acceso para acceder a la cuenta de almacenamiento. Siga las instrucciones que se indican en [Guía de inicio rápido: Creación de una cuenta de Azure Blob Storage](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Creación de un área de trabajo de Azure Databricks

En esta sección, creará un área de trabajo de Azure Databricks mediante Azure Portal. 

1. En Azure Portal, seleccione **Crear un recurso** > **Datos y análisis** > **Azure Databricks**. 

    ![Databricks en Azure Portal](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Databricks en Azure Portal")

3. En **Azure Databricks Service**, proporcione los valores para crear un área de trabajo de Databricks.

    ![Creación de un área de trabajo de Azure Databricks](./media/databricks-extract-load-sql-data-warehouse/create-databricks-workspace.png "Creación de un área de trabajo de Azure Databricks")

    Proporcione los valores siguientes: 
     
    |Propiedad  |DESCRIPCIÓN  |
    |---------|---------|
    |**Workspace name** (Nombre del área de trabajo)     | Proporcione un nombre para el área de trabajo de Databricks        |
    |**Suscripción**     | En el cuadro desplegable, seleccione la suscripción de Azure.        |
    |**Grupos de recursos**     | Especifique si desea crear un nuevo grupo de recursos o utilizar uno existente. Un grupo de recursos es un contenedor que almacena los recursos relacionados con una solución de Azure. Para más información, consulte [Información general del grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Ubicación**     | Seleccione **Este de EE. UU. 2**. Para otras regiones disponibles, consulte [Productos disponibles por región](https://azure.microsoft.com/regions/services/).        |
    |**Plan de tarifa**     |  Elija entre **Standard** o **Premium**. Para más información sobre estos planes, consulte la [página de precios de Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Seleccione **Anclar al panel** y, después, seleccione **Crear**.

4. La cuenta tarda unos minutos en crearse. Durante la creación de la cuenta, el portal muestra el icono **Enviando implementación para Azure Databricks** a la derecha. Puede que deba desplazarse a la derecha del panel para ver el icono. También hay una barra de progreso que se muestra en la parte superior de la pantalla. Puede ver cualquier área para el progreso.

    ![Icono de implementación de Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-deployment-tile.png "Databricks deployment tile")

## <a name="create-a-spark-cluster-in-databricks"></a>Creación de un clúster de Spark en Databricks

1. En Azure Portal, vaya al área de trabajo de Databricks que ha creado y, después, seleccione **Launch Workspace** (Iniciar área de trabajo).

2. Se le redirigirá al portal de Azure Databricks. En el portal, seleccione **Cluster**.

    ![Databricks en Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks en Azure")

3. En la página **Nuevo clúster**, proporcione los valores para crear un clúster.

    ![Creación de un clúster de Databricks Spark en Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Creación de un clúster de Databricks Spark en Azure")

    Acepte los demás valores predeterminados, salvo los siguientes:

    * Escriba un nombre para el clúster.
    * Para este artículo, cree un clúster con el entorno en tiempo de ejecución **4.0**. 
    * Asegúrese de que selecciona la casilla **Terminate after ____ minutes of inactivity** (Terminar después de ____ minutos de inactividad). Proporcione una duración (en minutos) para terminar el clúster, si este no se usa.
    
    Seleccione **Create cluster** (Crear clúster). Una vez que el clúster se está ejecutando, puede asociarle notebooks y ejecutar trabajos de Spark.

## <a name="create-an-azure-data-lake-store-account"></a>Creación de una cuenta de Almacén de Azure Data Lake

En esta sección, creará una cuenta de Azure Data Lake Store y le asociará una entidad de servicio de Azure Active Directory. Más adelante en este tutorial, usará esta entidad de servicio en Azure Databricks para acceder a Azure Data Lake Store. 

1. En [Azure Portal](https://portal.azure.com), seleccione **Crear un recurso** > **Storage** > **Data Lake Store**.
3. En la hoja **Nuevo Data Lake Store**, proporcione los valores como se muestra en la captura de pantalla siguiente:
   
    ![Creación de una nueva cuenta de Azure Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/create-new-datalake-store.png "Creación de una nueva cuenta de Azure Data Lake")

    Proporcione los valores siguientes: 
     
    |Propiedad  |DESCRIPCIÓN  |
    |---------|---------|
    |**Name**     | Escriba un nombre único para la cuenta de Data Lake Store.        |
    |**Suscripción**     | En el cuadro desplegable, seleccione la suscripción de Azure.        |
    |**Grupos de recursos**     | En este tutorial, seleccionará el mismo grupo de recursos que usó al crear el área de trabajo Azure Databricks.  |
    |**Ubicación**     | Seleccione **Este de EE. UU. 2**.  |
    |**Paquete de precios**     |  Seleccione **Pago por uso**. |
    | **Configuración de cifrado**. | Mantenga la configuración predeterminada. |

    Seleccione **Anclar al panel** y, después, seleccione **Crear**.

Ahora creará una entidad de servicio de Azure Active Directory y la asociará a la cuenta de Data Lake Store.

### <a name="create-an-azure-active-directory-service-principal"></a>Creación de una entidad de servicio de Azure Active Directory
   
1. En [Azure Portal](https://portal.azure.com), seleccione **Todos los servicios** y, luego, **Azure Active Directory**.

2. Seleccione **App registrations** (Registros de aplicaciones).

   ![seleccionar registros de aplicaciones](./media/databricks-extract-load-sql-data-warehouse/select-app-registrations.png)

3. Seleccione **Nuevo registro de aplicaciones**.

   ![agregar aplicación](./media/databricks-extract-load-sql-data-warehouse/select-add-app.png)

4. Proporcione un nombre y una dirección URL para la aplicación. Seleccione **Aplicación web o API** para indicar el tipo de aplicación que desea crear. Proporcione una dirección URL de inicio de sesión y, a continuación, seleccione **Crear**.

   ![aplicación de nombre](./media/databricks-extract-load-sql-data-warehouse/create-app.png)

Para acceder a la cuenta de Data Lake Store desde Azure Databricks, debe tener los siguientes valores para la entidad de servicio de Azure Active Directory que creó:
- Identificador de aplicación
- Clave de autenticación
- Id. de inquilino

En las secciones siguientes, recuperará estos valores para la entidad de servicio de Azure Active Directory que creó anteriormente.

### <a name="get-application-id-and-authentication-key-for-the-service-principal"></a>Obtención del identificador de aplicación y la clave de autenticación de la entidad de servicio

Al iniciar sesión mediante programación, necesitará el identificador de la aplicación y una clave de autenticación. Para obtener estos valores, use los pasos siguientes:

1. En **Registros de aplicaciones**, en Azure Active Directory, seleccione su aplicación.

   ![seleccionar aplicación](./media/databricks-extract-load-sql-data-warehouse/select-app.png)

2. Copie el **id. de aplicación** y almacénelo en el código de la aplicación. En algunas [aplicaciones de ejemplo](#log-in-as-the-application), este valor se conoce como identificador de cliente.

   ![Identificador de cliente](./media/databricks-extract-load-sql-data-warehouse/copy-app-id.png)

3. Para generar una clave de autenticación, seleccione **Configuración**.

   ![Seleccionar Configuración](./media/databricks-extract-load-sql-data-warehouse/select-settings.png)

4. Para generar una clave de autenticación, seleccione **Claves**.

   ![seleccionar claves](./media/databricks-extract-load-sql-data-warehouse/select-keys.png)

5. Proporcione una descripción de la clave y una duración. Cuando haya terminado, seleccione **Guardar**.

   ![guardar clave](./media/databricks-extract-load-sql-data-warehouse/save-key.png)

   Después de guardar la clave, se muestra el valor de la clave. Copie este valor porque no podrá recuperarlo más adelante. Proporcione el valor de clave junto con el id. de aplicación para iniciar sesión con la aplicación. Guarde el valor de clave donde la aplicación pueda recuperarlo.

   ![clave guardada](./media/databricks-extract-load-sql-data-warehouse/copy-key.png)

### <a name="get-tenant-id"></a>Obtención del identificador de inquilino

Al iniciar sesión mediante programación, deberá pasar el id. de inquilino con la solicitud de autenticación.

1. Seleccione **Azure Active Directory**.

   ![seleccionar azure active directory](./media/databricks-extract-load-sql-data-warehouse/select-active-directory.png)

1. Para obtener el identificador de inquilino, seleccione **Propiedades** en el inquilino de Azure AD.

   ![Selección de las propiedades de Azure AD](./media/databricks-extract-load-sql-data-warehouse/select-ad-properties.png)

1. Copie el **id. de directorio**. Este valor es el id. de inquilino.

   ![tenant ID](./media/databricks-extract-load-sql-data-warehouse/copy-directory-id.png) 

### <a name="associate-service-principal-with-azure-data-lake-store"></a>Asociación de la entidad de servicio con Azure Data Lake Store

En esta sección, asociará la cuenta de Azure Data Lake Store con la entidad de servicio de Azure Active Directory que creó. De esta forma, se garantiza que puede acceder a la cuenta de Data Lake Store desde Azure Databricks.

1. En [Azure Portal](https://portal.azure.com), seleccione la cuenta de Data Lake Store que creó.

2. En el panel izquierdo, seleccione **Access Control** > **Agregar**.

    ![Agregar acceso a Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/add-adls-access.png "Add Data Lake Store access")

3. En **Agregar permisos**, seleccione un rol que quiera asignar a la entidad de servicio. En este tutorial, seleccione **Propietario**. En **Asignar acceso a**, seleccione **Azure AD, user, group, or application** (Azure AD, usuario, grupo o aplicación). En **Seleccionar**, escriba el nombre de la entidad de servicio que creó para filtrar el número de entidades de servicio que se pueden seleccionar.

    ![Seleccionar la entidad de servicio](./media/databricks-extract-load-sql-data-warehouse/select-service-principal.png "Select service principal")

    Seleccione la entidad de servicio que creó anteriormente y, a continuación, elija **Guardar**. La entidad de servicio ahora está asociada a la cuenta de Azure Data Lake Store.

## <a name="upload-data-to-data-lake-store"></a>Carga de datos en el Almacén Data Lake

En esta sección, cargará un archivo de datos de ejemplo en Data Lake Store. Usará este archivo más adelante en Azure Databricks para ejecutar algunas transformaciones. Los datos de ejemplo (**small_radio_json.json**) que usará en este tutorial están disponibles en este [repositorio de Github](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json).

1. En [Azure Portal](https://portal.azure.com), seleccione la cuenta de Data Lake Store que creó.

2. En la pestaña **Información general**, haga clic en **Explorador de datos**.

    ![Abrir el Explorador de datos](./media/databricks-extract-load-sql-data-warehouse/open-data-explorer.png "Open Data Explorer")

3. En el Explorador de datos, haga clic en **Cargar**.

    ![Opción de carga](./media/databricks-extract-load-sql-data-warehouse/upload-to-data-lake-store.png "Upload option")

4. En **Cargar archivos**, vaya a la ubicación de su archivo de datos de ejemplo y, a continuación, seleccione **Agregar archivos seleccionados**.

    ![Opción de carga](./media/databricks-extract-load-sql-data-warehouse/upload-data.png "Upload option")

5. En este tutorial, ha cargado el archivo de datos en la raíz de Data Lake Store. Por lo tanto, ahora está disponible en `adl://<YOUR_DATA_LAKE_STORE_ACCOUNT_NAME>.azuredatalakestore.net/small_radio_json.json`.

## <a name="extract-data-from-data-lake-store"></a>Extracción de datos de Data Lake Store

En esta sección, creará un cuaderno en el área de trabajo de Azure Databricks y, a continuación, ejecutará fragmentos de código para extraer datos de Data Lake Store en Azure Databricks.

1. En [Azure Portal](https://portal.azure.com), vaya al área de trabajo de Azure Databricks que creó y seleccione **Launch Workspace** (Iniciar área de trabajo).

2. En el panel izquierdo, seleccione **Workspace** (Área de trabajo). En la lista desplegable **Workspace** (Área de trabajo), seleccione **Create** > **Notebook** (Crear > Cuaderno).

    ![Creación de notebooks en Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Creación de notebooks en Databricks")

2. En el cuadro de diálogo **Create Notebook** (Crear cuaderno), escriba un nombre para el cuaderno. Seleccione **Scala** como lenguaje y, a continuación, seleccione el clúster de Spark que creó anteriormente.

    ![Creación de notebooks en Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Creación de notebooks en Databricks")

    Seleccione **Crear**.

3. Agregue el siguiente fragmento de código a una celda de código vacía y reemplace los valores de marcador de posición por los valores que guardó anteriormente para la entidad de servicio de Azure Active Directory.

        spark.conf.set("dfs.adls.oauth2.access.token.provider.type", "ClientCredential")
        spark.conf.set("dfs.adls.oauth2.client.id", "<APPLICATION-ID>")
        spark.conf.set("dfs.adls.oauth2.credential", "<AUTHENTICATION-KEY>")
        spark.conf.set("dfs.adls.oauth2.refresh.url", "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token")

    Presione **MAYÚS + ENTRAR** para ejecutar la celda de código.

4. Ahora puede cargar el archivo JSON de ejemplo en Data Lake Store como trama de datos en Azure Databricks. Agregue el siguiente fragmento de código a una nueva celda de código, sustituya el valor de marcador de posición y, luego, presione **MAYÚS + ENTRAR**.

        val df = spark.read.json("adl://<DATA LAKE STORE NAME>.azuredatalakestore.net/small_radio_json.json")

5. Ejecute el siguiente fragmento de código para ver el contenido de la trama de datos.

        df.show()

    Debe ver una salida similar al siguiente fragmento de código:

        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
        | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
        | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
        ...
        ...

Ahora ha extraído los datos de almacén de Azure Data Lake en Azure Databricks.

## <a name="transform-data-in-azure-databricks"></a>Transformación de datos en Azure Databricks

Los datos de ejemplo sin procesar **small_radio_json.json** capturan la audiencia de una emisora de radio y presentan una variedad de columnas. En esta sección, transformará los datos para recuperar solo columnas específicas del conjunto de datos. 

1. Para comenzar, recupere solo las columnas *firstName*, *lastName*, *gender*, *location* y *level* de la trama de datos que ha creado.

        val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")

    Obtendrá una salida como la que se muestra en el fragmento de código siguiente:

        +---------+----------+------+--------------------+-----+
        |firstname|  lastname|gender|            location|level|
        +---------+----------+------+--------------------+-----+
        | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |     Tess|  Townsend|     F|Nashville-Davidso...| free|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
        |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Tess|  Townsend|     F|Nashville-Davidso...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        +---------+----------+------+--------------------+-----+

2.  Puede transformar aún más estos datos para renombrar la columna **level** como **subscription_type**.

        val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
        renamedColumnsDF.show()

    Obtendrá una salida como la que se muestra en el fragmento de código siguiente:

        +---------+----------+------+--------------------+-----------------+
        |firstname|  lastname|gender|            location|subscription_type|
        +---------+----------+------+--------------------+-----------------+
        | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
        |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        +---------+----------+------+--------------------+-----------------+

## <a name="load-data-into-azure-sql-data-warehouse"></a>Carga de datos en Azure SQL Data Warehouse

En esta sección, cargará los datos transformados en Azure SQL Data Warehouse. Mediante el conector de Azure SQL Data Warehouse para Azure Databricks, puede cargar directamente una trama de datos como una tabla en SQL Data Warehouse.

Como se mencionó anteriormente, el conector de SQL Data Warehouse usa Azure Blob Storage como almacenamiento temporal para cargar datos entre Azure Databricks y Azure SQL Data Warehouse. Por lo tanto, para comenzar, proporcione la configuración para conectarse a la cuenta de almacenamiento. Ya debe haber creado la cuenta como parte de los requisitos previos de este artículo.

1. Proporcione la configuración para acceder a la cuenta de Azure Storage desde Azure Databricks.

        val blobStorage = "<STORAGE ACCOUNT NAME>.blob.core.windows.net"
        val blobContainer = "<CONTAINER NAME>"
        val blobAccessKey =  "<ACCESS KEY>"

2. Especifique una carpeta temporal que se usará al mover datos entre Azure Databricks y Azure SQL Data Warehouse.

        val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"

3. Ejecute el siguiente fragmento de código para almacenar las claves de acceso de Azure Blob Storage en la configuración. De esta forma, se garantiza que no tiene que guardar la clave de acceso en el cuaderno en texto sin formato.

        val acntInfo = "fs.azure.account.key."+ blobStorage
        sc.hadoopConfiguration.set(acntInfo, blobAccessKey)

4. Proporcione los valores para conectarse a la instancia de Azure SQL Data Warehouse. Debe haber creado una instancia de SQL Data Warehouse como parte de los requisitos previos.

        //SQL Data Warehouse related settings
        val dwDatabase = "<DATABASE NAME>"
        val dwServer = "<DATABASE SERVER NAME>" 
        val dwUser = "<USER NAME>"
        val dwPass = "<PASSWORD>"
        val dwJdbcPort =  "1433"
        val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
        val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
        val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass

5. Ejecute el siguiente fragmento de código para cargar la trama de datos transformado, **renamedColumnsDF**, como una tabla en SQL Data Warehouse. Este fragmento de código crea una tabla denominada **SampleTable** en SQL Database.

        spark.conf.set(
          "spark.sql.parquet.writeLegacyFormat",
          "true")
        
        renamedColumnsDF.write
            .format("com.databricks.spark.sqldw")
            .option("url", sqlDwUrlSmall) 
            .option("dbtable", "SampleTable")
            .option( "forward_spark_azure_storage_credentials","True")
            .option("tempdir", tempDir)
            .mode("overwrite")
            .save()

6. Conéctese a la instancia de SQL Database y compruebe que aparece una tabla **SampleTable**.

    ![Comprobar la tabla de ejemplo](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Verify sample table")

7. Ejecute una consulta select para comprobar el contenido de la tabla. Debe tener los mismos datos que la trama de datos **renamedColumnsDF**.

    ![omprobar contenido de tabla de ejemplo](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Verify sample table content")

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado de ejecutar el tutorial, puede finalizar el clúster. Para ello, desde el área de trabajo de Azure Databricks, en el panel izquierdo, seleccione **Clusters** (Clústeres). En el clúster que desea finalizar, mueva el cursor sobre el botón de puntos suspensivos en la columna **Actions** (Acciones) y seleccione el icono **Terminate** (Finalizar).

![Detener un clúster de Databricks](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Stop a Databricks cluster")

Si no finaliza manualmente el clúster, este se detendrá automáticamente si seleccionó la casilla **Terminate after __ minutes of inactivity** (Finalizar después de __ minutos de inactividad) al crear el clúster. En tal caso, el clúster se detiene automáticamente si ha estado inactivo durante el tiempo especificado.

## <a name="next-steps"></a>Pasos siguientes 
En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Creación de un área de trabajo de Azure Databricks
> * Creación de un clúster de Spark en Azure Databricks
> * Creación de una cuenta de Almacén de Azure Data Lake
> * Carga de datos en Azure Data Lake Store
> * Creación de un cuaderno en Azure Databricks
> * Extracción de datos de Data Lake Store
> * Transformación de datos en Azure Databricks
> * Carga de datos en Azure SQL Data Warehouse

Vaya al siguiente tutorial para aprender sobre el streaming de datos en tiempo real en Azure Databricks mediante Azure Event Hubs.

> [!div class="nextstepaction"]
>[Transmisión de datos a Azure Databricks con Event Hubs](databricks-stream-from-eventhubs.md)
