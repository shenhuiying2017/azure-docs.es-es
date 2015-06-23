<properties 
	pageTitle="Tutorial de noticias de última hora en los Centros de notificaciones: Android" 
	description="Obtenga información acerca del uso de Centros de notificaciones del Bus de servicio de Azure para enviar notificaciones de noticias de última hora en dispositivos Android." 
	services="notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="ricksal"/>


# Uso de los Centros de notificaciones para enviar noticias de última hora
<div class="dev-center-tutorial-selector sublanding">     	
	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" >Windows Universal</a><a href="/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS">iOS</a>
	<a href="/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android" class="current">Android</a>
</div>

Este tema muestra cómo puede usar los Centros de notificaciones de Azure para difundir notificaciones de noticias de última hora en una aplicación Android. Cuando lo complete, podrá registrar las categorías de noticias de última hora en las que esté interesado y recibir solo notificaciones de inserción para esas categorías. Este escenario es un patrón común para muchas aplicaciones en las que las notificaciones tienen que enviarse a grupos de usuarios que han mostrado previamente interés en ellas, por ejemplo, lectores RSS, aplicaciones para aficionados a la música, etc. 

Los escenarios de difusión se habilitan mediante la inclusión de una o más _etiquetas_ cuando se crea un registro en el Centro de notificaciones. Cuando las notificaciones se envían a una etiqueta, todos los dispositivos registrados para la etiqueta recibirán la notificación. Puesto que las etiquetas son cadenas simples, no tendrán que aprovisionarse antes. Para obtener información sobre las etiquetas, consulte [Notification Hubs Guidance]. 

Este tutorial le guiará a través de estos pasos básicos para habilitar este escenario:

1. [Adición de una selección de categorías a la aplicación]
2. [Registro de notificaciones]
3. [Envío de notificaciones desde el back-end]
4. [Ejecución de la aplicación y generación de notificaciones]

Este tema se basa en la aplicación que creó en [Introducción a los Centros de notificaciones][get-started]. Antes de comenzar este tutorial, debe haber completado la [Introducción a los Centros de notificaciones][get-started].

##<a name="adding-categories"></a>Adición de una selección de categorías a la aplicación

El primer paso es agregar los elementos de la interfaz de usuario a la actividad principal existente que permiten al usuario seleccionar las categorías para registrar. Las categorías seleccionadas por un usuario se almacenan en el dispositivo. Cuando la aplicación se inicia, se crea un registro de dispositivos en el Centro de notificaciones con las categorías seleccionadas como etiquetas. 

1. Abra el archivo res/layout/activity_main.xml y sustituya el contenido por lo siguiente:
			
		<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		    xmlns:tools="http://schemas.android.com/tools"
		    android:layout_width="match_parent"
		    android:layout_height="match_parent"
		    android:paddingBottom="@dimen/activity_vertical_margin"
		    android:paddingLeft="@dimen/activity_horizontal_margin"
		    android:paddingRight="@dimen/activity_horizontal_margin"
		    android:paddingTop="@dimen/activity_vertical_margin"
		    tools:context="com.example.breakingnews.MainActivity"
		    android:orientation="vertical">
		
		        <CheckBox
		            android:id="@+id/worldBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_world" />
		        <CheckBox
		            android:id="@+id/politicsBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_politics" />
		        <CheckBox
		            android:id="@+id/businessBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_business" />
		        <CheckBox
		            android:id="@+id/technologyBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_technology" />
		        <CheckBox
		            android:id="@+id/scienceBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_science" />
		        <CheckBox
		            android:id="@+id/sportsBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_sports" />
			    <Button
			        android:layout_width="wrap_content"
			        android:layout_height="wrap_content"
			        android:onClick="subscribe"
			        android:text="@string/button_subscribe" />
		</LinearLayout>

2. Abra el archivo res/values/strings.xml y agregue las líneas siguientes:

	    <string name="button_subscribe">Subscribe</string>
	    <string name="label_world">World</string>
	    <string name="label_politics">Politics</string>
	    <string name="label_business">Business</string>
	    <string name="label_technology">Technology</string>
	    <string name="label_science">Science</string>
	    <string name="label_sports">Sports</string>

	El diseño gráfico main_activity.xml ahora debe tener un aspecto como este:

	![][A1]

3. Ahora cree una clase **Notifications** en el mismo paquete que la clase **MainActivity**.

		import java.util.HashSet;
		import java.util.Set;
		
		import android.content.Context;
		import android.content.SharedPreferences;
		import android.os.AsyncTask;
		import android.util.Log;
		import android.widget.Toast;
		
		import com.google.android.gms.gcm.GoogleCloudMessaging;
		import com.microsoft.windowsazure.messaging.NotificationHub;		
		
		public class Notifications {
			private static final String PREFS_NAME = "BreakingNewsCategories";
			private GoogleCloudMessaging gcm;
			private NotificationHub hub;
			private Context context;
			private String senderId;
			
			public Notifications(Context context, String senderId) {
				this.context = context;
				this.senderId = senderId;
				
				gcm = GoogleCloudMessaging.getInstance(context);
		        hub = new NotificationHub(<hub name>, <connection string with listen access>, context);
			}
			
			public void storeCategoriesAndSubscribe(Set<String> categories)
			{
				SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
			    settings.edit().putStringSet("categories", categories).commit();
			    subscribeToCategories(categories);
			}
			
			public void subscribeToCategories(final Set<String> categories) {
				new AsyncTask<Object, Object, Object>() {
					@Override
					protected Object doInBackground(Object... params) {
						try {
							String regid = gcm.register(senderId);
					        hub.register(regid, categories.toArray(new String[categories.size()]));
						} catch (Exception e) {
							Log.e("MainActivity", "Failed to register - " + e.getMessage());
							return e;
						}
						return null;
					}
		
					protected void onPostExecute(Object result) {
						String message = "Subscribed for categories: "
								+ categories.toString();
						Toast.makeText(context, message,
								Toast.LENGTH_LONG).show();
					}
				}.execute(null, null, null);
			}
			
		}

	Esta clase usa el almacenamiento local para almacenar las categorías de noticias que este dispositivo ha de recibir. También contiene métodos para registrar estas categorías.

4. En el código anterior, reemplace los marcadores de posición `<hub name>` y `<connection string with listen access>` por el nombre de su Centro de notificaciones y la cadena de conexión para *DefaultListenSharedAccessSignature* que obtuvo anteriormente.

	> [AZURE.NOTE] Puesto que las credenciales que se distribuyen con una aplicación de cliente no son normalmente seguras, solo debe distribuir la clave para el acceso de escucha con la aplicación cliente. El acceso de escucha permite a la aplicación el registro de notificaciones, pero los registros existentes no pueden modificarse y las notificaciones no se pueden enviar. La clave de acceso completo se usa en un servicio back-end protegido para el envío de notificaciones y el cambio de registros existentes.

4. En la clase **MainActivity**, elimine los campos privados para **NotificationHub** y **GoogleCloudMessaging** y agregue un campo para **Notifications**:

		// private GoogleCloudMessaging gcm;
		// private NotificationHub hub;
		private Notifications notifications;

5. Luego, en el método **onCreate**, quite la inicialización del campo **hub** y el método **registerWithNotificationHubs**. Agregue después las siguientes líneas que inicializan una instancia de la clase **Notifications**. El método debe contener las siguientes líneas:

		@Override
		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_main);
	
			NotificationsManager.handleNotifications(this, SENDER_ID,
					MyHandler.class);
	
			notifications = new Notifications(this, SENDER_ID);
		}

6. Luego, agregue el siguiente método:
	
	    public void subscribe(View sender) {
			final Set<String> categories = new HashSet<String>();
	
			CheckBox world = (CheckBox) findViewById(R.id.worldBox);
			if (world.isChecked())
				categories.add("world");
			CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
			if (politics.isChecked())
				categories.add("politics");
			CheckBox business = (CheckBox) findViewById(R.id.businessBox);
			if (business.isChecked())
				categories.add("business");
			CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
			if (technology.isChecked())
				categories.add("technology");
			CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
			if (science.isChecked())
				categories.add("science");
			CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
			if (sports.isChecked())
				categories.add("sports");
	
			notifications.storeCategoriesAndSubscribe(categories);
	    }
	
	Este método crea una lista de categorías y usa la clase **Notifications** para almacenar la lista en el almacenamiento local y registrar las etiquetas correspondientes en el centro de notificaciones. Cuando se modifican las categorías, el registro vuelve a crearse con las nuevas categorías.

La aplicación ahora puede almacenar un conjunto de categorías en el almacenamiento local en el dispositivo y registrarse en el Centro de notificaciones siempre que el usuario cambie la selección de categorías. 

##<a name="register"></a>Registro de notificaciones

Estos pasos permiten registrar el Centro de notificaciones en el inicio mediante las categorías que se han almacenado en el almacén local. 

> [AZURE.NOTE] Dado que el identificador de registro asignado por el servicio de mensajería en la nube de Google (GCM) puede cambiar en cualquier momento, debe registrarse para notificaciones frecuentemente para evitar errores de notificación. En este ejemplo se registra la notificación cada vez que se inicia la aplicación. En las aplicaciones que se ejecutan con frecuencia, más de una vez al día, es posible que pueda omitir el registro para conservar el ancho de banda si pasa menos de un día del registro previo.

