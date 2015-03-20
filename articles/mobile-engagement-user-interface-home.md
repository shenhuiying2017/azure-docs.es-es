<properties 
   pageTitle="Interfaz de usuario de Azure Mobile Engagement: inicio" 
   description="Información general de la interfaz de usuario de la sección de inicio de Azure Mobile Engagement" 
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

# Inicio
 
La sección de inicio de la interfaz de usuario contiene la lista de todas las aplicaciones de Mis aplicaciones, así como la capacidad para conceder a otros usuarios permisos para las aplicaciones. Cualquier persona puede tener acceso a la página principal de la interfaz de usuario mediante la creación de una cuenta, pero deberá conceder a otros usuarios permisos para que tengan acceso a las aplicaciones personalizadas en Mis proyectos.

**Consulte también:** 

-  [Guía de solución de problemas - Servicio][Link 2]
 
## Mis aplicaciones:
 
![Home1][2]

Esta información general rápida de las aplicaciones le permite seleccionar qué aplicación desea abrir para ver las opciones detalladas de la cinta de opciones. Puede hacer clic en el nombre de la aplicación para enviar la ubicación de la cinta de opciones visitada más recientemente en la aplicación, o hacer clic en el icono de engranaje para ir directamente a la página "Configuración" de la aplicación. Puede buscar, filtrar u ordenar la información mostrada en las tablas de las aplicaciones. También puede arrastrar y colocar los encabezados de columna para cambiar el orden. 
 
La información general de las aplicaciones incluye:

- Total de usuarios (número total de usuarios)
- Nueva tendencia de usuarios (evolución de nuevos usuarios durante las últimas dos semanas)
- Usuarios activos (número de usuarios activos durante los últimos 30 días)
- Tendencia de los usuarios activos (evolución de usuarios activos en las últimas dos semanas)
 
También puede ver un gráfico de comparación de las aplicaciones.

- Mostrar gráfico de comparación (puede utilizarse para mostrar los datos de la aplicación en forma de gráfico)
- Casillas de verificación (agregue o quite esta aplicación a o del gráfico de comparación)
 
![Home2][3]

## Mis proyectos:
 
![Home5][6]

Puede utilizar proyectos para agrupar las aplicaciones y conceder permisos a las aplicaciones. El botón Proyectos nuevos permite crear un nuevo proyecto especificando solo un "nombre" y "descripción" del nuevo proyecto. Una vez creado un proyecto, puede hacer clic en el nombre del proyecto para editar el nombre y la descripción del producto y seleccionar todas las aplicaciones que desea ver en este proyecto. También puede eliminar este proyecto, lo cual destruirá las aplicaciones a las que hace referencia. No obstante, perderá acceso a todas las aplicaciones que no le pertenecen y a las que no puede acceder desde otro proyecto. Por lo tanto, tenga cuidado. 
También puede invitar a un usuario a su proyecto en función de su dirección de correo electrónico. Los usuarios necesitan haber creado una cuenta en Azure Mobile Engagement para poder concederles permisos. 

**Entre los roles se incluyen:**

- Visor: Un visor es un usuario que solo puede ver las aplicaciones asociadas a un proyecto. Un visor puede tener acceso a análisis, supervisar datos y examinar los resultados de cobertura. Un visor no puede cambiar la información ni administrar aplicaciones o usuarios. Un visor no puede crear o cambiar el estado de la campaña de cobertura.
- Developer: Un desarrollador es un usuario que puede hacer todo lo que un visor, así como administrar las aplicaciones. Un desarrollador puede habilitar y deshabilitar aplicaciones, cambiar la información de las aplicaciones (como el paquete y la firma) y crear campañas de cobertura. Un desarrollador no puede administrar los usuarios. 
- Administrador: Un administrador es un usuario que puede hacer todo lo que un programador, así como administrar usuarios. Un administrador puede invitar a los usuarios a participar en un proyecto, puede cambiar los roles de usuario y puede cambiar la información del proyecto. Los permisos de nivel de aplicación también pueden establecerse en "configuración".
 
**Consulte también:** 

-  [Documentación de interfaz de usuario - Configuración][Link 20]

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
