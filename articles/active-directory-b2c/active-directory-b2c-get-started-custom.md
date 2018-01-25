---
title: "Azure Active Directory B2C: introducción a las directivas personalizadas | Microsoft Docs"
description: "Introducción a las directivas personalizadas de Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: mtillman
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja;parahk;gsacavdm
ms.openlocfilehash: 86b86c7c670b34b4f3303adbcb55aff8d5edb53a
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Azure Active Directory B2C: introducción a las directivas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Después de completar los pasos de este artículo, la directiva personalizada admitirá el registro o el inicio de sesión de la "cuenta local" mediante una dirección de correo electrónico y una contraseña. También preparará el entorno para agregar proveedores de identidades (como Facebook o Azure Active Directory). Le recomendamos que siga estos pasos antes de informarse sobre otros usos del marco de experiencia de identidad de Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>requisitos previos

Antes de comenzar, asegúrese de que tiene un inquilino de Azure AD B2C, que es un contenedor para todos los usuarios, las aplicaciones, las directivas y mucho más. Si todavía no tiene uno, debe [crear un inquilino de Azure AD B2C](active-directory-b2c-get-started.md). Se recomienda encarecidamente a todos los desarrolladores que completen los tutoriales de las directivas integradas de Azure AD B2C y que configuren las aplicaciones con directivas integradas antes de continuar. Las aplicaciones funcionarán con ambos tipos de directivas una vez que se realice un pequeño cambio en el nombre de la directiva para invocar la directiva personalizada.

>[!NOTE]
>Para obtener acceso a la edición de directivas personalizadas, necesita una suscripción válida a Azure vinculada al inquilino. Si no ha [vinculado su inquilino de Azure AD B2C a una suscripción de Azure](active-directory-b2c-how-to-enable-billing.md) o su suscripción de Azure está deshabilitada, el botón Marco de experiencia de identidad no estará disponible.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Agregar claves de firma y de cifrado a un inquilino de B2C para que las usen directivas personalizadas

1. Abra la hoja **Marco de experiencia de identidad** en la configuración de su inquilino de Azure AD B2C.
2. Seleccione **Policy Keys** (Claves de directiva) para ver las claves disponibles en el inquilino.
3. Cree B2C_1A_TokenSigningKeyContainer si no existe:<br>
    a. Seleccione **Agregar**. <br>
    b. Seleccione **Generar**.<br>
    c. En **Nombre**, use `TokenSigningKeyContainer`. <br> 
    Es posible que se agregue automáticamente el prefijo `B2C_1A_`.<br>
    d. En **Tipo de clave**, use **RSA**.<br>
    e. En **Fechas**, use los valores predeterminados. <br>
    f. En **Uso de claves**, use **Firma**.<br>
    g. Seleccione **Crear**.<br>
4. Cree B2C_1A_TokenEncryptionKeyContainer si no existe:<br>
 a. Seleccione **Agregar**.<br>
 b. Seleccione **Generar**.<br>
 c. En **Nombre**, use `TokenEncryptionKeyContainer`. <br>
   Es posible que se agregue automáticamente el prefijo `B2C_1A`_.<br>
 d. En **Tipo de clave**, use **RSA**.<br>
 e. En **Fechas**, use los valores predeterminados.<br>
 f. En **Uso de claves**, use **Cifrado**.<br>
 g. Seleccione **Crear**.<br>
5. Cree B2C_1A_FacebookSecret. <br>
Si ya tiene un secreto de la aplicación Facebook, agréguelo como clave de directiva al inquilino. De lo contrario, debe crear la clave con un valor de marcador de posición para que las directivas pasen la validación.<br>
 a. Seleccione **Agregar**.<br>
 b. En **Opciones**, use **Manual**.<br>
 c. En **Nombre**, use `FacebookSecret`. <br>
 Es posible que se agregue automáticamente el prefijo `B2C_1A_`.<br>
 d. En el cuadro **Secreto**, escriba la propiedad FacebookSecret desde developers.facebook.com o `0` como marcador de posición. *No es su identificador de aplicación de Facebook.* <br>
 e. En **Uso de claves**, use **Firma**. <br>
 f. Seleccione **Crear** y confirme la creación.

## <a name="register-identity-experience-framework-applications"></a>Registro de las aplicaciones del marco de experiencia de identidad

Azure AD B2C requiere que registre dos aplicaciones adicionales que el motor usa para registrar usuarios e iniciar su sesión.

>[!NOTE]
>Debe crear dos aplicaciones que permitan iniciar sesión mediante cuentas locales: IdentityExperienceFramework (una aplicación web) y ProxyIdentityExperienceFramework (una aplicación nativa) con permiso delegado de la aplicación IdentityExperienceFramework. Las cuentas locales solo existen en su inquilino. Los usuarios se registran con una combinación única de dirección de correo electrónico/contraseña para obtener acceso a las aplicaciones registradas en el inquilino.

