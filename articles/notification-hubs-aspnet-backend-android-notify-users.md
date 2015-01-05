<properties title="Azure Notification Hubs Notify Users" pageTitle="Notificación a los usuarios de Centros de notificaciones de Azure" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in C# using the .NET API." documentationCenter="" services="notification-hubs" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="glenga" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

#Notificación a los usuarios de Centros de notificaciones de Azure

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/en-us/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/en-us/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
		<a href="/en-us/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android" class="current">Android</a>
</div>

La compatibilidad con las notificaciones de inserción en Azure le permite tener acceso a una infraestructura multiplataforma y de escalamiento horizontal fácil de usar, que simplifica considerablemente la implementación de notificaciones de inserción tanto en aplicaciones de consumidor, como en aplicaciones empresariales para plataformas móviles. Este tutorial muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a un usuario de aplicaciones determinado en un dispositivo concreto. Un backend de ASP.NET WebAPI se usa para autenticar clientes y generar notificaciones, tal como se muestra en el tema de referencia [Registro desde el backend de la aplicación](http://msdn.microsoft.com/en-us/library/dn743807.aspx). Este tutorial se basa en el centro de notificaciones que creó en el tutorial **Introducción a los Centros de notificaciones**.

> [AZURE.NOTE] Este tutorial asume que ha creado y configurado el centro de notificaciones tal como se describe en [Introducción a los Centros de notificaciones (Android)](/en-us/documentation/articles/notification-hubs-android-get-started/). 
> Si usa Servicios móviles como su servicio backend, consulte la [versión de Servicios móviles](/en-us/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/) de este tutorial.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Creación del proyecto Android

El paso siguiente es crear la aplicación Android.

1. Siga el tutorial [Introducción a los Centros de notificaciones (Android)](/en-us/documentation/articles/notification-hubs-android-get-started/) para crear y configurar la aplicación para recibir notificaciones de inserción desde GCM.

2. Abra el archivo res/layout/activity_main.xml y sustituya el contenido por lo siguiente:
			
		<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		    xmlns:tools="http://schemas.android.com/tools"
		    android:layout_width="match_parent"
		    android:layout_height="match_parent"
		    android:paddingBottom="@dimen/activity_vertical_margin"
		    android:paddingLeft="@dimen/activity_horizontal_margin"
		    android:paddingRight="@dimen/activity_horizontal_margin"
		    android:paddingTop="@dimen/activity_vertical_margin"
		    tools:context="com.example.notifyusers.MainActivity"
		    android:orientation="vertical">
		    <EditText
		        android:id="@+id/usernameText"
		        android:layout_width="match_parent"
		        android:layout_height="wrap_content"
		        android:ems="10"
		        android:hint="@string/usernameHint" >
		    </EditText>
		    <EditText
		        android:id="@+id/passwordText"
		        android:layout_width="match_parent"
		        android:layout_height="wrap_content"
		        android:ems="10"
		        android:hint="@string/passwordHint"
		        android:inputType="textPassword" />
		    <Button
		        android:id="@+id/buttonLogin"
		        android:layout_width="wrap_content"
		        android:layout_height="wrap_content"
		        android:text="@string/loginButton"
		        android:onClick="login" />
		    <Button
		        android:id="@+id/buttonSend"
		        android:layout_width="wrap_content"
		        android:layout_height="wrap_content"
		        android:text="@string/sendButton"
		        android:onClick="sendPush" />
		</LinearLayout>

2. Abra el archivo res/values/strings.xml y agregue las líneas siguientes:

		<string name="usernameHint">Username</string>
	    <string name="passwordHint">Password</string>
	    <string name="loginButton">1. Log in</string>
	    <string name="sendButton">2. Send push</string>

	El diseño gráfico main_activity.xml ahora debe tener un aspecto como este:

	![][A1]

3. Ahora cree una clase **RegisterClient** en el mismo paquete que la clase **MainActivity**. Asegúrese de reemplazar `{backend endpoint}` por el extremo back-end obtenido en la sección anterior.

		import java.io.IOException;
		import java.io.UnsupportedEncodingException;
		import java.util.Set;
		
		import org.apache.http.HttpResponse;
		import org.apache.http.HttpStatus;
		import org.apache.http.client.ClientProtocolException;
		import org.apache.http.client.HttpClient;
		import org.apache.http.client.methods.HttpPost;
		import org.apache.http.client.methods.HttpPut;
		import org.apache.http.client.methods.HttpUriRequest;
		import org.apache.http.entity.StringEntity;
		import org.apache.http.impl.client.DefaultHttpClient;
		import org.apache.http.util.EntityUtils;
		import org.json.JSONArray;
		import org.json.JSONException;
		import org.json.JSONObject;
		
		import android.content.Context;
		import android.content.SharedPreferences;
		import android.util.Log;
		
		public class RegisterClient {
			private static final String PREFS_NAME = "ANHSettings";
			private static final String REGID_SETTING_NAME = "ANHRegistrationId";
			private static final String BACKEND_ENDPOINT = "{backend endpoint}/api/register";
			SharedPreferences settings;
			protected HttpClient httpClient;
			private String authorizationHeader;
		
			public RegisterClient(Context context) {
				super();
				this.settings = context.getSharedPreferences(PREFS_NAME, 0);
				httpClient =  new DefaultHttpClient();
			}
		
			public String getAuthorizationHeader() {
				return authorizationHeader;
			}
			
			public void setAuthorizationHeader(String authorizationHeader) {
				this.authorizationHeader = authorizationHeader;
			}
			
			public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
				String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);

				JSONObject deviceInfo = new JSONObject();
				deviceInfo.put("Platform", "gcm");
				deviceInfo.put("Handle", handle);
				deviceInfo.put("Tags", new JSONArray(tags));

				int statusCode = upsertRegistration(registrationId, deviceInfo);
				
				if (statusCode == HttpStatus.SC_OK) {
					return;
				} else if (statusCode == HttpStatus.SC_GONE){
					settings.edit().remove(REGID_SETTING_NAME).commit();
					registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
					statusCode = upsertRegistration(registrationId, deviceInfo);
					if (statusCode != HttpStatus.SC_OK) {
						Log.e("RegisterClient", "Error upserting registration: " + statusCode);
						throw new RuntimeException("Error upserting registration");
					}
				} else {
					Log.e("RegisterClient", "Error upserting registration: " + statusCode);
					throw new RuntimeException("Error upserting registration");
				}
			}

			private int upsertRegistration(String registrationId, JSONObject deviceInfo)
					throws UnsupportedEncodingException, IOException,
					ClientProtocolException {
				HttpPut request = new HttpPut(BACKEND_ENDPOINT+"/"+registrationId);
				request.setEntity(new StringEntity(deviceInfo.toString()));
				request.addHeader("Authorization", "Basic "+authorizationHeader);
				request.addHeader("Content-Type", "application/json");
				HttpResponse response = httpClient.execute(request);
				int statusCode = response.getStatusLine().getStatusCode();
				return statusCode;
			}

			private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
				if (settings.contains(REGID_SETTING_NAME))
					return settings.getString(REGID_SETTING_NAME, null);
				
				HttpUriRequest request = new HttpPost(BACKEND_ENDPOINT+"?handle="+handle);
				request.addHeader("Authorization", "Basic "+authorizationHeader);
				HttpResponse response = httpClient.execute(request);
				if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
					Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
					throw new RuntimeException("Error creating Notification Hubs registrationId");
				}
				String registrationId = EntityUtils.toString(response.getEntity());
				registrationId = registrationId.substring(1, registrationId.length()-1);
				
				settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();
				
				return registrationId;
			}
		}

	Este componente implementa las llamadas REST requeridas para ponerse en contacto con el back-end de la aplicación con la finalidad de registrar notificaciones de inserción. También almacena localmente los *registrationIds* creados por el Centro de notificaciones tal como se detalla en [Registro desde el backend de la aplicación](http://msdn.microsoft.com/en-us/library/dn743807.aspx). Tenga en cuenta que usa un token de autorización almacenado localmente cuando hace clic en el botón **Iniciar sesión y registrarse**.

4. En la clase **MainActivity**, quite los campos privados para **NotificationHub** y agregue un campo para **RegisterClient**:

		//private NotificationHub hub;
		private RegisterClient registerClient;
 
5. Luego, en el método **onCreate**, quite la inicialización del campo **hub** y el método **registerWithNotificationHubs**. Agregue después las siguientes líneas que inicializan una instancia de la clase **RegisterClient**. El método debe contener las siguientes líneas:

		@Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        
	        NotificationsManager.handleNotifications(this, SENDER_ID,
					MyHandler.class);
	        gcm = GoogleCloudMessaging.getInstance(this);
	        
	        registerClient = new RegisterClient(this);
	        
	        setContentView(R.layout.activity_main);
	    }

