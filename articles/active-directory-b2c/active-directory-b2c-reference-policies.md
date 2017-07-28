---
title: 'Azure Active Directory B2C: directivas integradas | Microsoft Docs'
description: "Tema sobre el marco de directiva extensible de Azure Active Directory B2C y sobre cómo crear distintos tipos de directiva"
services: active-directory-b2c
documentationcenter: 
author: sama
manager: mbaldwin
editor: bryanla
ms.assetid: 0d453e72-7f70-4aa2-953d-938d2814d5a9
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: sama
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: e6943eb619963dd56b1c3909a57ae7d6cefd0ddc
ms.contentlocale: es-es
ms.lasthandoff: 06/21/2017


---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C: directivas integradas


El marco de directiva extensible de Azure Active Directory (Azure AD) B2C es la fortaleza esencial del servicio. Las directivas describen totalmente las experiencias de identidad del consumidor como el registro, el inicio de sesión y la edición de perfil. Por ejemplo, una directiva de registro le permite controlar comportamientos configurando los siguientes valores:

* Tipos de cuenta (cuentas sociales como Facebook, o cuentas locales como direcciones de correo electrónico) que los consumidores pueden usar para registrarse en la aplicación
* Atributos (por ejemplo, nombre, código postal, número de calzado, etc.) que se recopilarán del consumidor durante el registro
* Uso de Azure Multi-Factor Authentication
* La apariencia de todas las páginas de registro
* Información (que se manifiesta como notificaciones en un token) que recibe la aplicación cuando se completa la ejecución de la directiva.

Puede crear varias directivas de diferentes tipos en su inquilino y usarlas en sus aplicaciones según sea necesario. Las directivas se pueden volver a usar en todas las aplicaciones. Esta flexibilidad permite a los desarrolladores definir y modificar experiencias de identidad de consumidor con cambios mínimos o ningún cambio en su código.

Las directivas están disponibles para usarlas mediante una interfaz de usuario sencilla. Su aplicación desencadena una directiva mediante una solicitud de autenticación HTTP estándar (pasando un parámetro de directiva en la solicitud) y recibe un token personalizado como respuesta. Por ejemplo, la única diferencia que hay entre las solicitudes que invocan una directiva de registro y las que invocan una directiva de inicio de sesión es el nombre de la directiva que se usa en el parámetro de cadena de consulta "p":

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

