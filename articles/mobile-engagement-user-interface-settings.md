<properties 
   pageTitle="Interfaz de usuario de Azure Mobile Engagement: configuración" 
   description="Obtenga información acerca de cómo administrar la configuración global de la aplicación mediante Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="piyushjo"/>

# Cómo administrar la configuración global de la aplicación
Las opciones del menú Configuración disponibles para una aplicación varían, dependiendo de la plataforma de la aplicación y de los permisos que se le hayan concedido para la aplicación. Entre la configuración se incluyen los siguientes ajustes: Detalles, Proyectos, Inserción nativa, Velocidad de inserción, SDK, Seguimiento, Información de la aplicación, Presión comercial y Permisos. Solamente la opción del menú Información de la aplicación de la sección de configuración de la interfaz de usuario contiene elementos que se pueden administrar con la función "Etiqueta" de la API del dispositivo. "Glosario" en "Conceptos" incluye las definiciones de términos y abreviaturas, como las siguientes: APNS, GCM, IDFA, API, SDK, clave de API, clave de SDK, identificador (Id. de aplicación) de la aplicación, identificador de AppStore, plan de etiquetas, identificador de usuario, identificador de dispositivo, delegado de aplicaciones, seguimiento de la pila y vinculación en profundidad.

### Consulte también
- [Documentación de API - API de dispositivo][Link 4], [Conceptos - Glosario][Link 6], [Guía de resolución de problemas - Servicio][Link 24]

  ![settings1][46]

## Detalles
Le permite cambiar el nombre y la descripción de la aplicación y ver el propietario de la aplicación y los permisos del rol. Configuración de análisis: permite ver o cambiar el día en que empieza la semana y el tiempo de retención en días
 
  ![settings2][47]
 
## Proyectos
Le permite seleccionar todos los proyectos en los que desea que aparezca la aplicación. También puede buscar un proyecto y ver el nombre, descripción, propietario y los permisos del rol de cualquier proyecto del que forme parte la aplicación.

### Consulte también
-    [Documentación de interfaz de usuario: página principal][Link 13]
 
  ![settings3][48]

## Inserción nativa:
Permite registrar un nuevo certificado o la eliminación de un certificado existente para usar con la inserción nativa. Inserción nativa permite a Azure Mobile Engagement insertar en su aplicación en cualquier momento, aunque no se esté ejecutando. Después de proporcionar las credenciales o certificados para al menos un servicio de inserción nativa, se puede seleccionar "Cualquier hora" al crear campañas de cobertura y también usar el parámetro "notifier" en la API de INSERCIÓN.

### Consulte también
- [Documentación de API - API de Cobertura][Link 4],[Documentación de la API - API de inserción][Link 4], [Documentación de la interfaz de usuario - Cobertura - Nueva campaña de inserción][Link 27]

### Servicio de notificaciones de inserción de Apple (APNS)
Para habilitar la inserción nativa mediante el servicio de notificación de inserción de Apple deberá registrar su certificado. Deberá especificar el tipo de certificado como de desarrollo (DEV) o de producción (PROD). A continuación, necesitará cargar su certificado y la contraseña.

### Consulte también
- [Documentación del SDK - iOS - Cómo preparar la aplicación para las notificaciones de inserción de Apple][Link 5]
 
![settings4][49]
 
### Servicio de notificación de inserción de Windows (WPNS)
Para habilitar la inserción nativa mediante el servicio de notificación de Windows, debe proporcionar las credenciales de la aplicación. Necesitará el identificador de seguridad del paquete (SID) y la clave secreta.
 
![settings5][50]
 
### Mensajería en la nube de Google para Android (GCM)
Para habilitar la inserción nativa mediante GCM, deberá seguir las instrucciones de Google. A continuación, debe pegar una clave de API simple de servidor configurada sin restricciones de IP. Requiere la integración con el SDK de Android v1.12.0 +.

