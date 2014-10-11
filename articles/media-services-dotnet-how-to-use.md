<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use Media Services" authors="" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Uso de Servicios multimedia

En esta guía se muestra cómo empezar a programar con Servicios multimedia de Azure. La guía consta de un conjunto de artículos e incluye una introducción técnica a Servicios multimedia, pasos para configurar la cuenta de Azure para Servicios multimedia, una guía de configuración para el desarrollo y temas que muestran cómo llevar a cabo tareas de programación típicas. Entre los escenarios demostrados se encuentran los siguientes: carga, cifrado, codificación y entrega de recursos. Los ejemplos se escriben en C# y usan el SDK de Servicios multimedia para .NET. Para obtener más información acerca de Servicios multimedia de Azure, consulte el tema [Pasos siguientes][].

También puede programar Servicios multimedia con las API de REST basadas en OData. Puede generar una aplicación que haga llamadas de la API de REST a Servicios multimedia desde lenguajes .NET u otros lenguajes de programación. Para obtener una serie de documentación completa sobre la programación con la API de REST de Servicios multimedia, consulte [Building Applications with the Azure Media Services REST API][].

Para comenzar a programar con la API de REST de Servicios multimedia o el SDK de Servicios multimedia, primero habilite su cuenta de Azure para Servicios multimedia como se describe en la sección [Configuración de una cuenta de Azure para Servicios multimedia][].

La documentación del SDK de Servicios multimedia más actualizada se encuentra [aquí][].

## <a name="what-are"></a><span class="short header">¿Qué son los Servicios multimedia?</span>

Los Servicios multimedia de Azure forman una plataforma multimedia extensible que integra lo mejor de la plataforma multimedia de Microsoft y los componentes multimedia de terceros en Azure. Los Servicios multimedia proporcionan un proceso en la nube que permite a los socios del sector ampliar o reemplazar las tecnologías de componentes. Los vendedores independientes de software y los proveedores multimedia pueden usar Servicios multimedia para generar soluciones multimedia de un extremo a otro. Esta introducción describe la arquitectura general y los escenarios de desarrollo comunes para Servicios multimedia.

El siguiente diagrama muestra la arquitectura de Servicios multimedia.

![Arquitectura de Servicios multimedia][]

### Compatibilidad de las características de Servicios multimedia

La versión actual de Servicios multimedia ofrece el siguiente conjunto de características para el desarrollo de aplicaciones multimedia en la nube.

-   **Introducción**. Las operaciones de introducción introducen los recursos en el sistema, por ejemplo mediante su carga y cifrado antes de que se coloquen en el servicio de almacenamiento de Azure. Servicios multimedia ofrece integración con los componentes de socios para proporcionar soluciones de carga de UDP (protocolo de datagramas de usuario).
-   **Codificación**. Las operaciones de codificación incluyen la codificación, transformación y conversión de recursos multimedia. Puede ejecutar tareas de codificación en la nube con el codificador multimedia de Azure. Las opciones de codificación incluyen las siguientes:
-   Use el codificador multimedia de Azure y trabaje con una amplia variedad de códecs y formatos estándar, incluidos la Transmisión por secuencias suave IIS líder del sector, MP4 y la conversión a Transmisión por secuencias en directo HTTP de Apple.
-   Convierta bibliotecas enteras o archivos individuales con un control total sobre la entrada y la salida.
-   Para consultar una gran cantidad de tipos de archivo compatibles, formatos y códecs, consulte [Códecs y tipos de archivo compatibles con Azure Media Encoder][].
-   Conversiones de formato compatibles. Los Servicios multimedia le permiten convertir ISO MP4 (.mp4) a Formato de archivo de transmisión por secuencias suave (PIFF 1.3) (.ismv; .isma). También puede convertir Formato de archivo de transmisión por secuencias suave (PIFF) a Transmisión por secuencias en directo HTTP de Apple (.msu8, .ts).
-   **Protección**. La protección de contenido implica el cifrado de la transmisión en directo o contenido bajo demanda de cara a la seguridad en la entrega, el almacenamiento y el transporte. Los Servicios multimedia ofrecen una solución independiente de la tecnología DRM para proteger el contenido. Las tecnologías DRM compatibles actualmente son Microsoft PlayReady y cifrado común de MPEG. También habrá compatibilidad con tecnologías DRM adicionales.
-   **Transmisión**. La transmisión de contenido implica el envío de este en directo o bajo demanda a los clientes, o puede descargar archivos multimedia específicos desde la nube. Los Servicios multimedia ofrecen una solución independiente del formato para el contenido por secuencias. Los Servicios multimedia proporcionan compatibilidad con el origen de la transmisión en formatos MP4, Transmisión por secuencias en directo HTTP de Apple y Transmisión por secuencias suave. Se ofrecerá compatibilidad con formatos adicionales. También puede entregar directamente contenido por secuencias usando un CDN de terceros, que permite la opción de realizar una escalación a millones de usuarios.

