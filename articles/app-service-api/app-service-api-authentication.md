<properties
	pageTitle="Autenticación y autorización para Aplicaciones de API en el Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Obtenga información acerca de los servicios de autenticación y autorización que el Servicio de aplicaciones de Azure proporciona para Aplicaciones de API."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="12/04/2015"
	ms.author="tdykstra"/>

# Autenticación y autorización para Aplicaciones de API en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Información general 

En este artículo se describen las opciones para tratar la autenticación y autorización para Aplicaciones de API en el Servicio de aplicaciones de Azure.

El siguiente diagrama ilustra algunas de las características clave de la autenticación de Servicio de aplicaciones:

* Preprocesa los solicitudes entrantes de la API, lo que le ofrece varias opciones para elegir cuánto trabajo de autenticación desea realizar en su propio código. 
* Admite cinco proveedores de autenticación: Azure Active Directory, Facebook, Google, Twitter y cuenta de Microsoft.
* Funciona para la autenticación tanto del usuario final como de la entidad de servicio. 
* Funciona igual para Aplicaciones de API, Aplicaciones Web y Aplicaciones móviles.

![](./media/app-service-api-authentication/api-apps-overview.png)

## Preprocesamiento de solicitudes entrantes

El Servicio de aplicaciones puede evitar que solicitudes HTTP anónimas accedan a las aplicaciones de API o que autentiquen las que tengan tokens antes de que lleguen a las aplicaciones de API. En una aplicación de API se puede configurar cualquiera de las tres opciones siguientes:

1. Permitir que solo lleguen a la aplicaciones de API las solicitudes autenticadas.

	Si se recibe una solicitud anónima de un explorador, Servicio de aplicaciones la redirigirá a una página de inicio de sesión.

	Esto funciona si sabe de antemano qué proveedor de autenticación (Google, Twitter, etc.) se desea usar, ya que en ese caso, puede configurar el Servicio de aplicaciones para que controle automáticamente el proceso de inicio de sesión. Como alternativa, se puede especificar la dirección URL propia a la que el Servicio de aplicaciones redirigirá las solicitudes anónimas. Posteriormente, se puede permitir a los usuarios elegir el proveedor de autenticación.

	Con esta opción, no es preciso escribir código de autenticación en la aplicación y la autorización se simplifica, ya que las notificaciones más importantes se proporcionan en los encabezados HTTP.

2. Permitir que todas las solicitudes lleguen a la aplicación de API, pero validar las solicitudes autenticadas y pasar la información de autenticación en los encabezados HTTP.

	Esta opción proporciona más flexibilidad en el tratamiento de las solicitudes anónimas y facilita la escritura de código que necesite acceso a las notificaciones más comunes. A diferencia de la opción 1, para evitar que usuarios anónimos usen la API es preciso escribir código.

3. Permitir que todas las solicitudes lleguen a la API, no realizar acciones relativas a la información de autenticación de las solicitudes.

	Esta opción deja las tareas de autenticación y autorización totalmente en manos del código de la aplicación.

En el [Portal de Azure](https://portal.azure.com/), seleccione la opción que desee en la hoja **Autenticación o autorización**.

![](./media/app-service-api-authentication/authblade.png)

Para las opciones 1 y 2, active **Autenticación de Servicio de aplicaciones** y en la lista desplegable **Acción que se realiza cuando la solicitud no está autenticada** y elija **Iniciar sesión** o **Permitir solicitud (ninguna acción)**. Si elige **Iniciar sesión**, tendrá que elegir un proveedor de autenticación y configurarlo.

![](./media/app-service-api-authentication/actiontotake.png)
 
## Independencia de lenguajes

El procesamiento de la autenticación de Servicio de aplicaciones se produce antes de que las solicitudes lleguen a su aplicación de API, lo que significa que las características de autenticación funcionan para las aplicaciones de API escritas en cualquier lenguaje o marco. La API se puede basar en ASP.NET, Java, Node.js o en cualquier marco compatible con el Servicio de aplicaciones.

El Servicio de aplicaciones pasa el token JWT en el encabezado de autorización de una solicitud HTTP y el código escrito en cualquier lenguaje o marco puede obtener la información que necesita del token. Además, el Servicio de aplicaciones facilita el acceso a las notificaciones más utilizadas, para lo que establece algunos encabezados especiales como:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
En una API de .NET se puede usar el atributo `Authorize` y, en el caso de una autorización específica, se puede escribir fácilmente código según las notificaciones, ya que la información de las notificaciones se rellena automáticamente en las clases .NET.

## Autenticación de entidad de servicio

La autenticación del Servicio de aplicaciones también se puede usar en escenarios internos, como para la realización de llamadas de una aplicación de API a otra. En este escenario se pueden usar las credenciales de una cuenta de servicio, en lugar de las credenciales de usuario final para la autenticación. La cuenta de servicio también se conoce como *entidad de servicio* y la autenticación que usa dicha cuenta de este tipo también se conoce como escenario de servicio a servicio.

En los escenarios internos, se puede proteger la aplicación de API llamada con Azure Active Directory y proporcionar un token de autorización de la entidad de servicio de AAD al llamar a la aplicación de API. El token se puede solicitar mediante la especificación del identificador de cliente y el secreto de cliente desde la aplicación de AAD. No se necesita ningún código especial solo para Azure, como el que se solía usar para controlar el token de Zumo de Servicios móviles. Un ejemplo de este escenario mediante de uso de aplicaciones de API de ASP.NET puede encontrarse en el tutorial [Autenticación de entidad de servicio para Aplicaciones de API en el Servicio de aplicaciones de Azure](app-service-api-dotnet-service-principal-auth.md).

Si desea administrar un escenario de servicio a servicio sin usar la autenticación del Servicio de aplicaciones, puede usar certificados de cliente o una autenticación básica. Para información acerca de los certificados de cliente en Azure, consulte [Configuración de la autenticación mutua de TLS para una aplicación web](../app-service-web/app-service-web-configure-tls-mutual-auth.md).

La autenticación de la entidad de servicio desde una aplicación lógica de Servicio de aplicaciones en una aplicación de API es un caso especial que se explica en [Uso de la API personalizada hospedada en Servicio de aplicaciones con aplicaciones lógicas](../app-service-logic/app-service-logic-custom-hosted-api.md).

## Más información

Para más información acerca de los servicios de autenticación y autorización del Servicio de aplicaciones de Azure, consulte [Expanding App Service Authentication/Authorization](/blog/announcing-app-service-authentication-authorization/).

## Pasos siguientes

En este artículo se han explicado las características de autenticación y autorización de las aplicaciones de API del Servicio de aplicaciones.

Si sigue la secuencia de tutoriales de introducción a ASP.NET y a Aplicaciones de API, pruebe estas características en el siguiente tutorial, [Autenticación de usuario para aplicaciones de API en el Servicio de aplicaciones de Azure](app-service-api-dotnet-user-principal-auth.md).

Para más información acerca del uso de Java y Node en Servicio de aplicaciones de Azure, consulte el [Centro para desarrolladores de Node.js](/develop/nodejs/) y el [Centro para desarrolladores de Java](/develop/java/).

<!---HONumber=AcomDC_1210_2015-->