---
title: "Importación y publicación de la primera API en Azure API Management | Microsoft Docs"
description: "Obtenga información sobre cómo importar y publicar la primera API con API Management."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: apimpm
ms.openlocfilehash: cd6ceaf5f8cdcfbde5d0d2bebb4b89488d0122e9
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="import-and-publish-your-first-api"></a>Importación y publicación de la primera API 

Este tutorial muestra cómo importar una API de back-end "Especificación OpenAPI" que reside en http://conferenceapi.azurewebsites.net?format=json. Esta API de back-end la proporciona Microsoft y se hospeda en Azure. 

Una vez que la API de back-end se importa en Management API (APIM), la API de APIM se convierte en una fachada de la API de back-end. En el momento en que se importa la API de back-end, el origen de la API y la API de APIM son idénticos. APIM permite personalizar la fachada según sus necesidades sin tocar la API de back-end. Para obtener más información, consulte [Transformación y protección de una API](transform-api.md). 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Importación de la primera API
> * Prueba de la API en Azure Portal
> * Pruebe la API en el Portal para desarrolladores

![Nueva API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>Requisitos previos

Completar la guía de inicio rápido siguiente: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importación y publicación de una API de back-end

Esta sección explica cómo importar y publicar una API de back-end de Especificación OpenAPI.
 
1. Seleccione **API** en **API MANAGEMENT**.
2. Seleccione **OpenAPI especificación** en la lista.

    ![Creación de una API](./media/api-management-get-started/create-api.png)

    Puede establecer los valores de API durante la creación o luego accediendo a la pestaña **Configuración**.  

    |Configuración|Valor|Descripción|
    |---|---|---|
    |**Especificación OpenAPI**|http://conferenceapi.azurewebsites.net?format=json|Hace referencia al servicio que implementa la API. Administración de API envía las solicitudes a esta dirección.|
    |**Nombre para mostrar**|*API de conferencia de demostración* (API de conferencia de demostración)|Si presiona la tecla Tab después de escribir la dirección URL del servicio, APIM rellenará este campo en función de lo que aparece en el JSON. <br/>El nombre se muestra en el Portal para desarrolladores.|
    |**Name**|*demo-conference-api*|Proporciona un nombre único para la API. <br/>Si presiona la tecla Tab después de escribir la dirección URL del servicio, APIM rellenará este campo en función de lo que aparece en el JSON.|
    |**Descripción**|Proporcione una descripción opcional de la API.|Si presiona la tecla Tab después de escribir la dirección URL del servicio, APIM rellenará este campo en función de lo que aparece en el JSON.|
    |**Sufijo de dirección URL de API**|*conference*|El sufijo se anexa a la dirección URL base del servicio API Management. Administración de API distingue las API por su sufijo, por lo que el sufijo debe ser único para cada API de un publicador determinado.|
    |**Esquema URL**|*HTTPS*|Determina los protocolos que se pueden usar para acceder a la API. |
    |**Productos**|*Sin límite*| Publique la API asociándola a un producto. Para agregar, opcionalmente, esta nueva API a un producto, escriba el nombre del producto. Este paso se puede repetir varias veces para agregar la API a varios productos.<br/>Los productos son asociaciones de una o varias API. Puede incluir varias API y ofrecerlas a los desarrolladores mediante el portal para desarrolladores. En primer lugar, los desarrolladores deben suscribirse a un producto para acceder a la API. Al suscribirse, obtienen una clave de suscripción que funciona con cualquier API de ese producto. Si creó la instancia de APIM, ya es un administrador, así que, de forma predeterminada, está suscrito a todos los productos.<br/> De forma predeterminada, cada instancia de API Management incluye dos productos de ejemplo: **Starter** y **Unlimited**. |
3. Seleccione **Crear**.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Prueba de la nueva API APIM en Azure Portal

Se puede llamar a las operaciones directamente desde Azure Portal, lo que proporciona una forma cómoda de ver y probar las operaciones de una API.  
1. Seleccione API que creó en los pasos anteriores.
2. Presione la pestaña **Prueba**.

    ![Prueba de la API](./media/api-management-get-started/test-api.png)
3. Haga clic en **GetSpeakers**.

    La página muestra los campos de los parámetros de consulta, pero, en este caso, no tiene ninguno. La página también muestra los campos de los encabezados. Uno de los encabezados es "Ocp-Apim-Suscripción-Key", para la clave de suscripción del producto que está asociado a esta API. Si ha creado la instancia APIM, ya es administrador, por lo que la clave se rellena automáticamente. 
4. Presione **Enviar**.

    Back-end responde con **200 Aceptar** y algunos datos.

## <a name="call-operation"></a>Llamada a una operación desde el portal para desarrolladores

También se pueden llamar a las operaciones desde **portal para desarrolladores** para probar las API. 

1. Seleccione la API que creó en el paso "Importación y publicación de una API de back-end".
2. Presione **Portal para desarrolladores**.

    ![Prueba en el Portal para desarrolladores](./media/api-management-get-started/developer-portal.png)

    Se abre el sitio "Portal para desarrolladores".
3. Seleccione **API**.
4. Seleccione **Demo Conference API** (API de conferencia de demostración).
5. Haga clic en **GetSpeakers**.
    
    La página muestra los campos de los parámetros de consulta, pero, en este caso, no tiene ninguno. La página también muestra los campos de los encabezados. Uno de los encabezados es "Ocp-Apim-Suscripción-Key", para la clave de suscripción del producto que está asociado a esta API. Si ha creado la instancia APIM, ya es administrador, por lo que la clave se rellena automáticamente.
6. Presione **Try it** (Probarlo).
7. Presione **Enviar**.
    
    Después de invocar una operación, el portal para desarrolladores mostrará el **estado de respuesta**, los **encabezados de respuesta** y el **contenido de respuesta**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Importación de la primera API
> * Prueba de la API en Azure Portal
> * Pruebe la API en el Portal para desarrolladores

Avance hasta el siguiente tutorial:

> [!div class="nextstepaction"]
> [Creación y publicación de un producto](api-management-howto-add-products.md)