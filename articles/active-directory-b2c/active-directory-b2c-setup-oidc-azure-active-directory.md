---
title: 'Azure Active Directory B2C: agregar un proveedor de Azure AD mediante directivas integradas | Microsoft Docs'
description: Obtenga información sobre cómo agregar un proveedor de identidades de Open ID Connect (Azure AD)
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 7dac9545-d5f1-4136-a04d-1c5740aea499
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/27/2018
ms.author: parja
ms.openlocfilehash: 52a752df9cf199acf39596f49e7368bce27a8158
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/30/2018
ms.locfileid: "32309020"
---
# <a name="azure-active-directory-b2c-sign-in-using-azure-ad-accounts-through-a-built-in-policy"></a>Azure Active Directory B2C: iniciar sesión con cuentas de Azure AD mediante una directiva integrada

>[!NOTE]
> Esta característica está en versión preliminar pública. No use esta característica en entornos de producción.

En este artículo se muestra cómo habilitar el inicio de sesión para los usuarios de una organización concreta de Azure Active Directory (Azure AD) mediante directivas integradas.

## <a name="create-an-azure-ad-app"></a>Creación de una aplicación de Azure AD

Para habilitar el inicio de sesión para los usuarios de una organización específica de Azure AD, es preciso registrar una aplicación en el inquilino de Azure AD de la organización.

>[!NOTE]
> En las instrucciones siguientes, usamos "contoso.com" para el inquilino de Azure AD de la organización y "fabrikamb2c.onmicrosoft.com" como inquilino de Azure AD B2C.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
1. En la barra superior, seleccione su cuenta. En la lista **Directorio**, elija el inquilino de Azure AD de la organización en el que quiere registrar la aplicación (contoso.com).
1. Seleccione **Todos los servicios** en el panel izquierdo y busque "Registros de aplicaciones".
1. Seleccione **Nuevo registro de aplicaciones**.
1. Escriba el nombre de la aplicación (por ejemplo, `Azure AD B2C App`).
1. En Tipo de aplicación, seleccione **Aplicación web o API**.
1. En **Dirección URL de inicio de sesión**, escriba la dirección URL siguiente, donde `yourtenant` se sustituye por el nombre del inquilino de Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >El valor de "yourtenant" debe estar en minúsculas en **URL de inicio de sesión**.

    ```Console
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Guarde el identificador de aplicación, que luego se va a usar en la sección siguiente como identificador de cliente.
1. En la hoja **Configuración**, seleccione **Claves**.
1. Escriba una **Descripción de la clave** en la sección **Contraseñas** y establezca la **Duración** en "Nunca expira". 
1. Haga clic en **Guardar** y anote el **Valor** de la clave resultante, que se va a usar en la sección siguiente como secreto de cliente.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Configurar Azure AD como proveedor de identidades del inquilino

1. En la barra superior, seleccione su cuenta. En la lista **Directorio**, elija el inquilino de Azure AD B2C (fabrikamb2c.onmicrosoft.com).
1. [Vaya al menú de configuración de Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) en Azure Portal.
1. En el menú de configuración de Azure AD B2C, haga clic en **Proveedores de identidades**.
1. Haga clic en **+Agregar** en la parte superior de la hoja.
1. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, escriba "Contoso Azure AD".
1. Haga clic en **Tipo de proveedor de identidades**, seleccione **Open ID Connect** y haga clic en **Aceptar**.
1. Haga clic en **Configurar este proveedor de identidades**.
1. En **URL de metadatos**, escriba la dirección URL siguiente, donde `yourtenant` se sustituye por el nombre del inquilino de Azure AD (por ejemplo, `contoso.com`):

    ```Console
    https://login.microsoftonline.com/yourtenant/.well-known/openid-configuration
    ```
1. En **Id. de cliente** y **Secreto de cliente**, escriba el identificador de aplicación y la clave de la sección anterior.
1. Mantenga el valor predeterminado de **Ámbito**, que debe establecerse en `openid`.
1. Mantenga el valor predeterminado de **Tipo de respuesta**, que debe establecerse en `code`.
1. Mantenga el valor predeterminado de **Modo de respuesta**, que debe establecerse en `form_post`.
1. También puede escribir un valor en **Dominio** (por ejemplo, `ContosoAD`). Este es el valor que se usa al hacer referencia a este proveedor de identidades mediante *domain_hint* en la solicitud. 
1. Haga clic en **OK**.
1. Haga clic en **Asignar las notificaciones de este proveedor de identidades**.
1. En **Id. de usuario**, escriba `oid`.
1. En **Nombre para mostrar**, escriba `name`.
1. En **Nombre propio**, escriba `given_name`.
1. En **Apellido**, escriba `family_name`.
1. En **Correo electrónico**, escriba `unique_name`.
1. Haga clic en **Aceptar** y luego en **Crear** para guardar la configuración.

## <a name="next-steps"></a>Pasos siguientes

Agregar el proveedor de identidades de Azure AD recién creado a una directiva integrada y proporcionar comentarios a [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
