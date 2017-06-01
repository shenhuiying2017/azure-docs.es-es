---
title: "Introducción a Azure Data Lake Analytics mediante la CLI de Azure 2.0 | Microsoft Docs"
description: "Aprenda a usar la interfaz de la línea de comandos de Azure 2.0 para crear una cuenta de Data Lake Analytics, crear un trabajo de Data Lake Analytics mediante U-SQL y enviar dicho trabajo. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3f957bda2c38bdeac3ee6b0dbd94fac497c8f7cf
ms.contentlocale: es-es
ms.lasthandoff: 05/31/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-cli-20"></a>Tutorial: introducción a Azure Data Lake Analytics mediante la CLI de Azure 2.0
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Aprenda a usar la CLI de Azure 2.0 para crear cuentas de Azure Data Lake Analytics, definir trabajos de Data Lake Analytics en [U-SQL](data-lake-analytics-u-sql-get-started.md)y enviar trabajos a cuentas de Data Lake Analytics. Para obtener más información acerca de Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

En este tutorial, desarrollará un trabajo que lee un archivo de valores separados por tabulaciones (TSV) y lo convierte en un otro de valores separados por comas (CSV). Para realizar el mismo tutorial con otras herramientas compatibles, use la lista desplegable de la parte superior de esta sección.

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **CLI de Azure 2.0**. Consulte [Instalación y configuración de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Para iniciar sesión en una suscripción de Azure:

```azurecli
az login
```

Se le solicita que vaya a una dirección URL y que escriba un código de autenticación.  Y, después, debe seguir las instrucciones para escribir sus credenciales.

Una vez que haya iniciado sesión, el comando de inicio de sesión enumera las suscripciones.

Para usar una suscripción concreta:

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Creación de una cuenta de Análisis de Data Lake
Para poder ejecutar cualquier trabajo es preciso tener una cuenta de Data Lake Analytics. Para crearla, debe especificar los siguientes elementos:

* **Grupo de recursos de Azure**. Se debe crear una cuenta de Data Lake Analytics en un grupo de recursos de Azure. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permite trabajar con los recursos de la aplicación como un grupo. Puede implementar, actualizar o eliminar todos los recursos de la aplicación en una operación única y coordinada.  

    Para enumerar los grupos de recursos que contiene su suscripción:

    ```azurecli
    az group list
    ```

    Para crear un nuevo grupo de recursos:

    ```azurecli
    az group create --name "<Resource Group Name>" --location "<Azure Location>"
    ```

* **Nombre de la cuenta de Data Lake Analytics**. Cada cuenta de Data Lake Analytics tiene un nombre.
* **Ubicación**. Use uno de los centros de datos de Azure que admita Data Lake Analytics.
* **Cuenta predeterminada de Data Lake Store**: cada cuenta de Data Lake Analytics tiene una cuenta de Data Lake Store predeterminada.

    Para mostrar la cuenta de Data Lake Store existente:

    ```azurecli
    az dls account list
    ```

    Para crear una nueva cuenta de Data Lake Store:

    ```azurecli
    az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
    ```

Use la siguiente sintaxis para crear una cuenta de Data Lake Analytics:

```azurecli
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

Después de crear una cuenta, puede usar los comandos siguientes para enumerar las cuentas y mostrar los detalles de la misma:

```azurecli
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>Carga de datos en el Almacén Data Lake
En este tutorial, va a procesar algunos registros de búsqueda.  El registro de búsqueda se puede almacenar en el Almacén de Data Lake o en el almacenamiento de blobs de Azure.

Azure Portal proporciona una interfaz de usuario para copiar algunos archivos de datos de ejemplo a la cuenta predeterminada de Data Lake Store, entre los que se incluye un archivo de registro de búsqueda. Consulte [Preparar los datos de origen](data-lake-analytics-get-started-portal.md) para cargar los datos en la cuenta del Almacén Data Lake.

Para cargar archivos mediante la CLI 2.0, use los siguientes comandos:

```azurecli
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

Análisis de Data Lake también puede acceder al almacenamiento de blobs de Azure.  Para cargar datos al almacenamiento de blobs de Azure, consulte [Uso de la CLI de Azure con Almacenamiento de Azure](../storage/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Envío de trabajos de Análisis de Data Lake
Los trabajos de Análisis de Data Lake se escriben en el lenguaje U-SQL. Para más información sobre U-SQL, consulte la [introducción al lenguaje U-SQL](data-lake-analytics-u-sql-get-started.md) y la [referencia del lenguaje U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Para crear un script de trabajo de Análisis de Data Lake**

Cree un archivo de texto con el siguiente script U-SQL y guarde el archivo de texto en la estación de trabajo:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/Output/SearchLog-from-Data-Lake.csv"
    USING Outputters.Csv();

Este script de U-SQL lee el archivo de datos de origen mediante **Extractors.Tsv()** y crea un archivo csv con **Outputters.Csv()**.

No modifique ninguna de las dos rutas a menos que copie el archivo de origen en una ubicación diferente.  Data Lake Analytics creará la carpeta de salida si no existe.

Es más fácil usar rutas de acceso relativas para los archivos almacenados en las cuentas predeterminadas de Data Lake Store. También puede usar rutas de acceso absolutas.  Por ejemplo:

    adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

Debe usar rutas de acceso absolutas para acceder a los archivos de cuentas de almacenamiento vinculadas.  La sintaxis de los archivos almacenados en la cuenta de Almacenamiento de Azure vinculada es:

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

  > [!NOTE]
  > Los contenedores de blobs de Azure con permisos de acceso de contenedores públicos o blobs públicos no se admiten actualmente.      
  >
  >

**Para enviar trabajos**

Para enviar un trabajo, use la sintaxis siguiente.

```azurecli
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Por ejemplo:

```azurecli
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**Para enumerar los trabajos y mostrar los detalles de un trabajo**

```azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**Para cancelar trabajos**

```azurecli
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

##<a name="retrieve-job-results"></a>Recuperación de los resultados de un trabajo

Una vez que se completa un trabajo, puede usar los siguientes comandos para enumerar los archivos de salida y descargar los archivos:

```azurecli
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destintion>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs downlod --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "<Destination Path and File Name>"
```

Por ejemplo:

```azurecli
az dls fs downlod --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>Pasos siguientes

* Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestañas en la parte superior de la página.
* Para ver el documento de referencia de la CLI de Data Lake Analytics 2.0, consulte [Data Lake Analytics - az dla](https://docs.microsoft.com/cli/azure/dla).
* Para ver el documento de referencia de la CLI de Data Lake Store 2.0, consulte [Data Lake Store - az dls](https://docs.microsoft.com/cli/azure/dls).
* Para ver una consulta más compleja, consulte la página sobre el [análisis de registros de sitio web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para empezar a desarrollar aplicaciones con U-SQL, consulte [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para conocer las tareas de administración, consulte [Administración de Azure Data Lake Analytics mediante el Azure Portal](data-lake-analytics-manage-use-portal.md).
* Para obtener información general acerca de Análisis de Data Lake, consulte la página de [información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

