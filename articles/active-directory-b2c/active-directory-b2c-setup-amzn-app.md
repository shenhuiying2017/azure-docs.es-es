---
title: "Azure Active Directory B2C: configuración de Amazon | Microsoft Docs"
description: "Proporcionar registro e inicio de sesión a los consumidores con cuentas de Amazon en las aplicaciones protegidas por Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 77c099bb-a005-4d75-87f9-f61e3de48725
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: dcc97e1b7f6287bd7692c52bf068950065a26572
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure Active Directory B2C: proporcionar a los consumidores registro e inicio de sesión con cuentas de Amazon
## <a name="create-an-amazon-application"></a>Creación de una aplicación de Amazon
Para usar Amazon como proveedor de identidades en Azure Active Directory (Azure AD) B2C, debe crear una aplicación de Amazon y suministrarle los parámetros correctos. Necesita una cuenta de Amazon para ello. Si no tiene una, puede obtenerla en [http://www.amazon.com/](http://www.amazon.com/).

1. Vaya a [Amazon Developer Center](https://login.amazon.com/) e inicie sesión con las credenciales de su cuenta de Amazon.
2. Si aún no lo ha hecho, haga clic en **Sign up**(Registro), siga los pasos de registro para desarrolladores y acepte la directiva.
3. Haga clic en **Register new application**(Registrar nueva aplicación).
   
    ![Registrar una nueva aplicación en el sitio web de Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)
4. Especifique la información de la aplicación [**Name** (Nombre), **Description** (Descripción) y **Privacy Notice URL** (Dirección URL de aviso de privacidad)] y haga clic en **Save** (Guardar).
   
    ![Proporcionar información de la aplicación para registrar una nueva aplicación en Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)
5. En la sección **Web Settings** (Configuración web), copie los valores **Client ID** (Id. de cliente) y **Client Secret** (Secreto de cliente). [Para verlo, es preciso hacer clic en el botón **Show Secret** (Mostrar secreto)]. Necesitará ambos para configurar Amazon como proveedor de identidades de su inquilino. Haga clic en **Edit** (Editar) en la parte inferior de la sección. **secreto de cliente** es una credencial de seguridad importante.
   
    ![Proporcionar el identificador de cliente y el secreto de cliente para la nueva aplicación en Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)
6. Escriba `https://login.microsoftonline.com` en el campo **Allowed JavaScript Origins** (Orígenes de JavaScript permitidos) y `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **Allowed Return URLs** (Direcciones URL de retorno permitidas). Reemplace **{tenant}** por el nombre de su inquilino (por ejemplo, contoso.onmicrosoft.com). Haga clic en **Save**. El valor de **{tenant}** distingue mayúsculas de minúsculas.
   
    ![Proporcionar orígenes de JavaScript y direcciones URL de retorno para la nueva aplicación en Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Configuración de Amazon como proveedor de identidades del inquilino
1. Siga estos pasos para [ir a la hoja de características de B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) del Portal de Azure.
2. En la hoja de características B2C, haga clic en **Proveedores de identidades**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, "Amzn".
5. Haga clic en **Identity provider type** (Tipo de proveedor de identidades), seleccione **Amazon** y haga clic en **OK** (Aceptar).
6. Haga clic en **Set up this identity provider** (Configurar este proveedor de identidades) y escriba el identificador de cliente y el secreto de cliente de la aplicación de Amazon que creó anteriormente.
7. Haga clic en **OK** (Aceptar) y, luego, en **Create** (Crear) para guardar la configuración de Amazon.

