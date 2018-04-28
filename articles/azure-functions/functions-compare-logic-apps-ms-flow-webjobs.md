---
title: Comparativa de Flow, Logic Apps, Functions y WebJobs en Azure
description: 'Comparativa de los servicios en la nube de Microsoft optimizados para las tareas de integración: Flow, Logic Apps, Functions y WebJobs.'
services: functions,app-service\logic
documentationcenter: na
author: tdykstra
manager: cfowler
tags: ''
keywords: microsoft flow, flow, logic apps, azure functions, functions, azure webjobs, webjobs, procesamiento de eventos, proceso dinámico, arquitectura sin servidor
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/09/2018
ms.author: tdykstra
ms.custom: mvc
ms.openlocfilehash: 23501eddc8921ed36a9e7d839660455e04ee9381
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="compare-flow-logic-apps-functions-and-webjobs"></a>Comparativa de Flow, Logic Apps, Functions y WebJobs

En este artículo se comparan los siguientes servicios en la nube de Microsoft:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service](../app-service/web-sites-create-web-jobs.md)

Todos estos servicios pueden solucionar problemas de integración y automatizar procesos empresariales. Todos pueden definir entradas, acciones, condiciones y salidas. Todos se pueden ejecutar según una programación o un desencadenador. Sin embargo, cada servicio tiene unas ventajas únicas y este artículo explica las diferencias.

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Comparativa entre Microsoft Flow y Azure Logic Apps

Flow y Logic Apps son servicios de integración *orientados al diseñador* que pueden crear flujos de trabajo. Ambos servicios se integran con varias aplicaciones SaaS y empresariales. 

Flow se crea encima de Logic Apps. Ambas comparten el mismo diseñador de flujos de trabajo y los mismos [conectores](../connectors/apis-list.md). 

Flow permite a los administrativos realizar integraciones sencillas (por ejemplo, un proceso de aprobación en una biblioteca de documentos de SharePoint) sin pasar por los desarrolladores o el departamento de TI. Por otro lado, Logic Apps puede permitir integraciones avanzadas (por ejemplo, procesos B2B) donde se requieren procedimientos DevOps y de seguridad. Lo normal es que la complejidad de un flujo de trabajo empresarial aumente con el tiempo. En consecuencia, puede comenzar con un flujo al principio y luego convertirlo en una aplicación lógica cuando sea necesario.

La tabla siguiente le ayuda a determinar qué es lo mejor para una integración dada, si Flow o Logic Apps.

