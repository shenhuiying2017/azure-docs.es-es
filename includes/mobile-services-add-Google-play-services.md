

1. Abra el Administrador de SDK de Android haciendo clic en **Window** en la barra de herramientas superior de Eclipse. Busque la versión de destino del SDK de Android que se especifica en las propiedades del proyecto, ábralo y seleccione **Google APIs** (API de Google).

2. Desplácese hacia abajo hasta **Extras**, expándalo y seleccione **Google Play Services**, como se muestra a continuación. Haga clic en **Install Packages**. Tome nota de la ruta de acceso del SDK para usarla en el paso siguiente. Reinicie Eclipse.

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. Instale el SDK de Google Play Services en su proyecto. En Eclipse, haga clic en **File** y, a continuación, haga clic en **Import**. Seleccione **Android**, luego **Existing Android Code into Workspace** y haga clic en **Next**. Haga clic en **Browse**, vaya a la ruta del SDK de Android (normalmente una carpeta llamada `adt-bundle-windows-x86_64` inside the folder that contains Eclipse), then go to the `\extras\google\google_play_services\libprojectsubcarpeta `, seleccione la carpeta google-play-services-lib y haga clic en **OK**. Marque la casilla **Copy projects into workspace** (Copiar proyectos en el área de trabajo) y, a continuación, haga clic en **Finish** (Finalizar).

	![](./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png)

4. A continuación, debe hacer referencia la biblioteca de SDK de Google Play Services que acaba de importar, desde el proyecto. 

5. En el **Explorador de paquetes**, haga clic con el botón secundario en el proyecto y seleccione  *Properties*.
 
6. En la ventana Properties, seleccione Android a la izquierda.

	![](./media/mobile-services-android-get-started-push/mobile-google-set-project-properties.png)


7. En **Project Build Target**, asegúrese de que se selecciona  `Google APIs x86` (or `Google APIs`, según su plataforma de desarrollo) para el nivel de SDK apropiado.

 
8. En la sección **Library**, elija **Add** , seleccione el proyecto de Google Play Services (*google-play-services-lib*) y haga clic en **OK**.

9. Haga clic en **Apply** (Aplicar) y, a continuación, en **Aceptar**.



<!--HONumber=42-->
