---
title: "Configuración del proveedor de identidades de GitHub: Azure AD B2C | Microsoft Docs"
description: "Proporcione funciones de registro e inicio de sesión a los clientes con cuentas de GitHub en las aplicaciones protegidas con Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 5518e135-36b3-4f86-9a01-be25f021ed22
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: parja
ms.openlocfilehash: e827bea257309f6d6dda1af4e68ccda5a26d30ef
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-github-accounts"></a>Azure Active Directory B2C: provisión de registro e inicio de sesión a los consumidores con cuentas de GitHub

> [!NOTE]
> Esta característica se encuentra en su versión preliminar.
> 

En este artículo se muestra cómo habilitar el inicio de sesión de los usuarios con una cuenta de GitHub.

## <a name="create-a-github-oauth-application"></a>Creación de una aplicación GitHub OAuth

Para usar GitHub como proveedor de identidades en Azure AD B2C, debe crear una aplicación GitHub OAuth y suministrarle los parámetros correctos.

1. Inicie sesión en GitHub y vaya a la [configuración del desarrollador de GitHub](https://github.com/settings/developers).
1. Haga clic en **New OAuth App** (Nueva aplicación OAuth)
1. Escriba un **nombre de aplicación** y la **dirección URL de la página principal**.
1. Como **URL de devolución de llamada de autorización** escriba `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Reemplace **{tenant}** por el nombre del inquilino de Azure AD B2C (por ejemplo, B2C.onmicrosoft.com).

    >[!NOTE]
    >El valor de "tenant" debe estar en minúsculas en la dirección **URL de inicio de sesión**.

1. Haga clic en **Register application** (Registrar aplicación).
1. Guarde los valores de **Client Id** (Id. de cliente) y **Client secret** (Secreto de cliente). Los necesitará en la siguiente sección.

## <a name="configure-github-as-an-identity-provider-in-your-azure-ad-b2c-tenant"></a>Configuración de GitHub como proveedor de identidades en el inquilino de Azure AD B2C

1. Siga estos pasos para [ir a la hoja de características de B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) de Azure Portal.
1. En la hoja de características B2C, haga clic en **Proveedores de identidades**.
1. Haga clic en **+Agregar** en la parte superior de la hoja.
1. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, escriba "GitHub".
1. Haga clic en **Identity provider type** (Tipo de proveedor de identidades), seleccione **GitHub** y haga clic en **OK** (Aceptar).
1. Haga clic en **Set up this identity provider** (Configurar este proveedor de identidades) y escriba el identificador de cliente y el secreto de cliente de la aplicación GitHub OAuth que copió anteriormente.
1. Haga clic en **OK** (Aceptar) y en **Create** (Crear) para guardar la configuración de GitHub.

## <a name="next-steps"></a>pasos siguientes

Cree o edite una [directiva integrada](active-directory-b2c-reference-policies.md) y agregue GitHub como proveedor de identidades.