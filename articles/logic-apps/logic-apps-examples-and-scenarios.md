---
title: 'Ejemplos y escenarios comunes: Azure Logic Apps | Microsoft Docs'
description: "Más información acerca de Logic Apps, con ejemplos, escenarios, tutoriales y guías detalladas"
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
ms.workload: logic-apps
ms.date: 09/13/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: b88d0c1ccb7a729c95299bcdc3cba5fd73fcdeac
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Escenarios comunes, ejemplos, tutoriales y guías detalladas de Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) le ayuda a organizar e integrar servicios diferentes, ya que proporciona [más de 100 conectores que se pueden usar en cualquier momento](../connectors/apis-list.md), que van de SQL Server o SAP locales a Microsoft Cognitive Services. El servicio Logic Apps es "sin servidor", por lo que no tiene que preocuparse de escalados o instancias. Lo único que debe hacer es definir el flujo de trabajo con un desencadenador y las acciones que realiza el flujo de trabajo. La plataforma subyacente controla la escala, la disponibilidad y el rendimiento. Logic Apps es especialmente útil para aquellos casos de uso y escenarios en los que hay que coordinar varias acciones en varios sistemas.

En este artículo se muestran ejemplos y escenarios comunes para que obtenga más información sobre los distintos patrones y funcionalidades que [Azure Logic Apps](../logic-apps/logic-apps-overview.md) admite.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Puntos de partida más usados en flujos de trabajo de aplicaciones lógicas

