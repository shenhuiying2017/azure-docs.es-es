---
title: "Autenticación basada en token (HTTP/2) para APNs en Azure Notification Hubs | Microsoft Docs"
description: "En este tema se explica cómo aprovechar la nueva autenticación de token para APNs."
services: notification-hubs
documentationcenter: .net
author: kpiteira
manager: erikre
editor: 
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 05/17/2017
ms.author: kapiteir
ms.openlocfilehash: 5a21bcd9f12fc3f96b17a556ba15526c35ababe2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="token-based-http2-authentication-for-apns"></a>Autenticación basada en token (HTTP/2) para APNs
## <a name="overview"></a>Información general
En este artículo se detalla cómo usar el nuevo protocolo HTTP/2 de APNs con la autenticación basada en token.

Entre las ventajas principales de usar el nuevo protocolo destacan las siguientes:
-   La generación de tokens es relativamente fácil (comparada con los certificados).
-   Ya no hay fechas de expiración: es usted quien controla los tokens de autenticación y su revocación.
-   Ahora las cargas pueden ser de hasta 4 KB.
- Los comentarios son sincrónicos.
-   Está en el protocolo más reciente de Apple: los certificados siguen usando el protocolo binario, que está marcado como en desuso.

Para usar este mecanismo nuevo, basta con que realice dos pasos que le llevarán un par de minutos:
1.  Obtenga la información necesaria en el portal de la cuenta de desarrollador de Apple.
2.  Configure el centro de notificaciones con la nueva información.

Ahora, todos los Notification Hubs están configurados para usar el nuevo sistema de autenticación con APNs. 

Tenga en cuenta que si antes usaba credenciales de certificado para APNs y ha realizado la migración:
- las propiedades de token sobrescriben su certificado en nuestro sistema,
- pero la aplicación sigue recibiendo notificaciones sin problemas.

## <a name="obtaining-authentication-information-from-apple"></a>Obtener información de autenticación de Apple
Para habilitar la autenticación basada en token, necesita las siguientes propiedades de la cuenta de desarrollador de Apple:
### <a name="key-identifier"></a>Identificador de clave
Puede obtener el identificador de clave en la página "Claves" de la cuenta de desarrollador de Apple.

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Identificador y nombre de la aplicación
El nombre de la aplicación está disponible a través de la página de identificadores de aplicaciones en la cuenta de desarrollador. 
![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

El identificador de la aplicación está disponible a través de la página de detalles de la pertenencia en la cuenta de desarrollador.
![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)


### <a name="authentication-token"></a>Token de autenticación
Puede descargar el token de autenticación después de generar un token para la aplicación. Para obtener más información sobre cómo generar este token, vea la [documentación para desarrolladores de Apple](http://help.apple.com/xcode/mac/current/#/dev11b059073?sub=dev1eb5dfe65).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Configurar el centro de notificaciones para usar la autenticación basada en token
### <a name="configure-via-the-azure-portal"></a>Configurar mediante Azure Portal
Para habilitar la autenticación basada en token en el portal, inicie sesión en Azure Portal y vaya al Centro de notificaciones > Servicios de notificación > Panel APNs. 

Hay una nueva propiedad: *Modo de autenticación*. La selección de tokens le permite actualizar el centro con todas las propiedades de token pertinentes.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

- Escriba las propiedades que ha recuperado de la cuenta de desarrollador de Apple. 
- Elija el modo de aplicación (producción o espacio aislado). 
- Haga clic en Guardar para actualizar las credenciales de APNs. 

### <a name="configure-via-management-api-rest"></a>Configurar mediante la API de administración (REST)

Puede usar nuestras [API de administración](https://msdn.microsoft.com/library/azure/dn495827.aspx) para actualizar el centro de notificaciones de modo que use la autenticación basada en token.
Use uno de los puntos de conexión correspondientes, en función de si la aplicación que está configurando es una aplicación de espacio aislado o de producción (según se especifique en la cuenta de desarrollador de Apple):

- Punto de conexión de espacio aislado: [https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
- Punto de conexión de producción: [https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> La autenticación basada en token requiere una versión de API de **04-2017 o posterior**.
> 
> 

A continuación se muestra un ejemplo de una solicitud PUT para actualizar un centro con autenticación basada en token:


        PUT https://{namespace}.servicebus.windows.net/{Notification Hub}?api-version=2017-04
          "Properties": {
            "ApnsCredential": {
              "Properties": {
                "KeyId": "<Your Key Id>",
                "Token": "<Your Authentication Token>",
                "AppName": "<Your Application Name>",
                "AppId": "<Your Application Id>",
                "Endpoint":"<Sandbox/Production Endpoint>"
              }
            }
          }
        

### <a name="configure-via-the-net-sdk"></a>Configurar mediante el SDK de .NET
Puede configurar el centro para que use la autenticación basada en token mediante nuestro [SDK de cliente más reciente](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8). 

A continuación se muestra un ejemplo de código que ilustra el uso correcto:


        NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
        string token = "YOUR TOKEN HERE";
        string keyId = "YOUR KEY ID HERE";
        string appName = "YOUR APP NAME HERE";
        string appId = "YOUR APP ID HERE";
        NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
        desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
        desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
        nm.UpdateNotificationHubAsync(desc);

## <a name="reverting-to-using-certificate-based-authentication"></a>Volver a usar la autenticación basada en certificados
Puede volver a usar en cualquier momento la autenticación basada en certificados. Para ello, use cualquiera de los métodos anteriores y pase las propiedades del certificado en lugar de las propiedades del token. Esta acción sobrescribe las credenciales almacenadas previamente.
