1.  Abra el Administrador de SDK de Android haciendo clic en **Windows** en la barra de herramientas superior de Eclipse. Busque la versión de destino del SDK de Android que se especifica en las propiedades del proyecto, ábralo y seleccione **Google APIs**.

2.  Desplácese hacia abajo hasta **Extras**, expandálo y seleccione **Google Play Services**, como se muestra a continuación. Haga clic en **Install Packages**. Tome nota de la ruta de acceso del SDK para usarla en el paso siguiente. Reinicie Eclipse.

    ![][0]

3.  Instale el SDK de Google Play Services en su proyecto. En Eclipse, haga clic en **File** y, a continuación, haga clic en **Import**. Seleccione **Android**, luego **Existing Android Code into Workspace** y haga clic en **Next**. Haga clic en **Browse**, vaya a la ruta de acceso del SDK de Android (por lo general, en una carpeta llamada `adt-bundle-windows-x86_64`) y, a continuación, a la subcarpeta `\extras\google\google_play_services\libproject`; seleccione la carpeta google-play-services-lib y haga clic en **Aceptar**. Marque la casilla de verificación **Copy projects into workspace** y, a continuación, haga clic en **Finish**.

    ![][1]

4.  A continuación, debe hacer referencia la biblioteca de SDK de Google Play Services que acaba de importar, desde el proyecto.

5.  En el **Explorador de paquetes**, haga clic con el botón secundario en el proyecto y seleccione *Properties*.

6.  En la ventana Properties, seleccione Android a la izquierda.

    ![][2]

7.  En **Destino de compilación del proyecto**, asegúrese de que `Google APIs x86` (o `Google APIs`, dependiendo de su plataforma de desarrollo) está seleccionado para el nivel de SDK apropiado.

8.  En la sección de **Library**, elija **Add**, seleccione el proyecto Google Play Services (*google-play-services-lib*) y haga clic en **OK**.

9.  Haga clic en **Apply** y, a continuación, en **OK**.

  [0]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
  [1]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png
  [2]: ./media/mobile-services-android-get-started-push/mobile-google-set-project-properties.png
