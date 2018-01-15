---
title: "Publicación de aplicaciones cliente nativas: Azure AD | Microsoft Docs"
description: "Explica cómo habilitar las aplicaciones cliente nativas para comunicarse con el conector del proxy de la aplicación de Azure AD para proporcionar acceso remoto seguro a las aplicaciones locales."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: f0cae145-e346-4126-948f-3f699747b96e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 2be62c00d144e47cef8ea4df5aa82554f2bbcc18
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Habilitación de las aplicaciones cliente nativas para interactuar con el proxy de la aplicación

Además de las aplicaciones web, el proxy de la aplicación de Azure Active Directory también puede utilizarse para publicar las aplicaciones cliente nativas que se configuran con la Biblioteca de autenticación de Azure AD (ADAL). Las aplicaciones cliente nativas son distintas de las aplicaciones web porque se instalan en un dispositivo, mientras se tiene acceso a las aplicaciones web a través de un explorador. 

El proxy de la aplicación admite aplicaciones cliente nativas al aceptar tokens emitidos por Azure AD que se envían en el encabezado. El servicio de proxy de la aplicación realiza la autenticación en nombre de los usuarios. Esta solución no utiliza tokens de aplicación para la autenticación. 

![Relación entre los usuarios finales, Azure Active Directory y las aplicaciones publicadas](./media/active-directory-application-proxy-native-client/richclientflow.png)

Utilice la biblioteca de Autenticación de Azure AD, que se encarga de la autenticación y admite muchos de los entornos de cliente, para publicar aplicaciones nativas. El proxy de la aplicación se adapta a la [aplicación nativa para el escenario de Web API](develop/active-directory-authentication-scenarios.md#native-application-to-web-api). 

Este artículo le guiará por los cuatro pasos para publicar una aplicación nativa con el proxy de aplicación y la biblioteca de Autenticación de Azure AD. 

## <a name="step-1-publish-your-application"></a>Paso 1: Publicación de la aplicación
Publique su aplicación de proxy al igual que haría con cualquier otra aplicación y asigne a los usuarios el acceso a la aplicación. Para más información, consulte [Publicación de aplicaciones mediante el proxy de aplicación de Azure AD](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Paso 2: Configuración de la aplicación
Configure su aplicación nativa de la manera siguiente:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Vaya a **Azure Active Directory** > **Registros de aplicaciones**.
3. Seleccione **Nuevo registro de aplicaciones**.
4. Especifique un nombre para la aplicación, seleccione **Nativo** como tipo de aplicación y proporcione el URI de redirección para la aplicación. 

   ![Creación de un registro de aplicaciones](./media/active-directory-application-proxy-native-client/create.png)
5. Seleccione **Crear**.

Para más información sobre cómo crear un registro de aplicación, consulte [Integración de aplicaciones con Azure Active Directory](.//develop/active-directory-integrating-applications.md).


## <a name="step-3-grant-access-to-other-applications"></a>Paso 3: Conceder acceso a otras aplicaciones
Habilite la aplicación nativa para que se exponga a otras aplicaciones en el directorio:

1. Todavía en **Registros de aplicaciones**, seleccione la aplicación nativa que acaba de crear.
2. Seleccione **Permisos necesarios**.
3. Seleccione **Agregar**.
4. Abra el primer paso, **Seleccionar una API**.
5. Use la barra de búsqueda para encontrar la aplicación de proxy de la aplicación que ha publicado en la primera sección. Elija la aplicación y haga clic en **Seleccionar**. 

   ![Búsqueda de la aplicación de proxy](./media/active-directory-application-proxy-native-client/select_api.png)
6. Abra el segundo paso, **Seleccionar permisos**.
7. Utilice la casilla para conceder el acceso de la aplicación nativa a la aplicación de proxy y, después, haga clic en **Seleccionar**.

   ![Concesión de acceso a la aplicación de proxy](./media/active-directory-application-proxy-native-client/select_perms.png)
8. Seleccione **Listo**.


## <a name="step-4-edit-the-active-directory-authentication-library"></a>Paso 4: Editar la biblioteca de autenticación de Active Directory
Edite el código de la aplicación nativa en el contexto de autenticación de Active Directory Authentication Library (ADAL) para incluir el siguiente texto:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = authContext.AcquireToken("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

Las variables del código de ejemplo deben reemplazarse como sigue:

* **Tenant ID** puede encontrarse en Azure Portal. Vaya a **Azure Active Directory** > **Propiedades** y copie el identificador de directorio. 
* **External URL** es la dirección URL de front-end que ha especificado en la aplicación proxy. Para buscar este valor, vaya a la sección **Proxy de la aplicación** de la aplicación de proxy.
* La variable **App ID** de la aplicación nativa se encuentra en la página **Propiedades** de la aplicación nativa.
* **Redirect URI of the native app** se puede encontrar en la página**URI de redirección** de la aplicación nativa.

Una vez que se edita la ADAL con estos parámetros, los usuarios deberían poder autenticarse en las aplicaciones cliente nativas incluso si están fuera de la red corporativa. 

## <a name="next-steps"></a>pasos siguientes

Para más información sobre el flujo de la aplicación nativa, consulte el escenario de [Aplicación nativa a API web](develop/active-directory-authentication-scenarios.md#native-application-to-web-api).

Más información sobre cómo configurar el [inicio de sesión único para el proxy de la aplicación](application-proxy-sso-overview.md)
