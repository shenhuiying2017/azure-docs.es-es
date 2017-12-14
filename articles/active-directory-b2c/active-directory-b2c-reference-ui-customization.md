---
title: "Personalización de la interfaz de usuario - Azure AD B2C | Microsoft Docs"
description: "Tema sobre las características de personalización de la interfaz de usuario de Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: 99f5a391-5328-471d-a15c-a2fafafe233d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: be3fe7199308606aaab002290319df9c82149433
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Azure Active Directory B2C: personalización de la interfaz de usuario de Azure AD B2C

La experiencia del usuario es primordial en una aplicación de cliente.  Aumentar su base de clientes mediante el diseño de experiencias de usuario con la apariencia de su marca. Azure Active Directory B2C (Azure AD B2C) permite personalizar las páginas de registro, inicio de sesión, edición de perfil y restablecimiento de contraseña del cliente con control perfecto de píxeles.

> [!NOTE]
> La característica de personalización de la interfaz de usuario de la página que se describe en este artículo no se aplica a la directiva de inicio de sesión único, a la página de restablecimiento de contraseña adjunta y a los correos electrónicos de verificación.  Estas características utilizan en su lugar la [característica de personalización de marca de empresa](../active-directory/customize-branding.md).
>
> De manera similar, si un usuario inicia una directiva de edición de perfil *antes* de iniciar sesión, se redirigirá al usuario a una página que se puede personalizar con la [característica de personalización de marca de la empresa](../active-directory/customize-branding.md).

En este artículo se tratan los temas siguientes:

* La característica de personalización de la interfaz de usuario de la página.
* Una herramienta para cargar el contenido HTML en Azure Blob Storage para su uso con la característica de personalización de la interfaz de usuario de la página.
* Los elementos de la interfaz de usuario que usa Azure AD B2C que se pueden personalizar con hojas de estilo CSS.
* Prácticas recomendadas al usar esta característica.

## <a name="the-page-ui-customization-feature"></a>La característica de personalización de la interfaz de usuario de la página

Puede personalizar la apariencia de las páginas de registro, inicio de sesión, restablecimiento de contraseña y edición de perfil del cliente (mediante la configuración de [directivas](active-directory-b2c-reference-policies.md)). Sus clientes tendrán experiencias más libres al navegar entre su aplicación y las páginas que Azure AD B2C sirve.

A diferencia de otros servicios con opciones de interfaz de usuario, Azure AD B2C utiliza un enfoque simple y moderno a la personalización de la interfaz de usuario.

