<properties 
	pageTitle="Definición de alertas en Application Insights" 
	description="Reciba mensajes de correo electrónico sobre bloqueos, excepciones y cambios de métrica." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="awills"/>
 
# Definición de alertas en Application Insights

[Visual Studio Application Insights][start] pueden alertarle de los cambios en las métricas de rendimiento o de uso de la aplicación.

Application Insights supervisa la aplicación activa en una [amplia variedad de plataformas][platforms] para ayudarle a diagnosticar problemas de rendimiento y entender los patrones de uso.

Hay dos tipos de alertas:
 
* Las **pruebas web** le informan de que el sitio no está disponible en Internet o responde lentamente. [Más información][availability].
* Las **alertas de métricas** le avisan cuando una métrica cruza un valor de umbral durante un período determinado: por ejemplo, recuentos de error, memoria o vistas de página. 

Hay una [página aparte sobre las pruebas web][availability], por lo que aquí nos centraremos en las alertas de métricas.

## Alertas de métricas

Si aún no ha configurado Application Insights para su aplicación, [hágalo primero][start].

Para recibir un correo electrónico cuando una métrica cruce un umbral, inicie desde el Explorador de métricas o desde el icono Reglas de alerta en la hoja de información general.

![En la hoja Reglas de alerta, elija Agregar alerta. Establezca la aplicación como el recurso que se va a medir, proporcione un nombre para la alerta y elija una métrica.](./media/app-insights-alerts/01-set-metric.png)

Establezca el recurso antes de las demás propiedades. **Elija el recurso "(components)"** si desea establecer alertas sobre métricas de rendimiento o de uso.

Asegúrese de tener en cuenta las unidades en las que se le pide que escriba el valor de umbral.

El nombre que asigne a la alerta debe ser único dentro del grupo de recursos (no solo en la aplicación).

*No veo el botón Agregar alerta.* ¿Está usando una cuenta de organización? Puede establecer alertas si tiene acceso de propietario o colaborador a este recurso de aplicación. Eche un vistazo a Configuración -> Usuarios. [Más información sobre el control de acceso][roles].

## Visualización de alertas

Recibirá un correo electrónico cuando un alerta cambia el estado entre inactivo y activo.

En la hoja de reglas Alerta se muestra el estado actual de cada alerta.

Hay un resumen de la actividad reciente en la lista desplegable de alertas:

![](./media/app-insights-alerts/010-alert-drop.png)

El historial de cambios de estado está en el registro Eventos de operaciones:

![En la hoja Información general, cerca de la parte inferior, haga clic en 'Eventos de la semana pasada'.](./media/app-insights-alerts/09-alerts.png)

*¿Estos "eventos" están relacionados con eventos de telemetría o eventos personalizados?*

* No. Estos eventos operativos son simplemente un registro de cosas que han ocurrido en este recurso de aplicación. 


## Funcionamiento de las alertas

* Una alerta tiene dos estados: "alerta" y "correcto". 

* Se envía un correo electrónico cuando una alerta cambia de estado.

* Una alerta se evalúa cada vez que llega una métrica, pero no en caso contrario.

* La evaluación agrega la métrica durante el período anterior y, luego, la compara con el umbral para determinar el nuevo estado.

* El período que elija especifica el intervalo en el que se agregan métricas. No afecta a la frecuencia con la que se evalúa la alerta: depende de la frecuencia de llegada de métricas.

* Si no llega ningún dato para una métrica concreta durante un tiempo, este intervalo tiene efectos diferentes en la evaluación de la alerta y en los gráficos del Explorador de métricas. En el Explorador de métricas, si no se ve ningún dato durante más tiempo que el intervalo de muestreo del gráfico, el gráfico mostrará un valor de 0. Pero una alerta basada en la misma métrica no se volverá a evaluar, y estado de la alerta permanecerá sin cambios.

    Cuando finalmente llegan datos, el gráfico se desplazará a un valor distinto de cero. La alerta se evaluará en función de los datos disponibles para el período especificado. Si el nuevo punto de datos es el único disponible en el período, el agregado se basará en eso.

