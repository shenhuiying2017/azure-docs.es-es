1. Abra el Administrador de SDK de Android haciendo clic en el icono de la barra de herramientas de Android Studio o haciendo clic en **Herramientas** -> **Android** -> **SDK Manager** en el menú. Busque la versión de destino del SDK de Android que se usa en su proyecto, ábrala, para lo que debe hacer clic en **Show Package Details** (Mostrar detalles de paquete) y elija **Google APIs** (API de Google), en caso de que no esté instalado.
2. Haga clic en la pestaña **SDK Tools** (Herramientas del SDK). Si no ha instalado todavía el servicio Google Play, haga clic en **Google Play Services** (Servicios de Google Play), tal como se muestra a continuación. A continuación, haga clic en **Apply** (Aplicar) para instalarlo. 
   
    Tome nota de la ruta de acceso del SDK para usarla en el paso siguiente. 
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Abra el archivo **build.gradle** en el directorio de aplicaciones.
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. Agregue esta línea en *dependencias*: 
   
           compile 'com.google.android.gms:play-services-gcm:9.2.0'
5. Haga clic en el botón **Sincronizar proyecto con archivos de Gradle** en la barra de herramientas.
6. Abra **AndroidManifest.xml** y agregue esta etiqueta a la etiqueta *application* .
   
        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />

