---
title: "Detección inteligente: anomalías de rendimiento de Smart | Microsoft Docs"
description: "Application Insights realiza un análisis proactivo de la telemetría de aplicación y le advierte de posibles problemas. Esta característica no necesita ninguna configuración."
services: application-insights
documentationcenter: windows
author: antonfrMSFT
manager: douge
ms.assetid: 6acd41b9-fbf0-45b8-b83b-117e19062dd2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 843a3cafd5a00dd4b4de04a43f18d58e60a48b29
ms.lasthandoff: 04/12/2017


---
# <a name="smart-detection---performance-anomalies"></a>Detección inteligente: anomalías de rendimiento


[Application Insights](app-insights-overview.md) realiza un análisis profundo de la telemetría de aplicación y puede advertirle de los posibles problemas de rendimiento. Probablemente esté leyendo esto porque ha recibido una de nuestras alertas de inteligentes por correo electrónico.

Esta característica no requiere configuración y se activa automáticamente cuando la aplicación genera suficientes datos de telemetría.

## <a name="what-is-smart-detection-of-performance-anomalies"></a>¿Qué es la detección inteligente de anomalías de rendimiento?
La detección inteligente identifica patrones inusuales de rendimiento de su aplicación mediante el análisis de la telemetría que envía dicha aplicación a Application Insights.

En concreto, busca los problemas de rendimiento que solo afectan a algunos de sus usuarios o que solo afectan a sus usuarios en algunos casos.

Por ejemplo, puede notificarle si las páginas de su aplicación se cargan mucho más lentamente en un tipo de explorador que en otros, o si las solicitudes se atienden de una forma más lenta desde un servidor determinado. También puede detectar problemas asociados con combinaciones de propiedades, como cargas lentas de página en un área geográfica en momentos determinados del día.

Anomalías como estas son muy difíciles de detectar con tan solo inspeccionar los datos, pero son más comunes de lo que puede creer. A menudo, solo se muestran cuando se quejan los clientes. Para entonces, ya es demasiado tarde: los usuarios afectados ya se han pasado a sus competidores.

En la actualidad, nuestros algoritmos examinan tiempos de carga de página, tiempos de respuesta de solicitud en el servidor y tiempos de respuesta de dependencia.  

No tiene que establecer ningún umbral o regla de configuración. El aprendizaje automático y los algoritmos de minería de datos se usan para detectar patrones anormales.

## <a name="about-the-smart-detection-alert"></a>Acerca de la alerta de detección inteligente
* *¿Por qué he recibido este correo electrónico?*
  * La detección inteligente analizó la telemetría que su aplicación envió a Application Insights y detectó un problema de rendimiento en su aplicación.
* *¿La notificación significa que tengo definitivamente un problema?*
  * No. Simplemente es una sugerencia sobre algo que puede que desee examinar con más detenimiento.