### <a name="create-the-identityexperienceframework-application"></a>Creación de la aplicación IdentityExperienceFramework

1. En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
2. Abra la hoja **Azure Active Directory** (no la hoja **Azure AD B2C**). Tendrá que seleccionar **Más servicios** para encontrarla.
3. Seleccione **App registrations** (Registros de aplicaciones).
4. Seleccione **Nuevo registro de aplicaciones**.
   * En **Nombre**, use `IdentityExperienceFramework`.
   * En **Tipo de aplicación**, use **Aplicación web o API**.
   * En **Dirección URL de inicio de sesión**, use `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, donde `yourtenant` es el nombre de dominio del inquilino de Azure AD B2C.
5. Seleccione **Crear**.
6. Una vez que se ha creado, seleccione la aplicación **IdentityExperienceFramework** recién creada.<br>
   * Seleccione **Propiedades**.<br>
   * Copie el identificador de aplicación y guárdelo para más tarde.

### <a name="create-the-proxyidentityexperienceframework-application"></a>Crear la aplicación ProxyIdentityExperienceFramework

1. Seleccione **App registrations** (Registros de aplicaciones).
1. Seleccione **Nuevo registro de aplicaciones**.
   * En **Nombre**, use `ProxyIdentityExperienceFramework`.
   * En **Tipo de aplicación**, use **Nativa**.
   * En **URI de redirección**, use `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, donde `yourtenant` es el inquilino de Azure AD B2C.
1. Seleccione **Crear**.
1. Una vez que se ha creado, seleccione la aplicación **ProxyIdentityExperienceFramework**.<br>
   * Seleccione **Propiedades**. <br>
   * Copie el identificador de aplicación y guárdelo para más tarde.
1. Seleccione **Permisos necesarios**.
1. Seleccione **Agregar**.
1. Seleccione **Seleccionar una API**.
1. Busque el nombre IdentityExperienceFramework. Seleccione **IdentityExperienceFramework** en los resultados y haga clic en **Seleccionar**.
1. Active la casilla situada junto a **Access IdentityExperienceFramework** (Obtener acceso a IdentityExperienceFramework) y, después, haga clic en **Seleccionar**.
1. Seleccione **Listo**.
1. Seleccione **Conceder permisos** y haga clic en **Sí** para confirmar.

## <a name="download-starter-pack-and-modify-policies"></a>Descarga del paquete de inicio y modificación de directivas

Las directivas personalizadas son un conjunto de archivos XML que se deben cargar al inquilino de Azure AD B2C. Se proporcionan paquetes de inicio para poder ponerse en marcha con rapidez. Cada paquete de inicio de la lista siguiente contiene el menor número de perfiles técnicos y recorridos del usuario necesarios para lograr los escenarios descritos:
 * LocalAccounts. Habilita el uso solo de cuentas locales.
 * SocialAccounts. Habilita el uso solo de cuentas sociales (o federadas).
 * **SocialAndLocalAccounts**. Este archivo se usa para el tutorial.
 * SocialAndLocalAccountsWithMFA. Aquí se incluyen otras opciones sociales, locales y de Multi-Factor Authentication.

Cada paquete de inicio contiene lo siguiente:

