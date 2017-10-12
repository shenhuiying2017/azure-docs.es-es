---
title: "Conexión de aplicaciones e integración de datos con flujos de trabajo: Azure Logic Apps | Microsoft Docs"
description: "Creación de flujos de trabajo y automatización de procesos mediante la conexión de aplicaciones y la integración de datos en Azure Logic Apps."
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 07765c05-72a6-4169-a8ab-f6420bfbaf07
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/23/2017
ms.author: klam
ms.openlocfilehash: 59d35852d6c703f3c96089a8bf426b57660441a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-logic-apps"></a>¿Qué es Logic Apps?
Logic Apps es una manera de simplificar e implementar integraciones escalables y flujos de trabajo en la nube. Proporciona un diseñador visual para modelar y automatizar el proceso en una serie de pasos denominada flujo de trabajo.  Hay [muchos conectores](../connectors/apis-list.md) en la nube y locales para la integración rápida en servicios y protocolos.  Una aplicación lógica comienza con un desencadenador (como "Cuando se agrega una cuenta a Dynamics CRM") y después la activación puede comenzar muchas combinaciones de acciones, conversiones y lógicas de condiciones.

Algunas ventajas de Logic Apps:  

* Ahorro de tiempo al diseñar procesos complejos gracias a las herramientas de diseño que se entienden fácilmente
* Implementación inigualable de patrones y flujos de trabajo, que de lo contrario, serían difíciles de implementar en el código
* Introducción rápida desde plantillas
* Personalización de aplicaciones lógicas con API, código y acciones propios
* Conexión y sincronización de sistemas dispares locales y en la nube
* Generación del servidor de BizTalk, API Management, Azure Functions y Azure Service Bus compatible con integración de primera clase

Logic Apps es una iPaaS (plataforma de integración como servicio) totalmente administrada que permite a los desarrolladores despreocuparse del hospedaje, la escalabilidad, la disponibilidad y la administración.  Logic Apps se escala automáticamente según las necesidades.

![Diseñador de aplicación de flujo](media/logic-apps-what-are-logic-apps/LogicAppCapture2.png)

Como se ha mencionado, con Logic Apps puede automatizar procesos empresariales. Estos son algunos ejemplos:  

* Desplazamiento de archivos cargados en un servidor FTP a Azure Storage
* Procesamiento y redirección de pedidos de sistemas locales y en la nube
* Supervisión de todos los tweets sobre un tema específico, análisis de la opinión, y creación de alertas y tareas para los elementos que necesitan seguimiento.

Todos estos tipos de escenarios se pueden configurar desde el diseñador visual y sin escribir ni una sola línea de código. Comience a [crear una aplicación lógica ahora][create].  Una vez escritas, las aplicaciones lógicas [se implementan rápidamente y se vuelven a configurar](../logic-apps/logic-apps-create-deploy-template.md) en varios entornos y regiones.

## <a name="why-logic-apps"></a>¿Por qué Logic Apps?
Logic Apps ofrece velocidad y escalabilidad en el espacio de integración empresarial.  La facilidad de uso del diseñador, la variedad de desencadenadores y acciones, y las potentes herramientas de administración facilitan como nunca la centralización de las API.  Las empresas avanzan hacia la digitalización y Logic Apps ayuda a conectar sistemas antiguos con los nuevos.

Además, con nuestra [cuenta de integración Enterprise][biztalk], puede realizar el escalado a escenarios de integración consolidados con la eficacia de una [mensajería XML][xml], la [administración de socios comerciales][tpm], etc.

* **Herramientas de diseño fáciles de usar**: las aplicaciones lógicas pueden diseñarse de principio a fin en el explorador o con las herramientas de Visual Studio. Se empieza con un desencadenador: desde una simple programación hasta la generación de una publicación de GitHub. A continuación, se coordina cualquier número de acciones mediante la sofisticada galería de conectores.
* **Conexión sencilla de API** : incluso las tareas de composición que se describen fácilmente son difíciles de implementar en el código. Logic Apps facilita la conexión de sistemas dispares. ¿Desea conectar su solución de marketing en la nube a su sistema de facturación local? ¿Quiere centralizar la mensajería a través de las API y los sistemas con un bus de servicio empresarial? Logic Apps es la forma más rápida y más confiable para ofrecer soluciones a estos problemas.
* **Empezar a trabajar rápidamente gracias a las plantillas**: para ayudarle a empezar, hemos dispuesto una [galería de plantillas][templates] que permiten crear rápidamente varias soluciones comunes. Desde soluciones B2B avanzadas hasta una conectividad de SaaS simple e incluso algunas demostraciones: la galería es la introducción más rápida a la eficacia de Logic Apps.
* **Extensibilidad incorporada** : ¿no ve el conector que necesita? Logic Apps está diseñado para funcionar con sus propias API y código; puede crear fácilmente su propia aplicación de API para usarla como conector personalizado o llamar a una [función de Azure](https://functions.azure.com) para ejecutar fragmentos de código según los necesite. 
* **Eficacia de integración real** : empiece por algo sencillo y crezca a medida que sea necesario. Logic Apps puede aprovechar con facilidad la eficacia de BizTalk, la solución de integración líder del sector de Microsoft para permitir a los profesionales de integración crear las soluciones que necesitan. Más información sobre [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).

## <a name="logic-app-concepts"></a>Conceptos de aplicaciones lógicas
Las siguientes son algunas de las principales partes que componen la experiencia de Logic Apps. 

* **Flujo de trabajo**: Logic Apps ofrece una forma gráfica de modelar los procesos de negocio como una serie de pasos o un flujo de trabajo.
* **Conectores administrados**: Logic Apps necesitan acceder a datos y servicios. Los conectores administrados se crean específicamente para ayudarle cuando se conecte y trabaje con datos. Consulte la lista de conectores disponibles ahora en [conectores administrados][managedapis].
* **Desencadenadores** : algunos conectores administrados también pueden hacer de desencadenador. Un desencadenador inicia una nueva instancia de un flujo de trabajo de acuerdo con un evento específico, como la llegada de un correo electrónico o un cambio en la cuenta de almacenamiento de Azure.
* **Acciones** : cada paso después de la llamada a la acción de un desencadenador en un flujo de trabajo. Cada acción normalmente se asigna a una operación en el conector administrado o en las aplicaciones de API personalizadas.
* **Enterprise Integration Pack**: para escenarios más avanzados de integración, Logic Apps incluye funcionalidades de BizTalk. BizTalk es la plataforma de integración líder del sector de Microsoft. Los conectores de Enterprise Integration Pack permiten incluir fácilmente la validación, la transformación y mucho más en sus flujos de trabajo de aplicaciones lógicas.

## <a name="getting-started"></a>Introducción
* Para empezar a trabajar con Logic Apps, siga el tutorial para la [creación de aplicaciones lógicas][create].  
* [Ejemplos de aplicaciones lógicas y escenarios comunes](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Con las aplicaciones lógicas puede automatizar procesos empresariales.](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Obtenga información acerca de cómo integrar sus sistemas con Aplicaciones lógicas](http://channel9.msdn.com/Events/Build/2016/P462)

[biztalk]: logic-apps-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-web-overview.md
[create]: logic-apps-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: logic-apps-enterprise-integration-accounts.md
[xml]: logic-apps-enterprise-integration-b2b.md
[templates]: logic-apps-use-logic-app-templates.md
