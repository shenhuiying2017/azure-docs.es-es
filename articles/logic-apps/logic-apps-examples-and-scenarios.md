---
title: 'Ejemplos y escenarios comunes: Azure Logic Apps | Microsoft Docs'
description: "Más información sobre las aplicaciones lógicas con ejemplos, escenarios y tutoriales"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/9/2017
ms.author: LADocs; jehollan
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 50df1e3db239a6aa34ac91bfbd582625c5b0041b
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---
# <a name="examples-and-common-scenarios-for-azure-logic-apps"></a>Ejemplos y escenarios comunes de Azure Logic Apps

En este artículo se muestran ejemplos y escenarios comunes para que obtenga más información sobre los distintos patrones y funcionalidades de Azure Logic Apps.

## <a name="key-scenarios-for-logic-apps"></a>Escenarios clave de aplicaciones lógicas

Azure Logic Apps ofrece orquestación resistente e integración para los distintos servicios. El servicio de Logic Apps es "sin servidor", por lo que no tiene que preocuparse sobre la escala o las instancias: solo tiene que definir el flujo de trabajo (el desencadenador y las acciones). La plataforma subyacente controla la escala, la disponibilidad y el rendimiento. Cualquier escenario en que necesite coordinar varias acciones, especialmente entre varios sistemas, es un excelente caso de uso para Azure Logic Apps. A continuación puede ver algunos patrones y ejemplos.

## <a name="respond-to-triggers-and-extend-actions"></a>Respuesta a desencadenadores y extensión de acciones

Cada aplicación lógica comienza con un desencadenador. Por ejemplo, el flujo de trabajo puede comenzar con un evento de programación, una invocación manual o un evento proveniente de un sistema externo, como el desencadenador "cuando se agrega un archivo a un servidor FTP". Azure Logic Apps admite actualmente más de 100 conectores listos para usar, que van desde SAP local hasta Microsoft Cognitive Services. En el caso de los sistemas y servicios que podrían no tener conectores publicados, también puede extender las aplicaciones lógicas.

* [Creación de desencadenadores o acciones personalizadas](../logic-apps/logic-apps-create-api-app.md)
* [Configuración de acciones de ejecución prolongada para ejecuciones de flujo de trabajo](../logic-apps/logic-apps-create-api-app.md)
* [Respuesta a eventos y acciones externos con webhooks](../logic-apps/logic-apps-create-api-app.md)
* [Llamar, desencadenar o anidar flujos de trabajo con respuestas sincrónicas a las solicitudes HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Tutorial: Respond to Twilio SMS webhooks and send a text response](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot) (Tutorial: Responder a webhooks de SMS de Twilio y enviar una respuesta de texto)
* [Tutorial: Build an AI-powered social dashboard in minutes with Logic Apps and Power BI](http://aka.ms/logicappsdemo) (Tutorial: Crear un panel social con tecnología de AI en minutos con Logic Apps y Power BI)

## <a name="error-handling-logging-and-control-flow-capabilities"></a>Funcionalidades de flujo de control, registros y control de errores

Las aplicaciones lógicas incluyen amplias funciones de flujo de control avanzado, como condiciones, modificadores, bucles y ámbitos. Para garantizar que las soluciones sean resistentes, también puede implementar el control de errores y excepciones en los flujos de trabajo. Para los registros de notificación y diagnóstico del estado de ejecución del flujo de trabajo, Azure Logic Apps también ofrece supervisión y alertas.

* [Uso de acciones distintas con instrucciones switch](../logic-apps/logic-apps-switch-case.md)
* [Proceso de elementos en matrices y colecciones con bucles y lotes en aplicaciones lógicas](../logic-apps/logic-apps-loops-and-scopes.md)
* [Creación de control de errores y excepciones en un flujo de trabajo](../logic-apps/logic-apps-exception-handling.md)
* [Supervisar el estado, configurar el registro de diagnósticos y activar alertas para Azure Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Turn on monitoring and diagnostics logging when creating logic apps](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md) (Activación de la supervisión y el registro de diagnóstico al crear aplicaciones lógicas)
* [Caso de uso: Cómo una empresa de atención sanitaria usa el control de excepciones de aplicaciones lógicas para los flujos de trabajo de HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="deploy-and-manage-logic-apps"></a>Implementación y administración de aplicaciones lógicas

Puede desarrollar e implementar aplicaciones lógicas completamente con Visual Studio, Visual Studio Team Services o cualquier otra herramienta de compilación automatizada o de control de código fuente. Con el fin de admitir la implementación para flujos de trabajos y conexiones dependientes en una plantilla de recursos, las aplicaciones lógicas usan las plantillas de implementación de recursos de Azure. Visual Studio Tools genera automáticamente estas plantillas, lo que puede comprobar en el control de código fuente para el control de versiones.

* [Creación de una plantilla de implementación automatizada](../logic-apps/logic-apps-create-deploy-template.md)
* [Creación e implementación de aplicaciones lógicas en Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md)
* [Supervisión del estado de las aplicaciones lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Tipos de contenido, conversiones y transformaciones dentro de una ejecución

Puede obtener acceso a distintos tipos de contenido, convertirlos y transformarlos mediante las diversas funciones que existen en el [lenguaje de definición de flujo de trabajo de](http://aka.ms/logicappsdocs) Azure Logic Apps. Por ejemplo, puede convertir entre una cadena, JSON y XML con las expresiones de flujo de trabajo `@json()` y `@xml()`. El motor de Logic Apps conserva los tipos de contenido para admitir la transferencia de contenido sin pérdida de información entre los servicios.

* [Administración de los tipos de contenido no JSON](../logic-apps/logic-apps-content-type.md), como `application/xml`, `application/octet-stream` y `multipart/formdata`
* [Funcionamiento de las expresiones de flujo de trabajo en las aplicaciones lógicas](../logic-apps/logic-apps-author-definitions.md)
* [Referencia: Lenguaje de definición de flujo de trabajo de Azure Logic Apps](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Otras integraciones y funcionalidades

Las aplicaciones lógicas también ofrecen integración con muchos servicios, como Azure Functions, Azure API Management, Azure App Services y puntos de conexión HTTP personalizados, por ejemplo, REST y SOAP.

* [Creación de un panel de redes sociales en tiempo real y sin servidor con Azure](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Llamada a Azure Functions desde aplicaciones lógicas](../logic-apps/logic-apps-azure-functions.md)
* [Escenario: Desencadenamiento de aplicaciones lógicas con Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Blog: Call SOAP endpoints from logic apps](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/) (Blog: Llamada a puntos de conexión SAP desde aplicaciones lógicas)

## <a name="end-to-end-scenarios"></a>Escenarios de un extremo a otro

* [Notas del producto: Administración de un caso completo de integración empresarial con servicios de Azure, como Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="next-steps"></a>Pasos siguientes

- [Control de errores y excepciones en aplicaciones lógicas](../logic-apps/logic-apps-exception-handling.md)
- [Creación de definiciones de flujo de trabajo con el lenguaje de definición de flujo de trabajo](../logic-apps/logic-apps-author-definitions.md)
- [Envío de comentarios, preguntas o sugerencias sobre cómo podemos mejorar Azure Logic Apps](https://feedback.azure.com/forums/287593-logic-apps)