* El [archivo base](active-directory-b2c-overview-custom.md#policy-files) de la directiva. Se requieren algunas modificaciones en el archivo base.
* El [archivo de extensión](active-directory-b2c-overview-custom.md#policy-files) de la directiva.  Este archivo es donde se hace la mayoría de los cambios de configuración.
* Los [archivos de usuario de confianza](active-directory-b2c-overview-custom.md#policy-files) son archivos específicos de la tarea a los que llama la aplicación.

>[!NOTE]
>Si el editor XML admite la validación, valide los archivos con el archivo de esquema XML TrustFrameworkPolicy_0.3.0.0.xsd que se encuentra en el directorio raíz del paquete de inicio. La validación del esquema XML identifica los errores antes de realizar la carga.

 Comencemos:

1. Descargue active-directory-b2c-custom-policy-starterpack de GitHub. [Descargue el archivo ZIP](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o ejecute

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Abra la carpeta SocialAndLocalAccounts.  El archivo base (TrustFrameworkBase.xml) de esta carpeta incluye contenido necesario tanto para las cuentas locales como para las cuentas sociales o corporativas. El contenido social no interfiere con los pasos para configurar y ejecutar las cuentas locales.
3. Abra TrustFrameworkBase.xml. Si necesita un editor XML, [pruebe Visual Studio Code](https://code.visualstudio.com/download), un editor multiplataforma ligero.
4. En el elemento `TrustFrameworkPolicy` raíz, actualice los atributos `TenantId` y `PublicPolicyUri`, para lo que debe reemplazar `yourtenant.onmicrosoft.com` por el nombre de dominio del inquilino de Azure AD B2C:
   ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
   >[!NOTE]
   >`PolicyId` es el nombre de la directiva que ve en el portal y el nombre que otros archivos de directiva usan para referirse a este archivo de directiva.

5. Guarde el archivo.
6. Abra TrustFrameworkExtensions.xml. Realice los dos mismos cambios reemplazando `yourtenant.onmicrosoft.com` por el inquilino de Azure AD B2C. Realice la misma sustitución en el elemento `<TenantId>`, con lo que el total de cambios serán tres. Guarde el archivo.
7. Abra SignUpOrSignIn.xml. Realice los mismos cambios reemplazando `yourtenant.onmicrosoft.com` por el inquilino de Azure AD B2C en tres lugares. Guarde el archivo.
8. Abra los archivos de edición de perfiles y restablecimiento de contraseña. Realice los mismos cambios reemplazando `yourtenant.onmicrosoft.com` por el inquilino de Azure AD B2C en tres lugares en cada archivo. Guarde los archivos.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Incorporación de identificadores de aplicación a la directiva personalizada
Agregue los identificadores de aplicación al archivo de extensiones (`TrustFrameworkExtensions.xml`):

1. En el archivo de extensiones (TrustFrameworkExtensions.xml) busque el elemento `<TechnicalProfile Id="login-NonInteractive">`.
2. Reemplace ambas instancias de `IdentityExperienceFrameworkAppId` por el identificador de la aplicación del marco de experiencia de identidad que creó antes. Este es un ejemplo:

   ```xml
   <Item Key="IdTokenAudience">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. Reemplace ambas instancias de `ProxyIdentityExperienceFrameworkAppId` por el identificador de la aplicación del marco de experiencia de identidad del proxy que ha creado anteriormente.
4. Guarde el archivo de extensiones.

## <a name="upload-the-policies-to-your-tenant"></a>Carga de las directivas en el inquilino

1. En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y abra la hoja **Azure AD B2C**.
2. Seleccione **Marco de experiencia de identidad**.
3. Seleccione **Cargar directiva**.

    >[!WARNING]
    >Los archivos de directivas personalizadas se deben cargar en el orden siguiente:

1. Cargue TrustFrameworkBase.xml.
2. Cargue TrustFrameworkExtensions.xml.
3. Cargue SignUpOrSignin.xml.
4. Cargue los restantes archivos de directiva.

Cuando se carga un archivo, se antepone `B2C_1A_` al nombre del archivo de directiva.

## <a name="test-the-custom-policy-by-using-run-now"></a>Probar la directiva personalizada con Ejecutar ahora

1. Abra la **configuración de Azure AD B2C** y vaya a **Marco de experiencia de identidad**.

   >[!NOTE]
   >**Ejecutar ahora** Requiere al menos que una aplicación esté registrada previamente en el inquilino. Las aplicaciones deben estar registradas en el inquilino de B2C, ya sea mediante la selección del menú **Aplicaciones** en Azure AD B2C o mediante el marco de experiencia de identidad para invocar directivas integradas y personalizadas. Solo se necesita un registro por aplicación.<br><br>
   Para obtener información sobre cómo registrar aplicaciones, vea el artículo de [introducción](active-directory-b2c-get-started.md) a Azure AD B2C o el artículo [Registro de la aplicación](active-directory-b2c-app-registration.md) de Azure AD B2C.  

2. Abra la directiva personalizada del usuario de confianza (RP) que ha cargado (B2C_1A_signup_signin). Seleccione **Ejecutar ahora**.

3. Debe poder registrarse con una dirección de correo electrónico.

4. Inicie sesión con la misma cuenta para confirmar que tiene una configuración correcta.

>[!NOTE]
>Una causa común de los errores en el inicio de sesión es que la aplicación IdentityExperienceFramework no esté configurada correctamente.


## <a name="next-steps"></a>pasos siguientes

### <a name="add-facebook-as-an-identity-provider"></a>Incorporación de Facebook como proveedor de identidades
Para configurar Facebook:
1. [Configure una aplicación de Facebook en developers.facebook.com](active-directory-b2c-setup-fb-app.md).
2. [Agregue el secreto de la aplicación Facebook a su inquilino de Azure AD B2C](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies).
3. En el archivo de directiva TrustFrameworkExtensions, reemplace el valor de `client_id` por el identificador de aplicación de Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. Cargue el archivo de directiva TrustFrameworkExtensions.xml en el inquilino.
5. Realice la prueba mediante **Ejecutar ahora** o invoque la directiva directamente desde la aplicación registrada.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Adición de Azure Active Directory como proveedor de identidades
El archivo base que se ha usado en esta guía de introducción ya contiene parte del contenido que necesita para agregar otros proveedores de identidades. Para obtener información sobre cómo configurar inicios de sesión, vea el artículo [Azure Active Directory B2C: inicio de sesión con cuentas de Azure AD](active-directory-b2c-setup-aad-custom.md).

Para obtener información general sobre las directivas personalizadas de Azure AD B2C que usan el marco de experiencia de identidad, vea el artículo [Azure Active Directory B2C: directivas personalizadas](active-directory-b2c-overview-custom.md). 
