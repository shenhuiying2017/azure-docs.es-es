---
title: "Azure Active Directory B2C: solución de problemas de directivas personalizadas| Microsoft Docs"
description: "Una guía a varios enfoques para solucionar errores en directivas personalizadas"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 0f783062cd674edb0b6b49660f5aee69af4b3adb
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017


---
# <a name="azure-active-directory-b2c-custom-policies-and-identity-experience-framework-troubleshooting"></a>Solución de problemas de directivas personalizadas de Azure Active Directory B2C y el marco de experiencia de identidad
## <a name="the-basics"></a>Conceptos básicos

Los desarrolladores de identidades que usan directivas personalizadas de Azure AD B2C se enfrentan al desafío de configurar el marco de experiencia de identidad de su idioma de directiva en formato XML.  Esta guía es tanto una lista de herramientas recomendadas como sugerencias para descubrir y resolver problemas rápidamente.  Aprender a escribir directivas personalizado es similar a aprender un nuevo idioma.  
*Este artículo se centra en la solución de problemas de configuración de directivas personalizadas de Azure AD B2C y no de las aplicaciones de usuarios de confianza o su biblioteca de identidades.*



## <a name="xml-editing-inproperly-formatted-xml-is-the-most-common-error"></a>La edición de XML, el formato inadecuado de XML es el error más común.

Un buen editor de XML que muestre XML nativo, contenido codificado por colores, que rellene previamente términos comunes, mantenga los elementos XML indexados y se pueda validar con esquemas resulta casi esencial.  Dos de nuestros favoritos son:

