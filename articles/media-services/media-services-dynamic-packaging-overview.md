<properties
	pageTitle="Información general sobre el empaquetado dinámico"
	description="El tema proporciona información general sobre el empaquetado dinámico."
	authors="Juliako"
	manager="dwrede"
	editor=""
	services="media-services"
	documentationCenter=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/05/2015"
	ms.author="juliako"/>


#Empaquetado dinámico

##Información general

Servicios multimedia de Microsoft Azure puede usarse para proporcionar varios formatos de archivo de origen multimedia, formatos de streaming multimedia y formatos de protección de contenido a diversas tecnologías cliente (por ejemplo, iOS, XBOX, Silverlight y Windows 8). Estos clientes entienden distintos protocolos. Por ejemplo, iOS requiere un formato HTTP Live Streaming (HLS) V4, y Silverlight y Xbox requieren Smooth Streaming. Si tiene un conjunto de archivos MP4 (ISO Base Media 14496-12) de velocidad de bits adaptable (varias velocidades de bits) o un conjunto de archivos Smooth Streaming de velocidad de bits adaptable que desea ofrecer a los clientes que entienden MPEG DASH, HLS o Smooth Streaming, puede aprovechar el empaquetado dinámico de Servicios multimedia.

Con el empaquetado dinámico, lo único que debe hacer es crear un recurso que contenga un conjunto de archivos MP4 de velocidad de bits adaptable o archivos Smooth Streaming de velocidad de bits adaptable. Luego, según el formato especificado en la solicitud de manifiesto o fragmento, el servidor de streaming a petición se asegurará de que reciba la secuencia en el protocolo elegido. Como resultado, solo tendrá que almacenar y pagar los archivos en formato de almacenamiento único y Servicios multimedia creará y proporcionará la respuesta adecuada en función de las solicitudes de un cliente.

El diagrama siguiente muestra el flujo de trabajo de codificación y empaquetado estático tradicionales.

![Codificación estática](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

El diagrama siguiente muestra el flujo de trabajo de empaquetado dinámico.

![Codificación dinámica](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]Para aprovechar al máximo el empaquetado dinámico, primero debe obtener al menos una unidad de streaming a petición para el extremo de streaming desde el que va a entregar el contenido. Para obtener más información, consulte [Escalación de un Servicio multimedia](media-services-manage-origins.md#scale_streaming_endpoints).

##Escenario común

1. Cargar un archivo de entrada (llamado archivo intermedio). Por ejemplo, H.264, MP4 o WMV (para obtener la lista de formatos compatibles, vea[ Formatos admitidos por el Codificador de Servicios multimedia](media-services-azure-media-encoder-formats)).

1. Codificar el archivo intermedio en conjuntos MP4 de velocidad de bits adaptable H.264.

1. Publicar el recurso que contiene el conjunto MP4 de velocidad de bits adaptable mediante la creación del localizador a petición.

1. Compilar las direcciones URL de streaming para obtener acceso al contenido y hacer streaming de este.

>[AZURE.NOTE]No todos los formatos de archivo MP4 son compatibles con el empaquetado dinámico. Para obtener más información, consulte [Formatos no compatibles con el empaquetado dinámico](media-services-dynamic-packaging-overview.md#unsupported_formats).

##Preparación de recursos para el streaming dinámico

Si desea preparar el recurso para el streaming dinámico, tiene dos opciones:

- Cargar un archivo maestro y producir conjuntos MP4 de velocidad de bits adaptable de H.264 con el Codificador multimedia de Azure.
- Cargar los conjuntos existentes de velocidad de bits adaptable y validarlos con Media Packager.

###Cargar un archivo maestro y producir conjuntos MP4 de velocidad de bits adaptable de H.264 con el Codificador multimedia de Azure

Para obtener información sobre cómo cargar y codificar recursos, consulte los siguientes artículos:


Cargue los archivos mediante el **Portal de Azure clásico**, **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

Codifique con el **Codificador multimedia de Azure** mediante el **Portal de Azure clásico**, **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]


###Cargar los conjuntos existentes de velocidad de bits adaptable y validarlos con Media Packager

Normalmente, esta tarea se realiza si se carga un conjunto de archivos MP4 de velocidad de bits adaptable no codificados mediante el Codificador de Servicios multimedia. El tema [Validación de archivos MP4 de velocidad de bits adaptativa codificados con codificadores externos](https://msdn.microsoft.com/library/azure/dn750842.aspx) muestra cómo realizar esta tarea.

##Streaming del contenido a los clientes

Una vez que tenga los conjuntos de velocidad de bits adaptable, puede publicar el recurso si crea un localizador a petición y compone las direcciones URL de streaming para Smooth Streaming, MPEG DASH, HLS y HDS (solo para licenciatarios de Adobe PrimeTime/Access).

Para obtener información sobre cómo crear localizadores y usar el empaquetado dinámico para hacer streaming del contenido, consulte los temas siguientes:

[Información general sobre la entrega de contenido a los clientes](media-services-deliver-content-overview.md).

Configure la directiva de entrega de recursos con **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Publique activos (creando localizadores) mediante el **Portal de Azure clásico** o **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


##<a id="unsupported_formats"></a>Formatos no compatibles con el empaquetado dinámico

El empaquetado dinámico no admite los siguientes formatos de archivo de origen:

- Archivos MP4 Dolby Digital Plus.
- Archivos Smooth Dolby Digital Plus.

##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_1210_2015-->