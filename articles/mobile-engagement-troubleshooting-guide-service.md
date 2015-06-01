<properties 
   pageTitle="Guía de solución de problemas de Azure Mobile Engagement - Servicio" 
   description="Guías de solución de problemas de Azure Mobile Engagement" 
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


# Guía de solución de problemas de servicio

Los siguientes son posibles problemas que pueden producirse con cómo Azure Mobile Engagement se ejecuta.

## Interrupciones del servicio

### Problema
- Problemas que parecen deberse a interrupciones del servicio de Azure Mobile Engagement.

### Causas
- Los problemas que parecen deberse a interrupciones del servicio de Azure Mobile Engagement pueden deberse a diversos problemas:
    - Problemas aislados que originalmente parecen sistémicos de todo Azure Mobile Engagement
    - Problemas conocidos causados por interrupciones de los servidores (no siempre se muestran en el estado del servidor):
	- Retrasos de la programación, errores de orientación, problemas de actualización de distintivos, detención de la recopilación de estadísticas, detención del funcionamiento de las API, imposibilidad de crear nuevas aplicaciones o usuarios, errores de DNS y errores de tiempo de espera en la interfaz de usuario, en la API o en las aplicaciones de un dispositivo.
    - Interrupciones de dependencia en la nube [Estado del servicio Azure](http://status.azure.com/), [Estado de los servicios web de Amazon (AWS)](http://status.aws.amazon.com/) 
    - Interrupciones de dependencia de los servicios de notificaciones de inserción (PNS) [Google - Servicio](http://www.google.com/appsstatus#hl=en&v=status), [Apple - Servicio](http://www.apple.com/support/systemstatus/), [Android -  Google GCM](http://developer.android.com/google/gcm/index.html), [Android - Amazon ADM](https://developer.amazon.com/appsandservices/apis/engage/device-messaging), [Apple - APNS](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), [Windows Phone: WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx), [Windows Phone: MPNS][LinkMPNS], [Windows - WNS](https://developer.windows.com/)
    - Interrupciones de App Store [GooglePlay](https://play.google.com/), [iTunes](http://www.apple.com/itunes/charts/), [Tienda de Windows Phone](http://www.windowsphone.com/), [Tienda Windows](http://windows.microsoft.com/)

*Para comprobar si el problema es sistémico puede probar la misma función desde otro:*

    - Azure Mobile Engagement integrated application
    - Test device
    - Test device OS version
    - Campaign
    - Administrator user account
    - Browser (IE, Firefox, Chrome, etc.)
    - Computer
*Para probar si el problema solo afecta a la interfaz de usuario o a la API:*

    - Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[Documentación de la API][Link 4], [documentación de la interfaz de usuario][Link 1]

*Para probar si el problema está relacionado con la red de telefonía móvil:*

    - Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
    - Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

*Para probar si el problema está relacionado con el dispositivo:*

    - Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
    - Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
    - Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[Documentación de la interfaz de usuario - Configuración][Link 1]

*Para probar si el problema está relacionado con la aplicación:*

    - Install and test your application from an emulator instead of from a physical device:
        - Download and use Android SDK (includes an Android Emulator)
        - Download and use Apple Xcode (includes an iOS Simulator)
        - Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

Para probar si el problema está relacionado con las actualizaciones del sistema operativo de los dispositivos del usuario final, que requieren una actualización SDK para resolver:

    - Test your application on different devices with different versions of the OS.
    - Confirm that you are using the most recent version of the SDK.
[Guía de solución de problemas: SDK][Link 2]
 
## Problemas de conectividad e información incorrecta

### Problema
- Problemas para iniciar sesión en la interfaz de usuario de Azure Mobile Engagement.
- Errores de conexión con la API de Azure Mobile Engagement.
- Problemas al cargar etiquetas de información de aplicación a través de la API del dispositivo.
- Problemas de descarga de registros o datos exportados desde Azure Mobile Engagement.
- Información incorrecta que se muestra en la interfaz de usuario de Azure Mobile Engagement.
- Información incorrecta que se muestra en los registros de Azure Mobile Engagement.

### Causas
- Para los problemas de conectividad con Azure Mobile Engagement:
    - Confirme que su cuenta de usuario tiene permisos suficientes para realizar la tarea.
    - Confirme que el problema no se limita a un equipo o a la red local.
    - Confirme que el servicio de Azure Mobile Engagement no tiene interrupciones registradas.
    - Confirme que los archivos de la etiqueta de información de la aplicación siguen todas estas reglas:
        - Use únicamente el juego de caracteres UTF8 (no se admite el juego de caracteres ANSI).
        - Use una coma "," como carácter separador (puede abrir una solicitud de servicio para solicitar cambiar el carácter separador de .csv de una coma "," a otro carácter como un punto y coma ";").
        - Use minúsculas para los valores booleanos "true" y "false".
        - Use un archivo que de tamaño inferior al tamaño máximo de archivo de 35 MB.

### Consulte también

[Documentación de la API][Link 4], [Documentación de la interfaz de usuario - Inicio][Link 1]
 
## Solicitud de función

### Problema
- La función que desea utilizar no parece existir todavía en Azure Mobile Engagement.

### Causas

Para sugerir una nueva función de Azure Mobile Engagement que no existe, abra una solicitud de servicio de Azure Mobile Engagement con tantos detalles como sea posible sobre qué función nueva que le gustaría ver en Azure Mobile Engagement.

### Consulte también

[Comentarios y solicitudes de funciones de Mobile Engagement](http://feedback.azure.com/forums/285737-mobile-engagement)

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
[LinkMPNS]: http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105).aspx

<!--HONumber=54-->