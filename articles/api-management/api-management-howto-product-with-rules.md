---
title: "Protección de la API con Azure API Management | Microsoft Docs"
description: "Aprenda a proteger su API con directivas de cuotas y limitaciones (limitación de frecuencia)."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 450dc368-d005-401d-ae64-3e1a2229b12f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 300b1d762a61c810dbffde5aaacd8a85f12c9fca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>Protección de su API con límites de frecuencia mediante Azure API Management
Esta guía muestra lo fácil que es agregar protección para la API de back-end mediante la configuración de directivas de cuota y límite de frecuencia con Azure API Management.

En este tutorial, creará un producto de API de "evaluación gratuita" que permita a los desarrolladores realizar hasta 10 llamadas por minuto y hasta un máximo de 200 llamadas por semana a la API mediante las directivas [Limitar la frecuencia de llamadas por suscripción](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) y [Establecer la cuota de uso por suscripción](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota). A continuación, publicará la API y probará la directiva de límite de frecuencia.

Para más información sobre escenarios de limitación avanzados mediante las directivas [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) y [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey), consulte [Limitación avanzada de solicitudes con Azure API Management](api-management-sample-flexible-throttling.md).

## <a name="create-product"></a>Para crear un producto
En este paso, creará un producto de evaluación gratuita que no requiere aprobación de suscripción.

> [!NOTE]
> Si ya tiene un producto configurado y desea usarlo para este tutorial, puede pasar la sección [Configurar directivas de cuota y límite de frecuencia][Configure call rate limit and quota policies] y seguir el tutorial a partir de ahí con su producto en lugar de con el producto de evaluación gratuita.
> 
> 

Para comenzar, haga clic en **Portal para editores** en Azure Portal para el servicio API Management.

![Portal del publicador][api-management-management-console]

> Si aún no ha creado ninguna instancia del servicio de API Management, consulte [Creación de una instancia del servicio API Management][Create an API Management service instance] en el tutorial [Administración de su primera API en Azure API Management][Manage your first API in Azure API Management].
> 
> 

Haga clic en **Productos** en el menú **API Management** a la izquierda para mostrar la página **Productos**.

![Add product][api-management-add-product]

Haga clic en **Agregar producto** para mostrar el cuadro de diálogo **Agregar nuevo producto**.

![Agregar nuevo producto][api-management-new-product-window]

En el cuadro **Título** escriba **Evaluación gratuita**.

En el cuadro **Descripción**, escriba **Los suscriptores podrán realizar 10 llamadas/minuto hasta un máximo de 200 llamadas/semana; después, el acceso se denegará.**

Los productos de API Management pueden estar abiertos o protegidos. Para poder usar los productos protegidos es necesario suscribirse antes a ellos, mientras que los productos abiertos pueden usarse sin suscripción. Asegúrese de que la opción **Requerir suscripción** está seleccionada para crear un producto protegido que requiera una suscripción. Esta es la configuración predeterminada.

Si desea que un administrador revise y acepte o rechace los intentos de suscripción a este producto, active **Requerir aprobación de suscripción**. Si la casilla no está seleccionada, los intentos de suscripción se aprobarán automáticamente. En este ejemplo, las suscripciones se aprueban automáticamente, por lo que no tiene que seleccionar la casilla.

Para permitir que las cuentas de desarrollador se suscriban varias veces al nuevo producto, active la casilla **Permitir varias suscripciones simultáneas** . En este tutorial no se usan varias suscripciones simultáneas; por tanto, no active la casilla.

Una vez especificados todos los valores, haga clic en **Guardar** para crear el producto.

![Product added][api-management-product-added]

De forma predeterminada, los usuarios pueden ver los nuevos productos en el grupo **Administradores** . Vamos a agregar el grupo **Desarrolladores** . Haga clic en **Evaluación gratuita** y, después, en la pestaña **Visibilidad**.

> En API Management, los grupos se usan para administrar la visibilidad de productos para los desarrolladores. Los productos conceden visibilidad a los grupos y los desarrolladores pueden ver los productos visibles a los grupos a los que pertenecen y suscribirse a ellos. Para más información, consulte [Creación y uso de grupos en Azure API Management][How to create and use groups in Azure API Management].
> 
> 

![Add developers group][api-management-add-developers-group]

Seleccione la casilla **Desarrolladores** y, a continuación, haga clic en **Guardar**.

## <a name="add-api"></a>Para agregar una API al producto
En este paso del tutorial, agregaremos la API Eco al nuevo producto Prueba gratuita.

