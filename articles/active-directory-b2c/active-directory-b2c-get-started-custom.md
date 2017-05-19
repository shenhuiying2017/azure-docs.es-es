---
title: "Azure Active Directory B2C: introducción a las directivas personalizadas | Microsoft Docs"
description: "Introducción a las directivas personalizadas de Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: joroja;parahk;gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: b72db6a0eb8a8621be5f05da6028615d5d24ba1e
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C: introducción a las directivas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Después de completar los pasos de este artículo, la directiva personalizada admitirá el registro o el inicio de sesión de la "cuenta local" con un correo electrónico y una contraseña. También preparará el entorno para agregar proveedores de identidades adicionales (como Facebook o Azure AD).  Se fomenta que se completen estos pasos antes de presentar los restantes usos del marco de experiencia de identidad de Azure AD B2C y muchos conceptos esenciales.

## <a name="prerequisites"></a>Requisitos previos

Antes de seguir, asegúrese de tener un inquilino de Azure AD B2C. Un inquilino de Azure AD B2C es un contenedor para todos los usuarios, las aplicaciones, las directivas y mucho más. Si todavía no tiene uno, debe [crearlo](active-directory-b2c-get-started.md). Se recomienda encarecidamente a todos los programadores que completen los tutoriales de las directivas integradas de Azure AD B2C y que configuren su aplicación con directivas integradas antes de continuar.  Las aplicaciones funcionarán con ambos tipos de directivas con un pequeño cambio en el nombre de la directiva para invocar la directiva personalizada.

El acceso a la edición de directivas personalizadas requiere una suscripción válida a Azure vinculada a un inquilino.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Adición de claves de firma y de cifrado a un inquilino de B2C para que las usen directivas personalizadas

1. Navegue a la hoja Identity Experience Framework (Marco de experiencia de identidad) en la configuración de su inquilino de Azure AD B2C.
2. Seleccione **Policy Keys** (Claves de directiva) para ver las claves disponibles en el inquilino.
3. Cree `B2C_1A_TokenSigningKeyContainer` si no existe:
 * Haga clic en **Agregar**
 * Opciones > `Generate`
 * Nombre >`TokenSigningKeyContainer` El prefijo B2C_1A_ puede agregarse automáticamente.
 * Tipo de clave > `RSA`
 * Fechas: usar los valores predeterminados
 * Uso de la clave > `Signature`
 * Haga clic en **Crear**
4. Cree `B2C_1A_TokenEncryptionKeyContainer` si no existe:
 * Haga clic en **Agregar**
 * Opciones > `Generate`
 * Nombre >`TokenEncryptionKeyContainer` El prefijo B2C_1A_ puede agregarse automáticamente.
 * Tipo de clave > `RSA`
 * Fechas: usar los valores predeterminados
 * Uso de la clave > `Encryption`
 * Haga clic en **Crear**
5. Cree `B2C_1A_FacebookSecret`. Si ya tiene un secreto de la aplicación Facebook, agréguelo como clave de directiva al inquilino.  De lo contrario, debe crear la clave con un valor de marcador de posición para que las directivas pasen la validación.
 * Haga clic en **Agregar**
 * Opciones > `Manual`
 * Nombre > `FacebookSecret` El prefijo B2C_1A_ puede agregarse automáticamente.
 * Secreto > Escriba la propiedad FacebookSecret desde developers.facebook.com o "0" como marcador de posición. *No es su id de aplicación de Facebook*
 * Uso de la clave > Firma
 * Haga clic en **Crear** y confirme la creación

## <a name="register-identity-experience-framework-applications"></a>Registro de las aplicaciones del marco de experiencia de identidad

Azure AD B2C requiere que registre dos aplicaciones adicionales que el motor usa para registrar usuarios e iniciar su sesión.

>[!NOTE]
>Debemos crear dos aplicaciones que permitan iniciar sesión mediante cuentas locales: IdentityExperienceFramework (una aplicación web) y ProxyIdentityExperienceFramework (una aplicación nativa) con permiso delegado de la aplicación IdentityExperienceFramework. Las cuentas locales solo existen en su inquilino. Los usuarios finales se registran con una combinación única de correo electrónico:contraseña para acceder a las aplicaciones registradas en el inquilino.

### <a name="create-the-identityexperienceframework-application"></a>Creación de la aplicación IdentityExperienceFramework