### Escenarios de desarrollo de Servicios multimedia

Los Servicios multimedia son compatibles con escenarios de desarrollo multimedia comunes, como se describe en la siguiente tabla.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">

<thead>

<tr>

<th>
Escenario

</th>

<th>
Descripción

</th>

</tr>

</thead>

<tbody>

<tr>

<td>
Creación de flujos de trabajo de un extremo a otro

</td>

<td>
Genere flujos de trabajo multimedia completos en toda la nube. Desde la carga de medios a la distribución de contenido, los Servicios multimedia ofrecen una serie de componentes que pueden combinarse para administrar flujos de trabajo específicos de aplicaciones. Entre las capacidades actuales se encuentran la carga, el almacenamiento, el cifrado, la conversión de formato, la protección de contenido y la entrega de transmisión bajo demanda.

</td>

</tr>

<tr>

<td>
Creación de flujos de trabajo híbridos

</td>

<td>
Puede integrar los Servicios multimedia con procesos y herramientas existentes. Por ejemplo, codifique el contenido in situ y, a continuación, cárguelo en los Servicios multimedia para realizar la transcodificación en varios formatos y entregue a través de CDN de Azure o un CDN de otro proveedor. Los Servicios multimedia pueden llamarse individualmente a través de las API de REST estándar para la integración con servicios y aplicaciones externas.

</td>

</tr>

<tr>

<td>
Ofrecimiento de compatibilidad en la nube para reproductores multimedia

</td>

<td>
Puede crear, administrar y entregar contenido multimedia en varios dispositivos multimedia (incluidos los dispositivos iOS, Android y Windows) y distintas plataformas.

</td>

</tr>

</tbody>

</table>
</p>
### <a name="media-client"></a>Desarrollo de clientes de Servicios multimedia

Amplíe el alcance de la solución de Servicios multimedia mediante los SDK y los marcos de reproductores para generar aplicaciones clientes multimedia. Estos clientes son para los desarrolladores que deseen generar aplicaciones de Servicios multimedia que ofrezcan experiencias de usuario convincentes en una gama de dispositivos y plataformas. Según los dispositivos de destino, existen opciones para los SDK y marcos de reproductores disponibles en Microsoft y otros socios.

A continuación se muestra una lista de SDK de clientes y marcos de reproductores compatibles. Para obtener más información sobre estos y otros marcos de reproductores y SDK planificados, y la funcionalidad que pueden admitir, consulte [Desarrollo de clientes de los Servicios multimedia][] en los foros de los Servicios multimedia.

#### Compatibilidad con clientes de PC y Mac

Para los PC y Mac, puede dirigir una experiencia de transferencia mediante Microsoft Silverlight o Adobe Open Source Media Framework.

-   [Cliente de transmisión por secuencias suave para Silverlight][]
-   [Microsoft Media Platform: Player Framework for Silverlight][]
-   [Complemento de transmisión suave para OSMF 2.0][]. Para obtener información sobre el uso de este complemento, consulte [Uso del complemento de transmisión suave para Adobe Open Source Media Framework][].

#### Aplicaciones de Windows 8

Para Windows 8, puede generar aplicaciones de la Tienda Windows mediante una de las construcciones y lenguajes de desarrollo compatibles como HTML, Javascript, XAML, C# y C+.

-   [SDK del cliente de transmisión suave para Windows 8][]. Para obtener más información sobre cómo crear una aplicación de la Tienda Windows con este SDK, consulte [Generación de una aplicación de la Tienda Windows de transmisión suave][]. Para obtener información sobre cómo crear un reproductor de transmisión suave en HTML5, consulte [Tutorial: Generación del primer reproductor de transmisión suave de HTML5][].

-   [Microsoft Media Platform: Player Framework for Windows 8 Windows Store Applications][]

#### Xbox

