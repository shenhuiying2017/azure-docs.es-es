---
title: "Introducción al uso de Python por parte de Azure Data Lake Store | Microsoft Docs"
description: Aprenda a usar el SDK de Python para trabajar con cuentas de Data Lake Store y el sistema de archivos.
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
ms.date: 11/29/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: f29f36effd858f164f7b6fee8e5dab18211528b3
ms.openlocfilehash: 6f724576badb7cf3625a139c416860b7e43ed036


---

# <a name="get-started-with-azure-data-lake-store-using-python"></a>Introducción al uso de Python por parte de Azure Data Lake Store

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [SDK de Java](data-lake-store-get-started-java-sdk.md)
> * [API DE REST](data-lake-store-get-started-rest-api.md)
> * [CLI de Azure](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Aprenda a utilizar el SDK de Python para Azure y Azure Data Lake Store para realizar operaciones básicas como crear carpetas, cargar y descargar archivos de datos, etc. Para más información sobre Data Lake, consulte [Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Requisitos previos

* **Python**. Python se puede descargar desde [aquí](https://www.python.org/downloads/). En este artículo se usa Python 3.5.2.

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Cree una aplicación de Azure Active Directory**. Utilice la aplicación Azure AD para autenticar la aplicación Data Lake Store con Azure AD. Existen diferentes enfoques para realizar la autenticación con Azure AD, que son la **autenticación de usuario final** o la **autenticación de servicio a servicio**. Para instrucciones y más información acerca de cómo realizar la autenticación, consulte [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md)(Autenticación con Data Lake Store mediante Azure Active Directory).

## <a name="install-the-modules"></a>Instalación de los módulos

Para trabajar con Data Lake Store mediante Python, debe instalar tres módulos.

* El módulo `azure-mgmt-resource`. Aquí se incluyen módulos de Azure para Active Directory, etc.
* El módulo `azure-mgmt-datalake-store`. Aquí se incluyen las operaciones de administración de cuentas de Azure Data Lake Store. Para más información acerca de este módulo, consulte [referencia al módulo de Azure Data Lake Management Store](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html).
* El módulo `azure-datalake-store`. Aquí se incluyen las operaciones del sistema de archivos de Azure Data Lake Store. Para más información acerca de este módulo, consulte [referencia al módulo de Azure Data Lake Store Filesystem](http://azure-datalake-store.readthedocs.io/en/latest/).

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

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store.account import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.account.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Guarde los cambios en mysample.py.

## <a name="authentication"></a>Autenticación

### <a name="end-user-authentication-for-account-management"></a>Autenticación de usuario final para la administración de cuentas

Se utiliza para autenticarse en Azure AD para operaciones de administración de cuentas (crear o eliminar una cuenta de Data Lake Store, etc.). Debe proporcionar el nombre de usuario y la contraseña para un usuario de Azure AD. Tenga en cuenta que el usuario no debe estar configurado en Multi-Factor Authentication.

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="end-user-authentication-for-filesystem-operations"></a>Autenticación de usuario final para operaciones del sistema de archivos

Se utiliza para autenticarse en Azure AD para las operaciones de sistema de archivos (crear carpeta, cargar archivo, etc.). Se utiliza con una aplicación **cliente nativa** de Azure AD. El usuario de Azure AD al que se proporcionan las credenciales no debe estar configurado en Multi-Factor Authentication.

    tenant_id = 'FILL-IN-HERE'
    client_id = 'FILL-IN-HERE'
    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    token = lib.auth(tenant_id, user, password, client_id)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Autenticación de servicio a servicio con el secreto de cliente para la administración de cuentas

Se utiliza para autenticarse en Azure AD para operaciones de administración de cuentas (crear o eliminar una cuenta de Data Lake Store, etc.). El siguiente fragmento de código se puede utilizar para autenticar la aplicación de forma no interactiva, para lo que se usa el secreto del cliente para una entidad de servicio o aplicación. Utilícelo con una aplicación de "aplicación web" de Azure AD.

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

### <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Autenticación de servicio a servicio con el secreto de cliente para las operaciones del sistema de archivos

Se utiliza para autenticarse en Azure AD para las operaciones de sistema de archivos (crear carpeta, cargar archivo, etc.). El siguiente fragmento de código se puede utilizar para autenticar la aplicación de forma no interactiva, para lo que se usa el secreto del cliente para una entidad de servicio o aplicación. Utilícelo con una aplicación de "aplicación web" de Azure AD.

    token = lib.auth(tenant_id = 'FILL-IN-HERE', client_secret = 'FILL-IN-HERE', client_id = 'FILL-IN-HERE')

## <a name="create-an-azure-resource-group"></a>Crear un grupo de recursos de Azure

Utilice el siguiente fragmento de código para crear un grupo de recursos de Azure:

    ## Declare variables
    subscriptionId= 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'
    
    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )
    
    ## Create an Azure Resource Group
    armGroupResult = resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )

## <a name="create-clients-and-data-lake-store-account"></a>Creación de clientes y de una cuenta de Data Lake Store

El siguiente fragmento de código crea primero el cliente de la cuenta de Data Lake Store. Usa el objeto de cliente para crear una cuenta de Data Lake Store. Por último, el fragmento de código crea un objeto de cliente del sistema de archivos.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(credentials, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(token, store_name=adlsAccountName)

## <a name="list-the-data-lake-store-accounts"></a>Enumeración de las cuentas de Data Lake Store

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

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

## <a name="see-also"></a>Consulte también

- [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
- [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Referencia de SDK de .NET de Data Lake Store](https://msdn.microsoft.com/library/mt581387.aspx)
- [Referencia de REST de Data Lake Store](https://msdn.microsoft.com/library/mt693424.aspx)



<!--HONumber=Jan17_HO1-->


