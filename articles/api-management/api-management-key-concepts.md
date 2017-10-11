---
title: "Información general y conceptos clave de Azure API Management | Microsoft Docs"
description: "Obtenga información acerca de las API, los productos, los roles, los grupos y otros conceptos clave de Administración de API."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: e71da405-835a-48f3-956f-45c1a85698d7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 47358c6c209488d7a12e8afbf7a2d9b3f872f0de
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="what-is-api-management"></a>¿Qué es Administración de API?
La Administración de API ayuda a las organizaciones a publicar API para desarrolladores externos, asociados e internos para liberar el potencial de sus datos y servicios. Todas y cada una de las empresas pretenden extender sus operaciones como una plataforma digital creando nuevos canales, buscando nuevos clientes y estrechando la relación con los existentes. Administración de API proporciona las capacidades esenciales para garantizar un programa API exitoso a través de compromisos con desarrolladores, conocimiento de los negocios, análisis, seguridad y protección.

Vea el siguiente vídeo para más información sobre Administración de API de Azure y aprender a usarla para agregar características a su API, incluido el control de acceso, limitación de tasa, supervisión, registro de eventos y almacenamiento en caché de respuestas, con el mínimo esfuerzo por su parte.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-API-Management-Overview/player]
> 
> 

Para usar Administración de API, los administradores crean API. Cada API consta de una o varias operaciones y se puede agregar a uno o varios productos. Para usar una API, los desarrolladores se suscriben a un producto que contiene esa API y después pueden llamar a la operación de la API cumpliendo cualquier directiva de uso que pueda estar en vigor.

En este tema se proporciona información general de los conceptos clave de Administración de API.

