<properties
	pageTitle="Introducción a Azure Mobile Engagement"
	description="Aprenda a usar Azure Mobile Engagement con los análisis y las notificaciones de inserción para aplicaciones Android."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="get-started-article" 
	ms.date="05/01/2015"
	ms.author="piyushjo" />

# Introducción a Azure Mobile Engagement para Android Apps

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

En este tema se muestra cómo usar Azure Mobile Engagement para comprender el uso de su aplicación y enviar notificaciones de inserción a usuarios segmentados de una aplicación de Android. En este tutorial se demuestra el escenario de difusión sencillo con Mobile Engagement. En él, creará una aplicación en blanco de Android que recopila datos básicos y recibe notificaciones de inserción con el Servicio de mensajería en la nube de Google (GCM). Cuando haya terminado, podrá difundir notificaciones de inserción a todos los dispositivos o dirigirse a usuarios específicos en función de las propiedades de sus dispositivos. Asegúrese de seguir el tutorial siguiente para ver cómo usar Mobile Engagement para dirigirse a usuarios y grupos de dispositivos específicos.


Este tutorial requiere lo siguiente:

+ el SDK de Android (se supone que va usará Studio Android), que puede descargar [aquí](http://go.microsoft.com/fwlink/?LinkId=389797)
+ el [SDK de Android para Mobile Engagement Android]

> [AZURE.IMPORTANT]Completar este tutorial es un requisito previo para los demás tutoriales de Mobile Engagement para aplicaciones de Android. Para completarlo, debe tener una cuenta activa de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure</a>.

<!--
##<a id="register"></a>Enable Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

You will use your GCM API key later when setting up your app for Mobile Engagement.
-->

##<a id="setup-azme"></a>Configurar Mobile Engagement para su aplicación

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com) y, luego, haga clic en **+NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, en **Mobile Engagement** y luego en **Crear**.

   	![][7]

3. En el menú emergente que aparece, escriba la siguiente información:

   	![][8]

	1. **Nombre de la aplicación**: puede escribir el nombre de la aplicación. Puede usar cualquier carácter.
	2. **Plataforma**: seleccione la plataforma de destino de dicha aplicación (si la aplicación tiene como destino varias plataformas, repita este tutorial para cada plataforma).
	3. **Nombre del recurso de la aplicación**: este es el nombre por el que la aplicación será accesible a través de las API y las direcciones URL. Le recomendamos que use solo caracteres convencionales de dirección URL: el nombre que se genera automáticamente debería proporcionar una sugerencia sólida. También recomendamos anexar el nombre de la plataforma para evitar cualquier conflicto de nombres, ya que este debe ser único.
	4. **Localización**: elija el centro de datos donde se alojará esta aplicación y, más importante aún, su colección (consulte la siguiente información).
	5. **Colección**: si ya ha creado una aplicación, seleccione una colección creada anteriormente; en caso contrario, seleccione una colección nueva.
	6. **Nombre de la colección**: esto representa el grupo de aplicaciones. También garantizará que todas las aplicaciones se encuentren en un grupo que permitirá cálculos agregados. Recomendamos encarecidamente que use el nombre de la empresa o el departamento.


	Cuando haya terminado, haga clic en el botón de comprobación para finalizar la creación de la aplicación.

4. Ahora haga clic en la aplicación que acaba de crear (o selecciónela) en la pestaña **Aplicación**.

   	![][9]

5. A continuación, haga clic en **Información de conexión** para mostrar la configuración de la conexión que se debe incluir en la integración de SDK:

   	![][10]

6. Por último, anote la **Cadena de conexión**, que es lo que necesitará para identificar esta aplicación desde el código de aplicación.

   	![][11]

	>[AZURE.TIP]Para su comodidad, puede usar el icono "Copiar" a la derecha de la cadena de conexión para copiarlo en el Portapapeles.

##<a id="connecting-app"></a>Conectar la aplicación al back-end de Mobile Engagement

En este tutorial se presenta una "integración básica», que es el conjunto mínimo necesario para recopilar los datos y enviar una notificación de inserción. La documentación de integración completa se puede encontrar en la [documentación del SDK de Android para Mobile Engagement].

Crearemos una aplicación básica con Android Studio para demostrar la integración.

###Creación de un nuevo proyecto de Android

Puede omitir este paso si ya tiene una aplicación y está familiarizado con el desarrollo de Android.

