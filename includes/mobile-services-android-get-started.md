La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1. Vaya a la ubicación donde guardó los archivos del proyecto comprimidos y expanda los archivos del equipo en el directorio de proyectos de Android Studio.

2. Abra Android Studio. Si está trabajando con un proyecto y aparece, cierre el proyecto (Archivo => Cerrar proyecto).

3. Seleccione **Abrir un proyecto de Android Studio existente**, vaya a la ubicación del proyecto y, a continuación, haga clic en **Aceptar.** 

 	![](./media/mobile-services-android-get-started/android-studio-import-project.png)

4. En la ventana izquierda del **Explorador de proyectos**, asegúrese de que la pestaña  *Project* está seleccionada y, a continuación, abra **app**, **src**, **java** y haga doble clic en **ToDoactivity**,

   ![](./media/mobile-services-android-get-started/Android-Studio-quickstart.png)


5. Si ha descargado la versión 2.0 del SDK, deberá actualizar el código con la dirección URL y la clave del servicio móvil:
	- 	Busque el método **OnCreate** en **TodoActivity.java** y localice el código que crea una instancia del cliente de servicios móviles. El código se puede ver en la imagen anterior.
	- 	Reemplace "MobileServiceUrl" con la dirección URL real del servicio móvil.
	- 	Reemplace "AppKey" con la clave del servicio móvil.
	- 	Para obtener más información, consulte el tutorial [Incorporación de Servicios móviles a una aplicación existente](../articles/mobile-services-android-get-started-data.md). 

6. En el menú **Run** (Ejecutar), haga clic en **Run** (Ejecutar) para iniciar el proyecto en el emulador de Android.

	> [AZURE.IMPORTANT] Para poder ejecutar el proyecto en el emulador de Android, debe definir al menos un dispositivo virtual de Android (AVD). Use el administrador AVD para crear y administrar estos dispositivos.

7. En la aplicación, escriba un texto significativo, como "Realice el tutorial" y, a continuación, haga clic en **Agregar**.

   ![][10]

   	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

	> [AZURE.NOTE] Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo ToDoActivity.java.

8. De nuevo en el Portal de administración, haga clic en la pestaña **Datos** y luego haga clic en la tabla **TodoItems**.

   ![](./media/mobile-services-android-get-started/mobile-data-tab1.png)

   	Esto le permite examinar los datos que la aplicación inserta en la tabla.

   ![](./media/mobile-services-android-get-started/mobile-data-browse.png)


<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-android-get-started/Android-Studio-quickstart.png
[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png
[14]: ./media/mobile-services-android-get-started/android-studio-import-project.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Incorporación de Servicios móviles a una aplicación existente]: ../articles/mobile-services-android-get-started-data.md
[Introducción a la autenticación]: ../articles/mobile-services-android-get-started-users.md
[Introducción a las notificaciones de inserción]: ../articles/mobile-services-javascript-backend-android-get-started-push.md
[SDK de Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[SDK de Android para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portal de administración]: https://manage.windowsazure.com/

<!--HONumber=52-->