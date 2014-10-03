Para usar Almacenamiento de Azure, necesitará una suscripción a Azure. Siga
estos pasos para crear una cuenta de almacenamiento. También puede
crear una cuenta de almacenamiento [usando la API de REST][].

1.  Inicie sesión en el [Portal de administración de Azure][].

2.  En la parte inferior del panel de navegación, haga clic en **NEW**.

    ![+new][]

3.  Haga clic en **DATA SERVICES**, en **STORAGE** y, a continuación, en **QUICK CREATE**.

    ![Cuadro de diálogo de creación rápida][]

4.  En URL, escriba el nombre de subdominio que vaya usar en el URI para la
    cuenta de almacenamiento. Esta entrada puede contener de 3 a 24 letras minúsculas
    y números. Este valor se convierte en el nombre del host dentro del URI que se ha
    usado para direccionar los recursos Blob, Cola o Tabla de la
    suscripción.

5.  Seleccione un grupo de región/afinidad en el que ubicar el
    almacenamiento. Si va a usar el almacenamiento desde la aplicación de
    Azure, seleccione la misma región en la que implementará la
    aplicación.

6.  Puede seleccionar si lo desea el tipo de replicación de su cuenta de almacenamiento. La replicación con redundancia geográfica está seleccionada de manera predeterminada y ofrece una durabilidad máxima. Para obtener más detalles acerca de las opciones de replicación, consulte [Opciones de redundancia del Almacenamiento de Azure][] y el [Blog del equipo de Almacenamiento de Azure][].

7.  Haga clic en **CREATE STORAGE ACCOUNT**.

  [usando la API de REST]: http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [+new]: ./media/create-storage-account/plus-new.png
  [Cuadro de diálogo de creación rápida]: ./media/create-storage-account/quick-storage-2.png
  [Opciones de redundancia del Almacenamiento de Azure]: http://msdn.microsoft.com/en-us/library/azure/dn727290.aspx
  [Blog del equipo de Almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
