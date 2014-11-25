<properties linkid="develop-notificationhubs-tutorials-get-started-nokia-x" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="kirillg" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-nokia-x" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="piyushjo"/>

# Introducción a los Centros de notificaciones

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/es-es/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/es-es/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/es-es/documentation/articles/notification-hubs-nokia-x-get-started/" title="Nokia X" class="current">Nokia X</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tema muestra cómo puede usar **Centros de notificaciones de Azure** para enviar notificaciones de inserción a una aplicación Android en **Nokia X**. En este tutorial, puede crear una aplicación Android en blanco que reciba notificaciones de inserción mediante el servicio de notificaciones de Nokia. Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su centro de notificaciones.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

-   [Configuración del servicio de notificaciones de Nokia][Configuración del servicio de notificaciones de Nokia]
-   [Configuración de su Centro de notificaciones][Configuración de su Centro de notificaciones]
-   [Conexión de su aplicación al Centro de notificaciones][Conexión de su aplicación al Centro de notificaciones]
-   [Envío de una notificación a la aplicación][Envío de una notificación a la aplicación]
-   [Prueba de la aplicación][Prueba de la aplicación]

<div class="dev-callout"><strong>Nota:</strong> <p>para completar este tutorial, deber&aacute; tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluaci&oacute;n gratuita en tan solo unos minutos. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluaci&oacute;n gratuita de Azure</a>.</p></div>

Este tutorial requiere lo siguiente:

1.  El entorno de desarrollo de Nokia X, que puede configurar siguiendo [estas][estas] instrucciones. Asegúrese de instalar los paquetes específicos de Nokia X y configurar el emulador de Nokia X siguiendo las instrucciones.
2.  La configuración del dispositivo de Nokia X, que puede llevar a cabo siguiendo [estas][1] instrucciones.
3.  El SDK de Android (se presupone que usará Eclipse), que puede descargarse desde [aquí][aquí]
4.  El SDK de Android para Servicios móviles, que puede descargar desde [aquí][2]<a>.

## <span id="register"></span></a>Configuración del servicio de notificaciones de Nokia

1.  Inicie sesión en la [consola para desarrolladores de API de notificaciones de Nokia][consola para desarrolladores de API de notificaciones de Nokia]

2.  Vaya a la pestaña **Create services** y cree un nuevo servicio proporcionando la información correspondiente en los campos **Sender ID** y **Service description**

    ![](./media/notification-hubs-nokia-x-get-started/NokiaConsole.png)

3.  Anote la información de los campos **Sender ID** y **Authorization Key** cuando el servicio se haya creado correctamente.

4.  Puede ir a la pestaña **My services** para enumerar todos los servicios creados con la información **Sender ID** y **Authorization Key** correspondiente.

    ![][3]

5.  Para obtener detalles, consulte este [vínculo][vínculo].

## <span id="configure-hub"></span></a>Configuración de su Centro de notificaciones

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure] y, a continuación, haga clic en **+NEW** en la parte inferior de la pantalla.

2.  Haga clic sucesivamente en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y finalmente en **Creación rápida**.

    ![][4]

3.  Escriba un nombre para su centro de notificaciones, seleccione la región deseada y, si lo desea, un espacio de nombres; por último, haga clic en **Crear una nueva base de datos central de notificaciones**.

    ![][5]

4.  Vaya al centro notificaciones que acaba de crear. Haga clic en la pestaña Bus de servicio situada la izquierda y después haga clic en el espacio de nombres donde creó el centro de notificaciones; por último haga clic en la pestaña Bases de datos centrales de notificaciones.

    ![][6]

5.  Una vez en el centro de notificaciones creado, haga clic en la pestaña **Configurar** situada en la parte superior.

    ![][7]

6.  Desplácese hacia abajo, busque la **configuración de notificaciones de Nokia X**, pegue la clave de autorización (Authorization Key) que obtuvo del servicio de notificaciones de Nokia y haga clic en el botón **Guardar** situado en la parte inferior de la página.

    ![][8]

7.  Seleccione la pestaña **Panel** situada en la parte superior y haga clic en **Información de conexión** en la parte inferior de la página.

    ![][9]

8.  Anote las dos cadenas de conexión SAS correspondientes a **DefaultListenSharedAccessSignature** y **DefaultFullSharedAccessSignature** que usará más tarde en el tutorial.

