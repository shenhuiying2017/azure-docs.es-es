---
title: "  Publicación de contenido con Azure Portal | Microsoft Docscs"
description: "Este tutorial lo guiará a través de los pasos de publicación de contenidos con el Portal de Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: 72b0b7feef583799613b1cb2c18b2020c1401b36


---
# <a name="publish-content-with-the-azure-portal"></a>Publicación de contenido con el Portal de Azure
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>Información general
> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Para proporcionar al usuario una dirección URL que pueda utilizarse para transmitir o descargar su contenido, primero necesitará "publicar" su recurso mediante la creación de un localizador. Los localizadores proporcionan acceso a los archivos contenidos en el recurso. Servicios multimedia admite dos tipos de localizadores: 

* Los localizadores de streaming (OnDemandOrigin), que se usan en el streaming adaptable (por ejemplo, para transmitir MPEG DASH, HLS o Smooth Streaming). Para crear un localizador de streaming el recurso debe contener un archivo .ism. 
* Localizadores (SAS) progresivos, utilizados para la entrega de vídeo mediante descarga progresiva.

Una dirección URL de streaming tiene el siguiente formato y se puede usar para reproducir los recursos de Smooth Streaming:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para generar una dirección URL de streaming de HLS, anexe (format=m3u8-aapl) a la dirección URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para generar una dirección URL de streaming de MPEG DASH, anexe (format=mpd-time-csf) a la dirección URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Una dirección URL de SAS tiene el formato siguiente:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Para obtener más información, consulte la [introducción a la entrega de contenidos](media-services-deliver-content-overview.md).

> [!NOTE]
> Si usó el portal para crear localizadores antes de marzo de 2015, se crearon localizadores con una fecha de caducidad de dos años.  
> 
> 

Para actualizar la fecha de caducidad de un localizador, use las API de [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) o de [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Tenga en cuenta que, cuando se actualiza la fecha de caducidad de un localizador de SAS, cambia la dirección URL.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Uso del portal para publicar un recurso
Para usar el portal para publicar un recurso, haga lo siguiente:

1. En [Azure Portal](https://portal.azure.com/), seleccione la cuenta de Azure Media Services.
2. Seleccione **Configuración** > **Activos**.
3. Seleccione el recurso que desea publicar.
4. Haga clic en el botón **Publicar** .
5. Seleccione el tipo de localizador.
6. Presione **Agregar**.
   
    ![Publicar](./media/media-services-portal-vod-get-started/media-services-publish1.png)

La dirección URL se agregará a la lista de **direcciones URL publicadas**.

## <a name="play-content-from-the-portal"></a>contenido desde el portal
El Portal de Azure proporciona un reproductor de contenido que puede usar para probar el vídeo.

Haga clic en el vídeo deseado y, luego, en el botón **Reproducir** .

![Publicar](./media/media-services-portal-vod-get-started/media-services-play.png)

Se aplican algunas consideraciones:

* Asegúrese de que se ha publicado el vídeo.
* El **Reproductor multimedia** reproduce desde el punto de conexión de streaming predeterminado. Si desea reproducir desde un punto de conexión de streaming que no esté predeterminado, haga clic para copiar la dirección URL y use otro reproductor. Por ejemplo, el [Reproductor de Servicios multimedia de Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
* Debe estar ejecutándose el punto de conexión de streaming desde el que está realizando una operación de streaming.  

## <a name="next-steps"></a>Pasos siguientes
Consulte las rutas de aprendizaje de Servicios multimedia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


