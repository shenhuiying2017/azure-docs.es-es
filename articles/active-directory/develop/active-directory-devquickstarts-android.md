---
title: "Introducción a Azure AD Android | Microsoft Docs"
description: "Cómo crear una aplicación Android que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth2.0."
services: active-directory
documentationcenter: android
author: danieldobalian
manager: mtillman
editor: 
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/30/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: 619334b3ca65654fd845a62c2fc068156d94d6fc
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="azure-ad-android-getting-started"></a>Introducción a Azure AD Android
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Si está desarrollando una aplicación de escritorio, Azure Active Directory (Azure AD) le facilita la autenticación de los usuarios mediante sus cuentas de Active Directory locales. También permite a la aplicación consumir con seguridad cualquier API web protegida por Azure AD, como las API de Office 365 o la API de Azure.

Para los clientes de Android que necesitan tener acceso a recursos protegidos, Azure AD proporciona la Biblioteca de autenticación de Active Directory (ADAL). El único propósito de ADAL es facilitar a su aplicación la obtención de tokens de acceso. Para demostrar lo fácil que es, crearemos una aplicación Android de lista de tareas pendientes que permita realizar lo siguiente:

* Obtener tokens de acceso para llamar a la API de lista de tareas pendientes utilizando el [protocolo de autenticación OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Obtener listas de tareas pendientes de un usuario.
* Desconectar a los usuarios.

Para comenzar, necesitará a un inquilino de Azure AD en el que pueda crear usuarios y registrar una aplicación. Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>Paso 1: descargue y ejecute el servidor de ejemplos TODO de la API de REST de Node.js
Este servidor de ejemplos TODO de la API de REST de Node.js está escrito específicamente para que funcione con nuestro ejemplo existente para crear una API de REST To-Do de un solo inquilino para Azure AD. Se trata de un requisito previo para el Inicio rápido.

Para obtener información sobre cómo configurarlo, consulte los ejemplos existentes en [Servicio de API de REST de ejemplo de Microsoft Azure Active Directory para Node.js](active-directory-devquickstarts-webapi-nodejs.md).


## <a name="step-2-register-your-web-api-with-your-azure-ad-tenant"></a>Paso 2: registre la API web con el inquilino de Azure AD
Active Directory permite agregar dos tipos de aplicaciones:

- API web que ofrecen servicios a los usuarios
- Aplicaciones (que se ejecutan en la Web o en un dispositivo) que tienen acceso a esas API web

En este paso, va a registrar la API web que se ejecuta localmente para probar este ejemplo. Normalmente, esta API web es un servicio de REST que ofrece la funcionalidad a la que desea que acceda una aplicación. Azure AD puede ayudar a proteger cualquier punto de conexión.

Aquí suponemos que va a registrar la API de REST TODO mencionada anteriormente. No obstante, este procedimiento este procedimiento sirve para cualquier API web que desee que Azure Active Directory ayude a proteger.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En la barra superior, haga clic en su cuenta. En la lista **Directorio**, elija el inquilino de Azure AD donde desea registrar la aplicación.
3. Haga clic en **Más servicios** en el panel izquierdo y seleccione **Azure Active Directory**.
4. Haga clic en **Registros de aplicaciones** y seleccione **Agregar**.
5. Escriba un nombre descriptivo para la aplicación (por ejemplo, **TodoListService**), seleccione **Aplicación web y/o API web** y haga clic en **Siguiente**.
6. Para la URL de inicio de sesión, escriba la dirección URL base para el ejemplo. De manera predeterminada, será `https://localhost:8080`.
7. Haga clic en **Aceptar** para completar el registro.
8. Mientras sigue en Azure Portal, vaya a la página de la aplicación, busque el valor del id. de aplicación y cópielo. Lo necesitará más adelante cuando configure la aplicación.
9. En la página **Configuración** -> **Propiedades**, actualice la aplicación del URI del identificador de aplicación y escriba `https://<your_tenant_name>/TodoListService`. Reemplace `<your_tenant_name>` por el nombre de su inquilino de Azure AD.

## <a name="step-3-register-the-sample-android-native-client-application"></a>Paso 3: registre la aplicación de cliente nativo de Android de ejemplo
Debe registrar la aplicación web en este ejemplo. Esto permite a su aplicación comunicarse con la recién registrada API web. Azure AD rechazará incluso que la aplicación pueda solicitar el inicio de sesión, a menos que esté registrada. Eso forma parte de la seguridad del modelo.

Aquí suponemos que va a registrar la aplicación de ejemplo mencionada anteriormente. No obstante, este procedimiento sirve para cualquier aplicación que esté desarrollando.

> [!NOTE]
> Es posible que se pregunte por qué poner una aplicación y una API web en un mismo inquilino. Como posiblemente haya intuido, puede crear una aplicación con acceso a una API externa registrada en Azure AD desde otro inquilino. Si lo hace, se pedirá a los clientes que acepten el uso de la API en la aplicación. La Biblioteca de autenticación de Active Directory para iOS se encarga de este consentimiento por usted. A medida que exploremos características más avanzadas, verá que se trata de una parte importante del trabajo necesario para tener acceso al conjunto de API de Microsoft desde Azure y Office, así como cualquier otro proveedor de servicios. Por ahora, como ha registrado su API web y la aplicación en el mismo inquilino, no verá ninguna petición de consentimiento. Este suele ser el caso si va a desarrollar una aplicación solo para que la use su propia empresa.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En la barra superior, haga clic en su cuenta. En la lista **Directorio**, elija el inquilino de Azure AD donde desea registrar la aplicación.
3. Haga clic en **Más servicios** en el panel izquierdo y seleccione **Azure Active Directory**.
4. Haga clic en **Registros de aplicaciones** y luego seleccione **Agregar**.
5. Escriba un nombre descriptivo para la aplicación (por ejemplo **TodoListClient-Android**), seleccione **Aplicación de cliente nativo** y haga clic en **Siguiente**.
6. Para el URI de redirección, escriba `http://TodoListClient`. Haga clic en **Finalizar**
7. En la página de la aplicación, busque el valor de identificador de la aplicación y cópielo. Lo necesitará más adelante cuando configure la aplicación.
8. En la página **Configuración**, seleccione **Permisos necesarios** y, a continuación, **Agregar**.  Busque y seleccione TodoListService y agregue el permiso **Access TodoListService** en **Permisos delegados**; luego, haga clic en **Listo**.

Para compilar con Maven, puede utilizar pom.xml en el nivel superior:

1. Clone este repositorio en el directorio que desee:

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`  
2. Siga los pasos descritos en los [requisitos previos para configurar el entorno Maven para Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
3. Configure el emulador con SDK 19.
4. Vaya a la carpeta raíz donde ha clonado el repositorio.
5. Ejecute este comando: `mvn clean install`
6. Cambie el directorio al ejemplo de inicio rápido: `cd samples\hello`
7. Ejecute este comando: `mvn android:deploy android:run`

   La aplicación debería iniciarse.
8. Escriba las credenciales del usuario de prueba para probarlas.

Los paquetes JAR se enviarán junto con el paquete AAR.

## <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>Paso 4: descargue ADAL de Android y agréguelo al área de trabajo de Eclipse
Ahora tiene varias opciones para usar ADAL en el proyecto Android:

* Puede usar el código fuente para importar esta biblioteca en Eclipse y vincularla a la aplicación.
* Si utiliza Android Studio, puede usar el formato de paquete AAR y hacer referencia a los archivos binarios.

### <a name="option-1-source-zip"></a>Opción 1: origen a través de ZIP
Para descargar una copia del código fuente, haga clic en **Descargar ZIP** en el lado derecho de la página. También puede [descargarla desde GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

### <a name="option-2-source-via-git"></a>Opción 2: origen a través de Git
Para obtener el código fuente del SDK mediante Git, escriba:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

### <a name="option-3-binaries-via-gradle"></a>Opción 3: binarios a través de Gradle
Puede obtener los archivos binarios desde el repositorio central de Maven. El paquete AAR puede incluirse de la siguiente forma en el proyecto en Android Studio:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

### <a name="option-4-aar-via-maven"></a>Opción 4: AAR a través de Maven
Si utiliza el complemento M2Eclipse, puede especificar la dependencia en el archivo pom.xml:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


### <a name="option-5-jar-package-inside-the-libs-folder"></a>Opción 5: paquete JAR dentro de la carpeta libs
Puede obtener el archivo JAR desde el repositorio de Maven y colocarlo en la carpeta **libs** del proyecto. También deberá copiar los recursos necesarios en el proyecto, ya que los paquetes JAR no los incluyen.

## <a name="step-5-add-references-to-android-adal-to-your-project"></a>Paso 5: agregue referencias a ADAL de Android en el proyecto
1. Agregue una referencia al proyecto y especifíquela como una biblioteca de Android. Si no está seguro de cómo hacerlo, puede obtener más información en el [sitio de Android Studio](http://developer.android.com/tools/projects/projects-eclipse.html).
2. Agregue la dependencia de proyecto para la depuración en la configuración del proyecto.
3. Actualice el archivo del proyecto AndroidManifest.xml para incluir:

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
            ....
        <application/>

4. Cree una instancia de AuthenticationContext en la actividad principal. Los detalles de esta llamada quedan fuera del ámbito de este tema, pero puede empezar a trabajar examinando el [ejemplo del cliente nativo de Android](https://github.com/AzureADSamples/NativeClient-Android). En el ejemplo siguiente, SharedPreferences es la memoria caché predeterminada y Authority se presenta con el formato de `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`:

    `mContext = new AuthenticationContext(MainActivity.this, authority, true); // mContext is a field in your activity`

5. Copie este bloque de código para controlar el final de AuthenticationActivity después de que el usuario escriba las credenciales y reciba el código de autorización:

        @Override
         protected void onActivityResult(int requestCode, int resultCode, Intent data) {
             super.onActivityResult(requestCode, resultCode, data);
             if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
             }
         }

