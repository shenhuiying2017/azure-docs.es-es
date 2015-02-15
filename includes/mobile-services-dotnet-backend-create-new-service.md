

Siga los pasos siguientes para crear un servicio móvil nuevo.

1.	Inicie sesión en el [Portal de administración]. 

2.	En la parte inferior del panel de navegación, haga clic en **+NEW**.

	![](./media/mobile-services-dotnet-backend-create-new-service/plus-new.png)

3.	Expanda **Proceso** y **Mobile Service** y, a continuación, haga clic en **Create**.

	![](./media/mobile-services-dotnet-backend-create-new-service/mobile-create.png)

	Se muestra el cuadro de diálogo **New Mobile Service**.

4.	En la página **Create a mobile service**, seleccione **Create a free 20 MB SQL Database**, seleccione el tiempo de ejecución **.NET**, luego escriba un nombre de subdominio para el servicio móvil nuevo en el cuadro de texto **URL** y espere la verificación del nombre. Cuando se haya verificado el nombre, haga clic en el botón de la flecha derecha para ir a la página siguiente.	

	![](./media/mobile-services-dotnet-backend-create-new-service/mobile-create-page1.png)

   	Se muestra la página **Specify database settings**.

	> [AZURE.NOTE] Como parte de este tutorial, va a crear una instancia y un nuevo servidor de Base de datos SQL. Puede reutilizar esta nueva base de datos y administrarla como lo haría con cualquier otra instancia de Base de datos SQL. Si ya tiene una base de datos en la misma región que el nuevo servicio móvil, también puede elegir **Use existing Database** y, a continuación, seleccionar esa base de datos. No se recomienda el uso de una base de datos en una región diferente debido a los costes adicionales de ancho de banda y las elevadas latencias.	

6.	En **Name**, escriba el nombre de la base de datos nueva, luego escriba el **Login name**, que es el nombre de inicio de sesión de administrador para el nuevo servidor de Base de datos SQL, escriba y confirme la contraseña y haga clic en el botón de comprobación para completar el proceso.

	![](./media/mobile-services-dotnet-backend-create-new-service/mobile-create-page2.png)

	> [AZURE.NOTE] Aparece una advertencia cuando la contraseña que proporciona no cumple los requisitos mínimos o cuando no coincide con su confirmación. 
	>
	> Se recomienda que tome nota del nombre de inicio de sesión y la contraseña del administrador que especifique, ya que necesitará esta información para volver a usar la instancia de la base de datos SQL o el servidor más adelante.

Ahora ha creado un servicio móvil que pueden usar sus aplicaciones móviles.


<!-- URLs. -->
[Portal de administración]: https://manage.windowsazure.com/

<!--HONumber=42-->
