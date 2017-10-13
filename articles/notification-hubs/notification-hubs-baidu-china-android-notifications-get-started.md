---
title: "Introducción a Notification Hubs con Baidu | Microsoft Docs"
description: "En este tutorial aprenderá a usar Azure Notification Hubs para enviar notificaciones push a dispositivos Android mediante Baidu."
services: notification-hubs
documentationcenter: android
author: ysxu
manager: erikre
editor: 
ms.assetid: 23bde1ea-f978-43b2-9eeb-bfd7b9edc4c1
ms.service: notification-hubs
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: mobile-baidu
ms.workload: mobile
ms.date: 08/19/2016
ms.author: yuaxu
ms.openlocfilehash: df3bbda15e1245b6068c2b8290d0c96856051f1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-notification-hubs-using-baidu"></a>Introducción a Notification Hubs con Baidu
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Información general
La inserción de nube Baidu es un servicio de nube chino que puede utilizar para enviar notificaciones de inserción a dispositivos móviles. Este servicio es útil en China, donde la entrega de notificaciones push para Android es compleja debido a la presencia de distintas tiendas de aplicaciones y servicios de inserción, además de la disponibilidad de dispositivos Android que no suelen estar conectados a GCM (Google Cloud Messaging).

## <a name="prerequisites"></a>Requisitos previos
Este tutorial requiere lo siguiente:

* Android SDK (damos por hecho que usa Eclipse) que puede descargar en el <a href="http://go.microsoft.com/fwlink/?LinkId=389797">sitio de Android</a>
* [Android SDK para Mobile Services]
* [SDK de Android para inserciones Baidu]

> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).
> 
> 

## <a name="create-a-baidu-account"></a>Creación de una cuenta de Baidu
Para usar Baidu, tiene que tener una cuenta de Baidu. Si ya tiene una, inicie sesión en el [portal de Baidu] y vaya al paso siguiente. De lo contrario, consulte las siguientes instrucciones sobre cómo crear una cuenta de Baidu.  

1. Vaya al [portal de Baidu] y haga clic en el vínculo **登录** (**Iniciar sesión**). Haga clic en **立即注册** para iniciar el proceso de registro de cuenta.
   
   ![][1]
2. Especifique los detalles necesarios (teléfono o dirección de correo electrónico, la contraseña y el código de verificación) y haga clic en **Suscripción**.
   
   ![][2]
3. Se le enviará un mensaje de correo electrónico a la dirección que especificó con un vínculo para activar su cuenta de Baidu.
   
   ![][3]
4. Inicie sesión en su cuenta de correo electrónico, abra el mensaje de correo electrónico de activación de Baidu y haga clic en el vínculo de activación para activar su cuenta de Baidu.
   
   ![][4]

Una vez activada la cuenta de Baidu, inicie sesión en el [portal de Baidu].

## <a name="register-as-a-baidu-developer"></a>Registro como desarrollador de Baidu
1. Una vez que haya iniciado sesión en el [portal de Baidu], haga clic en **更多 >>** (**más**).
   
      ![][5]
2. Desplácese hacia abajo en la sección **站长与开发者服务 (Administrador de web y servicios para desarrolladores)** y haga clic en **百度开放云平台** (**plataforma en la nube abierta de Baidu**).
   
      ![][6]
3. En la siguiente página, haga clic en **开发者服务** (**Servicios para desarrolladores**) situado en la esquina superior derecha.
   
      ![][7]
4. En la siguiente página, haga clic en **注册开发者** (**Desarrolladores registrados**) en el menú de la esquina superior derecha.
   
      ![][8]
5. Escriba su nombre, una descripción y un número de teléfono móvil para recibir un mensaje de texto de verificación y, a continuación, haga clic en **送验证码** (**Enviar código de verificación**). Para números de teléfono internacionales deberá incluir el código de país entre paréntesis. Por ejemplo, para un número de Estados Unidos, es **(1) 1234567890**.
   
      ![][9]
6. A continuación, recibirá un mensaje de texto con un número de verificación, tal como se muestra en el ejemplo siguiente:
   
      ![][10]
7. Escriba el número de verificación del mensaje en **验证码** (**Código de confirmación**).
8. Por último, complete el registro de desarrollador aceptando el acuerdo de Baidu y haga clic en **提交** (**Enviar**). Se mostrará la página siguiente cuando el registro se haya completado correctamente:
   
      ![][11]

## <a name="create-a-baidu-cloud-push-project"></a>Creación de un proyecto de inserción de nube Baidu
Cuando se crea un proyecto de inserción de nube Baidu, recibirá el identificador de la aplicación, la clave de API y la clave secreta.

