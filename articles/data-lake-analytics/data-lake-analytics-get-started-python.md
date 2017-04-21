---
title: "Introducción a Azure Data Lake Analytics mediante Python | Microsoft Docs"
description: "Obtenga información acerca de cómo utilizar Python para crear una cuenta de Data Lake Store y enviar trabajos. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5618650671badfc54860c3ad8af5d1e727d3d8c9
ms.openlocfilehash: 40ccfc59cccd86a7634ec89656571b3cd23566b4
ms.lasthandoff: 11/23/2016


---


# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-python"></a>Tutorial: Introducción a Azure Data Lake Analytics mediante Python
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Aprenda a usar Python para crear cuentas de Azure Data Lake Analytics, definir trabajos de Data Lake Analytics en [U-SQL](data-lake-analytics-u-sql-get-started.md) y enviar trabajos a cuentas de Azure Data Lake Analytics. Para obtener más información acerca de Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

En este tutorial, desarrollará un trabajo que lee un archivo de valores separados por tabulaciones (TSV) y lo convierte en un otro de valores separados por comas (CSV). Para recorrer el mismo tutorial con otras herramientas compatibles, haga clic en las pestañas situadas en la parte superior de esta sección.

##<a name="prerequisites"></a>Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Una aplicación de Azure Active Directory**. Utilice la aplicación Azure AD para autenticar la aplicación Data Lake Store con Azure AD. Existen diferentes enfoques para realizar la autenticación con Azure AD, que son la autenticación de usuario final o la autenticación de servicio a servicio. Para instrucciones y más información acerca de cómo realizar la autenticación, consulte [Authenticate with Data Lake Store using Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)(Autenticación con Data Lake Store mediante Azure Active Directory).
- **[Python](https://www.python.org/downloads/)**. Debe usar la versión de 64 bits. En este artículo se usa la versión 3.5.2 de Python.


## <a name="install-azure-python-sdk"></a>Instalación del SDK de Python de Azure

Para trabajar con Data Lake Store mediante Python, debe instalar tres módulos.

El módulo azure-datalake-store incluye las operaciones de administración de cuentas de Azure Data Lake Store. El módulo azure-mgmt-resource incluye otros módulos de Azure para Active Directory, etc. El módulo azure-datalake-store incluye las operaciones de sistema de archivos de Azure Data Lake Store. El módulo azure-datalake-analytics incluye las operaciones de Azure Data Lake Analytics. Utilice el comando siguiente para instalar los módulos.

    pip install azure-mgmt-resource
    pip install azure-mgmt-datalake-store
    pip install azure-mgmt-datalake-analytics
    pip install azure-datalake-store

## <a name="create-a-python-application"></a>Creación de una aplicación de Python

1. Use el IDE de su elección para crear una nueva aplicación de Python, por ejemplo, mysample.py.

2. Agregue las líneas siguientes para importar los módulos necesarios.

        ## Use this only for Azure AD service-to-service authentication
        from azure.common.credentials import ServicePrincipalCredentials

        ## Use this only for Azure AD end-user authentication
        from azure.common.credentials import UserPassCredentials

        ## Required for Azure Resource Manager
        from azure.mgmt.resource.resources import ResourceManagementClient
        from azure.mgmt.resource.resources.models import ResourceGroup

        ## Required for Azure Data Lake Store account management
        from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
        from azure.mgmt.datalake.store.models import DataLakeStoreAccount

        ## Required for Azure Data Lake Store filesystem management
        from azure.datalake.store import core, lib, multithread

        ## Required for Azure Data Lake Analytics account management
        from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
        from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInfo

        ## Required for Azure Data Lake Analytics job management
        from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
        from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

        ## Required for Azure Data Lake Analytics catalog management
        from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

        ## Use these as needed for your application
        import logging, getpass, pprint, uuid, time

3. Guarde los cambios en el archivo de aplicación de Python.

## <a name="authentication"></a>Autenticación

Para realizar la autenticación use cualquiera de los métodos siguientes:

### <a name="end-user-authentication-for-account-management"></a>Autenticación de usuario final para la administración de cuentas

Utilice este método para autenticarse con Azure AD para operaciones de administración de cuentas (crear o eliminar cuenta de Data Lake Store, etc.). Debe proporcionar el nombre de usuario y la contraseña para un usuario de Azure AD. La cuenta de usuario no se puede configurar para Multi-Factor Authentication y la cuenta no puede ser una cuenta Microsoft ni Live ID como, por ejemplo, @outlook.com, y @live.com.

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Autenticación de servicio a servicio con el secreto de cliente para la administración de cuentas

Utilice este método para autenticarse con Azure AD para operaciones de administración de cuentas (crear o eliminar cuenta de Data Lake Store, etc.). El siguiente fragmento de código se puede utilizar para autenticar la aplicación de forma no interactiva, para lo que se usa el secreto del cliente para una entidad de servicio o aplicación. Utilícelo con una aplicación de "aplicación web" de Azure AD.

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

## <a name="create-resource-management-group"></a>Creación de un grupo de administración de recursos

Utilice el siguiente fragmento de código para crear un grupo de recursos de Azure:

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'

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


## <a name="create-data-lake-store-account"></a>Creación de una cuenta de Data Lake Store

Cada cuenta de Data Lake Analytics requiere una cuenta de Data Lake Store. Para obtener más instrucciones, consulte [Creación de una cuenta de Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account).



## <a name="create-data-lake-analytics-account"></a>Creación de una cuenta de Análisis de Data Lake

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'
    adlsAccountName = '<Azure Data Lake Store Account Name>'
    adlaAccountName = '<Azure Data Lake Analytics Account Name>'

    ## Create management client object
    adlaAcctClient = DataLakeAnalyticsAccountManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Data Lake Analytics account
    adlaAcctResult = adlaAcctClient.account.create(
        resourceGroup,
        adlaAccountName,
        DataLakeAnalyticsAccount(
            location=location,
            default_data_lake_store_account=adlsAccountName,
            data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adlsAccountName)]
        )
    ).wait()