Para obtener más información sobre el marco de directivas, consulte [esta entrada de blog sobre Azure AD B2C en el Blog de Enterprise Mobility + Security](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-or-sign-in-policy"></a>Creación de una directiva de registro o de inicio de sesión
Esta directiva controla las experiencias de registro y de inicio de sesión del cliente con una sola configuración. A los consumidores se les lleva por la ruta correcta (registro o inicio de sesión) según el contexto. 

La directiva también describe el contenido de los tokens que recibe la aplicación cuando el registro o el inicio de sesión se llevan a cabo correctamente. [En este artículo](active-directory-b2c-devquickstarts-web-dotnet-susi.md) se proporciona un código de ejemplo de la directiva de registro o de inicio de sesión.  Le recomendamos que use esta directiva en vez de una directiva de registro y una directiva de inicio de sesión.  

1. [En primer lugar, siga estos pasos para ir a la hoja de características de B2C de Azure Portal](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).

2. Seleccione **Directivas de inicio de sesión o de registro**.

3. En la parte superior de la hoja, seleccione **+Agregar**.

4. En **Nombre** se determina el nombre de la directiva de registro que usa la aplicación. Por ejemplo, escriba **SiUpIn**.

5. Seleccione **Proveedores de identidades** y seleccione **Registro por correo electrónico**. También puede seleccionar proveedores de identidades sociales, en el caso de que ya se hayan configurado. Haga clic en **Aceptar**.

6. Seleccione **Atributos de registro**. Aquí elige atributos que quiere recopilar del consumidor durante el registro. Por ejemplo, seleccione **País o región**, **Nombre para mostrar** y **Código postal**. Haga clic en **Aceptar**.

7. Seleccione **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens enviados de vuelta a su aplicación después de una experiencia de registro o de inicio de sesión correcta. Por ejemplo, seleccione **Nombre para mostrar**, **Proveedor de identidades**, **Código postal**, **El usuario es nuevo** e **Id. de objeto del usuario**.

8. Seleccione **Crear**. Tenga en cuenta que la directiva que ha creado aparece como **B2C_1_SiUpIn** (el fragmento **B2C\_1\_** se agrega automáticamente) en la hoja **Directivas de inicio de sesión o de registro**.

9. Para abrir la directiva, haga clic en **B2C_1_SiUpIn**.

10. En el menú desplegable **Aplicaciones**, seleccione **Aplicación Contoso B2C**. En el menú desplegable **URL de respuesta / URI de redirección**, seleccione `https://localhost:44321/`.

11. Seleccione **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del consumidor de registro o de inicio de sesión.
    
    > [!NOTE]
    > Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.
    > 
    > 

## <a name="create-a-sign-up-policy"></a>Creación de una directiva de registro
Para habilitar el registro en su aplicación, debe crear una directiva de registro. Esta directiva describe las experiencias que tendrán los consumidores durante el registro y el contenido de los tokens que recibe la aplicación en registros completados correctamente.

1. [En primer lugar, siga estos pasos para ir a la hoja de características de B2C de Azure Portal](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).

2. Seleccione **Directivas de registro**.

3. Seleccione **+Agregar** en la parte superior de la hoja.

4. En **Nombre** se determina el nombre de la directiva de registro que usa la aplicación. Por ejemplo, escriba **SiUp**.

5. Seleccione **Proveedores de identidades** y seleccione **Registro por correo electrónico**. También puede seleccionar proveedores de identidades sociales, en el caso de que ya se hayan configurado. Haga clic en **Aceptar**.

6. Seleccione **Atributos de registro**. Aquí elige atributos que quiere recopilar del consumidor durante el registro. Por ejemplo, seleccione **País o región**, **Nombre para mostrar** y **Código postal** y, luego, haga clic en **Aceptar**.

7. Seleccione **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens enviados de vuelta a su aplicación después de una experiencia de registro correcta. Por ejemplo, seleccione **Nombre para mostrar**, **Proveedor de identidades**, **Código postal**, **El usuario es nuevo** e **Id. de objeto del usuario**.

8. Seleccione **Crear**. Tenga en cuenta que la directiva creada aparece como **B2C_1_SiUp** (el fragmento **B2C\_1\_** se agrega automáticamente) en la hoja **Directivas de registro**.

9. Para abrir la directiva, haga clic en **B2C_1_SiUp**.

10. Seleccione **Aplicación Contoso B2C** en el menú desplegable **Aplicaciones** y `https://localhost:44321/` en el menú desplegable **Dirección URL de respuesta / URI de redirección**.

11. Seleccione **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del consumidor de registro en su aplicación.
    
    > [!NOTE]
    > Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.
    > 
    > 

## <a name="create-a-sign-in-policy"></a>Uso de una directiva de inicio de sesión
Para habilitar el inicio de sesión en la aplicación, debe crear una directiva de inicio de sesión. Esta directiva describe las experiencias que tendrán los consumidores durante el inicio de sesión y el contenido de los tokens que recibe la aplicación en inicios de sesión completados correctamente.

1. [En primer lugar, siga estos pasos para ir a la hoja de características de B2C de Azure Portal](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).

2. Seleccione **Directivas de inicio de sesión**.

3. Seleccione **+Agregar** en la parte superior de la hoja.

4. En **Nombre** se determina el nombre de la directiva de inicio de sesión que usa la aplicación. Por ejemplo, escriba **SiIn**.

5. Seleccione **Proveedores de identidades** e **Inicio de sesión de cuenta local**. También puede seleccionar proveedores de identidades sociales, en el caso de que ya se hayan configurado. Haga clic en **Aceptar**.

6. Seleccione **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens enviados de vuelta a su aplicación después de una experiencia de inicio de sesión correcta. Por ejemplo, seleccione **Nombre para mostrar**, **Proveedor de identidades**, **Código Postal** e **Id. de objeto del usuario**. Luego, haga clic en **Aceptar**.

7. Seleccione **Crear**. Tenga en cuenta que la directiva creada aparece como **B2C_1_SiIn** (el fragmento **B2C\_1\_** se agrega automáticamente) en la hoja **Directivas de inicio de sesión**.

8. Para abrir la directiva, haga clic en **B2C_1_SiIn**.

9. En el menú desplegable **URL de respuesta / URI de redirección**, seleccione **Aplicación Contoso B2C** y `https://localhost:44321/`.

10. Seleccione **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del consumidor de inicio de sesión en su aplicación.
    
    > [!NOTE]
    > Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.
    > 
    > 

## <a name="create-a-profile-editing-policy"></a>Creación de una directiva de edición de perfil
Para habilitar la edición de perfiles en su aplicación, debe crear una directiva de edición de perfiles. Esta directiva describe las experiencias que tendrán los consumidores durante la edición de perfiles y el contenido de los tokens que recibe la aplicación al finalizar correctamente.

1. En primer lugar, [siga estos pasos para ir a la hoja de características de B2C de Azure Portal](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).

1. Seleccione **Directivas de edición de perfiles**.

2. Seleccione **+Agregar** en la parte superior de la hoja.

3. En **Nombre** se determina el nombre de la directiva de edición de perfiles que usa la aplicación. Por ejemplo, escriba **SiPe**.

4. Seleccione **Proveedores de identidades** e **Inicio de sesión de cuenta local**. También puede seleccionar proveedores de identidades sociales, en el caso de que ya se hayan configurado. Haga clic en **Aceptar**.

5. Seleccione **Atributos de perfil**. Aquí se eligen los atributos que el consumidor puede ver y editar. Por ejemplo, seleccione **País o región**, **Nombre para mostrar** y **Código postal** y, luego, seleccione **Aceptar**.

6. Seleccione **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens enviados de vuelta a su aplicación después de una experiencia de edición de perfiles correcta. Por ejemplo, seleccione **Nombre para mostrar** y **Código postal**.

8. Seleccione **Crear**. Tenga en cuenta que la directiva creada aparece como **B2C_1_SiPe** (el fragmento **B2C\_1\_** se agrega automáticamente) en la hoja **Directivas de edición de perfil**.

9. Para abrir la directiva, haga clic en **B2C_1_SiPe**.

10. En el menú desplegable **Aplicaciones**, seleccione **Aplicación Contoso B2C**. En el menú desplegable **URL de respuesta / URI de redirección**, seleccione `https://localhost:44321/`.

11. Seleccione **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del consumidor de edición de perfiles en su aplicación.
    
    > [!NOTE]
    > Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.
    > 
    > 

## <a name="create-a-password-reset-policy"></a>Crear una directiva de restablecimiento de contraseña
Para habilitar en su aplicación el restablecimiento de contraseña específica, deberá crear una directiva de restablecimiento de contraseña. Tenga en cuenta que la opción de restablecimiento de contraseña de todos los inquilinos especificada en [Azure Active Directory B2C: configurar el autoservicio de restablecimiento de contraseña para los consumidores](active-directory-b2c-reference-sspr.md) sigue siendo aplicable a las directivas de inicio de sesión. 

Esta directiva describe las experiencias que tendrán los consumidores durante el restablecimiento de contraseña y el contenido de los tokens que recibe la aplicación una vez restablecida correctamente la contraseña.

1. [En primer lugar, siga estos pasos para ir a la hoja de características de B2C de Azure Portal.](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)

2. Seleccione **Directivas de restablecimiento de contraseña**.

3. Seleccione **+Agregar** en la parte superior de la hoja.

4. En **Nombre** se determina el nombre de la directiva de restablecimiento de contraseña que usa la aplicación. Por ejemplo, escriba **SSPR**.

5. Seleccione **Proveedores de identidades** y **Restablecer contraseña mediante la dirección de correo electrónico**. Haga clic en **Aceptar**.

6. Seleccione **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens enviados de vuelta a su aplicación después de una experiencia de restablecimiento de contraseña correcta. Por ejemplo, seleccione **Id. de objeto del usuario**.

7. Seleccione **Crear**. Tenga en cuenta que la directiva creada aparece como **B2C_1_SSPR** (el fragmento **B2C\_1\_** se agrega automáticamente) en la hoja **Directivas de restablecimiento de contraseña**.

8. Para abrir la directiva, haga clic en **B2C_1_SSPR**.

9. En el menú desplegable **Aplicaciones**, seleccione **Aplicación Contoso B2C**. En el menú desplegable **URL de respuesta / URI de redirección**, seleccione `https://localhost:44321/`.

10. Seleccione **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del usuario de restablecimiento de contraseña en su aplicación.
    
    > [!NOTE]
    > Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.
    > 
    > 

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>¿Cómo puedo vincular una directiva de inicio de sesión o de registro con una directiva de restablecimiento de contraseña?
Al crear una directiva de inicio de sesión o de registro (con cuentas locales), verá el vínculo **¿Olvidó la contraseña?** en la primera página de la experiencia. Al hacer clic en este vínculo, no se desencadena automáticamente ninguna directiva de restablecimiento de contraseña, 

sino que se devuelve a la aplicación el código de error **`AADB2C90118`**. La aplicación debe controlar este código de error invocando una directiva de restablecimiento de contraseña específica. Para obtener más información, consulte un [ejemplo en el que se muestra el método para vincular directivas](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>¿Debo usar una directiva de inicio de sesión o de registro o una directiva de inicio de sesión y una directiva de registro?
Le recomendamos que use una directiva de inicio de sesión o de registro en vez de una directiva de inicio de sesión y una directiva de registro.  

La directiva de inicio de sesión o de registro tiene más capacidades que la directiva de inicio de sesión. También le permite usar la personalización de la interfaz de usuario de la página y tiene una mejor compatibilidad con la localización. 

La directiva de inicio de sesión se recomienda si no necesita localizar las directivas, solo necesita unas capacidades de personalización secundarias para la personalización de marca y quiere que el restablecimiento de contraseña esté integrado en ella.

## <a name="next-steps"></a>Pasos siguientes
* [Configuración de token, sesión e inicio de sesión único](active-directory-b2c-token-session-sso.md)
* [Deshabilitación de la comprobación de correos electrónicos durante la suscripción de consumidores](active-directory-b2c-reference-disable-ev.md)