### Consulte también
- [Documentación del SDK - Android - Cómo integrar GCM][Link 5], [Programador de Google - Guía de GCM](http://developer.android.com/guide/google/gcm/gs.html), [Programador de Google - Documentación de GCM](http://developer.android.com/google/gcm/index.html)
 
### Mensajería de dispositivos de Amazon para Android (ADM)
Para habilitar la inserción nativa con ADM, debe proporcionar <OAuth credentials> de Amazon compuesta de un identificador de cliente y el secreto del cliente (requiere la integración con el SDK de Android v2.1.0+).

### Consulte también
- [Documentación del SDK - Android - Cómo integrar ADM][Link 5], [Desarrollador de Amazon - Documentación de ADM](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## Velocidad de inserción
Muestra la velocidad de inserción actual de la aplicación y le permite definir la velocidad de inserción de la aplicación. La velocidad de inserción define el número máximo de inserciones por segundo que se llevará a cabo el módulo de cobertura. Esto puede resultar útil en situaciones en las que sus propios servidores están sobrecargados por demasiadas solicitudes por segundo después de la activación de una campaña.
 
  ![settings7][52]

## Seguimiento
La función de seguimiento le permite realizar un seguimiento de los orígenes de las instalaciones de las aplicaciones para iOS y Android. Le permite saber dónde descargaron los usuarios la aplicación (es decir, de qué tienda de aplicaciones) y qué origen los llevó hasta allí (es decir, campaña de publicidad, blog, sitio web, correo electrónico, SMS, etc.). La función de seguimiento de Azure Mobile Engagement debe estar integrada en la aplicación desde el SDK como un paso independiente.

### Consulte también
- [Documentación del SDK - Android - Cómo integrar][Link 5], [Documentación del SDK - iOS - Cómo integrar][Link 5]
 
### Tiendas
Le permiten registrar todas las tiendas desde las que se puede descargar la aplicación en función de sus nombres y su direcciones URL de descarga asociadas. Esto permite crear ubicaciones de alojamiento de URL de seguimiento. Las tiendas se pueden crear o eliminar desde esta página. Utilizando el icono para crear una nueva dirección URL de seguimiento, será llevado a la página direcciones URL de seguimiento que se trata más adelante. Hay varias maneras de seguir dónde descargan los usuarios su aplicación:

-    Utilice un servidor de publicidad de terceros (actualmente admite Azure Mobile Engagement [SmartAd](http://smartadserver.fr/) y [Surikate](http://www.surikate.com/).
-    Use un servicio de atribución de terceros (actualmente Azure Mobile Engagement admite el [seguimiento de aplicaciones móviles](http://www.mobileapptracking.com/) y es posible que [Trademob](http://www.trademob.com/) también se admita próximamente).
-    Use un sitio de referencia de instalación de terceros (actualmente Azure Mobile Engagement admite el [origen de referencia de instalación de Google Play](https://developers.google.com/app-conversion-tracking/docs/third-party-trackers/) - solo para Android).
-    Utilice la generación de informes manual.
 
  ![settings8][53]
 
### Campañas publicitarias
Permite crear nuevas campañas de anuncios compuestas de un nombre de servidor de publicidad, un identificador de campaña y el origen en el que se puede descargar la aplicación.
 
  ![settings9][54]
 
### URL de seguimiento
Permite generar direcciones URL de seguimiento para usar como URL de destino en sus orígenes (campaña de publicidad, blog, sitio web, correo electrónico, SMS, etc.) que puede redirigir a los usuarios a las tiendas donde pueden descargar la aplicación. También le permite mostrar todas las direcciones URL de seguimiento que ya ha creado. Una dirección URL de seguimiento puede utilizarse como URL de acción de una campaña de publicidad o un anuncio de cobertura para invitar a los usuarios a descargar una de sus aplicaciones desde otra aplicación. Una URL de seguimiento redirecciona a la URL de descarga asociada a la tienda seleccionada, permitiendo que nuestro sistema de seguimiento registre la tienda desde la que se descarga la aplicación en la instalación. Si se proporciona un origen, nuestro sistema de seguimiento también lo registra, lo que permite distinguir entre las diversas campañas creadas para su aplicación.

Crear una nueva dirección URL de seguimiento requiere que especifique una tienda en el origen de ninguno, cliente o agregar servidor.

-    Un origen de Ninguno crea una URL de seguimiento predeterminada.
-    Un origen de Personalizado le permite especificar una dirección URL en un servidor externo para descargar la aplicación.
-    Un origen de servidor de anuncios crea una URL de seguimiento predeterminada en un servidor de publicidad de nombre predeterminado.
 
### Consulte también
- [Documentación de la interfaz de usuario - Alcance - Nueva campaña de inserción][Link 27] 
 
### Crear una dirección URL de seguimiento
También puede generar una dirección URL de seguimiento que permita a los usuarios descargar una de las aplicaciones desde dentro de la sección contenido de una nueva campaña de cobertura.

### Consulte también
- [Documentación de la interfaz de usuario - Cobertura - Insertar contenido][Link 29]

![settings10][55]

## Información de la aplicación:
Permite registrar información adicional asociada a los usuarios de su aplicación. Esta información puede insertarse por parte de la aplicación (mediante el SDK) o por parte del backend (mediante la API del dispositivo).

### Consulte también
- [Documentación de API: API de dispositivos][Link 4]

Registrar etiquetas de información de aplicaciones permite segmentar sus campañas cobertura mediante la creación de criterios específicos de audiencia de cobertura basados en ella. Puede ver el nombre y tipo de las etiquetas de información de aplicación existentes o agregar nueva información de aplicación según el nombre y el tipo de cadena, fecha, número entero o valor booleano.

### Consulte también
- [Documentación de la interfaz de usuario - Alcance - Nueva campaña de inserción][Link 27]
 
![settings11][56]
 
## Presión comercial
Las cuotas de inserción permiten definir el número máximo de veces que se puede insertar un dispositivo durante un período. Las cuotas de inserción solo son utilizadas por las campañas de cobertura que tienen habilitada la opción "Aplicar cuotas de inserción". Las cuotas de inserción pueden administrarse por segmento o de forma predeterminada. Las cuotas se pueden establecer para que solo envíen un número máximo de inserciones en un período deslizante de última hora, día, semana o mes.

### Consulte también
- [Documentación de UI - Cobertura - Nueva campaña de inserción][Link 27], [Documentación de la interfaz de usuario - Segmentos][Link 18]

### Cuotas:
- Cuota predeterminada: esta cuota se aplica a los usuarios que no coinciden con ningún segmento de la sección «Cuota por segmento» siguiente. De forma predeterminada, no se establece ninguna cuota.
- Cuota por segmento: como cuota se debe aplicar a un grupo de usuarios, deberá crear un segmento para identificar a los usuarios a los que aplicar una cuota. Podría ser un segmento de «usuarios habituales» definido por el número de sesiones que realizan o cualquier cosa que considere interesante para definir el grupo de usuarios. Si un dispositivo coincide con varios segmentos con una cuota definida, solo se aplica la cuota con el mayor número máximo de inserciones por hora.

![settings12][57]
 
## Permisos
Permite buscar y ver el correo electrónico, nombre, organización y nivel de permisos de usuarios de la aplicación. El concepto de permisos es una adición al rol del proyecto. Permite asociar un conjunto de permisos a un usuario específico que tenga acceso a la aplicación.

### Los niveles de permisos actuales disponibles son:
-    "Creador de campaña de cobertura" (usuario que puede crear campañas de cobertura) 
-    "Administrador de campaña de cobertura" (un usuario que puede crear campañas de cobertura y activarlas, suspenderlas, finalizarlas o destruirlas)

> Nota: Cuando un usuario tiene un rol del proyecto y un conjunto de permisos para una aplicación determinada, se utiliza el concepto más permisivo. Por lo tanto, se recomienda que al usar permisos establezca el rol de proyecto de los usuarios en "Visor" (el rol menos permisivo) y agregar más permisos permisivos en el nivel de aplicación.

### Consulte también
- [Documentación de interfaz de usuario: página principal][Link 13]  
 
![settings13][58]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-how-tos-first-push.md
[Link 28]: mobile-engagement-how-tos-test-campaign.md
[Link 29]: mobile-engagement-how-tos-personalize-push.md
[Link 30]: mobile-engagement-how-tos-differentiate-push.md
[Link 31]: mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: mobile-engagement-how-tos-text-view.md
[Link 33]: mobile-engagement-how-tos-web-view.md

<!--HONumber=54-->