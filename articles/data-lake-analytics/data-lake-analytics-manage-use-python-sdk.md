---
title: "Administración de Azure Data Lake Analytics con Python | Microsoft Docs"
description: "Obtenga información acerca de cómo utilizar Python para crear una cuenta de Data Lake Store y enviar trabajos. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: saveenr
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: ab652d6e1e8e6d9bc443af324943bfe24ce4bdc1
ms.contentlocale: es-es
ms.lasthandoff: 06/20/2017


---


# <a name="manage-azure-data-lake-analytics-using-python"></a>Administración de Azure Data Lake Analytics con Python

## <a name="python-versions"></a>Versiones de Python

* Debe usar una versión de Python de 64 bits.
* Puede utilizar la distribución estándar de Python que encontrará en la sección de **[descargas de Python.org](https://www.python.org/downloads/)**. 
* Muchos desarrolladores consideran conveniente utilizar **[Anaconda Distribution de Python](https://www.continuum.io/downloads)**.  
* Este artículo se escribió para la versión 3.6 de Python con la distribución de Python estándar

## <a name="install-azure-python-sdk"></a>Instalación del SDK de Python de Azure

Necesitará instalar los siguientes módulos.

* El módulo **azure-mgmt-resource** incluye otros módulos de Azure para Active Directory, etc.
* El módulo **azure-datalake-store** incluye las operaciones de administración de cuentas de Azure Data Lake Store.
* El módulo **azure-datalake-store** incluye las operaciones de sistema de archivos de Azure Data Lake Store. 
* El módulo **azure-datalake-analytics** incluye las operaciones de Azure Data Lake Analytics. 

En primer lugar, asegúrese de que dispone del último `pip`; para ello, ejecute el comando siguiente.

```
python -m pip install --upgrade pip
```

Este documento se ha escrito con `pip version 9.0.1`.

Utilice el comando `pip` siguiente para instalar los módulos desde la línea de comandos.

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Creación de un nuevo script de Python

Pegue el código siguiente en el script.

```
## Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

## Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

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
```

Ejecute este script para comprobar que se pueden importar los módulos.

## <a name="authentication"></a>Autenticación

### <a name="interactive-user-authentication-with-a-pop-up"></a>Autenticación interactiva de usuarios con elemento emergente

Este método no se admite.

### <a name="interactice-user-authentication-with-a-device-code"></a>Autenticación interactiva de usuarios con código de dispositivo

```
user = input('Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-a-spi-and-a-secret"></a>Autenticación no interactiva con SPI y secreto

```
credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-a-api-and-a-cetificate"></a>Autenticación no interactiva con API y certificado

Este método no se admite.

## <a name="common-script-variables"></a>Variables de script comunes

Estas variables se utilizarán en los ejemplos

```
subid= '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>' # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adls = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Creación de los clientes

```
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient( credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Crear un grupo de recursos de Azure

```
armGroupResult = resourceClient.resource_groups.create_or_update( rg, ResourceGroup( location=location ) )
```

## <a name="create-data-lake-analytics-account"></a>Creación de una cuenta de Análisis de Data Lake

En primer lugar, cree una cuenta de almacenamiento.

```
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adls)]
    )
).wait()
```
A continuación, cree una cuenta de ADLA que utilice ese almacén.

```
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adls)]
    )
).wait()
```

## <a name="submit-data-lake-analytics-jobs"></a>Envío de trabajos de Análisis de Data Lake

```
script = """
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
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adlaAccountName,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-the-job-to-finish"></a>Espere a que el trabajo finalice

```
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adlaAccountName, jobId)

print ('Job finished with result: ' + jobResult.result.value)
```

## <a name="next-steps"></a>Pasos siguientes

- Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestañas en la parte superior de la página.
- Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
- Para conocer las tareas de administración, consulte [Administración de Azure Data Lake Analytics mediante el Azure Portal](data-lake-analytics-manage-use-portal.md).


