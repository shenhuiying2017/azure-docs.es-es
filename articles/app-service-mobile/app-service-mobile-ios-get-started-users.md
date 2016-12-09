---
title: "Incorporación de autenticación en iOS con Aplicaciones móviles de Azure"
description: "Obtenga información acerca de cómo usar las Aplicaciones móviles de Azure para autenticar a los usuarios de su aplicación iOS en una variedad de proveedores de identidades, incluidos AAD, Google, Facebook, Twitter y Microsoft."
services: app-service\mobile
documentationcenter: ios
author: ysxu
manager: yochayk
editor: 
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4df1f27e5cefe9813fdc79b96d42e5c5d033901c


---
# <a name="add-authentication-to-your-ios-app"></a>Incorporación de la autenticación a la aplicación iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

En este tutorial podrá agregar la autenticación al proyecto de [inicio rápido de iOS] mediante un proveedor de identidades compatible. Este tutorial está basado en el tutorial de [inicio rápido de iOS] , que debe completar primero.

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Registro de la aplicación para la autenticación y configuración del Servicio de aplicaciones
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Restricción de los permisos para los usuarios autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

En Xcode, presione **Ejecutar** para iniciar la aplicación. Se genera una excepción porque la aplicación intenta acceder al back-end como usuario sin autenticar, pero la tabla *TodoItem* ahora requiere autenticación.

## <a name="a-nameadd-authenticationaadd-authentication-to-app"></a><a name="add-authentication"></a>Incorporación de autenticación a la aplicación
[!INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]

<!-- URLs. -->

[inicio rápido de iOS]: app-service-mobile-ios-get-started.md

[Azure Portal]: https://portal.azure.com



<!--HONumber=Nov16_HO3-->