## <span id="connect-hub"></span></a>Conexión de su aplicación al Centro de notificaciones

### Creación de un nuevo proyecto de Android

1.  En Eclipse ADT, cree un nuevo proyecto de Android (File -\> New -\> Android Application).

2.  Asegúrese de que Minimum Required SDK está establecido en **API 16: Android 4.1 (Jelly Bean)** y que las próximas entradas del SDK se establezcan en la última versión disponible. Seleccione Next y siga el asistente asegurándose de que **Create activity** se encuentra seleccionado para crear una **actividad en blanco**. Acepte el icono del selector predeterminado en la siguiente casilla y haga clic en **Finish** en la última.

    ![][10]

3.  Asegúrese de que el destino de creación del proyecto está correctamente establecido en (Platform = 4.1.2 y API Level = 16 para este ejemplo). Haga clic con el botón secundario en Project, seleccione Properties y elija Android en el cuadro de diálogo Project Properties.

    ![][11]

4.  Agregue el archivo JAR del servicio de notificaciones de Nokia al proyecto. Esta biblioteca auxiliar de notificaciones de Nokia **push.jar** proporciona una API similar a la biblioteca auxiliar.
    Navegue a Project Properties -\> Java Build Path -\> Libraries -\> Add External JARs y agregue la biblioteca **push.jar** disponible en **\\extras\\nokia\\nokia\_x\_services\\libs\\nna\\push.jar**. Javadoc para la biblioteca se encuentra en **\\extras\\nokia\\nokia\_x\_services\\javadocs\\nna**, donde está instalado Eclipse IDE.

5.  Asegúrese de copiar esta biblioteca push.jar en el directorio \\libs del proyecto en el Explorador de paquetes.

6.  Descargue el SDK para Android de Centros de notificaciones desde [aquí][2]. Extraiga el archivo .zip y copie el archivo notificationhubs\\notification-hubs-0,3.jar en el directorio \\libs del proyecto en el Explorador de paquetes.

    <div class="dev-callout"><b>Nota:</b>
<p>Los n&uacute;meros que aparecen al final del nombre del archivo pueden cambiar en versiones de SDK posteriores.</p>
</div>

### Incorporación de código

1.  Abra el archivo **AndroidManifest.xml** y reemplace el elemento de aplicación por las siguientes líneas de código asegurándose de que reemplaza `[YourPackageName]` por el nombre del paquete que especificó al crear la aplicación Android.

        <!-- Push Notifications connects to Internet services. -->
        <uses-permission android:name="android.permission.INTERNET" />

        <!-- Keeps the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />

         <!--
         Creates a custom permission so only this app can receive its messages.

         NOTE: the permission *must* be called PACKAGE.permission.C2D_MESSAGE,
               where PACKAGE is the application's package name.
        -->
        <permission 
            android:name="[YourPackageName].permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="[YourPackageName].permission.C2D_MESSAGE" />

        <uses-permission android:name="com.nokia.pushnotifications.permission.RECEIVE" />

        <application
            android:allowBackup="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >
            <activity
                android:name="[YourPackageName].MainActivity"
                android:label="@string/app_name" >
                <intent-filter>
                    <action android:name="android.intent.action.MAIN" />
                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
            </activity>

            <receiver
                android:name="com.nokia.push.PushBroadcastReceiver"
                android:permission="com.nokia.pushnotifications.permission.SEND" >
                <intent-filter>

                    <!-- Receives the actual messages. -->
                    <action android:name="com.nokia.pushnotifications.intent.RECEIVE" />
                    <!-- Receives the registration id. -->
                    <action android:name="com.nokia.pushnotifications.intent.REGISTRATION" />
                    <category android:name="[YourPackageName]" />
                </intent-filter>
            </receiver>

            <service android:name="[YourPackageName].PushIntentService" />
        </application> 

2.  En el Explorador de paquetes, haga clic con el botón secundario en el paquete (bajo el nodo `src`), haga clic en **Nuevo**, después en **Clase** y cree una nueva clase llamada **ConfigurationSettings.java**
    ![][12].

    Después, agregue el siguiente código a ella, que define las constantes usadas en el ejemplo:

        public class ConfigurationSettings {
            public static String NotificationHubName = "";
            public static String NotificationHubConnectionString = "";
            public static String SenderId = "";
        }

    Rellene las constantes anteriores con la configuración de **SenderId**, de la consola de inserción de Nokia, y con **NotificationHubName** y **NotificationHubConnectionString** (DefaultListenSharedAccessSignature), del portal de administración.