Xbox es compatible con aplicaciones de Xbox LIVE que consumen contenido de transmisión suave. El kit de desarrollo de aplicaciones (ADK) para Xbox LIVE incluye:

-   Cliente de transmisión suave para ADK de Xbox Live
-   Microsoft Media Platform: Player Framework para ADK de Xbox LIVE

#### Dispositivos dedicados o insertados

Dispositivos como TV, decodificadores (set-top box), reproductores Blu-Ray, soluciones de TV OTT y dispositivos móviles conectados que tengan un marco de desarrollo de aplicaciones personalizado y un proceso multimedia personalizado. Microsoft proporciona los siguientes kits de migración que pueden ser objeto de licencia, y permite a los socios migrar la reproducción de transmisión suave para la plataforma.

-   [Smooth Streaming Client Porting Kit][]
-   [Microsoft PlayReady Device Porting Kit][]

#### Windows Phone

Microsoft proporciona un SDK que puede usarse para crear aplicaciones de vídeo premium para Windows Phone.

-   [Cliente de transmisión por secuencias suave para Silverlight][]
-   [Microsoft Media Platform: Player Framework for Silverlight][]

#### Dispositivos iOS

Para los dispositivos iOS, incluidos iPhone, iPod, e iPad, Microsoft incorpora un SDK que puede usar para generar aplicaciones para estas plataformas y ofrecer contenido de vídeo premium: SDK de transmisión suave para dispositivos iOS con PlayReady. El SDK está disponible solo para los licenciatarios. Para obtener más información, [escriba un correo electrónico a Microsoft][]. Para obtener información sobre el desarrollo de iOS, consulte [iOS Developer Center][].

#### Dispositivos Android

Varios socios de Microsoft incluyen SDK para la plataforma Android que agrega la capacidad de reproducir la transmisión suave en un dispositivo Android. [Escriba un correo electrónico a Microsoft][] para obtener más información sobre los socios.

## Pasos siguientes

Ahora que dispone de una visión general de los Servicios multimedia, diríjase al tema [Configuración de su equipo para Servicios multimedia][].

  [Pasos siguientes]: #next-steps
  [Building Applications with the Azure Media Services REST API]: http://go.microsoft.com/fwlink/?linkid=252967
  [Configuración de una cuenta de Azure para Servicios multimedia]: #setup-account
  [aquí]: http://msdn.microsoft.com/en-us/library/hh973613.aspx
  [Arquitectura de Servicios multimedia]: ./media/media-services-dotnet-how-to-use/wams-01.png
  [Códecs y tipos de archivo compatibles con Azure Media Encoder]: http://msdn.microsoft.com/en-us/library/hh973634
  [Desarrollo de clientes de los Servicios multimedia]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/thread/e9092ec6-2dfc-44cb-adce-1dc935309d2a
  [Cliente de transmisión por secuencias suave para Silverlight]: http://www.microsoft.com/en-us/download/details.aspx?id=29940
  [Microsoft Media Platform: Player Framework for Silverlight]: http://smf.codeplex.com/documentation
  [Complemento de transmisión suave para OSMF 2.0]: http://go.microsoft.com/fwlink/?LinkId=275022
  [Uso del complemento de transmisión suave para Adobe Open Source Media Framework]: http://go.microsoft.com/fwlink/?LinkId=275034
  [SDK del cliente de transmisión suave para Windows 8]: http://go.microsoft.com/fwlink/?LinkID=246146
  [Generación de una aplicación de la Tienda Windows de transmisión suave]: http://go.microsoft.com/fwlink/?LinkId=271647
  [Tutorial: Generación del primer reproductor de transmisión suave de HTML5]: http://msdn.microsoft.com/en-us/library/jj573656(v=vs.90).aspx
  [Microsoft Media Platform: Player Framework for Windows 8 Windows Store Applications]: http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home
  [Smooth Streaming Client Porting Kit]: http://www.microsoft.com/en-us/mediaplatform/sspk.aspx
  [Microsoft PlayReady Device Porting Kit]: http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx
  [escriba un correo electrónico a Microsoft]: mailto:askdrm@microsoft.com
  [iOS Developer Center]: https://developer.apple.com/devcenter/ios/index.action
  [Escriba un correo electrónico a Microsoft]: mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices
  [Configuración de su equipo para Servicios multimedia]: http://go.microsoft.com/fwlink/?LinkId=301751