* *¿Cuál debo hacer?*
  * [Observe los datos presentados](#responding-to-an-alert). Use el Explorador de métricas para revisar el rendimiento en el tiempo y acceda para obtener métricas adicionales. Use Búsqueda para filtrar eventos específicos que lo ayuden a identificar la causa raíz.
* *¿Entonces están mirando mis datos?*
  * No. El servicio es completamente automático. Solo obtendrá las notificaciones. Sus datos son [privados](app-insights-data-retention-privacy.md).

## <a name="the-detection-process"></a>El proceso de detección
* *¿Qué tipos de anomalías de rendimiento se detectan?*
  * Patrones que llevarían mucho tiempo de comprobar por usted mismo. Por ejemplo, un bajo rendimiento en una combinación específica de ubicación, hora del día y plataforma.
* *¿Analiza todos los datos recopilados por Application Insights?*
  * No en este momento. Actualmente, analizamos el tiempo de respuesta de la solicitud, el tiempo de respuesta de dependencia y el tiempo de carga de la página. Próximamente estará disponible el análisis de métricas adicionales.
* *¿Puedo crear mis propias reglas de detección de anomalías?*

  * Todavía no. Pero puede:
  * [Configurar alertas](app-insights-alerts.md) que le indiquen cuándo una métrica cruza un umbral.
  * [Exportar telemetría](app-insights-export-telemetry.md) a una [base de datos](app-insights-code-sample-export-sql-stream-analytics.md) o a [PowerBI](app-insights-export-power-bi.md) para poder realizar un análisis usted mismo.
* *¿Con qué frecuencia se lleva a cabo el análisis?*

  * Ejecutamos el análisis diariamente en la telemetría desde el día anterior.
* *¿Sustituye esto a las [alertas de métricas](app-insights-alerts.md)?*
  * No.  No nos comprometemos a detectar cada comportamiento que considere anormal.

## <a name="how-to-investigate-the-issues-raised"></a>Cómo se investigan los problemas surgidos
Abra el informe de diagnóstico desde el correo electrónico o desde la lista de anomalías.

![En la alerta de correo electrónico, haga clic en el vínculo para abrir el informe de diagnóstico en Azure.](./media/app-insights-proactive-performance-diagnostics/03.png)

* **Cuándo** muestra la hora en que se detectó el problema.
* **Qué** describe:

  * el problema detectado;
  * las características del conjunto de eventos que encontramos mostraron el comportamiento del problema.
* La tabla compara el conjunto de bajo rendimiento con el comportamiento medio de todos los demás eventos.

Haga clic en los vínculos para abrir el Explorador de métricas y Búsqueda en los informes relevantes, filtrados en la hora y las propiedades del conjunto de rendimiento lento.

Modifique los filtros y el intervalo de tiempo para explorar la telemetría.

## <a name="how-can-i-improve-performance"></a>¿Cómo puedo mejorar el rendimiento?
Las respuestas lentas y los errores se encuentran entre los principales motivos de frustración entre los usuarios de los sitios web, como bien sabrá por su propia experiencia. Por lo tanto es importante que trate los problemas.

### <a name="triage"></a>Evaluación de errores
En primer lugar, ¿es realmente importante? Si una página siempre tarda en cargarse, pero solo un 1 % de los usuarios del sitio accede a ella, es posible que tenga cosas más importantes de las que ocuparse. Por otro lado, si solo un 1 % de los usuarios la abren, pero aún así produce excepciones cada vez, puede que merezca la pena investigar el problema.

Use la instrucción de impacto en el correo electrónico como guía general, pero tenga en cuenta que esta no le va a explicar todo lo que pasa. Recopile otras pruebas para confirmar.

Tenga en cuenta los parámetros del problema. Si es dependiente de la ubicación, configure [pruebas de disponibilidad](app-insights-monitor-web-app-availability.md) que incluyan la región: simplemente puede que haya problemas de red en esa área.

### <a name="diagnose-slow-page-loads"></a>Diagnostico de cargas de página lentas
¿Dónde está el problema? ¿Es el servidor lento para responder, es la página muy larga o requiere mucho trabajo del explorador para mostrarla?

Abra la hoja de métricas del navegador. La visualización segmentada del tiempo de carga de página del explorador muestra en qué se va el tiempo. 

* Si el **tiempo de solicitud de envío** es alto, o bien el servidor responde con lentitud o la solicitud es un envío con una gran cantidad de datos. Mire las [métricas de rendimiento](app-insights-web-monitor-performance.md#metrics) para investigar los tiempos de respuesta.
* Configure el [seguimiento de dependencias](app-insights-asp-net-dependencies.md) para ver si la lentitud se debe a los servicios externos o a su base de datos.
* Si predomina la **recepción de respuesta** , la página y sus elementos dependientes (JavaScript, CSS, imágenes y demás, excluyendo los datos cargados de forma asincrónica) son largos. Configure una [prueba de disponibilidad](app-insights-monitor-web-app-availability.md), y asegúrese de establecer la opción de cargar los elementos dependientes. Cuando obtenga algunos resultados, abra el detalle de un resultado y expándalo para ver los tiempos de carga de los distintos archivos.
* Un **Tiempo de procesamiento del cliente** alto sugiere que los scripts se ejecutan con lentitud. Si el motivo no es obvio, considere la posibilidad de agregar algún código de control de tiempo y enviar las horas en llamadas de trackMetric.

### <a name="improve-slow-pages"></a>Mejora de páginas lentas
Hay un sitio web completo de consejos sobre cómo mejorar las respuestas del servidor y los tiempos de carga de página, por lo que no intentaremos repetirlo aquí todo otra vez. Estas son algunas sugerencias que probablemente ya conozca, pero que pueden ayudarle a pensar en soluciones:

* Lentitud debida a archivos de gran tamaño: cargar los scripts y otras partes de forma asincrónica. Use la agrupación de scripts. Divida la página principal en widgets que cargan sus datos por separado. No envíe simples HTML antiguos para tablas de gran tamaño: use un script para solicitar los datos como JSON u otro formato compacto y luego rellene la tabla en su lugar. Existen excelentes marcos para ayudarle con todo esto. (Lo que también implican grandes scripts, por supuesto.)
* Dependencias de un servidor lento: tenga en cuenta las ubicaciones geográficas de los componentes. Por ejemplo, si usa Azure, asegúrese de que el servidor web y la base de datos se encuentran en la misma región. ¿Es posible que consultas las consultas recuperen más información de la que necesitan? ¿Podría ayudar el almacenamiento en caché o el procesamiento por lotes?
* Problemas de capacidad: eche una ojeada a las métricas de servidor de tiempos de respuesta y recuentos de solicitudes. Si los tiempos de respuesta presentan picos desproporcionados en recuentos de solicitud, es probable que se está tirando en exceso de los servidores.

## <a name="notification-emails"></a>Mensajes de correos electrónico de notificación
* *¿Es necesario suscribirse a este servicio para recibir notificaciones?*
  * No. Nuestro bot analiza periódicamente los datos de todos los usuarios de Application Insights y envía notificaciones si detecta problemas.
* *¿Puedo cancelar la suscripción u hacer que mis colegas reciban las notificaciones?*

  * Haga clic en el vínculo para cancelar la suscripción de la alerta o el correo electrónico.

    Actualmente se envían a los usuarios que tienen [acceso de escritura al recurso de Application Insights](app-insights-resources-roles-access-control.md).

    También puede editar la configuración de la lista de destinatarios en la hoja Detección inteligente.
* *No me quiero ver inundado por estos mensajes.*
  * Solo se envía uno al día con el problema más relevante que aún no hayamos notificado. No obtendrá repeticiones de ningún mensaje.
* *Si no hago nada, ¿obtendré un aviso?*
  * No, obtendrá un mensaje sobre cada problema solo una vez.
* *Perdí el mensaje de correo electrónico. ¿Dónde puedo encontrar las notificaciones en el portal?*
  * En la información general de Application Insights de su aplicación, haga clic en el icono **Detección inteligente**. Allí encontrará todas las notificaciones de los 7 últimos días.

## <a name="next-steps"></a>Pasos siguientes
Estas herramientas de diagnóstico lo ayudarán a inspeccionar los datos de telemetría de su aplicación:

* [Explorador de métricas](app-insights-metrics-explorer.md)
* [Explorador de búsqueda](app-insights-diagnostic-search.md)
* [Analytics: Lenguaje de consulta eficaz](app-insights-analytics-tour.md)

Las detecciones inteligentes son completamente automáticas. Pero ¿quizás le gustaría configurar algunas alertas más?

* [Alertas de métricas configuradas manualmente](app-insights-alerts.md)
* [Pruebas web de disponibilidad](app-insights-monitor-web-app-availability.md)