##<a name="submit-data-lake-analytics-jobs"></a>Envío de trabajos de Análisis de Data Lake

Los trabajos de Análisis de Data Lake se escriben en el lenguaje U-SQL. Para más información sobre U-SQL, consulte la [introducción al lenguaje U-SQL](data-lake-analytics-u-sql-get-started.md) y la [referencia del lenguaje U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

    ## Declare variables
    adlsAccountName = '<Azure Data Lake Store Account Name>'

    ## Create management client object
    adlaJobClient = DataLakeAnalyticsJobManagementClient(
        credentials,
        'azuredatalakeanalytics.net'
    )

    ## Submit a U-SQL job
    jobId = str(uuid.uuid4())
    jobResult = adlaJobClient.job.create(
        adlaAccountName,
        jobId,
        JobInformation(
            name='Sample Job',
            type='USql',
            properties=USqlJobProperties(
                script='DROP DATABASE IF EXISTS FOO; CREATE DATABASE FOO;'
            )
        )
    )

    ## Print the job result
    while(jobResult.state != JobState.ended):
        print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
        time.sleep(3)
        jobResult = adlaJobClient.job.get(adlaAccountName, jobId)
        
    print ('Job finished with result: ' + jobResult.result.value)

## <a name="next-steps"></a>Pasos siguientes

- Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestañas en la parte superior de la página.
- Para ver una consulta más compleja, consulte la página sobre el [análisis de registros de sitio web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
- Para empezar a desarrollar aplicaciones con U-SQL, consulte [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
- Para conocer las tareas de administración, consulte [Administración de Azure Data Lake Analytics mediante el Azure Portal](data-lake-analytics-manage-use-portal.md).
- Para obtener información general acerca de Análisis de Data Lake, consulte la página de [información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).


