---
title: 'Azure Active Directory B2C: Registro de aplicaciones | Microsoft Docs'
description: "Registro de la aplicación con Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 0b87ec728891d4f64bb7b2e4b7b4f0f2276b1ffc
ms.openlocfilehash: ca75ddf8d099f411c4ae1af00e922f26e145f939


---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: Registro de la aplicación
## <a name="prerequisite"></a>Requisito previo
Para crear una aplicación que acepte registros e inicios de sesión de consumidores, primero deberá registrarla en un inquilino de Azure Active Directory B2C. Para obtener su propio inquilino, siga los pasos descritos en [Creación de un inquilino de Azure AD B2C](active-directory-b2c-get-started.md). Si ha seguido todos los pasos de este artículo, debe tener la hoja de características B2C anclada en el panel de inicio.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Navegación a la hoja de características B2C
Si tiene la hoja de características B2C anclada al Panel de inicio, la verá en cuanto inicie sesión en el [Portal de Azure](https://portal.azure.com/) como administrador global del inquilino B2C.

Otra manera de acceder a la hoja es hacer clic en **Examinar** y luego en **Azure AD B2C** en el panel de navegación izquierdo de [Azure Portal](https://portal.azure.com/).

> [!IMPORTANT]
> Necesita ser administrador global del inquilino B2C para poder acceder a la hoja Características B2C. Un administrador global de cualquier otro inquilino o un usuario de cualquier inquilino no puede acceder a dicha hoja.  Puede cambiar a su inquilino B2C usando el selector de inquilinos en la esquina superior derecha del Portal de Azure.
> 
> 

## <a name="register-an-application"></a>Registro de una aplicación
1. En la hoja de características B2C del Portal de Azure, haga clic en **Aplicaciones**.
2. Haga clic en **+Agregar** en la parte superior de la hoja.
3. Escriba un **Nombre** para la aplicación que la describa a los consumidores. Por ejemplo, puede escribir "Aplicación Contoso B2C".
4. Si va a escribir una aplicación basada en web, mueva el conmutador **Incluir aplicación web/API web** a **Sí**. Las **Direcciones URL de respuesta** son puntos de conexión en los que Azure AD B2C devolverá los tokens que su aplicación solicite. Por ejemplo, escriba: `https://localhost:44321/`. Si la aplicación web también llamará a algunas API web protegidas por Azure AD B2C, también querrá crear un **secreto de aplicación**; para ello, haga clic en el botón **Generar clave**.
   
   > [!NOTE]
   > Un **secreto de aplicación** es una credencial de seguridad importante y debe protegerse correctamente.
   > 
   > 
5. Si va a escribir una aplicación móvil, mueva el conmutador **Incluir cliente nativo** a **Sí**. Copie el **URI de redirección** predeterminado creado automáticamente.
6. Haga clic en **Crear** para registrar la aplicación.
7. Haga clic en la aplicación que acaba de crear y copie el **Id. de cliente de aplicación** único global que usará más adelante en el código.

> [!IMPORTANT]
> Las aplicaciones creadas en la hoja de características de B2C tienen que administrarse en la misma ubicación. Si edita las aplicaciones B2C mediante PowerShell u otro portal, ya no serán compatibles y probablemente no funcionen con Azure AD B2C.
> 
> 

## <a name="build-a-quick-start-application"></a>Creación de una aplicación de inicio rápido
Ahora que tiene una aplicación registrada en Azure AD B2C, puede completar uno de nuestros tutoriales de inicio rápido para ponerse en marcha rápidamente. Estas son algunas recomendaciones:

[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]




<!--HONumber=Jan17_HO1-->


