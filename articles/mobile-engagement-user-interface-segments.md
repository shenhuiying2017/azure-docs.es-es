<properties 
   pageTitle="Interfaz de usuario de Azure Mobile Engagement: segmentos" 
   description="Información general de la interfaz de usuario de la sección de segmentos de Azure Mobile Engagement" 
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

# Segmentos
 
La sección de segmentos de la interfaz de usuario permite trabajar en la segmentación de los usuarios según los diferentes comportamientos y análisis que puede obtener de la aplicación y a los que también puede tener acceso a través de la API de segmentos. Los segmentos se calculan por primera vez 24 horas después de crearse y se vuelven a calcular cada 24 horas, según la información de análisis más reciente. Una vez que se calcula un segmento, muestra un gráfico de "Historial de día a día" cada día.

**Consulte también:**

- [API de documentación - API de segmentos][Link 4], [Guía de solución de problemas - Análisis][Link 2]

Puede crear un segmento basándose en hasta 10 criterios en un período concreto hasta 60 días en el pasado desde la sección de análisis. 
Por ejemplo, puede crear un segmento basándose en las personas que han consultado ciertas páginas o buscado contenido específico dentro de la aplicación en los últimos 10 días. Esta información está disponible en la sección de análisis. Por lo tanto, se puede utilizar para crear un segmento y, a continuación, configurar una notificación de inserción para orientarla a este subconjunto para que vuelvan a la aplicación. 
 
> Nota: Una vez que se ha calculado un segmento, no se puede editar; solo se puede clonar (copiar) o destruir (eliminar). Los segmentos se pueden clonar dentro de la misma aplicación (con el mismo AppID) y también se pueden clonar en otras aplicaciones (con un AppID diferente). 
 
 ![segments1][35] 

## Ejemplos de segmentación: 

 ![segments2][36]

Los segmentos permiten segmentar a los usuarios finales de la aplicación.
La segmentación de los usuarios es una estrategia de marketing importante. Azure Mobile Engagement le permite obtener datos históricos y crear segmentos personalizados. Esta poderosa herramienta permite para obtener información acerca de la experiencia de los clientes en la aplicación. Puede analizar fácilmente los segmentos y utilizarlos como destinos de inserción.
Un caso de uso común es en el que desea enviar una notificación para animar a los usuarios finales a calificar la aplicación en la tienda. En lugar de enviar una notificación a todos los usuarios finales, puede crear un segmento que especificará solo los usuarios que han utilizado la aplicación todos los días durante el último mes y que han tenido una gran experiencia de usuario. Cuando se envían menos notificaciones de inserción de mayor grado de orientación, se obtiene una mayor rentabilidad.
 
 ![segments3][37]

**Segmentos de ejemplo que puede crear en función de los elementos principales de Azure Mobile Engagement:**

- Evento: cree un segmento orientado a un evento específico de la aplicación que haya ocurrido más de dos veces por semana. 
- Sesión: cree un segmento de los usuarios que haya utilizado la aplicación más de 5 veces la semana pasada.
- Actividad: cree un segmento de los usuarios que hayan utilizado una página o contenido más o menos de 10 horas el último mes.
- Trabajo: cree un segmento de usuarios que ha completado un trabajo más de dos veces al día.
- Bloqueo: cree un segmento de todos los usuarios que hayan tenido un bloqueo más de 10 veces la semana pasada. (Puede insertar este segmento con una disculpa o incluso un cupón).
- Error: cree un segmento de todos los usuarios que han tenido un error más de 100 veces los últimos 3 días.
- Información de la aplicación: cree un segmento que tenga como destino información de aplicación personalizada que se produjese durante los últimos 25 días.
 
 ![segments4][38]

Para usar el segmento de forma óptima, debe realizar una integración personalizada del SDK en la aplicación con un plan de marcado de etiquetas "Información de la aplicación".
A continuación, vaya a la página principal de la interfaz, seleccione la aplicación que desee y haga clic en la sección "Segmentos".

1. Seleccione la sección "Segmentos".
2. Haga clic en el botón "Nuevo segmento" para crear un nuevo segmento.

**Ejemplo: Cree un segmento simple basado en la información de "Sesión".**

Crear un segmento de todos los usuarios finales que han utilizado la aplicación al menos 50 veces en la última semana. Desde allí, busque solo los usuarios finales que han invertido al menos 30 segundos en la aplicación por sesión. Esto mostrará todos los usuarios finales que tengan una experiencia positiva en la aplicación. A continuación, el segmento creado puede usarse para insertar una notificación a estos usuarios finales para pedirles que valoren la aplicación en la tienda.
 
 ![segments5][39]

1. Asigne un nombre de segmento para encontrarlo rápidamente en la lista "Segmento".
2. Haga clic en el botón "Crear".
 
 ![segments6][40]

Seleccione la sesión.
 
 ![segments7][41]

1. Seleccione el periodo de "Semana pasada".
2. Haga clic en Siguiente.
 
 ![segments8][42]

1. Seleccione el operador relevante en la lista: =; ≥, ≤.
2. Escriba el recuento que desee.
3. Seleccione la instancia que desee. 
4. Haga clic en Siguiente.
Este ejemplo se establece para que en la última semana, detecte a los usuarios que han realizado al menos 50 sesiones.
 
 ![segments9][43]

Para la segmentación de la sesión, puede elegir la duración por sesión como criterio.

1. Seleccione el operador de la lista.
2. Proporcione la duración por sesión.
3. Haga clic en Next.
En este ejemplo, dice que en todas las sesiones que han sido segmentadas en la sección de repetición, seleccione solo a los usuarios que han pasado más de 30 segundos por sesión.
 
 ![segments10][44]

Asigne un nombre al criterio para recuperarlo en el embudo completo y haga clic en Finalizar.
 
 ![segments11][45]

Cuando haya terminado de configurar su criterio, aparecerá en el embudo de segmento.
Dado que los segmentos se basan en un segmento de datos de análisis, los segmentos se calculan una vez al día.
En este ejemplo, el 47,7% del total de usuarios finales coincide con el criterio. Deben ser los usuarios que han tenido una buena experiencia y que es posible que proporcionen una clasificación superior si les inserta una notificación en la que se les solicite que califiquen la aplicación en la tienda.

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
