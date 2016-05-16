<properties
	pageTitle="Autenticación y autorización en el Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Referencia e información general conceptual de la característica de autenticación o autorización para el Servicio de aplicaciones de Azure"
	services="app-service"
	documentationCenter=""
	authors="mattchenderson"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="04/25/2016"
	ms.author="mahender"/>
    
# Autenticación y autorización en el Servicio de aplicaciones de Azure

## ¿Qué es la autenticación o autorización del Servicio de aplicaciones?

La autenticación o autorización del Servicio de aplicaciones es una característica que permite que los usuarios inicien sesión en la aplicación sin necesidad de realizar cambios en el código del back-end de la misma. Ofrece una forma fácil de proteger su aplicación y trabajar con datos por usuario.

El Servicio de aplicaciones usa la identidad federada, en la cual un proveedor de identidades de terceros almacena cuentas y autentica usuarios. La aplicación se basa en la información de identidad del proveedor en lugar de almacenar esa información por sí misma. El Servicio de aplicaciones admite cinco proveedores de identidades listos para usar: _Azure Active Directory_, _Facebook_, _Google_, _la cuenta Microsoft_ y _Twitter_. La aplicación puede aprovechar cualquier cantidad de estos proveedores de identidades para proporcionar opciones de inicio de sesión a sus usuarios. También puede ampliar la compatibilidad incorporada integrando otro proveedor de identidades o [su propia solución de identidad personalizada][custom-auth].

Si desea comenzar inmediatamente, consulte uno de los siguientes tutoriales:

- [Incorporación de la autenticación a la aplicación iOS][iOS] (o [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms], o [Cordova])
- [Autenticación de usuario para aplicaciones de API en el Servicio de aplicaciones de Azure][apia-user]

## Funcionamiento de la autenticación en el Servicio de aplicaciones

Para autenticarse con uno de los proveedores de identidades, primero debe configurar el proveedor de identidades para obtener información sobre su aplicación. A continuación, el proveedor de identidades le proporcionará los identificadores y secretos que, a su vez, usted debe proporcionar al Servicio de aplicaciones. Esto completa la relación de confianza y permite al Servicio de aplicaciones validar las aserciones de usuario del proveedor de identidades, como los tokens de autenticación.

Para que un usuario inicie sesión con uno de estos proveedores, se debe redirigir al usuario a un punto de conexión del inicio de sesión para ese proveedor. Si los clientes están utilizando un explorador web, puede hacer que el Servicio de aplicaciones dirija automáticamente a todos los usuarios no autenticados al punto de conexión de inicio de sesión. En caso contrario, deberá dirigir a los clientes a `{your App Service base URL}/.auth/login/<provider>`, donde `<provider>` es uno de los siguientes valores: _aad_, _facebook_, _google_, _microsoft_ y _twitter_. En las secciones siguientes se analizan los escenarios móviles y de la API

