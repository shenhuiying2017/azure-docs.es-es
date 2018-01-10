---
title: "Azure Active Directory B2C: adquisición de un token con una aplicación Android | Microsoft Docs"
description: "En este artículo se mostrará cómo crear una aplicación Android que usa AppAuth con Azure Active Directory B2C para administrar las identidades de usuario y autenticar usuarios."
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: mtillman
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 03/06/2017
ms.author: parakhj
ms.openlocfilehash: dd08c666c09b651e6c0def72a89eda56ba73e34d
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="azure-ad-b2c-sign-in-using-an-android-application"></a>Azure AD B2C: Inicio de sesión con una aplicación Android

La plataforma Microsoft Identity utiliza estándares abiertos como OAuth2 y OpenID Connect. Esto permite a los desarrolladores aprovechar cualquier biblioteca que deseen integrar con nuestros servicios. Para ayudar a los desarrolladores en el uso de nuestra plataforma con otras bibliotecas, hemos escrito algunos tutoriales, como este, para demostrar cómo configurar bibliotecas de terceros para conectarse a la plataforma Microsoft Identity. La mayoría de las bibliotecas que implementan [la especificación OAuth2 RFC6749](https://tools.ietf.org/html/rfc6749) podrán conectarse a la plataforma Microsoft Identity.

> [!WARNING]
> No se proporcionan correcciones para bibliotecas de terceros y no se ha realizado ninguna revisión de esas bibliotecas. Este ejemplo usa una biblioteca de terceros llamada AppAuth cuya compatibilidad se ha probado en escenarios básicos con Azure AD B2C. Los problemas y las solicitudes de características deben dirigirse al proyecto de código abierto de la biblioteca. Vea [este](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) artículo para más información.  
>
>

Si no está familiarizado con OAuth2 o con OpenID Connect, es posible que gran parte de esta configuración de ejemplo no tenga mucho sentido para usted. Se recomienda que consulte una breve [información general sobre el protocolo que hemos documentado aquí](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Obtener un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor para todos los usuarios, las aplicaciones, los grupos, etc. Si aún no tiene uno, [cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar.

## <a name="create-an-application"></a>Creación de una aplicación

A continuación, debe crear una aplicación en su directorio B2C. Esto proporciona a Azure AD la información que necesita para comunicarse de forma segura con la aplicación. Para crear una aplicación móvil, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

* Incluir un **cliente nativo** en la aplicación.
* Copiar el **identificador de aplicación** asignado a la aplicación. Lo necesitará más adelante.
* Configurar un **URI de redireccionamiento** del cliente nativo (por ejemplo, com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). También lo necesitará más adelante.

## <a name="create-your-policies"></a>Crear sus directivas

En Azure AD B2C, cada experiencia del usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). Esta aplicación contiene una experiencia de identidad: una combinación de inicio de sesión y registro. Es necesario crear esta directiva, tal como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Al crear la directiva, tenga en cuenta lo siguiente:

* Elija el **nombre para mostrar** como atributo de registro en la directiva.
* Elija las notificaciones de aplicación de **nombre para mostrar** e **id. de objeto** de cada directiva. Puede elegir también otras notificaciones.
* Copiar el **nombre** de cada directiva después de crearla. Debe tener el prefijo `b2c_1_`.  Necesitará el nombre de la directiva más adelante.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Después de crear las directivas, está listo para compilar la aplicación.

## <a name="download-the-sample-code"></a>Descarga del código de ejemplo

Hemos proporcionado un ejemplo de trabajo que usa AppAuth con Azure AD B2C [en GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Puede descargar el código y ejecutarlo. Puede comenzar rápidamente con su propia aplicación con su propia configuración de Azure AD B2C si sigue las instrucciones que aparecen en el archivo [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

El ejemplo es una modificación del ejemplo que proporciona [AppAuth](https://openid.github.io/AppAuth-Android/). Visite la página para más información sobre AppAuth y sus características.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modificación de la aplicación para usar Azure AD B2C con AppAuth

> [!NOTE]
> AppAuth admite Android API 16 (Jellybean) y versiones superiores. Se recomienda usar API 23 y superior.
>

### <a name="configuration"></a>Configuración

Puede configurar la comunicación con Azure AD B2C si especifica el URI de detección o tanto los URI del punto de conexión de autorización y del punto de conexión del token. Cualquiera sea el caso, necesitará la siguiente información:

* Id. de inquilino (por ejemplo, contoso.onmicrosoft.com)
* Nombre de la directiva (por ejemplo, B2C\_1\_SignUpIn)

Si elige detectar automáticamente los URI de punto de conexión de autorización y token, deberá recuperar información del URI de detección. El URI de detección se puede generar si se reemplaza Tenant\_ID y Policy\_Name en la siguiente dirección URL:

```java
String mDiscoveryURI = "https://login.microsoftonline.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

A continuación, puede adquirir los URI de punto de conexión de autenticación y token y, además, crear un objeto AuthorizationServiceConfiguration; para ello, ejecute lo siguiente:

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

En lugar de usar la detección para obtener los URI de punto de conexión de autorización y token, también puede especificarlos si reemplaza Tenant\_ID y Policy\_Name en las direcciones URL que aparecen a continuación:

```java
String mAuthEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Ejecute el código siguiente para crear el objeto AuthorizationServiceConfiguration:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Autorización

Después de configurar o recuperar una configuración de servicio de autorización, es posible construir una autorización. Para crear la solicitud, necesitará la información siguiente:

* Id. de cliente (ejemplo, 00000000-0000-0000-0000-000000000000)
* URI de redireccionamiento con un esquema personalizado (por ejemplo, com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect)

Ambos elementos se deberían haber guardado cuando [registró la aplicación](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Consulte la [guía de AppAuth](https://openid.github.io/AppAuth-Android/) sobre cómo completar el resto del proceso. Si necesita comenzar rápidamente con una aplicación de trabajo, revise [nuestro ejemplo](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Siga los pasos que aparecen en el archivo [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) para escribir su propia configuración de Azure AD B2C.

Siempre estamos abiertos a todo tipo de comentarios y sugerencias. Si tiene dificultades para completar este tema o tiene recomendaciones para mejorar este contenido, agradecemos que escriba sus comentarios en la parte inferior de la página. Si tiene solicitudes de características, agréguelas a [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

