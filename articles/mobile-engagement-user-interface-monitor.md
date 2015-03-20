<properties 
   pageTitle="Interfaz de usuario de Azure Mobile Engagement: supervisión" 
   description="Información general de la interfaz de usuario para la sección de supervisión de Azure Mobile Engagement" 
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

# Monitor
 
La sección de supervisión de la interfaz de usuario proporciona información de análisis en tiempo real y le permite establecer alertas cuando se alcanzan los umbrales de la mayor parte de la misma información que está disponible históricamente en la sección "Análisis" de la interfaz de usuario. "Glosario" en "Conceptos" tiene las definiciones de los términos y abreviaturas de Análisis y Supervisión, como las siguientes: Usuario activo, nuevo usuario, usuario conservado, sesión, gráfico de ruta de acceso de usuario, asignación de usuarios, direcciones URL de seguimiento, tendencias, actividad, evento, trabajo, error, información adicional, bloqueo e información de la aplicación.

**Consulte también:** 

-  [Conceptos - Glosario][Link 6], [Guía de solución de problemas - Análisis][Link 2]

## Supervisión - Sesiones, trabajos, eventos, errores y bloqueos:
Puede ver cuántos usuarios están actualmente en sesión y en pantallas específicas o realizando acciones específicas. Puede ver la actividad de los usuarios dividida en sesiones, trabajos, eventos, errores y bloqueos. Puede ver la información actual y mostrar la información de la última hora, día o semana. Puede ver toda la información de cada categoría u ordenar por sesión, trabajo, evento, error y bloqueo específico.  La supervisión activa es útil para utilizar durante eventos como una campaña de inserción para ver si hay un repunte en la acción justo después de enviar la notificación de inserción. 
 
![Monitor1][14]  

## Solución de problemas con Supervisión - Eventos - Detalles:
Generar un evento en la aplicación desde el dispositivo de prueba y encontrarlo en Supervisión - Eventos - Detalles es una de las maneras más fáciles de encontrar el identificador del dispositivo para el dispositivo de prueba y confirmar que la integración de Azure Mobile Engagement de Análisis, Segmentos y Supervisión está ejecutándose desde su aplicación. Una vez que dispone del identificador de dispositivo del dispositivo de prueba, puede agregarlo a sus dispositivos de prueba en "Mi cuenta - Dispositivos". Si no se puede generar un evento, asegúrese de que Azure Mobile Engagement está integrado correctamente en la aplicación Android/iOS/Web/Windows/Windows Phone con el SDK.

**Consulte también:** 

-  [Documentación del SDK][Link 5]

![Monitor2][15]  

## Solución de problemas con Supervisión - Bloqueos - Detalles:
Puede revisar la información de bloqueos sobre la aplicación desde Supervisión - Bloqueos - Detalles para ayudar a determinar por qué se bloquea la aplicación. También debería buscar problemas conocidos relacionados con cada versión del SDK en las notas de la versión para cada versión del SDK de Android/iOS/Web/Windows/Windows Phone. 

**Consulte también:** 

-  [Documentación del SDK - Notas de la versión][Link 5]

![Monitor3][16] 

## Supervisión - Alertas:
También puede especificar condiciones para las alertas que se le enviarán automáticamente por correo electrónico o mensajes instantáneos. (Los servicios compatibles con XMPP como GTalk de Google o iChat de Apple son compatibles). Las alertas se basan en un umbral de detección predefinido mayor que (>) o menor que (<) un número específico de sesiones, trabajos, eventos, errores o bloqueos por hora, minuto o segundo. Las alertas pueden supervisar todas las actividades de un tipo determinado o supervisar un trabajo, evento o actividad de error específico. También puede especificar una tasa de detección mínima, que es la cantidad mínima de tiempo que separa dos notificaciones para la misma alerta para asegurarse de que cuando se desencadene la alerta, nunca recibirá más de una notificación cada X minutos.
 
![Monitor4][17]

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