Los usuarios que interactuan con su aplicación a través de un explorador web tendrán una cookie establecida de modo que puedan permanecer autenticados a medida que se desplazan por la aplicación. Para otros tipos de cliente, como los dispositivos móviles, se emitirá un token de web JSON (JWT) para el cliente que debe presentarse en el encabezado `X-ZUMO-AUTH`. Los SDK de cliente de Aplicaciones móviles controlarán esto en su lugar. Como alternativa, se puede incluir un token de acceso o el token de identidad de Azure Active Directory directamente en el encabezado `Authorization` como [token de portador](https://tools.ietf.org/html/rfc6750).

El Servicio de aplicaciones validará cualquier cookie o token que haya emitido la aplicación, lo que permitirá a los usuarios autenticarse. Para restringir quién puede tener acceso a la aplicación, consulte la sección de [autorización](#authorization) más abajo.

### Funcionamiento de la autenticación con un SDK del proveedor

Una vez que se ha configurado todo en el back-end, puede modificar los clientes móviles para iniciar sesión en el Servicio de aplicaciones. Hay dos enfoques aquí:

- Aproveche un SDK publicado por un proveedor de identidades determinado para establecer la identidad y después obtener acceso al Servicio de aplicaciones.
- Permita que los usuarios inicien sesión en el SDK del cliente de Aplicaciones móviles con una sola línea de código.

>[AZURE.TIP] La mayoría de las aplicaciones deben usar un SDK del proveedor para obtener una experiencia de inicio de sesión más nativa y aprovechar la compatibilidad de actualización y otras ventajas específicas del proveedor.

Trabajar con un SDK del proveedor permite que la experiencia de inicio de sesión interactúe de manera más estrecha con el SO de la plataforma donde se ejecuta la aplicación. También le proporciona un token del proveedor e información de usuario sobre el cliente, lo que facilita en gran medida el consumo de API de gráficos y la personalización de la experiencia del usuario. En ocasiones, en blogs y foros encontrará que se hace referencia a este como "flujo de cliente" o "flujo dirigido al cliente", pues el código del cliente controla el inicio de sesión y tiene acceso a un token del proveedor.

Una vez que se obtiene un token del proveedor, debe enviarse al Servicio de aplicaciones para su validación. Después de que el Servicio de aplicaciones valida el token, crea un nuevo token de Servicio de aplicaciones que se devuelve al cliente. El SDK de cliente de Aplicaciones móviles tiene métodos auxiliares para administrar este intercambio y asociar automáticamente el token a todas las solicitudes en el back-end de la aplicación. El desarrollador también puede mantener una referencia al token del proveedor si así lo desea.

### Autenticación móvil sin un SDK del proveedor

Si no desea configurar un SDK del proveedor, puede dejar que Aplicaciones móviles del Servicio de aplicaciones realice el inicio de sesión por usted. El SDK del cliente de Aplicaciones móviles abrirá una vista web al proveedor de su elección y completará el inicio de sesión. En ocasiones, en blogs y foros encontrará que se hace referencia a este como "flujo de servidor" o "flujo dirigido al servidor", pues el servidor administra el inicio de sesión y el SDK del cliente nunca recibe el token del proveedor.

El código necesario para iniciar este flujo se trata en el tutorial de autenticación para cada plataforma. Al final del flujo, el SDK del cliente tiene un token del Servicio de aplicaciones que se adjunta automáticamente a todas las solicitudes para el back-end de la aplicación.

### Autenticación entre servicios

Además de dar a los usuarios acceso a la aplicación, también puede permitir que otra aplicación de confianza llame a su propia API. Por ejemplo, podría hacer que una aplicación del Servicio de aplicaciones llame a una API de otra aplicación. En este escenario se puede obtener un token mediante credenciales para una cuenta de servicio, en lugar de las credenciales de usuario final. Las cuentas de servicio también se conocen como *entidades de servicio* en la jerga de Azure Active Directory y la autenticación que usa dichas cuentas también se conoce como escenario entre servicios.

>[AZURE.IMPORTANT] Como se ejecutan en dispositivos de cliente, las aplicaciones móviles _no_ se consideran aplicaciones de confianza y no deberían usar un flujo de entidades de servicio. En su lugar, deben usar uno de los flujos de usuario detallados anteriormente.

Para escenarios entre servicios, el Servicio de aplicaciones puede proteger la aplicación mediante Azure Active Directory. La aplicación de llamada solo debe proporcionar un token de autorización de una entidad de servicio de AAD obtenido al proporcionar el identificador y el secreto de cliente desde AAD. Un ejemplo del uso de este escenario por parte de aplicaciones de API de ASP.NET puede encontrarse en el tutorial [Autenticación de entidad de servicio para Aplicaciones de API en el Servicio de aplicaciones de Azure][apia-service].

Si desea administrar un escenario de servicio a servicio sin usar la autenticación del Servicio de aplicaciones, puede usar certificados de cliente o una autenticación básica. Para obtener información acerca de los certificados de cliente en Azure, consulte [Configuración de la autenticación mutua de TLS para una aplicación web](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Para más información sobre la autenticación básica en ASP.NET, consulte [Authentication Filters in ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters) (Filtros de autenticación en ASP.NET Web API 2).

La autenticación de cuentas de servicio desde una aplicación lógica de Servicio de aplicaciones en una aplicación de API es un caso especial que se explica detalladamente en [Uso de la API personalizada hospedada en Servicio de aplicaciones con aplicaciones lógicas](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="authorization"></a>Funcionamiento de la autorización en el Servicio de aplicaciones

Tiene un control total para decidir a qué solicitudes se les permite acceder a su aplicación. Se puede configurar la autenticación o autorización del Servicio de aplicaciones mediante cualquiera de las siguientes acciones:

- Permitir que solo lleguen a la aplicación las solicitudes autenticadas.

	Si se recibe una solicitud anónima desde un explorador, el Servicio de aplicaciones le redirigirá a una página de inicio de sesión del proveedor de identidades que elija. Si la solicitud proviene de un dispositivo móvil, se devolverá una respuesta HTTP _401 no autorizado_.

	Con esta opción, no es necesario escribir ningún código de autenticación en la aplicación. Si necesita una autorización más específica, hay información disponible para su código acerca del usuario.

- Permitir que todas las solicitudes lleguen a la aplicación, pero validar las solicitudes autenticadas y pasar la información de autenticación en los encabezados HTTP.

	Esta opción traslada las decisiones de autorización al código de aplicación. Esto proporciona más flexibilidad a la hora de controlar las solicitudes anónimas, pero requiere que escriba el código.
	
- Permitir que todas las solicitudes lleguen a la aplicación y no realizar acciones relativas a la información de autenticación de las solicitudes.

	En este caso, la característica de autenticación o autorización está desactivada. Esta opción deja las tareas de autenticación y autorización totalmente en manos del código de la aplicación.

Los comportamientos descritos anteriormente se pueden controlar mediante la opción **Acción necesaria cuando la solicitud no está autenticada** del portal. Si elige "Iniciar sesión con..." para uno de los proveedores, necesitará que todas las solicitudes se autentiquen. "Permitir solicitud (sin acción)" traslada la decisión de autorización al código, pero sigue proporcionando información de autenticación. Si desea que su código lo controle todo, puede deshabilitar la característica de autenticación y autorización.

## Trabajo con identidades de usuario en la aplicación

El Servicio de aplicaciones pasa algo de información del usuario a la aplicación mediante encabezados especiales. No se permiten estos encabezados de solicitudes externas y solo estarán presentes si así se ha establecido mediante la autenticación o autorización del Servicio de aplicaciones. A continuación puede ver algunos encabezados de ejemplo:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

El código escrito en cualquier lenguaje o marco puede obtener la información que necesita de estos encabezados. Para las aplicaciones de ASP.NET 4.6, ClaimsPrincipal se establece automáticamente con los valores adecuados.

La aplicación también puede obtener detalles de usuario adicionales a través de un comando HTTP GET en el punto de conexión `/.auth/me` de la aplicación. Si se incluyó un token válido con la solicitud, esta devolverá una carga JSON con detalles sobre el proveedor que se está usando, el token del proveedor subyacente y alguna otra información de usuario. Los SDK del servidor de Aplicaciones móviles proporcionan métodos auxiliares para trabajar con estos datos ([Node.JS](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md/#howto-tables-getidentity), [.NET](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md/#user-info)).

## Documentación y recursos adicionales

### Proveedores de identidades
En los siguientes tutoriales se muestra cómo configurar el Servicio de aplicaciones para aprovechar los distintos proveedores de autenticación:

- [Configuración de la aplicación para usar el inicio de sesión de Azure Active Directory][AAD]
- [Configuración de la aplicación para usar el inicio de sesión de Facebook][Facebook]
- [Configuración de la aplicación para usar el inicio de sesión de Google][Google]
- [Configuración de la aplicación para usar el inicio de sesión de la cuenta Microsoft][MSA]
- [Configuración de la aplicación para usar el inicio de sesión de Twitter][Twitter]

Si desea usar un sistema de identidades diferente a los proporcionados aquí, también puede aprovechar la [compatibilidad con la autenticación personalizada de vista previa en el SDK de servidor .NET del Servicio de aplicaciones][custom-auth], que se puede utilizar en aplicaciones web, móviles o de API.

### Aplicaciones móviles
Los siguientes tutoriales muestran cómo incorporar la autenticación a los clientes móviles con el flujo dirigido al servidor:

- [Incorporación de la autenticación a la aplicación iOS][iOS]
- [Adición de la autenticación a la aplicación de Android][Android]
- [Adición de la autenticación a la aplicación de Windows][Windows]
- [Adición de la autenticación a la aplicación Xamarin.iOS][Xamarin.iOS]
- [Adición de la autenticación a la aplicación Xamarin.Android][Xamarin.Android]
- [Agregar autenticación a la aplicación de Xamarin.Forms][Xamarin.Forms]
- [Adición de la autenticación a la aplicación de Cordova][Cordova]

Si desea usar el flujo dirigido al cliente para AAD:

- [Utilice la biblioteca de autenticación de Active Directory para iOS][ADAL-iOS]
- [Utilice la biblioteca de autenticación de Active Directory para Android][ADAL-Android]
- [Utilice la biblioteca de autenticación de Active Directory para Windows y Xamarin][ADAL-dotnet]

### Aplicaciones de API
Los siguientes tutoriales muestran cómo proteger sus aplicaciones de API:

- [Autenticación de usuario para aplicaciones de API en el Servicio de aplicaciones de Azure][apia-user]
- [Autenticación de entidad de servicio para Aplicaciones de API en el Servicio de aplicaciones de Azure][apia-service]









[apia-user]: ../app-service-api/app-service-api-dotnet-user-principal-auth.md
[apia-service]: ../app-service-api/app-service-api-dotnet-service-principal-auth.md

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: ../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: ../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md
[MSA]: ../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: ../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md/#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md/#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md/#adal

<!---HONumber=AcomDC_0504_2016-->