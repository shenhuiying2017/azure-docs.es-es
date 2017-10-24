---
title: "Información general de los conectores personalizados: Azure Logic Apps | Microsoft Docs"
description: "Información general sobre la creación de conectores personalizados para admitir y expandir los escenarios de integración"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 0515b21bc7c7cc737e14fda016606bbea1aab476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="custom-connectors-overview"></a>Información general de los conectores personalizados

Sin necesidad de escribir ningún código, puede crear flujos de trabajo o aplicaciones con [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), [Microsoft Flow](https://flow.microsoft.com) o [Microsoft PowerApps](https://powerapps.microsoft.com). Para ayudarle a integrar los procesos empresariales y de datos, estos servicios ofrecen [más de 100 conectores](../connectors/apis-list.md), no solo para los servicios de Microsoft y productos como Azure y SQL Server, sino también para otros servicios, como GitHub, Salesforce, Twitter, etc. 

A veces, sin embargo, es posible que desee llamar a API, servicios y sistemas que no están disponibles como conectores compilados previamente. A fin de admitir más escenarios diseñados a medida para los negocios y las necesidades de productividad de sus usuarios, puede compilar *conectores personalizados* con sus propios desencadenadores y acciones.
Los conectores personalizados expanden las integraciones, el alcance, la detectabilidad y el uso para el servicio o producto, lo que puede ayudar a aumentar y acelerar la adopción del cliente.

Por ejemplo, este diagrama muestra la interacción entre una API web, un conector de Logic Apps personalizado creado para esa API y una aplicación lógica que funciona con esa API mediante el conector personalizado:

![Descripción conceptual de una API web, un conector personalizado y una aplicación lógica](./media/custom-connector-overview/custom-connector-conceptual.png)

En esta información general se describen las tareas generales detalladas para crear, proteger, registrar y usar sus conectores, y opcionalmente compartirlos o certificarlos:

![Pasos para la creación de un conector personalizado](./media/custom-connector-overview/authoring-steps.png)

## <a name="prerequisites"></a>Requisitos previos

Para abordar el proceso de creación del conector de principio a fin, necesitará estos elementos:

* Una suscripción de Azure. Si no tiene una suscripción, puede [comenzar con una cuenta de Azure gratuita](https://azure.microsoft.com/free/). También puede registrarse para obtener una [suscripción de pago por uso](https://azure.microsoft.com/pricing/purchase-options/).

* API de RESTful con algún tipo de acceso autenticado. Para más información consulte [Creación de conectores personalizados desde API web ](../logic-apps/custom-connector-build-web-api-app-tutorial.md). Para conocer los modelos que se pueden usar para los desencadenadores y las acciones del conector, vea [Creación de API personalizadas como conectores para aplicaciones lógicas](../logic-apps/logic-apps-create-api-app.md).

* Cualquiera de estos elementos:

  * Un [archivo de OpenAPI 2.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md), anteriormente conocido como Swagger
  * Una dirección URL a una definición de OpenAPI
  * Una [colección de Postman](../logic-apps/custom-connector-api-postman-collection.md) para su API 

  Si no tiene ninguno de estos elementos, nosotros le orientaremos.

* Opcional: una imagen que se usará como icono para el conector personalizado

## <a name="1-build-your-restful-api"></a>1. Compilación de la API de RESTful

Técnicamente, un conector es un contenedor alrededor de una API de REST que se basa en una especificación OpenAPI (anteriormente Swagger) y permite que el servicio subyacente se comunique con Logic Apps, Flow o PowerApps. Por ello, en primer lugar, y antes de crear el conector personalizado, necesita una API totalmente operativa. 

Puede usar cualquier lenguaje y plataforma para la API. Para las tecnologías de Microsoft, se recomienda una de estas plataformas:

* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure Web Apps](https://azure.microsoft.com/services/app-service/web/)
* [Azure API Apps](https://azure.microsoft.com/services/app-service/api/)

Por ejemplo, en este tutorial se muestra [cómo crear un conector personalizado a partir de una API web](../logic-apps/custom-connector-build-web-api-app-tutorial.md). Para conocer los modelos que se pueden usar para los desencadenadores y las acciones del conector, vea [Creación de API personalizadas como conectores para aplicaciones lógicas](../logic-apps/logic-apps-create-api-app.md).

## <a name="2-secure-your-api"></a>2. Protección de la API

Puede usar estos estándares de autenticación para las API y los conectores:

   * [OAuth 2.0](https://oauth.net/2/), incluido [Azure Active Directory](https://azure.microsoft.com/develop/identity/) o servicios específicos, por ejemplo, Dropbox, GitHub y SalesForce
   * OAuth 2.0 genérico
   * [Autenticación básica](https://swagger.io/docs/specification/authentication/basic-authentication/)
   * [Clave de API](https://swagger.io/docs/specification/authentication/api-keys/)

Puede establecer la autenticación de Azure Active Directory (Azure AD) para su API en Azure Portal para que no sea necesario implementar la autenticación a través del código. También puede requerir y aplicar la autenticación a través del código de la API. 

Para obtener más información, siga los tutoriales adecuados:

* [Logic Apps: Secure your custom connector](../logic-apps/custom-connector-azure-active-directory-authentication.md) (Logic Apps: Protección del conector personalizado)
* [Uso de Azure Active Directory con un conector personalizado en Microsoft Flow](https://ms.flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/)
* [Uso de Azure Active Directory con un conector personalizado en PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)

## <a name="3-describe-your-api"></a>3. Descripción de la API 

Suponiendo que la API tenga algún tipo de acceso autenticado, es necesario describir la interfaz y las operaciones de la API para que Logic Apps, Flow o PowerApps puedan comunicarse con ella.
Use una de estas definiciones estándar del sector:

* Un [archivo de OpenAPI 2.0](https://swagger.io/). Puede empezar por compilar con un archivo de OpenAPI existente.

  Si no está familiarizado con OpenAPI, visite [Getting started with Swagger](http://swagger.io/getting-started/) (Introducción a Swagger) en el sitio swagger.io.
  Para obtener un ejemplo de un archivo de OpenAPI, consulte la [documentación de la API de Text Analytics](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/export?DocumentFormat=Swagger&ApiName=Azure). 

* Una colección de Postman, que genera automáticamente un archivo de OpenAPI. Si aún no tiene un archivo de OpenAPI y tampoco desea crear uno, tiene la posibilidad de crear fácilmente un conector personalizado mediante una colección de Postman.

  Si no está familiarizado con Postman, [instale la aplicación Postman](https://www.getpostman.com/apps) desde su sitio. Para obtener más información, consulte [Describe custom connectors with Postman](../logic-apps/custom-connector-api-postman-collection.md) (Descripción de conectores personalizados con Postman).

> [!IMPORTANT]
> El tamaño del archivo debe ser inferior a 1 MB.

En segundo plano, Logic Apps, Flow y PowerApps utilizan en último lugar OpenAPI, analizan una colección de Postman y traducen la colección a un archivo de definición de OpenAPI. Aunque las colecciones de OpenAPI 2.0 y Postman utilizan formatos distintos, ambas son documentos independientes del lenguaje y legibles automáticamente que describen las operaciones y los parámetros de la API. Puede generar estos documentos desde distintas herramientas, según el idioma y la plataforma que use la API. También puede crear un archivo de OpenAPI al registrar el conector.

Por ejemplo, puede crear un archivo de OpenAPI o una colección de Postman desde cualquier punto de conexión de la API de REST, lo cual incluye lo siguiente:

* Conectores disponibles públicamente, como [Spotify](https://developer.spotify.com/), [Slack](https://api.slack.com/), [Rackspace](http://docs.rackspace.com/), etc.

* Una API que haya creado e implementado en cualquier proveedor de hospedaje en la nube, como Azure, Heroku o Google Cloud, entre otros.

* Una API de línea de negocio personalizada que esté implementada en la red, pero solo si esa API se expone en la red pública de Internet.

## <a name="4-register-your-connector"></a>4. Registro del conector

El proceso de registro ayuda a Logic Apps, Flow o PowerApps a comprender las características de la API, como la descripción, el requisito de autenticación o las operaciones (incluidos los parámetros y las salidas para cada operación). Cuando se inicia el proceso de registro, puede proporcionar un archivo de OpenAPI o una colección de Postman, que rellena automáticamente los campos de metadatos en el Asistente para registro. Puede editar los valores de estos campos en cualquier momento.

![Descripción de la API](./media/custom-connector-overview/choose-api-definition-file.png)

Para registrar el conector, siga el tutorial adecuado:

* [Logic Apps: Register your connector](../logic-apps/logic-apps-custom-connector-register.md) (Logic Apps: Registro del conector)
* [Flow: Registro del conector personalizado](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [Registrar y usar conectores personalizados en PowerApps](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)

## <a name="5-use-your-connector-in-a-logic-app-flow-or-app"></a>5. Uso del conector en una aplicación lógica, un flujo o una aplicación 

Puede usar el conector de la misma manera que utiliza los conectores administrados por Microsoft. Por ejemplo, en un flujo de trabajo de aplicación lógica, agregue el conector personalizado a fin de crear una conexión a su API, y llame a las operaciones que proporciona la API de la misma manera que llama a las operaciones para conectores administrados por Microsoft.

## <a name="6-share-your-connector"></a>6. Uso compartido del conector 

Puede compartir su conector con los usuarios de su organización de la misma manera que comparte recursos en Logic Apps, Flow o PowerApps. Aunque el uso compartido es opcional, es posible que se encuentre con situaciones en las que desee compartir los conectores con otros usuarios.

Los conectores personalizados registrados pero no certificados funcionan como conectores administrados por Microsoft, pero son visibles y están disponibles *solo* para el autor del conector y los usuarios que tienen el mismo inquilino de Azure Active Directory y la misma suscripción de Azure para aplicaciones lógicas en la región en la que se implementan tales aplicaciones. En el paso siguiente se trata el modo de compartir el conector con usuarios externos; por ejemplo, todos los usuarios de Logic Apps, Flow, y PowerApps.

> [!IMPORTANT]
> Si comparte un conector, otros usuarios podrían empezar a depender de ese conector. 
> ***Al eliminar el conector, se eliminan todas las conexiones con ese conector.***

* [Logic Apps: Share your connector](../logic-apps/logic-apps-custom-connector-register.md) (Logic Apps: uso compartido del conector)
* [Flow: Uso compartido del conector personalizado](https://ms.flow.microsoft.com/documentation/register-custom-api/#share-your-custom-connector)
* [PowerApps: Compartir el conector personalizado](https://powerapps.microsoft.com/tutorials/register-custom-api/#share-your-custom-connector)

## <a name="7-certify-your-connector"></a>7. Certificación del conector

Para compartir opcionalmente el conector con todos los usuarios de Logic Apps, Flow y PowerApps, presente su conector para que obtenga la certificación de Microsoft. En este proceso se revisa el conector, se comprueba la compatibilidad técnica y de contenido y se valida la funcionalidad para Logic Apps, Flow y PowerApps antes de que Microsoft pueda publicar su conector. Obtenga información acerca de [cómo presentar el conector para conseguir la certificación de Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Obtención de soporte técnico

* Para obtener soporte con la incorporación y el desarrollo, envíe un correo electrónico a [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com). Microsoft supervisa activamente esta cuenta para los problemas y las preguntas de los desarrolladores, y los dirige al equipo adecuado. 

* Para consultar las preguntas más frecuentes, consulte [Preguntas más frecuentes sobre conectores personalizados](../logic-apps/custom-connector-faq.md).

## <a name="next-steps"></a>Pasos siguientes

* [Build a custom connector from a Web API](../logic-apps/custom-connector-build-web-api-app-tutorial.md) (Compilación de un conector personalizado desde una API web)
* [Set up authentication for custom connectors](../logic-apps/custom-connector-azure-active-directory-authentication.md) (Configuración de la autenticación para conectores personalizados)
* [Describe custom connectors with Postman collections](../logic-apps/custom-connector-api-postman-collection.md) (Descripción de conectores personalizados con colecciones de Postman)
* [Submit custom connectors for Microsoft certification](../logic-apps/custom-connector-submit-certification.md) (Presentación de conectores personalizados para la certificación de Microsoft)
