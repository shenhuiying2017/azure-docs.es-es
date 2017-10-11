---
title: "Creación y publicación de un producto en Administración de API de Azure"
description: "Obtenga información acerca de cómo crear y publicar productos en Administración de API de Azure."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 31de55cb-9384-490b-a2f2-6dfcf83da764
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 73bf4451ba1b71807e22440beecc73a7e8045c5e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>Creación y publicación de un producto en Administración de API de Azure
En Administración de API de Azure, un producto contiene una o varias API, así como una cuota de uso y los términos de uso. Una vez publicado un producto, los desarrolladores pueden suscribirse al producto y comenzar a usar las API del producto. Este tema ofrece una guía para crear un producto, agregarle una API y publicarlo para los desarrolladores.

## <a name="create-product"> </a>Creación de un producto
Las operaciones se agregan y se configuran para una API en el portal del publicador. Para obtener acceso al portal del publicador, haga clic en el **portal del publicador** en Azure Portal para el servicio API Management.

![Portal del publicador][api-management-management-console]

> Si aún no ha creado ninguna instancia del servicio de API Management, consulte [Creación de una instancia del servicio API Management][Create an API Management service instance] en el tutorial [Introducción a Azure API Management][Get started with Azure API Management].
> 
> 

Haga clic en **Productos** en el menú de la izquierda para mostrar la página **Productos** y haga clic en **Agregar producto**.

![Productos][api-management-products]

![New product][api-management-add-new-product]

Escriba un nombre descriptivo para el producto en el campo **Nombre** y una descripción del producto en el campo **Descripción**.

Los productos de API Management pueden tener el estado **Abierto** o **Protegido**. Para poder usar los productos protegidos es necesario suscribirse antes a ellos, mientras que los productos abiertos pueden usarse sin suscripción. Active la opción **Requerir suscripción** para crear un producto protegido que requiera una suscripción. Esta es la configuración predeterminada.

Active **Requerir aprobación de suscripción** si desea que un administrador revise y acepte o rechace los intentos de suscripción a este producto. Si la casilla no se activa, los intentos de suscripción se autoaprobarán. Para obtener más información sobre suscripciones, consulte [Vista de los suscriptores de un producto][View subscribers to a product].

Para permitir que las cuentas de desarrollador se suscriban varias veces al producto, active la casilla **Permitir varias suscripciones** . Si esta casilla no está activada, cada cuenta de desarrollador puede suscribirse una sola vez al producto.

![Suscripciones múltiples ilimitadas][api-management-unlimited-multiple-subscriptions]

Para limitar el número de suscripciones múltiples simultáneas, active la casilla **Limitar el número de suscripciones simultáneas a** y escriba el límite de suscripción. En el ejemplo siguiente, las suscripciones simultáneas se limitan a cuatro por cada cuenta de desarrollador.

![Para varias suscripciones][api-management-four-multiple-subscriptions]

Una vez configuradas todas las opciones del nuevo producto, haga clic en **Guardar** para crear el nuevo producto.

![Productos][api-management-products-page]

> De forma predeterminada, los nuevos productos no se publican y solo son visibles para el grupo **Administradores**.
> 
> 

Para configurar un producto, haga clic en el nombre del producto en la pestaña **Productos** .

## <a name="add-apis"> </a>Incorporación de API a un producto
La página **Productos** contiene cuatro vínculos de configuración: **Resumen**, **Configuración**, **Visibilidad** y **Suscriptores**. En la pestaña **Resumen** puede agregar API y publicar o anular la publicación de un producto.

![Resumen][api-management-new-product-summary]

Antes de publicar el producto, debe agregar una o más API. Para ello, haga clic en **Agregar API al producto**.

![Add APIs][api-management-add-apis-to-product]

Seleccione las API que desee y haga clic en **Guardar**.

## <a name="add-description"> </a>Incorporación de información descriptiva a un producto
La pestaña **Configuración** permite proporcionar información detallada sobre el producto como, por ejemplo, su finalidad, las API a las que ofrece acceso y otra información útil. El contenido se dirige a los desarrolladores que llamarán a la API y pueden escribirse en texto sin formato o marcado HTML.

![Product settings][api-management-product-settings]

Active **Requerir suscripción** para crear un producto protegido que requiera una suscripción para usarse o, desactive la casilla de verificación para crear un producto abierto al que se pueda llamar sin una suscripción.

Seleccione **Requerir aprobación de suscripción** si desea aprobar manualmente todas las solicitudes de suscripción de productos. De forma predeterminada, todas las suscripciones de productos se conceden automáticamente.

Para permitir que las cuentas de desarrollador se suscriban varias veces al nuevo producto, active la casilla **Permitir varias suscripciones** y, opcionalmente, especifique un límite. Si esta casilla no está activada, cada cuenta de desarrollador puede suscribirse una sola vez al producto.

Opcionalmente, rellene el campo **Términos de uso** que describe los términos de uso del producto que los suscriptores deben aceptar para usar el producto.

## <a name="publish-product"> </a>Publicación de un producto
Para poder llamar a las API de un producto, este debe publicarse. En la pestaña **Resumen** correspondiente al producto, haga clic en **Publicar**, y luego en **Sí, publicarlo** para confirmar. Para convertir en privado un producto previamente publicado, haga clic en **Anular publicación**.

![Publish product][api-management-publish-product]

## <a name="make-visible"> </a>Visibilidad de un producto para los desarrolladores
La pestaña **Visibilidad** permite elegir los roles que pueden ver el producto en el portal para desarrolladores y suscribirse al producto.

![Product visibility][api-management-product-visiblity]

Para habilitar o deshabilitar la visibilidad de un producto para los desarrolladores de un grupo, active o desactive la casilla situada junto al grupo y haga clic en **Guardar**.

> Para obtener más información, consulte [Creación y uso de grupos para administrar cuentas de desarrollador en Azure API Management][How to create and use groups to manage developer accounts in Azure API Management].
> 
> 

## <a name="view-subscribers"> </a>Vista de los suscriptores de un producto
La pestaña **Suscriptores** muestra la lista de desarrolladores que se han suscrito al producto. Los detalles y la configuración de cada desarrollador se pueden ver haciendo clic en el nombre del desarrollador. En este ejemplo, ningún desarrollador se ha suscrito todavía al producto.

![Desarrolladores][api-management-developer-list]

## <a name="next-steps"> </a>Pasos siguientes
Una vez agregadas las API que se deseen y publicado el producto, los desarrolladores pueden suscribirse al producto y comenzar a llamar a las API. Para obtener un tutorial que demuestre estos elementos, además de la configuración avanzada del producto, consulte el artículo que versa sobre la [creación y definición de configuraciones de productos avanzadas en Azure API Management][How create and configure advanced product settings in Azure API Management].

Para obtener más información acerca de cómo trabajar con los productos, vea el siguiente vídeo.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[How to create and use groups to manage developer accounts in Azure API Management]: api-management-howto-create-groups.md
[How create and configure advanced product settings in Azure API Management]: api-management-howto-product-with-rules.md 