1. Inicie Studio Android y, en el menú emergente, seleccione **Iniciar un nuevo proyecto de Android Studio**.

   	![][12]

2. Rellene el nombre de la aplicación y el dominio de la empresa. Anote estos datos, ya que los necesitará más adelante. Haga clic en **Siguiente**.

   	![][13]

3. Ahora seleccione el factor de forma de destino y el nivel de API, a continuación, haga clic en **Siguiente**.
	>[AZURE.NOTE]Compromiso de Mobile requiere el nivel de API mínimo de 10 (2.3.3 Android).

   	![][14]

4. Ahora agregaremos una actividad a nuestra aplicación simple que será su pantalla principal y única. Asegúrese de que **Actividad en blanco** esté seleccionada y haga clic en **Siguiente**.

   	![][15]

5. En la pantalla final del asistente, puede dejarlo todo para los fines de este tutorial y hacer clic en **Finalizar**.

   	![][16]

Ahora Android Studio creará la aplicación de demostración que a la que integraremos Mobile Engagement.

###Incluir la biblioteca de SDK en el proyecto

Descargar e integrar la biblioteca de SDK

1. Descargue el [SDK de Android para Mobile Engagement].
2. Extraiga el archivo a una carpeta en el equipo.
3. Identifique la biblioteca .jar para la versión actual de este SDK y cópiela al Portapapeles.

	![][17]

4. Vaya a la sección Proyecto (1) y pegue el .jar en la carpeta libs (2).

	![][18]

5. Sincronice el proyecto para cargar la biblioteca.

	![][19]


###Conectar la aplicación al back-end de Mobile Engagement mediante la cadena de conexión

1. Copie las siguientes líneas de código en la creación de la actividad (debe realizarse solo en un lugar de la aplicación, normalmente la actividad principal).

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Vuelva al portal de Azure en la página **Información de conexión** de la aplicación y copie la **cadena de conexión**.

	![][11]

3. Péguela en el parámetro `setConnectionString` que reemplaza el ejemplo proporcionado, tal como se muestra a continuación (los elementos AppId y Sdkkey están ocultos).

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

4. EngagementConfiguration y EngagementAgent probablemente se mostrarán como no resueltos (en rojo en el código). Haga clic en cada una de las clases sin resolver y presione ALT+ENTRAR para resolverlas automáticamente.

	![][20]

###Agregar permisos y declaración de servicio

1. Agregue estos permisos al archivo Manifest.xml del proyecto inmediatamente anterior a la etiqueta `<application>`:

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

	El resultado debería ser como el mostrado a continuación:

	![][21]

2. Agregue lo siguiente entre las etiquetas `<application>` y `</application>` para declarar el servicio del agente:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

3. En el código que acaba de pegar, reemplace "`"<Your application name>"`" en la etiqueta. Esto es lo que se muestra en el menú de configuración, donde el usuario puede ver los servicios que se están ejecutando en el dispositivo. Puede agregar la palabra "Servicio" a la etiqueta, por ejemplo.

###Enviar una pantalla a Mobile Engagement

Para comenzar a enviar datos y asegurarse de que los usuarios estén activos, debe enviar al menos una pantalla (Actividad) al back-end de Mobile Engagement. Lo conseguiremos hacer esto mediante la creación de subclases de nuestra actividad con el elemento EngagementActivity que proporciona nuestro SDK. Para ello, reemplace la superclase de MainActivity, que precede a ActionBarActivity, con EngagementActivity, tal como se muestra a continuación:

![][22]

>[AZURE.NOTE]No olvide resolver la clase si aparece en rojo, haciendo clic en ella y presionando ALT+ENTRAR.

##<a id="monitor"></a>Cómo comprobar que la aplicación está conectada con la supervisión en tiempo real

En esta sección se muestra cómo asegurarse de que la aplicación se conecta al back-end de Mobile Engagement mediante la característica de supervisión en tiempo real de Mobile Engagement.

1. Desplácese hasta el portal de Mobile Engagement.

	En el portal de Azure, asegúrese de que se encuentra en la aplicación que estamos utilizando para este proyecto y, a continuación, haga clic en el botón **Interactuar** en la parte inferior:

	![][26]

2. Será dirigido a la página de configuración del portal de Engagement de la aplicación. Desde allí, haga clic en la pestaña **Monitor**, tal como se muestra a continuación.![][30]

