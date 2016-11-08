---
title: Creación de una aplicación de Apache Cordova en las Aplicaciones móviles del Servicio de aplicaciones de Azure| Microsoft Docs
description: Siga este tutorial para aprender a usar back-ends de aplicación móvil de Azure para el desarrollo de Apache Cordova.
services: app-service\mobile
documentationcenter: javascript
author: adrianhall
manager: erikre
editor: ''
tags: ''
keywords: cordova, javascript, móvil, cliente

ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha

---
# Creación de una aplicación de Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Información general
En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación móvil de Apache Cordova usando un back-end de la aplicación móvil de Azure. Creará un back-end de aplicación móvil nuevo y una sencilla aplicación de *lista de tareas pendientes* de Apache Cordova que almacena los datos de la aplicación en Azure.

Completar este tutorial es un requisito previo para todos los demás tutoriales de Apache Cordova acerca de cómo usar la característica Aplicaciones móviles en el Servicio de aplicaciones de Azure.

## Requisitos previos
Para completar este tutorial, necesitará lo siguiente:

* Un equipo con [Visual Studio Community 2015], o cualquier versión posterior.
* [Visual Studio Tools para Apache Cordova].
* Una [cuenta de Azure activa](https://azure.microsoft.com/pricing/free-trial/).

También puede omitir Visual Studio y utilizar la línea de comandos de Apache Cordova directamente. Esto resulta útil si realiza el tutorial en un equipo Mac. Este tutorial no aborda la compilación de aplicaciones de cliente de Apache Cordova con la línea de comandos.

## Creación de un nuevo back-end de Aplicaciones móviles de Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[Visualización de un vídeo donde se muestren pasos similares](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## Configuración del proyecto de servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Descarga y ejecución de la aplicación Apache Cordova
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## Pasos siguientes
Ahora que ha completado este tutorial de inicio rápido, continúe con uno de los siguientes tutoriales:

* [Agregar autenticación] a su aplicación de Apache Cordova.
* [Agregar notificaciones push] a su aplicación de Apache Cordova.

Obtenga más información acerca de los conceptos principales con el servicio de aplicaciones de Azure.

* [Autenticación]
* [Notificaciones de inserción]

Obtenga información sobre cómo usar los SDK.

* [SDK de Apache Cordova]
* [SDK de servidor ASP.NET]
* [SDK de servidor Node.js]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools para Apache Cordova]: https://www.visualstudio.com/es-ES/features/cordova-vs.aspx
[Agregar autenticación]: app-service-mobile-cordova-get-started-users.md
[Agregar notificaciones push]: app-service-mobile-cordova-get-started-push.md
[Autenticación]: app-service-mobile-auth.md
[Notificaciones de inserción]: ../notification-hubs/notification-hubs-push-notification-overview.md
[SDK de Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[SDK de servidor ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[SDK de servidor Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_1005_2016-->