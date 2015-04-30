<properties 
	pageTitle="Uso de codificadores locales para enviar secuencias en directo de velocidad de bits múltiple a un canal" 
	description="En este tema se describe cómo configurar un canal que recibe una secuencia en directo de velocidad de bits múltiple desde un codificador local. Posteriormente, la secuencia se puede enviar a aplicaciones de reproducción cliente a través de uno o más extremos de streaming, mediante uno de los siguientes protocolos de streaming adaptable: HLS, Smooth Stream, MPEG DASH, HDS." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="juliako"/>

#Uso de codificadores locales para enviar secuencias en directo de velocidad de bits múltiple a un canal

##Información general

En Servicios multimedia de Azure (AMS), un **canal** representa una canalización para procesar contenido de streaming en vivo. Un **programa** le permite controlar la publicación y el almacenamiento de segmentos en una secuencia en directo. Los canales administran los programas. La relación entre canales y programas es muy similar a los medios tradicionales, donde un canal tiene un flujo constante de contenido y un programa se enfoca a algún evento programado en dicho canal. 

Al trabajar con streaming en vivo, uno de los componentes que interviene en el flujo de trabajo es un codificador de vídeo en directo que convierte las señales de la cámara en secuencias que se envían a un servicio de streaming en vivo. Un **canal** puede secuencias de entrada en directo de un codificador en directo local que genera una secuencia en RTMP o MP4 fragmentado (Smooth Streaming) de velocidad de bits múltiple. Puede usar los siguientes codificadores que generan Smooth Streaming: Elemental, Envivio, Cisco.  Los siguientes codificadores en vivo generan RTMP: transcodificadores de Adobe Flash Live, Telestream Wirecast y Tricaster. Las secuencias tomadas pasan a través de **canal**es sin más procesamiento. El codificador en directo también permite tomar una secuencia de velocidad de bits única, pero dado que la secuencia no se procesa, las aplicaciones cliente también recibirán una secuencia de velocidad de bits única (no se recomienda esta opción). Una vez publicado el contenido recibido, se puede transmitir a las aplicaciones cliente de reproducción a través de uno o varios **extremos de streaming**. Los siguientes protocolos de streaming adaptable se pueden usar para reproducir la secuencia: HLS, MPEG DASH, Smooth Stream y HDS. 