3. El monitor está listo para mostrar en tiempo real cualquier dispositivo que inicie la aplicación. ![][31]

4. De nuevo en Android Studio, inicie la aplicación en el monitor o en un dispositivo conectado haciendo clic en el triángulo verde y, a continuación, seleccione el dispositivo. ![][32]

5. Si funcionó, ahora debería ver una sesión en el monitor. ![][33]

**¡Enhorabuena!** Ha realizado correctamente el primer paso de este tutorial con una aplicación que se conecta con el back-end de Mobile Engagement, que ya está enviando datos.


##<a id="integrate-push"></a>Habilitación de las notificaciones de inserción y mensajería en la aplicación

Mobile Engagement permite interactuar y llegar a los usuarios mediante notificaciones de inserción y mensajería en la aplicación en el contexto de las campañas. Este módulo se denomina REACH en el portal de Mobile Engagement. En las secciones siguientes se instalará la aplicación para recibirlos.

### Habilitación de mensajería en la aplicación

1. Copie los recursos de mensajería en la aplicación siguientes en su Manifest.xml entre las etiquetas `<application>` y `</application>`.

		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
  			<intent-filter>
    			<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
    			<category android:name="android.intent.category.DEFAULT" />
    			<data android:mimeType="text/plain" />
  			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
				<category android:name="android.intent.category.DEFAULT" />
				<data android:mimeType="text/html" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
				<category android:name="android.intent.category.DEFAULT" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
				<category android:name="android.intent.category.DEFAULT"/>
			</intent-filter>
		</activity>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
			<intent-filter>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
			</intent-filter>
		</receiver>

2. Copie los recursos en el proyecto siguiendo estos pasos:
	1. Vuelva al contenido de descarga del SDK y abra la carpeta 'res'.

		![][23]

	2. Vuelva a Android Studio, seleccione el directorio 'main' de los archivos de proyecto y pegue los recursos para agregarlos al proyecto.

		![][24]

###Especificación de un icono para las notificaciones

El código siguiente define el icono que se usa para mostrar las notificaciones tanto de la aplicación y como del sistema.

Aunque es opcional para las notificaciones de la aplicación, es obligatorio para las notificaciones del sistema. Android rechaza las notificaciones del sistema con iconos no válidos.

Este fragmento xml se debe pegar en el Manifest.xml entre las etiquetas `<application>` y `</application>`.

Asegúrese de que está usando un icono que se encuentra en una de las carpetas de recursos **dibujables** (como ``engagement_close.png``). No se admiten carpetas de recursos **mipmap**.
	
		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