* Una alerta puede parpadear con frecuencia entre los estados de alerta y correcto, incluso si se establece un período largo. Esto puede suceder si el valor de métrica se sitúa alrededor del umbral. No hay ninguna histéresis en el umbral: la transición a alerta se produce en el mismo valor que la transición a correcto.



## Alertas de disponibilidad

Puede configurar pruebas web que prueben cualquier sitio web desde diferentes puntos alrededor del mundo. [Más información][availability].

## ¿Qué alertas es conveniente establecer?

Depende de la aplicación. Para empezar, es mejor no establecer demasiadas métricas. Observe durante un tiempo sus gráficos de métrica mientras se ejecuta la aplicación para hacerse una idea de cómo se comporta normalmente. Esto le ayudará a encontrar maneras de mejorar su rendimiento. A continuación, configure alertas para que le avisen cuando las métricas salgan de la zona normal.

Las alertas más populares son:

* Las [pruebas web][availability] son importantes si la aplicación es un servicio web o un sitio web que está visible en Internet de acceso público. Le indican si el sitio deja de funcionar o responde lentamente, incluso si el problema es del operador y no de la aplicación. Sin embargo, son pruebas sintéticas, por lo que no miden la experiencia real de los usuarios.
* Las [métricas del explorador][client], especialmente los **tiempos de carga de página del explorador**, son buenas para aplicaciones web. Si la página tiene una gran cantidad de scripts, deberá tener en cuenta las **excepciones del explorador**. Para obtener estas métricas y alertas, tiene que configurar [la supervisión de páginas web][client].
* **Tiempo de respuesta del servidor** y **Solicitudes incorrectas** para las aplicaciones web del lado servidor. Además de configurar alertas, eche un vistazo a estas métricas para ver si varían desproporcionadamente con tasas de solicitud altas: esto puede indicar que la aplicación se está quedando sin recursos.
* **Excepciones de servidor**: para verlas, deberá realizar alguna [configuración adicional](app-insights-asp-net-exceptions.md).

## Establecimiento de alertas mediante PowerShell

En la mayoría de los casos, basta con establecer manualmente las alertas. Pero si desea crear alertas de métrica automáticamente, puede hacerlo mediante el uso de PowerShell.

#### Instalación única

Si no ha usado PowerShell con su suscripción de Azure antes:

