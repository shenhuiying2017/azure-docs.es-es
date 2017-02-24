---
title: "Aplicaciones compatibles con notificaciones: proxy de aplicación de Azure AD | Microsoft Docs"
description: "En este artículo se explica cómo publicar aplicaciones de ASP.NET locales que acepten notificaciones ADFS para proteger el acceso remoto a sus usuarios."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 91e6211b-fe6a-42c6-bdb3-1fff0312db15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 081e45e0256134d692a2da7333ddbaafc7366eaa
ms.openlocfilehash: ff07a52f6a503f07f5919b63f345878571742cac


---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Trabajar con las aplicaciones para notificaciones en Proxy de aplicación
Las aplicaciones para notificaciones realizan una redirección al servicio de token de seguridad (STS), que a su vez solicita las credenciales del usuario a cambio de un token antes de redirigir al usuario a la aplicación. Para permitir que el proxy de aplicación funcione con estas redirecciones, deben realizarse los siguientes pasos.

## <a name="prerequisites"></a>Requisitos previos
Antes de realizar este procedimiento, asegúrese de que el STS al que redirige la aplicación para notificaciones está disponible fuera de la red local.

## <a name="azure-classic-portal-configuration"></a>Configuración del Portal de Azure clásico
1. Publique la aplicación según las instrucciones de [Publicar aplicaciones con el proxy de aplicación](active-directory-application-proxy-publish.md).
2. En la lista de aplicaciones, seleccione la aplicación para notificaciones y haga clic en **Configurar**.
3. Si elige **Acceso directo** como su **Método de autenticación previa**, asegúrese de seleccionar **HTTPS** como su esquema de **Dirección URL externa**.
4. Si elige **Azure Active Directory** como **Método de autenticación previa**, seleccione **Ninguno** como **Método de autenticación interno**.

## <a name="adfs-configuration"></a>Configuración de AD FS
1. Abra Administración de AD FS.
2. Vaya a **Relying Party Trusts** (Relaciones de confianza de usuarios de confianza), haga clic con el botón derecho en la aplicación que va a publicar con el proxy de aplicación y seleccione **Propiedades**.  

   ![Relaciones de confianza para usuario autenticado: haga clic con el botón derecho en el nombre de la aplicación (captura de pantalla)](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  

3. En la pestaña **Puntos de conexión**, en **Tipo de punto de conexión**, seleccione **WS-Federation**.
4. En **Trusted URL** (Dirección URL de confianza), escriba la dirección URL que indicó en Proxy de aplicación en **Dirección URL externa** y haga clic en **Aceptar**.  

   ![Agregar un extremo: establezca el valor de Dirección URL de confianza - captura de pantalla](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Pasos siguientes
* [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
* [Solucionar los problemas que tiene con el Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)
* [Habilitación de las aplicaciones de cliente nativo para interactuar con el proxy de aplicación](active-directory-application-proxy-native-client.md)





<!--HONumber=Feb17_HO1-->


