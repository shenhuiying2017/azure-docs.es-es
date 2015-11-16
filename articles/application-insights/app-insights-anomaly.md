<properties 
	pageTitle="Application Insights: detección proactiva de anomalías" 
	description="Application Insights realiza un análisis profundo de la telemetría de aplicación y le advierte de los posibles problemas." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/02/2015" 
	ms.author="awills"/>

#  Application Insights: alertas proactivas

*Application Insights se encuentra en su versión de vista previa.*


Application Insights realiza un análisis profundo de la telemetría de aplicación y puede advertirle de los posibles problemas de rendimiento. Probablemente esté leyendo esto porque ha recibido una de nuestras alertas por correo electrónico.

## Acerca de las alertas proactivas

* *¿Por qué he recibido esta alerta?*
 * Application Insights analiza periódicamente sus datos con reglas de reconocimiento de patrones. Busca anomalías que pudieran indicar problemas de rendimiento de su aplicación.
* *¿La notificación significa que tengo definitivamente un problema?*
 * No. Simplemente es una sugerencia sobre algo que puede que desee examinar con más detenimiento. 
* *¿Cuál debo hacer?*
 * [Mire los datos que se presentan](#responding-to-an-alert) y piense si podrían representar un problema. De lo contrario, todo está correcto.
* *¿Entonces están mirando mis datos?*
 * No, el servicio es completamente automático. Solo obtendrá las notificaciones. Sus datos son [privados](app-insights-data-retention-privacy.md).


## El proceso de detección

* *¿Qué tipos de anomalías se detectan?*
 * Patrones que llevarían mucho tiempo de comprobar por usted mismo. Por ejemplo, un bajo rendimiento en una combinación específica de ubicación, hora del día y plataforma.
* *¿Puedo crear mis propias reglas de detección de anomalías?*
 * Todavía no. Pero puede:
 * [Configurar alertas](app-insights-alerts.md) que le indiquen cuándo una métrica cruza un umbral.
 * [Exportar telemetría](app-insights-export-telemetry.md) a una [base de datos](app-insights-code-sample-export-sql-stream-analytics.md) o [a PowerBI](app-insights-export-power-bi.md) u [otras](app-insights-code-sample-export-telemetry-sql-database.md) herramientas para poder usted mismo realizar un análisis.
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

## Está muy bien poder contar con estas alertas. Pero, ¿qué puedo hacer para mejorar el rendimiento?

Las respuestas lentas y los errores se encuentran entre los principales motivos de frustración entre los usuarios de los sitios web, como bien sabrá por su propia experiencia. Por lo tanto es importante que trate los problemas.

### Evaluación de errores

En primer lugar, ¿es realmente importante? Si una página siempre tarda en cargarse, pero solo un 1 % de los usuarios del sitio accede a ella, es posible que tenga cosas más importantes de las que ocuparse. Por otro lado, si solo un 1 % de los usuarios la abren, pero aún así produce excepciones cada vez, puede que merezca la pena investigar el problema.

Use la instrucción de impacto en el correo electrónico como guía general, pero tenga en cuenta que esta no le va a explicar todo lo que pasa. Recopile otras pruebas para confirmar.

Tenga en cuenta los parámetros del problema. Si es dependiente de la ubicación, configure [pruebas de disponibilidad](app-insights-monitor-web-app-availability.md) que incluyan la región: simplemente puede que haya problemas de red en esa área.

### Diagnostico de cargas de página lentas 

¿Dónde está el problema? ¿Es el servidor lento para responder, es la página muy larga o requiere mucho trabajo del explorador para mostrarla?

Abra la hoja de métricas del navegador. La [visualización segmentada del tiempo de carga de página del explorador](app-insights-javascript.md#explore-your-data) muestra en qué se va el tiempo.

* Si el **tiempo de solicitud de envío** es alto, o bien el servidor responde con lentitud o la solicitud es un envío con una gran cantidad de datos. Mire las [métricas de rendimiento](app-insights-web-monitor-performance.md#metrics) para investigar los tiempos de respuesta. 
* Configure el [seguimiento de dependencias](app-insights-dependencies.md) para ver si la lentitud se debe a los servicios externos o a su base de datos.
* Si predomina la **recepción de respuesta**, la página y sus elementos dependientes (JavaScript, CSS, imágenes y demás, excluyendo los datos cargados de forma asincrónica) son largos. Configure una [prueba de disponibilidad](app-insights-monitor-web-app-availability.md), y asegúrese de establecer la opción de cargar los elementos dependientes. Cuando obtenga algunos resultados, abra el detalle de un resultado y expándalo para ver los tiempos de carga de los distintos archivos.
* Un **Tiempo de procesamiento del cliente** alto sugiere que los scripts se ejecutan con lentitud. Si el motivo no es obvio, considere la posibilidad de agregar algún código de control de tiempo y enviar las horas en llamadas de trackMetric.

### Mejora de páginas lentas

Hay un sitio web completo de consejos sobre cómo mejorar las respuestas del servidor y los tiempos de carga de página, por lo que no intentaremos repetirlo aquí todo otra vez. Estas son algunas sugerencias que probablemente ya conozca, pero que pueden ayudarle a pensar en soluciones:

* Lentitud debida a archivos de gran tamaño: cargar los scripts y otras partes de forma asincrónica. Use la agrupación de scripts. Divida la página principal en widgets que cargan sus datos por separado. No envíe simples HTML antiguos para tablas de gran tamaño: use un script para solicitar los datos como JSON u otro formato compacto y luego rellene la tabla en su lugar. Existen excelentes marcos para ayudarle con todo esto. (Lo que también implican grandes scripts, por supuesto.)
* Dependencias de un servidor lento: tenga en cuenta las ubicaciones geográficas de los componentes. Por ejemplo, si usa Azure, asegúrese de que el servidor web y la base de datos se encuentran en la misma región. ¿Es posible que consultas las consultas recuperen más información de la que necesitan? ¿Podría ayudar el almacenamiento en caché o el procesamiento por lotes?
* Problemas de capacidad: eche una ojeada a las métricas de servidor de tiempos de respuesta y recuentos de solicitudes. Si los tiempos de respuesta presentan picos desproporcionados en recuentos de solicitud, es probable que se está tirando en exceso de los servidores. 


## Mensajes de correos electrónico de notificación

* *¿Es necesario suscribirse a este servicio para recibir notificaciones?*
 * No. Nuestro bot analiza periódicamente los datos de todos los usuarios de Application Insights y envía notificaciones si detecta problemas.
* *¿Puedo cancelar la suscripción u hacer que mis colegas reciban las notificaciones?*
 * Haga clic en el vínculo de la alerta o el correo electrónico. Abra la configuración de anomalías.
 
    ![](./media/app-insights-anomaly/01.png)

    Actualmente se envían a los usuarios que tienen [acceso de escritura al recurso de Application Insights](app-insights-resources-roles-access-control.md).
* *No me quiero ver inundado por estos mensajes*.
 * Se limitan a tres al día. No obtendrá repeticiones de ningún mensaje.
* *Si no hago nada, ¿obtendré un aviso?*
 * No, obtendrá un mensaje sobre cada problema solo una vez.
* *Perdí el mensaje de correo electrónico. ¿Dónde puedo encontrar las notificaciones en el portal?*
 * En la información general de Application Insights de su aplicación, haga clic en el icono **Anomalías**. 






 

<!---HONumber=Nov15_HO2-->