<properties 
   pageTitle="Interfaz de usuario de Azure Mobile Engagement: Mi cuenta" 
   description="Información general de la interfaz de usuario de la sección Mi cuenta de Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="mattgre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="required" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure Mobile Engagement: interfaz de usuario

<div class="dev-center-tutorial-selector sublanding">
<a href="../mobile-engagement-user-interface" title="Introduction">Introducción</a>
<a href="../mobile-engagement-user-interface-navigation" title="Navigation">Navegación</a>
<a href="../mobile-engagement-user-interface-home/" title="Home">Inicio</a>
<a href="../mobile-engagement-user-interface-my-account" title="My Account">Mi cuenta</a>
<a href="../mobile-engagement-user-interface-analytics" title="Analytics">Análisis</a>
<a href="../mobile-engagement-user-interface-monitor" title="Monitor">Supervisión</a>
<a href="../mobile-engagement-user-interface-reach" title="Reach">Cobertura</a>
<a href="../mobile-engagement-user-interface-segments" title="Segments">Segmentos</a>
<a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">Panel</a>
<a href="../mobile-engagement-user-interface-settings" title="Settings">Configuración</a>
</div>

# Mi cuenta
 
La sección Mi cuenta de la interfaz de usuario es donde puede ver y cambiar la configuración asociada a su cuenta, incluida la configuración del perfil y probar los ID de dispositivo. Estos valores contienen elementos a los que también se puede acceder mediante la API del dispositivo.

**Consulte también:** 

-  [Guía de solución de problemas - Servicio][Link 2]
 
![MyAccount1][7]  

## Perfil:
Puede ver o cambiar cualquier configuración de la cuenta: Contraseña, Nombre, Apellidos, Organización, Número de teléfono, Zona horaria o Participar o No participar en actualizaciones por correo electrónico. También puede dar a otro usuario permiso para utilizar la aplicación basándose en su dirección de correo electrónico de "inicio".

**Consulte también:** 

-  [Documentación de interfaz de usuario: página principal][Link 13]

![MyAccount2][8]  

## Dispositivos:
Puede ver, agregar o quitar Id. de dispositivo de los dispositivos de prueba que puede usar para probar sus campañas de inserción. Se muestran instrucciones contextuales para encontrar el identificador de dispositivo de los dispositivos de cada plataforma (iOS, Android, Windows Phone, etc.) al hacer clic en "Nuevo dispositivo". 
 
![MyAccount3][9]  
 
Para usar la API de inserción o la API de dispositivo necesitará conocer el identificador único del dispositivo de los usuarios (el parámetro deviceid). Existen varias formas de recuperarlo:
 
1. Desde el backend, puede utilizar la función "Get" de la API del dispositivo para obtener la lista completa de identificadores de dispositivo.
2. Desde su aplicación, puede usar el SDK para obtenerlo. (En Android, llame a la función getDeviceID() de la clase de agente y en iOS, lea la propiedad deviceid de la clase de agente).
3. Desde un anuncio de cobertura, si la URL de la acción asociada con el anuncio contiene el patrón {deviceid}, se reemplazará automáticamente por el identificador del dispositivo que desencadena la acción.
http://<example>.com/registeruser?deviceid={deviceid}&otherparam=myparamdata 
será reemplazado por:
http://<example>.com/registeruser?deviceid=XXXXXXXXXXXXXXXX&otherparam=myparamdata 
4. Desde un anuncio de web de cobertura, si el código HTML del anuncio contiene el patrón {deviceid}, se reemplazará automáticamente por el identificador del dispositivo que muestre el anuncio web.
Este es el identificador de mi dispositivo: deviceId
será reemplazado por:
Este es el identificador de mi dispositivo: XXXXXXXXXXXXXXXX
5.  Abra la aplicación en el dispositivo y realice un evento en la aplicación que se ha etiquetado.
Desde " IU - su aplicación - Supervisión - Detalles", busque el evento que haya realizado en la lista.
Haga clic en este evento en Supervisión.
Debe buscar el identificador de dispositivo en la lista de los dispositivos que han realizado este evento.
A continuación, puede copiar este Id. de dispositivo y registrarlo en "IU - Mi cuenta - Nuevo dispositivo - Seleccione su plataforma de dispositivo".
>(Tenga en cuenta que cuando IDFA está deshabilitado para iOS, es posible que el identificador de dispositivo cambie con el tiempo si desinstala y vuelve a instalar la aplicación).

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
[20]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach/Reach-Content4.png
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
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/


 

<!--HONumber=47-->
