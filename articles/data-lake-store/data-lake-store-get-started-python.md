---
title: "Python: operaciones de administración de cuentas en Azure Data Lake Store | Microsoft Docs"
description: "Aprenda a usar el SDK de Python para realizar operaciones de administración de cuentas de Data Lake Store."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 834dbc93348e00b0a0821bcbca989b0c2d539056
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="account-management-operations-on-azure-data-lake-store-using-python"></a>Operaciones de administración de cuentas en Azure Data Lake Store con Python
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [API DE REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Aprenda a usar el SDK de Python para Azure Data Lake Store para realizar operaciones de administración de cuentas básicas, como la creación o la enumeración de cuentas de Data Lake Store. Para instrucciones sobre cómo realizar operaciones de sistema de archivos en Data Lake Store con Python consulte [Operaciones de sistema de archivos en Azure Data Lake Store con Python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Requisitos previos

* **Python**. Python se puede descargar desde [aquí](https://www.python.org/downloads/). En este artículo se usa Python 3.6.2.

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Un grupo de recursos de Azure**. Para obtener instrucciones, vea [Creación de un grupo de recursos de Azure](../azure-resource-manager/resource-group-portal.md).

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

2. Agregue el fragmento de código siguiente para importar los módulos necesarios

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

## <a name="create-client-and-data-lake-store-account"></a>Creación de un cliente y una cuenta de Data Lake Store

El siguiente fragmento de código crea primero el cliente de la cuenta de Data Lake Store. Usa el objeto de cliente para crear una cuenta de Data Lake Store. Por último, el fragmento de código crea un objeto de cliente del sistema de archivos.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create data lake store account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-store-accounts"></a>Enumeración de las cuentas de Data Lake Store

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-store-account"></a>Eliminación de una cuenta de Data Lake Store

    ## Delete the existing Data Lake Store accounts
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>pasos siguientes
* [Operaciones de sistema de archivos en Azure Data Lake Store con Python](data-lake-store-data-operations-python.md)

## <a name="see-also"></a>Otras referencias
* [Referencia de Azure Data Lake Store con Python (administración de cuentas)](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
* [Referencia de Azure Data Lake Store con Python (sistema de archivos)](http://azure-datalake-store.readthedocs.io/en/latest)
* [Abrir aplicaciones Big Data de origen que funcionan con el Almacén de Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)
