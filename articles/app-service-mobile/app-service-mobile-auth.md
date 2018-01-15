---
title: "Autenticación y autorización en Azure Mobile Apps | Microsoft Docs"
description: "Referencia e información general conceptual de la característica de autenticación o autorización para Azure Mobile Apps"
services: app-service\mobile
documentationcenter: 
author: mattchenderson
manager: cfowler
editor: 
ms.assetid: a46dbf70-867d-48f6-8885-7f5207ad102e
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 90c11b09351f019c45f5f1b025d67947b69b3b0a
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="authentication-and-authorization-in-azure-mobile-apps"></a>Autenticación y autorización en Azure Mobile Apps
## <a name="what-is-app-service-authentication--authorization"></a>¿Qué es la autenticación o autorización de App Service?
> [!NOTE]
> Este tema se migrará al artículo consolidado [Autenticación y autorización en Azure App Service](../app-service/app-service-authentication-overview.md) , que abarca las aplicaciones web, móviles y de API.
> 
> 

La autenticación o autorización de App Service es una característica que permite que los usuarios inicien sesión en la aplicación sin necesidad de realizar cambios en el código del back-end de la misma. Ofrece una forma fácil de proteger su aplicación y trabajar con datos por usuario.

App Service usa la identidad federada, en la cual un **proveedor de identidades** ("IdP") de terceros almacena cuentas y autentica usuarios, mientras que la aplicación utiliza esta identidad en lugar de la suya. App Service admite cinco proveedores de identidades estándar: *Azure Active Directory*, *Facebook*, *Google*, *Cuenta Microsoft* y *Twitter*. También puede ampliar esta compatibilidad con sus aplicaciones integrando otro proveedor de identidades o su propia solución de identidad personalizada.

La aplicación puede aprovechar cualquier cantidad de estos proveedores de identidades, por lo que puede proporcionar opciones de inicio de sesión a sus usuarios finales.

Si desea comenzar inmediatamente, consulte uno de los siguientes tutoriales:

* [Incorporación de la autenticación a la aplicación iOS]
* [Adición de la autenticación a la aplicación Xamarin.iOS]
* [Adición de la autenticación a la aplicación Xamarin.Android]
* [Adición de la autenticación a la aplicación de Windows]

## <a name="how-authentication-works"></a>Funcionamiento de la autenticación
Para autenticarse con uno de los proveedores de identidades, primero debe configurar el proveedor de identidades para obtener información sobre su aplicación. A continuación, el proveedor de identidades le proporcionará identificadores y secretos que, a su vez, proporciona a la aplicación. De este modo se completa la relación de confianza y se permite a App Service validar las identidades proporcionadas al mismo.

Estos pasos se describen en los temas siguientes:

* [Configuración de la aplicación para usar el inicio de sesión de Azure Active Directory]
* [Configuración de la aplicación para usar el inicio de sesión de Facebook]
* [Configuración de la aplicación para usar el inicio de sesión de Google]
* [Configuración de la aplicación para usar el inicio de sesión de la cuenta Microsoft]
* [Configuración de la aplicación para usar el inicio de sesión de Twitter]

Una vez que se ha configurado todo en el back-end, puede modificar su cliente para iniciar sesión. Hay dos enfoques aquí:

* Permita que los usuarios inicien sesión en el SDK del cliente de Mobile Apps con una sola línea de código.
* Aproveche un SDK publicado por un proveedor de identidades determinado para establecer la identidad y después obtener acceso a App Service.

> [!TIP]
> La mayoría de las aplicaciones deben usar un SDK del proveedor para obtener una experiencia de inicio de sesión más nativa y aprovechar la compatibilidad de actualización y otras ventajas específicas del proveedor.
> 
> 

### <a name="how-authentication-without-a-provider-sdk-works"></a>Funcionamiento de la autenticación sin un SDK del proveedor
Si no desea configurar un SDK del proveedor, puede dejar que Mobile Apps realice el inicio de sesión por usted. El SDK del cliente de Mobile Apps abrirá una vista web al proveedor de su elección y completará el inicio de sesión. En ocasiones, en blogs y foros encontrará que se hace referencia a este como "flujo de servidor" o "flujo dirigido al servidor", pues el servidor administra el inicio de sesión y el SDK del cliente nunca recibe el token del proveedor.