1. Agregue el siguiente código a la clase **Notifications**:

		public Set<String> retrieveCategories() {
			SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
			return settings.getStringSet("categories", new HashSet<String>());
		}

	Esto devuelve las categorías definidas en la clase.

2. Ahora agregue este código al final del método **onCreate** en la clase **MainActivity**:

		notifications.subscribeToCategories(notifications.retrieveCategories());

	De esta forma, se garantiza que cada vez que la aplicación se inicia, se recuperan las categorías del almacenamiento local y se solicita un registro de estas categorías. El método **InitNotificationsAsync** se creó como parte del tutorial [Introducción a los Centros de notificaciones], pero no es necesario en este tema.

3. Luego agregue el siguiente método a **MainActivity**:

		@Override
		protected void onStart() {
			super.onStart();
			
			Set<String> categories = notifications.retrieveCategories();
			
			CheckBox world = (CheckBox) findViewById(R.id.worldBox);
			world.setChecked(categories.contains("world"));
			CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
			politics.setChecked(categories.contains("politics"));
			CheckBox business = (CheckBox) findViewById(R.id.businessBox);
			business.setChecked(categories.contains("business"));
			CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
			technology.setChecked(categories.contains("technology"));
			CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
			science.setChecked(categories.contains("science"));
			CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
			sports.setChecked(categories.contains("sports"));
		}

	De esta forma, se actualiza la actividad principal según el estado de las categorías guardadas anteriormente. 

La aplicación está ahora completa y puede almacenar un conjunto de categorías en el almacenamiento local del dispositivo usado para registrarse en el Centro de notificaciones cuando el usuario cambie la selección de categorías. A continuación, definiremos un back-end que pueda enviar notificaciones de categorías a esta aplicación.

<h2><a name="send"></a>Envío de notificaciones desde el back-end</h2>

[AZURE.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

##<a name="test-app"></a>Ejecución de la aplicación y generación de notificaciones

1. En Eclipse, cree la aplicación e iníciela en un dispositivo o emulador.
	
	Tenga en cuenta que la interfaz de usuario de la aplicación ofrece un conjunto de elementos de alternancia que le permite seleccionar las categorías a las que suscribirse. 

2. Habilite uno o más elementos de alternancia de las categorías y, a continuación, haga clic en **Subscribe**.

	La aplicación convierte las categorías seleccionadas en etiquetas y solicita un nuevo registro de dispositivo para las etiquetas seleccionadas al Centro de notificaciones. Las categorías registradas se devuelven y se muestran en un cuadro de diálogo.

4. Envíe una nueva notificación desde el back-end de alguna de las siguientes formas:

	+ **Aplicación de consola .NET:** inicie la aplicación de consola.

	+ **Java/PHP:** ejecución de la aplicación/script.

	Las notificaciones para las categorías seleccionadas aparecen como notificaciones del sistema.

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial hemos aprendido cómo difundir noticias de última hora por categoría. Considere la posibilidad de llevar a cabo uno de los siguientes tutoriales que destacan otros escenarios de Centros de notificaciones avanzados:

+ [Uso de los Centros de notificaciones para difundir noticias de última hora localizadas]

	Conozca cómo expandir la aplicación de noticias de última hora para habilitar el envío de notificaciones localizadas. 

+ [Notificación a los usuarios con los Centros de notificaciones]

	Conozca cómo insertar notificaciones para usuarios autenticados específicos. Esta es una buena solución para enviar notificaciones solo a usuarios específicos.


<!-- Anchors. -->
[Adición de una selección de categorías a la aplicación]: #adding-categories
[Registro de notificaciones]: #register
[Envío de notificaciones desde el back-end]: #send
[Ejecución de la aplicación y generación de notificaciones]: #test-app
[Pasos siguientes]: #next-steps

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-get-started.md
[Uso de los Centros de notificaciones para difundir noticias de última hora localizadas]: /manage/services/notification-hubs/breaking-news-localized-dotnet/ 
[Notificación a los usuarios con los Centros de notificaciones]: /manage/services/notification-hubs/notify-users
[Servicio móvil]: /develop/mobile/tutorials/get-started/
[Información general acerca de los Centros de notificaciones ]: http://msdn.microsoft.com/library/jj927170.aspx
[Procedimientos de los Centros de notificaciones para la Tienda Windows]: http://msdn.microsoft.com/library/jj927172.aspx
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Portal de administración de Azure]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591

<!--HONumber=49-->