3.  En **MainActivity.java**, agregue la siguiente instrucción de importación:

        import com.nokia.push.PushRegistrar;

    y después agregue el siguiente código en el método onCreate:

        // Make sure the device has the proper dependencies.
        PushRegistrar.checkDevice(this);

        // Make sure the manifest was properly set - comment out this line
        // while developing the app, then uncomment it when it's ready
        PushRegistrar.checkManifest(this);

        // Register the device with the Nokia Notification service
        PushRegistrar.register(this, ConfigurationSettings.SenderId);

4.  Agregue otra nueva clase llamada **PushIntentService.java** y agregue el siguiente código que se registrará con el centro de notificaciones y también administrará los mensajes recibidos de dicho centro.

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import android.util.Log;

        import com.microsoft.windowsazure.messaging.NotificationHub;
        import com.nokia.push.PushBaseIntentService;

        /**
         * IntentService responsible for handling push notification messages.
         */
        public class PushIntentService extends PushBaseIntentService {

            private NotificationManager mNotificationManager;
            private static NotificationHub hub;
            public static final int NOTIFICATION_ID = 1;
            private static final String TAG = "NokiaXPush/PushIntentService";

            /**
             * Constructor.
             */
            public PushIntentService() {
            }

            @Override
            protected String[] getSenderIds(Context context) {
                return new String[] { ConfigurationSettings.SenderId };
            }   

            @Override
            protected void onRegistered(Context context, String registrationId) {
                Log.i(TAG, "Nokia Registration ID \"" + registrationId + "\"");
                RegisterWithNotificationHub(context, registrationId);
            }

            public static void RegisterWithNotificationHub(Context context, String registrationId) {
                if (hub == null) {
                    hub = new NotificationHub(
                            ConfigurationSettings.NotificationHubName, 
                            ConfigurationSettings.NotificationHubConnectionString, 
                            context);
                }
                try {
                    hub.register(registrationId);
                    Log.i(TAG, "Registered with Notification Hub - '" 
                            + ConfigurationSettings.NotificationHubName + "'" 
                            + " with RegistrationID - '"
                            + registrationId + "'");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }

            @Override
            protected void onMessage(Context context, Intent intent) {
                String message = intentExtrasToString(intent.getExtras());
                Log.i(TAG, "Received message. Extras: " + message);
                generateNotification(context, message);
            }

            /**
             * Extracts the key-value pairs from the given Intent extras and returns
             * them in a string.
             */
            private String intentExtrasToString(Bundle extras) {
                StringBuilder sb = new StringBuilder();
                sb.append("{ ");

                for (String key : extras.keySet()) {
                    sb.append(sb.length() <= 2 ? "" : ", ");
                    sb.append(key).append("=").append(extras.get(key));
                }

                sb.append(" }");
                return sb.toString();
            }

            /**
             * Issues a notification to inform the user that server has sent a message.
             */
            private void generateNotification(Context context, String message)
            {
                mNotificationManager = (NotificationManager)
                        context.getSystemService(Context.NOTIFICATION_SERVICE);

                PendingIntent contentIntent = PendingIntent.getActivity(context, 0,
                    new Intent(context, MainActivity.class), 0);

                NotificationCompat.Builder mBuilder =
                    new NotificationCompat.Builder(context)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle("Notification Hub Demo")
                    .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(message))
                    .setContentText(message);

                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }

            @Override
            protected void onError(Context arg0, String errorId) {
                Log.i(TAG, "Received error:  " + errorId);
            }

            @Override
            protected void onUnregistered(Context arg0, String errorId) {
                Log.i(TAG, "Received recoverable error: " + errorId);
            }
        }

## <a name="send"></a>Envío de una notificación a la aplicación

Puede enviar notificaciones mediante los centros de notificaciones desde cualquier back-end que use la [interfaz de REST][interfaz de REST]. En este tutorial se lo mostraremos usando una aplicación de consola .NET.

1.  Cree una aplicación de consola nueva de Visual C#:

    ![][13]

