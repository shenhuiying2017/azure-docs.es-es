---
title: 'Python: operaciones de sistema de archivos en Azure Data Lake Store | Microsoft Docs'
description: Aprenda a usar el SDK de Python para trabajar con el sistema de archivos de Data Lake Store.
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 19e41cb0bb22bf27db4581954f1f6e68b8a941c2
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-python"></a>Operaciones de sistema de archivos en Azure Data Lake Store con Python
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [SDK de Java](data-lake-store-get-started-java-sdk.md)
> * [API de REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Con este artículo aprenderá a utilizar el SDK de Python para realizar operaciones de sistema de archivos en Azure Data Lake Store. Para instrucciones sobre cómo realizar operaciones de administración de cuentas en Data Lake Store con Python, consulte [Operaciones de administración de cuentas en Azure Data Lake Store con Python](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>requisitos previos

* **Python**. Python se puede descargar desde [aquí](https://www.python.org/downloads/). En este artículo se usa Python 3.6.2.

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Cuenta del Almacén de Azure Data Lake**. Siga las instrucciones de [Introducción al uso de Azure Portal por parte de Azure Data Lake Store](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Instalación de los módulos

Para trabajar con Data Lake Store mediante Python, debe instalar tres módulos.

* El módulo `azure-mgmt-resource`, que incluye los módulos de Azure para Active Directory, etc.
* El módulo `azure-mgmt-datalake-store`, que incluye las operaciones de administración de cuentas de Azure Data Lake Store. Para más información acerca de este módulo, consulte [referencia al módulo de Azure Data Lake Management Store](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html).
* El módulo `azure-datalake-store`, que incluye las operaciones de sistema de archivos de Azure Data Lake Store. Para más información acerca de este módulo, consulte [referencia al módulo de Azure Data Lake Store Filesystem](http://azure-datalake-store.readthedocs.io/en/latest/).

Utilice el comando siguiente para instalar los módulos.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Creación de una nueva aplicación de Python

1. En el IDE que prefiera, cree una nueva aplicación de Python, por ejemplo, **mysample.py**.

2. Agregue las líneas siguientes para importar los módulos necesarios.

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Guarde los cambios en mysample.py.

## <a name="authentication"></a>Autenticación

En esta sección, se explican las distintas maneras de autenticarse con Azure AD. Las opciones disponibles son:

* Para la autenticación del usuario final para la aplicación, consulte [End-user authentication with Data Lake Store using Python](data-lake-store-end-user-authenticate-python.md) (Autenticación del usuario final con Data Lake Store mediante Python).
* Para la autenticación entre servicios para la aplicación, consulte [Service-to-service authentication with Data Lake Store using Python](data-lake-store-service-to-service-authenticate-python.md) (Autenticación entre servicios con Data Lake Store mediante Python).

## <a name="create-filesystem-client"></a>Creación del cliente del sistema de archivos

El siguiente fragmento de código crea primero el cliente de la cuenta de Data Lake Store. Usa el objeto de cliente para crear una cuenta de Data Lake Store. Por último, el fragmento de código crea un objeto de cliente del sistema de archivos.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)

## <a name="create-a-directory"></a>Creación de directorios

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Cargar un archivo


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Descarga de un archivo

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Eliminación de un directorio

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="next-steps"></a>pasos siguientes
* [Operaciones de administración de cuentas en Azure Data Lake Store con Python](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Otras referencias
* [Referencia de Azure Data Lake Store con Python (administración de cuentas)](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
* [Referencia de Azure Data Lake Store con Python (sistema de archivos)](http://azure-datalake-store.readthedocs.io/en/latest)
* [Abrir aplicaciones Big Data de origen que funcionan con el Almacén de Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)
