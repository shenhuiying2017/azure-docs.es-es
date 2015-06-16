<properties 
	pageTitle="Creación y publicación de un producto en Administración de API de Azure" 
	description="Obtenga información acerca de cómo crear y publicar productos en Administración de API de Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Creación y publicación de un producto en Administración de API de Azure

En Administración de API (vista previa) de Azure, un producto contiene una o más API, así como una cuota de uso y los términos de uso. Una vez publicado un producto, los desarrolladores pueden suscribirse al producto y comenzar a usar las API del producto. Este tema ofrece una guía para crear un producto, agregarle una API y publicarlo para los desarrolladores.

## En este tema

-   [Creación de un producto][Creación de un producto]
-   [Incorporación de una API a un producto][Incorporación de una API a un producto]
-   [Incorporación de información descriptiva a un producto][Incorporación de información descriptiva a un producto]
-   [Publicación de un producto][Publicación de un producto]
-   [Visibilidad de un producto para los desarrolladores][Visibilidad de un producto para los desarrolladores]
-   [Vista de los suscriptores de un producto][Vista de los suscriptores de un producto]
-   [Pasos siguientes][Pasos siguientes]

## <a name="create-product"> </a>Creación de un producto

En primer lugar, se agregan operaciones y se configuran para una API en la consola de administración. Para obtener acceso a la consola de administración, haga clic en **Consola de administración** en el Portal de Azure para su servicio Administración de API.

> Si todavía no ha creado una instancia de servicio Administración de API, consulte [Creación de una instancia de Administración de API][Creación de una instancia de Administración de API] en el tutorial [Introducción a la Administración de API de Azure][Introducción a la Administración de API de Azure].

![API Management console][API Management console]

Haga clic en **Productos** en el menú de la izquierda para mostrar la página **Productos** y haga clic en **Agregar producto**.

![Productos][Productos]

![New product][New product]

Escriba un nombre descriptivo para el producto en el campo **Nombre** y una descripción del producto en el campo **Descripción**.

Active **Requerir aprobación de suscripción** si desea que un administrador revise y acepte o rechace los intentos de suscripción a este producto. Si la casilla no se activa, los intentos de suscripción se autoaprobarán. Para obtener más información sobre suscripciones, consulte [Vista de los suscriptores de un producto][Vista de los suscriptores de un producto].

![Productos][1]

> De forma predeterminada, los nuevos productos no se publican y solo son visibles para el grupo **Administradores**.

Para configurar un producto, haga clic en el nombre del producto en la pestaña **Productos**.

## <a name="add-apis"> </a>Incorporación de una API a un producto

La página **Productos** contiene cuatro vínculos de configuración: **Resumen**, **Configuración**, **Visibilidad** y **Desarrolladores**. En la pestaña **Resumen** puede agregar API y publicar o anular la publicación de un producto.

![Resumen][Resumen]

Antes de publicar el producto, debe agregar una o más API. Para ello, haga clic en **Agregar API al producto**.

![Add APIs][Add APIs]

Seleccione las API que desee y haga clic en **Guardar**.

## <a name="add-description"> </a>Incorporación de información descriptiva a un producto

La pestaña **Configuración** permite proporcionar información detallada sobre el producto como, por ejemplo, su finalidad, las API a las que ofrece acceso y otra información útil. El contenido se dirige a los desarrolladores que llamarán a la API y pueden escribirse en texto sin formato o marcado HTML.

![Product settings][Product settings]

Seleccione **Requerir aprobación de suscripción** si desea aprobar manualmente todas las solicitudes de suscripción de productos. De forma predeterminada, todas las suscripciones de productos se conceden automáticamente.

Opcionalmente, rellene el campo **Términos de uso** que describe los términos de uso del producto que los suscriptores deben aceptar para usar el producto.

## <a name="publish-product"> </a>Publicación de un producto

Para poder llamar a las API de un producto, este debe publicarse. En la pestaña **Resumen** correspondiente al producto, haga clic en **Publicar** y luego en **Sí, publicarlo** para confirmarlo. Para convertir en privado un producto previamente publicado, haga clic en **Anular publicación**.

![Publish product][Publish product]

## <a name="make-visible"> </a>Visibilidad de un producto para los desarrolladores

La pestaña **Visibilidad** permite elegir los roles que pueden ver el producto en el portal para desarrolladores y suscribirse al producto.

![Product visibility][Product visibility]

Para habilitar o deshabilitar la visibilidad de un producto para los desarrolladores de un grupo, active o desactive la casilla situada junto al grupo y haga clic en **Guardar**.

> Para obtener más información, consulte [Creación y uso de grupos para administrar cuentas de desarrollador en Administración de API de Azure][Creación y uso de grupos para administrar cuentas de desarrollador en Administración de API de Azure].

## <a name="view-subscribers"> </a>Vista de los suscriptores de un producto

La pestaña **Desarrolladores** muestra la lista de desarrolladores que se han suscrito al producto. Los detalles y la configuración de cada desarrollador se pueden ver haciendo clic en el nombre del desarrollador. En este ejemplo, ningún desarrollador se ha suscrito todavía al producto.

![Desarrolladores][Desarrolladores]

## <a name="next-steps"> </a>Pasos siguientes

Una vez agregadas las API que se deseen y publicado el producto, los desarrolladores pueden suscribirse al producto y comenzar a llamar a las API. Para obtener un tutorial que demuestre estos elementos, además de la configuración avanzada del producto, consulte [Creación y definición de configuraciones de productos avanzadas en Administración de API de Azure][Creación y definición de configuraciones de productos avanzadas en Administración de API de Azure].

  [Creación de un producto]: #create-product
  [Incorporación de una API a un producto]: #add-apis
  [Incorporación de información descriptiva a un producto]: #add-description
  [Publicación de un producto]: #publish-product
  [Visibilidad de un producto para los desarrolladores]: #make-visible
  [Vista de los suscriptores de un producto]: #view-subscribers
  [Pasos siguientes]: #next-steps
  [Creación de una instancia de Administración de API]: ../api-management-get-started/#create-service-instance
  [Introducción a la Administración de API de Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-add-products/api-management-management-console.png
  [Productos]: ./media/api-management-howto-add-products/api-management-products.png
  [New product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
  [1]: ./media/api-management-howto-add-products/api-management-products-page.png
  [Resumen]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
  [Add APIs]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
  [Product settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
  [Publish product]: ./media/api-management-howto-add-products/api-management-publish-product.png
  [Product visibility]: ./media/api-management-howto-add-products/api-management-product-visibility.png
  [Creación y uso de grupos para administrar cuentas de desarrollador en Administración de API de Azure]: ../api-management-howto-create-groups
  [Desarrolladores]: ./media/api-management-howto-add-products/api-management-developer-list.png
  [Creación y definición de configuraciones de productos avanzadas en Administración de API de Azure]: ../api-management-howto-product-with-rules

<!--HONumber=46--> 

<!--HONumber=46--> 
 