2.  Agregue una referencia al SDK de Bus de servicio de Azure con el [paquete WindowsAzure.ServiceBus NuGet][paquete WindowsAzure.ServiceBus NuGet]. En el menú principal de Visual Studio, haga clic en **Herramientas**, luego en **Administrador de paquetes de biblioteca** y finalmente en **Consola del Administrador de paquetes**. Posteriormente, en la ventana de la consola, escriba lo siguiente y presione Entrar:

        Install-Package WindowsAzure.ServiceBus

3.  Abra el archivo Program.cs y agregue la siguiente instrucción using:

        using Microsoft.ServiceBus.Notifications;

4.  En la clase `Program`, agregue el siguiente método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<DefaultFullSharedAccessSignatureSASConnectionString>", "<hub name>");
            await hub.SendNokiaXNativeNotificationAsync("{\"data\" : {\"payload\":\"" + "Hello from Azure" + "\"}}");
        }

5.  Posteriormente, agregue las siguientes líneas al método Main:

         SendNotificationAsync();
         Console.ReadLine();

## <a name="test-app"></a>Prueba de la aplicación

Para realizar una prueba de la aplicación con un teléfono real, conéctelo al equipo mediante un cable USB.

Para realizar una prueba de esta aplicación con el emulador:

1.  En la barra de herramientas superior de Eclipse, haga clic en Run y, a continuación, seleccione la aplicación.

2.  De esa forma, se carga la aplicación en el teléfono vinculado o se inicia el emulador y se carga y ejecuta la aplicación.

3.  La aplicación recupera el valor de registrationId del servicio de notificaciones de Nokia y se registra con el centro de notificaciones.

    <div class="dev-callout"><b>Nota:</b>
<p>
Si la aplicaci&oacute;n Android es capaz de registrarse correctamente con el centro de notificaciones, ver&aacute; un mensaje como el siguiente en los registros 'Eclipse Logcat':
'Registered with Notification Hub - '<yourNotificationHubName>' with RegistrationID - '<RegistrationIdReturnedByNokiaNotificationService'' </p>
</div>

4.  Para enviar una notificación de prueba cuando use la aplicación de consola .Net, simplemente presione la tecla F5 en Visual Studio para ejecutar la aplicación y se enviará una notificación que aparecerá en el área de notificaciones superior del dispositivo o emulador.

 

  [Configuración del servicio de notificaciones de Nokia]: #register
  [Configuración de su Centro de notificaciones]: #configure-hub
  [Conexión de su aplicación al Centro de notificaciones]: #connect-hub
  [Envío de una notificación a la aplicación]: #send
  [Prueba de la aplicación]: #test-app
  [estas]: http://developer.nokia.com/resources/library/nokia-x/getting-started/environment-setup.html
  [1]: http://developer.nokia.com/resources/library/nokia-x/getting-started/device-setup.html
  [aquí]: http://go.microsoft.com/fwlink/?linkid=389797&clcid=0x409
  [2]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [consola para desarrolladores de API de notificaciones de Nokia]: https://console.push.nokia.com/ncm/Web/index.jsp
  
  [3]: ./media/notification-hubs-nokia-x-get-started/NokiaConsoleService.png
  [vínculo]: http://developer.nokia.com/resources/library/nokia-x/nokia-notifications/nokia-notifications-developer-guide.html
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [4]: ./media/notification-hubs-nokia-x-get-started/AzureManagementPortal.png
  [5]: ./media/notification-hubs-nokia-x-get-started/AzureManagementCreateNH.png
  [6]: ./media/notification-hubs-nokia-x-get-started/NHConfigureTopItem.png
  [7]: ./media/notification-hubs-nokia-x-get-started/NHConfigure.png
  [8]: ./media/notification-hubs-nokia-x-get-started/NHConfigureNokiaX.png
  [9]: ./media/notification-hubs-nokia-x-get-started/NHDashboard.png
  [10]: ./media/notification-hubs-nokia-x-get-started/NewAndroidApp.png
  [11]: ./media/notification-hubs-nokia-x-get-started/AndroidBuildTarget.png
  [12]: ./media/notification-hubs-nokia-x-get-started/NewJavaClass.png
  [interfaz de REST]: http://msdn.microsoft.com/es-es/library/windowsazure/dn223264.aspx
  [13]: ./media/notification-hubs-nokia-x-get-started/ConsoleProject.png
  [paquete WindowsAzure.ServiceBus NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
