---
title: "Uso de la interfaz de la línea de comandos 2.0 de Azure para empezar a trabajar con Azure Data Lake Store | Microsoft Docs"
description: "Uso de la línea de comandos 2.0 multiplataforma de Azure para crear una cuenta de Data Lake Store y realizar operaciones básicas"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 4ffa0f4a-1cca-46ac-803d-1fc8538c685b
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 976ddf25a57dd1eff66e121c5a66bc31f664a9dc
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli-20"></a>Introducción a Azure Data Lake Store mediante la CLI de Azure 2.0
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [CLI de Azure 2.0](data-lake-store-get-started-cli-2.0.md)
>
> 

Aprenda a usar la CLI 2.0 de Azure para crear una cuenta de Azure Data Lake Store y realizar operaciones básicas como crear carpetas, cargar y descargar archivos de datos, eliminar la cuenta, etc. Para más información acerca de Data Lake Store, consulte [Información general de Data Lake Store](data-lake-store-overview.md).

La CLI 2.0 de Azure es la nueva experiencia de línea de comandos de Azure para administrar recursos de Azure. Se puede usar en macOS, Linux y Windows. Para más información, consulte la [introducción a la CLI 2.0 de Azure](https://docs.microsoft.com/cli/azure/overview). También puede examinar la [referencia de la CLI 2.0 de Azure Data Lake Store](https://docs.microsoft.com/cli/azure/dls) para obtener una lista completa de comandos y sintaxis.


## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este artículo, debe tener lo siguiente:

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **CLI 2.0 de Azure**: consulte [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Instalación de la CLI 2.0 de Azure) para obtener instrucciones.

## <a name="authentication"></a>Autenticación

En este artículo se utiliza un enfoque de autenticación más sencillo con Data Lake Store, donde inicia sesión como usuario final. El nivel de acceso a la cuenta de Data Lake Store y al sistema de archivos está determinado por el nivel de acceso del usuario que ha iniciado sesión. No obstante, existen otros enfoques para realizar la autenticación con Data Lake Store, que son **autenticación de usuario final** o **autenticación de servicio a servicio**. Para obtener instrucciones y más información acerca de cómo realizar la autenticación, consulte [Autenticación de usuario final con Data Lake Store mediante Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md) o [Autenticación entre servicios con Data Lake Store mediante Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).


## <a name="log-in-to-your-azure-subscription"></a>Inicio de sesión en la suscripción de Azure

1. Inicie sesión en su suscripción de Azure.

    ```azurecli
    az login
    ```

    Obtendrá un código que usará en el paso siguiente. Use un explorador web para abrir la página https://aka.ms/devicelogin y escriba el código para autenticarse. Se le pedirá que inicie sesión con sus credenciales.

2. Una vez que inicie sesión, la ventana muestra todas las suscripciones de Azure que están asociadas a su cuenta. Use el comando siguiente para utilizar una suscripción específica.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-store-account"></a>Creación de una cuenta de Almacén de Azure Data Lake

1. Cree un nuevo grupo de recursos. En el siguiente comando, proporcione los valores de los parámetros que desee usar. Si el nombre de la ubicación contiene espacios, escríbalo entre comillas. Por ejemplo "East US 2". 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Cree una cuenta de Almacén de Data Lake.
   
    ```azurecli
    az dls account create --account mydatalakestore --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-store-account"></a>Crear carpetas en una cuenta de Almacén de Data Lake

Puede crear carpetas en su cuenta de Almacén de Azure Data Lake para administrar y almacenar datos. Use el siguiente comando para crear una carpeta denominada **minuevacarpeta** en la raíz de Data Lake Store.

```azurecli
az dls fs create --account mydatalakestore --path /mynewfolder --folder
```

> [!NOTE]
> El parámetro `--folder` garantiza que el comando crea una carpeta. Si este parámetro no está presente, el comando crea un archivo vacío llamado minuevacarpeta en la raíz de la cuenta de Data Lake Store.
> 
>

## <a name="upload-data-to-a-data-lake-store-account"></a>Carga de datos en una cuenta de Data Lake Store

Puede cargar datos en Data Lake Store directamente en el nivel raíz o en la carpeta que haya creado en la cuenta. Los fragmentos de código siguientes muestran cómo cargar algunos datos de ejemplo en la carpeta (**miNuevaCarpeta**) que creó en la sección anterior.

Si busca datos de ejemplo para cargar, puede obtener la carpeta **Ambulance Data** en el [repositorio Git de Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Descargue el archivo y almacénelo en un directorio local del equipo, como C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestore --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Como destino, debe especificar la ruta de acceso completa, incluido el nombre de archivo.
> 
>


## <a name="list-files-in-a-data-lake-store-account"></a>Enumeración de archivos en una cuenta de Data Lake Store

Use el siguiente comando para enumera los archivos de una cuenta de Almacén de Data Lake.

```azurecli
az dls fs list --account mydatalakestore --path /mynewfolder
```

La salida de este comando debe ser similar a la siguiente:

    [
        {
            "accessTime": 1491323529542,
            "aclBit": false,
            "blockSize": 268435456,
            "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "length": 1589881,
            "modificationTime": 1491323531638,
            "msExpirationTime": 0,
            "name": "mynewfolder/vehicle1_09142014.csv",
            "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "pathSuffix": "vehicle1_09142014.csv",
            "permission": "770",
            "replication": 1,
            "type": "FILE"
        }
    ]

## <a name="rename-download-and-delete-data-from-a-data-lake-store-account"></a>Cambio del nombre, descarga y eliminación de datos en Data Lake Store 

* **Para cambiar el nombre de un archivo**, use el comando siguiente:
  
    ```azurecli
    az dls fs move --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Para descargar un archivo**, use el comando siguiente. Asegúrese de que la ruta de destino especificada ya existe.
  
    ```azurecli     
    az dls fs download --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > El comando crea la carpeta de destino si no existe.
    > 
    >

* **Para eliminar un archivo**, use el comando siguiente:
  
    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Si quiere eliminar la carpeta **minuevacarpeta** y el archivo **vehicle1_09142014_copy.csv** juntos en un solo comando, use el parámetro -- recurse.

    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-store-account"></a>Uso de permisos y listas ACL para una cuenta de Data Lake Store

En esta sección aprenderá a administrar listas ACL y permisos mediante la CLI 2.0 de Azure. Para obtener una explicación detallada sobre cómo se implementan las listas ACL de Azure Data Lake Store, consulte [Control de acceso en Azure Data Lake Store](data-lake-store-access-control.md).

* **Para actualizar el propietario de un archivo o carpeta**, use el siguiente comando:

    ```azurecli
    az dls fs access set-owner --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Para actualizar los permisos de un archivo o carpeta**, use el siguiente comando:

    ```azurecli
    az dls fs access set-permission --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Para obtener las listas ACL para una ruta de acceso dada**, use el siguiente comando:

    ```azurecli
    az dls fs access show --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv
    ```

    La salida debe ser similar a la siguiente:

        {
            "entries": [
            "user::rwx",
            "group::rwx",
            "other::---"
          ],
          "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "permission": "770",
          "stickyBit": false
        }

* **Para establecer una entrada para una lista ACL**, use el siguiente comando:

    ```azurecli
    az dls fs access set-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Para quitar una entrada de una lista ACL**, use el siguiente comando:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Para quitar una lista ACL entera predeterminada**, use el siguiente comando:

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder --default-acl
    ```

* **Para quitar una lista ACL entera no predeterminada**, use el siguiente comando:

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-store-account"></a>Eliminar una cuenta del Almacén de Data Lake
Use el comando siguiente para eliminar la cuenta del Almacén de Data Lake.

```azurecli
az dls account delete --account mydatalakestore
```

Cuando se le solicite, escriba **Y** para eliminar la cuenta.

## <a name="next-steps"></a>pasos siguientes
* [Uso de Azure Data Lake Store para requisitos de macrodatos](data-lake-store-data-scenarios.md) 
* [Protección de los datos en Data Lake Store](data-lake-store-secure-data.md)
* [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
