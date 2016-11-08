---
title: Codificación de un recurso mediante Estándar de codificador multimedia con el Portal de Azure | Microsoft Docs
description: Este tutorial lo guiará a través de los pasos de codificación de un recurso mediante Estándar de codificador multimedia con el Portal de Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: juliako

---
# Codificación de un recurso mediante Estándar de codificador multimedia con el Portal de Azure
> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
> 
> 

Cuando se trabaja con Servicios multimedia de Azure, uno de los escenarios más comunes es entregar streaming de velocidad de bits adaptable a los clientes. Servicios multimedia admite las siguientes tecnologías de streaming adaptable: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH y HDS (únicamente para licenciatarios de Adobe PrimeTime/Access). Para preparar vídeos para streaming con velocidad de bits adaptable, debe codificar el vídeo de origen en archivos de varias velocidades de bits. Debe utilizar el **Codificador multimedia estándar** para codificar sus vídeos.

Servicios multimedia también proporciona empaquetado dinámico que permite entregar archivos MP4 de velocidad de bits adaptable en los siguientes formatos de streaming: MPEG DASH, HLS, Smooth Streaming o HDS sin tener que volver a empaquetar en dichos formatos. Con el empaquetado dinámico solo necesita almacenar y pagar por los archivos en formato de almacenamiento sencillo y Servicios multimedia creará y servirá la respuesta adecuada en función de las solicitudes del cliente.

Para aprovecharse de los paquetes dinámicos, deberá hacer lo siguiente:

* Codifique su archivo de origen en un conjunto de archivos MP4 de varias velocidades de bits (los pasos de codificación se muestran más adelante en esta sección).
* Obtener al menos una unidad de streaming para el extremo de streaming para el que planea entregar el contenido. Para más información, consulte [Configuración de puntos de conexión de streaming](media-services-portal-vod-get-started.md#configure-streaming-endpoints).

Para escalar el procesamiento de medios, consulte [este](media-services-portal-scale-media-processing.md) tema.

## Codificación con el Portal de Azure
En esta sección se describen los pasos que puede seguir para codificar el contenido con Estándar de codificador multimedia.

1. En la ventana **Configuración**, seleccione **Activos**.
2. En la ventana **Activos**, seleccione el recurso que desea codificar.
3. Presione el botón **Codificar**.
4. En la ventana **Encode an asset** (Codificar un recurso), seleccione el procesador "Codificador multimedia estándar" y un valor predeterminado. Por ejemplo, si sabe que el vídeo de entrada tiene una resolución de 1920 x 1080 píxeles, se podría utilizar el valor predeterminado "H264 Multiple Bitrate 1080p". Para más información acerca de los valores predeterminados, consulte [este](https://msdn.microsoft.com/library/azure/mt269960.aspx) artículo: es importante seleccionar el valor predeterminado que resulta más adecuado para el vídeo de entrada. Si tiene un vídeo de baja resolución (640 x 360), no debería utilizar el valor predeterminado "H264 Multiple Bitrate 1080p".
   
   Para facilitar la administración, se puede editar el nombre del recurso de salida y el nombre del trabajo.
   
   ![Codificación de recursos](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Pulse **Crear**.

## Paso siguiente
Puede supervisar el progreso del trabajo de codificación con el Portal de Azure, tal y como se describe en [este](media-services-portal-check-job-progress.md) artículo.

## Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0831_2016-->