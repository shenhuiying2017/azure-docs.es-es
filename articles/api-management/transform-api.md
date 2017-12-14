---
title: "Transformación y protección de una API con Azure API Management | Microsoft Docs"
description: "Aprenda a proteger su API con directivas de cuotas y limitaciones (limitación de frecuencia)."
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
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 772f3828d85c54e7b8bb44c857e555175b7444cc
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="transform-and-protect-your-api"></a>Transformación y protección de una API 

El tutorial muestra cómo puede transformar una API para que no revele información privada del back-end. Por ejemplo, recomendamos ocultar la información sobre la pila de tecnología que se ejecuta en el back-end, así como las URL originales que aparecen en el cuerpo de la respuesta HTTP de la API y, en su lugar, redirigirlas a la puerta de enlace de APIM.

Este tutorial muestra lo fácil que es agregar protección para la API de back-end configurando el límite de frecuencia con Azure API Management. Por ejemplo, puede que quiera limitar un número de llamadas a la API para que los desarrolladores no la sobreutilicen. Para obtener más información, consulte [Directivas de administración de API](api-management-policies.md).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Transformar una API para eliminar encabezados de respuesta
> * Reemplazar URL originales en el cuerpo de la respuesta de API con las URL de puerta de enlace de APIM
> * Proteger una API agregando la directiva de límite de frecuencia (limitación)
> * Probar las transformaciones

