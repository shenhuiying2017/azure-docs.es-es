1. Abra el Administrador de SDK de Android haciendo clic en el icono de la barra de herramientas de Android Studio o haciendo clic en **Herramientas** -> **Android** -> **Administrador de SDK** en el menú. Busque la versión de destino del SDK de Android que se utiliza en el proyecto, ábralo y elija **API de Google**, en caso de que no se haya instalado todavía.

2. Haga clic en la pestaña **SDK Tools** (Herramientas de SDK). Si no ha instalado todavía el servicio Google Play, haga clic en **Google Play Services** (Servicios de Google Play), tal como se muestra a continuación. A continuación, haga clic en **Apply** (Aplicar) para instalarlo.
 
	Tome nota de la ruta de acceso del SDK para usarla en el paso siguiente.

   	![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)


3. Abra el archivo **build.gradle** en el directorio de aplicaciones.

	![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)

4. Agregue esta línea bajo *android*

		useLibrary 'org.apache.http.legacy'

5. Agregue esta línea en *dependencias*:

   		compile 'com.google.android.gms:play-services-base:6.5.87'

7. En *defaultConfig*, cambie *minSdkVersion* a 9.
 
8. Haga clic en el botón **Sincronizar proyecto con archivos de Gradle** en la barra de herramientas.

9. Abra **AndroidManifest.xml** y agregue esta etiqueta a la etiqueta *application*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=Nov15_HO1-->