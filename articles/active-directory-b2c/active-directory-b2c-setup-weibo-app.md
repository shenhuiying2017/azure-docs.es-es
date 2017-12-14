---
title: "Azure Active Directory B2C: configuración de Weibo | Microsoft Docs"
description: "Proporcione funciones de registro e inicio de sesión a los consumidores con cuentas de Weibo en las aplicaciones protegidas por Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 1860de34-94cb-4ceb-851e-102f930f7230
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: 320d7c57035d3b3ecdb4e03d80141a58cf429461
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>Azure Active Directory B2C: provisión de registro e inicio de sesión a los usuarios con cuentas de Weibo

> [!NOTE]
> Esta característica se encuentra en su versión preliminar. No utilice este proveedor de identidades en su entorno de producción.
> 

## <a name="create-a-weibo-application"></a>Creación de una aplicación de Weibo

Para usar Weibo como proveedor de identidades en Azure Active Directory (Azure AD) B2C, debe crear una aplicación de Weibo y suministrarle los parámetros correctos. Necesita una cuenta de Weibo para hacerlo. Si no tiene una, puede obtenerla en [http://weibo.com/signup/signup.php?lang=en-us](http://weibo.com/signup/signup.php?lang=en-us).

### <a name="register-for-the-weibo-developer-program"></a>Registro para el programa para desarrolladores de Weibo

1. Vaya al [portal para desarrolladores de Weibo](http://open.weibo.com/) e inicie sesión con sus credenciales de cuenta de Weibo.
2. Después de iniciar sesión, haga clic en el nombre para mostrar en la esquina superior derecha.
3. En la lista desplegable, seleccione **编辑开发者信息** (editar la información para desarrolladores).
4. Escriba la información necesaria en el formulario y haga clic en **提交** (enviar).
5. Complete el proceso de comprobación de correo electrónico.
6. Vaya a la [página de comprobación de identidad](http://open.weibo.com/developers/identity/edit).
7. Escriba la información necesaria en el formulario y haga clic en **提交** (enviar).

### <a name="register-a-weibo-application"></a>Registro de una aplicación de Weibo

1. Vaya a la [página de registro de la aplicación de Weibo](http://open.weibo.com/apps/new).
2. Escriba la información de aplicación necesaria.
3. Haga clic en **创建** (crear).
4. Copie los valores de **Clave de la aplicación** y **Secreto de la aplicación**. Lo necesitará más adelante.
5. Cargue las fotos y escriba la información necesarias.
6. Haga clic en **保存以上信息** (guardar).
7. Haga clic en **高级信息** (información avanzada).
8. Haga clic en **编辑** (editar) junto al campo de OAuth2.0 **授权设置** (dirección URL de redireccionamiento).
9. Escriba `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` para OAuth2.0 **授权设置** (dirección URL de redireccionamiento). Por ejemplo, si `tenant_name` es contoso.onmicrosoft.com, establezca la dirección URL para que sea `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Haga clic en **提交** (enviar).  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>Configuración de Weibo como proveedor de identidades del inquilino
1. Siga estos pasos para [ir a la hoja de características de B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) del Portal de Azure.
2. En la hoja de características B2C, haga clic en **Proveedores de identidades**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, escriba "Weibo".
5. Haga clic en **Tipo de proveedor de identidades**, seleccione **Weibo** y haga clic en **Aceptar**.
6. Haga clic en **Configurar este proveedor de identidades**.
7. Escriba la **Clave de la aplicación** que copió anteriormente como **Id. de cliente**.
8. Escriba el **Secreto de la aplicación** que copió anteriormente como **Secreto de la aplicación**.
9. Haga clic en **OK** (Aceptar) y luego en **Create** (Crear) para guardar la configuración de Weibo.

