## Creación de una cuenta de almacenamiento de Azure

Para usar Almacenamiento de Azure, necesitará una suscripción a Azure. Siga 
estos pasos para crear una cuenta de almacenamiento. (También puede
crear una cuenta de almacenamiento utilizando la biblioteca de clientes de administración de servicios de Azure o la administración de servicios [REST API]).

1.  Inicie sesión en el [Portal de administración de Azure].

2.  En la parte inferior del panel de navegación, haga clic en **NEW**.

	![+new][plus-new]

3.  Haga clic en **DATA SERVICES**, en **STORAGE** y, a continuación, en **QUICK CREATE**.

	![Quick create dialog][quick-create-storage]

4.  En URL, escriba el nombre de subdominio que vaya usar en el URI para la
    cuenta de almacenamiento. La entrada puede contener de 3 a 24 letras minúsculas
    y números. Este valor se convierte en el URI que es el nombre de host
    que se usa para tratar recursos de Blob, Cola o Tabla para la
    suscripción.

5.  Seleccione una región o un grupo de afinidad donde ubicar el
    almacenamiento. Si va a usar almacenamiento de su aplicación de Azure,
    seleccione la misma región donde implementará su
    .

6. Puede seleccionar si lo desea el tipo de replicación de su cuenta de almacenamiento. La replicación con redundancia geográfica está seleccionada de manera predeterminada y ofrece una durabilidad máxima. Para obtener más detalles sobre las opciones de replicación, consulte [Opciones de redundancia del Almacenamiento de Azure ](http://msdn.microsoft.com/library/azure/dn727290.aspx) y el [Blog del equipo de Almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/).

6.  Haga clic en **CREAR CUENTA DE ALMACENAMIENTO**.

[REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Portal de administración de Azure]: http://manage.windowsazure.com
[plus-new]: ./media/storage-create-account-include/plus-new.png
[quick-create-storage]: ./media/storage-create-account-include/quick-storage-2.png

<!--HONumber=49-->