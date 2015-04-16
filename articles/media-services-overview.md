<properties 
	pageTitle="Información general de Servicios multimedia de Azure" 
	description="En este tema se proporciona información general de Servicios multimedia de Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="juliako"/>

# Información general de Servicios multimedia de Azure

Servicios multimedia de Microsoft Azure es una plataforma extensible basada en la nube que permite a los desarrolladores crear aplicaciones de administración y entrega de contenido multimedia escalables. Servicios multimedia se basa en las API de REST, que permiten cargar, almacenar, codificar y empaquetar de forma segura contenido de audio o vídeo para la entrega bajo demanda y de streaming en vivo a varios clientes (por ejemplo, televisión, PC y dispositivos móviles).

Puede crear flujos de trabajo de un extremo a otro usando solamente Servicios multimedia. También puede usar componentes de terceros para algunas partes del flujo de trabajo. Por ejemplo, codifique mediante un codificador de terceros. A continuación, cargue, proteja, empaquete y entregue con Servicios multimedia.

Para compilar soluciones de Servicios multimedia, puede usar:

- [API de REST de Servicios multimedia](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Uno de los SDK de cliente disponibles: [SDK de Servicios multimedia de Azure para .NET](https://github.com/Azure/azure-sdk-for-media-services), [SDK de Azure para Java](https://github.com/Azure/azure-sdk-for-java), [Servicios multimedia de Azure para Node.js](https://github.com/fritzy/node-azure-media), [SDK de Azure PHP](https://github.com/Azure/azure-sdk-for-php)
- Herramientas existentes: [Portal de administración de Azure](http://manage.windowsazure.com/) o [zure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).

**Contrato de nivel de servicio (SLA)**: Servicios multimedia garantiza la disponibilidad del 99,9% de las transacciones de API de REST para codificación de Servicios multimedia. El streaming a petición procesará correctamente las solicitudes de servicio con una garantía de disponibilidad del 99,9% para el contenido multimedia existente cuando se adquiera al menos una unidad reservada de streaming. La disponibilidad se calcula en un ciclo de facturación mensual. Para obtener más información, descargue el [Documento de SLA](https://www.microsoft.com/download/details.aspx?id=39302).

La siguiente publicación describe flujos de trabajo de Servicios multimedia de Azure, desde la creación de contenido multimedia hasta el consumo. Puede descargar el póster desde aquí: [Póster de Servicios multimedia de Azure](http://www.microsoft.com/download/details.aspx?id=38195).

![Overview][overview]

## Conceptos

Para obtener más información, consulte [Conceptos](media-services-concepts.md).

## Elección de la experiencia multimedia

Uno de los primeros pasos para compartir contenido de vídeo es decidir qué tipo de experiencia desea que tengan los clientes. ¿Cómo verán los clientes el contenido de vídeo? ¿Estarán conectados a Internet? ¿Verán el contenido en un equipo o en un dispositivo de mano? ¿Los clientes esperarán que el vídeo sea en HD? Preguntas como estas pueden ayudarle a ofrecerles a los clientes la mejor experiencia posible.

### Acceso a vídeo
 
Existen cuatro maneras básicas en que los clientes pueden tener acceso a vídeos:

- Visualización sin conexión 
- Descarga progresiva
- Streaming\Streaming de velocidad de bits adaptable

#### Visualización sin conexión

Para ver un vídeo sin conexión, un cliente descargará el vídeo completo en su equipo o dispositivo. Los vídeos suelen ser bastante grandes, por lo que la descarga puede tardar en finalizar. La ventaja de la visualización sin conexión es que no se necesita una conexión de red para ver el vídeo una vez que se ha descargado en el dispositivo. 

#### Descarga progresiva

La descarga progresiva requiere que el cliente esté conectado a Internet y le permite empezar a ver el vídeo antes de que se haya descargado por completo. Tanto la visualización sin conexión como la descarga progresiva requieren que el dispositivo que el cliente está usando para ver el vídeo tenga suficiente espacio de almacenamiento para contener todo el vídeo.

#### en

Las tecnologías de streaming también requieren conexión a Internet, pero se descarga una pequeña parte del vídeo cada vez y se descarta una vez que se ha mostrado. Esto requiere muy poco almacenamiento en el dispositivo de visualización. El rendimiento de una conexión de red puede variar, pero, aun así, los clientes esperan poder ver vídeos, independientemente del ancho de banda de red. Las tecnologías de velocidad de bits adaptable permiten a las aplicaciones para reproductor de vídeo determinar las condiciones de red y seleccionar entre varias velocidades de bits. Cuando se degrada la comunicación de red, el cliente puede seleccionar una velocidad de bits más baja que permita al reproductor seguir reproduciendo el vídeo con una calidad inferior. A medida que mejoren las condiciones de red, el cliente puede cambiar a una velocidad de bits superior con calidad de vídeo mejorada. Servicios multimedia de Azure admite tecnologías de velocidad de bits adaptable siguiente: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH y HDS.

### En qué dispositivos

Otra decisión que debe tomar es qué tipo de dispositivos usará el cliente para ver los vídeos. Servicios multimedia es compatible con exploradores web, smartphones, tabletas, XBOX, decodificadores (set-top boxes) y televisores conectados.

#### Exploradores web

Los exploradores web se pueden ejecutar en equipos con Windows, Macintosh PC y smartphones. Cuando se ejecutan en PC o Macintosh, puede aprovechar la pantalla de gran tamaño y la capacidad de almacenamiento de gran tamaño. Esto permite transmitir por secuencias vídeos de mayor calidad. Equipos con Windows o Macintosh puede visualizar los vídeos entregados por Servicios multimedia mediante una aplicación nativa o un explorador de web compatible con HTML. Las aplicaciones nativas admiten Smooth Streaming, Apple HLS, descarga progresiva o visualización sin conexión. Las páginas web HTML5 admiten la descarga progresiva.


#### Smartphones

Los smartphones tienen pantallas pequeñas y menor capacidad de almacenamiento. El streaming es la mejor opción para estos dispositivos. Se admiten, teléfonos iPhone, Windows Phone y Android. Los teléfonos iPhone y Android admiten Smooth Streaming y HLS. Windows Phone admite Smooth Streaming.

### Tabletas

Las tabletas tienen pantallas más grandes que los smartphones, pero suelen tener menor capacidad de almacenamiento. El streaming es la mejor opción para las tabletas. Las tabletas con mayor capacidad de almacenamiento también pueden aprovechar la visualización sin conexión, así como la descarga progresiva.

#### XBox

Las consolas XBox tienen la ventaja de las pantallas grandes y mayor capacidad de almacenamiento, por lo que la visualización sin conexión, la descarga progresiva y el streaming se adaptan bien.
Decodificadores (set-top boxes) y televisores conectados
Estos dispositivos también tienen una pantalla más grande pero menos capacidad de almacenamiento, por lo que el streaming es la mejor opción.

### Tecnologías compatibles por dispositivo

En la siguiente tabla se muestra cada tipo de dispositivo y las tecnologías cliente compatibles con Servicios multimedia:
 
<table border="1">
<tr><th>Dispositivo</th><th>Tecnologías</th></tr>
<tr><td>iOS</td><td>Smooth Streaming, Apple HLS, descarga progresiva</td></tr>
<tr><td>Windows Phone 8</td><td>Smooth Streaming</td></tr>
<tr><td>Windows RT</td><td>Smooth Streaming</td></tr>
<tr><td>Windows</td><td>Smooth Streaming, descarga progresiva</td></tr>
<tr><td>Teléfonos Android</td><td>Smooth Streaming y Apple HLS</td></tr>
<tr><td>XBox</td><td>Smooth Streaming</td></tr>
<tr><td>Macintosh</td><td>Apple HLS, descarga progresiva</td></tr>
<tr><td>Decodificador (set-top box), televisor conectado</td><td>Smooth Streaming, Apple HLS, descarga progresiva</td></tr>
</table>


## Flujos de trabajo de desarrollo comunes: Vídeo bajo demanda y streaming en vivo

### Vídeo bajo demanda: Entrega de contenido de streaming 

1. Cargue un archivo intermedio de alta calidad en un recurso.
1. Codifique en conjunto de archivos MP4 de velocidad de bits adaptable.
1. Configure la directiva de entrega de recursos(usada por paquetes dinámicos).
1. (Opcionalmente) Configure la protección del contenido y la directiva de autorización de claves (usada por el cifrado dinámico).
1. Publique el recurso.
1. Trasmita el contenido publicado. 

### Vídeo bajo demanda: Indizar contenido

1. Cargue un archivo intermedio de alta calidad en un recurso.
1. Indice el contenido.
1. Consuma contenido indizado.

### Vídeo bajo demanda: Entregar la descarga progresiva 

1. Cargue un archivo intermedio de alta calidad en un recurso.
1. Codifique en un solo MP4 o en un conjunto de archivos MP4 de velocidad de bits adaptable.
1. Publique el recurso.
1. Descargue contenido de forma progresiva.

[Vídeo bajo demanda de Servicios multimedia](media-services-video-on-demand-workflow.md) contiene vínculos a temas que muestran cómo realizar las tareas mencionadas anteriormente.

### Streaming en vivo

1. Cree e inicie un canal.
1. Recupere la URL de ingesta de canales.
1. Inicie y configurar el transcodificador en vivo de su elección.
1. Recupere el extremo de vista previa del canal y compruebe que el canal recibe correctamente la secuencia en vivo.
1. Cree un programa y un recurso. 
2. Configure la directiva de entrega para el recurso (usada por paquetes dinámicos).
3. (Opcionalmente)  Configure la protección del contenido y la directiva de autorización de claves (usada por el cifrado dinámico).
1. Para que la secuencia esté disponible para los usuarios, cree un localizador de streaming necesario para tener acceso al recurso asociado con el programa.
1. Inicie el programa cuando esté listo para iniciar el streaming y el archivo.
1. Detenga el programa cuando quiera detener el streaming y el archivo del evento.
1. Elimine el programa (y, opcionalmente, elimine el recurso).

[Streaming en vivo de Servicios multimedia](media-services-live-streaming-wrokflow.md) contiene vínculos a temas que muestran cómo realizar las tareas mencionadas anteriormente.

## Clientes

Servicios multimedia de Azure proporciona las herramientas que necesita para crear aplicaciones cliente de reproductor enriquecidas y dinámicas para la mayoría de las plataformas, como dispositivos iOS, dispositivos Android, Windows, Windows Phone, Xbox y decodificadores (set-top boxes).

- [SDK de cliente de Smooth Streaming](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Plataforma multimedia de Microsoft: Player Framework](http://playerframework.codeplex.com/) 
- [Documentación de HTML5 Player Framework](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Complemento Microsoft Smooth Streaming para OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
- [Media Player Framework para iOS](https://github.com/Azure/azure-media-player-framework) 
- [Licencias de kit de migración de cliente de Microsoft(r) Smooth Streaming](https://www.microsoft.com/mediaplatform/sspk.aspx) 
- Creación de aplicaciones de vídeo en Windows 8 
- [Desarrollo de aplicaciones de vídeo de XBOX](http://xbox.create.msdn.com/)

Para obtener más información, consulte [Desarrollo de aplicaciones de reproductor de vídeo](media-services-develop-video-players.md)

## Guía de patrones y prácticas

[Guía de patrones y prácticas](https://wamsg.codeplex.com/)
[Documentación en línea](https://msdn.microsoft.com/library/dn735912.aspx)
[Libro electrónico descargable](https://www.microsoft.com/download/details.aspx?id=42629)

## Soporte técnico

[Soporte técnico de Azure](http://azure.microsoft.com/support/options/) proporciona opciones de soporte técnico para Azure, incluidos los Servicios multimedia.



<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png

<!--HONumber=47-->
