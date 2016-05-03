<properties
   pageTitle="Administración de Análisis de Azure Data Lake mediante Azure SDK para Node.js | Azure"
   description="Aprenda a administrar cuentas de Análisis con Data Lake, orígenes de datos, usuarios y trabajos mediante Azure SDK para Node.js"
   services="data-lake-analytics"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/21/2016"
   ms.author="jgao"/>

# Administración de Análisis de Azure Data Lake mediante Azure SDK para Node.js


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure SDK para Node.js se puede usar para administrar cuentas de Análisis con Azure Data Lake, trabajos y catálogos. Para ver los temas de administración con otras herramientas, haga clic en el selector de pestañas de arriba.

Ahora es compatible con:

  *  **Versión de Node.js: 0.10.0 o superior**
  *  **Versión de API de REST para la cuenta: 2015-10-01-preview**
  *  **Versión de API de REST para el catálogo: 2015-10-01-preview**
  *  **Versión de API de REST para el trabajo: 2016-03-20-preview**

## Características

- Administración de cuentas: crear, obtener, enumerar, actualizar y eliminar.
- Administración de trabajos: enviar, obtener, enumerar, cancelar.
- Administración de catálogos: obtener, enumerar, crear (secretos), actualizar (secretos), eliminar (secretos).

## Cómo instalarlo

```bash
npm install azure-arm-datalake-analytics
```

## Autenticación mediante Azure Active Directory

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## Creación del cliente de Análisis de Data Lake

```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## Creación de una cuenta de Análisis de Data Lake

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Obtención de una lista de dominios

```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Obtención de una lista de bases de datos en el catálogo de Análisis de Data Lake
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Consulte también

- [Microsoft Azure SDK for Node.js](https://github.com/azure/azure-sdk-for-node) (Microsoft Azure SDK para Node.js)
- [Microsoft Azure SDK for Node.js - Data Lake Store Management](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store) (Microsoft Azure SDK para Node.js: Administración de Almacén de Data Lake)

<!---HONumber=AcomDC_0427_2016-->