> Cada instancia del servicio API Management viene previamente configurada con una API Eco que se puede usar para experimentar con API Management y aprender de esta. Para más información, consulte [Administración de su primera API en Azure API Management][Manage your first API in Azure API Management].
> 
> 

Haga clic en **Productos** en el menú **API Management** a la izquierda y luego haga clic en **Evaluación gratuita** para configurar el producto.

![Configure product][api-management-configure-product]

Haga clic en **Agregar API al producto**.

![Agregar API al producto][api-management-add-api]

Seleccione **Echo API** (API Eco ) y luego haga clic en **Guardar**.

![Add Echo API][api-management-add-echo-api]

## <a name="policies"></a>Para configurar las directivas de límite de frecuencia de llamadas y de cuota
Los límites de tasa y las cuotas se configuran en el editor de directivas. Las dos directivas que se van a agregar en este tutorial son [Limitar la frecuencia de llamadas por suscripción](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) y [Establecer la cuota de uso por suscripción](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota). Estas directivas deben aplicarse en el ámbito del producto.

Haga clic en **Directivas** en el menú **API Management** de la izquierda. En la lista **Producto**, haga clic en **Evaluación gratuita**.

![Product policy][api-management-product-policy]

Haga clic en **Agregar directiva** para importar la plantilla de directivas y comenzar a crear la directiva de límite de frecuencia y de cuota.

![Agregar directiva][api-management-add-policy]

Las directivas de límite de tasa y de cuota son directivas de entrada, por lo que debe posicionar el cursor en el elemento inbound.

![Policy editor][api-management-policy-editor-inbound]

Desplácese por la lista de directivas y busque la entrada de directiva para **limitar la frecuencia de llamadas por suscripción**.

![Policy statements][api-management-limit-policies]

Una vez que el cursor se ha situado en el elemento de directiva **inbound**, haga clic en la flecha situada junto a **Limitar la frecuencia de llamadas por suscripción** para insertar su plantilla de directiva.

```xml
<rate-limit calls="number" renewal-period="seconds">
<api name="name" calls="number">
<operation name="name" calls="number" />
</api>
</rate-limit>
```

Como puede ver por el fragmento de código, la directiva permite establecer los límites de las API y las operaciones del producto. En este tutorial no se usará esa funcionalidad, así que elimine los elementos **api** y **operation** del elemento **rate-limit**, de forma que solo quede el elemento **rate-limit** exterior, como se muestra en el ejemplo siguiente.

```xml
<rate-limit calls="number" renewal-period="seconds">
</rate-limit>
```

En el producto de evaluación gratuita, la tasa máxima de llamadas permitida es de 10 llamadas por minuto; por tanto, escriba **10** como el valor para el atributo **calls** y **60** para el atributo **renewal-period**.

```xml
<rate-limit calls="10" renewal-period="60">
</rate-limit>
```

Para configurar la directiva **Establecer la cuota de uso por suscripción**, sitúe el cursor inmediatamente debajo del elemento **rate-limit** recién agregado dentro del elemento **inbound** y haga clic en la flecha situada a la izquierda de **Establecer la cuota de uso por suscripción**.

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
<api name="name" calls="number" bandwidth="kilobytes">
<operation name="name" calls="number" bandwidth="kilobytes" />
</api>
</quota>
```

Al igual que la directiva **Limitar la frecuencia de llamadas por suscripción**, la directiva **Establecer la cuota de uso por suscripción** permite configurar los límites de las operaciones y las API del producto. En este tutorial no se usará esa funcionalidad, así que elimine los elementos **api** y **operation** del elemento **quota**, como se muestra en el ejemplo siguiente.

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
</quota>
```

Las cuotas se pueden basar en el número de llamadas por intervalo, ancho de banda o ambos. En este tutorial no estamos fijando límites en función del ancho de banda, por lo que puede eliminar el atributo **bandwidth** .

```xml
<quota calls="number" renewal-period="seconds">
</quota>
```

En el producto Prueba gratuita, la cuota es de 200 llamadas por semana. Especifique **200** como el valor del atributo **calls** y **604800** como el valor de **renewal-period**.

```xml
<quota calls="200" renewal-period="604800">
</quota>
```

> Los intervalos de directiva se especifican en segundos. Para calcular el intervalo para una semana, puede multiplicar el número de días (7) por el número de horas de un día (24) por el número de minutos de una hora (60) por el número de segundos de un minuto (60): 7 * 24 * 60 * 60 = 604800.
> 
> 

Cuando haya terminado de configurar la directiva, debe coincidir con el siguiente ejemplo.

