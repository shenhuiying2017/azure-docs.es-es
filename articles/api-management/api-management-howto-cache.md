---
title: "Incorporación del almacenamiento en caché para mejorar el rendimiento en Azure API Management | Microsoft Docs"
description: "Obtenga información acerca de cómo mejorar la latencia, el consumo de ancho de banda y la carga de servicios web para las llamadas de servicio de API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 7458ad6e0a864d742f74ce743ce3179594113c00
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Incorporación del almacenamiento en caché para mejorar el rendimiento en Azure API Management
En Administración de API, las operaciones se pueden configurar para el almacenamiento en caché de respuestas. El almacenamiento en caché de respuestas puede reducir significativamente la latencia de la API, el consumo de ancho de banda y la carga del servicio web en cuanto a datos que no cambian con frecuencia.
 
Para más información acerca del almacenamiento en caché, consulte [Directivas de almacenamiento en caché de API Management](api-management-caching-policies.md) y [Almacenamiento en caché personalizado en Azure API Management](api-management-sample-cache-by-key.md).

![directivas de caché](media/api-management-howto-cache/cache-policies.png)

Temas que se abordarán:

> [!div class="checklist"]
> * Agregar almacenamiento en caché de respuesta a una API
> * Comprobar el almacenamiento en caché en acción

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

+ [Cree una instancia de Azure API Management](get-started-create-service-instance.md)
+ [Incorpore y publique una API](import-and-publish.md)

## <a name="caching-policies"> </a>Adición de las directivas de almacenamiento en caché

Con las directivas de almacenamiento en caché que se muestran en este ejemplo, la primera solicitud de la operación **GetSpeakers** devuelve una respuesta del servicio back-end. Dicha respuesta se almacena en la caché, con una clave especificada mediante encabezados y parámetros de la cadena de consulta. Las siguientes llamadas a la operación, con parámetros coincidentes, devolverán la respuesta almacenada en caché hasta que el intervalo de duración en la caché haya expirado.

1. Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).
2. Vaya a la instancia de APIM.
3. Seleccione la pestaña **API**.
4. Haga clic en **Demo Conference API** (API de conferencia de demostración) en la lista de API.
5. Seleccione **GetSpeakers**.
6. En la parte superior de la pantalla, seleccione la pestaña **Design** (Diseño).
7. En la ventana **Inbound processing** (Procesamiento de entrada), haga clic en el triángulo (al lado del lápiz).

    ![editor de código](media/api-management-howto-cache/code-editor.png)
8. Seleccione **Code editor** (Editor de código).
9. En el elemento **inbound**, agregue la siguiente directiva:

        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
            <vary-by-header>Accept</vary-by-header>
            <vary-by-header>Accept-Charset</vary-by-header>
            <vary-by-header>Authorization</vary-by-header>
        </cache-lookup>

10. En el elemento **outbound**, agregue la siguiente directiva:

        <cache-store caching-mode="cache-on" duration="20" />

    **Duración** especifica el intervalo de expiración de las respuestas almacenadas en caché. En este ejemplo, el intervalo es **20** segundos.

## <a name="test-operation"></a>Llamada a una operación y prueba del almacenamiento en caché
Para ver cómo funciona el almacenamiento en caché, llame a la operación desde el portal para desarrolladores.

1. En Azure Portal, vaya a la instancia de APIM.
2. Seleccione la pestaña **API**.
3. Seleccione la API a la que ha agregado directivas de almacenamiento en caché.
4. Seleccione la operación **GetSpeakers**.
5. Haga clic en la pestaña **Test** (Prueba) del menú superior derecho.
6. Pulse **Enviar**.

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre las directivas de almacenamiento en caché, consulte [Caching policies][Caching policies] (Directivas de almacenamiento en caché) en [API Management policy reference][API Management policy reference] (Referencia de la directiva de API Management).
* Para obtener información sobre el almacenamiento en caché de los elementos por parte de la clave mediante expresiones de directiva, consulte [Custom caching in Azure API Management](api-management-sample-cache-by-key.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
