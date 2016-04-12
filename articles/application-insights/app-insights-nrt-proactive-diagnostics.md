<properties 
	pageTitle="Diagnóstico proactivo en tiempo casi real en Application Insights" 
	description="El diagnóstico proactivo NRT le notifica automáticamente si el tiempo de respuesta del servidor muestra un comportamiento inusual. No necesita ninguna configuración." 
	services="application-insights" 
    documentationCenter=""
	authors="yorac" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/15/2016" 
	ms.author="awills"/>
 
# Diagnóstico proactivo en tiempo casi real

[Application Insights para Visual Studio](app-insights-overview.md) notifica automáticamente al usuario en tiempo casi real si se detecta un aumento anómalo de la tasa de solicitudes con errores. Para ayudarle a evaluar y diagnosticar el problema, en la notificación se proporciona un análisis de las características de las solicitudes con errores y la telemetría relacionada. También hay vínculos en el portal de Application Insights para obtener un diagnóstico más amplio. La característica no necesita ninguna instalación o configuración, ya que usa algoritmos de aprendizaje automático para predecir la tasa normal de errores.

Esta característica funciona para aplicaciones web ASP.NET y de Java, hospedadas en la nube o en sus propios servidores. También funciona para cualquier aplicación que genere telemetría de solicitudes, por ejemplo, si tiene un rol de trabajo que llama a [TrackRequest()](app-insights-api-custom-events-metrics.md#track-request).

Después de configurar [Application Insights para su proyecto](app-insights-get-started.md), y siempre que la aplicación genere una cantidad mínima determinada de datos de telemetría, el diagnóstico proactivo en tiempo casi real tarda 24 horas en familiarizarse con el comportamiento normal de la aplicación. Después, se activará y podrá enviar alertas.

Esta es una alerta de ejemplo:

![El ejemplo de alerta inteligente muestra el análisis del clúster sobre el error](./media/app-insights-nrt-proactive-diagnostics/010.png)

Observe que le indica:

* La tasa de errores en comparación con el comportamiento normal de la aplicación.
* Cuántos usuarios se ven afectados (para darle una idea de la gravedad).
* Un patrón característico relacionado con los errores. En este ejemplo encontrará un código de respuesta, un nombre de solicitud (operación) y una versión de aplicación concretos. Esto le indica inmediatamente por dónde empezar a buscar en el código. Otras posibilidades pueden ser un sistema operativo cliente o explorador específicos.
* Las excepciones, seguimientos de registros y errores de dependencias (bases de datos u otros componentes externos) que parecen estar asociados con las solicitudes erróneas caracterizadas.
* Los vínculos directos a búsquedas significativas en los datos de telemetría en Application Insights.

Normalmente las [alertas de métricas](app-insights-alerts.md) le comunican que puede haber un problema. Pero el diagnóstico proactivo en tiempo casi real, empieza el trabajo de diagnóstico, realizando muchos de los análisis que, de otra forma, tendría que hacer usted mismo. Los resultados se le presentan claramente organizados, lo que le ayuda a llegar rápidamente a la raíz del problema.

## Cómo funciona

El diagnóstico proactivo en tiempo casi real supervisa la telemetría recibida de su aplicación y, en particular, la tasa de solicitudes con errores. Esta métrica cuenta el número de solicitudes para las que la propiedad `Successful request` es False. De manera predeterminada, `Successful request== (resultCode < 400)` (a no ser que haya escrito código personalizado para [filtrar](app-insights-api-filtering-sampling.md#filtering) o generar sus propias llamadas [TrackRequest](app-insights-api-custom-events-metrics.md#track-request)).

El rendimiento de una aplicación tiene un patrón típico de comportamiento. Algunas solicitudes son más propensas a errores que otras; y la tasa de error general puede aumentar a medida que aumenta la carga. El diagnóstico proactivo en tiempo casi real utiliza el aprendizaje automático para buscar estas anomalías.

A medida que Application Insights recibe telemetría de su aplicación web, el diagnósticos proactivo compara el comportamiento actual con los patrones vistos a lo largo de los últimos días. Si se observa un incremento anómalo de la tasa de errores en comparación con el rendimiento previo, se desencadena un análisis.

Cuando se desencadena un análisis, el servicio realiza un análisis del clúster en la solicitud errónea, para tratar de identificar un patrón de valores que caracterice los errores. En el ejemplo anterior, el análisis ha detectado que la mayoría de los errores giran en torno a un código de resultado, nombre de solicitud, host de URL de servidor e instancia de rol específicos. Por el contrario, el análisis ha descubierto que la propiedad del sistema operativo de cliente se distribuye en varios valores y, por lo tanto, no se muestra.

Si se instrumenta el servicio con estos datos de telemetría, el analizador busca una excepción y un error de dependencia que estén asociados a solicitudes del clúster que identificó, junto con un ejemplo de cualquier registro de seguimiento asociado a estas solicitudes.

El análisis resultante se le envía como una alerta, a no ser que configurara lo contrario.

Al igual que sucede con las [alertas que establece manualmente](app-insights-alerts.md), puede inspeccionar el estado de la alerta y configurarla en la hoja Alertas del recurso de Application Insights. No obstante, a diferencia de otras alertas, no tiene que instalar ni configurar un diagnóstico proactivo en tiempo casi real. Si lo desea, puede deshabilitarla o cambiar sus direcciones de correo electrónico de destino.

## Clasificación y diagnóstico de una alerta

Una alerta indica que se detectó un aumento anómalo de la tasa de solicitudes con errores. Es probable que haya algún problema con la aplicación o con su entorno.

Puede determinar la urgencia del problema a partir del porcentaje de solicitudes y del número de usuarios afectados. En el ejemplo anterior, la tasa de error del 15 % se compara con una tasa normal del 1,3 %, lo que indica que hay algo que no va bien. 22 usuarios diferenciados se vieron afectados por errores en una operación determinada. Si se tratara de su aplicación, podría evaluar la importancia que tiene el problema.

En muchos casos, podrá diagnosticar el problema rápidamente a partir del nombre de la solicitud, las excepciones, los errores de dependencias y otros datos de seguimiento proporcionados.

Hay algunas otras pistas. Por ejemplo, la tasa de error de dependencia en este ejemplo es la misma que la tasa de excepción (89,3 %). Esto sugiere que la excepción se produce directamente desde el error de dependencia, lo que le da una idea clara de dónde puede empezar a buscar en el código.

Pero si necesita investigar más, los vínculos de cada sección le llevarán directamente a una [página de búsqueda](app-insights-diagnostic-search.md) filtrada por las solicitudes, la excepción, la dependencia o el seguimiento relevantes. O bien puede abrir el [Portal de Azure](https://portal.azure.com), navegar hasta el recurso de Application Insights de su aplicación y abrir la hoja Problemas.

En este ejemplo, al hacer clic en el vínculo 'Ver detalles de errores de dependencias' se abre la hoja de búsqueda de Application Insights en la instrucción SQL con la causa principal: se proporcionaron valores NULL en campos obligatorios y, por ello, no se superó la validación durante la operación de guardado.


![Búsqueda de diagnóstico](./media/app-insights-nrt-proactive-diagnostics/051.png)

## Revisar las alertas recientes

Para consultar las alertas en el portal, abra **Configuración, Registros de auditoría**.

![Resumen de alertas](./media/app-insights-nrt-proactive-diagnostics/040.png)

Haga clic en cualquier alerta para ver sus detalles completos.


## Configurar alertas 

Abra la página Alertas. Se incluye el diagnóstico proactivo en cualquier alerta configurada manualmente, y puede ver si se encuentra actualmente en el estado de alerta.

![En la página de información general, haga clic en el icono Alertas. O bien, en cualquier página de métricas, haga clic en el botón Alertas.](./media/app-insights-nrt-proactive-diagnostics/021.png)

Haga clic en la alerta para configurarla.

![Configuración](./media/app-insights-nrt-proactive-diagnostics/031.png)

Observe que puede deshabilitar el diagnóstico proactivo, pero no puede eliminarlo (ni crear otro).


## ¿Cuál es la diferencia ...

El diagnóstico proactivo NRT complementa a otras características diferentes pero similares de Application Insights.

* Las [alertas de métricas](app-insights-alerts.md) las configura el usuario y pueden supervisar una amplia variedad de métricas, como el uso de la CPU, las tasas de solicitudes y los tiempos de carga de las páginas, entre otras. Puede usarlas para avisarle, por ejemplo, de si necesita agregar más recursos. Por el contrario, el diagnóstico proactivo NRT abarca un pequeño conjunto de métricas críticas (actualmente solo la tasa de solicitudes con errores), diseñadas para notificarle en tiempo casi real el momento en el que la tasa de solicitudes con errores de la aplicación web aumenta significativamente en comparación con el comportamiento normal de esta.

    El diagnóstico proactivo en tiempo casi real ajusta automáticamente su umbral en respuesta a condiciones existentes.

    El diagnóstico proactivo en tiempo casi real inicia el trabajo de diagnóstico por usted. 
* La [detección proactiva](app-insights-proactive-detection.md) también usa la inteligencia automática para descubrir patrones inusuales en las métricas, y no requiere ninguna configuración por su parte. Pero, a diferencia del diagnóstico proactivo NRT, la finalidad de la detección proactiva es encontrar segmentos del colector de uso que pudieran haberse servido incorrectamente, por ejemplo, por páginas concretas de un tipo específico de explorador. El análisis se realiza diariamente y, si se encuentra algún resultado, probablemente sea mucho menos urgente que una alerta. Por el contrario, el análisis del diagnóstico proactivo NRT se realiza continuamente en la telemetría entrante, y se le notificará en unos minutos si las tasas de errores del servidor son mayores de lo esperado.

## Si recibe una alerta del diagnóstico proactivo en tiempo casi real

*¿Por qué he recibido esta alerta?*

*	Hemos detectado un aumento anómalo en la tasa de solicitudes con errores en comparación con la línea de base normal del período anterior. Después de analizar los errores y la telemetría asociada, creemos que hay un problema que debe examinar. 

*¿La notificación significa que tengo definitivamente un problema?*

*	Intentamos alertarle sobre las interrupciones o la degradación de la aplicación, aunque solo usted puede entender totalmente la semántica y el impacto en la aplicación o los usuarios.

*¿Entonces están mirando mis datos?*

*	No, el servicio es completamente automático. Solo obtendrá las notificaciones. Sus datos son [privados](app-insights-data-retention-privacy.md).

*¿Es necesario suscribirse a esta alerta?*

*	No. Cada aplicación que envía telemetría de solicitudes tiene esta regla de alerta.

*¿Puedo cancelar la suscripción u hacer que mis colegas reciban las notificaciones?*

*	Sí, en Reglas de alerta, haga clic en la regla Diagnóstico proactivo para configurarla. Puede deshabilitar la alerta o cambiar a los destinatarios de la misma. 

*Perdí el mensaje de correo electrónico. ¿Dónde puedo encontrar las notificaciones en el portal?*

*	En los registros de auditoría Haga clic en Configuración, Registros de auditoría y, después, en cualquier alerta para ver su aparición, pero con una vista limitada en cuanto a los detalles.

*Algunas de las alertas se refieren a problemas conocidos y no deseo recibirlas.*

*	Tenemos pendiente la incorporación de la supresión de alertas.


## Queremos sus comentarios

*Estamos muy interesados en saber lo que piensa sobre esto. Envíe sus comentarios a* [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com).

<!---HONumber=AcomDC_0323_2016-->