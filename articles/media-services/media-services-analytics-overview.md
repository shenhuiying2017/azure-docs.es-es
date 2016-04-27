<properties
	pageTitle="Información general de análisis de Servicios multimedia de Azure"
	description="Servicios multimedia de Azure ofrece la versión preliminar pública de Análisis multimedia de Azure, una colección de servicios de voz y visión informática a escala empresarial, cumplimiento, seguridad y alcance global. Los servicios Análisis multimedia de Azure se generan con los componentes principales de la plataforma Servicios multimedia de Azure y, por tanto, están preparados para ocuparse del procesamiento multimedia a escala desde el primer día."
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/18/2016"   
	ms.author="milanga;juliako;johndeu"/>


# Información general de análisis de Servicios multimedia de Azure

##Información general

Cada vez más empresas y organizaciones adoptan el vídeo como medio preferido para formar a sus empleados, atraer a sus clientes y documentar las funciones empresariales. La informática en la nube hace que resulte eficaz almacenar estos archivos multimedia de gran tamaño, transmitirlos y acceder a ellos. Sin embargo, a medida que la biblioteca de contenido de vídeo de las empresas crece, se necesita un medio igualmente eficaz para extraer nuevos detalles del vídeo a fin de crear interacciones más significativas y personalizadas con el público y llevar el negocio al siguiente nivel.

Para abordar esta necesidad del mercado en continuo crecimiento, Servicios multimedia de Azure ofrece Análisis multimedia, una colección de servicios de voz y visión informática a escala empresarial, cumplimiento, seguridad y alcance global. Los servicios Análisis multimedia de Azure se generan con los componentes principales de la plataforma Servicios multimedia de Azure y, por tanto, están preparados para ocuparse del procesamiento multimedia a escala desde el primer día.

Análisis multimedia de Azure permite a los desarrolladores empezar a trabajar rápidamente con las funciones de visión para vídeo a escala limitada e integrar esta funcionalidad avanzada en bots y aplicaciones. Análisis multimedia de Azure está diseñado para usarse en entornos empresariales con la escala total, el cumplimiento, la seguridad y el alcance global que necesitan las grandes organizaciones.

## Servicios de Análisis multimedia de Azure

- **Indexer**: Azure Media Indexer permite realizar búsquedas en el contenido, así como generar pistas de subtítulos. Servicios multimedia de Azure publicó **Azure Media Indexer 2 Preview** con indexación más rápida y compatibilidad con más idiomas. Los idiomas compatibles son alemán, árabe, chino, español, francés, inglés, italiano y portugués. Para obtener información detallada y ejemplos, consulte [Process videos with Azure Media Indexer 2 (Procesar vídeos con Azure Media Indexer 2)](media-services-process-content-with-indexer2.md).
 
- **Hyperlapse**: Microsoft Hyperlapse es el resultado de más de 20 años de investigación sobre visión informática en Microsoft Research (MSR), combinando la estabilización de vídeo y la técnica de time-lapse para crear hermosos vídeos rápidos y consumibles a partir de contenido en formato largo. Además de crear time-lapses, también puede usar Hyperlapse para crear vídeos estables a partir de vídeos temblorosos capturados con teléfonos móviles y cámaras de vídeo. Para obtener información detallada y ejemplos, consulte [Archivos multimedia de Hyperlapse con Azure Media Hyperlapse](media-services-hyperlapse-content.md)
 
- **Motion detection**: puede usar este servicio para detectar movimiento en un vídeo con fondos de diseño. Es perfecto para aquellos clientes que quieren buscar falsos positivos en eventos de movimiento detectados por cámaras de vigilancia en las fuentes de vídeo de vigilancia. Para obtener información detallada y ejemplos, consulte [Motion Detection for Azure Media Analytics (Motion Detection de Análisis multimedia de Azure)](media-services-motion-detection.md).
 
- **Face detection y Face emotions**: con este servicio puede detectar caras de personas y sus emociones, por ejemplo, la felicidad, la tristeza, la sorpresa, la ira, el desprecio, el miedo, el desagrado y la indiferencia o neutralidad. Esto tiene varias aplicaciones útiles para el sector, que se describen a continuación, como la adición y el análisis de reacciones de personas que asisten a un evento. Para obtener información detallada y ejemplos, consulte [Face and Emotion Detection for Azure Media Analytics (Face and Emotion Detection de Análisis multimedia de Azure)](media-services-face-and-emotion-detection.md).
 
- **Video summarization**: este servicio puede ayudarle a crear resúmenes de vídeos largos al seleccionar automáticamente fragmentos interesantes del vídeo original. Esto es útil si quiere proporcionar una rápida descripción de lo que se va a encontrar en un vídeo largo. Para obtener información detallada y ejemplos, consulte [Use Azure Media Video Thumbnails to Create a Video Summarization (Usar Miniaturas de vídeo multimedia de Azure para crear un resumen de vídeo)](media-services-video-summarization.md).

## Escenarios comunes

A continuación se muestran un par de escenarios en los que Análisis multimedia de Azure puede ayudar a las organizaciones y empresas de distintos sectores a extraer nuevos detalles a partir del vídeo para conseguir compromisos más personalizados del público y los empleados, así como a administrar más eficazmente grandes volúmenes de contenido de vídeo:

- **Centros de atención al cliente**: incluso con la aparición de las redes sociales, los centros de atención al cliente todavía gestionan un gran porcentaje de transacciones de servicios de clientes. Codificada en estos datos de audio hay una gran cantidad de información sobre los clientes que se puede analizar para mejorar las hojas de ruta de los productos y también para formar a los empleados del centro de atención al cliente a fin de lograr mayor satisfacción de este. Con Azure Media Indexer, los clientes pueden extraer texto y generar un índice de búsqueda y paneles para extraer datos sobre las quejas más comunes, los motivos de queja y otros datos igualmente relevantes.

- **Moderación de contenido generado por el usuario**: desde medios de comunicación a departamentos de policía, muchas organizaciones cuentan con portales orientados al público en los que aceptan medios de contenido generado por el usuario, como imágenes y vídeos. El volumen de contenido puede tener picos debido a sucesos inesperados. En estos casos, es casi imposible llevar a cabo una revisión manual eficaz del contenido para garantizar su idoneidad. Los clientes pueden basarse en el servicio de moderación de contenido para centrarse en el contenido adecuado.

- **Vigilancia**: con el desarrollo de las cámaras IP, hay un aumento vertiginoso de vídeos de vigilancia. La revisión manual de vídeos de vigilancia exige mucho tiempo y es propensa a errores humanos. Análisis multimedia de Azure proporciona varios componentes, como la detección de movimiento, la detección de caras e Hyperlapse, para facilitar el proceso de revisión, administración y creación de derivados.



##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Artículos relacionados

[Media Services Analytics announcement (Anuncio de análisis de Servicios multimedia)](https://azure.microsoft.com/blog/introducing-azure-media-analytics/)
  


 

<!---HONumber=AcomDC_0413_2016-->