<properties
	pageTitle="Aplicación Android de Azure AD v2.0 | Microsoft Azure"
	description="Cómo crear una aplicación Android con la que los usuarios pueden iniciar sesión utilizando tanto la cuenta personal de Microsoft como sus cuentas profesionales o educativas y realizar llamadas a la API Graph mediante bibliotecas de terceros."
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="brandwe"/>

#  Adición de inicio de sesión a una aplicación Android mediante una biblioteca de terceros con la API Graph con el punto de conexión v2.0

La plataforma Microsoft Identity utiliza estándares abiertos como OAuth2 y OpenID Connect. Esto permite a los desarrolladores aprovechar cualquier biblioteca que deseen integrar con nuestros servicios. Para ayudar a los desarrolladores en el uso de nuestra plataforma con otras bibliotecas, hemos escrito algunos tutoriales, como este, para demostrar cómo configurar bibliotecas de terceros para conectarse a la plataforma Microsoft Identity. La mayoría de las bibliotecas que implementan [la especificación OAuth2 RFC6749](https://tools.ietf.org/html/rfc6749) podrán conectarse a la plataforma Microsoft Identity.

Esta aplicación permitirá al usuario iniciar sesión en su organización y, después, buscar él mismo en la organización mediante la API Graph.

Si no está familiarizado con OAuth2 o con OpenID Connect, es posible que gran parte de esta configuración de ejemplo no tenga mucho sentido para usted. Se recomienda que consulte una breve [información general sobre el protocolo que hemos documentado aquí](active-directory-v2-protocols-oauth-code.md).


> [AZURE.NOTE]
	Algunas características de nuestra plataforma que tienen una expresión en estos estándares, como el acceso condicional y la administración de directivas de Intune, deben usar nuestras bibliotecas de código abierto de Microsoft Azure Identity.


> [AZURE.NOTE]
	No todas las características y escenarios de Azure Active Directory son compatibles con el punto de conexión v2.0. Para determinar si debe utilizar el punto de conexión v2.0, lea acerca de las [limitaciones de v2.0](active-directory-v2-limitations.md).


## Descargar
El código de este tutorial se conserva [en GitHub](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git). Para continuar, puede [descargar el esqueleto de la aplicación como un archivo .zip](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git/archive/skeleton.zip) o clonar el esqueleto:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

O simplemente descárguelo y empiece a trabajar de inmediato:

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## Registrar una aplicación
Crea una nueva aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com) o siga estos [pasos detallados](active-directory-v2-app-registration.md). Asegúrese de que:

- Anotar el **Id. de aplicación** asignado a su aplicación; lo necesitará pronto.
- Agregar la plataforma **Móvil** a la aplicación.
- Copiar el **URI de redirección** desde el portal. Debe usar el valor predeterminado de `https://login.microsoftonline.com/common/oauth2/nativeclient`.


## Descarga de la biblioteca de terceros nxoauth2 e inicio de un área de trabajo

Para este tutorial, usaremos OIDCAndroidLib desde GitHub, una biblioteca de OAuth2 basada en el código OpenID Connect de Google. Implementa el perfil de la aplicación nativa y admite el punto de conexión de autorización del usuario final. Esto es todo lo que vamos a necesitar para integrarlo con la plataforma de Microsoft Identity.

*  Clon

Comience con la clonación del repositorio OIDCAndroidLib en su equipo.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

### Configuración del entorno de Android Studio

*  Cree el proyecto de AndroidStudio y siga el asistente predeterminado.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)

*  Configurar los módulos del proyecto. Creo que la forma más sencilla de establecer los módulos es mover el repositorio clonado a la ubicación del proyecto. También se puede empezar con la creación del proyecto y después clonarlo directamente en la ubicación del proyecto.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)

Después, abra la configuración de los módulos del proyecto en el menú contextual o mediante el acceso directo `Ctrl + Alt + Maj + S`.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)

Quite el módulo de aplicación predeterminado ya que solo se quiere la configuración del contenedor del proyecto.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)

Ahora hay que importar los módulos del repositorio clonado al proyecto actual.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)

> Repita estos pasos para el módulo `oidlib-sample`.

