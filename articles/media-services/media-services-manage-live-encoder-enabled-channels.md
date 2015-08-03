<properties 
	pageTitle="Uso de canales habilitados para realizar la codificación en directo con Servicios multimedia de Azure" 
	description="En este tema se describe cómo configurar un canal que recibe una secuencia de streaming en vivo de una sola velocidad de bits desde un codificador local y, a continuación, realiza la codificación en directo a una secuencia de velocidad de bits adaptable con Servicios multimedia. Posteriormente, la secuencia se puede enviar a aplicaciones de reproducción cliente a través de uno o más extremos de streaming, mediante uno de los siguientes protocolos de streaming adaptable: HLS, Smooth Stream, MPEG DASH o HDS." 
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
	ms.date="05/27/2015" 
	ms.author="juliako"/>

#Uso de canales habilitados para realizar la codificación en directo con Servicios multimedia de Azure (vista previa)

##Información general

En Servicios multimedia de Azure, un **canal** representa una canalización para procesar contenido de streaming en vivo. Los **canales** reciben el flujo de entrada en directo de dos maneras posibles:

- Un codificador local en directo envía contenido **RTMP** o **Smooth Streaming** (MP4 fragmentado) de varias velocidades de bits al canal. Puede usar los siguientes codificadores en directo que generan Smooth Streaming de varias velocidades de bits: Elemental, Envivio y Cisco. Los siguientes codificadores en directo generan RTMP: transcodificadores Tricaster, Telestream Wirecast y Adobe Flash Live. Las secuencias recopiladas pasan a través de **canales** sin más procesamiento. Cuando se solicita, Servicios multimedia entrega la secuencia a los clientes.
- Una secuencia de una sola velocidad de bits (con uno de los siguientes formatos: **RTP** (MPEG-TS), **RTMP** o **Smooth Streaming** (MP4 fragmentado)) se envía al **canal** habilitado para realizar la codificación en directo con Servicios multimedia. A continuación, el **canal** codifica en directo la secuencia entrante de una sola velocidad de bits como secuencia de vídeo de varias velocidades de bits (adaptable). Cuando se solicita, Servicios multimedia entrega la secuencia a los clientes. 

	La codificación de secuencias en directo con Servicios multimedia está actualmente en versión de **vista previa**.

A partir de la versión 2.10 de Servicios multimedia, al crear un canal, puede especificar la forma en que desea que este reciba el flujo de entrada y si quiere que el canal realice la codificación en directo de la secuencia. Tiene dos opciones:

- **Ninguna**: especifique este valor si piensa usar un codificador en directo local que genere una secuencia de varias velocidades de bits. En este caso, el flujo entrante pasa hasta la salida sin codificación alguna. Este es el comportamiento de un canal antes de la versión 2.10. Para obtener información más detallada sobre cómo trabajar con los canales de este tipo, consulte [Trabajo con canales que reciben streams en vivo de varias velocidades de bits de codificadores locales](media-services-manage-channels-overview.md).

- **Estándar** (vista previa): elija este valor si piensa usar Servicios multimedia para codificar secuencias en directo de una sola velocidad de bits como secuencias de varias velocidades de bits.

	La codificación de una secuencia en directo con Servicios multimedia está actualmente en versión de vista previa.

>[AZURE.NOTE]En este tema se describen los atributos de los canales habilitados para realizar la codificación en directo (tipo de codificación **Estándar**). Para obtener información sobre cómo trabajar con los canales no habilitados para realizar la codificación en directo, consulte [Trabajo con canales que reciben streams en vivo de varias velocidades de bits de codificadores locales](media-services-manage-channels-overview.md).

El siguiente diagrama representa un flujo de trabajo de streaming en vivo donde un canal recibe una secuencia de una sola velocidad de bits en uno de los siguientes protocolos: RTMP, Smooth Streaming o RTP (MPEG-TS). A continuación, la codifica como secuencia de varias velocidades de bits.

![Flujo de trabajo activo][live-overview]

>[AZURE.NOTE]No todos los centros de datos admiten la codificación en directo con Servicios multimedia de Azure.
>
>Si usas el Portal de administración de Azure para crear canales, tendrá dos opciones de tipo de codificación de canal disponibles: **Ninguno** y **Estándar**. Si solo ve la opción **Ninguno**, significa que su centro de datos no admite la codificación en directo con AMS.
>
>Si usa .NET SDK o API de REST, haga lo siguiente para comprobar:
>
>1. Intente crear un canal con el tipo de codificación establecido en Estándar. 
>2. Si el valor devuelto es Código de error HTTP 412 (error de condición previa) con el siguiente mensaje: *"Esta región no admite la codificación en directo; EncodingType debe establecerse en 'Ninguno'."*, su centro de datos no admite la codificación en directo.


##En este tema

- Información general de un [escenario común de streaming en vivo](media-services-manage-live-encoder-enabled-channels.md#scenario)
- [Descripción de un canal y sus componentes relacionados](media-services-manage-live-encoder-enabled-channels.md#channel)
- [Consideraciones](media-services-manage-live-encoder-enabled-channels.md#considerations)
- [Tareas relacionadas con el streaming en vivo](media-services-manage-live-encoder-enabled-channels.md#tasks)

##<a id="scenario"></a>Escenario común de streaming en vivo

A continuación se indican los pasos generales para crear aplicaciones comunes de streaming en vivo.

1. Conecte una cámara de vídeo a un equipo. Inicie y configure un codificador local en directo que pueda generar una secuencia de una **sola** velocidad de bits en uno de los siguientes protocolos: RTMP, Smooth Streaming o RTP (MPEG-TS). Para obtener más información, consulte [Compatibilidad con RTMP de Servicios multimedia de Azure y codificadores en directo](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Este paso también puede realizarse después de crear el canal.

1. Cree e inicie un canal.

1. Recupere la URL de ingesta de canales.

	El codificador en directo usa la URL de ingesta para enviar la secuencia al canal.
1. Recupere la URL de vista previa de canal. 

	Use esta dirección URL para comprobar que el canal recibe correctamente la secuencia en directo.

3. Cree un programa.

	Con el Portal de administración de Azure, al crear un programa también se crea un recurso.

	Con el SDK de .NET o REST, debe crear un recurso y especificar que este se use al crear un programa. 
1. Publique el recurso asociado al programa.   

	Asegúrese de tener al menos una unidad de streaming reservada en el extremo de streaming desde el que desea transmitir el contenido.
1. Inicie el programa cuando esté listo para iniciar el streaming y el archivo.
2. Si lo desea, puede señalar el codificador en directo para iniciar un anuncio. El anuncio se inserta en el flujo de salida.
1. Detenga el programa cuando quiera detener el streaming y el archivo del evento.
1. Elimine el programa (y, opcionalmente, elimine el recurso).   

La sección de [tareas de streaming en vivo](media-services-manage-channels-overview.md#tasks) incluye vínculos a temas que demuestran cómo realizar las tareas descritas anteriormente.


##<a id="channel"></a>Configuraciones de entrada de canal (ingesta)

###<a id="Ingest_Protocols"></a>Protocolo de streaming de ingesta

Si el **Tipo de codificador** está establecido en **Estándar**, las opciones válidas son:

- **RTP** (MPEG-TS): secuencia de transporte MPEG-2 a través de RTP.  
- **RTMP** de una sola velocidad de bits
- **MP4 fragmentado** de una sola velocidad de bits (Smooth Streaming)

Para obtener más información, consulte [Compatibilidad con RTMP de Servicios multimedia de Azure y codificadores en directo](http://go.microsoft.com/fwlink/?LinkId=532824).

####RTP (MPEG-TS): secuencia de transporte MPEG-2 a través de RTP.  

Caso de uso típico:

Los emisores profesionales suelen trabajar con codificadores locales en directo de tecnología avanzada (de proveedores como Elemental Technologies, Ericsson, Ateme, Imagine o Envivio) para enviar secuencias. Estos suelen usarse conjuntamente con un departamento de TI y redes privadas.

Consideraciones:

- Se recomienda encarecidamente el uso de una entrada de secuencias de transporte de un solo programa (SPTS). Sin embargo, se admite el uso de varias pistas de audio de idioma.
- La secuencia de vídeo debe tener una velocidad de bits media inferior a 15 Mbps.
- La suma de la velocidad de bits media de las secuencias de audio debe ser inferior a 1 Mbps.
- A continuación, se indican los códecs admitidos:
	- MPEG-2/H.262 Video 
		
		- Perfil Principal (4:2:0)
		- Perfil Alta (4:2:0, 4:2:2)
		- Perfil 422 (4:2:0, 4:2:2)

	- MPEG-4 AVC/H.264 Video
	
		- Perfil Línea de base, Principal, Alta (4:2:0 de 8 bits)
		- Perfil Alta 10 (4:2:0 de 10 bits)
		- Perfil Alta 422 (4:2:2 de 10 bits)


	- MPEG-2 AAC-LC Audio
	
		- Mono, estéreo, Surround (5.1, 7.1)
		- Empaquetado ADTS estilo MPEG-2

	- Audio Dolby Digital (AC-3)

		- Mono, estéreo, Surround (5.1, 7.1)

	- Audio MPEG (nivel II y III)
			
		- Mono, estéreo

- Entre los codificadores de difusión recomendados se incluyen:
	- Ateme AM2102
	- Ericsson AVP2000
	- eVertz 3480
	- Ericsson RX8200
	- Imagine Communications Selenio ENC 1
	- Imagine Communications Selenio ENC 2
	- AdTec EN-30
	- AdTec EN-91P
	- AdTec EN-100
	- Harmonic ProStream 1000
	- Thor H-2 4HD-EM
	- eVertz 7880 SLKE
	- Cisco Spinnaker
	- Elemental Live

####<a id="single_bitrate_RTMP"></a>RTMP de una sola velocidad de bits

Consideraciones:

- La secuencia de entrada no puede contener vídeo de varias velocidades de bits.
- La secuencia de vídeo debe tener una velocidad de bits media inferior a 15 Mbps.
- La secuencia de audio debe tener una velocidad de bits media inferior a 1 Mbps.
- A continuación, se indican los códecs admitidos:

	- MPEG-4 AVC/H.264 Video  
	
		- Perfil Línea de base, Principal, Alta (4:2:0 de 8 bits)
		- Perfil Alta 10 (4:2:0 de 10 bits)
		- Perfil Alta 422 (4:2:2 de 10 bits)

	- MPEG-2 AAC-LC Audio

		- Mono, estéreo, Surround (5.1, 7.1)
		- Velocidad de muestreo 44,1 kHz
		- Empaquetado ADTS estilo MPEG-2
	
- Entre los codificadores recomendados se incluyen:

	- Telestream Wirecast
	- Flash Media Live Encoder
	- Tricaster

####MP4 fragmentado de una sola velocidad de bits (Smooth Streaming)

Caso de uso típico:

Use codificadores locales en directo de proveedores como Elemental Technologies, Ericsson, Ateme y Envivio para enviar el flujo de entrada a través de Internet abierto a un centro de datos de Azure cercano.

Consideraciones:

Las mismas aplicables al apartado [RTMP de una sola velocidad de bits](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

####Otras consideraciones

- No se puede cambiar el protocolo de entrada mientras el canal o sus programas asociados se están ejecutando. Si necesita diferentes protocolos, debe crear canales independientes para cada protocolo de entrada. 
- La resolución máxima de la secuencia de vídeo entrante es de 1920 x 1080, con un máximo de 60 campos por segundo, cuando es entrelazada, o 30 fotogramas por segundo, si es progresiva.


###Direcciones URL de ingesta (extremos) 

Un canal proporciona un extremo de entrada (dirección URL de ingesta) que usted especifica en el codificador en directo, de modo que el codificador puede insertar secuencias en sus canales.

Puede obtener las direcciones URL de ingesta al crear un canal. Para obtener estas direcciones URL, el canal no puede encontrarse en el estado **En ejecución**. Cuando esté listo para comenzar a insertar datos en el canal, este debe estar **En ejecución**. Una vez que el canal empieza a consumir datos, puede obtener una vista previa de la secuencia a través de la dirección URL de vista previa.

Tiene la opción de consumir una secuencia en directo de MP4 fragmentado (Smooth Streaming) a través de una conexión SSL. Para introducir en SSL, asegúrese de actualizar la dirección URL de introducción a HTTPS.

###Direcciones IP permitidas

Puede definir las direcciones IP permitidas para publicar vídeo en el canal. Dichas direcciones se pueden especificar como dirección IP individual (por ejemplo, ‘10.0.0.1’), un intervalo de direcciones IP mediante una dirección IP y una máscara de subred CIDR (por ejemplo, ‘10.0.0.1/22’) o un intervalo de direcciones IP mediante una dirección IP y una máscara de subred decimal con puntos (por ejemplo, ‘10.0.0.1(255.255.252.0)’).

Si no se especifican direcciones IP y no hay ninguna definición de regla, no se permitirá ninguna dirección IP. Para permitir las direcciones IP, cree una regla y establezca 0.0.0.0/0.


##Vista previa de canal 

###Direcciones URL de vista previa

Los canales proporcionan un extremo de vista previa (dirección URL de vista previa) que se puede utilizar para obtener una vista previa y validar la secuencia antes de mayor procesamiento y entrega.

Puede obtener la dirección URL de vista previa al crear el canal. Para obtenerla, el canal no puede encontrarse en el estado **En ejecución**.

Una vez que el canal empieza a consumir datos, puede obtener una vista previa de la secuencia.

**Nota** Actualmente la secuencia de vista previa solo se puede entregar en formato MP4 fragmentado (Smooth Streaming), independientemente del tipo de entrada especificado. Puede usar el reproductor [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) para probar el formato Smooth Stream. También puede usar un reproductor hospedado en el Portal de administración de Azure para ver la secuencia.

###Direcciones IP permitidas

Puede definir las direcciones IP permitidas para conectarse al extremo de vista previa. Si no se especifica ninguna dirección IP, se permitirá cualquier dirección IP. Dichas direcciones se pueden especificar como dirección IP individual (por ejemplo, ‘10.0.0.1’), un intervalo de direcciones IP mediante una dirección IP y una máscara de subred CIDR (por ejemplo, ‘10.0.0.1/22’) o un intervalo de direcciones IP mediante una dirección IP y una máscara de subred decimal con puntos (por ejemplo, ‘10.0.0.1(255.255.252.0)’).

##Configuración de la codificación en directo

En esta sección se describe cómo configurar los valores del codificador en directo dentro del canal cuando el **Tipo de codificación** del canal se establece en **Estándar**.

###Origen de marcador de anuncio

Puede especificar el origen de las señales de los marcadores de anuncio. El valor predeterminado es **Api**, que indica que el codificador en directo del canal debe escuchar una **API de marcadores de anuncio** asincrónica.

La otra opción válida es **Scte35** (solo permitida si el protocolo de streaming de ingesta está establecido en RTP (MPEG-TS). Cuando se especifica Scte35, el codificador en directo analizará las señales de SCTE-35 del flujo de entrada RTP (MPEG-TS).

###Subtítulos CEA 708

Marca opcional que indica al codificador en directo que omita cualquier dato de los subtítulos CEA 708 insertado en el vídeo entrante. Si la marca está establecida en false (valor predeterminado), el codificador detectará y volverá a insertar los datos de CEA 708 en las secuencias de vídeo salientes.

###Secuencia de vídeo

Opcional. Describe la secuencia de vídeo de entrada. Si este campo no se especifica, se usa el valor predeterminado. Este valor solo se permite si el protocolo de streaming de entrada está establecido en RTP (MPEG-TS).

####Índice

Índice basado en cero que especifica qué secuencia de vídeo de entrada debe procesar el codificador en directo dentro del canal. Esta configuración solo se aplica si el protocolo de streaming de ingesta es RTP (MPEG-TS).

El valor predeterminado es cero. Se recomienda enviar una secuencia de transporte de un solo programa (SPTS). Si la secuencia de entrada contiene varios programas, el codificador en directo analiza la tabla de asignación de programas (PMT) en la entrada, identifica las entradas con un nombre de tipo de secuencia MPEG-2 Video o H.264 y las organiza en el orden especificado en la tabla PMT. A continuación, se usa el índice basado en cero para seleccionar la entrada concreta en esa disposición.

###Secuencia de audio

Opcional. Describe las secuencias de audio de entrada. Si este campo no se especifica, se aplican los valores predeterminados. Este valor solo se permite si el protocolo de streaming de entrada está establecido en RTP (MPEG-TS).

####Índice

Se recomienda enviar una secuencia de transporte de un solo programa (SPTS). Si la secuencia de entrada contiene varios programas, el codificador en directo del canal analiza la tabla de asignación de programas (PMT) en la entrada, identifica las entradas con un nombre de tipo de secuencia MPEG-2 AAC ADTS, AC-3 System-A, AC-3 System-B, MPEG-2 Private PES, MPEG-1 Audio o MPEG-2 Audio y las organiza en el orden especificado en la tabla PMT. A continuación, se usa el índice basado en cero para seleccionar la entrada concreta en esa disposición.

####Idioma

El identificador de idioma de la secuencia de audio, conforme a ISO 639-2, por ejemplo, ENG. Si no aparece, el valor predeterminado es UND (sin definir).

Pueden especificarse hasta 8 conjuntos de secuencias de audio si la entrada al canal es MPEG-2 TS a través de RTP. Sin embargo, no puede haber dos entradas con el mismo valor para Índice.

###<a id="preset"></a>Valor preestablecido del sistema

Especifica el valor preestablecido que usará el codificador en directo dentro de este canal. Actualmente, el único valor permitido es **Default720p** (valor predeterminado).

**Default720p** codificará el vídeo en las 7 capas siguientes.


####Secuencia de vídeo de salida

<table border="1">
<tr><th>Velocidad de bits</th><th>Ancho</th><th>Alto</th><th>Fotogramas/seg. máx.</th><th>Perfil</th><th>Nombre secuencia salida</th></tr>
<tr><td>3500</td><td>1280</td><td>720</td><td>30</td><td>Alto</td><td>Video_1280x720_30fps_3500kbps</td></tr>
<tr><td>2200</td><td>960</td><td>540</td><td>30</td><td>Principal</td><td>Video_960x540_30fps_2200kbps</td></tr>
<tr><td>1350</td><td>704</td><td>396</td><td>30</td><td>Principal</td><td>Video_704x396_30fps_1350kbps</td></tr>
<tr><td>850</td><td>512</td><td>288</td><td>30</td><td>Principal</td><td>Video_512x288_30fps_850kbps</td></tr>
<tr><td>550</td><td>384</td><td>216</td><td>30</td><td>Principal</td><td>Video_384x216_30fps_550kbps</td></tr>
<tr><td>350</td><td>340</td><td>192</td><td>30</td><td>Línea base</td><td>Video_340x192_30fps_350kbps</td></tr>
<tr><td>200</td><td>340</td><td>192</td><td>30</td><td>Línea base</td><td>Video_340x192_30fps_200kbps</td></tr>
</table>

####Secuencia de audio de salida

El audio se codifica como estéreo AAC-LC a 64 kbps, con una frecuencia de muestreo de 44,1 kHz.

##Señalización de anuncios

Si el canal tiene habilitada la codificación en directo, dispone de un componente en la canalización de procesamiento de vídeo y puede manipularlo. Puede señalar que el canal inserte pizarras o anuncios en la secuencia de velocidad de bits adaptable saliente. Las pizarras son imágenes estáticas que puede usar para cubrir la fuente de entrada directa en determinados casos (por ejemplo, durante una pausa comercial). Las señales de anuncio son señales sincronizadas temporalmente que se insertan en la secuencia saliente para indicar al reproductor de vídeo que realice una acción determinada, por ejemplo, cambiar a un anuncio en el momento adecuado. Consulte este [blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) para obtener información general sobre el mecanismo de señalización SCTE-35 usado para este fin. A continuación se muestra un escenario típico que puede implementar en el evento en directo.

1. Haga que los visores obtengan una imagen ANTERIOR AL EVENTO antes de que este empiece.
1. Haga que los visores obtengan una imagen POSTERIOR AL EVENTO una vez que este finalice.
1. Haga que los visores obtengan una imagen de ERROR DEL EVENTO si hay algún problema durante el mismo (por ejemplo, un corte de luz en el estadio).
1. Envíe una imagen de PAUSA COMERCIAL para ocultar la fuente del evento en directo durante una pausa publicitaria.

A continuación se indican las propiedades que puede establecer al señalar anuncios.

###Duración

La duración, en segundos, de la pausa comercial. Para que se inicie la pausa comercial, este debe ser un valor positivo distinto de cero. Si hay una pausa comercial en curso, la duración está establecida en cero y el identificador de pila coincide con el de dicha pausa comercial, la pausa se cancela.

###Identificador de pila

Identificador único de la pausa comercial que la aplicación correspondiente usará para emprender las acciones adecuadas. Debe ser un entero positivo. Puede establecer este valor en un número entero positivo aleatorio o usar un sistema ascendente para realizar un seguimiento de los identificadores de pila. Asegúrese de normalizar los identificadores como enteros positivos antes de enviar a través de la API.

###Mostrar pizarra

Opcional. Indica al codificador en directo que cambie a la [pizarra predeterminada](media-services-manage-live-encoder-enabled-channels.md#default_slate) durante una pausa comercial y ocultar la fuente de vídeo entrante. El audio también está desactivado durante el uso de la pizarra. El valor predeterminado es **false**.
 
La imagen usada será la especificada mediante la propiedad Id del recurso de pizarra predeterminado en el momento de la creación del canal. La pizarra se estira para ajustarse al tamaño de la imagen en pantalla.


##Insertar imágenes de pizarra

Puede señalarse al codificador en directo del canal que cambie a una imagen de pizarra. También puede se le puede indicar que finalice una pizarra en curso.

El codificador en directo puede configurarse para cambiar a una imagen de pizarra y ocultar la señal de vídeo entrante en determinadas situaciones, por ejemplo, durante una pausa de anuncio. Si no se ha configurado ninguna pizarra de este tipo, el vídeo de entrada no se enmascara durante esa pausa.

###Duración

La duración de la pizarra en segundos. Para iniciar la pizarra, este debe ser un valor positivo distinto de cero. Si hay una pizarra en curso y se especifica una duración de cero, dicha pizarra se terminará.

###Insertar pizarra en marcador de anuncio

Cuando está establecido en true, este valor configura el codificador en directo para insertar una imagen de pizarra durante una pausa de anuncio. El valor predeterminado es true.

###<a id="default_slate"></a>Id. del recurso de pizarra predeterminado

Opcional. Especifica el identificador del recurso de Servicios multimedia que contiene la imagen de pizarra. El valor predeterminado es null.

**Nota**: antes de crear el canal, la imagen de pizarra debe cargarse con las siguientes restricciones como recurso dedicado (no debe haber ningún otro archivo en este recurso).

- Máximo 1920x1080 de resolución.
- Al menos 3 MB de tamaño.
- El nombre de archivo debe tener una *extensión .jpg. - La imagen se debe cargar en un recurso como el único AssetFile en ese recurso y este AssetFile debe marcarse como el archivo principal. El recurso no se puede almacenar cifrado.

Si no se especifica el **Id. del recurso de pizarra predeterminado** y el valor **Insertar pizarra en marcador de anuncio** está establecido en **true**, se usará una imagen de Servicios multimedia de Azure predeterminada para ocultar la secuencia de vídeo de entrada. El audio también está desactivado durante el uso de la pizarra.


##Programas del canal

Un canal está asociado a programas que le permiten controlar la publicación y el almacenamiento de segmentos en una secuencia en directo. Los canales administran los programas. La relación entre canales y programas es muy similar a los medios tradicionales, donde un canal tiene un flujo constante de contenido y un programa se enfoca a algún evento programado en dicho canal.

Puede especificar la cantidad de horas que desea conservar el contenido grabado del programa en la configuración de la duración de **Ventana de archivo**. Este valor se puede establecer desde un mínimo de cinco minutos a un máximo de 25 horas. La duración de la ventana de archivo también indica el tiempo máximo que los clientes pueden buscar hacia atrás a partir de la posición en vivo actual. Los programas pueden transmitirse durante la cantidad de tiempo especificada, pero el contenido que escape de esa longitud de ventana se descartará continuamente. El valor de esta propiedad también determina durante cuánto tiempo los manifiestos de cliente pueden crecer.

Cada programa se asocia a un recurso que almacena el contenido transmitido por streaming. Un recurso se asigna a un contenedor de blobs en la cuenta de almacenamiento de Azure y los archivos del recurso se almacenan como blobs en ese contenedor. Para publicar el programa a fin de que los clientes puedan ver la secuencia, debe crear un localizador a petición para el recurso asociado. Contar con este localizador le permitirá crear una dirección URL de streaming que puede proporcionar a sus clientes.

Un canal es compatible con hasta tres programas en ejecución simultánea, por lo que puede crear varios archivos del mismo flujo entrante. Esto le permite publicar y archivar distintas partes de un evento, según sea necesario. Por ejemplo, el requisito de su empresa es solo archivar seis horas de un programa, pero difundir solo los últimos diez minutos. Para lograrlo, necesita crear dos programas en ejecución simultánea. Un programa está definido para archivar seis horas del evento, pero no está publicado. El otro programa está definido para archivar durante diez minutos y este programa sí se publica.

No debe volver a usar programas existentes para eventos nuevos. En su lugar, cree e inicie un programa nuevo para cada evento como se describe en la sección Programación de aplicaciones de streaming en vivo.

Inicie el programa cuando esté listo para iniciar el streaming y el archivo. Detenga el programa cuando quiera detener el streaming y el archivo del evento.

Para eliminar contenido archivado, detenga y elimine el programa y, a continuación, elimine el recurso asociado. No se puede eliminar un recurso si se usa un programa; primero se debe eliminar el programa.

Incluso después de detener y eliminar el programa, los usuarios podrán transmitir el contenido archivado como un vídeo a petición siempre que no elimine el recurso.

Si desea conservar el contenido archivado, pero no hacerlo disponible para la transmisión, elimine el localizador de streaming.


##Obtención de una vista previa en miniatura de una fuente directa

Si la codificación en directo está habilitada, puede obtener una vista previa de la fuente directa cuando llega al canal. Esta puede ser una herramienta muy valiosa para comprobar si la fuente directa llega realmente al canal.

##<a id="states"></a>Estados del canal y cómo se asignan los estados al modo de facturación 

El estado actual de un canal. Los valores posibles son:

- **Detenido**. Este es el estado inicial del canal después de su creación. En este estado, se pueden actualizar las propiedades del canal pero no se permite el streaming.
- **Iniciando**. El canal se está iniciando. No se permiten actualizaciones ni streaming durante este estado. Si se produce un error, el canal vuelve al estado Detenido.
- **En ejecución**. El canal es capaz de procesar secuencias en directo.
- **Deteniéndose**. El canal se está deteniendo. No se permiten actualizaciones ni streaming durante este estado.
- **Eliminando**. El canal se está eliminando. No se permiten actualizaciones ni streaming durante este estado.

En la tabla siguiente se muestra cómo se asignan los estados del canal al modo de facturación.
 
<table border="1">
<tr><th>Estado del canal</th><th>Indicadores IU del portal</th><th>¿Facturado?</th></tr>
<tr><td>Iniciando</td><td>Iniciando</td><td>No (estado transitorio)</td></tr>
<tr><td>En ejecución</td><td>Listo (no hay programas en ejecución)<br/>o<br/>Streaming (al menos un programa en ejecución)</td><td>Sí</td></tr>
<tr><td>Deteniéndose</td><td>Deteniéndose</td><td>No (estado transitorio)</td></tr>
<tr><td>Detenido</td><td>Detenido</td><td>No</td></tr>
</table>


>[AZURE.NOTE]Actualmente en versión preliminar; el inicio del canal puede tardar hasta 20 minutos. El restablecimiento de canal puede tardar hasta 5 minutos.


##<a id="Considerations"></a>Consideraciones

- No se puede cambiar el protocolo de entrada mientras el canal o sus programas asociados se están ejecutando. Si necesita diferentes protocolos, debe crear canales independientes para cada protocolo de entrada. 
- Cada vez que vuelva a configurar el codificador en directo, llame al método **Restablecer** en el canal. Antes de restablecer el canal, debe detener el programa. Después de restablecer el canal, reinicie el programa. 
- Un canal se puede detener solo cuando está en el estado En ejecución y se han detenido todos los programas en el canal.
- De forma predeterminada solo puede agregar 5 canales a su cuenta de Servicios multimedia. Esta es una cuota de advertencia a todas las cuentas nuevas. Para obtener más información, consulte [Cuotas y limitaciones](media-services-quotas-and-limitations.md).
- No se puede cambiar el protocolo de entrada mientras el canal o sus programas asociados se están ejecutando. Si necesita diferentes protocolos, debe crear canales independientes para cada protocolo de entrada.
- Solo se le cobrará cuando el canal esté en estado **En ejecución**. Para obtener más información, consulte [esta](media-services-manage-live-encoder-enabled-channels.md#states) sección.

##Problemas conocidos

- El inicio del canal puede tardar más de 20 minutos.
- La compatibilidad con RTP está dirigida a los operadores de radiodifusión profesionales. Revise las notas sobre RTP en [este](http://azure.microsoft.com/blog/2015/04/13/an-introduction-to-live-encoding-with-azure-media-services/) blog.
- Las imágenes de pizarra deben cumplir las restricciones descritas [aquí](media-services-manage-live-encoder-enabled-channels.md#default_slate). Si intenta crear un canal con una pizarra predeterminada que sea superior a 1920x1080, la solicitud terminará por producir un error.


##<a id="tasks"></a>Tareas relacionadas con el streaming en vivo

###Creación de una cuenta de Servicios multimedia

[Creación de una cuenta de Servicios multimedia](media-services-create-account.md).

###Configuración de extremos de streaming

Para obtener información general acerca de los extremos de streaming e información acerca de cómo administrarlos, vea [Administración de extremos de streaming en una cuenta de Servicios multimedia](media-services-manage-origins.md).

###Configuración del entorno de desarrollo  

Elija **.NET** o **API de REST** para el entorno de desarrollo.

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

###Conexión mediante programación  

Elija **.NET** o **API de REST** para conectarse mediante programación a los Servicios multimedia de Azure.

[AZURE.INCLUDE [media-services-selector-connect](../../includes/media-services-selector-connect.md)]

###Creación de canales que realizan la codificación en directo desde una secuencia de una sola velocidad de bits a una secuencia de velocidad de bits adaptable 

Elija **Portal**, **.NET** o **API de REST** para ver cómo crear y administrar los canales y programas.

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

###Protección de recursos

**Información general**:

[Protección de contenido (PlayReady)](media-services-content-protection-overview.md)

Si desea cifrar un recurso asociado a un programa con Estándar de cifrado avanzado (AES) (mediante claves de cifrado de 128 bits) o DRM de PlayReady, debe crear una clave de contenido.

Use **.NET** o **API de REST** para crear claves.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

Una vez creada la clave de contenido, puede configurar la directiva de autorización de claves mediante **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

####Integración con socios

[Uso de castLabs para entregar licencias de DRM a Servicios multimedia de Azure](media-services-castlabs-integration.md)


###Publicación y entrega de recursos

**Información general**:

- [Información general sobre el empaquetado dinámico](../media-services-dynamic-overview.md)


Configure la directiva de entrega de recursos con **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Publique recursos (creando localizadores) mediante el **Portal de administración de Azure** o **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


Entrega de contenido

> [AZURE.SELECTOR]
- [Overview](media-services-deliver-content-overview.md)

###Habilitación de CDN de Azure

Servicios multimedia admite la integración con CDN de Azure. Para obtener información sobre cómo habilitar CDN de Azure, consulte [Administración de extremos de streaming en una cuenta de Servicios multimedia](media-services-manage-origins.md#enable_cdn).

###Escalado de una cuenta de Servicios multimedia

Puede escalar **Servicios multimedia** especificando el número de **Unidades reservadas de streaming** que desea aprovisionar en su cuenta.

Para obtener información sobre el escalado de unidades de streaming, consulte: [Cómo escalar unidades de streaming](media-services-manage-origins.md#scale_streaming_endpoints.md).

##Temas relacionados

[Entrega de eventos de streaming en vivo con Servicios multimedia de Azure](media-services-live-streaming-workflow.md)

[Conceptos de Servicios multimedia de Azure](media-services-concepts.md)

[Especificación de la introducción en directo de MP4 fragmentado de Servicios multimedia de Azure](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png
 

<!---HONumber=July15_HO4-->