![Directivas](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>Requisitos previos

+ Completar la guía de inicio rápido siguiente: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
+ Además, completar el tutorial siguiente: [Importación y publicación de la primera API](import-and-publish.md).
 
[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Transformación una API para eliminar encabezados de respuesta

Esta sección muestra cómo ocultar los encabezados HTTP que no desea mostrar a los usuarios. En este ejemplo, se eliminan los siguientes encabezados en la respuesta HTTP:

* **X-Powered-By**
* **X-AspNet-Version**

### <a name="test-the-original-response"></a>Prueba de la respuesta original

Para ver la respuesta original, siga estos pasos:

1. Seleccione la pestaña **API**.
2. Haga clic en **Demo Conference API** (API de conferencia de demostración) en la lista de API.
3. Seleccione la operación **GetSpeakers**.
4. Haga clic en la casilla **Prueba** de la parte superior de la pantalla.
5. Haga clic en el botón **Enviar** situado en la parte inferior de la pantalla. 

    Como puede ver, la respuesta original tiene el siguiente aspecto:

    ![Directivas](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>Establecimiento de la directiva de transformación

1. Vaya a la instancia de APIM.
2. Seleccione la pestaña **API**.
3. Haga clic en **Demo Conference API** (API de conferencia de demostración) en la lista de API.
4. Seleccione **Todas las operaciones**.
5. En la parte superior de la pantalla, seleccione la pestaña **Diseño**.
6. En la ventana **Procesamiento de salida**, haga clic en el triángulo (al lado del lápiz).
7. Seleccione **Editor de código**.
    
     ![Editar directiva](./media/set-edit-policies/set-edit-policies01.png)
9. Coloque el cursor dentro del elemento **<outbound>**.
10. En la ventana de la derecha, en **Transformation policies** (Directivas de transformación), haga clic en **Set HTTP header** (Establecer encabezado HTTP) dos veces (para insertar dos fragmentos de directiva).

    ![Directivas](./media/transform-api/transform-api.png)
11. Modificar el código **<outbound>** para que se parezca a esto:

        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
                
## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Reemplazo de URL originales en el cuerpo de la respuesta de API con las URL de puerta de enlace de APIM

En esta sección se explica cómo ocultar las URL originales que aparecen en el cuerpo de la respuesta HTTP de la API y, en su lugar, redirigirlas a la puerta de enlace de APIM.

### <a name="test-the-original-response"></a>Prueba de la respuesta original

Para ver la respuesta original, siga estos pasos:

1. Seleccione la pestaña **API**.
2. Haga clic en **Demo Conference API** (API de conferencia de demostración) en la lista de API.
3. Seleccione la operación **GetSpeakers**.
4. Haga clic en la casilla **Prueba** de la parte superior de la pantalla.
5. Haga clic en el botón **Enviar** situado en la parte inferior de la pantalla. 

    Como puede ver, la respuesta original tiene el siguiente aspecto:

    ![Directivas](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>Establecimiento de la directiva de transformación

1. Vaya a la instancia de APIM.
2. Seleccione la pestaña **API**.
3. Haga clic en **Demo Conference API** (API de conferencia de demostración) en la lista de API.
4. Seleccione **Todas las operaciones**.
5. En la parte superior de la pantalla, seleccione la pestaña **Diseño**.
6. En la ventana **Procesamiento de salida**, haga clic en el triángulo (al lado del lápiz).
7. Seleccione **Editor de código**.
8. Coloque el cursor dentro del elemento **<outbound>**.
9. En la ventana de la derecha, en **Transformation policies** (Directivas de transformación), haga clic en **+ Find and replace string in body** (+ Buscar y reemplazar cadena en el cuerpo).
10. Modifique el código **<find-and-replace** (en el elemento **<outbound>**) para reemplazar la URL con el fin de que coincida con la puerta de enlace de APIM. Por ejemplo:

        <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Protección de una API agregando la directiva de límite de frecuencia (limitación)

En esta sección se explica cómo agregar protección para la API de back-end configurando límites de frecuencia. Por ejemplo, puede que quiera limitar un número de llamadas a la API para que los desarrolladores no la sobreutilicen. En este ejemplo, el límite se establece en 3 llamadas cada 15 segundos para cada identificador de suscripción. Después de 15 segundos, un desarrollador puede volver a tratar de llamar a la API.

1. Vaya a la instancia de APIM.
2. Seleccione la pestaña **API**.
3. Haga clic en **Demo Conference API** (API de conferencia de demostración) en la lista de API.
4. Seleccione **Todas las operaciones**.
5. En la parte superior de la pantalla, seleccione la pestaña **Diseño**.
6. En la ventana **Procesamiento de entrada**, haga clic en el triángulo (al lado del lápiz).
7. Seleccione **Editor de código**.
8. Coloque el cursor dentro del elemento **<inbound>**.
9. En la ventana de la derecha, bajo **Access restriction policies** (Directivas de restricción de acceso), haga clic en **+ Limit call rate per key** (+ Limitar la tasa de llamadas por clave).
10. Modifique el código **<rate-limit-by-key** (en el elemento **<inbound>**) en el código siguiente:

        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />

## <a name="test-the-transformations"></a>Prueba de las transformaciones
        
En este punto, la directivas de código tienen este aspecto:

    <policies>
        <inbound>
            <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <set-header name="X-Powered-By" exists-action="delete" />
            <set-header name="X-AspNet-Version" exists-action="delete" />
            <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>

En el resto de esta sección se prueban transformaciones de directiva que estableció en este artículo.

### <a name="test-the-stripped-response-headers"></a>Prueba de los encabezados de respuesta eliminados

1. Vaya a la instancia de APIM.
2. Seleccione la pestaña **API**.
3. Haga clic en **Demo Conference API** (API de conferencia de demostración) en la lista de API.
4. Haga clic en la operación **GetSpeakers**.
5. Seleccione la pestaña **Prueba**.
6. Presione **Enviar**.

    Como puede ver, los encabezados se han eliminado:

    ![Directivas](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>Prueba de la URL reemplazada

1. Vaya a la instancia de APIM.
2. Seleccione la pestaña **API**.
3. Haga clic en **Demo Conference API** (API de conferencia de demostración) en la lista de API.
4. Haga clic en la operación **GetSpeakers**.
5. Seleccione la pestaña **Prueba**.
6. Presione **Enviar**.

    Como puede ver, la URL se ha reemplazado.

    ![Directivas](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>Prueba del límite de frecuencia (limitación)

1. Vaya a la instancia de APIM.
2. Seleccione la pestaña **API**.
3. Haga clic en **Demo Conference API** (API de conferencia de demostración) en la lista de API.
4. Haga clic en la operación **GetSpeakers**.
5. Seleccione la pestaña **Prueba**.
6. Presione **Enviar** tres veces en una fila.

    Después de enviar la solicitud 3 veces, obtendrá la respuesta **429 Demasiadas solicitudes**.
7. Espere 15 segundos o menos y vuelva a pulsar **Enviar**. Esta vez debería obtener una respuesta **200 OK**.

    ![Limitaciones](./media/transform-api/test-throttling.png)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Transformación una API para eliminar encabezados de respuesta
> * Reemplazo de URL originales en el cuerpo de la respuesta de API con las URL de puerta de enlace de APIM
> * Protección de una API agregando la directiva de límite de frecuencia (limitación)
> * Prueba de las transformaciones

Avance hasta el siguiente tutorial:

> [!div class="nextstepaction"]
> [Supervisión de una API](api-management-howto-use-azure-monitor.md)
