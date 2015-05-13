## Crear una cuenta de Almacenamiento de Azure

Para usar Almacenamiento de Azure, necesitará una cuenta de almacenamiento. Siga estos pasos para crear una cuenta de almacenamiento. \(También puede crear una cuenta de almacenamiento usando la biblioteca de clientes de administración de servicios de Azure o la administración de servicios [API de REST]\).

1.  Inicie sesión en el [Portal de administración de Azure].

2.  En la parte inferior del panel de navegación, haga clic en **NUEVO**.

	![\+nuevo][plus-new]

3.  Haga clic en **SERVICIOS DE DATOS**, en **ALMACENAMIENTO** y, a continuación, en **CREACIÓN RÁPIDA**.

	![Cuadro de diálogo de creación rápida][quick-create-storage]

4.  En la URL, escriba el nombre de subdominio que vaya usar en el URI para la cuenta de almacenamiento. Esta entrada puede contener de 3 a 24 letras minúsculas y números. Este valor se convierte en el nombre del host dentro del URI que se usó para direccionar los recursos Blob, Cola o Tabla de la suscripción.

5.  Seleccione un valor de Región/grupo de afinidad en el que ubicar el almacenamiento. Si va a usar el almacenamiento desde la aplicación de Azure, seleccione la misma región en la que implementará la aplicación.

6. Puede seleccionar si lo desea el tipo de replicación de su cuenta de almacenamiento. La replicación con redundancia geográfica está seleccionada de manera predeterminada y ofrece la durabilidad máxima. Para obtener más información sobre las opciones de replicación, consulte [Opciones de redundancia del Almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx) y el [Blog del equipo de Almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/).

6.  Haga clic en **CREAR CUENTA DE ALMACENAMIENTO**.

[API de REST]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Portal de administración de Azure]: http://manage.windowsazure.com
[plus-new]: ./media/storage-create-account-include/plus-new.png
[quick-create-storage]: ./media/storage-create-account-include/quick-storage-2.png

<!--HONumber=52-->
