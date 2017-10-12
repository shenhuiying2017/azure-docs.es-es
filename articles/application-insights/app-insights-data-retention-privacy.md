---
title: "Retención y almacenamiento de datos en Azure Application Insights | Microsoft Docs"
description: "Declaración de directiva de retención y privacidad"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: bwren
ms.openlocfilehash: ddb9fa516da66da0484619439848583a29e1f5c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Recopilación, retención y almacenamiento de datos en Application Insights


Al instalar el SDK de [Azure Application Insights][start] en su aplicación, se envía telemetría sobre la aplicación a la nube. Naturalmente, los desarrolladores responsables desean saber exactamente qué datos se envían, qué les ocurre a los datos y cómo puede mantener el control de los mismos. En concreto, si se puede enviar información confidencial, dónde se almacena y su nivel de seguridad. 

En primer lugar, la respuesta corta:

* Es improbable que los módulos de telemetría estándar que se ejecutan "de fábrica" envíen información confidencial al servicio. La telemetría se ocupa de la carga, las métricas de rendimiento y uso, los informes de excepciones y otros datos de diagnóstico. Los principales datos del usuario que aparecen los informes de diagnóstico son direcciones URL; pero, en cualquier caso, la aplicación no debería colocar información confidencial como texto sin formato en una dirección URL.
* Puede escribir código que envíe telemetría personalizada adicional que le ayude con el uso de la supervisión y el diagnóstico. (Esta extensibilidad es una excelente característica de Application Insights.) Por error, sería posible escribir este código de modo que incluya datos personales y otra información confidencial. Si la aplicación trabaja con estos datos, debe aplicar procesos de revisión exhaustivos a todo el código que escriba.
* Al desarrollar y probar la aplicación, es fácil de inspeccionar lo que envía el SDK. Los datos aparecen en las ventanas de salida de depuración tanto del IDE como del explorador. 
* Los datos se almacenan en servidores de [Microsoft Azure](http://azure.com) de EE. UU o Europa. (La aplicación puede ejecutarse en cualquier lugar). Azure tiene [procesos de seguridad exhaustivos y satisface un amplio intervalo de estándares de cumplimiento](https://azure.microsoft.com/support/trust-center/). Solo usted y el equipo que designe tienen acceso a sus datos. El personal de Microsoft puede acceder a ellos de forma restringida solo en circunstancias concretas y siempre con su conocimiento. Dichos datos están cifrados durante el tránsito, pero no así en los servidores.

En el resto de este artículo se describen más detalladamente estas respuestas. El artículo está diseñado para ser independiente, por lo que puede mostrarlo a compañeros que no formen parte de su equipo.

## <a name="what-is-application-insights"></a>¿Qué es Application Insights?
[Azure Application Insights][start] es un servicio que proporciona Microsoft que le ayuda a mejorar el rendimiento y la facilidad de uso de la aplicación activa. Supervisa la aplicación durante todo el tiempo que se ejecute, tanto durante las pruebas como después de haberla publicado o implementado. Application Insights crea gráficos y tablas que muestran, por ejemplo, en qué horas del día se obtiene la mayoría de los usuarios, la capacidad de respuesta de la aplicación y lo bien que la atienden los servicios externos de los que depende. Si hay bloqueos, errores o problemas de rendimiento, puede buscar en los datos de la telemetría para diagnosticar la causa. Y el servicio le enviará mensajes de correo electrónico si se produce cualquier tipo de cambio en la disponibilidad y rendimiento de la aplicación.

Para obtener esta funcionalidad, instale un SDK de Application Insights en la aplicación, que se convierte en parte de su código. Cuando se ejecuta la aplicación, el SDK supervisa su funcionamiento y envía la telemetría al servicio Application Insights. Se trata de un servicio en la nube hospedado por [Microsoft Azure](http://azure.com). (No obstante, Application Insights funciona con cualquier aplicación, no solo con las hospedadas en Azure.)

![El SDK de la aplicación envía datos de telemetría al servicio Application Insights.](./media/app-insights-data-retention-privacy/01-scheme.png)

El servicio de Application Insights almacena y analiza los datos de telemetría. Para ver el análisis o realizar búsquedas en la telemetría almacenada, inicie sesión en su cuenta de Azure y abra el recurso Application Insights de la aplicación. También puede compartir el acceso a los datos con otros miembros del equipo o con determinados suscriptores de Azure.

Los datos exportados del servicio Application Insights se pueden exportar, por ejemplo a una base de datos o a herramientas externas. Proporcione a cada herramienta una clave especial, que se obtiene desde el servicio. Si es necesario, dicha clave se puede revocar. 

Los SDK de Application Insights están disponible para varios tipos de aplicación: servicios web hospedados en sus propios servidores J2EE o ASP.NET, o en Azure; clientes web (es decir, el código se ejecuta en una página web); aplicaciones de escritorio y servicios; aplicaciones para dispositivos como Windows Phone, iOS y Android. Todos ellos envían telemetría al mismo servicio.

## <a name="what-data-does-it-collect"></a>¿Qué datos recopila?
### <a name="how-is-the-data-is-collected"></a>¿Cómo se recopilan los datos?
Hay tres orígenes de datos:

* El SDK, que se integra con la aplicación en la [fase de desarrollo](app-insights-asp-net.md) o [en tiempo de ejecución](app-insights-monitor-performance-live-website-now.md). Existen distintos SDK para los diferentes tipos de aplicaciones. También hay un [SDK para páginas web](app-insights-javascript.md), que se carga en el explorador del usuario final junto con la página.
  
  * Cada SDK tiene varios [módulos](app-insights-configuration-with-applicationinsights-config.md), que emplean diferentes técnicas para recopilar distintos tipos de datos de telemetría.
  * Si instala el SDK en la fase de desarrollo, puede usar su API para enviar su propia telemetría, además de los módulos estándar. Esta telemetría personalizada puede incluir los datos que desee enviar.
* En algunos servidores web, también hay agentes que se ejecutan junto con la aplicación y envían datos de telemetría de la CPU, memoria y ocupación de la red. Por ejemplo, las máquinas virtuales de Azure, los hosts de Docker y los [servidores J2EE](app-insights-java-agent.md) pueden tener dichos agentes.
* [pruebas de disponibilidad](app-insights-monitor-web-app-availability.md) son procesos que ejecuta Microsoft que envían solicitudes a una aplicación web a intervalos regulares. Los resultados se envían al servicio Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>¿Qué tipos de datos se recopilan?
Las principales categorías son:

* [Telemetría de servidor web](app-insights-asp-net.md) : solicitudes HTTP.  Identificador URI, tiempo invertido en procesar la solicitud, código de respuesta y dirección IP del cliente. Identificador de sesión.
* [Páginas web](app-insights-javascript.md) : contadores de sesión, página y usuario. Tiempos de carga de las páginas. Excepciones. Llamadas AJAX.
* Contadores de rendimiento: memoria, CPU, E/S, ocupación de la red.
* Contexto de cliente y servidor: SO, configuración regional, tipo de dispositivo, explorador y resolución de pantalla.
* [Excepciones](app-insights-asp-net-exceptions.md) y bloqueos: **volcados de pila**, identificador de compilación y tipo de CPU. 
* [Dependencias](app-insights-asp-net-dependencies.md) : llamadas a servicios externos como REST, SQL y AJAX. Identificador URI o cadena de conexión, duración, con éxito, comando.
* [Pruebas de disponibilidad](app-insights-monitor-web-app-availability.md) : duración de prueba y pasos, respuestas.
* [Registros de seguimiento](app-insights-asp-net-trace-logs.md) y [telemetría personalizada](app-insights-api-custom-events-metrics.md) - **: todo el código que se escribe en los registros o telemetría**.

[Más detalle](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>¿Cómo se puede comprobar lo que se recopila?
Si desarrolla la aplicación mediante Visual Studio, ejecútela en modo de depuración (F5). Los datos de telemetría aparecen en la ventana de salida. Desde ahí, puede copiarlos y darles formato como JSON para facilitar su inspección. 

![](./media/app-insights-data-retention-privacy/06-vs.png)

También existe una vista más legible en la ventana Diagnóstico.

En el caso de las páginas web, abra la ventana de depuración del explorador.

![Presione F12 y abra la pestaña Red.](./media/app-insights-data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>¿Puedo escribir código para filtrar los datos de telemetría antes de enviarlos?
Sí, es posible, solo es preciso escribir un [complemento de procesador de telemetría](app-insights-api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>¿Cuánto tiempo se conservan los datos?
Los puntos de datos sin procesar (es decir, elementos que puede consultar en Analytics e inspeccionar en la búsqueda) se conservan hasta 90 días. Si necesita mantener los datos más tiempo, puede usar la [exportación continua](app-insights-export-telemetry.md) para copiarlos a una cuenta de almacenamiento.

Los datos agregados (es decir, recuentos, promedios y otros datos estadísticos que se ven en el Explorador de métricas) se retienen con un nivel de detalle de un minuto durante 90 días.

## <a name="who-can-access-the-data"></a>¿Quién puede acceder a los datos?
Usted puede ver los datos y, si tiene una cuenta de organización, también pueden los miembros del equipo. 

Los puede exportar tanto usted como los miembros del equipo y pueden copiarse a otras ubicaciones y pasarse a otras personas.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>¿Qué hace Microsoft con la información que la aplicación envía a Application Insights?
Microsoft usa los datos con el fin exclusivo de proporcionarle el servicio.

## <a name="where-is-the-data-held"></a>¿Donde se conservan los datos?
* En los Estados Unidos o en Europa. Puede seleccionar la ubicación cuando se crea un nuevo recurso de Application Insights. 


#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-or-europe"></a>¿Significa que la aplicación tiene que estar hospedada en Estados Unidos o Europa?
* No. La aplicación puede ejecutarse desde cualquier lugar, en sus propios hosts locales en la nube.

## <a name="how-secure-is-my-data"></a>¿Están seguros mis datos?
Application Insights es un servicio de Azure. Las directivas de seguridad se describen en las [notas del producto de seguridad, privacidad y cumplimiento de Azure](http://go.microsoft.com/fwlink/?linkid=392408).

Los datos se almacenan en servidores de Microsoft Azure. En el caso de las cuentas del Portal de Azure, las restricciones se describen en el documento [Trusted Cloud: Microsoft Azure Security, Privacy, and Compliance](http://go.microsoft.com/fwlink/?linkid=392408).

El acceso a los datos por parte del personal de Microsoft está restringido. El acceso a los datos solo se realiza con su permiso y si es necesario para prestarle soporte en el uso de Application Insights. 

Los datos que se agregan en todas las aplicaciones de nuestros clientes (por ejemplo, la velocidad de datos y el tamaño medio de los seguimientos) se usan para mejorar Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>¿Puede interferir la telemetría de otro usuario con mis datos de Application Insights?
Podría enviarse telemetría adicional a su cuenta mediante el uso de la clave de instrumentación, que se encuentra en el código de las páginas web. Si tienen demasiados datos adicionales, las métricas no representan correctamente el rendimiento y el uso de la aplicación.

Si comparte el código con otros proyectos, no olvide quitar la clave de instrumentación.

## <a name="is-the-data-encrypted"></a>¿Se cifran los datos?
No dentro de los servidores en la actualidad.

Todos los datos se cifran al moverse entre centros de datos.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>¿Se cifran los datos en tránsito desde mi aplicación a los servidores de Application Insights?
Sí, se usa https para enviar datos al portal desde casi todos los SDK, incluidos dispositivos y servidores web y páginas web HTTPS. La única excepción son los datos enviados desde páginas web HTTP sin formato. 

## <a name="personally-identifiable-information"></a>Información de identificación personal
#### <a name="could-personally-identifiable-information-pii-be-sent-to-application-insights"></a>¿Puede enviarse información de identificación personal (PII) a Application Insights?
Sí, es posible. 

Como regla general:

* La mayor parte de la telemetría estándar (es decir, telemetría enviada sin tener que escribir código) no incluye información de identificación personal explícita. Sin embargo, es posible que puedan identificarse individuos por inferencia a partir de una colección de eventos.
* Los mensajes de excepción y seguimiento pueden incluir información de identificación personal.
* Telemetría personalizada: es decir, llamadas como TrackEvent, que se escriben en el código mediante seguimientos de registro o la API. Puede contener cualquier dato que elija.

La tabla incluida al final de este documento contiene descripciones más detalladas de los datos recopilados.

#### <a name="am-i-responsible-for-complying-with-laws-and-regulations-in-regard-to-pii"></a>¿Es mi responsabilidad cumplir las leyes y las regulaciones relativas a la información de identificación personal?
Sí. Es su responsabilidad asegurarse de que la recopilación y el uso de los datos cumplan las leyes y regulaciones respectivas, junto con los términos de Microsoft Online Services.

Debe informar bien a sus clientes sobre los datos que la aplicación recopila y sobre cómo se usan.

#### <a name="can-my-users-turn-off-application-insights"></a>¿Pueden los usuarios desactivar Application Insights?
No directamente. No se proporciona un conmutador que los usuarios puedan operar para desactivar Application Insights.

Sin embargo, puede implementar esta característica en la aplicación. Todos los SDK incluyen un valor de configuración de la API que desactiva la recopilación de telemetría. 

#### <a name="my-application-is-unintentionally-collecting-sensitive-information-can-application-insights-scrub-this-data-so-it-isnt-retained"></a>Mi aplicación recopila información confidencial de forma involuntaria. ¿Puede Application Insights limpiar estos datos para que no se conserven?
Application Insights no filtra ni elimina los datos. El usuario debe administrar los datos correctamente y evitar el envío de este tipo de datos a Application Insights.

## <a name="data-sent-by-application-insights"></a>Datos enviados por Application Insights
Los SDK varían entre las distintas plataformas y hay varios componentes que se pueden instalar. (Consulte [Introducción a Application Insights][start]). Cada componente envía datos diferentes.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Clases de datos que se envían en distintos escenarios
| Acción del usuario | Clases de datos recopilados (ver tabla siguiente) |
| --- | --- |
| [Agregar el SDK de Application Insights a un proyecto web de .NET][greenbrown] |ServerContext<br/>Inferidos<br/>Contadores de rendimiento<br/>Solicitudes<br/>**Excepciones**<br/>Sesión<br/>users |
| [Instalar el Monitor de estado en IIS][redfield] |Dependencias<br/>ServerContext<br/>Inferidos<br/>Contadores de rendimiento |
| [Incorporar el SDK de Application Insights a una aplicación web de Java][java] |ServerContext<br/>Inferidos<br/>Solicitud<br/>Sesión<br/>users |
| [Incorporar el SDK de JavaScript a una página web][client] |ClientContext  <br/>Inferidos<br/>Page<br/>ClientPerf<br/>Ajax |
| [Definir propiedades predeterminadas][apiproperties] |**Propiedades** en todos los eventos estándar y personalizados |
| [Llamar a TrackMetric][api] |Valores numéricos<br/>**Propiedades** |
| [Llamar a Track*][api] |Nombre del evento<br/>**Propiedades** |
| [Llamar a TrackException][api] |**Excepciones**<br/>Volcado de la pila<br/>**Propiedades** |
| El SDK no puede recopilar datos. Por ejemplo: <br/> - no se puede acceder a los contadores de rendimiento<br/> - excepción en el inicializador de telemetría |Diagnóstico de SDK |

Para los [SDK de otras plataformas][platforms], consulte los documentos correspondientes.

#### <a name="the-classes-of-collected-data"></a>Clases de los datos recopilados
| Clase de datos recopilados | Se incluyen (no es una lista exhaustiva) |
| --- | --- |
| **Propiedades** |**Cualquier dato - determinado por el código** |
| DeviceContext |Identificador, IP, configuración regional, modelo de dispositivo, red, tipo de red, nombre de OEM, resolución de pantalla, instancia de rol, nombre de rol, tipo de dispositivo |
| ClientContext  |Sistema operativo, configuración regional, idioma, red, resolución de ventana |
| Sesión |identificador de sesión |
| ServerContext |Nombre del equipo, configuración regional, sistema operativo, dispositivo, sesión de usuario, contexto de usuario, operación |
| Inferidos |ubicación geográfica de dirección IP, marca de tiempo, sistema operativo, explorador |
| Métricas |Nombre y valor de la métrica |
| Eventos |Nombre y valor del evento |
| PageViews |URL y nombre de página o nombre de pantalla |
| Rendimiento del cliente |URL o nombre de página, tiempo de carga del explorador |
| Ajax |Llamadas HTTP de la página web al servidor |
| Solicitudes |URL, duración, código de respuesta |
| Dependencias |Tipo (SQL, HTTP,...), cadena de conexión o URI, sincrónico/asincrónico, duración, éxito, instrucción SQL (con monitor de estado) |
| **Excepciones** |Tipo, **mensaje**, pilas de llamadas, archivo de origen y número de línea, identificador de subproceso |
| Bloqueos |Identificador de proceso, identificador de proceso principal, identificador de subproceso de bloqueo; revisión de aplicación, identificador, compilación; tipo de excepción, dirección, razón; símbolos y registros confusos, direcciones binarias inicial y final, nombre binario y ruta de acceso, tipo de cpu |
| Seguimiento |**Mensaje** y nivel de gravedad |
| Contadores de rendimiento |Tiempo de procesador, memoria disponible, velocidad de solicitudes, tasa de excepciones, bytes privados del proceso, velocidad de E/S, duración de la solicitud, longitud de la cola de solicitudes |
| Disponibilidad |Código de respuesta de prueba web, duración de cada paso de la prueba, nombre de la prueba, marca de tiempo, éxito, tiempo de respuesta, ubicación de la prueba |
| Diagnóstico de SDK |Mensaje de seguimiento o de excepción |

También puede [desactivar algunos de los datos mediante la edición de ApplicationInsights.config][config]

## <a name="credits"></a>Créditos
Este producto incluye datos GeoLite2 creados por MaxMind, disponible en [http://www.maxmind.com](http://www.maxmind.com).



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