* [Código de VS](https://code.visualstudio.com/)
* [Notepad++](https://notepad-plus-plus.org/)

Obtenga la definición de esquema XML `TrustFrameworkPolicy_0.3.0.0.xsd` de la carpeta raíz del paquete de inicio. La validación del esquema XML identifica los errores antes de realizar la carga.  Busque en la documentación de su editor XML `XML tools` y `XML Validation`

Una revisión rápida de las reglas de XML puede ser muy útil porque Azure AD B2C rechazará todo error de formato que detecte.  En ocasiones, un formato XML incorrecto puede dar lugar a mensajes de error que resultan engañosos.

## <a name="uploading-policies-and-policy-validation"></a>Validación de directivas y carga

 La validación de **carga** es automática y la mayoría de los errores dará lugar a la anulación de una carga.  ** Tenga en cuenta que la validación incluye el archivo de directiva que intenta cargar y la cadena de archivos a la que se hace referencia.  `RP policy file > Extensions file > Base File`Errores de validación comunes incluyen:

Fragmento de código de error: `... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* ClaimType puede estar mal escrito o no existir en el esquema.
* Se deben definir valores de ClaimType en al menos uno de los archivos de la directiva.  Por ejemplo, ` <ClaimType Id="socialIdpUserId">`.
* Si ClaimType se define en el archivo de extensiones, pero se usa en TechnichalProfile en el archivo base, al cargar dicho archivo se producirá un error.

Fragmento de código de error: `...males a reference to a ClaimsTransformation with id...`
* Igual que el anterior.

Fragmento de código de error: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Compruebe que TenantId en los elementos **<TrustFrameworkPolicy>** y **<BasePolicy>** coincide con su inquilino de destino B2C.  



## <a name="runtime-troubleshooting"></a>Solución de problemas de tiempo de ejecución

* Use `Run Now` y `https://jwt.io` para probar sus directivas con independencia de su aplicación web o móvil. Este sitio web actúa como una aplicación de usuario de confianza y muestra el contenido del token JWT generado por la directiva de Azure AD B2C.  Para crear una aplicación de prueba en el marco de experiencia de identidad:
    * Nombre: TestApp
    * Aplicación web/API web: No
    * Cliente nativo: No

* Use [fiddler](http://www.telerik.com/fiddler) para realizar el seguimiento de los mensajes entre su explorador cliente y Azure AD B2C.  Obtendrá una indicación de dónde se encuentra su recorrido de usuario en los pasos de orquestación.

* Use **Application Insights** en **Modo de desarrollo** para realizar un seguimiento del comportamiento de su recorrido de usuario del marco de experiencia de identidad (IEF).  En **Modo de desarrollo** es posible observar el intercambio de notificaciones entre el IEF y los diversos proveedores de notificaciones definidos por perfiles técnicos, como proveedores de identidades, servicios de bases de API, el directorio de usuario de Azure AD B2C (directorio de AAD) y otros servicios como Multi-Factor-Authentication.  

## <a name="recommended-practies"></a>Procedimientos recomendados

**Mantenga varias versiones de sus escenarios y agrúpelas en un proyecto con su aplicación.** Los archivos base, de extensiones y de usuarios de confianza (RP) dependen directamente unos de otros, así que guárdelos como un grupo y mantenga versiones de trabajo aparte a medida que se materializan nuevas características en sus directivas.  Organícelos en su propio sistema de archivos con el código de aplicación con el que interactúan.  Las aplicaciones pueden invocar muchas directivas de RP diferentes en un inquilino y convertirse en dependientes de las notificaciones que esperan de sus directivas de Azure AD B2C.

**Desarrolle y pruebe perfiles técnicos con recorridos de usuario conocidos.**  Use directivas de paquete de inicio probadas para configurar perfiles técnicos y pruébelas por separado antes de incorporar sus propios recorridos de usuario.

**Desarrolle y pruebe recorridos de usuario con perfiles técnicos probados**. Cambie los pasos de orquestación de un recorrido de usuario paso a paso y construya progresivamente sus escenarios deseados.







Comencemos:

1. Descargue "active-directory-b2c-custom-policy-starterpack" de GitHub.  [Descargue el archivo ZIP](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o ejecute
### <a name="built-in"></a>Característica integrada



El marco de directiva extensible de Azure Active Directory (Azure AD) B2C es la fortaleza esencial del servicio. Las directivas describen totalmente las experiencias de identidad del consumidor como el registro, el inicio de sesión y la edición de perfil. Por ejemplo, una directiva de registro le permite controlar comportamientos configurando los siguientes valores:

* Tipos de cuenta (cuentas sociales como Facebook, o cuentas locales como la dirección de correo electrónico) que los consumidores pueden usar para registrarse en la aplicación.
* Atributos (por ejemplo, nombre, código postal, número de calzado, etc.) que se recopilarán del consumidor durante el registro.
* Uso de Multi-Factor Authentication.
* La apariencia de todas las páginas de registro.
* Información (que se manifiesta como notificaciones en un token) que la aplicación recibe cuando se completa la ejecución de la directiva.

Puede crear varias directivas de diferentes tipos en su inquilino y usarlas en sus aplicaciones según sea necesario. Las directivas se pueden volver a usar en todas las aplicaciones. Esto permite a los desarrolladores definir y modificar experiencias de identidad de consumidor con cambios mínimos o ningún cambio en su código.

Las directivas están disponibles para usarlas mediante una interfaz de usuario sencilla. Su aplicación desencadena una directiva mediante una solicitud de autenticación HTTP estándar (pasando un parámetro de directiva en la solicitud) y recibe un token personalizado como respuesta. Por ejemplo, la única diferencia entre las solicitudes que invocan una directiva de registro y las que invocan una directiva de inicio de sesión es el nombre de la directiva que se usa en el parámetro de cadena de consulta "p":

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

Para más información sobre el marco de directivas, consulte esta [entrada de blog](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-policy"></a>Creación de una directiva de registro
Para habilitar el registro en su aplicación, deberá crear una directiva de registro. Esta directiva describe las experiencias que tendrán los consumidores durante el registro y el contenido de los tokens que recibirá la aplicación en inicios de sesión completados correctamente.

1. [Siga estos pasos para ir a la hoja de características de B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **Directivas de registro**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. El **Nombre** determina el nombre de la directiva de registro usado por su aplicación. Por ejemplo, escriba "SiUp".
5. Haga clic en **Proveedores de identidades** y seleccione "Email signup" (Registro de correo electrónico). También puede seleccionar proveedores de identidades sociales, si ya se han configurado. Haga clic en **OK**.
6. Haga clic en **Atributos de registro**. Aquí elige atributos que quiere recopilar del consumidor durante el registro. Por ejemplo, seleccione "Ciudad o región", "Nombre para mostrar" y "Código postal". Haga clic en **OK**.
7. Haga clic en **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens enviados de vuelta a su aplicación después de una experiencia de registro correcta. Por ejemplo, seleccione "Nombre para mostrar", "Proveedor de identidades", "Código Postal", "El usuario es nuevo" e "Id. de objeto del usuario".
8. Haga clic en **Crear**. Tenga en cuenta que la directiva que se acaba de crear aparece como "**B2C_1_SiUp**" (el fragmento **B2C\_1\_** se agrega automáticamente) en la hoja **Sign-up policies** (Directivas de registro).
9. Para abrir la directiva, haga clic en "**B2C_1_SiUp**".
10. Seleccione "Contoso B2C app" en el menú desplegable **Applications** (Aplicaciones) y `https://localhost:44321/` en el menú desplegable **Reply URL / Redirect URI** (Dirección URL de respuesta/URI de redirección).
11. Haga clic en **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del consumidor de registro en su aplicación.
    
    > [!NOTE]
    > Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.
    > 
    > 

## <a name="create-a-sign-in-policy"></a>Uso de una directiva de inicio de sesión
Para habilitar el inicio de sesión en la aplicación, deberá crear una directiva de inicio de sesión. Esta directiva describe las experiencias que tendrán los consumidores durante el inicio de sesión y el contenido de los tokens que recibirá la aplicación en inicios de sesión correctos.

1. [Siga estos pasos para ir a la hoja de características de B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **Directivas de inicio de sesión**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. El **Nombre** determina el nombre de la directiva de inicio de sesión usada por su aplicación. Por ejemplo, escriba "SiIn".
5. Haga clic en **Proveedores de identidades** y seleccione "Local Account SignIn" (Inicio de sesión de cuenta local). También puede seleccionar proveedores de identidades sociales, si ya se han configurado. Haga clic en **OK**.
6. Haga clic en **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens enviados de vuelta a su aplicación después de una experiencia de inicio de sesión correcta. Por ejemplo, seleccione "Nombre para mostrar", "Proveedor de identidades", "Código Postal" e "Id. de objeto del usuario". Haga clic en **OK**.
7. Haga clic en **Crear**. Tenga en cuenta que la directiva que se acaba de crear aparece como "**B2C_1_SiIn**" (el fragmento **B2C\_1\_** se agrega automáticamente) en la hoja **Sign-in policies** (Directivas de inicio de sesión).
8. Para abrir la directiva, haga clic en "**B2C_1_SiIn**".
9. Seleccione "Contoso B2C app" en el menú desplegable **Applications** (Aplicaciones) y `https://localhost:44321/` en el menú desplegable **Reply URL / Redirect URI** (Dirección URL de respuesta/URI de redirección).
10. Haga clic en **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del consumidor de inicio de sesión en su aplicación.
    
    > [!NOTE]
    > Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.
    > 
    > 

## <a name="create-a-sign-up-or-sign-in-policy"></a>Creación de una directiva de registro o de inicio de sesión
Esta directiva controla las experiencias de registro y de inicio de sesión del cliente con una sola configuración. A los consumidores se les lleva por la ruta correcta (registro o inicio de sesión) según el contexto. También describe el contenido de los tokens que recibirá la aplicación cuando el registro o el inicio de sesión sean correctos.  [Aquí puede encontrar](active-directory-b2c-devquickstarts-web-dotnet-susi.md)código de ejemplo de la directiva de registro o de inicio de sesión.

1. [Siga estos pasos para ir a la hoja de características de B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **Sign-up or sign-in policies**(Directivas de registro o de inicio de sesión).
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. El **Nombre** determina el nombre de la directiva de registro usado por su aplicación. Por ejemplo, escriba "SiUpIn".
5. Haga clic en **Proveedores de identidades** y seleccione "Email signup" (Registro de correo electrónico). También puede seleccionar proveedores de identidades sociales, si ya se han configurado. Haga clic en **OK**.
6. Haga clic en **Atributos de registro**. Aquí elige atributos que quiere recopilar del consumidor durante el registro. Por ejemplo, seleccione "Ciudad o región", "Nombre para mostrar" y "Código postal". Haga clic en **OK**.
7. Haga clic en **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens enviados de vuelta a su aplicación después de una experiencia de registro o de inicio de sesión correcta. Por ejemplo, seleccione "Nombre para mostrar", "Proveedor de identidades", "Código Postal", "El usuario es nuevo" e "Id. de objeto del usuario".
8. Haga clic en **Crear**. Tenga en cuenta que la directiva que se acaba de crear aparece como "**B2C_1_SiUpIn**" (el fragmento **B2C\_1\_** se agrega automáticamente) en la hoja **Sign-up or sign-in policies** (Directivas de inicio de sesión o de registro).
9. Para abrir la directiva, haga clic en "**B2C_1_SiUpIn**".
10. Seleccione "Contoso B2C app" en el menú desplegable **Applications** (Aplicaciones) y `https://localhost:44321/` en el menú desplegable **Reply URL / Redirect URI** (Dirección URL de respuesta/URI de redirección).
11. Haga clic en **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del consumidor de registro o de inicio de sesión tal como se configuró.
    
    > [!NOTE]
    > Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.
    > 
    > 

## <a name="create-a-profile-editing-policy"></a>Creación de una directiva de edición de perfil
Para habilitar la edición de perfiles en su aplicación, deberá crear una directiva de edición de perfiles. Esta directiva describe las experiencias que tendrán los consumidores durante la edición de perfiles y el contenido de los tokens que recibirá la aplicación al finalizar correctamente.

1. [Siga estos pasos para ir a la hoja de características de B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **Directivas de edición de perfiles**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. El **Nombre** determina el nombre de la directiva de edición de perfiles usada por su aplicación. Por ejemplo, escriba "SiPe".
5. Haga clic en **Proveedores de identidades** y seleccione "Inicio de sesión de cuenta local". También puede seleccionar proveedores de identidades sociales, si ya se han configurado. Haga clic en **OK**.
6. Haga clic en **Atributos de perfil**. Aquí se eligen los atributos que el consumidor puede ver y editar. Por ejemplo, seleccione "Ciudad o región", "Nombre para mostrar" y "Código postal". Haga clic en **OK**.
7. Haga clic en **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens a su aplicación después de una experiencia de edición de perfiles correcta. Por ejemplo, seleccione "Nombre para mostrar" y "Código postal".
8. Haga clic en **Crear**. Tenga en cuenta que la directiva que se acaba de crear aparece como "**B2C_1_SiPe**" (el fragmento **B2C\_1\_** se agrega automáticamente) en la hoja **Profile editing policies** (Directivas de edición de perfiles).
9. Para abrir la directiva, haga clic en "**B2C_1_SiPe**".
10. Seleccione "Contoso B2C app" en el menú desplegable **Applications** (Aplicaciones) y `https://localhost:44321/` en el menú desplegable **Reply URL / Redirect URI** (Dirección URL de respuesta/URI de redirección).
11. Haga clic en **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del consumidor de edición de perfiles en su aplicación.
    
    > [!NOTE]
    > Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.
    > 
    > 

## <a name="create-a-password-reset-policy"></a>Crear una directiva de restablecimiento de contraseña
Para habilitar en su aplicación el restablecimiento de contraseña específica, debe crear una directiva de restablecimiento de contraseña. Tenga en cuenta que la opción de restablecimiento de contraseña de todos los inquilinos especificada [aquí](active-directory-b2c-reference-sspr.md) sigue siendo aplicable para las directivas de inicio de sesión. Esta directiva describe las experiencias que tendrán los consumidores durante el restablecimiento de contraseña y el contenido de los tokens que recibirá la aplicación al finalizar correctamente.

1. [Siga estos pasos para ir a la hoja de características de B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **Directivas de restablecimiento de contraseña**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. El **nombre** determina el nombre de la directiva de restablecimiento de contraseña que utiliza la aplicación. Por ejemplo, escriba "SSPR".
5. Haga clic en **Proveedores de identidades** y seleccione "Reset password using email address" (Restablecer contraseña mediante la dirección de correo electrónico). Haga clic en **OK**.
6. Haga clic en **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens a su aplicación después de una experiencia de restablecimiento de contraseña correcta. Por ejemplo, seleccione "Id. de objeto del usuario".
7. Haga clic en **Crear**. Tenga en cuenta que la directiva que se acaba de crear aparece como "**B2C_1_SSPR**" (el fragmento **B2C\_1\_** se agrega automáticamente) en la hoja **Password reset policies** (Directivas de restablecimiento de contraseña).
8. Para abrir la directiva, haga clic en "**B2C_1_SSPR**".
9. Seleccione "Contoso B2C app" en el menú desplegable **Applications** (Aplicaciones) y `https://localhost:44321/` en el menú desplegable **Reply URL / Redirect URI** (Dirección URL de respuesta/URI de redirección).
10. Haga clic en **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del usuario de restablecimiento de contraseña en su aplicación.
    
    > [!NOTE]
    > Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.
    > 
    > 

## <a name="how-to-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>¿Cómo vincular una directiva de inicio de sesión o de registro con una directiva de restablecimiento de contraseña?
Al crear una directiva de inicio de sesión o de registro (con cuentas locales), el consumidor verá un vínculo “He olvidado mi contraseña” en la primera página de la experiencia. Al hacer clic en este vínculo, no se desencadena automáticamente una directiva de restablecimiento de contraseña. En su lugar, se devuelve un código de error específico `AADB2C90118` a su aplicación. La aplicación debe controlar esto e invocar una directiva de restablecimiento de contraseña específica. Se puede encontrar una muestra de este enfoque basado en vincular directivas [aquí](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

## <a name="additional-resources"></a>Recursos adicionales
* [Configuración de token, sesión e inicio de sesión único](active-directory-b2c-token-session-sso.md).
* [Deshabilitación de la comprobación de correos electrónicos durante la suscripción de consumidores](active-directory-b2c-reference-disable-ev.md)


