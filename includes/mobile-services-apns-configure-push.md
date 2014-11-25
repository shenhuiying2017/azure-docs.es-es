Una vez que haya registrado su aplicación con APNS y haya configurado su proyecto, debe configurar su servicio móvil para la integración con APNS.

1.  En Acceso a llaves, haga clic con el botón secundario en el nuevo certificado, haga clic en **Export**, utilice un nombre para el archivo QuickstartPusher, seleccione el formato **.p12** y, a continuación, haga clic en **Save**.

	![][ ]

Anote el nombre de archivo y la ubicación del certificado exportado.

> [WACOM.NOTE] Con este tutorial se crea un archivo QuickstartPusher.p12. El nombre del archivo y la ubicación pueden ser diferentes.

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

    ![][1]

2.  Haga clic en la pestaña **Push** y, a continuación, en **Upload**.

    ![][2]

    Se mostrará el cuadro de diálogo Upload Certificate.

3.  Haga clic en **File**, seleccione el archivo QuickstartPusher.p12 del certificado exportado y especifique la contraseña en **Password**. Asegúrese de que selecciona el modo correcto en **Mode**, haga clic en el icono de marca de verificación y, a continuación, haga clic en **Save**.

    ![][3]

    > [WACOM.NOTE] Este tutorial usa certificados de desarrollo.

El servicio móvil está configurado ahora para que funcione con APNS.



  [ ]: ./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-apns-configure-push/mobile-services-selection.png
  [2]: ./media/mobile-services-apns-configure-push/mobile-push-tab-ios.png
  [3]: ./media/mobile-services-apns-configure-push/mobile-push-tab-ios-upload.png