El código necesario para iniciar este flujo se trata en el tutorial de autenticación para cada plataforma. Al final del flujo, el SDK del cliente tiene un token de App Service que se adjunta automáticamente a todas las solicitudes para el back-end.

### <a name="how-authentication-with-a-provider-sdk-works"></a>Funcionamiento de la autenticación con un SDK del proveedor
Trabajar con un SDK del proveedor permite que la experiencia de inicio de sesión interactúe de manera más estrecha con el sistema operativo de la plataforma donde se ejecuta la aplicación. También le proporciona un token del proveedor e información de usuario sobre el cliente, lo que facilita en gran medida el consumo de API de gráficos y la personalización de la experiencia del usuario. En ocasiones, en blogs y foros encontrará que se hace referencia a este como "flujo de cliente" o "flujo dirigido al cliente", pues el código del cliente controla el inicio de sesión y tiene acceso a un token del proveedor.

Una vez que se obtiene un token del proveedor, debe enviarse a App Service para su validación. Al final del flujo, el SDK del cliente tiene un token de App Service que se adjunta automáticamente a todas las solicitudes para el back-end. El desarrollador también puede mantener una referencia al token del proveedor si así lo desea.

## <a name="how-authorization-works"></a>Funcionamiento de la autorización
La autenticación o autorización de App Service expone varias opciones para **Acción por realizar cuando no se autentique la solicitud**. Antes de que el código reciba una solicitud determinada, puede hacer que App Service compruebe si la solicitud se ha autenticado y, si no es así, rechazarla e intentar que el usuario inicie sesión antes de probar de nuevo.

Una opción es el redireccionamiento de las solicitudes no autenticadas a uno de los proveedores de identidades. En realidad, en un explorador web, esto llevaría al usuario a una nueva página. Sin embargo, su cliente móvil no puede redirigirse de esta manera y las respuestas no autenticadas recibirán una respuesta HTTP del tipo *401 - No autorizado*. Dicho esto, la primera solicitud realizada por el cliente siempre debe ser para el punto de conexión de inicio de sesión y después podrá realizar llamadas a cualquier otra API. Si intenta llamar a otra API antes de iniciar sesión, el cliente recibirá un error.

Si desea tener un control pormenorizado sobre qué puntos de conexión requieren autenticación, también puede elegir "Ninguna acción (permitir solicitud)" para las solicitudes no autenticadas. En este caso, todas las decisiones de autenticación se aplazan al código de la aplicación. Además, gracias a esto puede permitir el acceso a usuarios específicos según las reglas de autorización personalizadas.

## <a name="documentation"></a>Documentación
Los siguientes tutoriales muestran cómo incorporar la autenticación a los clientes móviles con App Service:

* [Incorporación de la autenticación a la aplicación iOS]
* [Adición de la autenticación a la aplicación Xamarin.iOS]
* [Adición de la autenticación a la aplicación Xamarin.Android]
* [Adición de la autenticación a la aplicación de Windows]

En los siguientes tutoriales se muestra cómo configurar App Service para aprovechar los distintos proveedores de autenticación:

* [Configuración de la aplicación para usar el inicio de sesión de Azure Active Directory]
* [Configuración de la aplicación para usar el inicio de sesión de Facebook]
* [Configuración de la aplicación para usar el inicio de sesión de Google]
* [Configuración de la aplicación para usar el inicio de sesión de la cuenta Microsoft]
* [Configuración de la aplicación para usar el inicio de sesión de Twitter]

Si desea usar un sistema de identidades diferente a los proporcionados aquí, también puede aprovechar la [compatibilidad con la autenticación personalizada de vista previa en el SDK de servidor .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth).

[Incorporación de la autenticación a la aplicación iOS]: app-service-mobile-ios-get-started-users.md
[Adición de la autenticación a la aplicación Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started-users.md
[Adición de la autenticación a la aplicación Xamarin.Android]: app-service-mobile-xamarin-android-get-started-users.md
[Adición de la autenticación a la aplicación de Windows]: app-service-mobile-windows-store-dotnet-get-started-users.md

[Configuración de la aplicación para usar el inicio de sesión de Azure Active Directory]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Configuración de la aplicación para usar el inicio de sesión de Facebook]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[Configuración de la aplicación para usar el inicio de sesión de Google]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[Configuración de la aplicación para usar el inicio de sesión de la cuenta Microsoft]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Configuración de la aplicación para usar el inicio de sesión de Twitter]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
