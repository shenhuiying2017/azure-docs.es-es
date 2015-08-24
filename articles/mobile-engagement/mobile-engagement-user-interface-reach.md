<properties 
   pageTitle="Interfaz de usuario de Azure Mobile Engagement: cobertura" 
   description="Obtenga información acerca de cómo llegar a los usuarios de su aplicación mediante notificaciones de inserción usando Azure Mobile Engagement" 
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
   ms.date="08/10/2015"
   ms.author="piyushjo"/>


# Cómo llegar a los usuarios de su aplicación mediante notificaciones de inserción
La sección de cobertura de la interfaz de usuario es la herramienta de administración de campaña de inserción en la que puede crear/editar/activar/finalizar/supervisar y obtener estadísticas de las campañas de notificaciones de inserción y funciones a las que también se puede acceder a través de la API de cobertura (y algunos elementos de la API de inserción de bajo nivel). Recuerde que tanto si está utilizando las API o la interfaz de usuario, deberá integrar Azure Mobile Engagement y la cobertura en la aplicación para cada plataforma con el SDK para poder utilizar campañas de cobertura.

### Consulte también
-  [Documentación de API - API de Cobertura][Link 4], [Documentación de API - API de inserción][Link 4], [Guía de resolución de problemas - Inserción/Cobertura][Link 23]
-  [Cobertura - Campaña][Link 27], [Cobertura - Criterio][Link 28], [Cobertura - Contenido][Link 29], [Cobertura - Guía práctica][Link 3]
 
## Cuatro tipos de notificaciones de inserción
1.    Anuncios: le permiten enviar mensajes de publicidad a los usuarios que los redireccionan a otra ubicación dentro de la aplicación o enviarlos a una página web o tienda fuera de la aplicación. 
2.    Sondeos: le permiten reunir información de los usuarios finales formulándoles preguntas.
3.    Inserciones de datos: le permiten enviar un archivo de datos binario o base64. La información contenida en una inserción de datos se envía a la aplicación para modificar la actual experiencia del usuario en la aplicación. La aplicación debe ser capaz de procesar los datos de una inserción de datos.
4.    Mosaicos (solo en Windows Phone): le permiten utilizar el servicio de notificaciones de inserción de Microsoft (MPNS) para enviar la inserción de Windows nativa que contiene datos XML. (Compatible desde la versión 0.9.0. del SDK La carga final de los mosaicos final no puede superar los 32 kilobytes).

### Consulte también
-  [Conceptos - Glosario][Link 6]

## Tres categorías de estadísticas en tiempo real mostradas para cada campaña
1.    Insertado: ¿cuántas inserciones se han enviado según los criterios especificados en la campaña? 
2.    Respondido: ¿cuántos usuarios han reaccionado a la notificación abriéndola desde fuera de la aplicación o cerrándola en la aplicación. 
3.    Ejecutados: cuántos usuarios han hecho clic en el vínculo de la notificación para ser redirigidos a una nueva ubicación de la aplicación, a una tienda o a un explorador web. 

> Nota: Hay estadísticas de campaña más detalladas disponibles desde las estadísticas de la API de cobertura

### Consulte también
-  [Conceptos - Glosario][Link 6], [Documentación de la API - API de cobertura - Estadísticas][Link 4]


## Detalles de la campaña
Puede editar, clonar, eliminar o activar las campañas que no se han activado todavía pasando el mouse sobre sus nombres o puede hacer clic para abrirlas. Se pueden clonar las campañas que ya se han activado pasando el mouse sobre sus nombres o puede hacer clic para abrirlas. Sin embargo, no puede cambiar una campaña una vez activada.
 
![Reach1][18]

## Comentarios sobre la cobertura
Puede cambiar de la vista de detalles a la vista de estadísticas de una campaña abierta que ya se ha activado y cambiar de la vista simple a la vista avanzada de las estadísticas para ver más información detallada (según los permisos). También puede utilizar la información de comentarios de la cobertura de una campaña anterior como criterios de orientación de una nueva campaña. Las estadísticas de comentarios de cobertura también se pueden recopilar mediante "Estadísticas" de la API de cobertura. También puede personalizar el público de las campañas de inserción en función de las campañas anteriores.


### Consulte también 
-  [Documentación de UI - Cobertura - Nueva campaña de inserción][27], [Documentación de la API - API de cobertura - Estadísticas][Link 4]

![Reach2][19]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 

<!---HONumber=August15_HO7-->