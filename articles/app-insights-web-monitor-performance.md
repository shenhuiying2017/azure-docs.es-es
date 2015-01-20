<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Supervisar el estado y el uso de su aplicación con Application Insights" description="Introducción a Application Insights. Analice el uso, la disponibilidad y el rendimiento de las aplicaciones de Microsoft Azure o locales." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-11-21" ms.author="awills" />
 
# Supervisar el rendimiento de aplicaciones web

*Application Insights se encuentra en su versión de vista previa.*


Asegúrese de que la aplicación tendrá un rendimiento correcto y descubra rápidamente los posibles errores. [Application Insights][start] le comunicará todas las excepciones y los problemas de rendimiento y le ayudará a diagnosticar las causas principales.

Application Insights puede supervisar aplicaciones web ASP.NET hospedadas en máquinas virtuales o locales, o en sitios web de Microsoft Azure. 

* [Configuración de la supervisión de rendimiento](#setup)
* [Consulta de los datos](#view)
* [¿Qué significa todo esto?](#metrics)
* [Problemas de diagnóstico](#diagnosis)
* [Pasos siguientes](#next)

## <a name="setup"></a>Configurar la supervisión de rendimiento

Si todavía no ha agregado Application Insights a un proyecto (es decir, si no dispone de ApplicationInsights.config), puede comenzar con uno de estos procedimientos:

* [Agregar Application Insights a un proyecto de aplicación en Visual Studio][greenbrown]: opción recomendada. Puede realizar una supervisión pasiva del rendimiento, insertar el registro de diagnósticos y seguir el uso.
* [Supervisar el rendimiento de un sitio web activo ahora][redfield]: este procedimiento no precisa actualizar el proyecto de aplicación ni volver a implementar el sitio web.
* [Para un sitio web de Microsoft Azure](../insights-how-to-customize-monitoring/)  ya puede ver las métricas en la supervisión del sitio web. 


## <a name="view"></a>Ver informes

Ejecute la aplicación con F5 y pruébela. Abra varias páginas.

En Visual Studio, aparecerá un recuento de los eventos que se han recibido.

![](./media/appinsights/appinsights-09eventcount.png)


Abra Application Insights desde un proyecto.

![Right-click your project and open the Azure portal](./media/appinsights/appinsights-04-openPortal.png)


Busque los datos en los mosaicos de **Estado de la aplicación**. Al principio, solo aparecerán uno o dos puntos. Por ejemplo:

![Click through to more data](./media/appinsights/appinsights-41firstHealth.png)

Si se trabaja en modo de depuración, la telemetría se agiliza a través de la canalización y los datos aparecen en cuestión de segundos. Cuando se implementa una aplicación, los datos se acumulan a menor velocidad.

Si no puede ver los datos en un primer momento, espere un minuto y haga clic en Actualizar.

### Explorar las métricas

Haga clic en un mosaico para ver su contenido con mayor detalle y los resultados durante más tiempo. Por ejemplo, haga clic en el mosaico Solicitudes y seleccione un intervalo de tiempo:


![Click through to more data and select a time range](./media/appinsights/appinsights-48metrics.png)

Haga clic en un gráfico para elegir las métricas mostradas o para agregar un nuevo gráfico y seleccionar sus métricas:

![Click a graph to choose metrics](./media/appinsights/appinsights-61perfchoices.png)

> [AZURE.NOTE] **Desactive todas las métricas** para ver una selección completa de las opciones disponibles. Las métricas se organizan en grupos. Cuando se selecciona un miembro de un grupo, solo aparecen los demás miembros de dicho grupo.


## <a name="metrics"></a>¿Qué significa todo esto? Informes y mosaicos de rendimiento

Puede obtener una gran variedad de métricas de rendimiento. Comencemos por las que aparecen de forma predeterminada en la hoja de la aplicación.


### Solicitudes

El número de solicitudes HTTP que se reciben en un período específico. Compare este dato con los resultados de otros informes para ver cómo se comporta la aplicación cuando la carga varía.

Las solicitudes HTTP incluyen todas las solicitudes GET o POST de páginas, datos e imágenes.

Haga clic en el mosaico para obtener las cuentas de URL específicas.

### Tiempo de respuesta promedio

Mide el tiempo entre la entrada de una solicitud web y la devolución de la respuesta correspondiente.

Los puntos muestran una media móvil. Si se producen muchas solicitudes, es posible que algunas de ellas se desvíen de la media sin que en el gráfico se muestre ningún pico o descenso evidente.

Busque picos poco habituales. Por lo general, el tiempo de respuesta aumenta cuando aumentan las solicitudes. Si este aumento es desproporcionado, la aplicación puede alcanzar el límite de recursos (por ejemplo, de CPU o de un servicio que esté usando).

Haga clic en el mosaico para obtener los tiempos de URL específicas.

![](./media/appinsights/appinsights-42reqs.png)


### Solicitudes más lentas

![](./media/appinsights/appinsights-44slowest.png)

Muestra las solicitudes que pueden precisar un ajuste de rendimiento.


### Error en las solicitudes

![](./media/appinsights/appinsights-46failed.png)

Un recuento de las solicitudes que inician excepciones no detectadas.

Haga clic en el mosaico para ver los detalles de errores específicos y seleccione una solicitud individual para revisarla de forma más exhaustiva. 

Solo se conserva una muestra representativa de los errores para su inspección individual.

### Otras métricas

Si desea ver las demás métricas que puede mostrar, haga clic en un gráfico y desactive todas las métricas para ver el conjunto completo de opciones disponibles. Haga clic en (i) para ver la definición de cada métrica.

![Deselect all metrics to see the whole set](./media/appinsights/appinsights-62allchoices.png)


Si selecciona una métrica, se deshabilitarán las demás que no pueden aparecer en el mismo gráfico.

## Establecer alertas

Para recibir notificaciones por correo electrónico de los valores no habituales de cualquier métrica, agregue una alerta. Puede decidir si se debe enviar un mensaje de correo electrónico a los administradores de cuentas o a direcciones de correo electrónico específicas.

![](./media/appinsights/appinsights-413setMetricAlert.png)

Establezca el recurso antes de las demás propiedades. No elija los recursos de la prueba web si desea establecer alertas en las métricas de rendimiento o de uso.

Asegúrese de tener en cuenta las unidades en las que se le pide que escriba el valor de umbral.

*No puedo ver el botón Agregar alerta.* - Probablemente se deba a que tiene acceso de solo lectura. 

## <a name="diagnosis"></a>Problemas de diagnóstico

Para buscar y diagnosticar problemas de rendimiento, lea estas sugerencias:

* Configure las [pruebas web][availability] para recibir una alerta si el sitio web deja de funcionar o si responde de forma incorrecta o más lento de lo debido. 
* Compare el recuento de Solicitudes con otras métricas para ver si la lentitud de respuesta o los errores se encuentran relacionados con la carga.
* [Inserte y busque instrucciones de seguimiento][diagnostic] en el código para facilitar la identificación de los problemas.

## <a name="next"></a>Pasos siguientes

[Pruebas web][availability]: reciba solicitudes web en su aplicación de forma periódica desde distintos lugares del mundo.

[Capture y busque seguimientos de diagnóstico][diagnostic]: inserte llamadas de seguimiento y cribe los resultados para identificar los problemas.

[Seguimiento de uso][usage]: averigüe cuántas personas usan una aplicación.

[Solución de problemas][qna]: preguntas y respuestas.



[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




<!--HONumber=35.2-->
