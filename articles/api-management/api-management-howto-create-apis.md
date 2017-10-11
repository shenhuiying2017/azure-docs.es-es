---
title: "Creación de API en Administración de API de Azure"
description: "Obtenga información acerca de cómo crear y configurar las API en Administración de API de Azure."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 14c20da4-f29f-4b28-bec7-3d4c50b734da
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: ab08256fbc3caca05bf23a12016ad2acf4fc7412
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-create-apis-in-azure-api-management"></a>Creación de API en Administración de API de Azure
En Administración de API, una API representa un conjunto de operaciones que puede invocarse por las aplicaciones cliente. Se crean nuevas API en el portal del publicador y luego se agregan las operaciones deseadas. Una vez agregadas las operaciones, la API se agrega a un producto y se puede publicar. Una vez publicada una API, pueden usarla y suscribirse a ella los desarrolladores.

Esta guía muestra el primer paso del proceso: cómo crear y configurar una nueva API en Administración de API. Para obtener más información sobre cómo agregar operaciones y publicar un producto, consulte [Incorporación de operaciones a una API][How to add operations to an API] y [Creación y publicación de un producto][How to create and publish a product].

## <a name="create-new-api"> </a>Creación de una API
Las API se crean y se configuran en el portal del publicador. Para obtener acceso al portal del publicador, haga clic en el **portal del publicador** en Azure Portal para el servicio API Management.

![Portal del publicador][api-management-management-console]

> Si aún no ha creado ninguna instancia del servicio de API Management, consulte [Creación de una instancia del servicio API Management][Create an API Management service instance] en el tutorial [Introducción a Azure API Management][Get started with Azure API Management].
> 
> 

Haga clic en **API** en el menú **API Management** de la izquierda y haga clic en **Agregar API**.

![Create API][api-management-create-api]

Use la ventana **Agregar nueva API** para configurar la nueva API.

![Agregar nueva API][api-management-add-new-api]

Para configurar la nueva API se usan los campos siguientes.

* **nombre de la API web** ofrece un nombre único y descriptivo para la API. Se muestra en el portal para desarrolladores y en el portal del publicador.
* **URL de servicio Web** hace referencia al servicio HTTP que implementa la API. Administración de API envía las solicitudes a esta dirección.
* **Sufijo de dirección URL API web** se anexa a la dirección URL base para el servicio Administración de API. La dirección URL base es común para todas las API hospedadas en una instancia del servicio Administración de API. Administración de API distingue las API por su sufijo, por lo que el sufijo debe ser único para cada API de un publicador determinado.
* **Esquema de URL de la API web** determina qué protocolos se pueden usar para tener acceso a la API. HTTPs es el protocolo especificado de forma predeterminada.
* Para agregar opcionalmente esta nueva API a un producto, haga clic en el desplegable **Productos (opcional)** y elija un producto. Este paso se puede repetir varias veces para agregar la API a varios productos.

Una vez configurados los valores que se desean, haga clic en **Guardar**. Una vez creada la nueva API, su página de resumen se mostrará en el portal para editores.

![API summary][api-management-api-summary]

## <a name="configure-api-settings"> </a>Definición de la configuración de la API
Puede usar la pestaña **Configuración** para comprobar y editar la configuración de una API. **Nombre de la API web**, **URL de servicio web** y **Sufijo de URL de la API web** se establecen inicialmente al crear la API y se pueden modificar aquí. **Descripción** proporciona una descripción opcional y **Esquema de URL de la API web** determina qué protocolos se pueden usar para tener acceso a la API.

![API settings][api-management-api-settings]

Para configurar la autenticación de puerta de enlace para el servicio back-end que implementa la API, seleccione la pestaña **Seguridad** . El menú desplegable **Con credenciales** se puede usar para configurar la **HTTP básica** o **Certificados de cliente**. Para usar la autenticación básica HTTP, solo tiene que escribir las credenciales que quiera. Para obtener información sobre el uso de la autenticación de certificados de cliente, consulte [Cómo asegurar servicios back-end con la autenticación de certificados de cliente en Azure API Management][How to secure back-end services using client certificate authentication in Azure API Management].

La pestaña **Seguridad** también se puede usar para configurar la **Autorización de usuario** con OAuth 2.0. Para obtener más información, consulte [Procedimiento para autorizar a las cuentas de desarrollador para que usen OAuth 2.0 en Azure API Management][How to authorize developer accounts using OAuth 2.0 in Azure API Management].

![Basic authentication settings][api-management-api-settings-credentials]

Haga clic en **Guardar** para guardar los cambios que efectúe en la configuración de la API.

## <a name="next-steps"> </a>Pasos siguientes
Una vez creada una API y definida la configuración, los pasos siguientes permiten agregar las operaciones a la API, agregar la API a un producto y publicarlo para ponerlo a disposición de los desarrolladores. Para obtener más información, consulte los siguientes artículos.

* [Incorporación de operaciones a una API][How to add operations to an API]
* [Creación y publicación de un producto][How to create and publish a product]

[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[How to secure back-end services using client certificate authentication in Azure API Management]: api-management-howto-mutual-certificates.md
[How to authorize developer accounts using OAuth 2.0 in Azure API Management]: api-management-howto-oauth2.md