|  | Flujo | Logic Apps |
| --- | --- | --- |
| Usuarios |Administrativos, usuarios empresariales, administradores de SharePoint |Integradores profesionales y desarrolladores, profesionales de TI |
| Escenarios |Autoservicio |Integraciones avanzadas |
| Herramienta de diseño |En el explorador y aplicación móvil, solo UI |En el explorador y [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [visualización del código](../logic-apps/logic-apps-author-definitions.md) disponible |
| Administración del ciclo de vida de las aplicaciones (ALM) |Diseño y pruebas en entornos no destinados a producción, paso a producción cuando sea posible. |DevOps: control del código fuente, pruebas, soporte técnico, automatización y facilidad de administración en [Azure Resource Management](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Experiencia del administrador |Administración de directivas de prevención de pérdida de datos (DLP) y entornos de flujo, seguimiento de licencias [https://admin.flow.microsoft.com](https://admin.flow.microsoft.com) |Administración de grupos de recursos, conexiones, administración de acceso y registro [https://portal.azure.com](https://portal.azure.com) |
| Seguridad |Registros de auditoría de Seguridad y cumplimiento de Office 365, Prevención de pérdida de datos (DLP), [cifrado en reposo](https://wikipedia.org/wiki/Data_at_rest#Encryption) para datos confidenciales, etcétera. |Garantía de seguridad de Azure: [Seguridad de Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Security Center](https://azure.microsoft.com/services/security-center/), [registros de auditoría](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/), y mucho más. |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Comparativa entre Azure Functions y Azure Logic Apps

Functions y Logic Apps son servicios de Azure que posibilitan las cargas de trabajo sin servidor. Azure Functions es un servicio de proceso sin servidor, mientras que Azure Logic Apps proporciona flujos de trabajo sin servidor. Con ambos se pueden crear *orquestaciones* complejas. Una orquestación es una colección de funciones o pasos, llamados *acciones* en Logic Apps, que se ejecutan para realizar una tarea compleja. Por ejemplo, para procesar un lote de pedidos podría ejecutar varias instancias de una función en paralelo, esperar a que todas las instancias terminen y, a continuación, ejecutar una función que calcula un resultado sobre el agregado.

En Azure Functions, las orquestaciones se desarrollan escribiendo código y utilizando la [extensión de funciones duraderas](durable-functions-overview.md) (en versión preliminar). En Logic Apps, las orquestaciones se crean mediante una GUI o editando archivos de configuración.

Puede mezclar y combinar servicios cuando crea una orquestación, puede llamar a las funciones desde las aplicaciones lógicas y llamar a las aplicaciones lógicas desde las funciones. Elija cómo crear cada orquestación según las funcionalidades de los servicios o sus preferencias personales. En la tabla siguiente se enumeran algunas de las diferencias principales entre estos servicios:
 
|  | Funciones duraderas | Logic Apps |
| --- | --- | --- |
| Desarrollo | Orientado a código (imperativo) | Orientado al diseñador (declarativo) |
| Conectividad | [Con una docena de tipos de enlaces integrados](functions-triggers-bindings.md#supported-bindings), puede escribir código para los enlaces personalizados | [Gran colección de conectores](../connectors/apis-list.md), [Enterprise Integration Pack para escenarios B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [creación de conectores personalizados](../logic-apps/custom-connector-overview.md) |
| Acciones | Cada actividad es una función de Azure; puede escribir código para las funciones de actividad |[Gran colección de acciones listas para usar](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| Supervisión | [Azure Application Insights](../application-insights/app-insights-overview.md) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Operations Management Suite](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md), [Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| Administración | [API REST](durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [API REST](https://docs.microsoft.com/en-us/rest/api/logic/), [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.logicapp/?view=azurermps-5.6.0), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| Contexto de ejecución | Se puede ejecutar [localmente](functions-runtime-overview.md) o en la nube. | Solo se ejecuta en la nube.|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Comparativa entre Functions y WebJobs

Al igual que Azure Functions, Azure App Service WebJobs con el SDK de WebJobs es un servicio de integración de tipo *código primero* que está diseñado para desarrolladores. Ambos se basan en [Azure App Service](../app-service/app-service-web-overview.md) y admiten características como la [integración del control de código fuente](../app-service/app-service-continuous-deployment.md), la [autenticación](../app-service/app-service-authentication-overview.md) y la [supervisión con integración de Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs y el SDK de WebJobs

La característica *WebJobs* de App Service permite ejecutar un script o código en el contexto de una aplicación web de App Service. El *SDK de WebJobs* es un marco diseñado para WebJobs que simplifica el código que se escribe para responder a eventos en los servicios de Azure. Por ejemplo, puede responder a la creación de un blob de imágenes en Azure Storage mediante la creación de una imagen en miniatura. El SDK de WebJobs se ejecuta como una aplicación de consola .NET que se puede implementar en un WebJob. 

Tanto WebJobs como el SDK de WebJobs funcionan mejor conjuntamente, pero WebJobs se puede usar sin el SDK de WebJobs, y viceversa. Un WebJob puede ejecutar cualquier programa o script que se ejecute en el espacio aislado de App Service. Una aplicación de consola del SDK de WebJobs se puede ejecutar en cualquier lugar en el que se puedan ejecutar aplicaciones de consola, como los servidores locales.

### <a name="comparison-table"></a>Tabla de comparación

Azure Functions se basa en el SDK de WebJobs, por lo que comparte muchos desencadenadores de eventos y conexiones con otros servicios de Azure. Estos son algunos de los factores que se deben tener en cuenta al elegir entre Azure Functions y WebJobs con el SDK de WebJobs:

|  | Functions | WebJobs con el SDK de WebJobs |
| --- | --- | --- |
|[Modelo de aplicaciones sin servidor](https://azure.microsoft.com/overview/serverless-computing/) con [escalado automático](functions-scale.md#how-the-consumption-plan-works)|✔||
|[Desarrollo y pruebas en el explorador](functions-create-first-azure-function.md) |✔||
|[Precio de pago por uso](functions-scale.md#consumption-plan)|✔||
|[Integración con Logic Apps](functions-twitter-email.md)|✔||
| Desencadenar eventos |[Temporizador](functions-bindings-timer.md)<br>[Blobs y colas de Azure Storage](functions-bindings-storage-blob.md)<br>[Colas y temas de Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Temporizador](functions-bindings-timer.md)<br>[Blobs y colas de Azure Storage](functions-bindings-storage-blob.md)<br>[Colas y temas de Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Sistema de archivos](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Idiomas admitidos  |C#<br>F#<br>JavaScript<br>Java (versión preliminar) |C#<sup>1</sup>|
|Administradores de paquetes|NPM y NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs (sin SDK de WebJobs) admite C#, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python, etc. Esta no es una lista completa; un WebJob puede ejecutar cualquier programa o script que se pueda ejecutar en el espacio aislado de App Service.

<sup>2</sup> WebJobs (sin el SDK de WebJobs) admite NPM y NuGet.

### <a name="summary"></a>Resumen

Azure Functions ofrece mayor productividad del desarrollador, más opciones del lenguaje de programación, más opciones del entorno de desarrollo, más opciones de integración de servicios de Azure y más opciones de precios. En la mayoría de los casos, es la mejor opción.

Estos son dos de los casos en los que WebJobs puede ser la mejor opción:

* Es preciso tener más control sobre el código que realiza escuchas de eventos, el objeto `JobHost`. Functions ofrece un número limitado de formas de personalizar el comportamiento de `JobHost` en el archivo [host.json](functions-host-json.md). A veces es preciso hacer cosas que no se pueden especificar mediante una cadena de un archivo JSON. Por ejemplo, el SDK de WebJobs es el único que le permite configurar una directiva de reintentos personalizada para Azure Storage.
* Tiene una aplicación de App Service en la que desea ejecutar fragmentos de código y desea administrarlos conjuntamente en el mismo entorno de DevOps.

Para otros escenarios en los que desee ejecutar fragmentos de código para integrar Azure o servicios de terceros, elija Azure Functions, en lugar de WebJobs con el SDK de WebJobs.

<a name="together"></a>

## <a name="flow-logic-apps-functions-and-webjobs-together"></a>Flow, Logic Apps, Functions y WebJobs conjuntamente

No tiene que elegir solo uno de estos servicios; entre ellos se integran tan bien como con los servicios externos.

Un flujo puede llamar a una aplicación lógica. Una aplicación de lógica puede llamar a una función y una función puede llamar a una aplicación lógica. Consulte, por ejemplo, [Creación de una función que se integre con Azure Logic Apps](functions-twitter-email.md).

La integración entre Flow, Logic Apps y Functions sigue mejorando con el tiempo. Puede crear algo en un servicio y usarlo en los otros.

## <a name="next-steps"></a>Pasos siguientes

Para empezar, cree su primer flujo, aplicación lógica o aplicación de función. Haga clic en cualquiera de los siguientes vínculos:

* [Get started with Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/) (Introducción a Microsoft Flow)
* [Creación de una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Creación de su primera función de Azure](functions-create-first-azure-function.md)

O bien, obtenga más información sobre estos servicios de integración con los siguientes vínculos:

* [Leveraging Azure Functions &amp; Azure App Service for integration scenarios](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Aprovechamiento de Azure Functions y Azure App Service para escenarios de integración) por Christopher Anderson
* [Integrations Made Simple](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Webcast en directo sobre Logic Apps](http://aka.ms/logicappslive)
* [Preguntas frecuentes sobre Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)
