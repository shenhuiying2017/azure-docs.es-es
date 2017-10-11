---
title: "Introducción a Azure App Service Mobile Apps para aplicaciones Xamarin.iOS | Microsoft Docs"
description: "Siga este tutorial para empezar a usar Aplicaciones móviles para el desarrollo de Xamarin.iOS."
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: syntaxc4
ms.openlocfilehash: 8dc965df2cd45366970effb29f246b0045a94717
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="create-a-xamarinios-app"></a>Creación de una aplicación Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Información general
En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube a una aplicación móvil Xamarin.iOS con un back-end de Aplicaciones móviles de Azure.  Creará tanto un back-end de aplicación móvil nuevo como una aplicación Xamarin.iOS simple de la *lista de tareas pendientes* que almacene los datos de la aplicación en Azure.

Completar este tutorial es un requisito previo para todos los demás tutoriales de Xamarin.iOS sobre cómo usar la característica Aplicaciones móviles en el Servicio de aplicaciones de Azure.

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, debe cumplir los siguientes requisitos previos:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, regístrese para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio con Xamarin. Consulte [Configuración e instalación](https://msdn.microsoft.com/library/mt613162.aspx) para obtener instrucciones.
* Un equipo Mac con Xcode v7.0 o versiones posteriores y Xamarin Studio Community instalados. Consulte [Configuración e instalación](https://msdn.microsoft.com/library/mt613162.aspx) y [Configuración, instalación y comprobaciones para usuarios de Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

## <a name="create-an-azure-mobile-app-backend"></a>Creación de un nuevo back-end de Azure Mobile App
Siga estos pasos para crear un back-end de Mobile App.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Configuración del proyecto de servidor
Ahora ha aprovisionado un back-end de aplicación móvil de Azure que puede usarse por las aplicaciones del cliente móvil. Después, descargue un proyecto de servidor para un back-end de "lista de tareas" sencillo y publíquelo en Azure.

Para configurar el proyecto de servidor para que use el back-end de Node.js o. NET, siga los pasos que se indican a continuación.

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>Descarga y ejecución de la aplicación Xamarin.iOS
1. En el equipo Mac, abra el [Portal de Azure] en una ventana del explorador.
2. En la hoja de configuración de la aplicación móvil, haga clic en **Introducción** > **Xamarin.iOS**. En el paso 3, haga clic en **Crear una nueva aplicación** , en caso de que no esté seleccionado.  A continuación, haga clic en el botón **Descargar** .

      Se descarga una aplicación cliente que se conecta a su back-end móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.
3. Extraiga el proyecto descargado y, después, ábralo en Xamarin Studio (o Visual Studio).

    ![][9]

    ![][8]
4. Presione la tecla F5 para compilar el proyecto e iniciar la aplicación en el emulador de iPhone.
5. En la aplicación, escriba texto significativo, como *Aprender Xamarin*, y haga clic en el botón **+**.

    ![][10]

    Los datos de la solicitud se insertan en la tabla TodoItem. El back-end de aplicación móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

> [!NOTE]
> Puede revisar el código que obtiene acceso al back-end de aplicación móvil para consultar e insertar datos en el archivo de C# QSTodoService.cs.
>
>

## <a name="next-steps"></a>Pasos siguientes
* [Add Offline Sync to your app](app-service-mobile-xamarin-ios-get-started-offline-data.md) (Incorporación de sincronización sin conexión a la aplicación)
* [Adición de la autenticación a la aplicación Xamarin.Android ](app-service-mobile-xamarin-ios-get-started-users.md)
* [Agregar notificaciones push a la aplicación de Xamarin.Android](app-service-mobile-xamarin-ios-get-started-push.md)
* [Uso del cliente administrado para Aplicaciones móviles de Azure](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Portal de Azure]: https://portal.azure.com/