1. Una vez que haya iniciado sesión en el [portal de Baidu], haga clic en **更多 >>** (**más**).
   
      ![][5]
2. Desplácese hacia abajo en la sección **站长与开发者服务** (**Administrador de web y servicios para desarrolladores**) y haga clic en **百度开放云平台** (**plataforma en la nube abierta de Baidu**).
   
      ![][6]
3. En la siguiente página, haga clic en **开发者服务** (**Servicios para desarrolladores**) situado en la esquina superior derecha.
   
      ![][7]
4. En la siguiente página, haga clic en **云推送** (**Cloud Push**) desde la sección **云服务** (**Servicios en la nube**).
   
      ![][12]
5. Una vez que sea un desarrollador registrado, aparecerá **管理控制台** (**Consola de administración**) en el menú superior. Haga clic en **开发者服务管理** (**Administración de servicios de desarrolladores**).
   
      ![][13]
6. En la siguiente página, haga clic en **创建工程** (**Crear proyecto**).
   
      ![][14]
7. Escriba un nombre de aplicación y haga clic en **创建** (**Crear**).
   
      ![][15]
8. Tras crear correctamente un proyecto de Baidu Cloud Push, se mostrará una página con el **AppID**, la **clave de API** y la **clave secreta**. Tome nota de la clave de API y la clave secreta que se usarán más adelante.
   
      ![][16]
9. Configure el proyecto para las notificaciones push haciendo clic en **云推送** (**Cloud Push**) en el panel izquierdo.
   
      ![][31]
10. En la siguiente página, haga clic en el botón **推送设置** (**Configuración de inserción**).
    
    ![][32]  
11. En la página de configuración, agregue el nombre del paquete que se va a utilizar en su proyecto Android en el campo **应用包名** (**Paquete de aplicación**) y, a continuación, haga clic en **保存设置** (**Guardar**).  
    
    ![][33]

Verá el mensaje **保存成功！** (**Guardado correctamente**).

## <a name="configure-your-notification-hub"></a>Configuración de su Centro de notificaciones
1. Inicie sesión en el [Portal de Azure clásico]y, luego, haga clic en **+NUEVO** en la parte inferior de la pantalla.
2. Haga clic sucesivamente en **App Services**, **Service Bus**, **Centro de notificaciones** y, finalmente, en **Creación rápida**.
3. Proporcione un nombre para el **Centro de notificaciones**, seleccione la **Región** y el **espacio de nombres** donde se creará este centro de notificaciones y luego haga clic en **Crear un nuevo Centro de notificaciones**.  
   
      ![][17]
4. Haga clic en el espacio de nombres en el que creó el centro de notificaciones y luego haga clic en **Notification Hubs**  en la parte superior.
   
      ![][18]
5. Seleccione el centro de notificaciones creado y haga clic en **Configurar** en el menú superior.
   
      ![][19]
6. Desplácese hacia abajo hasta la sección de **Configuración de notificaciones de Baidu** y escriba la clave de API y la clave secreta obtenidas anteriormente en la consola Baidu para el proyecto de inserción de nube Baidu. Haga clic en **Guardar**.
   
      ![][20]
7. Haga clic en la pestaña **Panel** en la parte superior del centro de notificaciones y haga clic en **Ver cadena de conexión**.
   
      ![][21]
8. Tome nota de los valores de **DefaultListenSharedAccessSignature** y **DefaultFullSharedAccessSignature** de la ventana de **información de conexión de acceso**.
   
    ![][22]

## <a name="connect-your-app-to-the-notification-hub"></a>Conexión de la aplicación al Centro de notificaciones
1. En Eclipse ADT, cree un nuevo proyecto de Android (**File** > **New** > **Android Application Project**).
   
    ![][23]
2. Escriba un **nombre de la aplicación** y asegúrese de que la versión de **SDK mínimo requerido** esté establecida en **API 16: Android 4.1**.
   
    ![][24]
3. Haga clic en **Siguiente** y siga las instrucciones del asistente hasta llegar a la ventana **Create Activity** (Crear actividad). Asegúrese de que está seleccionada la opción **Blank Activity** (Actividad en blanco) y, finalmente, seleccione **Finish** (Finalizar) para crear una nueva aplicación de Android.
   
    ![][25]
4. Asegúrese de que **Project Build Target** (Destino de compilación del proyecto) esté correctamente establecido.
   
    ![][26]
5. Descargue el archivo notification-hubs-0.4.jar de la pestaña **Archivos** del [Notification-Hubs-Android-SDK en Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4). Agregue el archivo a la carpeta **libs** del proyecto Eclipse y actualice la carpeta *libs* .
6. Descargue y descomprima el [SDK de Android para inserciones Baidu], abra la carpeta **libs** y copie el archivo jar **pushservice-x.y.z** y las carpetas **armeabi** & **mips** en la carpeta **libs** de la aplicación de Android.
7. Abra el archivo **AndroidManifest.xml** de su proyecto de Android y agregue los permisos que requiere el SDK de Baidu.
   
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.WRITE_SETTINGS" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />
8. Agregue la propiedad **android:name** al elemento **application** en el archivo **AndroidManifest.xml** reemplazando *yourprojectname* (por ejemplo, por **com.example.BaiduTest**). Asegúrese de que este nombre de proyecto coincide con el que configuró en la consola de Baidu.
   
        <application android:name="yourprojectname.DemoApplication"
9. Agregue la siguiente configuración al elemento application después del elemento de actividad **.MainActivity** reemplazando *yourprojectname* (por ejemplo, por **com.example.BaiduTest**):
   
        <receiver android:name="yourprojectname.MyPushMessageReceiver">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
                <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
                <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>
        </receiver>
   
        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>
10. Agregue una nueva clase denominada **ConfigurationSettings.java** al proyecto.
    
     ![][28]
    
     ![][29]
11. Agregue el siguiente código:
    
        public class ConfigurationSettings {
                public static String API_KEY = "...";
                public static String NotificationHubName = "...";
                public static String NotificationHubConnectionString = "...";
            }
    
    Establezca el valor de **API_KEY** con el valor recuperado del proyecto de nube Baidu anterior. Establezca **NotificationHubName** con el nombre del Centro de notificaciones del Portal de Azure clásico y **NotificationHubConnectionString** con el valor de DefaultListenSharedAccessSignature del Portal de Azure clásico.
12. Agregue una nueva clase denominada **DemoApplication.java**y agréguele el código siguiente:
    
        import com.baidu.frontia.FrontiaApplication;
    
        public class DemoApplication extends FrontiaApplication {
            @Override
            public void onCreate() {
                super.onCreate();
            }
        }
13. Agregue otra nueva clase denominada **MyPushMessageReceiver.java** y agréguele el código siguiente. Es la clase que controla las notificaciones push que se reciben del servidor push de Baidu.
    
        import java.util.List;
        import android.content.Context;
        import android.os.AsyncTask;
        import android.util.Log;
        import com.baidu.frontia.api.FrontiaPushMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub;
    
        public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
            /** TAG to Log */
            public static NotificationHub hub = null;
            public static String mChannelId, mUserId;
            public static final String TAG = MyPushMessageReceiver.class
                    .getSimpleName();
    
            @Override
            public void onBind(Context context, int errorCode, String appid,
                    String userId, String channelId, String requestId) {
                String responseString = "onBind errorCode=" + errorCode + " appid="
                        + appid + " userId=" + userId + " channelId=" + channelId
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
                mChannelId = channelId;
                mUserId = userId;
    
                try {
                    if (hub == null) {
                        hub = new NotificationHub(
                                ConfigurationSettings.NotificationHubName,
                                ConfigurationSettings.NotificationHubConnectionString,
                                context);
                        Log.i(TAG, "Notification hub initialized");
                    }
                } catch (Exception e) {
                   Log.e(TAG, e.getMessage());
                }
    
                registerWithNotificationHubs();
            }
    
            private void registerWithNotificationHubs() {
               new AsyncTask<Void, Void, Void>() {
                  @Override
                  protected Void doInBackground(Void... params) {
                     try {
                         hub.registerBaidu(mUserId, mChannelId);
                         Log.i(TAG, "Registered with Notification Hub - '"
                                 + ConfigurationSettings.NotificationHubName + "'"
                                 + " with UserId - '"
                                 + mUserId + "' and Channel Id - '"
                                 + mChannelId + "'");
                     } catch (Exception e) {
                         Log.e(TAG, e.getMessage());
                     }
                     return null;
                 }
               }.execute(null, null, null);
            }
    
            @Override
            public void onSetTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onSetTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onDelTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onDelTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onListTags(Context context, int errorCode, List<String> tags,
                    String requestId) {
                String responseString = "onListTags errorCode=" + errorCode + " tags="
                        + tags;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onUnbind(Context context, int errorCode, String requestId) {
                String responseString = "onUnbind errorCode=" + errorCode
                        + " requestId = " + requestId;
                Log.d(TAG, responseString);
            }
    
            @Override
            public void onNotificationClicked(Context context, String title,
                    String description, String customContentString) {
                String notifyString = "title=\"" + title + "\" description=\""
                        + description + "\" customContent=" + customContentString;
                Log.d(TAG, notifyString);
            }
    
            @Override
            public void onMessage(Context context, String message,
                    String customContentString) {
                String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
                Log.d(TAG, messageString);
            }
        }
14. Abra **MainActivity.java** y agregue lo siguiente al método **onCreate**:
    
            PushManager.startWork(getApplicationContext(),
                    PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);
15. Abra las siguientes instrucciones de importación en la parte superior:
    
            import com.baidu.android.pushservice.PushConstants;
            import com.baidu.android.pushservice.PushManager;

## <a name="send-notifications-to-your-app"></a>Envío de notificaciones a la aplicación
Para probar de forma rápida la recepción de notificaciones en su aplicación, envíe notificaciones en [Azure Portal](https://portal.azure.com/) mediante el botón **Enviar** en el centro de notificaciones, tal como se muestra en la pantalla que aparece a continuación:

![](./media/notification-hubs-baidu-get-started/notification-hub-test-send-baidu.png)

Las notificaciones push se envían normalmente en un servicio back-end como Mobile Services o ASP.NET mediante una biblioteca compatible. Puede usar la API de REST directamente para enviar mensajes de notificación si no hay disponible una biblioteca para su back-end.

En este tutorial, vamos a simplificar las cosas y mostrar solo la prueba de la aplicación cliente mediante el envío de notificaciones con el SDK de .NET para Notification Hubs en una aplicación de consola en lugar de un servicio back-end. Se recomienda seguir el tutorial [Azure Notification Hubs notifica a los usuarios con back-end de .NET](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) como paso siguiente para enviar notificaciones desde un back-end de ASP.NET. Sin embargo, se pueden usar los siguientes enfoques para enviar notificaciones:

* **Interfaz de REST**: puede admitir notificaciones en cualquier plataforma de back-end mediante la [interfaz de REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).
* **SDK para .NET de Microsoft Azure Notification Hubs**: en el Administrador de paquetes NuGet para Visual Studio, ejecute [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* **Node.js**: [Uso de Notification Hubs desde Node.js](notification-hubs-nodejs-push-notification-tutorial.md).
* **Mobile Apps**: para ver un ejemplo de cómo enviar notificaciones desde un back-end de Azure App Service Mobile Apps integrado en Notification Hubs, consulte [Incorporación de notificaciones push a la aplicación móvil](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).
* **Java / PHP**: para ver un ejemplo de cómo enviar notificaciones con las API de REST, consulte "Uso de Notification Hubs desde Java o PHP" ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

## <a name="optional-send-notifications-from-a-net-console-app"></a>(Opcional) Envío de notificaciones desde una aplicación de consola .NET
En esta sección, mostramos cómo enviar una notificación mediante una aplicación de consola .NET.

1. Cree una aplicación de consola nueva de Visual C#:
   
    ![][30]
2. En la ventana de la Consola del Administrador de paquetes, seleccione en **Proyecto predeterminado** el nuevo proyecto de aplicación de consola y, a continuación, ejecute el siguiente comando en la ventana de la consola:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    Esta instrucción agrega una referencia al SDK de Azure Notification Hubs mediante el <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">paquete NuGet Microsoft.Azure.NotificationHubs</a>.
   
    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)
3. Abra el archivo **Program.cs** y agregue la siguiente instrucción using:
   
        using Microsoft.Azure.NotificationHubs;
4. En la clase `Program`, agregue el método siguiente y reemplace *DefaultFullSharedAccessSignatureSASConnectionString* y *NotificationHubName* por los valores que tiene.
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
            string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
            var result = await hub.SendBaiduNativeNotificationAsync(message);
        }
5. Agregue las siguientes líneas al método **Main** :
   
         SendNotificationAsync();
         Console.ReadLine();

## <a name="test-your-app"></a>Prueba de la aplicación
Para realizar una prueba de la aplicación con un teléfono real, conéctelo al equipo mediante un cable USB. Esta acción carga la aplicación en el teléfono vinculado.

Para probar esta aplicación con el emulador, en la barra de herramientas superior de Eclipse, haga clic en **Run**(Ejecutar) y seleccione la aplicación: se inicia el emulador y se carga y ejecuta la aplicación.

La aplicación recupera los valores de 'userId' y 'channelId' desde el servicio de notificaciones push de Baidu y se registra con el centro de notificaciones.

Para enviar una notificación de prueba, puede usar la pestaña de depuración del Portal de Azure clásico. Si compila la aplicación de la consola .NET para Visual Studio, presione simplemente la tecla F5 en Visual Studio para ejecutar la aplicación. La aplicación envía una notificación que aparece en el área de notificación superior del dispositivo o emulador.

<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[Android SDK para Mobile Services]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[SDK de Android para inserciones Baidu]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Portal de Azure clásico]: https://manage.windowsazure.com/
[portal de Baidu]: http://www.baidu.com/
