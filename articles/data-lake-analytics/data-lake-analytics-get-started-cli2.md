---
title: "Introducción a Azure Data Lake Analytics mediante la CLI de Azure 2.0 | Microsoft Docs"
description: "Aprenda a usar la interfaz de la línea de comandos de Azure 2.0 para crear una cuenta de Data Lake Analytics, crear un trabajo de Data Lake Analytics mediante U-SQL y enviar dicho trabajo. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: jgao
ms.openlocfilehash: fe2b84aac718ff5eddd4d73b5dc2120362952c1e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-cli-20"></a>Introducción al uso de la CLI de Azure 2.0 por parte de Azure Data Lake Analytics
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

En este tutorial, se desarrolla un trabajo que lee un archivo de valores separados por tabulaciones (TSV) y lo convierte en un otro de valores separados por comas (CSV). Para realizar el mismo tutorial con otras herramientas compatibles, use la lista desplegable de la parte superior de esta sección.

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **CLI de Azure 2.0**. Consulte [Instalación y configuración de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Para iniciar sesión en una suscripción de Azure:

```
azurecli
az login
```

Se le solicita que vaya a una dirección URL y que escriba un código de autenticación.  Y, después, debe seguir las instrucciones para escribir sus credenciales.

Una vez que haya iniciado sesión, el comando de inicio de sesión enumera las suscripciones.

Para usar una suscripción concreta:

```
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Creación de una cuenta de Análisis de Data Lake
Para poder ejecutar cualquier trabajo es preciso tener una cuenta de Data Lake Analytics. Para crearla, debe especificar los siguientes elementos:

* **Grupo de recursos de Azure**. Se debe crear una cuenta de Data Lake Analytics en un grupo de recursos de Azure. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permite trabajar con los recursos de la aplicación como un grupo. Puede implementar, actualizar o eliminar todos los recursos de la aplicación en una operación única coordinada.  

Para enumerar los grupos de recursos que contiene su suscripción:

```
az group list
```

Para crear un nuevo grupo de recursos:

```
az group create --name "<Resource Group Name>" --location "<Azure Location>"
```

* **Nombre de la cuenta de Data Lake Analytics**. Cada cuenta de Data Lake Analytics tiene un nombre.
* **Ubicación**. Use uno de los centros de datos de Azure que admita Data Lake Analytics.
* **Cuenta predeterminada de Data Lake Store**: cada cuenta de Data Lake Analytics tiene una cuenta de Data Lake Store predeterminada.

Para mostrar la cuenta de Data Lake Store existente:

```
az dls account list
```

Para crear una nueva cuenta de Data Lake Store:

```azurecli
az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
```

Use la siguiente sintaxis para crear una cuenta de Data Lake Analytics:

```
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

Después de crear una cuenta, puede usar los comandos siguientes para enumerar las cuentas y mostrar los detalles de la misma:

```
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>Carga de datos en el Almacén Data Lake
En este tutorial, va a procesar algunos registros de búsqueda.  El registro de búsqueda se puede almacenar en el Almacén de Data Lake o en el almacenamiento de blobs de Azure.

Azure Portal proporciona una interfaz de usuario para copiar algunos archivos de datos de ejemplo a la cuenta predeterminada de Data Lake Store, entre los que se incluye un archivo de registro de búsqueda. Consulte [Preparar los datos de origen](data-lake-analytics-get-started-portal.md) para cargar los datos en la cuenta del Almacén Data Lake.

Para cargar archivos mediante la CLI 2.0, use los siguientes comandos:

```
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

Análisis de Data Lake también puede acceder al almacenamiento de blobs de Azure.  Para cargar datos al almacenamiento de blobs de Azure, consulte [Uso de la CLI de Azure con Almacenamiento de Azure](../storage/common/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Envío de trabajos de Análisis de Data Lake
Los trabajos de Análisis de Data Lake se escriben en el lenguaje U-SQL. Para más información acerca de U-SQL, consulte [Introducción a U-SQL](data-lake-analytics-u-sql-get-started.md) y [U-SQL Language Reference](http://go.microsoft.com/fwlink/?LinkId=691348) (Referencia del lenguaje U-SQL).

**Para crear un script de trabajo de Análisis de Data Lake**

Cree un archivo de texto con el siguiente script U-SQL y guarde el archivo de texto en la estación de trabajo:

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

Este script de U-SQL lee el archivo de datos de origen mediante **Extractors.Tsv()** y crea un archivo csv con **Outputters.Csv()**.

No modifique ninguna de las dos rutas a menos que copie el archivo de origen en una ubicación diferente.  Data Lake Analytics creará la carpeta de salida si no existe.

Es más fácil usar rutas de acceso relativas para los archivos almacenados en las cuentas predeterminadas de Data Lake Store. También puede usar rutas de acceso absolutas.  Por ejemplo:

```
adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
```

Debe usar rutas de acceso absolutas para acceder a los archivos de cuentas de almacenamiento vinculadas.  La sintaxis de los archivos almacenados en la cuenta de Almacenamiento de Azure vinculada es:

```
wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
```

> [!NOTE]
> El contenedor de blobs de Azure con blobs públicos no se admite.      
> El contenedor de blobs de Azure con contenedores públicos no se admite.      
>

**Para enviar trabajos**

Para enviar un trabajo, use la sintaxis siguiente.

```
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Por ejemplo:

```
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**Para enumerar los trabajos y mostrar los detalles de un trabajo**

```
azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**Para cancelar trabajos**

```
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

## <a name="retrieve-job-results"></a>Recuperación de los resultados de un trabajo

Una vez que se completa un trabajo, puede usar los siguientes comandos para enumerar los archivos de salida y descargar los archivos:

```
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destintion>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs downlod --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "<Destination Path and File Name>"
```

Por ejemplo:

```
az dls fs downlod --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "C:\DLA\myfile.csv"
```

## <a name="pipelines-and-recurrences"></a>Canalizaciones y repeticiones

**Obtención de información sobre canalizaciones y repeticiones**

Utilice los comandos `az dla job pipeline` para ver la información de canalización de trabajos enviados previamente.

```
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

Utilice los comandos `az dla job recurrence` para ver la información de repetición de trabajos enviados previamente.

```
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="next-steps"></a>Pasos siguientes

* Para ver el documento de referencia de la CLI de Data Lake Analytics 2.0, consulte [Data Lake Analytics](https://docs.microsoft.com/cli/azure/dla).
* Para ver el documento de referencia de la CLI de Data Lake Store 2.0, consulte [Data Lake Store](https://docs.microsoft.com/cli/azure/dls).
* Para ver una consulta más compleja, consulte la página sobre el [análisis de registros de sitio web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