1. En [Azure Portal](https://portal.azure.com), [cambie al contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
1. Abra la hoja `Azure Active Directory` (no la hoja Azure AD B2C). Puede que deba hacer clic en **> Más servicios** para encontrarla.
1. Seleccione **App registrations** (Registros de aplicaciones).
1. Haga clic en **+ Nuevo registro de aplicaciones**.
   * Nombre: `IdentityExperienceFramework`
   * Tipo de aplicación: `Web app/API`
   * Dirección URL de inicio de sesión: `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, donde `yourtenant` es el nombre de dominio del inquilino de Azure AD B2C.
1. Haga clic en **Crear**.
1. Seleccione la aplicación `IdentityExperienceFramework` recientemente creada, haga clic en **Propiedades**, copie el identificador de la aplicación y guárdelo para más adelante.

### <a name="create-the-proxy-identity-experience-framework-application"></a>Creación de la aplicación del marco de experiencia de identidad del proxy

1. Seleccione **App registrations** (Registros de aplicaciones).
1. Haga clic en **+ Nuevo registro de aplicaciones**.
   * Nombre: `ProxyIdentityExperienceFramework`
   * Tipo de aplicación: `Native`
   * Dirección URL de inicio de sesión: `https://login.microsoftonline.com/yourtenant.onmicrosoft.com` donde `yourtenant` es el inquilino de Azure AD B2C.
1. Haga clic en **Crear**.
1. Una vez creada, seleccione la aplicación `ProxyIdentityExperienceFramework`, haga clic en **Propiedades**, copie el identificador de la aplicación y guárdelo para más adelante.
1. Seleccione **Permisos necesarios**, **+ Agregar** y, luego, **Seleccionar una API**.
1. Busque el nombre `IdentityExperienceFramework`, seleccione IdentityExperienceFramework en los resultados y haga clic en **Seleccionar**.
1. Active la casilla junto a `Access IdentityExperienceFramework` y, luego, haga clic en **Seleccionar**.
1. Haga clic en **Done**.
1. Haga clic en **Conceder permisos** y confirme **Sí**

## <a name="download-starter-pack-and-modify-policies"></a>Descarga del paquete de inicio y modificación de directivas

Las directivas personalizadas son un conjunto de archivos XML que se deben cargar al inquilino de Azure AD B2C. Se proporcionan paquetes de inicio para poder ponerse en marcha con rapidez. Cada uno de los siguientes paquetes de inicio tiene los perfiles técnicos y recorridos de usuario mínimos necesarios para lograr los escenarios como se describe:
 * LocalAccounts: habilita el uso solo de cuentas locales
 * SocialAccounts: habilita el uso solo de cuentas sociales (o federadas)
 * **SocialAndLocalAccounts**: será el que se use en este tutorial
 * SocialAndLocalAccountsWithMFA: se incluyen las opciones social, local y de MFA

Cada paquete de inicio contiene:

* El [archivo base](active-directory-b2c-overview-custom.md#policy-files) de la directiva. Se requieren algunas modificaciones en el archivo base.
* El [archivo de extensión](active-directory-b2c-overview-custom.md#policy-files) de la directiva.  Este archivo es donde se hace la mayoría de los cambios de configuración.
* [Archivos de usuario de confianza](active-directory-b2c-overview-custom.md#policy-files) Son archivos específicos de la tarea a los que llama la aplicación para una tarea específica.

>[!NOTE]
>Si el editor XML admite la validación, es posible que desee validar los archivos contra el archivo de esquema XML `TrustFrameworkPolicy_0.3.0.0.xsd` que se encuentra en la carpeta raíz del paquete de inicio. La validación del esquema XML identifica los errores antes de realizar la carga.

Comencemos:

1. Descargue "active-directory-b2c-custom-policy-starterpack" de GitHub.  [Descargue el archivo ZIP](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o ejecute

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Abra la carpeta `SocialAndLocalAccounts`.  El archivo base (`TrustFrameworkBase.xml`) de esta carpeta incluye contenido necesario tanto para las cuentas locales como para las cuentas sociales o corporativas. El contenido social no interfiere con los pasos para configurar y ejecutar las cuentas locales.
3. Abra `TrustFrameworkBase.xml`.  Si necesita un editor XML, pruebe [Visual Studio Code](https://code.visualstudio.com/download), un editor multiplataforma ligero.
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
>`PolicyId` es el nombre de la directiva que verá en el portal y el nombre que otros archivos de directiva usarán para referirse a este archivo de directiva.

5. Guarde el archivo.
6. Abra `TrustFrameworkExtensions.xml` y realice los dos mismos cambios reemplazando `yourtenant.onmicrosoft.com` por el inquilino de Azure AD B2C. Realice la misma sustitución en el elemento `<TenantId>`, con lo que el total de cambios serán tres.  Guarde el archivo .
7. Abra `SignUpOrSignIn.xml` y realice los mismos cambios reemplazando `yourtenant.onmicrosoft.com` por el inquilino de Azure AD B2C en tres lugares. Guarde el archivo .
8. Abra el secreto de la contraseña y los archivos de edición de perfiles, y realice los mismos cambios reemplazando `yourtenant.onmicrosoft.com` por el inquilino de Azure AD B2C en tres lugares en cada archivo. Guarde los archivos.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Incorporación de identificadores de aplicación a la directiva personalizada
Agregue los identificadores de aplicación al archivo de extensiones (`TrustFrameworkExtensions.xml`).

1. En el archivo de extensiones (`TrustFrameworkExtensions.xml`), busque el elemento `<TechnicalProfile Id="login-NonInteractive">`.
2. Reemplace ambas instancias de `IdentityExperienceFrameworkAppId` por el identificador de la aplicación del marco de experiencia de identidad que creó antes. Este es un ejemplo:

```xml
<Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
```

3. Reemplace ambas instancias de `ProxyIdentityExperienceFrameworkAppId` por el identificador de la aplicación del marco de experiencia de identidad que creó antes
4. Guarde el archivo de extensiones.

## <a name="upload-the-policies-to-your-tenant"></a>Carga de las directivas en el inquilino

1. En [Azure Portal](https://portal.azure.com), [cambie al contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y abra la hoja Azure AD B2C.
2. Haga clic en **Identity Experience Framework** (Marco de experiencia de identidad)
3. Seleccione **Upload Policy** (Cargar directiva) para cargar archivos de directiva

    >[!WARNING]
    >Los archivos de directivas personalizadas se deben cargar en el orden siguiente:

1. Cargue `TrustFrameworkBase.xml`.
2. Cargue `TrustFrameworkExtensions.xml`.
3. Cargue `SignUpOrSignin.xml`.
4. Cargue los restantes archivos de directiva.

Cuando se carga un archivo, se antepone `B2C_1A_` al nombre del archivo de directiva.

## <a name="test-the-custom-policy-using-run-now"></a>Prueba de la directiva personalizada con "Ejecutar ahora"

1. Abra la **configuración de Azure AD B2C** y navegue hasta **Identity Experience Framework** (Marco de experiencia de identidad).

>[!NOTE]
>**Ejecutar ahora** Requiere al menos que una aplicación esté registrada previamente en el inquilino. Las aplicaciones deben estar registradas en el inquilino de B2C y se debe usar el menú **Aplicaciones** en B2C o en el marco de la experiencia de identidad para invocar directivas integradas y personalizadas. Solo se necesita un registro por aplicación. 

En los artículos [Introducción](active-directory-b2c-get-started.md) a Azure AD B2C o [Registro de la aplicación](active-directory-b2c-app-registration.md) de Azure AD B2C encontrará la información necesaria para registrar aplicaciones.  

2. Abra la directiva personalizada del usuario de confianza (RP) que cargó `B2C_1A_signup_signin` y luego haga clic en el botón **Ejecutar ahora**.


3. Debe poder registrarse con una dirección de correo electrónico.
4. Inicie sesión con la misma cuenta para confirmar que tiene una configuración correcta

>[!NOTE]
>Una causa común de los errores en el inicio de sesión es que la aplicación IdentityExperienceFramework no esté configurada correctamente.


## <a name="next-steps"></a>Pasos siguientes

### <a name="add-facebook-as-an-identity-provider"></a>Incorporación de Facebook como proveedor de identidades
Para configurar Facebook:
1. [Configure una aplicación de Facebook en developers.facebook.com](active-directory-b2c-setup-fb-app.md)
2. [Agregue el secreto de la aplicación Facebook a su inquilino de Azure AD B2C](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies)
3. Agregue el identificador de aplicación de Facebook en el archivo de directiva TrustFrameworkExtensions en `Item Key="client_id"`:

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <Metadata>
  <!--Replace the value of client_id in this technical profile with your the Facebook App ID"-->
    <Item Key="client_id">00000000000000</Item>
```
4. Cargue el archivo de directiva TrustFrameworkExtensions.xml en el inquilino.
5. Realice la prueba mediante **Ejecutar ahora** o invoque la directiva directamente desde la aplicación registrada.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Adición de Azure Active Directory como proveedor de identidades
El archivo base que se usó en esta guía de introducción ya contiene parte del contenido que necesita para agregar otros proveedores de identidades. Para configurar el inicio de sesión con cuentas de Azure AD, [siga aquí](active-directory-b2c-setup-aad-custom.md).


## <a name="reference"></a>Referencia

[Información general](active-directory-b2c-overview-custom.md) de las directivas personalizadas en Azure AD B2C mediante el marco de experiencia de identidad

