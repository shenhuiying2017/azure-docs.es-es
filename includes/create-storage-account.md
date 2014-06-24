Necesita una cuenta de almacenamiento de Azure para usar operaciones de
almacenamiento. Siga estos pasos para crear una cuenta de
almacenamiento. También puede crear una cuenta de almacenamiento [usando
la API de REST][1].

1.  Inicie sesión en el [Portal de administración de Azure][2].

2.  En la parte inferior del panel de navegación, haga clic en **NEW**.
    
    ![+new](./media/create-storage-account/plus-new.png)

3.  Haga clic en **DATA SERVICES**, en **STORAGE** y, a continuación, en
    **QUICK CREATE**.
    
    ![Cuadro de di&aacute;logo Quick
    create](./media/create-storage-account/quick-storage-2.png)

4.  En URL, escriba el nombre de subdominio que vaya usar en el URI para
    la cuenta de almacenamiento. Esta entrada puede contener de 3 a 24
    letras minúsculas y números. Este valor se convierte en el nombre
    del host dentro del URI que se ha usado para direccionar los
    recursos Blob, Cola o Tabla de la suscripción.

5.  Seleccione un grupo de región/afinidad en el que ubicar el
    almacenamiento. Si va a usar el almacenamiento desde la aplicación
    de Azure, seleccione la misma región en la que implementará la
    aplicación.

6.  También puede habilitar la replicación geográfica.

7.  Haga clic en **CREATE STORAGE ACCOUNT**.



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx
[2]: http://manage.windowsazure.com
