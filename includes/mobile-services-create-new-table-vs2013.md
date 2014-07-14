Para poder almacenar datos en su nuevo servicio móvil, debe crear antes una tabla de almacenamiento en el servicio. Estos pasos muestran cómo utilizar Visual Studio 2013 para crear una tabla en el servicio móvil. A continuación, actualizará la aplicación para usar los Servicios móviles a fin de almacenar elementos en Azure en lugar de en la colección local.

1.  En el Explorador de servidores, expanda **Servicios móviles de Azure**, haga clic con el botón secundario en el servicio móvil, haga clic en **Crear tabla** y, a continuación, escriba `TodoItem` en **Nombre de la tabla**.
    
    ![crear tabla in VS
    2013](./media/mobile-services-create-new-table-vs2013/mobile-create-table-vs2013.png)

2.  Expanda **Advanced**, verifique que los permisos de funcionamiento de la tabla estén establecidos de forma predeterminada en **Anybody with the Application Key** y, a continuación, haga clic en **Crear**.
    
    ![crear tabla en VS 2013 parte 2](./media/mobile-services-create-new-table-vs2013/mobile-create-table-vs2013-2.png)
    
    Esto crea la tabla TodoItem en el servidor, donde cualquiera que tenga la clave de aplicación puede tener acceso a los datos de la tabla y modificarlos sin necesidad de autenticarse antes.

	<div class="dev-callout">

	<b>Nota:</b>
	<p>La clave de aplicación se distribuye con la aplicación. Puesto que
	esta clave no se distribuye de forma segura, no se puede considerar
	un token de seguridad. Para proteger el acceso a los datos de su
	servicio móvil, los usuarios se deben autenticar antes del acceso.
	Para obtener más información, consulte <a href="http://msdn.microsoft.com/en-us/library/windowsazure/jj193161.aspx">Permisos</a>.</p>
</div>



