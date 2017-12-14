---
title: "Azure Active Directory B2C: configuración de WeChat | Microsoft Docs"
description: "Proporcione funciones de registro e inicio de sesión a los consumidores con cuentas de WeChat en las aplicaciones protegidas por Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: d2424c66-ba68-4d82-847e-d137683527b0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: 1cc1de1926edd28d09a3779da9595ef22491d0d9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>Azure Active Directory B2C: provisión de registro e inicio de sesión a los usuarios con cuentas de WeChat

> [!NOTE]
> Esta característica se encuentra en su versión preliminar.
> 

## <a name="create-a-wechat-application"></a>Creación de una aplicación de WeChat

Para usar WeChat como proveedor de identidades en Azure Active Directory (Azure AD) B2C, debe crear una aplicación de WeChat y suministrarle los parámetros correctos. Necesita una cuenta de WeChat para hacerlo. Si no tiene una, puede obtenerla registrándose a través de una de sus aplicaciones móviles o mediante su número de QQ. A continuación, registre su cuenta con el programa para desarrolladores de WeChat. Puede encontrar más información [aquí](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html):

### <a name="register-a-wechat-application"></a>Registro de una aplicación de WeChat

1. Vaya a [https://open.weixin.qq.com/](https://open.weixin.qq.com/) e inicie sesión.
2. Haga clic en **管理中心** (centro de administración).
3. Siga los pasos necesarios para registrar una nueva aplicación.
4. Para **授权回调域** (dirección URL de devolución de llamada), escriba `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Por ejemplo, si `tenant_name` es contoso.onmicrosoft.com, establezca la dirección URL para que sea `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Busque y copie el **id. de la aplicación** y la **clave de la aplicación**. Los necesitará más adelante.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configuración de WeChat como proveedor de identidades del inquilino
1. Siga estos pasos para [ir a la hoja de características de B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) del Portal de Azure.
2. En la hoja de características B2C, haga clic en **Proveedores de identidades**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, escriba "WeChat".
5. Haga clic en **Tipo de proveedor de identidades**, seleccione **WeChat** y haga clic en **Aceptar**.
6. Haga clic en **Configurar este proveedor de identidades**.
7. Escriba la **Clave de la aplicación** que copió anteriormente como **Id. de cliente**.
8. Escriba el **Secreto de la aplicación** que copió anteriormente como **Secreto de la aplicación**.
9. Haga clic en **OK** (Aceptar) y luego en **Create** (Crear) para guardar la configuración de WeChat.

