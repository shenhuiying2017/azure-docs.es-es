<properties 
	pageTitle="Application Insights: detección proactiva de anomalías" 
	description="Application Insights realiza un análisis profundo de la telemetría de aplicación y le advierte de los posibles problemas." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/21/2015" 
	ms.author="awills"/>

#  Application Insights: detección proactiva de anomalías

*Application Insights se encuentra en su versión de vista previa.*


Application Insights realiza un análisis profundo de la telemetría de aplicación y puede advertirle de los posibles problemas de rendimiento. Probablemente esté leyendo esto porque ha recibido una de nuestras alertas de anomalías por correo electrónico.

## Sobre la alerta de anomalía

* *¿Por qué he recibido esta alerta?*
 * Application Insights analiza periódicamente los datos con reglas de reconocimiento de patrones. Busca anomalías que pudieran indicar problemas de rendimiento de su aplicación.
* *¿La notificación significa que tengo definitivamente un problema?*
 * No. Simplemente es una sugerencia sobre algo que puede que desee examinar con más detenimiento. 
* *¿Cuál debo hacer?*
 * [Mire los datos que se presentan](#responding-to-an-alert) y piense si podrían representar un problema. De lo contrario, todo está correcto.
* *¿Entonces examinan mis datos?*
 * No, el servicio es completamente automático. Sólo obtendrá las notificaciones. Sus datos son [privados](app-insights-data-retention-privacy.md).


## El proceso de detección

* *¿Qué tipos de anomalías se detectan?*
 * Patrones que llevarían mucho tiempo de comprobar por usted mismo. Por ejemplo, un bajo rendimiento en una combinación específica de ubicación, hora del día y plataforma.
* *¿Puedo crear mis propias reglas de detección de anomalías?*
 * No para este tipo de análisis detallado. (Pero puede [configurar alertas](app-insights-alerts.md) que le indiquen cuándo una métrica cruza un umbral.)
* *¿Con qué frecuencia se lleva a cabo el análisis?*
 * No realizamos análisis en un recurso de aplicación que no obtenga mucha telemetría. Es poco probable que obtenga advertencias sobre sus sesiones de depuración.


## Responder a una alerta

Abra el informe de anomalías desde el correo electrónico o desde la lista de anomalías.

![](./media/app-insights-anomaly/02.png)

Aviso:

* La descripción
* La instrucción de impacto, que le indica cuántos usuarios se ven afectados y con qué frecuencia.

Haga clic en un gráfico para abrir una hoja que tenga más detalle.

Modifique los filtros y el intervalo de tiempo para explorar la telemetría.




## Mensajes de correos electrónico de notificación

* *¿Es necesario suscribirse a este servicio para recibir notificaciones?*
 * No. Nuestro bot analiza periódicamente los datos de todos los usuarios de Application Insights y envía notificaciones si detecta problemas.
* *¿Puedo cancelar la suscripción u hacer que mis colegas reciban las notificaciones?*
 * Haga clic en el vínculo de la alerta o el correo electrónico. Abra la configuración de anomalías. ![](./media/app-insights-anomaly/01.png) Actualmente se envían a los usuarios que tengan [acceso de escritura al recurso de Application Insights](app-insights-resources-roles-access-control.md).
* *No me quiero ver inundado por estos mensajes*.
 * Se limitan a tres al día. No obtendrá repeticiones de ningún mensaje.
* *Si no hago nada, ¿obtendré un aviso?*
 * No, obtendrá un mensaje sobre cada problema solo una vez.
* *He perdido el mensaje de correo electrónico. ¿Dónde puedo encontrar las notificaciones en el portal?*
 * En la información general de Application Insights de su aplicación, haga clic en la ventana **Anomalías**. 






 

<!---HONumber=August15_HO6-->