---
title: "Implementación y llamada de API web y API de REST desde Azure Logic Apps | Microsoft Docs"
description: "Implemente y llame a sus API web y API de REST para flujos de trabajo de integración de sistemas en Azure Logic Apps."
keywords: API web, API de REST, conectores, flujos de trabajo, integraciones de sistemas, autenticar
services: logic-apps
author: stepsic-microsoft-com
manager: anneta
editor: 
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: LADocs; stepsic
ms.openlocfilehash: 3df59ec172b037daaeed9e3eb69ffb990d70d8a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-and-call-custom-apis-from-logic-app-workflows"></a>Implementación y llamada de API personalizadas desde flujos de trabajo de aplicación lógica

Después de [crear API personalizadas](./logic-apps-create-api-app.md) para usarlas en flujos de trabajo de aplicaciones lógicas, debe implementar las API para poder llamarlas. Puede implementar las API como [aplicaciones web](../app-service/app-service-web-overview.md), pero considere la posibilidad de implementarlas como [aplicaciones de API](../app-service/app-service-web-tutorial-rest-api.md), lo que puede facilitarle su trabajo al compilar, hospedar y consumir API en la nube y en el entorno local. No tiene que cambiar ningún código en las API; basta con implementar el código en una aplicación de API. Puede hospedar las API en [Azure App Service](../app-service/app-service-web-overview.md), una oferta de plataforma como servicio (PaaS) que proporciona hospedaje API fácil y altamente escalable.

Aunque se puede llamar a cualquier API desde una aplicación lógica, para lograr una experiencia óptima, agregue [metadatos de OpenAPI (anteriormente Swagger)](http://swagger.io/specification/) que describan las operaciones y los parámetros de la API. Este archivo de OpenAPI ayuda a que su API se integre con más facilidad y funcione mejor con aplicaciones lógicas.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Implementación de la API como aplicación web o aplicación de API

Para poder llamar a su API personalizada desde una aplicación lógica, impleméntela como aplicación web o aplicación de API en Azure App Service. Además, para mejorar la legibilidad del archivo de OpenAPI en el Diseñador de Logic Apps, establezca las propiedades de definición de la API y active [Uso compartido de recursos entre orígenes (CORS)](../app-service/app-service-web-overview.md) para su aplicación web o de API.

1. En [Azure Portal](https://portal.azure.com), seleccione la aplicación web o de API.

2. En el menú que se abre, en **API**, elija **Definición de la API**. Establezca **Ubicación de la definición de la API** en la dirección URL del archivo swagger.json de OpenAPI.

   Por lo general, la dirección URL aparece en este formato: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Vinculación al archivo de OpenAPI para la API personalizada](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. En **API**, elija **CORS**. Establezca la directiva CORS para **Orígenes permitidos** en  **"*"** (permitir todos).

   Esta configuración permite solicitudes del Diseñador de aplicación lógica.

   ![Permitir solicitudes del Diseñador de aplicación lógica a la API personalizada](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Para obtener más información, consulte [Creación de una API de RESTful de Node.js](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Llamada a la API personalizada desde flujos de trabajo de aplicación lógica

Después de configurar las propiedades de definición de la API y de CORS, los desencadenadores y las acciones de la API personalizada deberían estar disponibles para incluirlos en el flujo de trabajo de la aplicación lógica. 

*  Para ver los sitios web que tienen direcciones URL de OpenAPI, puede examinar los sitios web de su suscripción en el Diseñador de Logic Apps.

*  Para ver las acciones y entradas disponibles señalando a un documento de OpenAPI, use la [acción HTTP + Swagger](../connectors/connectors-native-http-swagger.md).

*  Para llamar a cualquier API, incluso aquellas que no tengan ni expongan un documento de OpenAPI, siempre puede crear una solicitud con la [acción HTTP](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Pasos siguientes

* [Información general de los conectores personalizados](../logic-apps/custom-connector-overview.md)