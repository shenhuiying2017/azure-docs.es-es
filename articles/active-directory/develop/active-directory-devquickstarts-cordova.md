---
title: "Introducción a Azure AD Cordova | Microsoft Docs"
description: "Creación de una aplicación Cordova que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth."
services: active-directory
documentationcenter: 
author: vibronet
manager: mtillman
editor: 
ms.assetid: b1a8d7bd-7ad6-44d5-8ccb-5255bb623345
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: vittorib
ms.custom: aaddev
ms.openlocfilehash: b489add83a462d1d3902831d63be0b70e2443718
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="azure-ad-cordova-getting-started"></a>Introducción a Azure AD Cordova
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Puede usar Apache Cordova para desarrollar aplicaciones HTML5/JavaScript que se ejecuten en dispositivos móviles como aplicaciones nativas completas. Con Azure Active Directory (Azure AD), puede agregar funcionalidades de autenticación de nivel empresarial a sus aplicaciones Cordova.

Un complemento Cordova ajusta los SDK nativos de Azure AD en iOS, Android, la Tienda Windows y Windows Phone. Con este complemento, puede mejorar la aplicación para admitir el inicio de sesión con las cuentas de Windows Server Active Directory de los usuarios, acceder a las API de Azure y Office 365 e incluso ayudar a proteger las llamadas a su propia API de web personalizada.

En este tutorial, usaremos el complemento de Apache Cordova para la biblioteca de autenticación de Active Directory (ADAL) con el fin de mejorar una aplicación sencilla al añadir las siguientes características:

* Con solo unas líneas de código, autenticar a un usuario y obtener un token.
* Utilizar ese token para invocar la API Graph, consultar ese directorio y mostrar los resultados.  
* Aprovechar la caché de tokens de la ADAL para reducir al máximo las peticiones de autenticación al usuario.

Para realizar dichas mejoras, necesitará:

1. Registrar una aplicación con Azure AD.
2. Agregar código a la aplicación para solicitar tokens.
3. Agregue código para usar el token para hacer consultas a la API Graph y mostrar los resultados.
4. Crear el proyecto de implementación de Cordova con todas las plataformas a las que desee dirigirse, agregar el complemento de la ADAL de Cordova y probar la solución en emuladores.

## <a name="prerequisites"></a>requisitos previos
Para completar este tutorial, necesita:

* Un inquilino de Azure AD que disponga de una cuenta con derechos de desarrollo de aplicaciones.
* Un entorno de desarrollo configurado para usar Apache Cordova.  

Si ya los tiene configurados, vaya directamente al paso 1.

Si no tiene un inquilino de Azure AD, siga [instrucciones sobre cómo conseguir uno aquí](active-directory-howto-tenant.md).

Si no dispone de Apache Cordova configurado en su máquina, instale lo siguiente:

* [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Node.js](https://nodejs.org/download/)
* [Interfaz de línea de comandos Cordova](https://cordova.apache.org/) (se instala fácilmente mediante el administrador de paquetes NPM: `npm install -g cordova`)

Lo que se ha instalado antes debería funcionar tanto en PC como en Mac.

Cada plataforma de destino tiene diferentes requisitos previos:

* Para compilar y ejecutar una aplicación para tabletas/PC Windows o Windows Phone:
  * Instale [Visual Studio 2013 para Windows con la actualización 2 o posterior](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express u otra versión) o [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs#d-community).

* Para compilar y ejecutar una aplicación para iOS:

  * Instale Xcode 6.x o una versión posterior. Descárguelo del [sitio para desarrolladores de Apple](http://developer.apple.com/downloads) o la [tienda de aplicaciones para Mac](http://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  * Instale [ios-sim](https://www.npmjs.org/package/ios-sim). Puede usarlo para iniciar aplicaciones de iOS en el simulador de iOS desde la línea de comandos. (Se puede instalar fácilmente a través del terminal: `npm install -g ios-sim`).
* Para compilar y ejecutar una aplicación para Android:

  * Instale el [Kit de desarrollo de Java (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o una versión posterior. Asegúrese de que `JAVA_HOME` (variable de entorno) se haya configurado correctamente según la ruta de instalación de JDK (por ejemplo, C:\Archivos de programa\Java\jdk1.7.0_75).
  * Instale el [SDK de Android](http://developer.android.com/sdk/installing/index.html?pkg=tools) y agregue la ubicación `<android-sdk-location>\tools` (por ejemplo, C:\tools\Android\android-sdk\tools) a su variable de entorno `PATH`.
  * Abra Android SDK Manager (por ejemplo, a través del terminal: `android`) e instale:
    * *Android 5.0.1 (API 21)*
    * *Herramientas de compilación de SDK de Android*, versión 19.1.0 o posterior
    * *Repositorio compatible con Android* (extras)

  El SDK de Android no proporciona ninguna instancia de emulador predeterminada. Cree una nueva ejecutando `android avd` desde el terminal y seleccione **Crear** si desea ejecutar la aplicación Android en el emulador. Se recomienda un nivel de API de 19 o superior. Consulte [AVD Manager](http://developer.android.com/tools/help/avd-manager.html) para más información acerca de las opciones de creación y el emulador de Android.

## <a name="step-1-register-an-application-with-azure-ad"></a>Paso 1: Registro de una aplicación con Azure AD
Este paso es opcional. Este tutorial proporciona valores configurados previamente que puede usar para ver el ejemplo en acción sin tener que realizar aprovisionamientos en su propio inquilino. Sin embargo, se recomienda que realice este paso y se familiarice con el proceso, ya que será necesario si va a crear sus propias aplicaciones.

Azure AD emite tokens solo a aplicaciones conocidas. Para poder usar Azure AD desde su aplicación, deberá crear una entrada para él en el inquilino. Para registrar una nueva aplicación en el inquilino:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En la barra superior, haga clic en su cuenta. En la lista **Directorio**, elija el inquilino de Azure AD donde desea registrar la aplicación.
3. Haga clic en **Más servicios** en el panel izquierdo y seleccione **Azure Active Directory**.
4. Haga clic en **Registros de aplicaciones** y luego seleccione **Agregar**.
5. Siga las indicaciones y cree una **Aplicación de cliente nativo**. (Aunque las aplicaciones Cordova están basadas en HTML, aquí estamos creando una aplicación cliente nativa. La opción **Aplicación cliente nativo** debe estar seleccionada o la aplicación no funcionará).
  * **Nombre** describe la aplicación a los usuarios.
  * **URI de redirección** es el identificador URI que se usa para devolver tokens a su aplicación. Escriba **http://MyDirectorySearcherApp**.

Cuando termine el registro, Azure AD asignará un identificador de aplicación único a la aplicación. Necesitará este valor en las secciones siguientes. Puede encontrarlo en la pestaña de la aplicación recién creada.

Para poder ejecutar `DirSearchClient Sample`, conceda a la aplicación recién creada el permiso para consultar la API Graph de Azure AD:

1. En la página **Configuración**, seleccione **Permisos necesarios** y **Agregar**.  
2. Para la aplicación Azure Active Directory, seleccione **Microsoft Graph** como API y agregue el permiso **Access the directory as the signed-in user** (Acceder al directorio como usuario con sesión iniciada) en **Permisos delegados**.  Esto permitirá a su aplicación consultar la API Graph para los usuarios.

## <a name="step-2-clone-the-sample-app-repository"></a>Paso 2: Clonación del repositorio de aplicaciones de ejemplo
En el shell o en la línea de comandos, escriba lo siguiente:

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="step-3-create-the-cordova-app"></a>Paso 3: Creación de la aplicación Cordova
Hay varias maneras de crear aplicaciones de Cordova. En este tutorial, utilizaremos la interfaz de la línea de comandos (CLI) de Cordova.

1. En el shell o en la línea de comandos, escriba lo siguiente:

        cordova create DirSearchClient

   Este comando creará la estructura de carpetas y el scaffolding para el proyecto Cordova.

2. Acceda a la nueva carpeta de DirSearchClient:

        cd .\DirSearchClient

3. Copie el contenido del proyecto de inicio en la subcarpeta www con un administrador de archivos o el comando siguiente en el shell:

  * Windows: `xcopy ..\NativeClient-MultiTarget-Cordova\DirSearchClient www /E /Y`
  * Mac: `cp -r  ../NativeClient-MultiTarget-Cordova/DirSearchClient/* www`

4. Agregue el complemento de lista blanca. Esto es necesario para invocar la API Graph.

        cordova plugin add cordova-plugin-whitelist

5. Agregue todas las plataformas que desee admitir. Para obtener un ejemplo funcional, deberá ejecutar al menos uno de los siguientes comandos. Tenga en cuenta que no podrá emular iOS en Windows ni Windows en Mac.

        cordova platform add android
        cordova platform add ios
        cordova platform add windows

6. Agregue el complemento de la ADAL para Cordova al proyecto:

        cordova plugin add cordova-plugin-ms-adal

## <a name="step-4-add-code-to-authenticate-users-and-obtain-tokens-from-azure-ad"></a>Paso 4: Incorporación de código para autenticar a los usuarios y obtener tokens de Azure AD
La aplicación que va a desarrollar en este tutorial proporciona una característica de búsqueda de directorio sencilla. El usuario puede escribir el alias de cualquier usuario del directorio y visualizar algunos atributos básicos. El proyecto de inicio contiene la definición de la interfaz de usuario básica de la aplicación (en www/index.html) y el scaffolding que conecta los ciclos de eventos de la aplicación básica, los enlaces de la interfaz de usuario y la lógica de visualización de los resultados (en www/js/index.js). Lo único que le queda es agregar la lógica que implementa las tareas de identidad.

Lo primero que hay que hacer en el código es introducir los valores de protocolo que usa Azure AD para identificar la aplicación y los recursos a los que se dirige. Estos valores se utilizarán para construir las solicitudes de tokens más adelante. Inserte el fragmento de código siguiente en la parte superior del archivo index.js:

```javascript
var authority = "https://login.microsoftonline.com/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

Los valores `redirectUri` y `clientId` deben coincidir con los valores que describen la aplicación en Azure AD. Puede encontrarlos en la pestaña **Configurar** de Azure Portal, tal como se describe en el paso 1 de este tutorial.

> [!NOTE]
> Si ha optado por no registrar una nueva aplicación en su propio inquilino, puede simplemente pegar los valores preconfigurados tal cual. A continuación, verá el ejemplo en ejecución, aunque siempre debe crear su propia entrada para las aplicaciones diseñadas para entornos de producción.

A continuación, agregue el código de solicitud del token. Inserte el siguiente fragmento de código entre las definiciones `search` y `renderData`:

```javascript
    // Shows the user authentication dialog box if required
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Examinemos esa función dividiéndola en dos partes principales.
Este ejemplo está diseñado para que funcione con cualquier inquilino, en lugar de vincularse a uno concreto. Se utiliza el punto de conexión "/common", que permite al usuario especificar cualquier cuenta en el momento de la autenticación y dirige la solicitud al inquilino al que pertenece.

Esta primera parte del método inspecciona la memoria caché de la ADAL para ver si ya hay un token almacenado. Si es así, el método usa los inquilinos de dónde procede el token para reinicializar la ADAL. Esto es necesario para evitar mensajes adicionales, ya que el uso de "/common" siempre hace que se le pida al usuario que especifique una nueva cuenta.

```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
La segunda parte del método realiza la solicitud de token correcta. El método `acquireTokenSilentAsync` solicita a la ADAL que devuelva un token para el recurso especificado sin mostrar ninguna experiencia de usuario. Esto puede ocurrir si la memoria caché ya tiene un token de acceso adecuado almacenado o si hay un token de actualización que puede utilizarse para obtener un nuevo token de acceso sin mostrar ningún símbolo del sistema. Si este intento falla, se recurre a `acquireTokenAsync`, que solicitará visiblemente al usuario que se autentique.

```javascript
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Ahora que tenemos el token, podemos invocar finalmente la API Graph y realizar la consulta de búsqueda que queremos. Inserte el fragmento de código siguiente justo debajo de la definición `authenticate`:

```javascript
// Makes an API call to receive the user list
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Los archivos de punto de partida proporcionaron una experiencia de usuario sencilla para especificar el alias del usuario en un cuadro de texto. Este método utiliza ese valor para construir una consulta, combinarla con el token de acceso, enviarlo a Microsoft Graph y analizar los resultados. El método `renderData`, que ya está presente en el archivo de punto de partida, se encarga de visualizar los resultados.

## <a name="step-5-run-the-app"></a>Paso 5: Ejecución de la aplicación
La aplicación está finalmente lista para ejecutarse. El funcionamiento es sencillo: cuando se inicie la aplicación, escriba el alias del usuario que desea buscar y haga clic en el botón. Se le pedirá que se autentique. Tras autenticarse y realizar la búsqueda correctamente, se mostrarán los atributos del usuario buscado.

Las ejecuciones posteriores llevarán a cabo la búsqueda sin mostrar ningún símbolo del sistema, gracias a la presencia en la memoria caché del token obtenido anteriormente.

Los pasos concretos para ejecutar la aplicación varían según la plataforma.

### <a name="windows-10"></a>Windows 10
   Tableta/PC: `cordova run windows --archs=x64 -- --appx=uap`

   Dispositivo móvil (requiere el dispositivo Windows 10 Mobile conectado a PC): `cordova run windows --archs=arm -- --appx=uap --phone`

   > [!NOTE]
   > Durante la primera ejecución, se le pedirá que inicie sesión para obtener una licencia de desarrollador. Para más información, consulte el artículo sobre la [licencia de desarrollador](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-81-tabletpc"></a>Tableta/PC con Windows 8.1
   `cordova run windows`

   > [!NOTE]
   > Durante la primera ejecución, se le pedirá que inicie sesión para obtener una licencia de desarrollador. Para más información, consulte el artículo sobre la [licencia de desarrollador](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-phone-81"></a>Windows Phone 8,1
   Para realizar la ejecución en un dispositivo conectado: `cordova run windows --device -- --phone`

   Para realizar la ejecución en el emulador predeterminado: `cordova emulate windows -- --phone`

   Use `cordova run windows --list -- --phone` para ver todos los destinos disponibles y `cordova run windows --target=<target_name> -- --phone` para ejecutar la aplicación en el emulador o un dispositivo específico (por ejemplo, `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

### <a name="android"></a>Android
   Para realizar la ejecución en un dispositivo conectado: `cordova run android --device`

   Para realizar la ejecución en el emulador predeterminado: `cordova emulate android`

   Asegúrese de que ha creado la instancia del emulador con AVD Manager, tal y como se describió anteriormente en la sección Requisitos previos.

   Use `cordova run android --list` para ver todos los destinos disponibles y `cordova run android --target=<target_name>` para ejecutar la aplicación en el emulador o un dispositivo específico (por ejemplo, `cordova run android --target="Nexus4_emulator"`).

### <a name="ios"></a>iOS
   Para realizar la ejecución en un dispositivo conectado: `cordova run ios --device`

   Para realizar la ejecución en el emulador predeterminado: `cordova emulate ios`

   > [!NOTE]
   > Asegúrese de que el paquete `ios-sim` esté instalado para ejecutarse en el emulador. Para más información, consulte la sección Requisitos previos de esta sección.

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run the application on specific device or emulator (for example, `cordova run android --target="iPhone-6"`).

    Use `cordova run --help` to see additional build and run options.

## <a name="next-steps"></a>pasos siguientes
Como referencia, en [GitHub](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient) está disponible el ejemplo finalizado (sin sus valores de configuración).

Ahora puede pasar a escenarios más avanzados (y, por lo tanto, más interesantes). Quizá quiera intentar: [Proteger una API web de Node.js con Azure AD](active-directory-devquickstarts-webapi-nodejs.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
