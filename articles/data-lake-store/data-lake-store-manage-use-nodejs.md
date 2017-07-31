---
title: "Introducción a Azure Data Lake Store mediante Azure SDK para Node.js | Microsoft Docs"
description: Aprenda a usar Node.js para trabajar con cuentas de Data Lake Store y el sistema de archivos.
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 2fee173c-69ae-4e1d-8773-48618cda9e16
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/06/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 8c7a2e6ca061bbfa077592efb73d592906c3d070
ms.contentlocale: es-es
ms.lasthandoff: 06/07/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-sdk-for-nodejs"></a>Introducción a Azure Data Lake Store mediante Azure SDK para Node.js
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [SDK de Java](data-lake-store-get-started-java-sdk.md)
> * [API de REST](data-lake-store-get-started-rest-api.md)
> * [CLI de Azure 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

> [!NOTE]
> Para cargar y descargar gran cantidad de datos (archivos grandes, un gran número de archivos o ambos), se recomienda usar el [SDK de Python](data-lake-store-get-started-python.md), el [SDK de .NET](data-lake-store-get-started-net-sdk.md), la [CLI de Azure 2.0](data-lake-store-get-started-cli-2.0.md) o [Azure PowerShell](data-lake-store-get-started-powershell.md). Estas opciones tienen mejor rendimiento, ya que utilizan varios subprocesos para realizar el movimiento de datos en paralelo.
> 
> 

Aprenda a usar el SDK de Azure para Node.js para crear una cuenta de Azure Data Lake Store y realizar operaciones básicas como crear carpetas, cargar y descargar archivos de datos, eliminar la cuenta, etc. Para más información acerca de Data Lake Store, consulte [Información general de Data Lake Store](data-lake-store-overview.md). Actualmente, el SDK admite

* **Versión de Node.js: 0.10.0 o superior**
* **Versión de API de REST para la cuenta: 2015-10-01-preview**
* **Versión de API de REST para FileSystem: 2015-10-01-preview**

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este artículo, debe tener lo siguiente:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Cree una aplicación de Azure Active Directory**. Utilice la aplicación Azure AD para autenticar la aplicación Data Lake Store con Azure AD. Existen diferentes enfoques para realizar la autenticación con Azure AD, que son la **autenticación de usuario final** o la **autenticación de servicio a servicio**. Para obtener instrucciones y más información acerca de cómo realizar la autenticación, consulte [Autenticación de usuario final con Data Lake Store mediante Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md) o [Autenticación entre servicios con Data Lake Store mediante Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="how-to-install"></a>Cómo instalarlo
```bash
npm install azure-arm-datalake-store
```

## <a name="authenticate-using-azure-active-directory"></a>Autenticación mediante Azure Active Directory
Los fragmentos de código siguientes muestran dos maneras independientes de autenticar con Data Lake Store mediante Azure AD. Para obtener una explicación detallada sobre diversos métodos para usar para la autenticación con Data Lake Store, consulte [Autenticación con Data Lake Store mediante Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

El siguiente fragmento de código también necesita entradas como el nombre de dominio de Azure AD, el identificador de cliente para una aplicación de Azure AD, etc. Todos estos detalles pueden obtenerse de una aplicación de Azure AD que debe crear, que también se incluyen en el vínculo anterior.

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-store-clients"></a>Creación de los clientes de Data Lake Store
```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## <a name="create-a-data-lake-store-account"></a>Creación de una cuenta de Almacén de Data Lake
```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
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

## <a name="create-a-file-with-content"></a>Creación de un archivo con contenido
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.fileSystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## <a name="get-a-list-of-files-and-folders"></a>Obtención de una lista de archivos y carpetas
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.fileSystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Otras referencias
* [Microsoft Azure SDK for Node.js (Microsoft Azure SDK para Node.js)](https://github.com/azure/azure-sdk-for-node)
* [Microsoft Azure SDK for Node.js - Data Lake Store Management (Microsoft Azure SDK para Node.js: Administración de Análisis de Data Lake)](https://www.npmjs.com/package/azure-arm-datalake-analytics)


