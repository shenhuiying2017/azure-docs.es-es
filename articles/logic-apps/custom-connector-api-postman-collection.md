---
title: "Descripción de las API y los conectores personalizados con Postman - Azure Logic Apps | Microsoft Docs"
description: Cree colecciones de Postman para describir, agrupar y organizar las API y los conectores personalizados
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
ms.openlocfilehash: 072d544e5d29c4abb3d69651e53cfb33d5e0c9e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="describe-custom-apis-and-custom-connectors-with-postman"></a>Descripción de las API y los conectores personalizados con Postman

Para facilitar y acelerar el desarrollo de [API personalizadas](../logic-apps/logic-apps-create-api-app.md) y [conectores personalizados](../logic-apps/custom-connector-overview.md), puede crear colecciones de [Postman](https://www.getpostman.com/), en lugar de documentos de OpenAPI, para describir las API y los conectores. Las colecciones de Postman le ayudan a organizar y agrupar las solicitudes de API relacionadas. Por ejemplo, puede usar Postman al crear conectores para Azure Logic Apps, Microsoft Flow o Microsoft PowerApps. 

En este tutorial se muestra cómo crear una [colección de Postman](https://www.getpostman.com/docs/postman/collections/creating_collections) mediante el uso de la [API Detect Language](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) en [Text Analytics de Azure Cognitive Services ](https://azure.microsoft.com/services/cognitive-services/text-analytics/) como ejemplo. Esta API identifica el lenguaje, la opinión y las frases clave en el texto que se pasa a la API.

## <a name="prerequisites"></a>Requisitos previos

* Si no está familiarizado con Postman, [instale la aplicación Postman](https://www.getpostman.com/apps).

* Una suscripción de Azure. Si no tiene una suscripción, puede [comenzar con una cuenta de Azure gratuita](https://azure.microsoft.com/free/). También puede registrarse para obtener una [suscripción de pago por uso](https://azure.microsoft.com/pricing/purchase-options/).

* Si tiene una suscripción de Azure, regístrese en las API de Text Analytics completando la [Tarea 1 que se presenta aquí](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-signup.md). 

## <a name="create-a-postman-collection"></a>Creación de la colección de Postman

Antes de crear una colección, cree una solicitud HTTP para el punto de conexión de API. 

### <a name="create-an-http-request-for-your-api"></a>Creación de una solicitud HTTP para la API

1. Abra la aplicación de Postman para crear una [solicitud HTTP](https://www.getpostman.com/docs/postman/sending_api_requests/requests) para el punto de conexión de la API. Para obtener más información, consulte la [documentación de solicitudes](https://www.getpostman.com/docs/postman/sending_api_requests/requests) de Postman.

   1. En la pestaña **Builder** (Compilador), seleccione el método HTTP, escriba la dirección URL de solicitud para el punto de conexión de API y seleccione un protocolo de autorización, si procede. 
   Cuando esté listo, elija **Params** (Parámetros).

      Para este tutorial, puede usar esta configuración de ejemplo:

      ![Creación de solicitud: "Método HTTP", "URL de la solicitud", "Autorización"](./media/custom-connector-api-postman-collection/01-create-api-http-request.png)

      | Parámetro | Valor sugerido | 
      | --------- | --------------- | 
      | **HTTP method** (Método HTTP) | POST | 
      | **Request URL** (URL de la solicitud) | `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages` | | 
      | **Autorización** | "Sin autenticación" | | 
      ||| 

   2. Ahora puede especificar pares de clave-valor para utilizar como parámetros de consulta o ruta de acceso en la dirección URL de la solicitud. Postman combina estos elementos juntos en una cadena de consulta.
   Cuando haya terminado, seleccione **Headers** (Encabezados).

      ![Solicitud continuada: "Parameters" (Parámetros)](./media/custom-connector-api-postman-collection/02-create-api-http-request-params.png)

      | Parámetro | Valor sugerido | 
      | --------- | --------------- | 
      | **Params** (Parámetros) | **Clave**: "numberOfLanguagesToDetect" </br>**Valor**: "1" | 
      ||| 

   3. Introduzca los pares de clave-valor para el encabezado de la solicitud. 
   En cuanto al nombre del encabezado, escriba cualquier cadena que desee. Para los encabezados HTTP comunes, puede seleccionar en la lista desplegable. Cuando termine, seleccione **Body** (Cuerpo). 
   
      ![Solicitud continuada: "Headers" (Encabezados)](./media/custom-connector-api-postman-collection/03-create-api-http-request-header.png)

      | Parámetro | Valor | 
      | --------- | ----- | 
      | **Encabezados** | **Clave**: "Ocp-Apim-Subscription-Key" </br>**Valor**: *clave-suscripción-API*, que encontrará en la cuenta de Cognitive Services <p>**Clave**: "Content-Type" </br> **Valor**: "application/json" | 
      ||| 

   4. Escriba el contenido que desea enviar en el cuerpo de solicitud. 
   Para comprobar que la solicitud funciona mediante la obtención de una respuesta de vuelta, elija **Send** (Enviar). 
   
      ![Solicitud continuada: "Body" (Cuerpo)](./media/custom-connector-api-postman-collection/04-create-api-http-request-body.png)

      | Parámetro | Valor sugerido | 
      | --------- | --------------- |    
      | **Cuerpo** | ```{"documents": [{ "id": "1", "text": "Hello World"}]}``` | 
      ||| 

      El campo de respuesta contiene la respuesta completa de la API, incluido el resultado o un error, si existe.

      ![Obtención de respuesta a la solicitud](./media/custom-connector-api-postman-collection/05-create-api-http-request-response.png)

2. Una vez que haya comprobado que la solicitud funciona, guárdela en una colección de Postman. 

   1. Elija **Guardar**. 
      
      ![Elija "Save" (Guardar).](./media/custom-connector-api-postman-collection/06a-save-request.png)
 
   2. En **Save Request** (Guardar solicitud), proporcione un valor para **Request Name** (Nombre de solicitud) y, opcionalmente, para **Request description** (Descripción de solicitud). 

      > [!NOTE]
      > El conector personalizado usa estos valores más adelante. Por lo tanto, asegúrese de proporcionar los mismos valores que posteriormente utilizará para el resumen y la descripción de la operación de la API personalizada.

   3. Elija **+ Create Collection** (Crear colección) y póngale un nombre a la colección. 
   Active la marca de verificación, que crea una carpeta de colección, y elija a continuación **Save to *(Guardar en) nombre-su-colección-Postman***.

      ![Guardado de solicitud](./media/custom-connector-api-postman-collection/06b-save-request.png)

### <a name="save-the-request-response"></a>Guardado de respuesta de solicitud

Después de guardar la solicitud, puede guardar la respuesta. De este modo, la respuesta aparece como un ejemplo al cargar la solicitud más tarde.

1. Encima de la ventana de la respuesta, elija **Save Response** (Guardar respuesta). 

   ![Guardado de respuesta](./media/custom-connector-api-postman-collection/07-create-api-http-request-save-response.png)

   Los conectores personalizados admiten solo una respuesta por solicitud. 
   Si guarda varias respuestas por solicitud, solo se utiliza la primera de ellas.

2. Póngale un nombre al ejemplo y elija **Save Example** (Guardar ejemplo).

3. Continúe compilando la colección de Postman con las solicitudes y las respuestas adicionales.

### <a name="export-your-postman-collection"></a>Exportación de la colección de Postman

1. Cuando haya terminado, exporte la colección a un archivo JSON.

   ![Exportación de colección](./media/custom-connector-api-postman-collection/08-export-http-request.png)

2. Elija el formato de exportación **Collection v1** (Colección v1) y vaya a la ubicación en la que desea guardar el archivo JSON.

   > [!NOTE]
   > Actualmente, solo funciona la versión 1 para los conectores personalizados.

   ![Selección de formato de exportación: "Collection v1"](./media/custom-connector-api-postman-collection/09-export-format.png)
   
Ahora puede utilizar esta colección de Postman para crear API y conectores personalizados. Después de exportar la colección, puede importar el archivo JSON en Logic Apps, Flow o PowerApps.

> [!IMPORTANT]
> Si crea un conector personalizado a partir de una colección de Postman, asegúrese de quitar el encabezado `Content-type` de las acciones y los desencadenadores. El servicio de destino, por ejemplo, Flow, agrega automáticamente este encabezado. Además, quite los encabezados de autenticación de la sección `securityDefintions`, de las acciones y los desencadenadores.

## <a name="next-steps"></a>Pasos siguientes

* [Logic Apps: Registro de conectores personalizados](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow: Registro del conector personalizado](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [Registrar y usar conectores personalizados en PowerApps](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)
* [Preguntas más frecuentes sobre conectores personalizados](../logic-apps/custom-connector-faq.md)
