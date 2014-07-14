A continuación, creará un nuevo servicio móvil para sustituir la lista en memoria del almacenamiento de datos. Siga los pasos siguientes para crear un servicio móvil nuevo.

1.  Inicie sesión en el [Portal de administración de Azure][1]. 
2.  En la parte inferior del panel de navegación, haga clic en **+NEW**.
    
    ![plus-new](./media/mobile-services-create-new-service-data/plus-new.png)

3.  Expanda **Proceso** y **Mobile Service** y, a continuación, haga clic en **Create**.
    
    ![mobile-create](./media/mobile-services-create-new-service-data/mobile-create.png)
    
    Se muestra el cuadro de diálogo **New Mobile Service**.

4.  En la página **Create a mobile service**, seleccione **Create a free 20 MB SQL Database**. A continuación, escriba el nombre de un subdominio para el nuevo servicio móvil en el cuadro de texto **URL** y espere la verificación del nombre. Cuando se haya verificado el nombre, haga clic en el botón de la flecha derecha para ir a la página siguiente.
    
    ![mobile-create-page1](./media/mobile-services-create-new-service-data/mobile-create-page1.png)
    
    Se muestra la página **Specify database settings**.
	<div class="dev-callout">

	<b>Nota:</b>
	<p>Como parte de este tutorial, va a crear una instancia y un nuevo
	servidor de Base de datos SQL. Puede reutilizar esta nueva base de
	datos y administrarla como lo haría con cualquier otra instancia de
	Base de datos SQL. Si ya tiene una base de datos en la misma región
	que el nuevo servicio móvil, también puede elegir **Use existing
	Database** y, a continuación, seleccionar esa
	base de datos. No se recomienda el uso de una base de datos en una región diferente debido a los costes adicionales de ancho de banda y las elevadas latencias.</p>
	</div>


5.  En **Name**, escriba el nombre de la base de datos nueva, luego escriba el **Login name**, que es el nombre de inicio de sesión de administrador para el nuevo servidor de Base de datos SQL, escriba y confirme la contraseña y haga clic en el botón de comprobación para completar el proceso.
    
    ![mobile-create-page2](./media/mobile-services-create-new-service-data/mobile-create-page2.png)
	<div class="dev-callout">

	<b>Nota:</b>
	<p>Aparece una advertencia cuando la contraseña que proporciona no
cumple los requisitos mínimos o cuando no coincide con su confirmación.    Recomendamos que tome nota del nombre de inicio de sesión de administrador y la contraseña que especifique; necesitará esta información para volver a usar la instancia de la Base de datos SQL o el servidor más adelante.</p>
</div>


Ahora ha creado un servicio móvil que pueden usar sus aplicaciones móviles. A continuación, agregará una nueva tabla en la que se almacenarán los datos de aplicaciones. Esta tabla será la que utilice la aplicación en lugar de la colección en memoria.



[1]: https://manage.windowsazure.com/