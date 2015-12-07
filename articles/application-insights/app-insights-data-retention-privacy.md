<properties 
	pageTitle="Retención de datos y almacenamiento en Application Insights" 
	description="Declaración de directiva de retención y privacidad" 
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
	ms.date="11/18/2015" 
	ms.author="awills"/>

# Recopilación, retención y almacenamiento de datos en Application Insights 

*Application Insights se encuentra en su versión de vista previa.*

## Información general

Este artículo ofrece respuestas a preguntas acerca de los datos que [Visual Studio Application Insights][start] recopila y cómo se procesan y almacenan.

Application Insights es un servicio de Azure en versión de vista previa. Durante la versión de vista previa, trabajamos para conseguir proteger sus datos de acuerdo con las directivas descritas en las [Notas sobre seguridad, privacidad y cumplimiento normativo de Azure](http://go.microsoft.com/fwlink/?linkid=392408).


## Colección

#### ¿Cómo recopila datos Application Insights?

El SDK de Application Insights y los agentes que se combinan con la aplicación envían datos al servicio Application Insights. El servicio procesa los datos para proporcionar informes, alertas y otras funcionalidades al usuario. Esto puede incluir los datos que desee capturar mediante la API, por ejemplo, en las propiedades y los eventos personalizados.

#### ¿Qué cantidad de datos puede capturarse? 

**Por segundo**: hasta 500 puntos de datos por segundo por clave de instrumentación (es decir, por aplicación). Para el [nivel de precios][pricing] gratuito, el límite es de 100 dp/s.

Hay tres depósitos que se cuentan por separado:

* [Llamadas a TrackTrace](app-insights-api-custom-events-metrics.md#track-trace) y [registros capturados](app-insights-asp-net-trace-logs.md)
* [Excepciones](app-insights-api-custom-events-metrics.md#track-exception), sujeto a un límite inferior de 50/s.
* Toda la demás telemetría (vistas de páginas, solicitudes, dependencias, métricas, eventos personalizados y resultados de prueba web).

**Mensual**: entre 5 y 15 millones puntos de datos cada mes natural, según su [plan de precios](http://azure.microsoft.com/pricing/details/application-insights/). Excepto para el [nivel de precios][pricing] gratuito, puede comprar capacidad adicional si alcanzó el límite.


Un *punto de datos* es un elemento de telemetría sobre la aplicación enviado al portal de Azure. Se puede enviar por:

* [Módulos de SDK](app-insights-configuration-with-applicationinsights-config.md), que recopilan datos de forma automática, por ejemplo, para informar de una solicitud o un bloqueo, o para medir el rendimiento.
* Las llamadas a [API](app-insights-api-custom-events-metrics.md) `Track...` escritas, como `TrackEvent` o `trackPageView`.
* [Las pruebas web de disponibilidad](app-insights-monitor-web-app-availability.md) que configuró.

Los datos de telemetría incluyen:

* Cada fila de la [búsqueda de diagnóstico](app-insights-diagnostic-search.md).
* Los datos sin procesar desde los que se agregan los gráficos del [Explorador de métricas](app-insights-metrics-explorer.md). Generalmente, los datos de métricas, como los datos de contadores de rendimiento, no aparecen como puntos individuales en el Explorador de métricas.
* Cada resultado de la prueba web de un informe de [disponibilidad](app-insights-monitor-web-app-availability.md).

Los recuentos de usuario y sesión no se incluyen en la cuota para calcular los precios.

*¿Cómo puedo saber cuántos puntos de datos envía mi aplicación?*

* Abra Configuración/cuotas y precios para ver el gráfico de volumen de datos.
* O bien, en el Explorador de métricas, agregue un nuevo gráfico y seleccione **Volumen de punto de datos** como su métrica. Active la agrupación y agrupe por **Tipo de datos**.

*¿Cómo puedo reducir la cantidad de datos que envía mi aplicación?*

* Use [Muestreo](app-insights-sampling.md). Esta tecnología reduce la velocidad de los datos sin sesgar las métricas y sin interrumpir la capacidad de navegar entre los elementos relacionados en la búsqueda.
* [Desactive los colectores de telemetría](app-insights-configuration-with-applicationinsights-config.md) que no necesite.


#### ¿Cuánto tiempo se conservan los datos? 

Depende de su [plan de precios](http://azure.microsoft.com/pricing/details/application-insights/).

Puntos de datos sin procesar (es decir, elementos que se pueden examinar en búsqueda de diagnóstico): entre 7 y 30 días.

Los datos agregados (es decir, recuentos, promedios y otros datos estadísticos que se ven en el Explorador de métricas) se retienen en un minuto para 30 días, y en una hora o un día (en función del tipo) para al menos 13 meses.

#### ¿Qué límites tienen los distintos tipos de datos?

1.	Un máximo de 200 nombres de métrica únicos y 200 nombres de propiedad únicos para la aplicación. Las métricas incluyen el envío de datos a través de TrackMetric, así como mediciones de otros tipos de datos como eventos. Los [nombres de métricas y propiedades][api] son globales por clave de instrumentación, no limitados al tipo de datos.
2.	Las [propiedades][apiproperties] se pueden usar para filtrar y agrupar por estas solo cuando tienen menos de 100 valores únicos para cada propiedad. Después de que los valores únicos superen los 100, la propiedad todavía se puede usar para búsqueda y filtrado, pero no para filtros.
3.	Las propiedades estándar como el nombre de la solicitud y la URL de página se limitan a 1000 valores únicos por semana. Después de 1000 valores únicos, los valores adicionales se marcan como "Otros valores". El valor original puede seguir usándose para la búsqueda de texto completo y el filtrado.


## Access

#### ¿Quién puede ver los datos?

Usted puede ver los datos y, si tiene una cuenta de organización, también pueden los miembros del equipo.

Los puede exportar tanto usted como los miembros del equipo y pueden copiarse a otras ubicaciones y pasarse a otras personas.

#### ¿Qué hace Microsoft con la información que la aplicación envía a Application Insights?

Microsoft usa los datos con el fin exclusivo de proporcionarle el servicio.


## Ubicación

#### ¿Donde se conservan los datos? 

* En Estados Unidos. 

#### ¿Pueden almacenarse en otro sitio, por ejemplo, en Europa? 

* Todavía no. 

## Seguridad 

#### ¿Están seguros mis datos? 

Los datos se almacenan en servidores de Microsoft Azure. La restricciones para las cuentas del Portal de Azure se describen en el [documento sobre seguridad, privacidad y cumplimiento normativo de Azure](http://go.microsoft.com/fwlink/?linkid=392408). Para las cuentas del portal de Visual Studio Team Services, se aplica el documento [Protección de datos de Visual Studio Team Services](http://download.microsoft.com/download/8/E/E/8EE6A61C-44C2-4F81-B870-A267F1DF978C/MicrosoftVisualStudioOnlineDataProtection.pdf).

El acceso a los datos por parte del personal de Microsoft está restringido. El acceso a los datos solo se realiza con su permiso y si es necesario para prestarle soporte en el uso de Application Insights.

Los datos que se agregan en todas las aplicaciones de nuestros clientes (por ejemplo, la velocidad de datos y el tamaño medio de los seguimientos) se usan para mejorar Application Insights.

#### ¿Puede interferir la telemetría de otro usuario con mis datos de Application Insights?

Podría enviarse telemetría adicional a su cuenta mediante el uso de la clave de instrumentación, que se encuentra en el código de las páginas web. Si tienen demasiados datos adicionales, las métricas no representan correctamente el rendimiento y el uso de la aplicación.

Si comparte el código con otros proyectos, no olvide quitar la clave de instrumentación.

## Cifrado

#### ¿Se cifran los datos en los servidores de Application Insights? 

No dentro de los servidores en la actualidad.

Todos los datos se cifran al moverse entre centros de datos.

#### ¿Se cifran los datos en tránsito desde mi aplicación a los servidores de Application Insights?

Sí, se usa https para enviar datos al portal desde casi todos los SDK, incluidos dispositivos y servidores web y páginas web HTTPS. La única excepción son los datos enviados desde páginas web HTTP sin formato.

## Información de identificación personal

#### ¿Puede enviarse información de identificación personal (PII) a Application Insights? 

Sí.

Como regla general:

* La mayor parte de la telemetría estándar (es decir, telemetría enviada sin tener que escribir código) no incluye información de identificación personal explícita. Sin embargo, es posible que puedan identificarse individuos por inferencia a partir de una colección de eventos.
* Los mensajes de excepción y seguimiento pueden incluir información de identificación personal.
* Telemetría personalizada: es decir, llamadas como TrackEvent, que se escriben en el código mediante seguimientos de registro o la API. Puede contener cualquier dato que elija.


La tabla incluida al final de este documento contiene descripciones más detalladas de los datos recopilados.



#### ¿Es mi responsabilidad cumplir las leyes y las regulaciones relativas a la información de identificación personal?

Sí. Es su responsabilidad asegurarse de que la recopilación y el uso de los datos cumplan las leyes y regulaciones respectivas, junto con los términos de Microsoft Online Services.

Debe informar bien a sus clientes sobre los datos que la aplicación recopila y sobre cómo se usan.

#### ¿Pueden los usuarios desactivar Application Insights?

No directamente. No se proporciona un conmutador que los usuarios puedan operar para desactivar Application Insights.

Sin embargo, puede implementar esta característica en la aplicación. Todos los SDK incluyen un valor de configuración de la API que desactiva la recopilación de telemetría.

#### Mi aplicación recopila información confidencial de forma involuntaria. ¿Puede Application Insights limpiar estos datos para que no se conserven?

Application Insights no filtra ni elimina los datos. El usuario debe administrar los datos correctamente y evitar el envío de este tipo de datos a Application Insights.



## Datos enviados por Application Insights

Los SDK varían entre las distintas plataformas y hay varios componentes que se pueden instalar. (Consulte [Introducción a Application Insights][start]). Cada componente envía datos diferentes.

#### Clases de datos que se envían en distintos escenarios

Acción del usuario | Clases de datos recopilados (ver tabla siguiente)
---|---
[Agregar el SDK de Application Insights a un proyecto web de .NET][greenbrown] | ServerContext<br/>Inferidos<br/>Contadores de rendimiento<br/>Solicitudes<br/>**Excepciones**<br/>Sesión<br/>usuarios
[Instalar monitor de estado en IIS][redfield]<br/>[Agregar la extensión AI a una aplicación web o máquina virtual de Azure][azure]|Dependencias<br/>ServerContext<br/>Inferidos<br/>Contadores de rendimiento
[Agregar el SDK de Application Insights a una aplicación web de Java][java]|ServerContext<br/>Inferidos<br/>Solicitud<br/>Sesión<br/>usuarios
[Agregar el SDK de JavaScript a una página web][client]|ClientContext <br/>Inferidos<br/>Página<br/>Rendimiento del cliente
[Agregar el SDK a una aplicación de la Tienda Windows][windows]|DeviceContext<br/>Usuarios<br/>Datos de bloqueo
[Definir propiedades predeterminadas][apiproperties]|**Propiedades** en todos los eventos estándar y personalizados
[Llamar a TrackMetric][api]|Valores numéricos<br/>**Propiedades**
[Llamar a Track*][api]|Nombre del evento<br/>**Propiedades**
[Llamar a TrackException][api]|**Excepciones**<br/>Volcado de pila<br/>**Propiedades**
El SDK no puede recopilar datos. Por ejemplo: <br/> no puede obtener acceso a los contadores de rendimiento<br/> excepción en el inicializador de telemetría | Diagnóstico de SDK
 

Para los [SDK de otras plataformas][platforms], consulte los documentos correspondientes.



#### Clases de los datos recopilados

Clase de datos recopilados | Se incluyen (no es una lista exhaustiva) 
---|---
**Propiedades**|**Cualquier dato - determinado por el código**
DeviceContext |Identificador, IP, configuración regional, modelo de dispositivo, red, tipo de red, nombre de OEM, resolución de pantalla, instancia de rol, nombre de rol, tipo de dispositivo
ClientContext |Sistema operativo, configuración regional, idioma, red, resolución de ventana
Sesión | identificador de sesión
ServerContext |Nombre del equipo, configuración regional, sistema operativo, dispositivo, sesión de usuario, contexto de usuario, operación 
Inferidos |ubicación geográfica de dirección IP, marca de tiempo, sistema operativo, explorador
Métricas | Nombre y valor de la métrica
Eventos | Nombre y valor del evento
PageViews | URL y nombre de página o nombre de pantalla
Rendimiento del cliente | URL o nombre de página, tiempo de carga del explorador
Solicitudes |URL, duración, código de respuesta
Dependencias|Tipo (SQL, HTTP,...), cadena de conexión o URI, sincrónico/asincrónico, duración, éxito, instrucción SQL (con monitor de estado)
**Excepciones** | Tipo, **mensaje**, pilas de llamadas, archivo de origen y número de línea, identificador de subproceso
Bloqueos | Identificador de proceso, identificador de proceso principal, identificador de subproceso de bloqueo; revisión de aplicación, identificador, compilación; tipo de excepción, dirección, razón; símbolos y registros confusos, direcciones binarias inicial y final, nombre binario y ruta de acceso, tipo de cpu
Seguimiento | **Mensaje** y nivel de gravedad
Contadores de rendimiento | Tiempo de procesador, memoria disponible, velocidad de solicitudes, tasa de excepciones, bytes privados del proceso, velocidad de E/S, duración de la solicitud, longitud de la cola de solicitudes
Disponibilidad | Código de respuesta de prueba web, duración de cada paso de la prueba, nombre de la prueba, marca de tiempo, éxito, tiempo de respuesta, ubicación de la prueba
Diagnóstico de SDK | Mensaje de seguimiento o de excepción 

También puede [desactivar algunos de los datos mediante la edición de ApplicationInsights.config][config]


## Créditos

Este producto incluye datos GeoLite2 creados por MaxMind, disponible en [http://www.maxmind.com](http://www.maxmind.com).

## <a name="video"></a>Vídeos

#### Introducción

> [AZURE.VIDEO application-insights-introduction]

#### Primeros pasos

> [AZURE.VIDEO getting-started-with-application-insights]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_1125_2015-->