Instale el módulo de Azure Powershell en el equipo donde desea ejecutar los scripts.

 * Instale el [Instalador de plataforma web de Microsoft (v5 o superior)](http://www.microsoft.com/web/downloads/platform.aspx).
 * Úselo para instalar Microsoft Azure Powershell.


#### Conexión a Azure

Inicie Azure PowerShell y [conéctese a su suscripción](powershell-install-configure.md):

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


#### Obtención de alertas

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

#### Agregar alerta


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



#### Ejemplo 1

Enviarme un correo electrónico si la respuesta del servidor a las solicitudes HTTP, calculada durante 5 minutos, tarda más de 1 segundo. Mi recurso de Application Insights se denomina IceCreamWebApp, y está en el grupo de recursos Fabrikam. Soy el propietario de la suscripción de Azure.

El GUID es el id. de la suscripción (no la clave de instrumentación de la aplicación).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

#### Ejemplo 2

Tengo una aplicación en la que uso [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) para informar de una métrica denominada "salesPerHour". Enviar un correo electrónico a mis colegas si "salesPerHour" es menor que 100, calculado durante 24 horas.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

La misma regla puede usarse con la métrica notificada mediante el [parámetro de medida](app-insights-api-custom-events-metrics.md#properties) de otra llamada de seguimiento, como TrackEvent o trackPageView.

#### Nombres de métrica

Nombre de métrica | Nombre de pantalla | Descripción
---|---|---
`basicExceptionBrowser.count`|Excepciones de explorador|Recuento de excepciones no detectadas en el explorador.
`basicExceptionServer.count`|Excepciones de servidor|Número de excepciones no controladas producidas por la aplicación
`clientPerformance.clientProcess.value`|Tiempo de procesamiento del cliente|Tiempo transcurrido entre la recepción del último byte de un documento hasta que se carga el DOM. Todavía se pueden procesar solicitudes asincrónicas.
`clientPerformance.networkConnection.value`|Tiempo de conexión de red de carga de página| Tiempo que tarda el explorador en conectarse a la red. Puede ser 0 si se almacena en caché.
`clientPerformance.receiveRequest.value`|Tiempo de recepción de respuesta| Tiempo que transcurre entre que el explorador envía la solicitud hasta que comienza a recibir la respuesta.
`clientPerformance.sendRequest.value`|Tiempo de solicitud de envío| Tiempo que tarda el explorador en enviar la solicitud.
`clientPerformance.total.value`|Tiempo de carga de página del explorador|Tiempo que transcurre entre la solicitud del usuario hasta que se cargan el DOM, las hojas de estilo, los scripts y las imágenes.
`performanceCounter.available_bytes.value`|Memoria disponible|Memoria física inmediatamente disponible para un proceso o para su uso por parte del sistema.
`performanceCounter.io_data_bytes_per_sec.value`|Velocidad de E/S del proceso|Número total de bytes por segundo leídos y escritos en los archivos, la red y los dispositivos.
`performanceCounter.number_of_exceps_thrown_per_sec`|velocidad de excepciones|Excepciones iniciadas por segundo.
`performanceCounter.percentage_processor_time.value`|CPU de procesos|El porcentaje de tiempo transcurrido de todos los subprocesos del proceso usados por el procesador en las instrucciones de ejecución del proceso de aplicaciones.
`performanceCounter.percentage_processor_total.value`|Tiempo de procesador|El porcentaje de tiempo que invierte el procesador en subprocesos no inactivos.
`performanceCounter.process_private_bytes.value`|Bytes privados del proceso|Memoria asignada exclusivamente a los procesos de la aplicación supervisada.
`performanceCounter.request_execution_time.value`|Tiempo de ejecución de solicitud ASP.NET|Tiempo de ejecución de la solicitud más reciente.
`performanceCounter.requests_in_application_queue.value`|Solicitudes ASP.NET en la cola de ejecución|Longitud de la cola de solicitudes de aplicación.
`performanceCounter.requests_per_sec`|Velocidad de solicitudes ASP.NET|Velocidad de todas las solicitudes a la aplicación por segundo desde ASP.NET.
`remoteDependencyFailed.durationMetric.count`|Errores de dependencia|Recuento de llamadas erróneas realizadas por la aplicación de servidor a recursos externos.
`request.duration`|Tiempo de respuesta del servidor|Tiempo entre que se recibe una solicitud HTTP y se termina de enviar la respuesta.
`request.rate`|Velocidad de solicitudes|Velocidad de todas las solicitudes a la aplicación por segundo.
`requestFailed.count`|Error en las solicitudes|Recuento de solicitudes HTTP que dieron lugar a un código de respuesta >= 400 
`view.count`|Vistas de página|Recuento de solicitudes de usuario de cliente de una página web. Se filtra el tráfico sintético.
{el nombre de métrica personalizado}|{El nombre de métrica}|El valor de métrica notificado por [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) o en el [parámetro de medidas de una llamada de seguimiento](app-insights-api-custom-events-metrics.md#properties).

Las métricas se envían por módulos de telemetría diferentes:

Grupo de métricas | Módulo de recopilador
---|---
basicExceptionBrowser,<br/>clientPerformance,<br/>view | [JavaScript de explorador](app-insights-javascript.md)
performanceCounter | [Rendimiento](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [Dependencia](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
request,<br/>requestFailed|[Solicitud de servidor](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)


## Consulte también


* [Crear Application Insights y recursos de pruebas web a partir de plantillas](app-insights-powershell.md)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_1125_2015-->