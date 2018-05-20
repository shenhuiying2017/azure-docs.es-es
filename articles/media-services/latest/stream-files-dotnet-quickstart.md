---
title: 'Streaming de archivos de vídeo con Azure Media Services: .NET | Microsoft Docs'
description: Siga los pasos de este inicio rápido para crear una nueva cuenta de Azure Media Services, codificar un archivo y hacer streaming a Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
keywords: azure media services, streaming
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/08/2018
ms.author: juliako
ms.openlocfilehash: 40759fc65caa181651de68756f4374f879fd9c9c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-stream-video-files---net"></a>Inicio rápido: streaming de archivos de vídeo: .NET

> [!NOTE]
> La versión más reciente de Azure Media Services está en versión preliminar y se denomina v3. Para empezar a usar las API de v3, debe crear una nueva cuenta de Media Services, como se describe en este inicio rápido. 

Este inicio rápido muestra lo fácil que es iniciar el streaming de vídeos en una amplia variedad de navegadores y dispositivos con Azure Media Services. 

Al final del inicio rápido, podrá hacer streaming de un vídeo.  

![Reproducción del vídeo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

Si no tiene Visual Studio instalado, puede obtener [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Descarga del ejemplo

Clone un repositorio GitHub que contenga el ejemplo de .NET de streaming en la máquina con el siguiente comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

Para obtener una explicación sobre qué hace cada función en el ejemplo, examine el código y observe los comentarios en [este archivo de origen](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en [Azure Portal](http://portal.azure.com).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="run-the-sample-app"></a>Ejecutar la aplicación de ejemplo

Al ejecutar la aplicación, se muestran las direcciones URL que se pueden usar para reproducir el vídeo con diferentes protocolos. 

1. Presione Control + F5 para ejecutar la aplicación *EncodeAndStreamFiles*.
2. Elija el protocolo **HLS** de Apple (termina por *manifest(format=m3u8-aapl)*) y copie la dirección URL del streaming desde la consola.

![Salida](./media/stream-files-tutorial-with-api/output.png)

En el [código fuente](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) del ejemplo, puede ver cómo se compila la dirección URL. Para compilarla, debe concatenar el nombre de host del punto de conexión del streaming y la ruta de acceso del localizador del streaming.  

## <a name="test-with-azure-media-player"></a>Prueba con Azure Media Player

Para probar el streaming, este artículo usa Azure Media Player. 

> [!NOTE]
> Si el reproductor está hospedado en un sitio https, asegúrese de actualizar la dirección URL a "https".

1. Abra un explorador web y vaya a [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. En el cuadro **Dirección URL:**, pegue uno de los valores de la dirección URL de streaming que se obtuvo al ejecutar la aplicación. 
3. Presione **Actualizar Player**.

Azure Media Player puede usarse para realizar pruebas, pero no debe usarse en un entorno de producción. 

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita ninguno de los recursos del grupo de recursos, incluida las cuentas de almacenamiento y de Media Services que ha creado en este inicio rápido, elimine el grupo de recursos. Puede usar la herramienta **CloudShell**.

En **CloudShell**, ejecute el siguiente comando:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>Examen del código

Para obtener una explicación sobre qué hace cada función en el ejemplo, examine el código y observe los comentarios en [este archivo de origen](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

El tutorial [Carga, codificación y streaming de archivos](stream-files-tutorial-with-api.md) proporciona un ejemplo de streaming más avanzado con explicaciones detalladas. 

## <a name="multithreading"></a>Multithreading

Los SDK de Azure Media Services v3 no son seguros para subprocesos. Al trabajar con una aplicación que admite multithreading, debe generar un nuevo objeto AzureMediaServicesClient por subproceso.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Carga, codificación y streaming de archivos](stream-files-tutorial-with-api.md)