6. Para solicitar un token, defina una devolución de llamada:

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };

7. Por último, solicite un token mediante esa devolución de llamada:

    `mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                   callback);`

A continuación, se proporciona una explicación de los parámetros:

* Hay que especificar *resource*, que es el recurso al que está intentando acceder.
* *clientid* es necesario. Su valor procede de Azure AD.
* No es necesario que se proporcione *RedirectUri* para la llamada a acquireToken. Se puede configurar como el nombre del paquete.
* *PromptBehavior* ayuda a pedir las credenciales para omitir la memoria caché y la cookie.
* Se llamará a *callback* una vez que se intercambie el código de autorización para un token. La devolución de llamada tiene un objeto de AuthenticationResult que tiene acceso al token, fecha de caducidad e información sobre el token del identificador.
* *acquireTokenSilent* es opcional. Puede llamarlo para controlar el almacenamiento en caché y la actualización del token. También proporciona la versión de sincronización. Acepta *userId* como parámetro.

        mContext.acquireTokenSilent(resource, clientid, userId, callback );

Con este procedimiento, debe contar con todo lo necesario para lograr una integración correcta con Azure Active Directory. Para obtener más ejemplos de este trabajo, visite el repositorio AzureADSamples/ en GitHub.

## <a name="important-information"></a>Información importante
### <a name="customization"></a>Personalización
Los recursos de la aplicación pueden sobrescribir a los recursos del proyecto de la biblioteca. Esto sucede cuando la aplicación se está generando. Por este motivo, puede personalizar el diseño de la actividad de autenticación del modo que desee. Asegúrese de conservar el identificador de los controles que use ADAL (WebView).

### <a name="broker"></a>Agente
La aplicación de portal de empresa de Microsoft Intune proporciona el componente del agente. La cuenta se crea en el administrador de cuentas. El tipo de cuenta es "com.microsoft.workaccount". El administrador de cuentas únicamente permite una sola cuenta SSO. Después de completar el desafío del dispositivo para una de las aplicaciones, creará una cookie de SSO para el usuario.

ADAL usará la cuenta del agente si en este autenticador hay creada una cuenta de usuario y decide no omitirla. Puede omitir el usuario del agente mediante:

   `AuthenticationSettings.Instance.setSkipBroker(true);`

Debe registrar un RedirectUri especial para el uso del agente. RedirectUri tiene el formato `msauth://packagename/Base64UrlencodedSignature`. Puede obtener un RedirectUri para su aplicación mediante el script brokerRedirectPrint.ps1. También puede usar la llamada de API mContext.getBrokerRedirectUri. La firma está relacionada con los certificados de firma.

El modelo de agente actual es para un usuario. AuthenticationContext proporciona un método de API para obtener el usuario del agente.

   `String brokerAccount =  mContext.getBrokerUser(); //Broker user is returned if account is valid.`

El manifiesto de aplicación debe tener los siguientes permisos para usar cuentas del administrador de cuentas. Para más información, consulte la [información del administrador de cuentas en el sitio de Android](http://developer.android.com/reference/android/accounts/AccountManager.html).

* GET_ACCOUNTS
* USE_CREDENTIALS
* MANAGE_ACCOUNTS

### <a name="authority-url-and-ad-fs"></a>AD FS y URL de la autoridad
Active Directory Federation Services (AD FS) no se reconoce como STS de producción, por lo que debe desactivar el descubrimiento de la instancia y pasar el valor "false" al constructor de AuthenticationContext.

La dirección URL de la autoridad necesita la instancia de STS y un [nombre de inquilino](https://login.microsoftonline.com/yourtenant.onmicrosoft.com).

### <a name="querying-cache-items"></a>Consulta de los elementos en caché
ADAL proporciona memoria caché predeterminada en SharedPreferences con algunas características sencillas para hacer consultas sobre los elementos en caché. Puede obtener la memoria caché actual de AuthenticationContext mediante:

    ITokenCacheStore cache = mContext.getCache();

También puede proporcionar su implementación de la memoria caché, si desea personalizarla.

    mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);

### <a name="prompt-behavior"></a>Comportamiento de la petición
ADAL permite especificar el comportamiento de la petición. PromptBehavior.Auto mostrará la interfaz de usuario si el token de actualización no es válido y se necesitan credenciales de usuario. PromptBehavior.Always omitirá el uso de la memoria caché y mostrará siempre la interfaz de usuario.

### <a name="silent-token-request-from-cache-and-refresh"></a>Solicitud de token silenciosa desde la memoria caché y actualización
Una solicitud de token silenciosa no utiliza la interfaz de usuario emergente ni requiere una actividad. Devuelve un token de la memoria caché si está disponible. Si el token ha caducado, este método intenta actualizarlo. Si el token de actualización está caducado o falla por cualquier motivo, devuelve AuthenticationException.

    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );

También puede hacer una llamada de sincronización con este método. Puede establecer null para la devolución de llamada o usar acquireTokenSilentSync.

### <a name="diagnostics"></a>Diagnóstico
Estas son las principales fuentes de información para diagnosticar problemas:

* Excepciones
* Registros
* Seguimientos de red

Tenga en cuenta que los identificadores de correlación son fundamentales para el diagnóstico en la biblioteca. Puede establecer los identificadores de correlación sujetos a solicitud si desea establecer una correlación de ADAL con otras operaciones en el código. Si no establece un identificador de correlación, ADAL generará uno aleatorio. A continuación, todos los mensajes de registro y llamadas de red se marcarán con el identificador de correlación. El identificador generado automáticamente se modifica con cada solicitud.

#### <a name="exceptions"></a>Excepciones
Las excepciones son el primer diagnóstico. Intentamos ofrecerle mensajes de error útiles. Si encuentra alguno que no sea útil, genere un caso y háganoslo saber. Proporcione también información sobre el dispositivo, por ejemplo, el modelo y el número de SDK.

#### <a name="logs"></a>Registros
Puede configurar la biblioteca para que genere mensajes de registro que podrá utilizar para diagnosticar problemas. Configure el registro mediante la siguiente llamada para configurar una devolución de llamada que ADAL usará para entregar los mensajes de registro a medida que se generen.

    Logger.getInstance().setExternalLogger(new ILogger() {
        @Override
        public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
        ...
        // You can write this to log file depending on level or error code.
        writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
        }
    }

Los mensajes pueden escribirse en un archivo de registro personalizado, tal como se muestra en el siguiente código. Por desgracia, no hay ninguna manera estándar de obtener los registros de un dispositivo. Hay algunos servicios que pueden ayudar con esta tarea. También puede "inventar" sus propios métodos, como enviar el archivo a un servidor.

    private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
    }

Estos son los niveles de registro:
* Error (excepciones)
* Warn (advertencia)
* Info (información)
* Verbose (más detalles)

El nivel de registro se define de la siguiente forma:

    Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

 Todos los mensajes de registro se envían a logcat, además de las devoluciones de llamada de registro personalizadas.
Es posible obtener un registro a un archivo de logcat como se indica a continuación:

    adb logcat > "C:\logmsg\logfile.txt"

 Para más información acerca de los comandos de adb, consulte la [información de logcat en el sitio de Android](https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat).

#### <a name="network-traces"></a>Seguimientos de red
Puede usar varias herramientas para capturar el tráfico HTTP que genera ADAL.  Esto es muy útil si está familiarizado con el protocolo OAuth o si necesita proporcionar información de diagnóstico a Microsoft o a otros canales de soporte técnico.

Fiddler es la herramienta de seguimiento de HTTP más sencilla. Utilice los vínculos siguientes para configurarla para que registre correctamente el tráfico de red de ADAL. Para que una herramienta de seguimiento como Fiddler o Charles surta efecto, debe configurarla para registrar tráfico SSL sin cifrar.  

> [!NOTE]
> Los seguimientos generados de esta manera pueden contener información altamente privilegiada, como tokens de acceso, nombres de usuario y contraseñas. Si utiliza cuentas de producción, no comparta esta información con terceras partes. Si necesita transmitir el seguimiento a alguien para obtener soporte técnico, reproduzca el problema con una cuenta temporal con nombres de usuario y contraseñas que no le importe compartir.

* En el sitio web de Telerik, consulte cómo [configurar Fiddler para Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid).
* En GitHub, consulte cómo [configurar reglas de Fiddler para ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler).

### <a name="dialog-mode"></a>Modo de diálogo
El método acquireToken sin actividad es compatible con el modo de cuadro de diálogo.

### <a name="encryption"></a>Cifrado
ADAL cifra los tokens y los almacena en SharedPreferences de forma predeterminada. Puede consultar la clase StorageHelper para ver los detalles. Android introdujo el almacenamiento seguro de claves privadas AndroidKeyStore 4.3 (API18). ADAL lo utiliza para API 18 y las versiones posteriores. Si desea usar ADAL para las versiones inferiores de SDK, deberá proporcionar una clave secreta en AuthenticationSettings.INSTANCE.setSecretKey.

### <a name="oauth2-bearer-challenge"></a>Desafío de portador de Oauth2
La clase AuthenticationParameters proporciona funcionalidad para obtener el authorization_uri a partir del desafío de portador de OAuth2.

### <a name="session-cookies-in-webview"></a>Cookies de sesión en WebView
Android WebView no elimina las cookies de sesión después de cerrar la aplicación. Esto puede controlarse mediante el siguiente código de ejemplo:

    CookieSyncManager.createInstance(getApplicationContext());
    CookieManager cookieManager = CookieManager.getInstance();
    cookieManager.removeSessionCookie();
    CookieSyncManager.getInstance().sync();

Para más información acerca de las cookies, consulte la [información sobre CookieSyncManager en el sitio de Android](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).

### <a name="resource-overrides"></a>Reemplazos de recursos
La biblioteca de ADAL incluye cadenas en inglés para mensajes de ProgressDialog. La aplicación debe sobrescribirlas si desea ver las cadenas traducidas.

     <string name="app_loading">Loading...</string>
     <string name="broker_processing">Broker is processing</string>
     <string name="http_auth_dialog_username">Username</string>
     <string name="http_auth_dialog_password">Password</string>
     <string name="http_auth_dialog_title">Sign In</string>
     <string name="http_auth_dialog_login">Login</string>
     <string name="http_auth_dialog_cancel">Cancel</string>

### <a name="ntlm-dialog-box"></a>Cuadro de diálogo NTLM
La versión 1.1.0 de ADAL admite el cuadro de diálogo NTLM que se procesa a través del evento onReceivedHttpAuthRequest desde WebViewClient. Puede personalizar el diseño y las cadenas del cuadro de diálogo.

### <a name="cross-app-sso"></a>SSO entre aplicaciones
Obtenga información sobre la [Habilitación de SSO entre aplicaciones en Android mediante ADAL](active-directory-sso-android.md).  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