```xml
<policies>
    <inbound>
        <rate-limit calls="10" renewal-period="60">
        </rate-limit>
        <quota calls="200" renewal-period="604800">
        </quota>
        <base />

</inbound>
<outbound>

    <base />

    </outbound>
</policies>
```

Una vez configuradas las directivas deseadas, haga clic en **Guardar**.

![Save policy][api-management-policy-save]

## <a name="publish-product"></a> Para publicar el producto
Ahora que se agregaron las API y se configuraron las directivas, el producto debe publicarse para que los desarrolladores puedan usarlo. Haga clic en **Productos** en el menú **API Management** a la izquierda y luego haga clic en **Evaluación gratuita** para configurar el producto.

![Configure product][api-management-configure-product]

Haga clic en **Publicar** y luego en **Sí, publicarlo** para confirmar la operación.

![Publish product][api-management-publish-product]

## <a name="subscribe-account"></a>Suscripción de una cuenta de desarrollador al producto
Ahora que el producto se ha publicado, estará disponible para suscribirse a él y que los desarrolladores lo usen.

> Los administradores de una instancia de API Management se suscriben automáticamente a cada producto. En este paso del tutorial suscribiremos una de las cuentas de desarrollador que no es de administrador al producto Prueba gratuita. Si la cuenta de desarrollador es parte del rol Administradores, puede seguir con este paso aunque esté suscrito.
> 
> 

Haga clic en **Usuarios** en el menú **API Management** a la izquierda y haga clic en el nombre de su cuenta de desarrollador. En este ejemplo usamos la cuenta del desarrollador **Clayton Gragg** .

![Configure developer][api-management-configure-developer]

Haga clic en **Agregar suscripción**.

![Agregar suscripción][api-management-add-subscription-menu]

Seleccione **Evaluación gratuita** y, después, haga clic en **Suscribirse**.

![Agregar suscripción][api-management-add-subscription]

> [!NOTE]
> En este tutorial, no está habilitada la posibilidad de varias suscripciones simultáneas para el producto de evaluación gratuita. Si lo estuvieran, se le pediría que pusiera un nombre a la suscripción, tal como se muestra en el ejemplo siguiente.
> 
> 

![Agregar suscripción][api-management-add-subscription-multiple]

Después de hacer clic **Suscribirse**, el producto aparece en la lista **Suscripción** del usuario.

![Subscription added][api-management-subscription-added]

## <a name="test-rate-limit"></a>Para llamar a una operación y prueba del límite de frecuencia
Ahora que el producto Prueba gratuita está configurado y publicado, podemos llamar a algunas operaciones y probar la directiva de límite de tasa.
Haga clic en **Portal para desarrolladores** en el menú superior derecho para cambiar al portal para desarrolladores.

![portal para desarrolladores][api-management-developer-portal-menu]

Haga clic en **API** en el menú superior y, después, en **Echo API** (API Eco).

![portal para desarrolladores][api-management-developer-portal-api-menu]

Haga clic en **GET Resource** (Recurso GET) y, después, en **Pruébelo**.

![Open console][api-management-open-console]

Mantenga los valores predeterminados de los parámetros y seleccione la clave de suscripción para el producto de evaluación gratuita.

![Subscription key][api-management-select-key]

> [!NOTE]
> Si tiene varias suscripciones, asegúrese de seleccionar la clave para **Prueba gratuita**ya que, de lo contrario, las directivas configuradas en los pasos anteriores no entrarán en vigor.
> 
> 

Haga clic en **Enviar**y vea la respuesta. Observe el **Estado de respuesta** de **200 OK**.

![Operation results][api-management-http-get-results]

Haga clic en **Enviar** en una frecuencia mayor que la directiva del límite de frecuencia de 10 llamadas por minuto. Una vez superada la directiva del límite de frecuencia, se devolverá un estado de respuesta de **429 Too many Requests** .

![Operation results][api-management-http-get-429]

El valor de **Contenido de respuesta** indica el intervalo restante antes de que los reintentos sean correctos.

Cuando la directiva de límite de tasa de 10 llamadas por minuto se aplique, las llamadas posteriores no se podrán realizar hasta que transcurran 60 segundos desde la primera de las 10 llamadas correctas al producto antes de que se superara el límite de tasa. En este ejemplo, el intervalo restante es 54 segundos.

## <a name="next-steps"></a>Pasos siguientes
* Vea una demostración de la configuración de cuotas y límites de frecuencia en el siguiente vídeo.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Manage your first API in Azure API Management]: api-management-get-started.md
[How to create and use groups in Azure API Management]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configure call rate limit and quota policies]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota
