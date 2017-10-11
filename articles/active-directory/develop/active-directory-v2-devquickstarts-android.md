---
title: "Aplicación para Android de la versión 2.0 de Azure Active Directory | Microsoft Docs"
description: "Procedimiento para compilar una aplicación Android con la que los usuarios pueden iniciar sesión utilizando tanto su cuenta personal de Microsoft y sus cuentas profesionales o educativas, además de realizar llamadas a la API Graph mediante bibliotecas de terceros."
services: active-directory
documentationcenter: 
author: danieldobalian
manager: mbaldwin
editor: 
ms.assetid: 16294c07-f27d-45c9-833f-7dbb12083794
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: c0a5a818c61f7af7ff04bf890b54e8364f3b21b1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="add-sign-in-to-an-android-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Adición de inicio de sesión a una aplicación Android mediante una biblioteca de terceros con la API Graph mediante la versión 2.0 del punto de conexión
La plataforma Microsoft Identity utiliza estándares abiertos como OAuth2 y OpenID Connect. Los desarrolladores pueden usar la biblioteca que quieran integrar con nuestros servicios. Para ayudar a los desarrolladores a utilizar nuestra plataforma con otras bibliotecas, hemos escrito algunos tutoriales como este para demostrar cómo configurar bibliotecas de terceros con el objetivo de conectarse a la plataforma Microsoft Identity. La mayoría de las bibliotecas que implementan [la especificación OAuth2 RFC6749](https://tools.ietf.org/html/rfc6749) pueden conectarse a la plataforma Microsoft Identity.

Con la aplicación que se crea en este tutorial, los usuarios podrán iniciar sesión en su organización y, después, buscarse en la organización mediante la API Graph.

Si no está familiarizado con OAuth2 o con OpenID Connect, es posible que gran parte de esta configuración de ejemplo no le sea relevante. Si este es el caso, es aconsejable que lea [Protocolos de la versión 2.0: Flujo de código de autorización de OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

> [!NOTE]
> Algunas características de nuestra plataforma mencionadas en los estándares OAuth2 u OpenID Connect, como el acceso condicional y la administración de directivas de Intune, deben usar nuestras bibliotecas de código abierto de Microsoft Azure Identity.
> 
> 

No todas las características y escenarios de Azure Active Directory son compatibles con la versión 2.0 del punto de conexión.

> [!NOTE]
> Para determinar si debe utilizar la versión 2.0 del punto de conexión, obtenga información sobre las [limitaciones de esta versión](active-directory-v2-limitations.md).
> 
> 

## <a name="download-the-code-from-github"></a>Descargue el código desde GitHub.
El código de este tutorial se conserva [en GitHub](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2).  Para continuar, puede [descargar el esqueleto de la aplicación como un archivo .zip](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2/archive/skeleton.zip) o clonar el esqueleto:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

También puede descargar el ejemplo y comenzar a trabajar inmediatamente:

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## <a name="register-an-app"></a>Registrar una aplicación
Cree una nueva aplicación en el [Portal de registro de aplicaciones](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) o siga los pasos que se detallan en [Cómo registrar una aplicación con el punto de conexión v2.0](active-directory-v2-app-registration.md).  Asegúrese de que:

* Copie el **id. de aplicación** asignado a su aplicación, ya que lo necesitará pronto.
* Agregar la plataforma **Móvil** a la aplicación.

> Nota: El portal de registro de la aplicación proporciona un valor de **URI de redirección** . Sin embargo, en este ejemplo debe utilizar el valor predeterminado de `https://login.microsoftonline.com/common/oauth2/nativeclient`.
> 
> 

## <a name="download-the-nxoauth2-third-party-library-and-create-a-workspace"></a>Descarga de la biblioteca de terceros NXOAuth2 y creación de un área de trabajo
Para este tutorial, usaremos OIDCAndroidLib desde GitHub, una biblioteca de OAuth2 basada en el código OpenID Connect de Google. Implementa el perfil de la aplicación nativa y admite el punto de conexión de autorización del usuario. Esto es todo lo que vamos a necesitar para integrarlo con la plataforma de Microsoft Identity.

Clone el repositorio de OIDCAndroidLib en el equipo.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](../media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

## <a name="set-up-your-android-studio-environment"></a>Configuración del entorno de Android Studio
1. Cree un nuevo proyecto de Android Studio y acepte los valores predeterminados en el asistente.
   
    ![Creación de un nuevo proyecto de Android Studio](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)
   
    ![Dispositivos Android de destino](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)
   
    ![Adición de una actividad a móviles](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)
2. Para configurar los módulos del proyecto, mueva el repositorio clonado a la ubicación del proyecto. También crear el proyecto y, después, clonarlo directamente en la ubicación del proyecto.
   
    ![Módulos del proyecto](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)
3. Abra la configuración de los módulos del proyecto mediante el menú contextual o utilizando la combinación de teclas Ctrl+Alt+Mayús+S.
   
    ![Configuración de los módulos del proyecto](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)
4. Quite el módulo de aplicación predeterminado, ya que solo se quiere la configuración del contenedor del proyecto.
   
    ![El módulo de aplicación predeterminado](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)
5. Ahora hay que importar los módulos del repositorio clonado al proyecto actual.
   
    ![Proyecto de importación gradle](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![crear una nueva página de módulo](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)
6. Repita estos pasos para el módulo `oidlib-sample` .
7. Compruebe las dependencias de oidclib en el módulo `oidlib-sample` .
   
    ![Dependencias de oidclib en el módulo oidlib-sample](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)
8. Haga clic en **Aceptar** y espere a que se inicie sincronización de gradle.
   
    El archivo settings.gradle debe ser similar a este:
   
    ![Captura de pantalla de settings.gradle](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)
9. Compile la aplicación de ejemplo para asegurarse de que el ejemplo se ejecuta correctamente.
   
    Todavía no podrá usarlo con Azure Active Directory. Deberá configurar primero algunos puntos de conexión. Esto es para asegurarse de que no tiene problemas con Android Studio antes de empezar a personalizar la aplicación de ejemplo.
10. Compile y ejecute `oidlib-sample` como destino en Android Studio.
    
    ![Progreso de la compilación oidlib-sample](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)
11. Elimine el directorio `app ` que quedó al quitar el módulo del proyecto, ya que Android Studio no lo elimina por seguridad.
    
    ![Estructura del archivo que incluye el directorio de aplicación](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)
12. Abra el menú **Editar configuración** para quitar la configuración de ejecución que quedó al eliminar el módulo del proyecto.
    
    ![Menú Editar configuración](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG)
    ![Ejecutar configuración de aplicación](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## <a name="configure-the-endpoints-of-the-sample"></a>Configuración de los puntos de conexión del ejemplo
Ahora que `oidlib-sample` se ejecuta correctamente, vamos a modificar algunos puntos de conexión para que funcionen con Azure Active Directory.

### <a name="configure-your-client-by-editing-the-oidcclientconfxml-file"></a>Configuración del cliente modificando el archivo oidc_clientconf.xml
1. Como estamos usando flujos de OAuth2 para obtener un token y llamar a la API Graph, vamos a configurar el cliente para que solo utilice OAuth2. El uso de OIDC se tratará en un ejemplo posterior.
   
    ```xml
        <bool name="oidc_oauth2only">true</bool>
    ```
2. Configure el id. de cliente que ha recibido en el portal de registro.
   
    ```xml
        <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
        <string name="oidc_clientSecret"></string>
    ```
3. Configure el URI de redireccionamiento con el siguiente código.
   
    ```xml
        <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
    ```
4. Configure los ámbitos que necesita para acceder a la API Graph.
   
    ```xml
        <string-array name="oidc_scopes">
            <item>openid</item>
            <item>https://graph.microsoft.com/User.Read</item>
            <item>offline_access</item>
        </string-array>
    ```

El valor `User.Read` en `oidc_scopes` permite leer el perfil básico del usuario que ha iniciado sesión.
Puede obtener más información sobre todos los ámbitos disponibles en [Microsoft Graph permission scopes](https://graph.microsoft.io/docs/authorization/permission_scopes)(Ámbitos de los permisos de Microsoft Graph).

Si quiere ver explicaciones sobre `openid` o `offline_access` como los ámbitos de OpenID Connect, consulte [Protocolos de la versión 2.0: Flujo de código de autorización de OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

### <a name="configure-your-client-endpoints-by-editing-the-oidcendpointsxml-file"></a>Configuración de los puntos de conexión de cliente modificando el archivo oidc_endpoints.xml
* Abra el archivo `oidc_endpoints.xml` y realice los siguientes cambios:
  
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

> [!NOTE]
> Los puntos de conexión de `userInfoEndpoint` y `revocationEndpoint` no son compatibles actualmente con Azure Active Directory. Si deja en ellos el valor predeterminado example.com, se le recordará que no estarán disponibles en el ejemplo.
> 
> 

## <a name="configure-a-graph-api-call"></a>Configuración de una llamada de API Graph
* Abra el archivo `HomeActivity.java` y realice los siguientes cambios:
  
    ```Java
       //TODO: set your protected resource url
        private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
    ```

Aquí se muestra una llamada sencilla a la API Graph que devuelve nuestra información.

Estos son todos los cambios que tiene que realizar. Ejecute aplicación `oidlib-sample` y haga clic en **Iniciar sesión**.

Cuando se haya autenticado correctamente, haga clic en el botón **Request Protected Resource** (Solicitar recurso protegido) para probar la llamada a la API Graph.

## <a name="get-security-updates-for-our-product"></a>Obtención de actualizaciones de seguridad para nuestro producto
Le animamos a que obtenga notificaciones de los incidentes de seguridad que se produzcan; para ello, visite la página [TechCenter de seguridad](https://technet.microsoft.com/security/dd252948) y suscríbase a las alertas de documentos informativos de seguridad.

