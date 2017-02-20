---
title: Copiado de blobs desde una cuenta de almacenamiento a un recurso de Azure Media Services | Microsoft Docs
description: "En este tema se muestra cómo copiar un blob existente en un recurso de Media Services. El ejemplo usa las extensiones del SDK de Azure Media Services para .NET."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 6a63823f-f3c9-424c-91b8-566f70bec346
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/31/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 197bf0f0d95422f5bd696a8e9b0abc799f2951fc
ms.openlocfilehash: f71adf1f12e2be0c63465eff7d61a35d3256d4f2


---
# <a name="copying-existing-blobs-into-a-media-services-asset"></a>Copiado de un blob existente en un recurso de Media Services
En este tema se muestra cómo copiar blobs de una cuenta de almacenamiento a un nuevo recurso de Microsoft Azure Media Services mediante [Extensiones del SDK de Azure Media Services para .NET](https://github.com/Azure/azure-sdk-for-media-services-extensions/).

Este método de extensión funciona con:

- Recursos habituales.
- Recursos de archivo dinámico (formato FragBlob).
- Recursos de origen y de destino que pertenecen a diferentes cuentas de Media Services (incluso a través de centros de datos diferentes). Sin embargo, se podrían cobrar gastos por hacerlo. Para obtener más información sobre los precios, vea [Transferencias de datos](https://azure.microsoft.com/pricing/#header-11).

> [!NOTE]
> No debe intentar cambiar el contenido de los contenedores de blobs que generó Servicios multimedia sin usar las API de Servicios multimedia.
> 

## <a name="download-sample"></a>Descarga de un ejemplo
Puede seguir los pasos descritos en este artículo o puede descargar un ejemplo que contiene el código que se describe en este artículo desde [aquí](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/).

## <a name="prerequisites"></a>Requisitos previos
* Dos cuentas de Media Services en una suscripción de Azure nueva o existente. Vea el tema [Cómo crear una cuenta de Media Services](media-services-portal-create-account.md).
* Sistemas operativos: Windows 10, Windows 7, Windows 2008 R2 o Windows 8.
* .NET Framework 4.5.
* Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express) o posterior.

## <a name="set-up-your-project"></a>Configurar su proyecto
En esta sección creará y configurará un proyecto de aplicación de consola de C#.

1. Use Visual Studio para crear una nueva solución que contenga el proyecto de aplicación de consola de C#. 
2. Escriba CopyExistingBlobsIntoAsset para el nombre y luego haga clic en Aceptar.
3. Utilice [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) para instalar Extensiones del SDK de Azure Media Services para .NET (windowsazure.mediaservices.extensions). Al instalar este paquete, también se instala el SDK de Servicios multimedia para .NET y agrega todas las demás dependencias necesarias.
4. Agregue otras referencias que son necesarias para este proyecto: System.Configuration.
6. Agregue la sección appSettings al archivo .config y actualice los valores en función de las cuentas de Media Services, la cuenta de almacenamiento de destino y el identificador del recurso de origen. 
   
        <appSettings>
              <add key="MediaServicesSourceAccountName" value="name"/>
              <add key="MediaServicesSourceAccountKey" value="key"/>
              <add key="MediaServicesDestAccountName" value="name"/>
              <add key="MediaServicesDestAccountKey" value="key"/>
              <add key="DestStorageAccountName" value="name"/>
              <add key="DestStorageAccountKey" value="key"/>
              <add key="SourceAssetID" value="nb:cid:UUID:assetID"/>       
        </appSettings>

## <a name="copy-blobs-from-a-storage-account-into-a-media-services-asset"></a>Copiar blobs de una cuenta de almacenamiento a un recurso de Media Services

El código siguiente utiliza el método **IAsset.Copy** de extensión para copiar todos los archivos del recurso de origen en el recurso de destino mediante una única extensión. Hay una sobrecarga adicional con compatibilidad asincrónica.

Reemplace el código de la clase Program.cs por el código siguiente:

    using System;
    using System.Linq;
    using System.Configuration;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.Storage.Auth;
    
    namespace CopyExistingBlobsIntoAsset
    {
        class Program
        {
            static string _sourceAaccountName = ConfigurationManager.AppSettings["MediaServicesSourceAccountName"];
            static string _sourceAccountKey = ConfigurationManager.AppSettings["MediaServicesSourceAccountKey"];
            static string _destAccountName = ConfigurationManager.AppSettings["MediaServicesDestAccountName"];
            static string _destAccountKey = ConfigurationManager.AppSettings["MediaServicesDestAccountKey"];
            static string _destStorageAccountName = ConfigurationManager.AppSettings["DestStorageAccountName"];
            static string _destStorageAccountKey = ConfigurationManager.AppSettings["DestStorageAccountKey"];
            static string _sourceAssetID = ConfigurationManager.AppSettings["SourceAssetID"];
            
    
            private static CloudMediaContext _sourceContext = null;
            private static CloudMediaContext _destContext = null;
    
            static void Main(string[] args)
            {
                // Create the context for your source Media Services account.
    
                // Use the cached credentials to create CloudMediaContext.
                _sourceContext = new CloudMediaContext(new MediaServicesCredentials(
                    _sourceAaccountName,
                    _sourceAccountKey));

                // Create the context for your destination Media Services account.
                _destContext = new CloudMediaContext(new MediaServicesCredentials(
                    _destAccountName,
                    _destAccountKey));
    
                // Get the credentials of the default Storage account bound to your destination Media Services account.
                StorageCredentials destinationStorageCredentials =
                    new StorageCredentials(_destStorageAccountName, _destStorageAccountKey);
    
                // Get a reference to the source asset in the source context.
                IAsset sourceAsset = _sourceContext.Assets.Where(a => a.Id == _sourceAssetID).First();
    
                // Create an empty destination asset in the destination context.
                IAsset destinationAsset = _destContext.Assets.Create(sourceAsset.Name, AssetCreationOptions.None);
    
                // Copy the files in the source asset instance into the destination asset instance.
                // There is an additional overload with async support.
                sourceAsset.Copy(destinationAsset, destinationStorageCredentials);

                
            }
        }
    }


## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO1-->