Funciona de la siguiente manera: Azure AD B2C ejecuta código en el explorador del consumidor y usa un enfoque moderno denominado [Uso compartido de recursos entre orígenes (CORS)](http://www.w3.org/TR/cors/).  En tiempo de ejecución, el contenido se carga desde una dirección URL especificada en una directiva. Puede especificar diferentes direcciones URL para distintas páginas. Cuando el contenido cargado desde la dirección URL se combina con un fragmento HTML insertado en Azure AD B2C, se muestra la página al cliente. Todo lo que necesita hacer es:

1. Crear contenido HTML5 con un elemento `<div id="api"></div>` vacío ubicado en algún lugar de `<body>`. Ese elemento marca el lugar en el que se inserta el contenido de Azure AD B2C.
1. Hospedar el contenido en un punto de conexión HTTPS (donde se permite CORS). Tenga en cuenta que los métodos de solicitud GET y OPTIONS se deben habilitar al configurar CORS.
1. Utilice CSS para aplicar estilo a los elementos de la interfaz de usuario que inserta Azure AD B2C.

### <a name="a-basic-example-of-customized-html"></a>Un ejemplo básico de HTML personalizado

A continuación se muestra un ejemplo del contenido HTML más básico que se puede utilizar para probar esta funcionalidad. Use la [herramienta auxiliar](active-directory-b2c-reference-ui-customization-helper-tool.md) para cargar y configurar este contenido en Azure Blob Storage. A continuación, puede comprobar que los botones básicos, sin estilo y los campos de formulario de cada página se muestran y funcionan.

```HTML
<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
    </body>
</html>
```

## <a name="test-out-the-ui-customization-feature"></a>Prueba de la característica de personalización de la interfaz de usuario

¿Desea probar la característica de personalización de la interfaz de usuario con nuestro contenido HTML y CSS de ejemplo?  Hemos proporcionado [una herramienta auxiliar](active-directory-b2c-reference-ui-customization-helper-tool.md) que carga y configura el contenido de ejemplo en Azure Blob Storage.

> [!NOTE]
> El contenido de la interfaz de usuario se puede hospedar en cualquier lugar: en servidores web, CDN, AWS S3, sistemas de uso compartido de archivos, etc. Mientras el contenido se hospede en un punto de conexión HTTPS disponible públicamente con CORS habilitado, puede continuar. Almacenamiento de blobs de Azure se usa solo con fines ilustrativos.
>

## <a name="the-ui-fragments-embedded-by-azure-ad-b2c"></a>Fragmentos de la interfaz de usuario insertados por Azure AD B2C

En las secciones siguientes se muestran fragmentos de HTML5 que Azure AD B2C combina en el elemento `<div id="api"></div>` que se encuentra en su contenido. **No inserte estos fragmentos en el contenido de HTML 5.** El servicio Azure AD B2C los inserta en tiempo de ejecución. Use estos fragmentos como referencia al diseñar sus propias hojas de estilo CSS.

### <a name="fragment-inserted-into-the-identity-provider-selection-page"></a>Fragmento insertado en la "página de selección de proveedores de identidades"

Esta página contiene una lista de proveedores de identidades que el usuario puede elegir durante el registro o el inicio de sesión. Estos botones incluyen los proveedores de identidades sociales como Facebook y Google+ o las cuentas locales (basadas en una dirección de correo electrónico o un nombre de usuario).

```HTML
<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-local-account-sign-up-page"></a>Fragmento insertado en la "página de registro de la cuenta local"

Esta página contiene un formulario para el registro en una cuenta local basada en una dirección de correo electrónico o un nombre de usuario. El formulario puede contener diferentes controles de entrada, como un cuadro de entrada de texto, un cuadro de entrada de contraseña, un botón de radio, cuadros desplegables de selección única y casillas de verificación de selección múltiple.

```HTML
<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-social-account-sign-up-page"></a>Fragmento insertado en la "página de registro de la cuenta de redes sociales"

Esta página puede aparecer al registrarse con una cuenta existente de un proveedor de identidades de redes sociales, como Facebook o Google+.  Se utiliza cuando se debe recopilar información adicional del usuario final mediante un formulario de registro. Esta página es similar a la página de registro de cuenta local (que se muestra en la sección anterior) con la excepción de los campos de entrada de contraseña.

### <a name="fragment-inserted-into-the-unified-sign-up-or-sign-in-page"></a>Fragmento insertado en la "página de registro o de inicio de sesión unificada"

Esta página controla tanto la suscripción como el inicio de sesión de los clientes, que pueden utilizar proveedores de identidades sociales como Facebook o Google +, o cuentas locales.

```HTML
<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>
```

### <a name="fragment-inserted-into-the-multi-factor-authentication-page"></a>Fragmento insertado en la "página de autenticación multifactor"

Esta página permite a los usuarios verificar sus números de teléfono (mediante texto o voz) durante el registro o el inicio de sesión.

```HTML
<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-error-page"></a>Fragmento insertado en la ""página de error"

```HTML
<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>
```

## <a name="localizing-your-html-content"></a>Localización del contenido HTML

Puede localizar el contenido HTML mediante la activación de la característica ["Personalización de idioma"](active-directory-b2c-reference-language-customization.md).  Al habilitar esta característica, Azure AD B2C puede reenviar el parámetro de OpenID Connect, `ui-locales`, al punto de conexión.  El servidor de contenido puede usar este parámetro para proporcionar páginas HTML personalizadas específicas del idioma.

## <a name="things-to-remember-when-building-your-own-content"></a>Elementos que debe recordar al crear su propio contenido

Si piensa usar la característica de personalización de la interfaz de usuario de la página, revise las siguientes prácticas recomendadas:

* No copie el contenido predeterminado de Azure AD B2C ni intente modificarlo. Es preferible generar su propio contenido de HTML5 desde cero y usar la plantilla predeterminada como referencia.
* Por motivos de seguridad, no le permitimos incluir cualquier código JavaScript en su contenido. La mayor parte de lo que necesita debería estar disponible de fábrica. De no ser así, utilice [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) para solicitar una nueva funcionalidad.
* Versiones de explorador admitidas:
  * Internet Explorer 11, 10, Edge
  * Compatibilidad limitada con Internet Explorer 9, 8
  * Google Chrome 42.0 y versiones posteriores
  * Mozilla Firefox 38.0 y versiones posteriores
