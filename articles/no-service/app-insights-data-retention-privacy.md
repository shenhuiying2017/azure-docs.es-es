<properties 
	pageTitle="Data retention and storage in Application Insights" 
	description="Retention and privacy policy statement" 
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
	ms.date="11/14/2014" 
	ms.author="awills"/>

# Retención de datos y almacenamiento en Application Insights 

*Application Insights se encuentra en su versión de vista previa.*


Este artículo ofrece respuestas a preguntas acerca de cómo se almacena la telemetría que envía la aplicación a Application Insights.

Application Insights almacena datos en servidores de Microsoft Azure en los Estados Unidos de América. Descargue las [notas sobre seguridad, privacidad y cumplimiento](http://go.microsoft.com/fwlink/?LinkId=392408) para obtener detalles sobre la directiva de Microsoft Azure. A continuación se detallan aspectos específicos acerca de Application Insights. 

#### ¿Qué cantidad de datos se almacena? 

Aunque Application Insights se proporciona en esta fase gratuita de vista previa, se almacenan para cada cuenta: 

* Hasta 500 mensajes de telemetría por segundo (o 30 000 por minuto). 

* Hasta 10 M de vistas de página o eventos por mes. 

*¿Qué sucede si se superan los límites?* 

* Si se superan los límites por poco, se mostrarán algunos mensajes tras el recuento. Estos no se mostrarán en la búsqueda de diagnóstico. El recuento de eventos será correcto. Las métricas de promedio se basan en los eventos registrados y deben ser válidas. 

* Si se supera el límite mensual, los eventos dejarán de registrarse, por lo que las métricas mostrarán ceros después de una fecha. 

####¿Durante cuánto tiempo se conserva la telemetría? 

* En la búsqueda de diagnóstico, por ejemplo, los datos estarán visibles durante 7 días. Vistas de páginas individuales, eventos, mensajes de registro, seguimientos y excepciones. 

* 13 meses para datos agregados visibles en el explorador de métricas. Las estadísticas de las métricas, eventos y excepciones (recuentos de tipo, funciones con errores, etc.) se conservan en 1 minuto (o menos) durante 30 días y en 1 hora para 13 meses. 

####¿Donde se conservan los datos? 

* En Estados Unidos. 

¿Existe la opción de almacenarlos en Europa o en otro lugar? 

* Todavía no. 

####¿Están seguros mis datos de telemetría? 

Los datos se almacenan en servidores de Microsoft Azure y para las cuentas del Portal de vista previa de Azure, se describen restricciones de cuenta en el documento de seguridad de Azure, privacidad y cumplimiento. Para las cuentas del portal en línea de Visual Studio, se aplica el documento de protección de datos en línea de VS. 

El acceso a los datos por parte del personal de Microsoft está restringido. El acceso a los datos solo se realiza con su permiso y si es necesario para prestarle soporte en el uso de Application Insights. 

####¿Se cifran los datos en los servidores de Application Insights? 

No en este momento. 

####¿Puede enviarse información de identificación personal (PII) a Application Insights? 

Sí. Tanto la PII que envía a Application Insights el código de telemetría personalizado y la PII que se incluye en la telemetría estándar. La declaración de privacidad de Azure se aplica a Application Insights. 

Existen varias formas en que se pueden enviar datos al portal. Se puede mostrar en la búsqueda de diagnóstico. Los miembros de su organización también pueden exportar y descargar los datos. 

* Si instala el Monitor de estado o si agrega Application Insights al proyecto, se capturarán los seguimientos de pila cuando se produzcan excepciones o alertas de rendimiento. Estas pueden incluir datos de parámetros reales como, por ejemplo, datos de SQL. 

* Si se insertan llamadas de telemetría en el código como, por ejemplo, TrackEvent, o si se capturan mensajes de marco de trabajo de registro, las propiedades podrían incluir PII. También se aplican los términos de Microsoft Online Services. En particular, para Application Insights, debe cumplir las leyes y regulaciones aplicables a la privacidad y la recopilación de datos. Debe determinar si necesita notificaciones u otros consentimientos antes de recopilar la información. 


####¿Qué telemetría se envía al usar Application Insights? 

Puede instalar varios componentes. (Consulte [Application Insights: introducción][start].) 

<table>
<tr><th>Elementos que se configuran</th><th>Telemetría enviada al portal</th><th>¿Puede contener datos confidenciales?</th></tr>
<tr><td><a href="../app-insights-start-monitoring-app-health-usage/">Incorporación de Application Insights a su proyecto web</a></td>
  <td>Métricas: número de solicitudes del servidor, tiempo de respuesta del servidor</td>
  <td>No</td></tr>
<tr><td></td>
  <td>Informes de excepción del servidor</td><td>Los volcados de pila pueden contener valores de parámetros</td></tr>
<tr><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Seguimiento de eventos y métricas personalizados</a></td>
  <td>Eventos con propiedades adjuntadas mediante el código</td>
  <td>Sí, si el código envía PII en las propiedades o títulos</td></tr>
<tr><td><a href="../app-insights-search-diagnostic-logs/#trace">Telemetría de seguimiento y registro</a></td><td>Mensajes de seguimiento y registro</td><td>Los mensajes de registro pueden contener PII</td></tr>
<tr><td><a href="../app-insights-web-track-usage/">Inserte el script AI en sus páginas web</a></td>
  <td>Identificador de cuenta y usuario anónimos</td><td>No</td></tr>
<tr><td></td><td>Inicio y cierre de sesión de usuario anónimo</td><td>No</td></tr>
<tr><td></td><td>URI de página, tiempos de carga, tiempo de sesión</td><td>No</td></tr>
<tr><td><a href="../app-insights-monitor-performance-live-website-now/">Instalación del monitor de estado en el servidor</a></td>
  <td>Llamadas de dependencia y duraciones</td>
  <td>Los datos de llamadas pueden contener parámetros como, por ejemplo, los campos SQL</td></tr>
<tr><td></td><td>CPU, memoria, red y otros contadores de recursos</td><td>No</td></tr>
<tr><td><a href="../app-insights-monitor-web-app-availability/">Pruebas web</a></td><td>Disponibilidad y tiempos de respuesta como se muestran desde la Web</td><td>No</td></tr>
</table>

## <a name="video"></a>Vídeos

#### Introducción

> [AZURE.VIDEO application-insights-introduction]

#### Primeros pasos

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]



<!--HONumber=35.2-->
 