---
title: "Azure Active Directory B2C: introducción a las directivas personalizadas | Microsoft Docs"
description: "Un tema de introducción a las directivas personalizadas de Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 93fc922433f0ba60ee438db8d4dcb14527c208fc
ms.contentlocale: es-es
ms.lasthandoff: 05/02/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C: introducción a las directivas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Después de completar los pasos de este artículo, la directiva personalizada admitirá el registro o el inicio de sesión de la "cuenta local" con un correo electrónico y una contraseña. También preparará el entorno para agregar proveedores de identidades adicionales (como Facebook o Azure AD).  Se requiere completar estos pasos antes de presentar todos los otros usos de Identity Experience Engine de Azure AD B2C y muchos conceptos esenciales.

## <a name="prerequisites"></a>Requisitos previos

Antes de seguir, asegúrese de tener un inquilino de Azure AD B2C. Un inquilino de Azure AD B2C es un contenedor para todos los usuarios, las aplicaciones, las directivas y mucho más. Si todavía no tiene uno, debe [crearlo](active-directory-b2c-get-started.md).

### <a name="confirming-your-b2c-tenant"></a>Confirmación del inquilino de B2C

Como las directivas personalizadas siguen estando en la versión preliminar privada, confirme que el inquilino de Azure AD B2C está habilitado para la carga de directivas personalizadas:

1. En [Azure Portal](https://portal.azure.com), [cambie al contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y abra la hoja Azure AD B2C.
1. Haga clic en **Todas las directivas**.
1. Asegúrese de que la opción **Cargar directiva** está disponible.  Si el botón aparece deshabilitado, envíe un correo electrónico a AADB2CPreview@microsoft.com.

## <a name="set-up-keys-for-your-custom-policy"></a>Configuración de claves para la directiva personalizada

El primer paso necesario para usar las directivas personalizadas es la configuración de claves.

[!INCLUDE [active-directory-b2c-setup-keys-custom.md](../../includes/active-directory-b2c-setup-keys-custom.md)]

## <a name="download-starter-pack-and-modify-policies"></a>Descarga del paquete de inicio y modificación de directivas

Las directivas personalizadas son un conjunto de archivos XML que se deben cargar al inquilino de Azure AD B2C. Se proporciona un paquete de inicio que puede usar para comenzar. El paquete de inicio contiene lo siguiente:

* El [archivo base](active-directory-b2c-overview-custom.md#policy-files) de la directiva. Se requieren algunas modificaciones en el archivo base.
* El [archivo de extensión](active-directory-b2c-overview-custom.md#policy-files) de la directiva.  Este archivo es donde se hace la mayoría de los cambios de configuración.

Comencemos:

1. Descargue el "paquete de inicio de directivas personalizadas de Azure AD B2C" en GitHub.  [Descargue el archivo ZIP](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o ejecute

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Abra la carpeta `SocialIDPAndLocalAccounts`.  El archivo base (`TrustFrameworkBase.xml`) de esta carpeta incluye contenido necesario tanto para las cuentas locales como para las cuentas sociales o corporativas. El contenido social no interfiere con los pasos para configurar y ejecutar las cuentas locales.
1. Abra `TrustFrameworkBase.xml`.  Si necesita un editor XML, intente [descargar Visual Studio Code](https://code.visualstudio.com/download), un editor multiplataforma ligero.
1. En el elemento `TrustFrameworkPolicy` raíz, actualice los atributos `TenantId` y `PublicPolicyUri`, reemplazando `{tenantName}` por el inquilino de Azure AD B2C:

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="{tenantName}.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://{tenantName}.onmicrosoft.com">
    ```

1. Guarde el archivo .
1. Abra `TrustFrameworkExtensions.xml` y haga los mismos cambios reemplazando `{tenantName}` por el inquilino de Azure AD B2C. Guarde el archivo .
1. Abra `SignUpOrSignIn.xml` y haga los mismos cambios reemplazando `{tenantName}` por el inquilino de Azure AD B2C. Guarde el archivo .

>[!NOTE]
>Si el editor XML admite la validación, es posible que desee validar los archivos contra el archivo de esquema XML `TrustFrameworkPolicy_0.3.0.0.xsd` que se encuentra en la carpeta raíz del paquete de inicio. La validación del esquema XML identifica los errores antes de realizar la carga.

## <a name="register-policy-engine-applications"></a>Registro de aplicaciones de motor de directivas

Azure AD B2C requiere que registre dos aplicaciones adicionales que el motor usa para registrar usuarios e iniciar su sesión.

>[!NOTE]
>A continuación, creamos dos aplicaciones que habilitan el inicio de sesión con cuentas locales: PolicyEngine (una aplicación web) y PolicyEngineProxy (una aplicación nativa) con permiso delegado de PolicyEngine. Esta sección solo es necesaria para los inquilinos de Azure AD B2C donde se espera el uso de cuentas locales.

### <a name="create-the-policy-engine-application"></a>Creación de la aplicación de motor de directivas

1. En [Azure Portal](https://portal.azure.com), [cambie al contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
1. Abra la hoja `Azure Active Directory` (no la hoja Azure AD B2C). Puede que deba hacer clic en **> Más servicios** para encontrarla.
1. Seleccione **App registrations** (Registros de aplicaciones).
1. Haga clic en **+ Nuevo registro de aplicaciones**.
   * Nombre: `PolicyEngine`
   * Tipo de aplicación: `Web app/API`
   * Dirección URL de inicio de sesión: `https://login.microsoftonline.com/{tenantName}.onmicrosoft.com` donde `{tenantName}` es el inquilino de Azure AD B2C.
1. Haga clic en **Crear**.
1. Una vez creada, seleccione la aplicación `PolicyEngine` recientemente creada, copie el identificador de aplicación y guárdelo para más adelante.

### <a name="create-the-policy-engine-proxy-application"></a>Creación de la aplicación de proxy del motor de directivas

1. Seleccione **App registrations** (Registros de aplicaciones).
1. Haga clic en **+ Nuevo registro de aplicaciones**.
   * Nombre: `PolicyEngineProxy`
   * Tipo de aplicación: `Native`
   * Dirección URL de inicio de sesión: `https://login.microsoftonline.com/{tenantName}.onmicrosoft.com` donde `{tenantName}` es el inquilino de Azure AD B2C.
1. Haga clic en **Crear**.
1. Una vez creada, seleccione la aplicación `PolicyEngineProxy`, copie el identificador de aplicación y guárdelo para más adelante.
1. Seleccione **Permisos necesarios**, **+ Agregar** y, luego, **Seleccionar una API**.
1. Busque el nombre `PolicyEngine`, seleccione PolicyEngine en los resultados y, luego, haga clic en **Seleccionar**.
1. Active la casilla junto a `Access PolicyEngine` y, luego, haga clic en **Seleccionar**.
1. Haga clic en **Done**.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Incorporación de identificadores de aplicación a la directiva personalizada

Para crear una directiva personalizada con cuentas locales habilitadas, debe agregar los identificadores de aplicación al archivo de extensiones (`TrustFrameworkExtensions.xml`).

1. En el archivo de extensiones (`TrustFrameworkExtensions.xml`), busque el elemento `<TechnicalProfile Id="login-NonInteractive">`.
1. Reemplace ambas instancias de `{Policy Engine Proxy Application ID}` con el identificador de la [aplicación proxy del motor de directivas que creó](#create-the-policy-engine-proxy-application).
1. Reemplace ambas instancias de `{Policy Engine Application ID}` con el identificador de la [aplicación del motor de directivas que creó](#create-the-policy-engine-application).
1. Guarde el archivo de extensiones.

## <a name="upload-the-policies-to-your-tenant"></a>Carga de las directivas en el inquilino

1. En [Azure Portal](https://portal.azure.com), [cambie al contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y abra la hoja Azure AD B2C.
1. Haga clic en **Todas las directivas**.
1. Seleccione **Cargar directiva**

    >[!WARNING]
    >Los archivos de directivas personalizadas se deben cargar en el orden siguiente:

1. Cargue `TrustFrameworkBase.xml`.
1. Cargue `TrustFrameworkExtensions.xml`.
1. Cargue `SignUpOrSignin.xml`.

Cuando se carga un archivo, el nombre se antepone con `B2C_1A_`.  En cambio, las directivas integradas comienzan con `B2C_1_`.

## <a name="test-the-custom-policy-using-run-now"></a>Prueba de la directiva personalizada con "Ejecutar ahora"

1. Abra la **hoja Azure AD B2C** y vaya a **Todas las directivas**.
1. Seleccione la directiva personalizada que cargó y, luego, haga clic en el botón **Ejecutar ahora**.
1. Debe poder registrarse con una dirección de correo electrónico.

## <a name="next-steps"></a>Pasos siguientes

El archivo base que se usó en esta guía de introducción ya contiene parte del contenido que necesita para agregar otros proveedores de identidades. Para configurar el inicio de sesión con cuentas de Azure AD, [siga aquí](active-directory-b2c-setup-aad-custom.md).

## <a name="reference"></a>Referencia

* Un **perfil técnico** es un elemento que define el nombre de un punto de conexión, sus metadatos, su protocolo y los detalles del intercambio de notificaciones que debe realizar Identity Experience Engine.  El inicio de sesión de cuenta local es el perfil técnico que Identity Experience Engine usa para realizar un inicio de sesión de cuenta local.