> [!NOTE]
> Para obtener más información, consulte las notas del producto en PDF [Administración de API basadas en la nube: aprovechamiento de la capacidad de las API](http://j.mp/ms-apim-whitepaper) . Estas notas del producto introductorias sobre la administración de API por CITO Research cubren: 
> 
> * Desafíos y requisitos comunes de API
> * Desacoplamiento de API y presentación de fachadas
> * Puesta en marcha de los desarrolladores rápidamente
> * Protección de acceso
> * Análisis y métricas
> * Obtención de control e información con una plataforma de administración de API
> * Uso de soluciones de nube frente a locales
> * Administración de API de Azure
> 
> 

## <a name="apis"> </a>API y operaciones
Las API son el fundamento de una instancia del servicio Administración de API. Cada API representa un conjunto de operaciones disponibles para los desarrolladores. Cada API contiene una referencia a un servicio back-end que implementa la API y sus operaciones se asignan a las operaciones implementadas por dicho servicio. Las operaciones de Administración de API son altamente configurables, con control sobre asignación de direcciones URL, parámetros de consulta y ruta de acceso, contenidos de solicitudes y respuestas y almacenamiento en caché de respuestas de operaciones. En la API o en el ámbito de operación individual, también se pueden implementar directivas de límite de tasa, cuotas y restricción de direcciones IP.

Para más información, consulte [Creación de API][How to create APIs] e [Incorporación de operaciones a una API][How to add operations to an API].

## <a name="products"> </a> Productos
Los productos son la forma de presentar las API a los desarrolladores. Los productos en Administración de API tienen una o varias API y se configuran con un título, una descripción y términos de uso. Los productos pueden ser de tipo **Abierto** o **Protegido**. Para poder usar los productos protegidos es necesario suscribirse antes a ellos, mientras que los productos abiertos pueden usarse sin suscripción. Cuando un producto está preparado para que lo usen los desarrolladores, se puede publicar. Una vez publicado, los desarrolladores pueden verlo (y, en el caso de los productos protegidos, suscribirse a él). La aprobación de la suscripción se configura en el ámbito de producto y puede requerir la aprobación del administrador o aprobarse automáticamente.

Los grupos se usan para administrar la visibilidad de productos a los desarrolladores. Los productos conceden visibilidad a los grupos y los desarrolladores pueden ver los productos visibles a los grupos a los que pertenecen y suscribirse a ellos. 

Para más información, consulte [Creación y publicación de un producto][How to create and publish a product] y el siguiente vídeo.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

## <a name="groups"> </a> Grupos
Los grupos se usan para administrar la visibilidad de productos a los desarrolladores. Administración de API tiene los siguientes grupos invariables del sistema.

* **Administradores** : los administradores de la suscripción de Azure son miembros de este grupo. Los administradores controlan las instancias del servicio Administración de API y crean las API, las operaciones y los productos que usan los desarrolladores.
* **Desarrolladores** : los usuarios del portal para desarrolladores autenticados se incluyen en este grupo. Los desarrolladores son los clientes que compilan aplicaciones con sus API. Los desarrolladores, después de que se les concede acceso al portal para desarrolladores, crean aplicaciones que llaman a las operaciones de una API.
* **Invitados** : a este grupo pertenecen los usuarios del portal para desarrolladores no autenticados como, por ejemplo, clientes potenciales que visitan el portal para desarrolladores de una instancia de Administración de API. Se les concede determinado acceso de solo lectura, como por ejemplo la posibilidad de ver API pero no llamarlas.

Además de estos grupos del sistema, los administradores pueden crear grupos personalizados o [aprovechar los grupos externos en inquilinos de Azure Active Directory asociados](api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group). Los grupos personalizados y externos pueden usarse junto con grupos del sistema en la concesión a los desarrolladores de visibilidad y acceso a productos de la API. Por ejemplo, podría crear un grupo personalizado para los desarrolladores afiliados a una organización asociada específica y permitirles el acceso a las API a partir de un producto que contenga solo las API relevantes. Un usuario puede ser miembro de más de un grupo.

Para más información, consulte [Creación y uso de grupos][How to create and use groups].

## <a name="developers"> </a> Desarrolladores
Los desarrolladores representan las cuentas de usuario de una instancia del servicio Administración de API. Los desarrolladores pueden ser creados por administradores o invitados por estos y también pueden suscribirse desde el [Portal para desarrolladores][Developer portal]. Cada desarrollador es un miembro de uno o varios grupos y se puede suscribir a los productos que conceden visibilidad a dichos grupos.

Cuando los desarrolladores se suscriben a un producto, se les concede la clave principal y secundaria para dicho producto. Esta clave se usa cuando se realizan llamadas en las API del producto.

Para más información, consulte [Creación o invitación de desarrolladores][How to create or invite developers] y [Asociación de grupos a desarrolladores][How to associate groups with developers].

## <a name="policies"> </a> Directivas
Las directivas son una poderosa funcionalidad de Administración de API que permite al publicador cambiar el comportamiento de la API a través de la configuración. Las directivas son una colección de declaraciones que se ejecutan secuencialmente en la solicitud o respuesta de una API. Entre las declaraciones más usadas se encuentran la conversión de formato de XML a JSON y la limitación de tasa de llamadas para restringir la cantidad de llamadas entrantes de un desarrollador, pero también hay muchas otras directivas disponibles.

Las expresiones de directiva pueden utilizarse como valores de atributos o valores de texto en cualquiera de las directivas de Administración de API, a menos que la directiva especifique lo contrario. Algunas directivas como [Flujo de control](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose) y [Establecer variable](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable) se basan en expresiones de directiva. Para más información, consulte [Directivas avanzadas](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies), [Expresiones de directiva](https://msdn.microsoft.com/library/azure/dn910913.aspx) y el vídeo siguiente.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

Para obtener una lista completa de las directivas de API Management, consulte [Referencia de la directiva][Policy reference]. Para más información acerca del uso y la configuración de directivas, consulte [Directivas de API Management][API Management policies]. Para ver un tutorial sobre la creación de un producto con directivas de cuota y límite de tasas, consulte [Creación y definición de configuraciones de productos avanzadas][How create and configure advanced product settings]. Para ver una demostración, vea el siguiente vídeo.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

## <a name="developer-portal"> </a> Portal para desarrolladores
El portal para desarrolladores es el lugar en el que los desarrolladores pueden aprender acerca de sus API, ver operaciones y llamarlas y suscribirse a productos. Los clientes potenciales pueden visitar el portal para desarrolladores, ver API y operaciones y suscribirse. La dirección URL del portal para desarrolladores se encuentra en el panel del Portal de Azure clásico para su instancia del servicio Administración de API.

Puede personalizar el aspecto y apariencia del portal para desarrolladores agregando contenido personalizado, personalizando estilos e incorporando su toque diferenciador.

## <a name="api-management-and-the-api-economy"></a>Administración de API y la economía de la API
Para más información acerca de Administración de API, vea la siguiente presentación de la conferencia Microsoft Ignite 2015.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3708/player]
> 
> 

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Developer portal]: #developer-portal

[How to create APIs]: api-management-howto-create-apis.md
[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[How create and configure advanced product settings]: api-management-howto-product-with-rules.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: api-management-policy-reference.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance




