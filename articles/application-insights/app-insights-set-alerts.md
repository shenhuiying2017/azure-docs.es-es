<properties 
	pageTitle="Definición de alertas en Application Insights" 
	description="Reciba mensajes de correo electrónico sobre bloqueos, excepciones y cambios de métrica." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/17/2015" 
	ms.author="awills"/>
 
#  Definición de alertas en Application Insights

[Visual Studio Application Insights][start] pueden alertarle de los cambios en las métricas de rendimiento o de uso de la aplicación.

Application Insights supervisa la aplicación activa en una [amplia variedad de plataformas][platforms] para ayudarle a diagnosticar problemas de rendimiento y entender los patrones de uso.

Hay dos tipos de alertas:
 
* Las **pruebas web** le informan de que el sitio no está disponible en Internet o responde lentamente. [Más información][availability].
* Las **alertas de métricas** le avisan cuando una métrica cruza un valor de umbral durante un período determinado: por ejemplo, recuentos de error, memoria o vistas de página. 

Hay una [página aparte sobre las pruebas web][availability], por lo que aquí nos centraremos en las alertas de métricas.

##  Alertas de métricas

Si aún no ha configurado Application Insights para su aplicación, [hágalo primero][start].

Para recibir un correo electrónico cuando una métrica cruce un umbral, inicie desde el Explorador de métricas o desde el icono Reglas de alerta en la hoja de información general.

![En la hoja Reglas de alerta, elija Agregar alerta. Establezca la aplicación como el recurso que se va a medir, proporcione un nombre para la alerta y elija una métrica.](./media/app-insights-set-alerts/01-set-metric.png)

Establezca el recurso antes de las demás propiedades. No elija los recursos de la prueba web si desea establecer alertas en las métricas de rendimiento o de uso.

Asegúrese de tener en cuenta las unidades en las que se le pide que escriba el valor de umbral.

*No veo el botón Agregar alerta.* ¿Está usando una cuenta de organización? Puede establecer alertas si tiene acceso de propietario o colaborador a este recurso de aplicación. Eche un vistazo a Configuración -> Usuarios. [Más información sobre el control de acceso][roles].

##  Visualización de alertas

Las alertas se le envían por correo electrónico.

También aparecen en el registro de eventos de operaciones:

![En la hoja Información general, cerca de la parte inferior, haga clic en 'Eventos de la semana pasada'.](./media/app-insights-set-alerts/09-alerts.png)

*¿Estos "eventos" están relacionados con eventos de telemetría o eventos personalizados?*

* No. Estos eventos operativos son simplemente un registro de cosas que han ocurrido en este recurso de aplicación. 

##  Alertas de disponibilidad

Puede configurar pruebas web que prueben cualquier sitio web desde diferentes puntos alrededor del mundo. [Más información][availability].

##  ¿Qué alertas es conveniente establecer?

Depende de la aplicación. Para empezar, es mejor no establecer demasiadas métricas. Observe durante un tiempo sus gráficos de métrica mientras se ejecuta la aplicación para hacerse una idea de cómo se comporta normalmente. Esto le ayudará a encontrar maneras de mejorar su rendimiento. A continuación, configure alertas para que le avisen cuando las métricas salgan de la zona normal.

Las alertas más populares son:

* Las [pruebas web][availability] son importantes si la aplicación es un servicio web o un sitio web que está visible en Internet de acceso público. Le indican si el sitio deja de funcionar o responde lentamente, incluso si el problema es del operador y no de la aplicación. Sin embargo, son pruebas sintéticas, por lo que no miden la experiencia real de los usuarios.
* Las [métricas de explorador][client], especialmente los tiempos de carga de página del explorador, son buenas para aplicaciones web. Si la página tiene una gran cantidad de scripts, deberá tener en cuenta las excepciones de explorador. Para obtener estas métricas y alertas, tiene que configurar [la supervisión de páginas web][client].
* Tiempo de respuesta de servidor y solicitudes incorrectas para el lado servidor de aplicaciones web. Además de configurar alertas, eche un vistazo a estas métricas para ver si varían desproporcionadamente con tasas de solicitud altas: esto puede indicar que la aplicación se está quedando sin recursos.



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md

 
<!--HONumber=62-->