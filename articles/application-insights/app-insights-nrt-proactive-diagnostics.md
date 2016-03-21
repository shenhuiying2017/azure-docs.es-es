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
	ms.date="03/05/2016" 
	ms.author="awills"/>
 
# Diagnóstico proactivo en tiempo casi real

[Application Insights para Visual Studio](app-insights-overview.md) le notifica automáticamente en tiempo casi real si la tasa de solicitudes con errores de su aplicación web aumenta significativamente. Para ayudarle a evaluar y diagnosticar el problema, en la notificación se proporciona un análisis de las características de las solicitudes con errores y la telemetría relacionada. También hay vínculos en el portal de Application Insights para obtener un diagnóstico más amplio. La característica no necesita ninguna instalación o configuración, ya que usa algoritmos de aprendizaje automático para predecir la tasa normal de errores de línea base. Necesita un determinado volumen mínimo de tráfico para que funcione.

Esta característica funciona para aplicaciones web ASP.NET y de Java, hospedadas en la nube o en sus propios servidores. También funciona para cualquier aplicación que genere telemetría de solicitudes, por ejemplo, si tiene un rol de trabajo que llama a [TrackRequest()](app-insights-api-custom-events-metrics.md#track-request).

El diagnóstico proactivo se activa tan pronto como configure [Application Insights para su proyecto](app-insights-get-started.md), y siempre que la aplicación genere una determinada cantidad mínima de telemetría.

Esta es una alerta de ejemplo:

![El ejemplo de alerta inteligente muestra el análisis del clúster sobre el error](./media/app-insights-nrt-proactive-diagnostics/010.png)

Observe que le indica:

* Cuántos usuarios se ven afectados (para darle una idea de la gravedad).
* Un patrón característico relacionado con los errores. En este ejemplo, hay un nombre de solicitud específico (dirección URL solicitada). Esto le indica inmediatamente por dónde empezar a buscar en el código. Otra posibilidad puede ser un tipo específico de dispositivo de cliente o explorador.
* Las excepciones, seguimientos de registros y errores en las dependencias (bases de datos u otros componentes externos) que parecen estar asociados con las solicitudes que presentan error.
* Los vínculos directos a búsquedas significativas en los datos de telemetría en Application Insights.

Normalmente las [alertas de métricas](app-insights-alerts.md) le comunican que puede haber un problema. Pero el diagnóstico proactivo en tiempo casi real, empieza el trabajo de diagnóstico, realizando muchos de los análisis que, de otra forma, tendría que hacer usted mismo. Los resultados se le presentan claramente organizados, lo que le ayuda a llegar rápidamente a la raíz del problema.

## Cómo funciona

El diagnóstico proactivo en tiempo casi real supervisa la telemetría recibida de su aplicación y, en particular, la tasa de solicitudes con errores. Esta métrica indica normalmente el número de solicitudes HTTP que devuelven un código de respuesta de 400 o superior (a no ser que escribiera un código personalizado para [filtrar](app-insights-api-filtering-sampling.md#filtering) o generar sus propias llamadas [TrackRequest](app-insights-api-custom-events-metrics.md#track-request)).

El rendimiento de una aplicación tiene un patrón típico de comportamiento. Algunas solicitudes son más propensas a errores que otras; y la tasa de error general puede aumentar a medida que aumenta la carga. El diagnóstico proactivo en tiempo casi real utiliza el aprendizaje automático para buscar estas correlaciones.

A medida que Application Insights recibe telemetría de su aplicación web, el diagnósticos proactivo compara el comportamiento actual con los patrones vistos a lo largo de los últimos días. Si la tasa de errores aumenta significativamente por encima de la expectativa establecida por el rendimiento anterior, se genera una alerta.

Cuando se emite una alerta, el servicio realiza un análisis del clúster en varias dimensiones de las solicitudes, para intentar identificar un patrón de valores que caracterice los errores. En el ejemplo anterior, el análisis detectó que la mayoría de los errores están relacionados con un nombre de solicitud específico, pero se descubrió que los errores son independientes de la instancia de host o de servidor.

A continuación, el analizador busca excepciones y errores de dependencia que estén asociados a solicitudes en el clúster que identificó, junto con un ejemplo de cualquier registro de seguimiento asociado a esas solicitudes.

El análisis resultante se le envía por correo electrónico, a no ser que configurara lo contrario.

Al igual que las [alertas que establece manualmente](app-insights-alerts.md), puede inspeccionar el estado de la alerta y configurarla en la hoja Alertas del recurso de Application Insights. Pero, a diferencia de otras alertas, no tiene que instalar ni configurar un diagnóstico proactivo en tiempo casi real. Si lo desea, puede deshabilitarla o cambiar sus direcciones de correo electrónico de destino.

## Clasificación y diagnóstico de una alerta

Una alerta indica que se detectó una anomalía en la tasa de solicitudes con errores. Es probable que haya algún problema con la aplicación o con su entorno.

Puede determinar la urgencia del problema a partir del porcentaje de solicitudes y del número de usuarios afectados. En el ejemplo anterior, la tasa de error del 15 % se compara con una tasa normal del 1,3 %, lo que indica que obviamente hay algo que no va bien. 22 usuarios diferenciados se vieron afectados por errores en una operación determinada. Si se tratara de su aplicación, podría evaluar la importancia que tiene el problema.

En muchos casos, podrá diagnosticar el problema rápidamente a partir del nombre de la solicitud, las excepciones, las dependencias y de otros datos de seguimiento proporcionados.

Hay algunas otras pistas. Por ejemplo, la tasa de error de dependencia en este ejemplo es la misma que la tasa de excepción (89,3 %). Esto sugiere que la excepción se produce directamente desde el error de dependencia, lo que le da una idea clara de dónde puede empezar a buscar en el código.

Pero si necesita investigar más, los vínculos de cada sección le llevarán directamente a una [página de búsqueda](app-insights-diagnostic-search.md) filtrada por las solicitudes, la excepción, la dependencia o el seguimiento relevantes. O bien, puede abrir el [Portal de Azure](https://portal.azure.com), navegar hasta el recurso de Application Insights de su aplicación y abrir la hoja Problemas.

En este ejemplo, al hacer clic en el vínculo de búsqueda se abre la hoja de búsqueda de Application Insights en la instrucción SQL con la causa raíz: se proporcionaron valores NULL en campos obligatorios y por ello no se superó la validación durante la operación de guarda.


![Búsqueda de diagnóstico](./media/app-insights-nrt-proactive-diagnostics/050.png)

## Revisar las alertas recientes

Para revisar las alertas en el portal, abra **Configuración, Registros de auditoría**.

![Resumen de alertas](./media/app-insights-nrt-proactive-diagnostics/040.png)

Haga clic en cualquier alerta para ver sus detalles completos.


## Configurar alertas 

Abra la página Alertas. Se incluye una alerta de error adaptable en cualquier alerta configurada manualmente, y puede ver si está actualmente en el estado de alerta.

![En la página de información general, haga clic en el icono Alertas. O bien, en cualquier página de métricas, haga clic en el botón Alertas.](./media/app-insights-nrt-proactive-diagnostics/020.png)

Haga clic en la alerta para configurarla.

![Configuración](./media/app-insights-nrt-proactive-diagnostics/030.png)

Observe que puede deshabilitar la alerta de error adaptable, pero no puede eliminarla (ni crear otra).


## ¿Cuál es la diferencia ...

El diagnóstico proactivo NRT complementa a otras características diferentes pero similares de Application Insights.

* [Las alertas de métricas](app-insights-alerts.md) las configura el usuario y pueden supervisar una amplia variedad de métricas, como el uso de la CPU, las tasas de solicitudes y los tiempos de carga de las páginas, entre otras. Puede usarlas para avisarle, por ejemplo, de si necesita agregar más recursos. Por el contrario, el diagnóstico proactivo NRT abarca un pequeño conjunto de métricas críticas (actualmente solo la tasa de solicitudes con errores), diseñadas para notificarle en tiempo casi real el momento en el que la tasa de solicitudes con errores de la aplicación web aumenta significativamente en comparación con el comportamiento normal de esta.

    El diagnóstico proactivo en tiempo casi real ajusta automáticamente su umbral en respuesta a condiciones existentes.

    El diagnóstico proactivo en tiempo casi real inicia el trabajo de diagnóstico por usted. 
* [La detección proactiva](app-insights-proactive-detection.md) también usa la inteligencia automática para descubrir patrones inusuales en las métricas, y no requiere ninguna configuración por su parte. Pero, a diferencia del diagnóstico proactivo NRT, la finalidad de la detección proactiva es encontrar segmentos del colector de uso que pudieran haberse servido incorrectamente, por ejemplo, por páginas concretas de un tipo específico de explorador. El análisis se realiza diariamente y, si se encuentra algún resultado, probablemente sea mucho menos urgente que una alerta. Por el contrario, el análisis del diagnóstico proactivo NRT se realiza continuamente en la telemetría entrante, y se le notificará en unos minutos si las tasas de errores del servidor son mayores de lo esperado.

## Si recibe una alerta del diagnóstico proactivo en tiempo casi real

*¿Por qué he recibido esta alerta?*

*	Hemos detectado un aumento anormal en las solicitudes con error en comparación con la línea de base normal del período anterior. Después de analizar los errores y la telemetría asociada, creemos que hay un problema que debe examinar. 

*¿La notificación significa que tengo definitivamente un problema?*

*	Intentamos alertarle sobre las interrupciones o la degradación de la aplicación, aunque solo usted puede entender totalmente la semántica y el impacto en la aplicación o los usuarios.

*¿Entonces están mirando mis datos?*

*	No, el servicio es completamente automático. Solo obtendrá las notificaciones. Sus datos son [privados](app-insights-data-retention-privacy.md).

*¿Es necesario suscribirse a esta alerta?*

*	No. Cada aplicación que envía telemetría de solicitudes tiene esta regla de alerta.

*¿Puedo cancelar la suscripción u hacer que mis colegas reciban las notificaciones?*

*	Sí, en Reglas de alerta, haga clic en la regla Diagnóstico proactivo en tiempo casi real para configurarla. Puede deshabilitar la alerta o cambiar a los destinatarios de la misma. 

*Perdí el mensaje de correo electrónico. ¿Dónde puedo encontrar las notificaciones en el portal?*

*	En los registros de auditoría Haga clic en Configuración, Registros de auditoría, y después en cualquier alerta para ver sus detalles completos.

*Algunas de las alertas se refieren a problemas conocidos y no deseo recibirlas.*

*	Tenemos pendiente la incorporación de la supresión de alertas.


## Queremos sus comentarios

*Estamos muy interesados en saber lo que piensa sobre esto. Por favor, envíe sus comentarios a:* [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com)

<!---HONumber=AcomDC_0309_2016-->