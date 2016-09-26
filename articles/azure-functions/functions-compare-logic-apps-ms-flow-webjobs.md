<properties
	pageTitle="Elección entre Flow, Logic Apps, Functions y WebJobs | Microsoft Azure"
	description="Compare y contraste los servicios de integración en la nube de Microsoft y decida cuál usar."
	services="functions,app-service\logic"
	documentationCenter="na"
	authors="cephalin"
	manager="wpickett"
	tags=""
	keywords="microsoft flow, flow, logic apps, azure functions, functions, azure webjobs, webjobs, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="09/08/2016"
	ms.author="chrande; glenga"/>

# Elección entre Flow, Logic Apps, Functions y WebJobs

En este artículo se comparan y contrastan los siguientes servicios en la nube de Microsoft. Todos ellos pueden resolver los problemas de integración y la automatización de los procesos empresariales:

- [Microsoft Flow](https://flow.microsoft.com/)
- [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
- [Funciones de Azure](https://azure.microsoft.com/services/functions/)
- [Azure App Service](../app-service-web/web-sites-create-web-jobs.md)

Todos estos servicios son útiles cuando se agrupan juntos sistemas dispares. Todos pueden definir entradas, acciones, condiciones y salidas. Todos se pueden ejecutar según una programación o un desencadenador. Sin embargo, cada servicio aporta un conjunto único de valores, y no se comparan para saber cuál es el mejor, sino cuál es el más adecuado para su situación. Con frecuencia, la mejor manera de crear rápidamente una solución de integración completa y escalable es combinar estos servicios.

<a name="flow"></a>
## Flow frente a Aplicaciones lógicas

Podemos hablar de Microsoft Flow y Azure Logic Apps de forma conjunta porque ambos son servicios de integración con una *configuración de línea base*, lo que facilita la creación de procesos y flujos de trabajo y la integración con varias aplicaciones SaaS y de empresa.

- Flow se crea encima de Logic Apps
- Tienen el mismo diseñador de flujo de trabajo
- Los [conectores](../connectors/apis-list.md) que funcionan en uno también pueden funcionar en el otro

Flows permite a cualquier trabajador de oficina realizar integraciones sencillas (por ejemplo, obtener SMS para correos electrónicos importantes) sin pasar por los desarrolladores o el departamento de TI. Por otro lado, Logic Apps puede permitir integraciones avanzadas o críticas (por ejemplo, procesos B2B) donde se requieren procedimientos DevOps y de seguridad. Lo normal es que la complejidad de un flujo de trabajo crezca con el tiempo. En consecuencia, puede comenzar con un flujo al principio y luego convertirlo en una aplicación lógica cuando sea necesario.

La tabla siguiente le ayuda a determinar qué es lo mejor para una integración dada, si Flow o Logic Apps.

| | Flujo | Aplicaciones lógicas |
|---------------|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| Público | trabajadores de oficina, usuarios empresariales | profesionales de TI, desarrolladores |
| Escenarios | Autoservicio | Críticas |
| Herramienta de diseño | En el explorador, solo interfaz de usuario | En el explorador y [Visual Studio](../app-service/logic/app-service-logic-deploy-from-vs.md), [visualización del código](../app-service-logic/app-service-logic-author-definitions.md) disponible |
| DevOps | Ad hoc, desarrollo en producción | control del código fuente, prueba, soporte técnico, automatización y manejabilidad en [Administración de recursos de Azure](../app-service-logic/app-service-logic-arm-provision.md)|
| Experiencia del administrador| [https://flow.microsoft.com](https://flow.microsoft.com) | [https://portal.azure.com](https://portal.azure.com) |
| Seguridad | Procedimientos estándar: [soberanía de datos](https://wikipedia.org/wiki/Technological_Sovereignty), [cifrado en reposo](https://wikipedia.org/wiki/Data_at_rest#Encryption) para datos confidenciales, etc. | Garantía de seguridad de Azure: [Seguridad de Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Security Center](https://azure.microsoft.com/services/security-center/), [registros de auditoría](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/), y mucho más. |

<a name="function"></a>
## Functions frente a Trabajos web

Analizaremos Azure Functions y Azure App Service WebJobs juntos porque ambos son servicios de integración *Code First* y están diseñados para los desarrolladores. Le permiten ejecutar un script o un fragmento de código en respuesta a distintos eventos, como [nuevos blobs de almacenamiento](functions-bindings-storage.md) o [una solicitud de WebHook](functions-bindings-http-webhook.md). Estas son sus similitudes:

- Ambos se basan en [Azure App Service](../app-service/app-service-value-prop-what-is.md) y disfrutan de características como [control del código fuente](../app-service-web/app-service-continuous-deployment.md), [autenticación](../app-service/app-service-authentication-overview.md) y [supervisión](../app-service-web/web-sites-monitor.md).
- Ambos son servicios orientados al desarrollador.
- Ambos admiten lenguajes de script y de programación estándar.
- Ambos con compatibles con NuGet y NPM.

Functions es la evolución natural de WebJobs ya que toma lo mejor de WebJobs y lo mejora. Estas mejoras incluyen:

- Desarrollo, prueba y ejecución de código optimizados, directamente en el explorador.
- Integración incorporada con más servicios de Azure y servicios de terceros como [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
- Pago por uso, sin necesidad de pagar un [plan de App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
- [Escalado dinámico](functions-scale.md) automático.
- Los clientes existentes de App Service aún pueden ejecutar un plan de App Service (para aprovechar las ventajas de recursos infrautilizados).
- Integración con Logic Apps.

En la tabla siguiente se resumen las diferencias entre Functions y WebJobs:

| | Funciones | Trabajos web |
|------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------|
| Escalado | Escalado sin configuración | escalado con plan de App Service |
| Precios | Pago por uso o parte del plan de App Service | parte del plan de App Service |
| Tipo ejecución | desencadenada, programada (por desencadenador de temporizador) | desencadenada, continua, programada |
| Desencadenar eventos | [temporizador](functions-bindings-timer.md), [Azure DocumentDB](functions-bindings-documentdb.md), [Azure Event Hubs](functions-bindings-event-hubs), [HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md), [Azure App Service Mobile Apps](functions-bindings-mobile-apps.md), [Azure Notification Hubs](functions-bindings-notification-hubs.md), [Azure Service Bus](functions-bindings-service-bus.md), [Azure Storage](articles/functions-bindings-storage.md) | [Azure Storage](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), [Azure Service Bus](websites-dotnet-webjobs-sdk-service-bus.md) |
| Desarrollo en el explorador | x | |
| Scripts de ventana | experimental | x |
| PowerShell | experimental | x |
| C# | x | x |
| F# | x | |
| Bash | experimental | x |
| PHP | experimental | x |
| Python | experimental | x |
| JavaScript | x | x |
| Java | experimental | x |

El uso de Functions o WebJobs depende en última instancia de lo que esté haciendo con App Service. Si tiene una aplicación de App Service para la que quiere ejecutar fragmentos de código y los quiere administrar juntos en el mismo entorno de DevOps, debería usar WebJobs. Si quiere ejecutar fragmentos de código para otros servicios de Azure o incluso aplicaciones de terceros, si quiere administrar los fragmentos de código de integración por separado de las aplicaciones de App Service o si quiere llamar a los fragmentos de código desde una aplicación lógica, debe aprovechar las ventajas de todas las mejoras que incorpora Functions.

<a name="together"></a>
## Flow, Logic Apps y Functions juntos

Como se mencionó anteriormente, el servicio más adecuado dependerá de su situación.

- Para una optimización empresarial sencilla, use Flow.
- Si su escenario de integración es demasiado avanzado para Flow, o si necesita funcionalidades de DevOps y cumplimiento de seguridad, use entonces Logic Apps.
- Si en uno de los pasos del escenario de integración hace falta una gran transformación personalizada o código especializado, escriba una aplicación de función y luego desencadene una función como una acción en la aplicación lógica.

Puede llamar a una aplicación lógica en un flujo. También puede llamar a una función en una aplicación lógica y a una aplicación lógica en una función. La integración entre Flow, Logic Apps y Functions sigue mejorando con el tiempo. Puede crear algo en un servicio y usarlo en los otros. Por lo tanto, cualquier inversión que realice en estas tres tecnologías merecerá la pena.

## Pasos siguientes

Comience con cada uno de estos servicios creando su primer flujo, aplicación lógica, aplicación de función o trabajo web. Haga clic en cualquiera de los siguientes vínculos:

- [Get started with Microsoft Flow](https://flow.microsoft.com/es-ES/documentation/getting-started/) (Introducción a Microsoft Flow)
- [Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Creación de su primera función de Azure](../azure-functions/functions-create-first-azure-function.md)
- [Implementar trabajos web con Visual Studio](../app-service-web/websites-dotnet-deploy-webjobs.md)

O bien, obtenga más información sobre estos servicios de integración con los siguientes vínculos:

- [Leveraging Azure Functions & Azure App Service for integration scenarios](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Aprovechamiento de Azure Functions y Azure App Service para escenarios de integración) por Christopher Anderson
- [Integrations Made Simple](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/) (Integraciones sencillas) por Charles Lamanna
- [Webcast en directo sobre Logic Apps](http://aka.ms/logicappslive)
- [Preguntas frecuentes sobre Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)
- [Recursos de documentación de WebJobs de Azure](../app-service-web/websites-webjobs-resources.md)

<!---HONumber=AcomDC_0914_2016-->