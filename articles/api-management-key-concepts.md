<properties 
	pageTitle="Conceptos clave de Administración de API" 
	description="Obtenga información acerca de las API, los productos, los roles, los grupos y otros conceptos clave de Administración de API." 
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
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

#Conceptos clave de Administración de API

La Administración de API ayuda a las organizaciones a publicar API para desarrolladores externos, asociados e internos para liberar el potencial de sus datos y servicios. Todas y cada una de las empresas pretenden extender sus operaciones como una plataforma digital creando nuevos canales, buscando nuevos clientes y estrechando la relación con los existentes. Administración de API proporciona las capacidades esenciales para garantizar un programa API exitoso a través de compromisos con desarrolladores, conocimiento de los negocios, análisis, seguridad y protección.

Para usar Administración de API, los administradores crean API. Cada API consta de una o varias operaciones y se puede agregar a uno o varios productos. Para usar una API, los desarrolladores se suscriben a un producto que contiene esa API y después pueden llamar a la operación de la API cumpliendo cualquier directiva de uso que pueda estar en vigor.

En este tema se proporciona información general de los conceptos clave de Administración de API.

## En este tema

-   [API y operaciones][]
-   [Productos][]
-   [Grupos][]
-   [Desarrolladores][]
-   [Directivas][]
-	[Portal para desarrolladores][]


## <a name="apis"> </a>API y operaciones

Las API son el fundamento de una instancia del servicio Administración de API. Cada API representa un conjunto de operaciones disponibles para los desarrolladores. Cada API contiene una referencia a un servicio back-end que implementa la API y sus operaciones se asignan a las operaciones implementadas por dicho servicio. Las operaciones de Administración de API son altamente configurables, con control sobre asignación de direcciones URL, parámetros de consulta y ruta de acceso, contenidos de solicitudes y respuestas y almacenamiento en caché de respuestas de operaciones. En la API o en el ámbito de operación individual, también se pueden implementar directivas de límite de tasa, cuotas y restricción de direcciones IP.

Para obtener más información, vea [Creación de API][] y [Adición de operaciones a una API][].


## <a name="products"> </a> Productos

Los productos son la forma de presentar las API a los desarrolladores. Los productos en Administración de API tienen una o varias API y se configuran con un título, una descripción y términos de uso. Cuando un producto está preparado para que lo usen los desarrolladores, se puede publicar. Una vez publicado, los desarrolladores pueden verlo y suscribirse a él. La aprobación de la suscripción se configura en el ámbito de producto y puede requerir la aprobación del administrador o aprobarse automáticamente.

Los grupos se usan para administrar la visibilidad de productos a los desarrolladores. Los productos conceden visibilidad a los grupos y los desarrolladores pueden ver los productos visibles a los grupos a los que pertenecen y suscribirse a ellos. 

Para obtener información, consulte [Creación y publicación de un producto][].

## <a name="groups"> </a> Grupos

Los grupos se usan para administrar la visibilidad de productos a los desarrolladores. Administración de API tiene los siguientes grupos integrados.

-	**Administradores**: los administradores gestionan instancias del servicio Administración de API, creando las API, las operaciones y los productos que usan los desarrolladores.
-	**Desarrolladores**: los desarrolladores son los clientes que crean aplicaciones usando sus API. Los desarrolladores, después de que se les concede acceso al [portal para desarrolladores][], crean aplicaciones que llaman a las operaciones de una API.
-	**Invitados**: a este grupo pertenecen los usuarios no autenticados, como por ejemplo clientes potenciales, que visitan el portal para desarrolladores de una instancia de Administración de API. Se les concede determinado acceso de solo lectura, como por ejemplo la posibilidad de ver API pero no llamarlas.

Además de estos grupos integrados, los administradores pueden crear grupos personalizados. Los grupos personalizados tienen los mismos privilegios que el grupo Desarrolladores integrado y se pueden usar para administrar varios grupos de desarrolladores. Por ejemplo, podría crear un grupo personalizado para desarrolladores que usarán las API de un producto y otro grupo para desarrolladores que usarán las API de otro producto.

Para obtener más información, consulte [Creación y uso de grupos][].

## <a name="developers"> </a> Desarrolladores

Los desarrolladores representan las cuentas de usuario de una instancia del servicio Administración de API. Los desarrolladores pueden ser creados por administradores o invitados por estos y también pueden suscribirse desde el [Portal para desarrolladores][]. Cada desarrollador es un miembro de uno o varios grupos y se puede suscribir a los productos que conceden visibilidad a dichos grupos.

Cuando los desarrolladores se suscriben a un producto, se les concede la clave principal y secundaria para dicho producto. Esta clave se usa cuando se realizan llamadas en las API del producto.

Para obtener más información, consulte [Creación de desarrolladores e invitación a los mismos][] y [Asociación de grupos a desarrolladores][].

## <a name="policies"> </a> Directivas

Las directivas son una poderosa funcionalidad de Administración de API que permite al publicador cambiar el comportamiento de la API a través de la configuración. Las directivas son una colección de declaraciones que se ejecutan secuencialmente en la solicitud o respuesta de una API. Entre las declaraciones más usadas se encuentran la conversión de formato de XML a JSON y la limitación de tasa de llamadas para restringir la cantidad de llamadas entrantes de un desarrollador, pero también hay muchas otras directivas disponibles.

Para obtener una lista completa de directivas de Administración de API, consulte [Referencia de directivas][]. Para obtener más información acerca del uso y la configuración de directivas, consulte [Directivas de Administración de API][]. Para ver un tutorial sobre la creación de un producto con directivas del límite de tasa y cuota, consulte [Creación y definición de configuraciones de productos avanzadas][].


## <a name="developer-portal"> </a> Portal para desarrolladores

El portal para desarrolladores es el lugar en el que los desarrolladores pueden aprender acerca de sus API, ver operaciones y llamarlas y suscribirse a productos. Los clientes potenciales pueden visitar el portal para desarrolladores, ver API y operaciones y suscribirse. La dirección URL del portal para desarrolladores se encuentra en el panel del Portal de Azure para su instancia del servicio Administración de API.

Puede personalizar el aspecto y apariencia del portal para desarrolladores agregando contenido personalizado, personalizando estilos e incorporando su toque diferenciador.

[API y operaciones]: #apis
[Productos]: #products
[Grupos]: #groups
[Desarrolladores]: #developers
[Directivas]: #policies
[Portal para desarrolladores]: #developer-portal

[Creación de API]: ../api-management-howto-create-apis
[Incorporación de operaciones a una API]: ../api-management-howto-add-operations
[Creación y publicación de un producto]: ../api-management-howto-add-products
[Creación y uso de grupos]: ../api-management-howto-create-groups
[Asociación de grupos a desarrolladores]: ../api-management-howto-create-groups/#associate-group-developer
[Creación y ajuste de una configuración de producto avanzada]: ../api-management-howto-product-with-rules
[Creación o invitación de desarrolladores]: ../api-management-howto-create-or-invite-developers
[Referencia de directiva]: ../api-management-policy-reference
[Directivas de administración de API]: ../api-management-howto-policies
[Creación de una instancia del servicio de Administración de API]: ../api-management-get-started/#create-service-instance




<!--HONumber=35.2-->

<!--HONumber=46--> 

<!--HONumber=46--> 
