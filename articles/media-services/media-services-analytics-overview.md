---
title: "Introducción a Análisis de Azure Media Services | Microsoft Docs"
description: "Servicios multimedia de Azure ofrece la versión preliminar pública de Análisis multimedia de Azure, una colección de servicios de voz y visión informática a escala empresarial, cumplimiento, seguridad y alcance global. Los servicios Análisis multimedia de Azure se generan con los componentes principales de la plataforma Servicios multimedia de Azure y, por tanto, están preparados para ocuparse del procesamiento multimedia a escala desde el primer día. "
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/24/2016
ms.author: milanga;juliako;johndeu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: fb8ec19434d4e8cc54b831bb4abf049c057da8df


---
# <a name="azure-media-services-analytics-overview"></a>Información general de análisis de Servicios multimedia de Azure
## <a name="overview"></a>Información general
Cada vez más empresas y organizaciones adoptan el vídeo como medio preferido para formar a sus empleados, atraer a sus clientes y documentar las funciones empresariales. La informática en la nube hace que resulte eficaz almacenar estos archivos multimedia de gran tamaño, transmitirlos y acceder a ellos. Sin embargo, a medida que la biblioteca de contenido de vídeo de las empresas crece, se necesita un medio igualmente eficaz para extraer nuevos detalles del vídeo a fin de crear interacciones más significativas y personalizadas con el público y llevar el negocio al siguiente nivel.

Para abordar esta necesidad creciente en el mercado, Servicios multimedia de Azure ofrece Análisis multimedia, una colección de componentes de voz y visión (a escala empresarial, cumplimiento, seguridad y alcance global) que facilita a las organizaciones y empresas la derivación de conocimientos prácticos partir de sus archivos de vídeo. Los servicios Análisis multimedia de Azure se generan con los componentes principales de la plataforma Servicios multimedia de Azure y, por tanto, están preparados para ocuparse del procesamiento multimedia a escala desde el primer día.

Análisis multimedia de Azure permite a los desarrolladores empezar a trabajar rápidamente con las funciones de visión para vídeo a escala limitada e integrar esta funcionalidad avanzada en aplicaciones. Análisis multimedia de Azure está diseñado para usarse en entornos empresariales con la escala total, el cumplimiento, la seguridad y el alcance global que necesitan las grandes organizaciones.

El siguiente diagrama muestra **Análisis multimedia** y otras partes principales de la plataforma de Servicios multimedia. 

![Flujo de trabajo de VoD](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)

Los procesadores multimedia de Análisis multimedia generan archivos MP4 o JSON. Si un procesador multimedia genera un archivo MP4, puede descargar progresivamente el archivo. Si un procesador multimedia genera un archivo JSON, puede descargar el archivo desde Almacenamiento de blobs de Azure. 

## <a name="azure-media-analytics-services"></a>Servicios de Análisis multimedia de Azure
* **Indexer** : Azure Media Indexer permite realizar búsquedas en el contenido, así como generar pistas de subtítulos. Servicios multimedia de Azure publicó **Azure Media Indexer 2 Preview** con indexación más rápida y compatibilidad con más idiomas. Los idiomas compatibles son alemán, árabe, chino, español, francés, inglés, italiano y portugués. Para obtener información detallada y ejemplos, consulte [Process videos with Azure Media Indexer 2 (Procesar vídeos con Azure Media Indexer 2)](media-services-process-content-with-indexer2.md)
* **Hyperlapse** : Microsoft Hyperlapse es el resultado de más de 20 años de investigación sobre visión informática en Microsoft Research (MSR), combinando la estabilización de vídeo y la técnica de time-lapse para crear hermosos vídeos rápidos y consumibles a partir de contenido en formato largo. Además de crear time-lapses, también puede usar Hyperlapse para crear vídeos estables a partir de vídeos temblorosos capturados con teléfonos móviles y cámaras de vídeo. Para obtener información detallada y ejemplos, consulte [Archivos multimedia de Hyperlapse con Azure Media Hyperlapse](media-services-hyperlapse-content.md)
* **Motion detection** : puede usar este servicio para detectar movimiento en un vídeo con fondos de diseño. Es perfecto para aquellos clientes que quieren buscar falsos positivos en eventos de movimiento detectados por cámaras de vigilancia en las fuentes de vídeo de vigilancia. Para obtener información detallada y ejemplos, consulte [Motion Detection for Azure Media Analytics (Motion Detection de Análisis multimedia de Azure)](media-services-motion-detection.md).
* **Face detection y Face emotions** : con este servicio puede detectar caras de personas y sus emociones, por ejemplo, la felicidad, la tristeza, la sorpresa, la ira, el desprecio, el miedo, el desagrado y la indiferencia o neutralidad. Esto tiene varias aplicaciones útiles para el sector, que se describen a continuación, como la adición y el análisis de reacciones de personas que asisten a un evento. Para obtener información detallada y ejemplos, consulte [Face and Emotion Detection for Azure Media Analytics (Face and Emotion Detection de Análisis multimedia de Azure)](media-services-face-and-emotion-detection.md).
* **Video summarization** : este servicio puede ayudarle a crear resúmenes de vídeos largos al seleccionar automáticamente fragmentos interesantes del vídeo original. Esto es útil si quiere proporcionar una rápida descripción de lo que se va a encontrar en un vídeo largo. Para obtener información detallada y ejemplos, consulte [Use Azure Media Video Thumbnails to Create a Video Summarization (Usar Miniaturas de vídeo multimedia de Azure para crear un resumen de vídeo)](media-services-video-summarization.md)
* **Reconocimiento óptico de caracteres** : gracias al OCR (reconocimiento óptico de caracteres) de Análisis multimedia de Azure, podrá convertir el contenido de texto de archivos de vídeo en texto digital modificable y utilizable en búsquedas. De este modo, se puede automatizar la extracción de metadatos significativos de la señal de vídeo de los elementos multimedia.
* **Censura de rostros escalable** - **Redactor multimedia de Azure** es un procesador multimedia (MP) de Análisis multimedia de Azure que ofrece censura de rostros escalable en la nube. La censura de rostros le permite modificar un vídeo con el fin de difuminar las caras de personas seleccionadas. Puede usar el servicio de censura de rostros en escenarios de seguridad pública y de noticias en los medios de comunicación. Unos minutos de material de archivo que contenga varias caras puede tardar horas en censurarse manualmente, pero con este servicio, el proceso de censura de caras requiere solamente unos pocos pasos sencillos. Para obtener más información, consulte [este artículo](media-services-face-redaction.md).

