---
title: "Autenticación y autorización en Azure App Service | Microsoft Docs"
description: "Referencia e información general conceptual de la característica de autenticación o autorización para el Servicio de aplicaciones de Azure"
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/29/2016
ms.author: mahender
ms.openlocfilehash: f0d2644903181cd2e20166feae4f90ddd4037fa8
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticación y autorización en el Servicio de aplicaciones de Azure
## <a name="what-is-app-service-authentication--authorization"></a>¿Qué es la autenticación o autorización del Servicio de aplicaciones?
La autenticación/autorización del Servicio de aplicaciones es una característica que proporciona una forma para que los usuarios inicien sesión en la aplicación con objeto de que no tengan que cambiar el código en el back-end de aplicación. Ofrece una forma fácil de proteger su aplicación y trabajar con datos por usuario.

El Servicio de aplicaciones usa la identidad federada, en la cual un proveedor de identidades de terceros almacena cuentas y autentica usuarios. La aplicación se basa en la información de identidad del proveedor para que la aplicación no tenga que almacenar esa información por sí misma. El Servicio de aplicaciones admite cinco proveedores de identidades listos para usar: Azure Active Directory, Facebook, Google, la cuenta Microsoft y Twitter. La aplicación puede usar cualquier cantidad de estos proveedores de identidades para proporcionar opciones de inicio de sesión a sus usuarios. Para ampliar la compatibilidad incorporada, puede integrar otro proveedor de identidades o [su propia solución de identidad personalizada][custom-auth].

Si quiere comenzar inmediatamente, consulte uno de los siguientes tutoriales [Incorporación de la autenticación a la aplicación iOS] [ iOS] ([Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms] o [Cordova]).

## <a name="how-authentication-works-in-app-service"></a>Funcionamiento de la autenticación en el Servicio de aplicaciones
Para autenticarse con uno de los proveedores de identidades, primero debe configurar el proveedor de identidades para obtener información sobre su aplicación. A continuación, el proveedor de identidades proporcionará los identificadores y secretos que se deben proporcionar al Servicio de aplicaciones. Esto completa la relación de confianza para que el Servicio de aplicaciones pueda validar las aserciones de usuario, como los tokens de autenticación, del proveedor de identidades.

Para que un usuario inicie sesión con uno de estos proveedores, se debe redirigir al usuario a un punto de conexión con el que los usuarios inician sesión para ese proveedor. Si los clientes están utilizando un explorador web, puede hacer que el Servicio de aplicaciones dirija automáticamente a todos los usuarios no autenticados al punto de conexión con el que los usuarios pueden iniciar sesión. En caso contrario, deberá dirigir a los clientes a `{your App Service base URL}/.auth/login/<provider>`, donde `<provider>` es uno de los siguientes valores: aad, facebook, google, microsoft y twitter. En secciones posteriores de este artículo se explican los escenarios móviles y API.

Los usuarios que interactúan con su aplicación a través de un explorador web tendrán una cookie establecida de modo que puedan permanecer autenticados a medida que se desplazan por la aplicación. Para otros tipos de cliente, como los dispositivos móviles, se emitirá un token de web JSON (JWT) para el cliente que debe presentarse en el encabezado `X-ZUMO-AUTH` . Los SDK de cliente de Aplicaciones móviles controlarán esto en su lugar. Como alternativa, se puede incluir un token de acceso o el token de identidad de Azure Active Directory directamente en el encabezado `Authorization` como [token de portador](https://tools.ietf.org/html/rfc6750).

El Servicio de aplicaciones validará cualquier cookie o token que haya emitido la aplicación para autenticar usuarios. Para restringir quién puede tener acceso a la aplicación, consulte la sección de [autorización](#authorization) más abajo en este artículo.

### <a name="mobile-authentication-with-a-provider-sdk"></a>Funcionamiento de la autenticación con un SDK del proveedor
Cuando todo se haya configurado en el back-end, puede modificar los clientes móviles para que inicien sesión con el Servicio de aplicaciones. Hay dos enfoques aquí:

* Use un SDK que un proveedor de identidades determinado establezca la identidad y después obtener acceso al Servicio de aplicaciones.
* Permita que los usuarios inicien sesión en el SDK del cliente de Aplicaciones móviles con una sola línea de código.

> [!TIP]
> La mayoría de las aplicaciones deben utilizar un SDK del proveedor para obtener una experiencia más coherente cuando los usuarios inician sesión, para usar la compatibilidad de actualización y para obtener otros beneficios que especifica el proveedor.
> 
> 

Cuando se utiliza un SDK del proveedor, los usuarios pueden iniciar sesión en una experiencia que se integra más estrechamente con el sistema operativo que la aplicación en la que se está ejecutando. También le proporciona un token del proveedor e información de usuario sobre el cliente, lo que facilita en gran medida el consumo de API de gráficos y la personalización de la experiencia del usuario. En ocasiones, en blogs y foros, encontrará que se hace referencia al "flujo de cliente" o "flujo dirigido al cliente", porque el código en el cliente con el que los usuarios pueden iniciar sesión y el código de cliente tiene acceso a un token del proveedor.

Una vez que se obtiene un token del proveedor, debe enviarse al Servicio de aplicaciones para su validación. Después de que el Servicio de aplicaciones valida el token, crea un nuevo token de Servicio de aplicaciones que se devuelve al cliente. El SDK de cliente de Aplicaciones móviles tiene métodos auxiliares para administrar este intercambio y asociar automáticamente el token a todas las solicitudes en el back-end de la aplicación. El desarrollador también puede mantener una referencia al token del proveedor si así lo desea.

### <a name="mobile-authentication-without-a-provider-sdk"></a>Autenticación móvil sin un SDK del proveedor
Si no desea configurar un SDK del proveedor, puede permitir que la característica de Aplicaciones móviles de Servicio de aplicaciones de Azure para iniciar sesión automáticamente. El SDK del cliente de Aplicaciones móviles abrirá una vista web al proveedor de su elección y permitirá que el usuario inicie sesión. En ocasiones, en blogs y foros encontrará que se hace referencia a este como "flujo de servidor" o "flujo dirigido al servidor", pues el servidor administra el proceso con el que los usuarios inician sesión y el SDK del cliente nunca recibe el token del proveedor.

El código para iniciar este flujo se incluye en el tutorial de autenticación para cada plataforma. Al final del flujo, el SDK del cliente tiene un token del Servicio de aplicaciones que se adjunta automáticamente a todas las solicitudes para el back-end de la aplicación.

### <a name="service-to-service-authentication"></a>Autenticación entre servicios
Aunque puede conceder acceso a los usuarios a la aplicación, también puede confiar en otra aplicación para llamar a su propia API. Por ejemplo, podría hacer que una aplicación web llame a una API en otra aplicación web. En este escenario utilice credenciales para una cuenta de servicio, en lugar de las credenciales de usuario para obtener un token. Las cuentas de servicio también se conocen como *entidades de servicio* en la jerga de Azure Active Directory y la autenticación que usa dichas cuentas también se conoce como escenario entre servicios.

> [!IMPORTANT]
> Al ejecutarse en dispositivos de cliente, las aplicaciones móviles *no* se consideran aplicaciones de confianza y no deberían usar un flujo de la entidad de servicio. En su lugar, deben utilizar un flujo de usuario que se ha mencionado anteriormente.
> 
> 

En los escenarios entre servicios, el Servicio de aplicaciones puede proteger la aplicación mediante Azure Active Directory. La aplicación de llamada solo debe proporcionar un token de autorización de una entidad de servicio de Azure Active Directory, obtenido al proporcionar el identificador y el secreto de cliente desde Azure Active Directory. Un ejemplo de este escenario que usa aplicaciones de API de ASP.NET puede encontrarse en el tutorial [Autenticación de entidad de servicio para API Apps][apia-service].

Si desea usar la autenticación de Servicio de aplicaciones para controlar un escenario entre servicios, puede usar certificados de cliente o una autenticación básica. Para obtener información acerca de los certificados de cliente en Azure, consulte [Configuración de la autenticación mutua de TLS para una aplicación web](app-service-web-configure-tls-mutual-auth.md). Para más información sobre la autenticación básica en ASP.NET, consulte [Filtros de autenticación en ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

La autenticación de cuentas de servicio desde una aplicación lógica de Servicio de aplicaciones en una aplicación de API es un caso especial que se explica detalladamente en [Uso de la API personalizada hospedada en Servicio de aplicaciones con aplicaciones lógicas](../logic-apps/logic-apps-custom-hosted-api.md).

## <a name="authorization"></a>Funcionamiento de la autorización en el Servicio de aplicaciones
Tiene un control total para decidir qué solicitudes pueden acceder a su aplicación. Se puede configurar la autenticación o autorización del Servicio de aplicaciones mediante cualquiera de las siguientes acciones:

* Permitir que solo lleguen a la aplicación las solicitudes autenticadas.
  
    Si un explorador envía una solicitud anónima, App Service le redirigirá a una página del proveedor de identidades que elija para que los usuarios puedan iniciar sesión. Si la solicitud proviene de un dispositivo móvil, se devolverá una respuesta HTTP *401 no autorizado*.
  
    Con esta opción, no es necesario escribir ningún código de autenticación en la aplicación. Si necesita una autorización más específica, hay información disponible para su código acerca del usuario.
* Permita que todas las solicitudes lleguen a la aplicación, pero valide las solicitudes autenticadas y pase la información de autenticación en los encabezados HTTP.
  
    Esta opción traslada las decisiones de autorización al código de aplicación. Esto proporciona más flexibilidad a la hora de controlar las solicitudes anónimas, pero tiene que escribir el código.
* Permita que todas las solicitudes lleguen a la aplicación y no realice acciones relativas a la información de autenticación de las solicitudes.
  
    En este caso, la característica de autenticación o autorización está desactivada. Esta opción deja las tareas de autenticación y autorización totalmente en manos del código de la aplicación.

Los comportamientos anteriores se controlan mediante la opción **Acción necesaria cuando la solicitud no está autenticada** del Portal de Azure. Si elige **Iniciar sesión con *nombre de proveedor***, se tienen que autenticar todas las solicitudes. **Permitir solicitud (sin acción)** traslada la decisión de autorización al código, pero sigue proporcionando información de autenticación. Si desea que su código lo controle todo, puede deshabilitar la característica de autenticación y autorización.

## <a name="working-with-user-identities-in-your-application"></a>Trabajo con identidades de usuario en la aplicación
El Servicio de aplicaciones pasa parte de la información del usuario a la aplicación mediante encabezados especiales. Las solicitudes externas prohíben estos encabezados y solo estarán presentes si así se ha establecido mediante la autenticación o autorización del Servicio de aplicaciones. A continuación puede ver algunos encabezados de ejemplo:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

El código escrito en cualquier lenguaje o plataforma puede obtener la información que necesita de estos encabezados. Para las aplicaciones de ASP.NET 4.6, **ClaimsPrincipal** se establece automáticamente con los valores adecuados.

La aplicación también puede obtener detalles adicionales del usuario a través de un comando HTTP GET en el punto de conexión `/.auth/me` de la aplicación. Un token válido que se incluye con la solicitud devolverá una carga JSON con detalles sobre el proveedor que se está usando, el token del proveedor subyacente y alguna otra información de usuario. Los SDK del servidor de Aplicaciones móviles proporcionan métodos auxiliares para trabajar con estos datos. Para más información, consulte [Uso del SDK de Node.js de Azure Mobile Apps](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity) y [Trabajar con el SDK del servidor back-end de .NET para Azure Mobile Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="documentation-and-additional-resources"></a>Documentación y recursos adicionales
### <a name="identity-providers"></a>Proveedores de identidades
En los siguientes tutoriales se muestra cómo configurar el Servicio de aplicaciones para usar los distintos proveedores de autenticación:

* [Configuración de la aplicación para usar el inicio de sesión de Azure Active Directory][AAD]
* [Configuración de la aplicación para usar el inicio de sesión de Facebook][Facebook]
* [Configuración de la aplicación para usar el inicio de sesión de Google][Google]
* [Configuración de la aplicación para usar el inicio de sesión de la cuenta Microsoft][MSA]
* [Configuración de la aplicación para usar el inicio de sesión de Twitter][Twitter]

Si quiere usar un sistema de identidades diferente a los proporcionados aquí, también puede usar la [compatibilidad con la autenticación personalizada de versión preliminar en el SDK de servidor .NET de Mobile Apps][custom-auth], que se puede utilizar en aplicaciones web, móviles o de API.

### <a name="mobile-applications"></a>Aplicaciones móviles
Los siguientes tutoriales muestran cómo incorporar la autenticación a los clientes móviles con el flujo dirigido al servidor:

* [Incorporación de la autenticación a la aplicación iOS][iOS]
* [Adición de autenticación a una aplicación Android][Android]
* [Adición de autenticación a una aplicación Windows][Windows]
* [Adición de autenticación a una aplicación Xamarin.iOS][Xamarin.iOS]
* [Adición de autenticación a una aplicación Xamarin.Android][Xamarin.Android]
* [Adición de autenticación a una aplicación Xamarin.Forms][Xamarin.Forms]
* [Adición de la autenticación a la aplicación de Cordova][Cordova]

Si desea usar el flujo dirigido al cliente para Azure Active Directory, use los siguientes recursos:

* [Utilice la biblioteca de autenticación de Active Directory para iOS][ADAL-iOS]
* [Utilice la biblioteca de autenticación de Active Directory para Android][ADAL-Android]
* [Utilice la biblioteca de autenticación de Active Directory para Windows y Xamarin][ADAL-dotnet]

Si desea usar el flujo dirigido al cliente para Facebook, use los siguientes recursos:

* [Uso del SDK de Facebook para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Si desea usar el flujo dirigido al cliente para Twitter, use los siguientes recursos:

* [Uso de Fabric de Twitter para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Si desea usar el flujo dirigido al cliente para Google, use los siguientes recursos:

* [Uso del SDK de inicio de sesión de Google para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

<!-- ### API applications
The following tutorials show how to protect your API apps:

* [User authentication for API Apps in Azure App Service][apia-user]
* [Service principal authentication for API Apps in Azure App Service][apia-service] -->

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: app-service-mobile-how-to-configure-google-authentication.md
[MSA]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
