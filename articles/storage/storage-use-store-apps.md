---
title: Uso de Azure Storage en las aplicaciones de la Tienda Windows | Microsoft Docs
description: "Obtenga información acerca de cómo crear una aplicación de la Tienda Windows que usa Almacenamiento de blobs, Almacenamiento de colas, Almacenamiento de tablas o Almacenamiento de archivos de Azure."
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 63c4b29d-b2f2-4d7c-b164-a0d38f4d14f6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
ms.translationtype: Human Translation
ms.sourcegitcommit: ff0c9f9c800b0e4764efb88e43087bd96fa820e9
ms.openlocfilehash: 7b02809da6082886b4e2982a698cef09212b6862
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017


---
# <a name="how-to-use-azure-storage-in-windows-store-apps"></a>Uso de Almacenamiento de Azure en las aplicaciones de la Tienda Windows
## <a name="overview"></a>Información general
Esta guía le muestra cómo comenzar con el desarrollo de una aplicación de la Tienda Windows que haga uso del almacenamiento de Azure.

## <a name="download-required-tools"></a>Descarga de las herramientas necesarias
* [Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx) hace que sea fácil compilar, depurar, localizar, empaquetar e implementar aplicaciones de la Tienda Windows. Se requiere Visual Studio 2012 o posterior.
* La [Biblioteca de cliente de Almacenamiento de Azure](https://www.nuget.org/packages/WindowsAzure.Storage) proporciona una biblioteca de clases en tiempo de ejecución de Windows para trabajar con Almacenamiento de Azure.
* [Herramientas de servicios de datos WCF para aplicaciones de la Tienda Windows](http://www.microsoft.com/download/details.aspx?id=30714) amplía la experiencia de incorporación de referencias de servicios gracias a la compatibilidad de OData del lado cliente en aplicaciones de la Tienda Windows en Visual Studio.

## <a name="develop-apps"></a>Desarrollo de aplicaciones
### <a name="getting-ready"></a>Preparación
Cree un nuevo proyecto de aplicaciones de la Tienda Windows en Visual Studio 2012 o posterior:

![store-apps-storage-vs-project][store-apps-storage-vs-project]

A continuación, agregue una referencia a la biblioteca de cliente de Almacenamiento de Azure; para ello, haga clic con el botón derecho en **Referencias**, a continuación, en **Agregar referencia** y vaya a la biblioteca de cliente de Almacenamiento para Windows en tiempo de ejecución que ha descargado:

![store-apps-storage-choose-library][store-apps-storage-choose-library]

### <a name="using-the-library-with-the-blob-and-queue-services"></a>Uso de la biblioteca con los servicios BLOB y Cola
En este momento, la aplicación está preparada para llamar a los servicios Blob de Azure y Cola. Agregue las siguientes instrucciones **using** de manera que pueda hacerse referencia directamente a los tipos de Almacenamiento de Azure:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
```

A continuación, agregue un botón a la página. Agregue el siguiente código al evento **Click** y modifique el método del controlador de eventos con la palabra clave [async](http://msdn.microsoft.com/library/vstudio/hh156513.aspx):

```csharp
var credentials = new StorageCredentials(accountName, accountKey);
var account = new CloudStorageAccount(credentials, true);
var blobClient = account.CreateCloudBlobClient();
var container = blobClient.GetContainerReference("container1");
await container.CreateIfNotExistsAsync();
```

Este código supone que hay dos variables de cadena, *accountName* y *accountKey*. Representan el nombre de la cuenta de almacenamiento y la clave de la cuenta que está asociada con esa cuenta.

Compile y ejecute la aplicación. Si hace clic en el botón, se comprobará si existe un contenedor con el nombre *container1* en la cuenta y se creará si no existiera.

### <a name="using-the-library-with-the-table-service"></a>Uso de la biblioteca con el servicio Tabla
Los tipos usados para comunicarse con el servicio Tabla de Azure dependen de la biblioteca de servicios de datos WCF para la aplicación de la Tienda Windows. A continuación, agregue una referencia a las bibliotecas WCF necesarias mediante la consola del administrador de paquetes:

![store-apps-storage-package-manager][store-apps-storage-package-manager]

Use el siguiente comando para que el administrador de paquetes apunte a la ubicación de la máquina:

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Este comando agregará automáticamente todas las referencias necesarias a su proyecto. Si no desea usar la consola del administrador de paquetes, puede agregar la carpeta NuGet de servicios de datos WCF en su máquina local a la lista de orígenes de paquetes y, a continuación, agregar la referencia a través de la interfaz de usuario como se describe en [Administración de paquetes NuGet mediante el cuadro de diálogo](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

Cuando haya hecho referencia al paquete NuGet de servicios de datos WCF, cambie el código en el evento **Click** del botón:

```csharp
var credentials = new StorageCredentials(accountName, accountKey);
var account = new CloudStorageAccount(credentials, true);
var tableClient = account.CreateCloudTableClient();
var table = tableClient.GetTableReference("table1");
await table.CreateIfNotExistsAsync();
```

Este código comprueba si la tabla con el nombre *table1* existe en su cuenta y, a continuación, se crea si no existiera.

También puede agregar una referencia a Microsoft.WindowsAzure.Storage.Table.dll que está disponible en el mismo paquete que descargó. Esta biblioteca contiene una funcionalidad adicional como consultas genéricas y de serialización basadas en reflejo. Tenga en cuenta que esta biblioteca no es compatible con JavaScript.

[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png