## <a name="common-scenarios"></a>Escenarios comunes
A continuación se muestran un par de escenarios en los que Análisis multimedia de Azure puede ayudar a las organizaciones y empresas de distintos sectores a extraer nuevos detalles a partir del vídeo para conseguir compromisos más personalizados del público y los empleados, así como a administrar más eficazmente grandes volúmenes de contenido de vídeo:

* **Centros de atención al cliente** : incluso con la aparición de las redes sociales, los centros de atención al cliente todavía gestionan un gran porcentaje de transacciones de servicios de clientes. Codificada en estos datos de audio hay una gran cantidad de información sobre los clientes que se puede analizar para mejorar las hojas de ruta de los productos y también para formar a los empleados del centro de atención al cliente a fin de lograr mayor satisfacción de este. Con Azure Media Indexer, los clientes pueden extraer texto y generar un índice de búsqueda y paneles para extraer datos sobre las quejas más comunes, los motivos de queja y otros datos igualmente relevantes.
* **Moderación de contenido generado por el usuario** : desde medios de comunicación a departamentos de policía, muchas organizaciones cuentan con portales orientados al público en los que aceptan elementos multimedia de contenido generado por el usuario, como imágenes y vídeos. El volumen de contenido puede tener picos debido a sucesos inesperados. En estos casos, es casi imposible llevar a cabo una revisión manual eficaz del contenido para garantizar su idoneidad. Los clientes pueden basarse en el servicio de moderación de contenido para centrarse en el contenido adecuado.
* **Vigilancia** : con el desarrollo de las cámaras IP, hay un aumento vertiginoso de vídeos de vigilancia. La revisión manual de vídeos de vigilancia exige mucho tiempo y es propensa a errores humanos. Análisis multimedia de Azure proporciona varios componentes, como la detección de movimiento, la detección de caras e Hyperlapse, para facilitar el proceso de revisión, administración y creación de derivados.

## <a name="media-services-analytics-media-processors"></a>Procesadores de multimedia de análisis de Servicios multimedia
En esta sección se enumeran todos los procesadores de multimedia (MP) de análisis de Servicios multimedia y se muestra cómo utilizar .NET o REST para obtener un objeto de MP.

### <a name="mp-names"></a>Nombres de MP
* Azure Media Indexer 2 Preview
* Azure Media Indexer
* Azure Media Hyperlapse
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR

### <a name="net"></a>.NET
La siguiente función toma uno de los nombres de MP especificados y devuelve un objeto MP.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


## <a name="rest"></a>REST
Solicitud:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net

Respuesta:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

## <a name="demos"></a>Demostraciones
[Demostraciones de Análisis multimedia de Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Pasos siguientes
Consulte las rutas de aprendizaje de Servicios multimedia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artículos relacionados
[Media Services Analytics announcement (Anuncio de análisis de Servicios multimedia)](https://azure.microsoft.com/blog/introducing-azure-media-analytics/)

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png



<!--HONumber=Dec16_HO2-->


