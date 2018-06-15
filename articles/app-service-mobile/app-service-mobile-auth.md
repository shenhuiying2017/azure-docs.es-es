---
title: Autenticación y autorización en Azure App Service para aplicaciones móviles | Microsoft Docs
description: Referencia conceptual e información general de la característica de autenticación o autorización para Azure App Service, en concreto para aplicaciones móviles.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 237310c607eb8488e53631b6e69d01703d1ebf99
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
ms.locfileid: "30839735"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Autenticación y autorización en Azure App Service para aplicaciones móviles

En este artículo se describe cómo funcionan la autenticación y autorización al desarrollar aplicaciones móviles nativas con un back-end de App Service. App Service proporciona autenticación y autorización integradas, así las aplicaciones móviles pueden iniciar la sesión de los usuarios sin cambiar ningún código de dicho servicio. Ofrece una forma fácil de proteger su aplicación y trabajar con datos por usuario. 

Este artículo se centra en el desarrollo de aplicaciones móviles. Para empezar a trabajar rápidamente con la autenticación y autorización de App Service para su aplicación móvil, consulte uno de los siguientes tutoriales [Incorporación de la autenticación a la aplicación iOS][iOS] (o el sistema operativo que corresponda en su caso: [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms] o [Cordova]). 

Para información sobre cómo funcionan la autenticación y autorización en App Service, consulte [Autenticación y autorización en Azure App Service](../app-service/app-service-authentication-overview.md).

## <a name="authentication-with-provider-sdk"></a>Autenticación con el SDK de proveedor

Cuando todo se haya configurado en App Service, puede modificar los clientes móviles para que inicien sesión con este servicio. Hay dos enfoques aquí:

* Use un SDK que un proveedor de identidades determinado establezca la identidad y después obtener acceso a App Service.
* Permita que los usuarios inicien sesión en el SDK del cliente de Mobile Apps con una sola línea de código.

> [!TIP]
> La mayoría de las aplicaciones deben usar un SDK de proveedor para obtener una experiencia más coherente cuando los usuarios inician sesión, para usar la compatibilidad con la actualización de tokens y para obtener otros beneficios que especifica el proveedor.
> 
> 

Cuando se utiliza un SDK del proveedor, los usuarios pueden iniciar sesión en una experiencia que se integra más estrechamente con el sistema operativo que la aplicación en la que se está ejecutando. Este método también le proporciona un token del proveedor e información de usuario sobre el cliente, lo que facilita en gran medida el consumo de API de grafos y la personalización de la experiencia del usuario. En ocasiones, en blogs y foros, se le conoce como "flujo de cliente" o "flujo dirigido al cliente", porque el código en el cliente inicia la sesión de los usuarios y el código de cliente tiene acceso a un token del proveedor.

Una vez que se obtiene un token del proveedor, debe enviarse a App Service para su validación. Después de que App Service valida el token, crea un nuevo token de App Service que se devuelve al cliente. El SDK de cliente de Mobile Apps dispone de métodos auxiliares para administrar este intercambio y asociar automáticamente el token a todas las solicitudes en el back-end de la aplicación. El desarrollador también puede mantener una referencia al token del proveedor.

Para más información sobre el flujo de autenticación, consulte [Flujo de autenticación de App Service](../app-service/app-service-authentication-overview.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Autenticación sin proveedor de SDK

Si no desea configurar un SDK del proveedor, puede permitir que la característica de Azure App Service Mobile Apps para iniciar sesión automáticamente. El SDK del cliente de Mobile Apps abrirá una vista web al proveedor de su elección y permitirá que el usuario inicie sesión. En ocasiones, en blogs y foros, se conoce como "flujo de servidor" o "flujo dirigido al servidor", pues el servidor administra el proceso que inicia la sesión de los usuarios y el SDK del cliente nunca recibe el token del proveedor.

El código para iniciar este flujo se incluye en el tutorial de autenticación para cada plataforma. Al final del flujo, el SDK del cliente tiene un token de App Service que se adjunta automáticamente a todas las solicitudes para el back-end de la aplicación.

Para más información sobre el flujo de autenticación, consulte [Flujo de autenticación de App Service](../app-service/app-service-authentication-overview.md#authentication-flow). 
## <a name="more-resources"></a>Más recursos

Los siguientes tutoriales muestran cómo incorporar la autenticación a los clientes móviles con el [flujo dirigido al servidor](../app-service/app-service-authentication-overview.md#authentication-flow):

* [Incorporación de la autenticación a la aplicación iOS][iOS]
* [Adición de autenticación a una aplicación Android][Android]
* [Adición de autenticación a una aplicación Windows][Windows]
* [Adición de autenticación a una aplicación Xamarin.iOS][Xamarin.iOS]
* [Adición de autenticación a una aplicación Xamarin.Android][Xamarin.Android]
* [Add authentication to your Xamarin.Forms app][Xamarin.Forms] (Adición de autenticación a una aplicación Xamarin.Forms)
* [Adición de la autenticación a la aplicación de Cordova][Cordova]

Si desea usar el [flujo dirigido al cliente](../app-service/app-service-authentication-overview.md#authentication-flow) para Azure Active Directory, use los siguientes recursos:

* [Utilice la biblioteca de autenticación de Active Directory para iOS][ADAL-iOS]
* [Utilice la biblioteca de autenticación de Active Directory para Android][ADAL-Android]
* [Utilice la biblioteca de autenticación de Active Directory para Windows y Xamarin][ADAL-dotnet]

Si desea usar el [flujo dirigido al cliente](../app-service/app-service-authentication-overview.md#authentication-flow) para Facebook, use los siguientes recursos:

* [Uso del SDK de Facebook para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Si desea usar el [flujo dirigido al cliente](../app-service/app-service-authentication-overview.md#authentication-flow) para Twitter, use los siguientes recursos:

* [Uso de Fabric de Twitter para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Si desea usar el [flujo dirigido al cliente](../app-service/app-service-authentication-overview.md#authentication-flow) para Google, use los siguientes recursos:

* [Uso del SDK de inicio de sesión de Google para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

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