6. Después, agregue los siguientes métodos, asegurándose de reemplazar `{backend endpoint}` por el extremo back-end obtenido en la sección anterior.

	    @Override
	    protected void onStart() {
	    	super.onStart();
	    	Button sendPush = (Button) findViewById(R.id.buttonSend);
	        sendPush.setEnabled(false);
	    }
	
		public void login(View view) throws UnsupportedEncodingException {
	    	this.registerClient.setAuthorizationHeader(getAuthorizationHeader());
	    	
	    	final Context context = this;
	    	new AsyncTask<Object, Object, Object>() {
				@Override
				protected Object doInBackground(Object... params) {
					try {
						String regid = gcm.register(SENDER_ID);
				        registerClient.register(regid, new HashSet<String>());
					} catch (Exception e) {
						Log.e("MainActivity", "Failed to register - " + e.getMessage());
						return e;
					}
					return null;
				}
	
				protected void onPostExecute(Object result) {
					Button sendPush = (Button) findViewById(R.id.buttonSend);
			        sendPush.setEnabled(true);
					Toast.makeText(context, "Logged in and registered.",
							Toast.LENGTH_LONG).show();
				}
			}.execute(null, null, null);
	    }
	    
	    public void sendPush(View view) throws ClientProtocolException, IOException {
	    	new AsyncTask<Object, Object, Object>() {
				@Override
				protected Object doInBackground(Object... params) {
					try {
						HttpUriRequest request = new HttpPost("{backend endpoint}/api/notifications");
						request.addHeader("Authorization", "Basic "+getAuthorizationHeader());
						HttpResponse response = new DefaultHttpClient().execute(request);
						if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
							Log.e("MainActivity", "Error sending notification" + response.getStatusLine().getStatusCode());
							throw new RuntimeException("Error sending notification");
						}
					} catch (Exception e) {
						Log.e("MainActivity", "Failed to send notification - " + e.getMessage());
						return e;
					}
					return null;
				}
			}.execute(null, null, null);
	    }
	    
		private String getAuthorizationHeader() throws UnsupportedEncodingException {
			EditText username = (EditText) findViewById(R.id.usernameText);
	    	EditText password = (EditText) findViewById(R.id.passwordText);
	    	String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
	    	basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
	    	return basicAuthHeader;
		}

	La devolución de llamada para **Iniciar sesión** genera un token de autenticación básico en el nombre de usuario y contraseña de entrada (tenga en cuenta que esto representa cualquier token que usa el esquema de autenticación) y después usa `RegisterClient` para llamar al back-end. La devolución de llamada para **Enviar inserción** llama al back-end para activar una notificación segura en todos los dispositivos de este usuario. 

## Ejecución de la aplicación

Para ejecutar la aplicación, realice las siguientes tareas:

1. En Eclipse, ejecute la aplicación en un dispositivo Android físico o en el emulador.

2. En la interfaz de usuario de la aplicación Android, escriba un nombre de usuario y contraseña. Esta información puede ser cualquier cadena, pero deben tener el mismo valor.

3. En la interfaz de usuario de la aplicación Android, haga clic en **Iniciar sesión**. A continuación, haga clic en **Enviar inserción**.


[A1]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users1.PNG

<!--HONumber=35.1-->
