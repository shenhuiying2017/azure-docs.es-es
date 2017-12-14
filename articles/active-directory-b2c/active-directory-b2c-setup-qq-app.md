---
title: "Azure Active Directory B2C: configuración de QQ | Microsoft Docs"
description: "Proporcione funciones de registro e inicio de sesión a los consumidores con cuentas de QQ en las aplicaciones protegidas por Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 18c2cf94-8004-4de1-81c2-e45be65ce12d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: d4cc26d4f206baf9137feae0825b1f9fa5a7c8d6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Azure Active Directory B2C: provisión de registro e inicio de sesión a los usuarios con cuentas de QQ

> [!NOTE]
> Esta característica se encuentra en su versión preliminar.
> 

## <a name="create-a-qq-application"></a>Creación de una aplicación de QQ

Para usar QQ como proveedor de identidades en Azure Active Directory (Azure AD) B2C, debe crear una aplicación de Google+ y suministrarle los parámetros correctos. Necesita una cuenta de QQ para hacerlo. Si no tiene una, puede obtenerla en [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registro para el programa para desarrolladores de QQ

1. Vaya al [portal para desarrolladores de QQ](http://open.qq.com) e inicie sesión con sus credenciales de cuenta de QQ.
2. Después de iniciar sesión, vaya a [http://open.qq.com/reg](http://open.qq.com/reg) para registrarse como un programador.
3. En el menú, seleccione**个人** (desarrollador individual).
4. Escriba la información necesaria en el formulario y haga clic en **下一步** (paso siguiente).
5. Complete el proceso de comprobación de correo electrónico.

> [!NOTE]
> Debe esperar unos días para recibir la aprobación después de registrarse como desarrollador. 

### <a name="register-a-qq-application"></a>Registro de una aplicación QQ

1. Vaya a [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Haga clic en **应用管理** (administración de la aplicación).
3. Haga clic en **创建应用** (crear la aplicación).
4. Escriba la información de la aplicación necesaria.
5. Haga clic en **创建应用** (crear la aplicación).
6. Escriba la información necesaria.
7. Para el campo **授权回调域** (dirección URL de devolución de llamada), escriba `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Por ejemplo, si `tenant_name` es contoso.onmicrosoft.com, establezca la dirección URL para que sea `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Haga clic en **创建应用** (crear la aplicación).
9. En la página de confirmación, haga clic en **应用管理** (administración de la aplicación) para volver a la página de administración de la aplicación.
10. Haga clic en **查看** (ver) junto a la aplicación que acaba de crear.
11. Haga clic en **修改** (editar).
12. En la parte superior de la página, copie el **id. de la aplicación** y la **clave de la aplicación**.

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>Configuración de QQ como proveedor de identidades del inquilino
1. Siga estos pasos para [ir a la hoja de características de B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) del Portal de Azure.
2. En la hoja de características B2C, haga clic en **Proveedores de identidades**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, escriba "QQ".
5. Haga clic en **Tipo de proveedor de identidades**, seleccione **QQ** y haga clic en **Aceptar**.
6. Haga clic en **Configurar este proveedor de identidades**.
7. Escriba la **Clave de la aplicación** que copió anteriormente como **Id. de cliente**.
8. Escriba el **Secreto de la aplicación** que copió anteriormente como **Secreto de la aplicación**.
9. Haga clic en **Aceptar** y luego en **Crear** para guardar la configuración de QQ.