Un **extremo de streaming** representa un servicio de streaming que puede entregar contenido directamente a una aplicación de reproducción de cliente o a una red de entrega de contenido (CDN) para la distribución posterior. La secuencia de salida del servicio de extremo de streaming puede ser streaming en vivo o un recurso de vídeo bajo demanda en la cuenta de Servicios multimedia. Además, puede controlar la capacidad del servicio de extremo de streaming para administrar las necesidades crecientes de ancho de banda mediante el ajuste de unidades reservadas de streaming. Debe asignar al menos una unidad reservada para las aplicaciones en un entorno de producción. Para obtener más información, consulte [Escalación de un servicio multimedia](media-services-manage-origins.md#scale_streaming_endpoints).

El siguiente diagrama representa un flujo de trabajo de streaming en vivo que usa un codificador en directo local para generar secuencias RTMP o MP4 fragmentado de velocidad de bits múltiple (Smooth Streaming). 

![Flujo de trabajo en directo][live-overview]

En este tema se incluye información general sobre un canal que recibe una secuencia en directo de velocidad de bits múltiple desde un codificador local. El tema también describe los componentes relacionados con los canales.

##<a id="scenarios"></a>Un escenario común de streaming en vivo

En los pasos siguientes se describen las tareas que intervienen en la creación de aplicaciones comunes de streaming en vivo.

1. Conecte una cámara de vídeo a un equipo. Iniciar y configurar un codificador en directo local que genera una secuencia de RTMP o MP4 fragmentado de velocidad de bits múltiple (Smooth Streaming) Para obtener más información, consulte [Compatibilidad con RTMP de Servicios multimedia de Azure y codificadores en directo](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Este paso también puede realizarse después de crear el canal.

1. Cree e inicie un canal.
1. Recupere la URL de ingesta de canales. 

	El codificador en directo usa la URL de ingesta para enviar la secuencia al canal.
1. Recupere la URL de vista previa de canal. 

	Use esta dirección URL para comprobar que el canal recibe correctamente la secuencia en directo.

2. Cree un recurso.
3. Si desea que el recurso se cifre dinámicamente durante la reproducción, haga lo siguiente: 	
	
	1. 	Cree una clave de contenido. 
	1. 	Configure la directiva de autorización de claves de contenido.
1. Configure la directiva de entrega de recursos(usada por paquetes dinámicos y por el cifrado dinámico).
3. Cree un programa y especifique que se use el recurso que ha creado.
1. Publique el recurso asociado al programa creando un localizador a petición.  

	Asegúrese de tener al menos una unidad reservada de streaming en el extremo de streaming desde el que quiere transmitir contenido.
1. Inicie el programa cuando esté listo para iniciar el streaming y el archivo.
1. Detenga el programa cuando quiera detener el streaming y el archivo del evento.
1. Elimine el programa (y, opcionalmente, elimine el recurso).   

La sección [tareas de streaming en vivo](media-services-manage-channels-overview.md#tasks) incluye vínculos a temas que demuestran cómo realizar las tareas descritas anteriormente.


##<a id="channel_input"></a>Configuraciones de entrada de canal (ingesta)

###<a id="ingest_protocols"></a>Protocolo de streaming de ingesta

Servicios multimedia admite la ingesta de fuentes en directo que usan el siguiente protocolo: 

- **MP4 fragmentado de velocidad de bits múltiple**
 
- **RTMP de velocidad de bits múltiple** 

	Cuando se selecciona el protocolo de streaming de ingesta **RTMP**, se crean dos extremos de ingesta (entrada) para el canal: 
	
	**Dirección URL principal**: especifica la dirección URL completa del extremo de ingesta RTMP principal del canal.

	**Dirección URL secundaria**(opcional): especifica la dirección URL completa del extremo de ingesta RTMP secundario del canal. 

	Use la dirección URL secundaria para mejorar la durabilidad y tolerancia a errores de la secuencia de ingesta, así como la conmutación por error y la tolerancia a errores del codificador. 
	
	- Para mejorar la durabilidad y la tolerancia a errores de la ingesta:
		
		Usas un codificador para enviar los mismos datos a las direcciones URL de ingesta principal y secundaria. La mayoría de codificadores de RTMP (por ejemplo, Flash Media Encoder o Wirecast) tienen la capacidad de usar las direcciones URL principal y secundaria.

	- Para controlar la conmutación por error y la tolerancia a errores del codificador:
		
		Use varios codificadores para generar los mismos datos y enviarlos a las direcciones URL de ingesta principal y secundaria. Este enfoque mejora la ingesta de durabilidad y la alta disponibilidad del codificador. NOTA: el codificador debe admitir el escenario de alta disponibilidad y también se debe sincronizar internamente (para obtener más información, consulte el manual del codificador).
	
 	El uso de la dirección URL de ingesta secundaria requiere más ancho de banda. 

Tenga en cuenta que puede utilizar una velocidad de bits única, pero dado que el canal no procesa la secuencia, las aplicaciones cliente también recibirán una secuencia de velocidad de bits única (no se recomienda esta opción).

Para obtener información sobre los codificadores en directo de RTMP, consulte [Compatibilidad con RTMP de Servicios multimedia de Azure y codificadores en directo](http://go.microsoft.com/fwlink/?LinkId=532824).

Se aplican las siguientes consideraciones:

- Asegúrese de que tiene suficiente conectividad a Internet disponible para enviar datos a los puntos de ingesta. 
- La secuencia entrante de velocidad de bits múltiple puede tener un máximo de 10 niveles de calidad de vídeo (también conocido conocidas como capas) y un máximo de 5 pistas de audio.
- La mayor velocidad de bits promedio para cualquiera de los niveles o las capas de calidad de vídeo debe ser inferior a 10 Mbps.
- La suma de las velocidades de bits promedio para todas las secuencias de audio y vídeo debe ser inferior a 25 Mbps.
- No se puede cambiar el protocolo de entrada mientras el canal o sus programas asociados se están ejecutando. Si necesita diferentes protocolos, debe crear canales independientes para cada protocolo de entrada. 


###Direcciones URL de ingesta (extremos) 

Un canal proporciona un extremo de entrada (dirección URL de ingesta) que usted especifica en el codificador en directo, de modo que el codificador puede insertar secuencias en sus canales.   

Puede obtener las direcciones URL de ingesta al crear el canal. Para obtener estas direcciones URL, el canal no puede encontrarse en el estado iniciado. Cuando está listo para comenzar a insertar datos en el canal, este debe estar iniciado. Una vez que el canal empieza a consumir datos, puede obtener una vista previa de la secuencia a través de la dirección URL de vista previa.

Tiene la opción de consumir una secuencia en directo de MP4 fragmentado (Smooth Streaming) a través de una conexión SSL. Para introducir en SSL, asegúrese de actualizar la dirección URL de introducción a HTTPS. Actualmente, no se puede consumir RTMP a través de SSL. 

###<a id="keyframe_interval"></a>Intervalo de fotogramas clave

Cuando se usa un codificador en directo local para generar una secuencia de velocidad de bits múltiple, el intervalo de fotogramas clave especifica la duración de GOP (tal como la usa el codificador externo). Una vez que el canal recibe esta secuencia entrante, puede entregar la secuencia en directo a las aplicaciones cliente de reproducción en cualquiera de los siguientes formatos: Smooth Streaming, DASH y HLS. Cuando se realiza el streaming en vivo, HLS siempre se empaqueta dinámicamente. De forma predeterminada, Servicios multimedia calcula automáticamente la relación de empaquetado por segmento HLS (fragmentos por segmento) según el intervalo de fotogramas clave, también denominado grupo de imágenes, GOP, que se recibe del codificador en directo. 

En la tabla siguiente se muestra cómo calcula la duración de los segmentos:

<table border="1">
<tr><th>Intervalo de fotogramas clave</th><th>Relación de empaquetado por segmento HLS (fragmento por segmento)</th><th>Ejemplo</th></tr>
<tr><td>menor o igual a 3 segundos</td><td>3:1</td><td>Si KeyFrameInterval (o GOP) es 2 segundos de duración, la relación de empaquetado por segmento HLS predeterminada será 3 a 1, lo que creará un segmento HLS de 6 segundos.</td></tr>
<tr><td>de 3 a 5 segundos</td><td>2:1</td><td>Si KeyFrameInterval (o GOP) es 4 segundos de duración, la relación de empaquetado por segmento HLS predeterminada será 2 a 1, lo que creará un segmento HLS de 8 segundos.</td></tr>
<tr><td>mayor de 5 segundos</td><td>1:1</td><td>Si KeyFrameInterval (o GOP) es 6 segundos de duración, la relación de empaquetado por segmento HLS predeterminada será 1 a 1, lo que creará un segmento HLS de 6 segundos.</td></tr>
</table>

Puede cambiar la relación de fragmentos por segmento configurando la salida del canal y estableciendo FragmentsPerSegment en ChannelOutputHls. 

También puede cambiar el valor del intervalo de fotogramas clave estableciendo la propiedad KeyFrameInterval en ChanneInput. 

Si se establece explícitamente KeyFrameInterval, la relación de empaquetado por segmento HLS FragmentsPerSegment se calcula mediante las reglas descritas anteriormente.  

Si se establece explícitamente KeyFrameInterval y FragmentsPerSegment, Servicios multimedia usará los valores que usted introduzca. 


###Direcciones IP permitidas

Puede definir las direcciones IP permitidas para publicar vídeo en el canal. Las direcciones IP permitidas se pueden especificar como una dirección IP única (p. ej., '10.0.0.1'), un intervalo de IP que usa una dirección IP y una máscara de subred CIDR (p. ej., '10.0.0.1/22') o un intervalo de IP que usa una máscara de subred decimal con puntos (p. ej., '10.0.0.1(255.255.252.0)'). 

Si no se especifica ninguna dirección IP y no hay ninguna definición de regla, no se permitirá ninguna dirección IP. Para permitir las direcciones IP, cree una regla y establezca 0.0.0.0/0.

##Vista previa de canal 

###Direcciones URL de vista previa

Los canales proporcionan un extremo de vista previa (dirección URL de vista previa) que se puede utilizar para obtener una vista previa y validar la secuencia antes de mayor procesamiento y entrega.

Puede obtener la dirección URL de vista previa al crear el canal. Para obtenerla, el canal no puede encontrarse en el estado iniciado. 

Una vez que el canal empieza a consumir datos, puede obtener una vista previa de la secuencia.

Tenga en cuenta que actualmente la secuencia de vista previa solo se puede entregar en formato MP4 fragmentado (Smooth Streaming) independientemente del tipo de entrada especificado. Puede usar el reproductor [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) para probar el formato Smooth Stream. También puede usar un reproductor hospedado en el Portal de administración de Azure para ver la secuencia.


###Direcciones IP permitidas

Puede definir las direcciones IP permitidas para conectarse al extremo de vista previa. Si no se especifica ninguna dirección IP, se permitirá cualquier dirección IP. Las direcciones IP permitidas se pueden especificar como una dirección IP única (p. ej., '10.0.0.1'), un intervalo de IP que usa una dirección IP y una máscara de subred CIDR (p. ej., '10.0.0.1/22') o un intervalo de IP que usa una máscara de subred decimal con puntos (p. ej., '10.0.0.1(255.255.252.0)').

##Salida del canal

Para obtener más información, consulte la sección [Configuración del intervalo de fotogramas clave](#keyframe_interval) .


##Programas del canal

Un canal está asociado a programas que le permiten controlar la publicación y el almacenamiento de segmentos en una secuencia en directo. Los canales administran los programas. La relación entre canales y programas es muy similar a los medios tradicionales, donde un canal tiene un flujo constante de contenido y un programa se enfoca algún evento programado en dicho canal.

Puede especificar la cantidad de horas que desea conservar el contenido grabado del programa en la configuración de la duración de **Ventana de archivo**. Este valor se puede establecer desde un mínimo de cinco minutos a un máximo de 25 horas. La duración de la ventana de archivo también indica el tiempo máximo que los clientes pueden buscar hacia atrás a partir de la posición en vivo actual. Los programas pueden transmitirse durante la cantidad de tiempo especificada, pero el contenido que escape de esa longitud de ventana se descartará continuamente. El valor de esta propiedad también determina durante cuánto tiempo los manifiestos de cliente pueden crecer.

Cada programa está asociado a un recurso. Para publicar el programa, debe crear un localizador a petición para el recurso asociado. Contar con este localizador le permitirá crear una dirección URL de streaming que puede proporcionar a sus clientes.

Un canal es compatible con hasta tres programas en ejecución simultánea, por lo que puede crear varios archivos del mismo flujo entrante. Esto le permite publicar y archivar distintas partes de un evento, según sea necesario. Por ejemplo, el requisito de su empresa es solo archivar seis horas de un programa, pero difundir solo los últimos diez minutos. Para lograrlo, necesita crear dos programas en ejecución simultánea. Un programa está definido para archivar seis horas del evento, pero no está publicado. El otro programa está definido para archivar durante diez minutos y este programa sí se publica.

No debe volver a usar programas existentes para eventos nuevos. En su lugar, cree e inicie un programa nuevo para cada evento como se describe en la sección Programación de aplicaciones de streaming en vivo.

Inicie el programa cuando esté listo para iniciar el streaming y el archivo. Detenga el programa cuando quiera detener el streaming y el archivo del evento. 

Para eliminar contenido archivado, detenga y elimine el programa y, a continuación, elimine el recurso asociado. No se puede eliminar un recurso si se usa un programa; primero se debe eliminar el programa. 

Incluso después de detener y eliminar el programa, los usuarios podrán transmitir el contenido archivado como un vídeo a petición siempre que no elimine el recurso.

Si desea conservar el contenido archivado, pero no hacerlo disponible para la transmisión, elimine el localizador de streaming.

##Estado del canal

El estado actual del canal. Los valores posibles son:

- Detenido. Este es el estado inicial del canal después de su creación. En este estado, se pueden actualizar las propiedades del canal pero no se permite el streaming.
- Iniciando. El canal se está iniciando. No se permiten actualizaciones ni streaming durante este estado. Si se produce un error, el canal vuelve al estado Detenido.
- En ejecución. El canal es capaz de procesar secuencias en directo.
- Se está deteniendo. El canal se está deteniendo. No se permiten actualizaciones ni streaming durante este estado.
- Eliminando. El canal se está eliminando. No se permiten actualizaciones ni streaming durante este estado. 

##Subtítulos e inserción de anuncios 

En la siguiente tabla se muestran los estándares de subtítulos e inserción de anuncios.

<table border="1">
<tr><th>Standard</th><th>Notas</th></tr>
<tr><td>CEA-708 y EIA-608 (708/608)</td><td>CEA-708 y EIA-608 son estándares de subtítulos para Estados Unidos y Canadá.<br/>Actualmente, solo se admiten subtítulos si se transmiten en la secuencia de entrada codificada. Debe usar un codificador multimedia en directo que pueda insertar 608 o 708 títulos en la secuencia codificada que se envía a Servicios multimedia. Servicios multimedia ofrecerá el contenido con subtítulos insertados para los usuarios.</td></tr>
<tr><td>TTML dentro de ismt (pistas de texto Smooth Streaming)</td><td>El empaquetado dinámico de Servicios multimedia permite a los clientes transmitir contenido de cualquiera de los siguientes formatos: MPEG DASH, HLS y Smooth Streaming. Sin embargo, si usted utiliza MP4 fragmentado (Smooth Streaming) con subtítulos dentro de .ismt (pistas de texto Smooth Streaming), solo podrá entregar la secuencia a clientes de Smooth Streaming.</td></tr>
<tr><td>SCTE-35</td><td>Sistema de señalización digital utilizado para poner en cola la inserción de publicidad. Los receptores descendentes usan la señal para unir la publicidad a la secuencia por el tiempo asignado. SCTE-35 se debe enviar como una pista dispersa en la secuencia de entrada.<br/>Tenga en cuenta que en la actualidad el único admite formato de secuencia de entrada admitido que transporta señales de publicidad es el formato MP4 fragmentado (Smooth Streaming). El único formato de salida admitido también es Smooth Streaming.</td></tr>
</table>

##Consideraciones

Cuando se usa un codificador en directo local para enviar una secuencia de velocidad de bits múltiple en un canal, se aplican las siguientes restricciones:

- Asegúrese de que tiene suficiente conectividad a Internet disponible para enviar datos a los puntos de ingesta. 
- La secuencia entrante de velocidad de bits múltiple puede tener un máximo de 10 niveles de calidad de vídeo (10 capas) y un máximo de 5 pistas de audio.
- La mayor velocidad de bits promedio para cualquiera de los niveles o las capas de calidad de vídeo debe ser inferior a 10 Mbps.
- La suma de las velocidades de bits promedio para todas las secuencias de audio y vídeo debe ser inferior a 25 Mbps.
- No se puede cambiar el protocolo de entrada mientras el canal o sus programas asociados se están ejecutando. Si necesita diferentes protocolos, debe crear canales independientes para cada protocolo de entrada. 


Otras consideraciones sobre el funcionamiento de los canales y los componentes relacionados:


- Solo se le cobrará cuando el canal esté en estado de ejecución.
- Cada vez que vuelva a configurar el codificador en directo, llame al método **Restablecer** en el canal. Antes de restablecer el canal, debe detener el programa. Después de restablecer el canal, reinicie el programa. 
- Un canal se puede detener solo cuando está en el estado En ejecución y se han detenido todos los programas en el canal.
- De forma predeterminada solo puede agregar 5 canales a su cuenta de Servicios multimedia. Para obtener más información, consulte [Cuotas y limitaciones](media-services-quotas-and-limitations.md).
- No se puede cambiar el protocolo de entrada mientras el canal o sus programas asociados se están ejecutando. Si necesita diferentes protocolos, debe crear canales independientes para cada protocolo de entrada. 

##<a id="tasks"></a>Tareas relacionadas con el streaming en vivo

###Configuración del equipo

Para obtener información acerca de cómo configurar el equipo, consulte [Configuración del equipo](media-services-set-up-computer.md).

###Configuración de extremos de streaming

Para obtener información general sobre los extremos de streaming y cómo administrarlos, vea [Administración de extremos de streaming en una cuenta de Servicios multimedia](media-services-manage-origins.md)

###Uso de codificadores en directo locales para transmitir secuencias de velocidad de bits múltiple en un canal

Para más información, vea [Uso de codificadores dinámicos de terceros con los Servicios multimedia de Azure](https://msdn.microsoft.com/library/azure/dn783464.aspx).

###Administración de canales, programas y recursos
Para obtener información detallada, vea [Información general de administración de canales y programas](media-services-manage-channels-overview.md).

Elija **Portal**, **.NET**, **API de REST** para ver ejemplos.

[AZURE.INCLUDE [media-services-selector-manage-channels](../includes/media-services-selector-manage-channels.md)]

###Configuración de la directiva de entrega de recursos

Configure la directiva de entrega de recursos con **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

###Creación de una clave de contenido

Cree una clave de contenido con lo que quiera para cifrar su recurso mediante **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../includes/media-services-selector-create-contentkey.md)]

###Configuración de la directiva de autorización de claves de contenido 

Configure la protección de contenido y las directivas de autorización de claves mediante **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]


###Publicación de recursos

Publique recursos (creando localizadores) mediante el **Portal de administración de Azure** o **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


###Habilitación de CDN de Azure

Servicios multimedia admite la integración con CDN de Azure. Para obtener información acerca de cómo habilitar CDN de Azure, consulte [Administración de extremos de streaming en una cuenta de Servicios multimedia](media-services-manage-origins.md#enable_cdn).

###Escalado de una cuenta de Servicios multimedia

Puede escalar **Servicios multimedia** especificando el número de **unidades reservadas** que desea aprovisionar con su cuenta. 

Para obtener información sobre la escalación de unidades de streaming, vea: [Escalado de unidades de streaming](media-services-manage-origins.md#scale_streaming_endpoints.md).


[live-overview]: ./media/media-services-overview/media-services-live-streaming-current.png

<!--HONumber=52-->