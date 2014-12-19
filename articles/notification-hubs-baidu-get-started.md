<properties urlDisplayName="Get Started" pageTitle="Introducción a los Centros de notificaciones de Azure" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="dwrede" editor="" />

<tags ms.service="" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="10/03/2014" ms.author="piyushjo" />

# Introducción a los Centros de notificaciones

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Store C#">C# de Tienda Windows</a><a href="/es-es/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/es-es/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/es-es/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/es-es/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu" class="current">Baidu</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Baidu es un servicio en la nube chino que puede utilizar para enviar notificaciones de inserción a dispositivos móviles. Este servicio es especialmente útil en China, donde la entrega de notificaciones de inserción para Android es compleja debido a la presencia de diferentes tiendas de aplicaciones y servicios de inserción y a la disponibilidad de los dispositivos Android que no están habitualmente conectados a GCM (Servicio de mensajería en la nube de Google). 

Este tutorial requiere lo siguiente:

+ El SDK de Android (se presupone que usará Eclipse), que puede descargarse <a href="http://go.microsoft.com/fwlink/?LinkId=389797">aquí</a>.
+ [SDK de Android para Servicios móviles]
+ [SDK de Android para Baidu Push]

>[WACOM.NOTE] Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure</a>..

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

* [Creación de una cuenta de Baidu](#createBaiduAccount)
* [Registro como desarrollador de Baidu](#registerBaiduDeveloper)
* [Creación de un proyecto de inserción en la nube de Baidu](#createBaiduPushProject)
* [Configuración de su Centro de notificaciones](#configure-hub)
* [Conexión de su aplicación al Centro de notificaciones](#connecting-app)
* [Envío de notificaciones a la aplicación](#send)

##<a id="createBaiduAccount"></a>Creación de una cuenta de Baidu

Para utilizar Baidu, debe crear una cuenta. Si ya tiene una, inicie sesión en el [portal de Baidu] con su cuenta de Baidu y vaya al paso siguiente; de lo contrario, consulte las siguientes instrucciones sobre cómo crear una nueva cuenta de Baidu.  

1. Vaya al [portal de Baidu] y haga clic en el vínculo de inicio de sesión . Haga clic en  para iniciar el proceso de registro de una nueva cuenta. 

   	![][1]

2. Especifique los detalles necesarios: teléfono o correo electrónico, contraseña y el código de verificación y haga clic en Signup.

   	![][2]

3. Se le enviará un correo electrónico a la dirección que ha especificado con un vínculo para activar su cuenta de Baidu. 

   	![][3]

4. Inicie sesión en su cuenta de correo electrónico, abra el correo de activación de Baidu y haga clic en el vínculo de activación para activar su cuenta de Baidu. 

   	![][4]

Una vez que tenga activada una cuenta de Baidu, inicie sesión en el [portal de Baidu] con su cuenta. 

##<a id="registerBaiduDeveloper"></a>Registro como desarrollador de Baidu

1. Después de iniciar sesión en el [portal de Baidu], haga clic en **>> (más)**.

  	![][5]

2. Desplácese hacia abajo la sección ** (Administrador de Web y servicios para desarrolladores)** y haga clic en ** (plataforma de nube abierta de Baidu)**. 

  	![][6]

3. En la siguiente página, haga clic en ** (Servicios para desarrolladores)** en la esquina superior derecha. 

  	![][7]

4. En la siguiente página, haga clic en ** (Desarrolladores registrados)** en el menú de la esquina superior derecha. 

  	![][8]

5. Escriba su nombre, la descripción y el número de teléfono móvil para recibir un mensaje de texto de comprobación y, a continuación, haga clic en ** (Enviar código de verificación)**. Tenga en cuenta que para números de teléfono internacionales deberá incluir el código de país entre llaves, por ejemplo, para un número de Estados Unidos, será **(1)1234567890**.

  	![][9]

6. A continuación, debe recibir un mensaje de texto con un número de verificación, tal como se muestra en el ejemplo siguiente:

  	![][10] 

7. Escriba el número de verificación del mensaje en ** (Código de confirmación)**. 

8. Por último, complete el registro para desarrolladores; para ello, acepte el acuerdo de Baidu y haga clic en ** (Enviar)**. Verá la página siguiente tras la finalización correcta del registro:

  	![][11] 

##<a id="createBaiduPushProject"></a>Creación de un proyecto de inserción en la nube de Baidu

Cuando se crea un proyecto de inserción en la nube de Baidu, recibe el identificador de la aplicación, la clave de API y la clave secreta.

1. Después de iniciar sesión en el [portal de Baidu], haga clic en **>> (más)**.

  	![][5]

2. Desplácese hacia abajo la sección ** (Administrador de Web y servicios para desarrolladores)**  y haga clic en ** (plataforma de nube abierta de Baidu)** . 

  	![][6]

3. En la siguiente página, haga clic en ** (Servicios para desarrolladores)** en la esquina superior derecha. 

  	![][7]

4. En la siguiente página, haga clic en ** (Inserción en la nube)** en la sección ** (Servicios en la nube)**. 

  	![][12]

5. Cuando ya sea un desarrollador registrado, verá ** (Consola de administración)** en el menú de la parte superior. Haga clic en ** (Administración de servicios para desarrolladores)**. 

  	![][13]

6. En la siguiente página, haga clic en ** (Crear proyecto)**.

  	![][14]

7. Escriba un nombre de aplicación y haga clic en ** (Crear)**.

  	![][15]

8. Tras la creación correcta, verá una página con el **AppID**, la **clave de API** y la **clave secreta**. Tome nota de la **clave de API** y la **clave secreta** que usaremos más tarde. 

  	![][16]

9. Configure el proyecto para notificaciones de inserción haciendo clic en ** (Inserción en la nube)** en el panel izquierdo. 

  	![][31]

10. En la siguiente página, haga clic en el botón ** (Configuración de inserción)** .

	![][32]  

11. En la página de configuración, agregue el nombre del paquete que usará en su proyecto de Android en el campo ** (Paquete de aplicación)** y haga clic en ** (Guardar)**  

	![][33]

Verá el mensaje **!(Guardado correctamente)**.

##<a id="configure-hub"></a>Configuración de su Centro de notificaciones

1. Inicie sesión en el [Portal de administración de Azure] y luego haga clic en **+NUEVO** en la parte inferior de la pantalla.

2. Haga clic sucesivamente en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y **Creación rápida**.
 
3. Proporcione un nombre para el **Centro de notificaciones**, seleccione la **Región** y el **Espacio de nombres** donde se creará este centro de notificaciones y luego haga clic en **Crear una nueva base de datos central de notificaciones**  

  	![][17]

4. Haga clic en el espacio de nombres en el que creó el centro de notificaciones y, a continuación, haga clic en **Centros de notificaciones** en la parte superior. 

  	![][18]

5. Seleccione el centro de notificaciones que creó y haga clic en **Configurar** en el menú superior.

  	![][19]

6. Desplácese hacia abajo hasta la sección de **configuración de notificaciones de baidu** y escriba la **clave de API** y la **clave secreta** que obtuvo anteriormente en la consola de Baidu para su proyecto de inserción en la nube de Baidu. Haga clic en **Guardar** tras escribir estos valores. 

  	![][20]

7. Haga clic en la pestaña **Panel** en la parte superior del centro de notificaciones y haga clic en **Ver cadena de conexión**.

  	![][21]

8. Anote los valores de **DefaultListenSharedAccessSignature** y **DefaultFullSharedAccessSignature** de la ventana de información de la conexión de acceso. 

    ![][22]

##<a id="connecting-app"></a>Conexión de su aplicación al Centro de notificaciones

1. En Eclipse ADT, cree un nuevo proyecto de Android (File -> New -> Android Application).

    ![][23]

2. Escriba un **nombre de aplicación** y asegúrese de que la versión de **SDK mínimo necesario** esté establecida en **API 16: Android 4.1**.

    ![][24]

3. Haga clic en **Next** y continúe con el asistente hasta llegar a la ventana **Create Activity**. Asegúrese de que está seleccionada la opción **Blank Activity** y, finalmente, seleccione **Finish** para crear una nueva aplicación de Android. 

    ![][25]

4. Asegúrese de que el **destino de compilación del proyecto** esté establecido correctamente.

    ![][26]

5. Descargue y descomprima el [SDK de Android para Servicios móviles], abra la carpeta **notificationhubs**, copie el archivo **notification-hubs-x.y.jar** en la carpeta *libs* de su proyecto de Eclipse y actualice la carpeta *libs*.

6. Descargue y descomprima el [SDK de Android para Baidu Push], abra la carpeta **libs** y copie el archivo jar *pushservice-x.y.z* y las carpetas *armeabi* & *mips* en la carpeta **libs** de su aplicación de Android. 

    ![][27]

7. Abra el archivo **AndroidManifest.xml** de su proyecto Android y agregue los permisos que requiere el SDK de Baidu.

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

8. Agregue la propiedad *android:name* a su elemento *application* en **AndroidManifest.xml** y sustituya *yourprojectname*, por ejemplo **com.example.BaiduTest**. Asegúrese de que este nombre de proyecto coincide con el que configuró en la consola de Baidu. 

		<application android:name="yourprojectname.DemoApplication"

9. Agregue la siguiente configuración en el elemento de la aplicación después del elemento de actividad .MainActivity y sustituya *yourprojectname*, por ejemplo, **com.example.BaiduTest**:

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

9. Agregue una nueva clase denominada **ConfigurationSettings.java** al proyecto. 

    ![][28]

    ![][29]

10. Agréguele el siguiente código:

		public class ConfigurationSettings {
		        public static String API_KEY = "...";
				public static String NotificationHubName = "...";
				public static String NotificationHubConnectionString = "...";
			}
	
	Establezca el valor de * API_KEY * con lo que ha recuperado anteriormente del proyecto en la nube de Baidu, * NotificationHubName* con el nombre del centro de notificaciones del portal de Azure y *NotificationHubConnectionString* con DefaultListenSharedAccessSignature desde el portal de Azure. 

11. Agregue una nueva clase denominada **DemoApplication.java** y agréguele el siguiente código:

		import com.baidu.frontia.FrontiaApplication;
		
		public class DemoApplication extends FrontiaApplication {
		    @Override
		    public void onCreate() {
		        super.onCreate();
		    }
		}

12. Agregue otra clase nueva denominada **MyPushMessageReceiver.java** y agréguele el siguiente código debajo: Esta es la clase que controla las notificaciones de inserción recibidas del servidor de inserción de Baidu:

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

13. Abra **MainActivity.java** y agregue el siguiente código al método **onCreate**:

	        PushManager.startWork(getApplicationContext(),
	                PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

y agregue las siguientes instrucciones de importación en la parte superior:
			import com.baidu.android.pushservice.PushConstants;
			import com.baidu.android.pushservice.PushManager;

##<a id="send"></a>Envío de notificaciones a la aplicación

Puede enviar notificaciones mediante los Centros de notificaciones desde cualquier back-end que use nuestra <a href="http://msdn.microsoft.com/es-es/library/windowsazure/dn223264.aspx">interfaz REST</a>. En este tutorial se lo mostraremos usando una aplicación de consola .NET. 

1. Cree una aplicación de consola nueva de Visual C#:

	![][30]

2. Agregue una referencia al SDK de Bus de servicio de Azure con el <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">paquete WindowsAzure.ServiceBus NuGet</a>. En el menú principal de Visual Studio, haga clic en **Tools**, luego en **Library Package Manager** y finalmente en **Package Manager Console**. Posteriormente, en la ventana de la consola, escriba lo siguiente y presione Entrar:

        Install-Package WindowsAzure.ServiceBus

3. Abra el archivo Program.cs y agregue la siguiente instrucción using:

        using Microsoft.ServiceBus.Notifications;

4. En la clase `Program`, agregue el siguiente método y sustituya *DefaultFullSharedAccessSignatureSASConnectionString* y *NotificationHubName* por los valores que tiene. 

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
			string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
			var result = await hub.SendBaiduNativeNotificationAsync(message);
		}

5. Posteriormente, agregue las siguientes líneas al método Main:

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="test-app"></a>Prueba de la aplicación

Para realizar una prueba de la aplicación con un teléfono real, conéctelo al equipo mediante un cable USB.

Para realizar una prueba de esta aplicación con el emulador:

1. En la barra de herramientas superior de Eclipse, haga clic en Run (Ejecutar) y, a continuación, seleccione la aplicación. 

2. De esa forma, se carga la aplicación en el teléfono vinculado o se inicia el emulador y se carga y ejecuta la aplicación.

3. La aplicación recupera los valores 'userId' y 'channelId' del servicio de notificaciones de inserción de Baidu y se registra en el centro de notificaciones.
	
4.	Para enviar una notificación de prueba cuando use la aplicación de consola .Net, simplemente presione la tecla F5 en Visual Studio para ejecutar la aplicación y se enviará una notificación que aparecerá en el área de notificaciones superior del dispositivo o emulador. 


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
[SDK de Android para Servicios móviles]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[SDK de Android para Baidu Push]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Portal de Baidu]: http://www.baidu.com/








	
