---
title: "Introducción a Data Lake Analytics mediante API de REST| Microsoft Docs"
description: Uso de las API de REST de WebHDFS para realizar operaciones en Data Lake Analytics
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 5e133d92-baaa-44c9-890c-ab2d85c91122
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: jgao
ms.openlocfilehash: 332d7af2539eea8890745005104ac5b0921c2b7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-data-lake-analytics-using-rest-apis"></a>Introducción a Azure Data Lake Analytics mediante API de REST
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Aprenda cómo usar las API de REST de WebHDFS y API de REST de Data Lake Analytics para administrar los trabajos, el catálogo y las cuentas de Data Lake Analytics. 

## <a name="prerequisites"></a>Requisitos previos
* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Cree una aplicación de Azure Active Directory**. Utilice la aplicación Azure AD para autenticar la aplicación Data Lake Analytics con Azure AD. Existen diferentes enfoques para realizar la autenticación con Azure AD, que son la **autenticación de usuario final** o la **autenticación de servicio a servicio**. Para instrucciones y más información acerca de cómo realizar la autenticación, consulte [Autenticación con Data Lake Analytics mediante Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).
* [cURL](http://curl.haxx.se/). En este artículo se usa cURL para demostrar cómo realizar llamadas de la API de REST en una cuenta de Data Lake Analytics.

## <a name="authenticate-with-azure-active-directory"></a>Autenticación mediante Azure Active Directory
Hay dos métodos para autenticar con Azure Active Directory.

### <a name="end-user-authentication-interactive"></a>Autenticación de usuario final (interactiva)
En este escenario, la aplicación pide al usuario que inicie sesión y todas las operaciones se realizan en el contexto del usuario. 

Siga estos pasos para la autenticación interactiva:

1. A través de la aplicación, redirija al usuario a la siguiente dirección URL:
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > \<REDIRECT-URI&gt; debe codificarse para utilizarse en una dirección URL. Por lo tanto, para https://localhost, use `https%3A%2F%2Flocalhost`).
   > 
   > 
   
    Para este tutorial, puede sustituir los valores de marcador de posición de la dirección URL anterior y pegarlos en la barra de direcciones del explorador web. Se le redirigirá a una página autenticarse con sus datos de inicio de sesión de Azure. Una vez que haya iniciado sesión correctamente, la respuesta se muestra en la barra de direcciones del explorador. La respuesta estará en el formato siguiente:
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>
2. Obtenga el código de autorización de la respuesta. Para este tutorial, puede copiar el código de autorización de la barra de direcciones del explorador web y pasarlo en la solicitud POST al punto de conexión de token, tal y como se muestra a continuación:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>
   
   > [!NOTE]
   > En este caso, no se necesita codificar \<REDIRECT-URI>.
   > 
   > 
3. La respuesta es un objeto JSON que contiene un token de acceso (por ejemplo, `"access_token": "<ACCESS_TOKEN>"`) y uno de actualización (por ejemplo, `"refresh_token": "<REFRESH_TOKEN>"`). La aplicación usa el token de acceso al acceder al Almacén de Azure Data Lake y el token de actualización para obtener otro token de acceso cuando expira un token de acceso.
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
4. Cuando expira el token de acceso, puede solicitar uno nuevo con el token de actualización, tal y como se muestra a continuación:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<CLIENT-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Para más información sobre la autenticación interactiva de usuarios, consulte el [flujo de concesión de un código de autorización](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Autenticación de servicio a servicio (no interactiva)
En este escenario, la aplicación proporciona sus propias credenciales para realizar las operaciones. Para ello, debe emitir una solicitud POST como la que se muestra a continuación. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

El resultado de esta solicitud incluirá un token de autorización (que se indica mediante `access-token` en el siguiente resultado) que pasará posteriormente con las llamadas de la API de REST. Guarde este token de autenticación en un archivo de texto; lo necesitará más adelante en este artículo.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

En este artículo se usa el enfoque **no interactivo** . Para más información sobre el enfoque no interactivo (llamadas de servicio a servicio), consulte el tema sobre [llamadas de servicio a servicio utilizando las credenciales del cliente](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="create-a-data-lake-analytics-account"></a>Creación de una cuenta de Análisis de Data Lake
Debe crear un grupo de recursos de Azure y una cuenta de Data Lake Store antes de poder crear una cuenta de Data Lake Analytics.  Consulte [Creación de una cuenta de Data Lake Store](../data-lake-store/data-lake-store-get-started-rest-api.md#create-a-data-lake-store-account).

El siguiente comando Curl muestra cómo crear una cuenta:

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<NewAzureDataLakeAnalyticsAccountName>?api-version=2016-11-01 -d@"C:\tutorials\adla\CreateDataLakeAnalyticsAccountRequest.json"

Reemplace \<`REDACTED`\> por el token de autorización, \<`AzureSubscriptionID`\> por su identificador de suscripción, \<`AzureResourceGroupName`\> por un nombre de Grupo de recursos de Azure existente y \<`NewAzureDataLakeAnalyticsAccountName`\> por el nombre de una nueva cuenta de Data Lake Analytics. La carga útil de la solicitud de este comando se encuentra en el archivo **CreateDatalakeAnalyticsAccountRequest.json** que se proporciona para el parámetro `-d` anterior. El contenido del archivo input.json es similar al siguiente:

    {  
        "location": "East US 2",  
        "name": "myadla1004",  
        "tags": {},  
        "properties": {  
            "defaultDataLakeStoreAccount": "my1004store",  
            "dataLakeStoreAccounts": [  
                {  
                    "name": "my1004store"  
                }     
            ]
        }  
    }  


## <a name="list-data-lake-analytics-accounts-in-a-subscription"></a>Lista de las cuentas de Data Lake Analytics en una suscripción
El siguiente comando Curl muestra cómo enumerar las cuentas de una suscripción:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/providers/Microsoft.DataLakeAnalytics/Accounts?api-version=2016-11-01

Reemplace \<`REDACTED`\> por el token de autorización y \<`AzureSubscriptionID`\> por su identificador de suscripción. La salida es parecida a esta:

    {
        "value": [
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla0831.azuredatalakeanalytics.net",
                "accountId": "21e74660-0941-4880-ae72-b143c2615ea9",
                "creationTime": "2016-09-01T12:49:12.7451428Z",
                "lastModifiedTime": "2016-09-01T12:49:12.7451428Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla0831rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla0831",
            "name": "myadla0831",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            },
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla1004.azuredatalakeanalytics.net",
                "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
                "creationTime": "2016-10-04T20:46:42.287147Z",
                "lastModifiedTime": "2016-10-04T20:46:42.287147Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
            "name": "myadla1004",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            }
        ]
    }

## <a name="get-information-about-a-data-lake-analytics-account"></a>Información acerca de una cuenta de Data Lake Analytics
El siguiente comando Curl muestra cómo obtener información de la cuenta:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>?api-version=2015-11-01

Reemplace \<`REDACTED`\> por el token de autorización, \<`AzureSubscriptionID`\> por su identificador de suscripción, \<`AzureResourceGroupName`\> por un nombre de Grupo de recursos de Azure existente y \<`DataLakeAnalyticsAccountName`\> por el nombre de una cuenta de Data Lake Analytics existente. La salida es parecida a esta:

    {
        "properties": {
            "defaultDataLakeStoreAccount": "my1004store",
            "dataLakeStoreAccounts": [
            {
                "properties": {
                "suffix": "azuredatalakestore.net"
                },
                "name": "my1004store"
            }
            ],
            "provisioningState": "Creating",
            "state": null,
            "endpoint": null,
            "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
            "creationTime": null,
            "lastModifiedTime": null
        },
        "location": "East US 2",
        "tags": {},
        "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
        "name": "myadla1004",
        "type": "Microsoft.DataLakeAnalytics/accounts"
    }

## <a name="list-data-lake-stores-of-a-data-lake-analytics-account"></a>Lista de instancias de Data Lake Store de una cuenta de Data Lake Analytics
El siguiente comando Curl muestra cómo enumerar las instancias de Data Lake Store de una cuenta:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>/DataLakeStoreAccounts/?api-version=2016-11-01

Reemplace \<`REDACTED`\> por el token de autorización, \<`AzureSubscriptionID`\> por su identificador de suscripción, \<`AzureResourceGroupName`\> por un nombre de Grupo de recursos de Azure existente y \<`DataLakeAnalyticsAccountName`\> por el nombre de una cuenta de Data Lake Analytics existente. La salida es parecida a esta:

    {
        "value": [
            {
            "properties": {
                "suffix": "azuredatalakestore.net"
            },
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004/dataLakeStoreAccounts/my1004store",
            "name": "my1004store",
            "type": "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts"
            }
        ]
    }

## <a name="submit-u-sql-jobs"></a>Envío de trabajos de U-SQL
El siguiente comando Curl siguiente muestra cómo enviar un trabajo de U-SQL:

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs/<NewGUID>?api-version=2016-03-20-preview -d@"C:\tutorials\adla\SubmitADLAJob.json"

Reemplace \<`REDACTED`\> por el token de autorización y \<`DataLakeAnalyticsAccountName`\> por el nombre de una cuenta de Data Lake Analytics existente. La carga útil de la solicitud de este comando se encuentra en el archivo **SubmitADLAJob.json** que se proporciona para el parámetro `-d` anterior. El contenido del archivo input.json es similar al siguiente:

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "properties": {
            "type": "USql",
            "script": "@searchlog =\n    EXTRACT UserId          int,\n            Start           DateTime,\n            Region          string,\n            Query          
        string,\n            Duration        int?,\n            Urls            string,\n            ClickedUrls     string\n    FROM \"/Samples/Data/SearchLog.tsv\"\n    US
        ING Extractors.Tsv();\n\nOUTPUT @searchlog   \n    TO \"/Output/SearchLog-from-Data-Lake.csv\"\nUSING Outputters.Csv();"
        }
    }

La salida es parecida a esta:

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "myadl@SPI",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "2016-10-05T13:54:59.9871859+00:00",
        "state": "Compiling",
        "result": "Succeeded",
        "stateAuditRecords": [
            {
            "newState": "New",
            "timeStamp": "2016-10-05T13:54:59.9871859+00:00",
            "details": "userName:myadl@SPI;submitMachine:N/A"
            }
        ],
        "properties": {
            "owner": "myadl@SPI",
            "resources": [],
            "runtimeVersion": "default",
            "rootProcessNodeId": "00000000-0000-0000-0000-000000000000",
            "algebraFilePath": "adl://myadls0831.azuredatalakestore.net/system/jobservice/jobs/Usql/2016/10/05/13/54/8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a/algebra.xml",
            "compileMode": "Semantic",
            "errorSource": "Unknown",
            "totalCompilationTime": "PT0S",
            "totalPausedTime": "PT0S",
            "totalQueuedTime": "PT0S",
            "totalRunningTime": "PT0S",
            "type": "USql"
        }
    }


