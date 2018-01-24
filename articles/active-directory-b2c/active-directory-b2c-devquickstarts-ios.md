---
title: "Uso de AppAuth en una aplicación iOS - Azure Active Directory B2C"
description: "En este artículo se mostrará cómo crear una aplicación iOS que usa AppAuth con Azure Active Directory B2C para administrar las identidades de usuario y autenticar usuarios."
services: active-directory-b2c
documentationcenter: ios
author: PatAltimore
manager: mtillman
editor: parakhj
ms.custom: seo
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objectivec
ms.topic: article
ms.date: 03/07/2017
ms.author: saeeda
ms.openlocfilehash: b4f46129a7a18e4653d714599630d6cdddfff4ed
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: Inicio de sesión con una aplicación iOS

La plataforma Microsoft Identity utiliza estándares abiertos como OAuth2 y OpenID Connect. Usar un protocolo estándar abierto ofrece más opciones a los desarrolladores cuando se selecciona una biblioteca para integrarla con nuestros servicios. Proporcionamos este tutorial y otros similares para ayudar a los desarrolladores a escribir aplicaciones que se conecten con la plataforma de Microsoft Identity. La mayoría de las bibliotecas que implementan [la especificación OAuth2 RFC6749](https://tools.ietf.org/html/rfc6749) pueden conectarse a la plataforma de Microsoft Identity.

> [!WARNING]
> Microsoft no proporciona correcciones para bibliotecas de terceros y no se ha realizado ninguna revisión de esas bibliotecas. Este ejemplo usa una biblioteca de terceros llamada AppAuth cuya compatibilidad se ha probado en escenarios básicos con Azure AD B2C. Los problemas y las solicitudes de características deben dirigirse al proyecto de código abierto de la biblioteca. Para obtener más información, consulte [este artículo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Si no está familiarizado con OAuth2 o con OpenID Connect, es posible que gran parte de esta configuración de ejemplo no tenga mucho sentido para usted. Se recomienda que consulte una breve [información general sobre el protocolo que hemos documentado aquí](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Obtener un directorio de Azure AD B2C
Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor de todos los usuarios, aplicaciones, grupos, etc. Antes de continuar, si no tiene un directorio B2C, [créelo](active-directory-b2c-get-started.md) .

## <a name="create-an-application"></a>Creación de una aplicación
A continuación, debe crear una aplicación en su directorio B2C. El registro de la aplicación proporciona a Azure AD la información que necesita para comunicarse de forma segura con la aplicación. Para crear una aplicación móvil, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

* Incluir un **cliente nativo** en la aplicación.
* Copiar el **identificador de aplicación** asignado a la aplicación. Necesitará este GUID más adelante.
* Configure un **URI de redireccionamiento** con un esquema personalizado (por ejemplo, com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Necesitará este URI más adelante.

## <a name="create-your-policies"></a>Crear sus directivas
En Azure AD B2C, cada experiencia del usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). Esta aplicación contiene una experiencia de identidad: una combinación de inicio de sesión y registro. Cree esta directiva, tal como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Al crear la directiva, tenga en cuenta lo siguiente:

* En **Atributos de registro**, seleccione **Nombre para mostrar**.  Puede seleccionar también otros atributos.
* En **Notificaciones de aplicación**, seleccione las notificaciones **Nombre para mostrar** e **Identificador de objeto del usuario**. Puede elegir también otras notificaciones.
* Copiar el **Nombre** de cada directiva después de crearla. El nombre de la directiva tiene como prefijo `b2c_1_` al guardarla.  Necesitará el nombre de la directiva más adelante.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Después de crear las directivas, está listo para compilar la aplicación.

## <a name="download-the-sample-code"></a>Descarga del código de ejemplo
Hemos proporcionado un ejemplo de trabajo que usa AppAuth con Azure AD B2C [en GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Puede descargar el código y ejecutarlo. Para usar su propio inquilino de Azure AD B2C, siga las instrucciones que aparecen en el archivo [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Este ejemplo se creó con las instrucciones del archivo README del [proyecto AppAuth de iOS en GitHub](https://github.com/openid/AppAuth-iOS). Para más detalles sobre cómo funcionan el ejemplo y la biblioteca, haga referencia al archivo README de AppAuth en GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modificación de la aplicación para usar Azure AD B2C con AppAuth

> [!NOTE]
> AppAuth es compatible con iOS7 y versiones superiores.  Sin embargo, para admitir inicios de sesión sociales en Google, se necesita SFSafariViewController, que requiere iOS 9 o superior.
>

### <a name="configuration"></a>Configuración

Puede configurar la comunicación con Azure AD B2C si especifica tanto los URI del punto de conexión de autorización como del punto de conexión del token.  Para generar estos URI, necesita la información siguiente:
* Identificador de inquilino (por ejemplo, contoso.onmicrosoft.com)
* Nombre de la directiva (por ejemplo, B2C\_1\_SignUpIn)

El URI de punto de conexión de token se puede generar si se reemplaza Tenant\_ID y Policy\_Name en la siguiente dirección URL:

```objc
static NSString *const tokenEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

El URI de punto de conexión de autorización se puede generar si se reemplaza Tenant\_ID y Policy\_Name en la siguiente dirección URL:

```objc
static NSString *const authorizationEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Ejecute el código siguiente para crear el objeto AuthorizationServiceConfiguration:

```objc
OIDServiceConfiguration *configuration = 
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autorización

Después de configurar o recuperar una configuración de servicio de autorización, es posible construir una autorización. Para crear la solicitud, necesita la información siguiente:  
* Id. de cliente (por ejemplo, 00000000-0000-0000-0000-000000000000)
* URI de redireccionamiento con un esquema personalizado (por ejemplo, com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect)

Ambos elementos se deberían haber guardado cuando [registró la aplicación](#create-an-application).

```objc
OIDAuthorizationRequest *request = 
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow = 
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Para configurar la aplicación y controlar el redireccionamiento al URI con el esquema personalizado, deberá actualizar la lista de "Esquemas de dirección URL" en Info.pList:
* Abra Info.pList.
* Mantenga el puntero sobre una fila como "Código de tipo de SO del lote" y haga clic en el símbolo \+.
* Cambie el nombre de la fila nueva "Tipos de dirección URL".
* Haga clic en la flecha a la izquierda de "Tipos de dirección URL" para abrir el árbol.
* Haga clic en la flecha situada a la izquierda de "Elemento 0" para abrir el árbol.
* Cambie el nombre del primer elemento de Elemento 0 a "Combinaciones de URL".
* Haga clic en la flecha a la izquierda de "Combinaciones de URL" para abrir el árbol.
* En la columna "Valor", hay un campo en blanco a la izquierda de "Elemento 0" debajo "Combinaciones de URL".  Establezca el valor al esquema único de la aplicación.  Debe coincidir con el esquema de redirectURL cuando se crea el objeto OIDAuthorizationRequest.  En el ejemplo, se usa el esquema "com.onmicrosoft.fabrikamb2c.exampleapp".

Consulte la [guía de AppAuth](https://openid.github.io/AppAuth-iOS/) sobre cómo completar el resto del proceso. Si necesita comenzar rápidamente con una aplicación de trabajo, extraiga [el ejemplo](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Siga los pasos que aparecen en el archivo [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) para escribir su propia configuración de Azure AD B2C.

Siempre estamos abiertos a todo tipo de comentarios y sugerencias. Si tiene dificultades con este artículo o tiene recomendaciones para mejorar este contenido, le agradeceríamos que escriba sus comentarios en la parte inferior de la página. Si tiene solicitudes de características, agréguelas a [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
