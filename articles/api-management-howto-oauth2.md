<properties pageTitle="How to authorize developer accounts using OAuth 2.0 in Azure API Management" metaKeywords="" description="Learn how to authorize users using OAuth 2.0 in API Management." metaCanonical="" services="api-management" documentationCenter="API Management" title="How to authorize developer accounts using OAuth 2.0 in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Procedimiento para autorizar a las cuentas de desarrollador para que usen OAuth 2.0 en Administración de API de Azure

Administración de API permite acceder al portal para desarrolladores mediante la autorización OAuth 2.0. En esta guía se explica cómo configurar una instancia del servicio Administración de API para que use la autorización OAuth 2.0.

> Para más información sobre OAuth 2.0, consulte las especificaciones de [http://oauth.net/2/][http://oauth.net/2/].

## En este tema

-   [Requisitos previos][Requisitos previos]
-   [Configurar un servidor de autorización OAuth 2.0 en Administración de API][Configurar un servidor de autorización OAuth 2.0 en Administración de API]
-   [Configurar una API para que use la autorización de usuario OAuth 2.0][Configurar una API para que use la autorización de usuario OAuth 2.0]
-   [Probar la autorización de usuario OAuth 2.0 en el portal para desarrolladores][Probar la autorización de usuario OAuth 2.0 en el portal para desarrolladores]

## <a name="prerequisites"> </a>Requisitos previos

En esta guía se explica cómo configurar la instancia del servicio Administración de API para que use la autorización OAuth 2.0 con las cuentas de desarrollador, pero no se explica cómo configurar un proveedor de OAuth 2.0. Aunque los proveedores de OAuth 2.0 tienen configuraciones diferentes, los pasos son similares y se precisa la misma información para configurar OAuth 2.0 en la instancia del servicio Administración de API. Este tema muestra ejemplos donde Azure Active Directory actúa como proveedor de OAuth 2.0.

> Para más información sobre cómo configurar OAuth 2.0 con Azure Active Directory, consulte el ejemplo de [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet].

## <a name="step1"> </a>Configurar un servidor de autorización OAuth 2.0 en Administración de API

Para comenzar, haga clic en **Consola de administración** en el Portal de Azure para su servicio Administración de API. Esta operación le llevará al portal administrativo Administración de API.

![API Management console][api-management-management-console]

> Si todavía no ha creado una instancia de servicio Administración de API, consulte [Creación de una instancia de Administración de API][Creación de una instancia de Administración de API] en el tutorial [Introducción a la Administración de API de Azure][Introducción a la Administración de API de Azure].

Haga clic en **Seguridad** en el menú **Administración de API** de la izquierda, en **OAuth 2.0** y en **Agregar servidor de autorización**.

![OAuth 2.0][api-management-oauth2]

Al hacer clic en **Agregar servidor de autorización**, se muestra el formulario para un nuevo servidor de autorización.

![Nuevo servidor][api-management-oauth2-server-1]

Escriba un nombre y una descripción opcional en los campos **Nombre** y **Descripción**.

> Estos campos sirven para identificar el servidor de autorización OAuth 2.0 en la instancia del servicio Administración de API y los valores que contienen no proceden del servidor OAuth 2.0.

Escriba la **URL de la página de registro de cliente**. En esta página, los usuarios pueden crear y administrar sus cuentas. Puede variar en función del proveedor de OAuth 2.0.

La sección siguiente del formulario contiene la configuración de los **tipos de concesión de código de autorización**, la **URL del extremo de autorización** y el **método de solicitud de autorización**.

![Nuevo servidor][api-management-oauth2-server-2]

Elija los **tipos de concesión de código de autorización** que desee. El **código de autorización** se especifica de forma predeterminada.

Escriba la **URL del extremo de autorización**. En Azure Active Directory, esta URL será similar a la siguiente, donde `<client_id>` se reemplaza por el identificador de cliente que identifica la aplicación en el servidor OAuth 2.0.

    https://login.windows.net/<client_id>/oauth2/authorize

El **método de solicitud de autorización** especifica cómo se envía la solicitud de autorización al servidor OAuth 2.0. El valor predeterminado es **GET**.

En la sección siguiente, se especifican la **URL del extremo de token**, los **métodos de autenticación de cliente**, el **método de envío de tokens de acceso** y el **ámbito predeterminado**.

![Nuevo servidor][api-management-oauth2-server-3]

En un servidor OAuth 2.0 de Azure Active Directory, la **URL del extremo de token** tendrá el formato siguiente, donde `<APPID>` tiene el formato de `yourapp.onmicrosoft.com`.

    https://login.windows.net/<APPID>/oauth2/token

Los valores predeterminados para **Métodos de autenticación de cliente** y **Método de envío de tokens de acceso** son **Básico** y **Encabezado de autorización** respectivamente. Estos valores se configuran en esta sección del formulario, junto con el **ámbito predeterminado**.

La sección **Credenciales de cliente** contiene el **identificador de cliente** y el **secreto de cliente** que se obtienen durante los procesos de creación y configuración del servidor OAuth 2.0. Al especificar el **identificador de cliente** y el **secreto de cliente**, se genera el **uri de redireccionamiento** para el **código de autorización**. Con este URI se configura la URL de respuesta en la configuración del servidor OAuth 2.0.

![Nuevo servidor][api-management-oauth2-server-4]

Si los **tipos de concesión de código de autorización** se establecen en **Contraseña de propietario de recursos**, las credenciales se especifican con la sección **Credenciales de contraseña de propietario de recursos**. De no ser así, puede dejarse en blanco.

![Nuevo servidor][api-management-oauth2-server-5]

Cuando complete el formulario, haga clic en **Guardar** para guardar la configuración del servidor de autorización OAuth 2.0 de Administración de API. Cuando se guarda la configuración del servidor, pueden configurarse las API para que usen estos valores, conforme a la sección siguiente.

## <a name="step2"> </a>Configurar una API para que use la autorización de usuario OAuth 2.0

Haga clic en **API** en el menú **Administración de API** de la izquierda, en el nombre de la API en cuestión, en **Seguridad** y active la casilla **OAuth 2.0**.

![Autorización de usuario][api-management-user-authorization]

Seleccione el **servidor de autorización** que quiera en la lista desplegable y haga clic en **Guardar**.

![Autorización de usuario][api-management-user-authorization-save]

## <a name="step3"> </a>Probar la autorización de usuario OAuth 2.0 en el portal para desarrolladores

Tras configurar el servidor de autorización OAuth 2.0 y las API para que usen dicho servidor, puede probarlo. Para ello, vaya al portal para desarrolladores y llame a una API. Haga clic en **Portal para desarrolladores** en el menú de la esquina superior derecha.

![Portal para desarrolladores][api-management-developer-portal-menu]

Haga clic en **API** en el menú superior y seleccione **API Eco**.

![API Eco][api-management-apis-echo-api]

> Si solamente tiene una API configurada o visible en su cuenta, al hacer clic en API irá directamente a las operaciones de dicha API.

Seleccione la operación **Recurso GET**, haga clic en **Abrir consola** y seleccione **Código de autorización** en la lista desplegable.

![Open console][api-management-open-console]

Al seleccionar **Código de autorización**, se abre una ventana emergente con el formulario de suscripción del proveedor de OAuth 2.0. En este ejemplo, Azure Active Directory ha proporcionado el formulario de suscripción.

> Si ha deshabilitado los elementos emergentes, el explorador le solicitará que los habilite. Cuando los habilite, seleccione **Código de autorización** de nuevo y se mostrará el formulario de suscripción.

![Iniciar sesión][api-management-oauth2-signin]

Tras iniciar sesión, los **encabezados de solicitud** se rellenan con el encabezado `Authorization : Bearer` que autoriza la solicitud.

![Token de encabezado de solicitud][api-management-request-header-token]

Llegado a este punto, puede configurar los valores que desea para los demás parámetros y enviar la solicitud.

  [http://oauth.net/2/]: http://oauth.net/2/
  [Requisitos previos]: #prerequisites
  [Configurar un servidor de autorización OAuth 2.0 en Administración de API]: #step1
  [Configurar una API para que use la autorización de usuario OAuth 2.0]: #step2
  [Probar la autorización de usuario OAuth 2.0 en el portal para desarrolladores]: #step3
  [WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
  [Creación de una instancia de Administración de API]: ../api-management-get-started/#create-service-instance
  [Introducción a la Administración de API de Azure]: ../api-management-get-started
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
