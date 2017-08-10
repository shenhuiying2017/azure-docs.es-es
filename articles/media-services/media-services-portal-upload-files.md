---
title: " Carga de archivos en una cuenta de Media Services mediante Azure Portal | Microsoft Docs"
description: "Este tutorial le guiará por los pasos que hay que seguir para cargar archivos en una cuenta de Servicios multimedia de Azure mediante el Portal de Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 8b9a7a266259077d49a38d51d7f801f420cc6315
ms.contentlocale: es-es
ms.lasthandoff: 08/08/2017

---
# <a name="upload-files-into-a-media-services-account-using-the-azure-portal"></a>Carga de archivos en una cuenta de Servicios multimedia mediante el Portal de Azure
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 
> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 


En Servicios multimedia, cargue los archivos digitales en un recurso. El recurso puede contener vídeo, audio, imágenes, colecciones de miniaturas, pistas de texto y archivos de subtítulos (y los metadatos de estos archivos). Una vez cargados los archivos, el contenido se almacena de forma segura en la nube para un posterior procesamiento y streaming.


## <a name="upload-files"></a>Carga de archivos

>[!NOTE]
>Existe un límite máximo de tamaño de archivo admitido para el procesamiento en Media Services. Consulte [este](media-services-quotas-and-limitations.md) tema para obtener información más detallada acerca de la limitación de tamaño de archivo.
>

1. En [Azure Portal](https://portal.azure.com/), seleccione la cuenta de Azure Media Services.
2. En la hoja **Configuración**, haga clic en **Activos**.
   
    ![Carga de archivos](./media/media-services-portal-vod-get-started/media-services-upload.png)
3. Haga clic en el botón **Upload** .
   
    Aparecerá la ventana **Upload a video asset** (Cargar un recurso de vídeo).
   
   > [!NOTE]
   > No hay ninguna limitación de tamaño de archivo.
   > 
   > 
4. Busque el vídeo deseado en su equipo, selecciónelo y haga clic en Aceptar.  
   
    La carga se inicia y puede ver el progreso en el nombre de archivo.  

Una vez que la carga se haya completado, verá el nuevo recurso en la ventana **Recursos** . 

## <a name="next-steps"></a>Pasos siguientes
Ahora puede codificar los recursos cargados. Para más información, consulte [Encode an asset using Media Encoder Standard with the Azure portal](media-services-portal-encode.md)(Codificación de recursos mediante el estándar de codificador multimedia con el Portal de Azure).

También puede usar Azure Functions para desencadenar un trabajo de codificación basado en un archivo que llega al contenedor configurado. Para más información, consulte [este ejemplo](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