Compruebe las dependencias de oidclib en el módulo `oidlib-sample`.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)

Haga clic en "Aceptar" y espere la sincronización de gradle.

El archivo settings.gradle debe ser similar a este:

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)

*  Compile la aplicación de ejemplo para asegurarse de que el ejemplo se ejecuta correctamente.

Todavía no podrá usarlo con Azure Active Directory. Deberá configurar primero algunos puntos de conexión. Esto es para asegurarse de que no tiene problemas con Android Studio antes de empezar a personalizar la aplicación de ejemplo.

Compile y ejecute `oidlib-sample` como destino en Android Studio.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)

*  Limpieza

Puede eliminar con seguridad el directorio `app ` que quedó al quitar el módulo del proyecto ya que AndroidStudio no lo elimina por seguridad.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)

También puede quitar la configuración de ejecución que quedó al quitar el módulo del proyecto en el menú "Editar configuraciones".

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG) ![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## Configuración de los puntos de conexión del ejemplo

Ahora que `oidlib-sample` se ejecuta correctamente, vamos a modificar algunos puntos de conexión para que funcionen con Azure Active Directory.

* Configuración del cliente

Abra el archivo `oidc_clientconf.xml` y realice los siguientes cambios:

1. Como solo estamos usando flujos de OAuth2 para obtener un token y llamar a la API Graph, vamos a configurar el cliente para que solo haga OAuth2. El uso de OIDC se tratará en un ejemplo posterior.

```xml
    <bool name="oidc_oauth2only">true</bool>
```

2. Configure el identificador de cliente que ha recibido en el portal de registro.

```xml
    <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
    <string name="oidc_clientSecret"></string>
```

3. Configure el identificador URI que ha recibido en el portal de registro.

```xml
    <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
```

4. Configure los ámbitos que necesita para acceder a la API Graph.

```xml
    <string-array name="oidc_scopes">
        <item>openid</item>
        <item>User.ReadBasic.All</item>
        <item>offline_access</item>
    </string-array>
```

Aquí establecemos `oidc_scopes`. El ámbito que vamos a solicitar para esta aplicación es `User.ReadBasic.All`, que nos permite leer el perfil básico de todos los usuarios de nuestro directorio. Puede obtener más información sobre todos los ámbitos que están disponibles para [su uso con Microsoft Graph aquí](https://graph.microsoft.io/docs/authorization/permission_scopes).

Si desea explicaciones sobre `openid` o `offline_access` como ámbitos de OpenID Connect, consulte una breve [información general sobre el protocolo que hemos documentado aquí](active-directory-v2-protocols-oauth-code.md).

* Configuración de los puntos de conexión de cliente

Abra el archivo `oidc_endpoints.xml` y realice los siguientes cambios:

```xml
<!-- Stores OpenID Connect provider endpoints. -->
<resources>
    <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
    <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
    <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
    <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
</resources>
```

Nunca deben cambiar estos puntos de conexión si usa OAuth2 como protocolo.

> [AZURE.NOTE] 
Los puntos de conexión para `userInfoEndpoint` y `revocationEndpoint` no los admite de momento Azure Active Directory, por lo que los dejaremos con sus valores predeterminados de example.com, que proporcionarán un útil recordatorio de que no están disponibles en el ejemplo.


## Configuración de una llamada de API Graph

Abra el archivo `HomeActivity.java` y realice los siguientes cambios:

```Java
   //TODO: set your protected resource url
    private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
```

Aquí hacemos una llamada sencilla de API Graph que devolverá nuestra información.

## ¡Y ya está!

Estos son todos los cambios que necesita hacer. Ejecute la aplicación `oidlib-sample` y haga clic para iniciar sesión. O

Cuando se haya autenticado correctamente, presione el botón "Request Protected Resource" (Solicitar recurso protegido) para probar la llamada a la API Graph.

## Obtención de actualizaciones de seguridad para nuestro producto

Le animamos a que obtenga notificaciones de los incidentes de seguridad que se produzcan; para ello, visite [esta página](https://technet.microsoft.com/security/dd252948) y suscríbase a las alertas de avisos de seguridad.

<!---HONumber=AcomDC_0629_2016-->