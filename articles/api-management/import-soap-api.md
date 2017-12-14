---
title: "Importación de la API de SOAP con Azure Portal | Microsoft Docs"
description: "Obtenga información sobre cómo importar la API de SOAP con API Management."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: 0a013aa63e8b04748e1b64126795189a5d189fa1
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="import-soap-api"></a>Importación de una API de SOAP

Este artículo explica cómo importar una representación XML estándar de una API de SOAP. El artículo también muestra cómo probar la API de APIM.

En este artículo, aprenderá a:

> [!div class="checklist"]
> * Importación de una API de SOAP
> * Prueba de la API en Azure Portal
> * Pruebe la API en el Portal para desarrolladores

## <a name="prerequisites"></a>Requisitos previos

Completar la guía de inicio rápido siguiente: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importación y publicación de una API de back-end

1. Seleccione **API** en **API MANAGEMENT**.
2. Seleccione **WSDL** en la lista **Add a new API** (Agregar una nueva API).

    ![API de SOAP](./media/import-soap-api/wsdl-api.png)
3. En la **especificación WSDL**, escriba la dirección URL donde reside la API de SOAP.
4. El botón de radio **Paso a través de SOAP** está seleccionado de forma predeterminada. Con esta selección, la API se va a exponer como SOAP. El consumidor tiene que utilizar reglas SOAP. Si desea usar Restify en la API, siga los pasos descritos en [Import a SOAP API and convert it to REST](restify-soap-api.md) (Importación de una API de SOAP y conversión a REST).

    ![Paso a través](./media/import-soap-api/pass-through.png)
5. Presione Tab.

    Los siguientes campos se rellenan con la información de la API de SOAP: Nombre para mostrar, Nombre y Descripción.
6. Agregue un sufijo URL de API. El sufijo es un nombre que identifica esta API concreta en esta instancia de APIM. Debe ser único en esta instancia de APIM.
9. Publique la API asociándola a un producto. En este caso, se usa el producto "*Unlimited*".  Si desea que la API se publique y esté disponible para los desarrolladores, agréguela a un producto. Puede hacerlo durante la creación de la API o configurarla más adelante.

    Los productos son asociaciones de una o varias API. Puede incluir varias API y ofrecerlas a los desarrolladores mediante el portal para desarrolladores. En primer lugar, los desarrolladores deben suscribirse a un producto para acceder a la API. Al suscribirse, obtienen una clave de suscripción que funciona con cualquier API de ese producto. Si creó la instancia de APIM, ya es un administrador, así que, de forma predeterminada, está suscrito a todos los productos.

    De forma predeterminada, cada instancia de API Management incluye dos productos de ejemplo:

    * **Starter**
    * **Sin límite**   
10. Seleccione **Crear**.

### <a name="test-the-new-apim-api-in-the-administrative-portal"></a>Prueba de la nueva API de APIM en el portal de administración

Se puede llamar a las operaciones directamente desde el portal administrativo para desarrolladores, lo que proporciona una forma cómoda de ver y probar las operaciones de una API.  

1. Seleccione API que creó en los pasos anteriores.
2. Presione la pestaña **Prueba**.
3. Seleccione alguna operación.

    La página muestra los campos de parámetros de consulta y los campos para los encabezados. Uno de los encabezados es "Ocp-Apim-Suscripción-Key", para la clave de suscripción del producto que está asociado a esta API. Si ha creado la instancia APIM, ya es administrador, por lo que la clave se rellena automáticamente. 
1. Presione **Enviar**.

    Back-end responde con **200 Aceptar** y algunos datos.

### <a name="call-operation"></a>Llamada a una operación desde el portal para desarrolladores

También se pueden llamar a las operaciones desde **portal para desarrolladores** para probar las API. 

1. Seleccione la API que creó en el paso "Importación y publicación de una API de back-end".
2. Presione **Portal para desarrolladores**.

    Se abre el sitio "Portal para desarrolladores".
3. Seleccione la **API** que ha creado.
4. Haga clic en la operación que desea probar.
5. Presione **Try it** (Probarlo).
6. Presione **Enviar**.
    
    Después de invocar una operación, el portal para desarrolladores mostrará el **estado de respuesta**, los **encabezados de respuesta** y el **contenido de respuesta**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Transformación y protección de una API publicada](transform-api.md)