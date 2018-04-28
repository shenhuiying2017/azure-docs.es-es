---
title: Configuración de Twitter para Azure AD B2C | Microsoft Docs
description: Proporcione funciones de registro e inicio de sesión a los clientes con cuentas de Twitter en las aplicaciones protegidas por Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/17/2018
ms.author: davidmu
ms.openlocfilehash: 40e4c5549414765dabc6f37c5ffb5aea519ae673
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>Proporcione registro e inicio de sesión a los usuarios con cuentas de Twitter mediante Azure AD B2C

## <a name="create-a-twitter-application"></a>Crear una aplicación de Twitter
Para usar Twitter como proveedor de identidades en Azure Active Directory (Azure AD) B2C, debe crear una aplicación de Twitter y suministrarle los parámetros correctos. Necesita una cuenta de Twitter para hacerlo. Si no tiene ninguna, puede obtener una en [https://twitter.com/signup](https://twitter.com/signup).

1. Vaya a [Aplicaciones de Twitter](https://apps.twitter.com/) e inicie sesión con sus credenciales.
2. Haga clic en **Crear nueva aplicación**. 
3. En el formulario, proporcione un valor para **Nombre**, **Descripción** y **Sitio web**.
4. Para la **URL de devolución de llamada**, escriba `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Asegúrese de reemplazar **{tenant}** por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com).
5. Active la casilla para aceptar el **Acuerdo para desarrolladores** y haga clic en **Crear su aplicación de Twitter**.
6. Una vez creada la aplicación, haga clic en **Claves y tokens de acceso**.
7. Copie el valor de **Clave de consumidor** y **Secreto de consumidor**. Necesitará los dos para configurar Twitter como proveedor de identidades de su inquilino.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configuración de Twitter como proveedor de identidades del inquilino
1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global del inquilino de Azure AD B2C. 
2. Para cambiar al inquilino de Azure AD B2C, seleccione el directorio Azure AD B2C en la esquina superior derecha del portal.
3. Haga clic en **Todos los servicios**y, a continuación, seleccione **Azure AD B2C** de la lista de servicios de **Seguridad e identidad**.
4. Haga clic en **Proveedores de identidades**.
4. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, escriba "Twitter".
5. Haga clic en **Tipo de proveedor de identidades**, seleccione **Twitter (versión preliminar)** y haga clic en **Aceptar**.
6. Haga clic en **Configurar este proveedor de identidades** y escriba la **Clave de consumidor** de Twitter para el **Id. de cliente** y el **Secreto de consumidor** de Twitter para el **Secreto de cliente**.
7. Haga clic en **Aceptar** y luego en **Crear** para guardar la configuración de Twitter.

## <a name="next-steps"></a>Pasos siguientes

Cree o edite una [directiva integrada](active-directory-b2c-reference-policies.md) y agregue Twitter como proveedor de identidades.