## <a name="list-u-sql-jobs"></a>Lista de trabajos de U-SQL
El siguiente comando Curl muestra cómo enumerar trabajos de U-SQL:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs?api-version=2016-11-01 

Reemplace \<`REDACTED`\> por el token de autorización y \<`DataLakeAnalyticsAccountName`\> por el nombre de una cuenta de Data Lake Analytics existente. 

La salida es parecida a esta:

    {
    "value": [
        {
        "jobId": "65cf1691-9dbe-43cd-90ed-1cafbfb406fb",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someone@microsoft.com",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:46:53 GMT",
        "startTime": "Wed, 05 Oct 2016 13:47:33 GMT",
        "endTime": "Wed, 05 Oct 2016 13:48:07 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        },
        {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someoneadl@SPI",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:54:59 GMT",
        "startTime": "Wed, 05 Oct 2016 13:55:43 GMT",
        "endTime": "Wed, 05 Oct 2016 13:56:11 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        }
    ],
    "nextLink": null,
    "count": null
    }


## <a name="get-catalog-items"></a>Obtener elementos del catálogo
El siguiente comando Curl muestra cómo obtener las bases de datos del catálogo:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/catalog/usql/databases?api-version=2016-11-01

La salida es parecida a esta:

    {
    "@odata.context":"https://myadla0831.azuredatalakeanalytics.net/sqlip/$metadata#databases","value":[
        {
        "computeAccountName":"myadla0831","databaseName":"mytest","version":"f6956327-90b8-4648-ad8b-de3ff09274ea"
        },{
        "computeAccountName":"myadla0831","databaseName":"master","version":"e8bca908-cc73-41a3-9564-e9bcfaa21f4e"
        }
    ]
    }

## <a name="see-also"></a>Consulte también
* Para ver una consulta más compleja, consulte la página sobre el [análisis de registros de sitio web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para empezar a desarrollar aplicaciones con U-SQL, consulte [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para conocer las tareas de administración, consulte [Administración de Azure Data Lake Analytics mediante el Azure Portal](data-lake-analytics-manage-use-portal.md).
* Para obtener información general sobre Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).
* Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestañas en la parte superior de la página.