Todas las aplicaciones lógica comienzan con un [*desencadenador*](../logic-apps/logic-apps-overview.md#logic-app-concepts), y solo un desencadenador, que inicia el flujo de trabajo de la aplicación lógica y pasa los datos como parte de dicho desencadenador. Algunos de los conectores proporcionan desencadenadores, que pueden ser de estos tipos:

* *Desencadenadores de sondeo*: comprueban de forma regular si un punto de conexión de servicio tiene datos nuevos. Cuando los haya, el desencadenador crea y ejecuta una nueva instancia de flujo de trabajo con dichos datos como entrada.

* *Desencadenadores de inserción*: escucha datos en un punto de conexión de servicio y espera hasta que se produce un evento específico. En ese momento, el desencadenador se activa inmediatamente y crear y ejecutar una nueva instancia de flujo de trabajo que utiliza como entrada todos los datos disponibles.

Estos son algunos ejemplos de los desencadenadores más usados:

* Sondeo: 

  * El [desencadenador **Programación - Periodicidad**](../connectors/connectors-native-recurrence.md) permite establecer no solo la fecha y hora de inicio sino también la periodicidad con que se activa la aplicación lógica. 
  Por ejemplo, puede seleccionar los días de la semana y las horas del día en que se activará la aplicación lógica.

  * El desencadenador "Cuando llega un correo electrónico nuevo" permite que la aplicación lógica compruebe si hay mensajes de correo electrónico nuevos en todos los proveedores de correo electrónico compatibles con Logic Apps, por ejemplo, [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [ Outlook.com](https://docs.microsoft.com/connectors/outlook/), etc.

  * El [desencadenador **HTTP**](../connectors/connectors-native-http.md) permite que la aplicación lógica compruebe un punto de conexión de servicio especificado, para lo que se comunica a través de HTTP.
  
* Inserción:

  * El [desencadenador **Solicitud/Respuesta - Solicitud**](../connectors/connectors-native-reqres.md) permite a la aplicación lógica recibir solicitudes HTTP y responder en tiempo real a los eventos de alguna manera.

  * El [desencadenador **Webhook de HTTP**](../connectors/connectors-native-webhook.md) realiza la suscripción a un punto de conexión de servicio mediante el registro de una *dirección URL de devolución de llamada* en dicho servicio. 
  De este modo, el servicio puede simplemente notificar el desencadenador cuando se produce el evento especificado, con el fin de que no sea preciso que este sondee el servicio.

Tras recibir una notificación acerca de datos nuevos o un evento, el desencadenador se activa, crea una nueva instancia de flujo de trabajo de aplicación lógica y ejecuta las acciones en el flujo de trabajo. Para acceder a los datos del desencadenador se usa el flujo de trabajo. Por ejemplo, el desencadenador "Cuando se publica un tweet nuevo" pasa el contenido del tweet a la aplicación lógica que se ejecute. 

## <a name="respond-to-triggers-and-extend-actions"></a>Respuesta a desencadenadores y extensión de acciones

En el caso de los sistemas y servicios que podrían no tener conectores publicados, también puede extender las aplicaciones lógicas.

* [Creación de desencadenadores o acciones personalizadas](../logic-apps/logic-apps-create-api-app.md)
* [Configuración de acciones de ejecución prolongada para ejecuciones de flujo de trabajo](../logic-apps/logic-apps-create-api-app.md)
* [Respuesta a eventos y acciones externos con webhooks](../logic-apps/logic-apps-create-api-app.md)
* [Llamar, desencadenar o anidar flujos de trabajo con respuestas sincrónicas a las solicitudes HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Tutorial: Build an AI-powered social dashboard in minutes with Logic Apps and Power BI](http://aka.ms/logicappsdemo) (Tutorial: Crear un panel social con tecnología de AI en minutos con Logic Apps y Power BI)
* [Tutorial: Respond to Twilio SMS webhooks and send a text response](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot) (Tutorial: Responder a webhooks de SMS de Twilio y enviar una respuesta de texto)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Funcionalidades de flujo de control, registro y control de errores

Las aplicaciones lógicas incluyen amplias funciones de flujo de control avanzado, como condiciones, modificadores, bucles y ámbitos. Para garantizar que las soluciones sean resistentes, también puede implementar el control de errores y excepciones en los flujos de trabajo. Para los registros de notificación y diagnóstico del estado de ejecución del flujo de trabajo, Azure Logic Apps también ofrece supervisión y alertas.

* [Proceso de elementos en matrices y colecciones con bucles y lotes en aplicaciones lógicas](../logic-apps/logic-apps-loops-and-scopes.md)
* [Uso de acciones distintas con instrucciones switch](../logic-apps/logic-apps-switch-case.md)
* [Creación de control de errores y excepciones en un flujo de trabajo](../logic-apps/logic-apps-exception-handling.md)
* [Caso de uso: Cómo una empresa de atención sanitaria usa el control de excepciones de aplicaciones lógicas para los flujos de trabajo de HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Supervisar el estado, configurar el registro de diagnósticos y activar alertas para Azure Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Turn on monitoring and diagnostics logging when creating logic apps](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md) (Activación de la supervisión y el registro de diagnóstico al crear aplicaciones lógicas)

## <a name="deploy-and-manage-logic-apps"></a>Implementación y administración de aplicaciones lógicas

Puede desarrollar e implementar aplicaciones lógicas completamente con Visual Studio, Visual Studio Team Services o cualquier otra herramienta de compilación automatizada o de control de código fuente. Con el fin de admitir la implementación para flujos de trabajos y conexiones dependientes en una plantilla de recursos, las aplicaciones lógicas usan las plantillas de implementación de recursos de Azure. Visual Studio Tools genera automáticamente estas plantillas, lo que puede comprobar en el control de código fuente para el control de versiones.

* [Creación e implementación de aplicaciones lógicas en Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md)
* [Supervisar el estado, configurar el registro de diagnósticos y activar alertas para Azure Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Creación de una plantilla de implementación automatizada](../logic-apps/logic-apps-create-deploy-template.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Tipos de contenido, conversiones y transformaciones dentro de una ejecución

Puede obtener acceso a distintos tipos de contenido, convertirlos y transformarlos mediante las diversas funciones que existen en el [lenguaje de definición de flujo de trabajo de](http://aka.ms/logicappsdocs) Azure Logic Apps. Por ejemplo, puede convertir entre una cadena, JSON y XML con las expresiones de flujo de trabajo `@json()` y `@xml()`. El motor de Logic Apps conserva los tipos de contenido para admitir la transferencia de contenido sin pérdida de información entre los servicios.

* [Funcionamiento de las expresiones de flujo de trabajo en las aplicaciones lógicas](../logic-apps/logic-apps-author-definitions.md)
* [Administración de los tipos de contenido no JSON](../logic-apps/logic-apps-content-type.md), como `application/xml`, `application/octet-stream` y `multipart/formdata`
* [Referencia: Lenguaje de definición de flujo de trabajo de Azure Logic Apps](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Otras integraciones y funcionalidades

Las aplicaciones lógicas también ofrecen integración con muchos servicios, como Azure Functions, Azure API Management, Azure App Services y puntos de conexión HTTP personalizados, por ejemplo, REST y SOAP.

* [Creación de un panel de redes sociales en tiempo real y sin servidor con Azure](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Llamada a Azure Functions desde aplicaciones lógicas](../logic-apps/logic-apps-azure-functions.md)
* [Escenario: Desencadenamiento de aplicaciones lógicas con Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Blog: Call SOAP endpoints from logic apps](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/) (Blog: Llamada a puntos de conexión SAP desde aplicaciones lógicas)

## <a name="end-to-end-scenarios"></a>Escenarios de un extremo a otro

* [Notas del producto: Administración de un caso completo de integración empresarial con servicios de Azure, como Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="next-steps"></a>pasos siguientes

* [Creación de definiciones de flujo de trabajo con el lenguaje de definición de flujo de trabajo](../logic-apps/logic-apps-author-definitions.md)
* [Control de errores y excepciones en aplicaciones lógicas](../logic-apps/logic-apps-exception-handling.md)
* [Envío de comentarios, preguntas o sugerencias sobre cómo podemos mejorar Azure Logic Apps](https://feedback.azure.com/forums/287593-logic-apps)