---
title: "Solución de problemas y preguntas sobre Azure Application Insights | Microsoft Docs"
description: "¿Hay algo en Azure Application Insights que no esté claro o que no funcione? Pruebe aquí."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 182e28e37eb56c547e28524f2a3e13f042238cb4
ms.openlocfilehash: e066a7fc671399ba44bec35a2ea860fccddb4cc5


---
# <a name="questions---application-insights-for-aspnet"></a>Preguntas: Application Insights para ASP.NET
## <a name="configuration-problems"></a>Problemas de configuración
*Tengo problemas con la configuración de:*

* [Aplicación .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Supervisión de una aplicación que ya se está ejecutando](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
* [Diagnóstico de Azure](app-insights-azure-diagnostics.md)
* [Aplicaciones web de Java](app-insights-java-troubleshoot.md)
* [Otras plataformas](app-insights-platforms.md)

*No recibo datos de mi servidor*

* [Establecer excepciones del firewall](app-insights-ip-addresses.md)
* [Configurar un servidor ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Configurar un servidor de Java](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>¿Se puede usar Application Insights con...?
[Ver plataformas][platforms]

## <a name="is-it-free"></a>¿Es gratis?

Sí, para su uso experimental. En el plan de precios básico, la aplicación puede enviar una determinada asignación de datos cada mes de forma gratuita. La asignación disponible es lo suficientemente amplia como para abarcar el desarrollo y la publicación de una aplicación para un número reducido de usuarios. Puede establecer un límite para evitar que se procese más de una determinada cantidad de datos.

Debe utilizar el plan Enterprise para obtener determinadas características, como Exportación continua. Se cobrará un importe diario.

[Lea el plan de precios](https://azure.microsoft.com/pricing/details/application-insights/).


## <a name="a-nameq14awhat-does-application-insights-modify-in-my-project"></a><a name="q14"></a>¿Qué modifica Application Insights en mi proyecto?
Los detalles dependen del tipo de proyecto. Para una aplicación web:

* Agregue estos archivos al proyecto:

  * ApplicationInsights.config.
  * ai.js
* Instale estos paquetes de NuGet:

  * *API de Application Insights* : la API central.
  * *API de Application Insights para aplicaciones web* : se usa para enviar la telemetría del servidor.
  * *API de Application Insights para aplicaciones JavaScript* : se usa para enviar la telemetría del cliente.

    El paquete incluye estos ensamblados:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Inserta elementos en:

  * Web.config
  * packages.config
* (Solo nuevos proyectos: si [agrega Application Insights a un proyecto existente][start], tiene que hacerlo manualmente). Inserte fragmentos de código en el código de cliente y servidor para inicializarlos con el identificador de recursos de Application Insights. Por ejemplo, en una aplicación MVC, el código se inserta en la página maestra Views/Shared/_Layout.cshtml.

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>¿Cómo se puede actualizar desde versiones anteriores de SDK?
Consulte las [notas de la versión](app-insights-release-notes.md) del SDK adecuado para el tipo de aplicación.

## <a name="a-nameupdateahow-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>¿Cómo puedo cambiar el recurso de Azure al que mi proyecto envía datos?
En el Explorador de soluciones, haga clic con el botón derecho en `ApplicationInsights.config` y elija **Actualizar Application Insights**. Puede enviar los datos a un recurso nuevo o existente en Azure. El Asistente para actualización cambia la clave de instrumentación en ApplicationInsights.config, que determina dónde el SDK del servidor envía los datos. A menos que desactive la opción "Actualizar todo", también cambiará la clave donde aparece en las páginas web.

#### <a name="a-namedataahow-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>¿Cuánto tiempo se retienen los datos en el portal? ¿Es seguro?
Eche un vistazo a [Privacidad y retención de los datos][data].

## <a name="logging"></a>Registro
#### <a name="a-namepostahow-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>¿Cómo puedo ver datos POST en Búsqueda de diagnóstico?
Los datos POST no se registran automáticamente, pero se puede usar una llamada TrackTrace: incluya los datos en el parámetro de mensaje. Este tiene un límite de tamaño superior al de las propiedades de cadena, aunque no se puede filtrar por él.

## <a name="security"></a>Seguridad
#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>¿Están mis datos seguros en el portal? ¿Cuánto tiempo se conservan?
Consulte [Privacidad y retención de los datos][data].

## <a name="a-nameq17a-have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> ¿He habilitado todo en Application Insights?
| Qué debería ver | Cómo conseguirlo | Razones para quererlo |
| --- | --- | --- |
| Gráficos de disponibilidad |[Pruebas web](app-insights-monitor-web-app-availability.md) |Saber que la aplicación web funciona |
| Rendimiento de la aplicación de servidor: tiempos de respuesta, etc. |[Agregue Application Insights a su proyecto](app-insights-asp-net.md) o [instale el Monitor de estado de Application Insights en un servidor](app-insights-monitor-performance-live-website-now.md) (o escriba su propio código para [realizar un seguimiento de las dependencias](app-insights-api-custom-events-metrics.md#trackdependency)). |Detectar problemas de rendimiento |
| Telemetría de dependencia |[Instalar el Monitor de estado de Application Insights en el servidor](app-insights-monitor-performance-live-website-now.md) |Diagnosticar problemas con las bases de datos u otros componentes externos |
| Obtener seguimientos de pila de las excepciones |[Insertar llamadas TrackException en el código](app-insights-search-diagnostic-logs.md#exceptions) (aunque algunas se notifican automáticamente) |Detectar y diagnosticar excepciones |
| Buscar seguimientos del registro |[Agregar un adaptador de registro](app-insights-search-diagnostic-logs.md) |Diagnosticar excepciones, problemas de rendimiento |
| Aspectos básicos del uso de cliente: vistas de página, sesiones,... |[Inicializador de JavaScript en páginas web](app-insights-javascript.md) |Análisis de uso |
| Métricas personalizadas de cliente |[Seguimiento de llamadas en páginas web](app-insights-api-custom-events-metrics.md) |Mejorar la experiencia del usuario |
| Métricas personalizadas de servidor |[Seguimiento de llamadas en el código de servidor](app-insights-api-custom-events-metrics.md) |Business intelligence |

## <a name="automation"></a>Automation
Puede [escribir scripts de PowerShell](app-insights-powershell.md) para crear y actualizar los recursos de Application Insights.

## <a name="more-answers"></a>Más respuestas
* [Foro de Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md



<!--HONumber=Feb17_HO2-->


