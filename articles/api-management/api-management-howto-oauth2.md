---
title: "Autorización de las cuentas de desarrollador mediante OAuth 2.0 en Azure API Management | Microsoft Docs"
description: "Obtenga información acerca de cómo autorizar a los usuarios para que utilicen OAuth 2.0 en Administración de API."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 78c48247-64f0-4708-b2d0-98b61a821283
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: a19c453bb3271374b587f3d0b35adad55863b490
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Procedimiento para autorizar a las cuentas de desarrollador para que usen OAuth 2.0 en Administración de API de Azure
Muchas API admiten [OAuth 2.0](http://oauth.net/2/) para proteger la API y garantizar que solo usuarios válidos obtengan acceso y que, además, solo puedan tener acceso a los recursos para los que estén autorizados. Para usar la consola interactiva para desarrolladores de la Administración de API de Azure, el servicio permite configurar la instancia del servicio para que funcione con la API habilitada para OAuth 2.0.

## <a name="prerequisites"> </a>Requisitos previos
En esta guía se explica cómo configurar la instancia del servicio Administración de API para que use la autorización OAuth 2.0 con las cuentas de desarrollador, pero no se explica cómo configurar un proveedor de OAuth 2.0. Aunque los proveedores de OAuth 2.0 tienen configuraciones diferentes, los pasos son similares y se precisa la misma información para configurar OAuth 2.0 en la instancia del servicio Administración de API. Este tema muestra ejemplos donde Azure Active Directory actúa como proveedor de OAuth 2.0.

> [!NOTE]
> Para obtener más información sobre cómo configurar OAuth 2.0 con Azure Active Directory, consulte el ejemplo de [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet].
> 
> 

## <a name="step1"> </a>Configurar un servidor de autorización OAuth 2.0 en Administración de API
Para comenzar, haga clic en **Portal para editores** en Azure Portal para el servicio API Management.

![Portal del publicador][api-management-management-console]

> [!NOTE]
> Si aún no ha creado ninguna instancia del servicio de API Management, consulte [Creación de una instancia del servicio API Management][Create an API Management service instance] en el tutorial [Introducción a Azure API Management][Get started with Azure API Management].
> 
> 

Haga clic en **Seguridad** en el menú **API Management** de la izquierda, haga clic en **OAuth 2.0** y, después, en **Agregar servidor de autorización**.

![OAuth 2.0][api-management-oauth2]

Al hacer clic en **Agregar servidor de autorización**, se muestra el formulario para un nuevo servidor de autorización.

![Nuevo servidor][api-management-oauth2-server-1]

Escriba un nombre y una descripción opcional en los campos **Nombre** y **Descripción**. 

> [!NOTE]
> Estos campos sirven para identificar el servidor de autorización OAuth 2.0 en la instancia del servicio Administración de API y los valores que contienen no proceden del servidor OAuth 2.0.
> 
> 

Escriba la **URL de la página de registro de cliente**. En esta página, los usuarios pueden crear y administrar sus cuentas. Puede variar en función del proveedor de OAuth 2.0. La **URL de la página de registro de cliente** señala a la página que los usuarios pueden utilizar para crear y configurar sus propias cuentas para proveedores de OAuth 2.0 que admiten la administración de usuarios de las cuentas. Algunas organizaciones no configuran ni usan esta funcionalidad, aunque la admita el proveedor de OAuth 2.0. Si el proveedor de OAuth 2.0 no tiene configurada la administración de usuarios de las cuentas, especifique aquí una URL de marcador de posición, por ejemplo, la URL de su empresa o una URL como `https://placeholder.contoso.com`.

La sección siguiente del formulario contiene la configuración de los **Tipos de concesión de código de autorización**, la **URL del punto de conexión de autorización** y el **Método de solicitud de autorización**.

![Nuevo servidor][api-management-oauth2-server-2]

Active las casillas de los **Tipos de concesión de código de autorización** que desee. **Código de autorización** se especifica de forma predeterminada.

Escriba la **URL del extremo de autorización**. En Azure Active Directory, esta URL será similar a la siguiente, donde se reemplaza `<client_id>` por el identificador de cliente que identifica la aplicación en el servidor OAuth 2.0.

`https://login.microsoftonline.com/<client_id>/oauth2/authorize`

El **Método de solicitud de autorización** especifica cómo se envía la solicitud de autorización al servidor OAuth 2.0. El valor predeterminado es **GET** .

En la sección siguiente, se especifican la **URL del punto de conexión de token**, los **Métodos de autenticación de cliente**, el **Método de envío de tokens de acceso** y el **Ámbito predeterminado**.

![Nuevo servidor][api-management-oauth2-server-3]

En un servidor OAuth 2.0 de Azure Active Directory, la **URL del punto de conexión de token** tendrá el formato siguiente, donde `<APPID>` tiene el formato de `yourapp.onmicrosoft.com`.

`https://login.microsoftonline.com/<APPID>/oauth2/token`

Los valores predeterminados para **Métodos de autenticación de cliente** y **Método de envío de tokens de acceso** son **Básico** y **Encabezado de autorización** respectivamente. Estos valores se configuran en esta sección del formulario, junto con el **Ámbito predeterminado**.

La sección **Credenciales de cliente** contiene el **Id. de cliente** y el **Secreto del cliente** que se obtienen durante los procesos de creación y configuración del servidor OAuth 2.0. Al especificar el **Id. de cliente** y el **Secreto del cliente**, se genera el **uri de redirección** para el **código de autorización**. Con este URI se configura la URL de respuesta en la configuración del servidor OAuth 2.0.

![Nuevo servidor][api-management-oauth2-server-4]

Si **Tipos de concesión de código de autorización** se establece en **Contraseña de propietario de recursos**, las credenciales se especifican con la sección **Credenciales de contraseña de propietario de recursos**. De no ser así, puede dejarse en blanco.

![Nuevo servidor][api-management-oauth2-server-5]

Cuando complete el formulario, haga clic en **Guardar** para guardar la configuración del servidor de autorización OAuth 2.0 de Administración de API. Cuando se guarda la configuración del servidor, pueden configurarse las API para que usen estos valores, conforme a la sección siguiente.

## <a name="step2"> </a>Configurar una API para que use la autorización de usuario OAuth 2.0
Haga clic en **API** en el menú **API Management** de la izquierda, en el nombre de la API en cuestión, en **Seguridad** y, después, active la casilla **OAuth 2.0**.

![Autorización de usuario][api-management-user-authorization]

Seleccione el **Servidor de autorización** que quiera en la lista desplegable y haga clic en **Guardar**.

![Autorización de usuario][api-management-user-authorization-save]

## <a name="step3"> </a>Probar la autorización de usuario OAuth 2.0 en el portal para desarrolladores
Tras configurar el servidor de autorización OAuth 2.0 y las API para que usen dicho servidor, puede probarlo. Para ello, vaya al portal para desarrolladores y llame a una API.  Haga clic en **Portal para desarrolladores** en el menú superior derecho.

![portal para desarrolladores][api-management-developer-portal-menu]

Haga clic en **API** en el menú superior y seleccione **API eco**.

![API eco][api-management-apis-echo-api]

> [!NOTE]
> Si solamente tiene una API configurada o visible en su cuenta, al hacer clic en API irá directamente a las operaciones de dicha API.
> 
> 

Seleccione la operación **Recurso GET**, haga clic en **Abrir consola** y seleccione **Código de autorización** en la lista desplegable.

![Open console][api-management-open-console]

Al seleccionar **Código de autorización** , se abre una ventana emergente con el formulario de suscripción del proveedor de OAuth 2.0. En este ejemplo, Azure Active Directory ha proporcionado el formulario de suscripción.

> [!NOTE]
> Si ha deshabilitado los elementos emergentes, el explorador le solicitará que los habilite. Cuando los habilite, seleccione **Código de autorización** de nuevo y se mostrará el formulario de suscripción.
> 
> 

![Iniciar sesión][api-management-oauth2-signin]

Tras iniciar sesión, los **Encabezados de solicitud** se rellenan con el encabezado `Authorization : Bearer` que autoriza la solicitud.

![Token de encabezado de solicitud][api-management-request-header-token]

Llegado a este punto, puede configurar los valores que desea para los demás parámetros y enviar la solicitud. 

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca del uso de OAuth 2.0 y Administración de API, vea el siguiente vídeo y el [artículo](api-management-howto-protect-backend-with-aad.md)que lo acompaña.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Protecting-Web-API-Backend-with-Azure-Active-Directory-and-API-Management/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