Es solo un ejemplo para mostrar la sintaxis; por supuesto, debe usar un icono adecuado para notificaciones según las [directrices de diseño de Android](http://developer.android.com/design/patterns/notifications.html).

No debe usar el icono del selector; tiene una resolución diferente y normalmente está en las carpetas mipmap, que no se admiten.

>[AZURE.TIP]Para asegurarse de usar las resoluciones de icono correctas, consulte [esos ejemplos](https://www.google.com/design/icons). Vaya a la sección *Notificación*, haga clic en un icono y haga clic en `PNGS` para descargar el conjunto de iconos dibujables. Verá qué carpetas de recursos dibujables puede usar con qué resolución para cada versión del icono.

###Habilitar la aplicación para recibir notificaciones de inserción de GCM

1. Escriba sus metadatos de gcm:remitente copiando y pegando lo siguiente en el Manifest.xml entre las etiquetas `<application>` y `</application>`. El valor oculto siguiente (con estrellas) es el `project number` obtenido de la consola de Google Play. \n se introduce de manera intencionada para asegurarse de finalizar el número de proyecto con él.

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Pegue el código siguiente en su Manifest.xml entre las etiquetas `<application>` y `</application>`. Reemplace el nombre del paquete <Your package name>.

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
		android:exported="false">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			</intent-filter>
		</receiver>

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			<intent-filter>
				<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
				<action android:name="com.google.android.c2dm.intent.RECEIVE" />
				<category android:name="<Your package name>" />
			</intent-filter>
		</receiver>

3. Agregue el último conjunto de permisos resaltado antes de la etiqueta `<application>`. Reemplace `<Your package name>` por el nombre real del paquete de la aplicación.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
		<permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Conceder acceso a la clave de API de GCM para Mobile Engagement

Para permitir que Mobile Engagement envíe notificaciones de inserción en su nombre, deberá conceder acceso a la clave de API. Esto se hace configurando y escribiendo la clave en el portal de Mobile Engagement.

1. Acceda al portal de Mobile Engagement.

	En el portal de Azure, asegúrese de que se encuentra en la aplicación que estamos utilizando para este proyecto y, a continuación, haga clic en el botón **Interactuar** en la parte inferior:

	![][26]

2. Ahora estará en la página de configuración del portal de Engagement. Desde allí, haga clic en la sección **Inserción nativa** para especificar la clave de GCM: ![][27]

3. Haga clic en el icono de edición delante de **Clave de API** en la sección Configuración de GCM tal como se muestra a continuación: ![][28]

4. En el menú emergente, pegue la clave de servidor de GCM obtenida en la sección [Habilitación de la mensajería en la nube de Google](#register) y, a continuación, haga clic en **Aceptar**.

	![][29]

Y está listo. Ahora comprobaremos que realizó correctamente la integración básica.

> [AZURE.IMPORTANT]Asegúrese de crear, iniciar con este código nuevo, salir de la aplicación y esperar aproximadamente un minuto antes de realizar lo siguiente.

##<a id="send"></a>Cómo enviar una notificación a su aplicación

Ahora crearemos una campaña de notificación de inserción simple que enviará una notificación de inserción a nuestra aplicación.

1. Vaya a la pestaña **COBERTURA** en el portal de Mobile Engagement.![][34]

2. Haga clic en **Nuevo anuncio** para crear la campaña de inserción. ![][35]

3. Configure el primer campo de la campaña mediante los pasos siguientes:![][36]

	1. Asigne el nombre deseado a la campaña.
	2. Seleccione el **Tipo de entrega** como *Notificación del sistema / Simple*: este es el tipo de notificación de inserción simple de Android que incluye un título y una pequeña línea de texto.
	3. Seleccione **Tiempo de entrega** como *Cualquier momento* para permitir que la aplicación reciba una notificación, independientemente de si se ha iniciado o no.
	4. En el texto de la notificación, escriba el título que aparecerá en negrita en la inserción.
	5. Luego, escriba el mensaje.

4. Desplácese hacia abajo. En la sección del contenido, elija **Solo notificación**. ![][37]

5. Ha terminado de configurar la campaña más básica posible. Ahora, desplácese hacia abajo de nuevo y cree la campaña para guardarla. ![][38]

6. Último paso: active la campaña. ![][39]


<!-- URLs. -->
[SDK de Android para Mobile Engagement]: http://go.microsoft.com/?linkid=9863935
[SDK de Android para Mobile Engagement Android]: http://go.microsoft.com/?linkid=9863935
[documentación del SDK de Android para Mobile Engagement]: http://go.microsoft.com/?linkid=9874682
<!-- Images. -->
[7]: ./media/mobile-engagement-android-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-android-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-android-get-started/select-app.png
[10]: ./media/mobile-engagement-android-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[13]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[14]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[15]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[16]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[17]: ./media/mobile-engagement-android-get-started/sdk-content.png
[18]: ./media/mobile-engagement-android-get-started/paste-jar.png
[19]: ./media/mobile-engagement-android-get-started/sync-project.png
[20]: ./media/mobile-engagement-android-get-started/resolve-classes.png
[21]: ./media/mobile-engagement-android-get-started/permissions.png
[22]: ./media/mobile-engagement-android-get-started/subclass-activity.png
[23]: ./media/mobile-engagement-android-get-started/copy-resources.png
[24]: ./media/mobile-engagement-android-get-started/paste-resources.png
[26]: ./media/mobile-engagement-android-get-started/engage-button.png
[27]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-android-get-started/api-key.png
[30]: ./media/mobile-engagement-android-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-android-get-started/monitor.png
[32]: ./media/mobile-engagement-android-get-started/launch.png
[33]: ./media/mobile-engagement-android-get-started/monitor-trafic.png
[34]: ./media/mobile-engagement-android-get-started/reach-tab.png
[35]: ./media/mobile-engagement-android-get-started/new-announcement.png
[36]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-android-get-started/campaign-content.png
[38]: ./media/mobile-engagement-android-get-started/campaign-create.png
[39]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!---HONumber=July15_HO4-->