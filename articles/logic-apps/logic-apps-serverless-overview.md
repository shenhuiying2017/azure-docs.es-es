---
title: "Introducción a Azure sin servidor | Microsoft Docs"
description: Cree eficaces soluciones en la nube sin tener que pensar en la infraestructura.
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 6803e22a78e27c15ff4fec301cd5bdd55aacd3e3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="overview-of-azure-serverless-with-functions-and-logic-apps"></a>Introducción a Azure sin servidor con Functions y Logic Apps

Las aplicaciones sin servidor proporcionan las ventajas de aumento en la velocidad de desarrollo, reducción del código necesario y simplicidad con la escala.  En este artículo se analizan los diferentes atributos de las soluciones sin servidor y las ofertas de Azure sin servidor.

## <a name="what-is-serverless"></a>¿Qué significa sin servidor?

Sin servidor no significa que no haya ningún servidor, sino que el desarrollador no tiene que preocuparse por los servidores.  Gran parte del desarrollo tradicional de aplicaciones es dar respuesta a preguntas en torno a las soluciones de escalado, hospedaje y supervisión para satisfacer las demandas de la aplicación.  Las soluciones sin servidor se ocupan de estas preguntas como parte de la solución.  Además, las aplicaciones sin servidor se facturan según un plan de consumo.  Si la aplicación no se usa nunca, nunca se incurrirá en ningún gasto.  Estas características permiten a los desarrolladores centrarse únicamente en la lógica de negocios de la solución.

Los principales servicios de Azure en torno al concepto sin servidor son [Azure Functions](https://azure.microsoft.com/services/functions/) y [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).  Ambas solucione siguen los principios anteriormente mencionados y permiten a los desarrolladores crear sólidas aplicaciones de nube con muy poco código.

## <a name="what-are-azure-functions"></a>¿Qué es Azure Functions?

Funciones de Azure es una solución para ejecutar fácilmente pequeños fragmentos de código, o "funciones", en la nube. Simplemente, puede escribir el código que necesita para el problema en cuestión, sin preocuparse de toda la aplicación o la infraestructura para ejecutarlo. Functions puede hacer que el desarrollo sea aún más productivo y, además, le permite utilizar el lenguaje de desarrollo que prefiera, como C#, F#, Node.js, Python o PHP. Pague solo por el tiempo que se ejecuta el código y que se escala Azure según sea necesario.

Si desea comenzar de inmediato y empezar a trabajar con Funciones de Azure, comience con el artículo [Creación de su primera función de Azure](../azure-functions/functions-create-first-azure-function.md). Si busca información más técnica acerca de las Funciones, consulte [Referencia para desarrolladores de Funciones de Azure](../azure-functions/functions-reference.md).

## <a name="what-are-azure-logic-apps"></a>¿Qué es Azure Logic Apps?

Azure Logic Apps ofrece una manera de simplificar e implementar flujos de trabajo e integraciones escalables en la nube. Proporciona un diseñador visual para modelar y automatizar el proceso en una serie de pasos denominada flujo de trabajo.  Existen [muchos conectores](../connectors/apis-list.md) en servicio locales y en la nube para conectar rápidamente una aplicación sin servidor a otras API.  Una aplicación lógica comienza con un desencadenador (como cuando se agrega una cuenta a Dynamics CRM) y la activación puede empezar muchas combinaciones, acciones, conversiones y lógicas de condiciones.  Logic Apps es una buena opción al orquestar diferentes instancias de Azure Functions en un proceso, en especial cuando el proceso requiere la interacción con un sistema o una API externos.

Para empezar a trabajar con Logic Apps, comience por la [creación de su primera aplicación lógica](logic-apps-create-a-logic-app.md).  Si busca información más técnica acerca de Logic Apps, consulte la [referencia para desarrolladores](logic-apps-workflow-actions-triggers.md).

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>¿Cómo puedo crear e implementar aplicaciones sin servidor en Azure?

Azure proporciona un conjunto abundante de herramientas para el desarrollo, la implementación y la administración de aplicaciones sin servidor.  Las aplicaciones se pueden crear directamente en el portal de Azure o con [herramientas de Visual Studio](logic-apps-serverless-get-started-vs.md).  Una vez que se ha desarrollado una aplicación, se puede [implementar al instante](logic-apps-create-deploy-template.md).  Azure también proporciona supervisión para aplicaciones sin servidor.  Para acceder a esta supervisión se puede usar el portal de Azure, la API o el SDK, o también se pueden utilizar herramientas integradas con OMS y Application Insights.

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a la creación de una aplicación sin servidor en Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Creación de un panel de Customer Insights con una aplicación sin servidor](logic-apps-scenario-social-serverless.md)
* [Creación de una plantilla de implementación para una aplicación lógica](logic-apps-create-deploy-template.md)