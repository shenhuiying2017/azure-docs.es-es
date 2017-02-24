---
title: "Introducción a Azure Data Lake Analytics mediante la interfaz de la línea de comandos de Azure | Microsoft Docs"
description: "Aprenda a usar la interfaz de la línea de comandos de Azure para crear una cuenta de Data Lake Analytics, crear un trabajo de Data Lake Analytics mediante U-SQL y enviar el trabajo. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 651021d4-4591-4c48-b1ef-3ebc4606d66d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: c9440e26a563ed9315225b34709b009d01e065d7
ms.openlocfilehash: ff9b0d9e098fd8c6cde7c63c005c8f1906c6e0f2


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Tutorial: Introducción a Análisis de Azure Data Lake mediante Interfaz de línea de comandos (CLI) de Azure
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Aprenda a usar la CLI de Azure para crear cuentas de Análisis de Azure Data Lake, definir trabajos de Análisis de Data Lake en [U-SQL](data-lake-analytics-u-sql-get-started.md)y enviar trabajos a cuentas de Análisis de Data Lake. Para obtener más información acerca de Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

En este tutorial, desarrollará un trabajo que lee un archivo de valores separados por tabulaciones (TSV) y lo convierte en un otro de valores separados por comas (CSV). Para recorrer el mismo tutorial con otras herramientas compatibles, haga clic en las pestañas situadas en la parte superior de esta sección.

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure CLI**. Consulte [Instalación y configuración de la CLI de Azure](../xplat-cli-install.md).
  * Descargue e instale la **versión preliminar** [herramientas de la CLI de Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases) para completar esta demostración.
* **Autenticación**, mediante el comando siguiente:
  
        azure login
    Para más información acerca de cómo autenticarse con una cuenta profesional o educativa, consulte [Conexión a una suscripción de Azure desde la CLI de Azure](../xplat-cli-connect.md).
* **Cambiar al modo de Administrador de recursos de Azure**con el siguiente comando:
  
        azure config mode arm

## <a name="create-data-lake-analytics-account"></a>Creación de una cuenta de Análisis de Data Lake
Debe tener una cuenta de Análisis de Data Lake para poder ejecutar trabajos. Para crear una cuenta de Análisis de Data Lake, debe especificar lo siguiente:

* **Grupo de recursos de Azure**: se debe crear una cuenta de Análisis de Data Lake dentro de un grupo de recursos de Azure. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permite trabajar con los recursos de la aplicación como un grupo. Puede implementar, actualizar o eliminar todos los recursos de la aplicación en una operación única y coordinada.  
  
    Para enumerar los grupos de recursos para la suscripción:
  
        azure group list 
  
    Para crear un nuevo grupo de recursos:
  
        azure group create -n "<Resource Group Name>" -l "<Azure Location>"
* **Nombre de la cuenta de Análisis de Data Lake**
* **Ubicación**: uno de los centros de datos de Azure que admite Análisis de Data Lake.
* **Cuenta predeterminada de Data Lake**: cada cuenta de Análisis de Data Lake tiene una cuenta predeterminada de Data Lake.
  
    Para enumerar la cuenta de Data Lake existente:
  
        azure datalake store account list
  
    Para crear una nueva cuenta de Data Lake:
  
        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"
  
  > [!NOTE]
  > El nombre de cuenta de Data Lake solo debe contener letras minúsculas y números.
  > 
  > 

**Para crear una cuenta de Análisis de Data Lake**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"            

![Cuenta de Análisis de Data Lake](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [!NOTE]
> El nombre de cuenta de Análisis de Data Lake solo debe contener letras minúsculas y números.
> 
> 

## <a name="upload-data-to-data-lake-store"></a>Carga de datos en el Almacén Data Lake
En este tutorial, va a procesar algunos registros de búsqueda.  El registro de búsqueda se puede almacenar en el Almacén de Data Lake o en el almacenamiento de blobs de Azure. 

El Portal de Azure proporciona una interfaz de usuario para copiar algunos archivos de datos de ejemplo a la cuenta predeterminada de Data Lake, entre los que se incluye un archivo de registro de búsqueda. Consulte [Preparar los datos de origen](data-lake-analytics-get-started-portal.md#prepare-source-data) para cargar los datos en la cuenta del Almacén Data Lake.

Para cargar archivos con la CLI, use el siguiente comando:

      azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
      azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Análisis de Data Lake también puede acceder al almacenamiento de blobs de Azure.  Para cargar datos al almacenamiento de blobs de Azure, consulte [Uso de la CLI de Azure con Almacenamiento de Azure](../storage/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Envío de trabajos de Análisis de Data Lake
Los trabajos de Análisis de Data Lake se escriben en el lenguaje U-SQL. Para más información sobre U-SQL, consulte la [introducción al lenguaje U-SQL](data-lake-analytics-u-sql-get-started.md) y la [referencia del lenguaje U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Para crear un script de trabajo de Análisis de Data Lake**

* Cree un archivo de texto con el siguiente script U-SQL y guarde el archivo de texto en la estación de trabajo:
  
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
  
    No modifique ninguna de las dos rutas a menos que copie el archivo de origen en una ubicación diferente.  Análisis de Data Lake creará la carpeta de salida si no existe.
  
    Es más fácil usar rutas de acceso relativas para los archivos almacenados en cuentas predeterminadas de Data Lake. También puede usar rutas de acceso absolutas.  Por ejemplo: 
  
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
  
    Debe usar rutas de acceso absolutas para acceder a los archivos de cuentas de almacenamiento vinculadas.  La sintaxis de los archivos almacenados en la cuenta de Almacenamiento de Azure vinculada es:
  
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
  
  > [!NOTE]
  > Los contenedores de blobs de Azure con permisos de acceso de contenedores públicos o blobs públicos no se admiten actualmente.      
  > 
  > 

**Para enviar el trabajo**

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"


Los siguientes comandos se pueden usar para enumerar los trabajos, obtener detalles de los trabajos y cancelar trabajos:

```
azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
azure datalake analytics job list "<Data Lake Analytics Account Name>"
azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"
```

Después de finalizar el trabajo, puede usar los siguientes cmdlets para mostrar el archivo y descargarlo:

    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## <a name="see-also"></a>Otras referencias
* Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestañas en la parte superior de la página.
* Para ver una consulta más compleja, consulte la página sobre el [análisis de registros de sitio web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para empezar a desarrollar aplicaciones con U-SQL, consulte [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para las tareas de administración, consulte [Administración de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-manage-use-portal.md).
* Para obtener información general sobre Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).




<!--HONumber=Nov16_HO3-->


