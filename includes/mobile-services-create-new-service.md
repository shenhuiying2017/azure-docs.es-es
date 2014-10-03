Siga los pasos siguientes para crear un servicio móvil nuevo.

1.  Inicie sesión en el [Portal de administración][].

2.  En la parte inferior del panel de navegación, haga clic en **+NEW**.
	
	![][ ]

3.  Expanda **Proceso** y **Mobile Service** y, a continuación, haga clic en **Create**.

    ![][1]

	Se muestra el cuadro de diálogo **New Mobile Service**.

4.  En la página **Create a mobile service**, seleccione **Create a free 20 MB SQL Database**, seleccione el tiempo de ejecución **Node.js**, luego escriba un nombre de subdominio para el servicio móvil nuevo en el cuadro de texto **URL** y espere la verificación del nombre. Cuando se haya verificado el nombre, haga clic en el botón de la flecha derecha para ir a la página siguiente.

    ![][2]

    Se muestra la página **Specify database settings**. 
	<div class="dev-callout"> 
	<b>Nota:</b> 
	<p>Como parte de este tutorial, va a crear una instancia y un nuevo servidor de Base de datos SQL. Puede reutilizar esta nueva base de datos y administrarla como lo har&iacute;a con cualquier otra instancia de Base de datos SQL. Si ya tiene una base de datos en la misma regi&oacute;n que el nuevo servicio m&oacute;vil, tambi&eacute;n puede elegir <strong>Use existing Database</strong> y, a continuaci&oacute;n, seleccionar esa base de datos. No se recomienda el uso de una base de datos en una regi&oacute;n diferente debido a los costes adicionales de ancho de banda y las elevadas latencias.</p></div>

5.  En **Name**, escriba el nombre de la base de datos nueva, luego escriba el **Login name**, que es el nombre de inicio de sesión de administrador para el nuevo servidor de Base de datos SQL, escriba y confirme la contraseña y haga clic en el botón de comprobación para completar el proceso.

    ![][3]

    <div class="dev-callout"> 
	<b>Nota:</b> 
	<p>Aparece una advertencia cuando la contrase&ntilde;a que proporciona no cumple los requisitos m&iacute;nimos o cuando no coincide con su confirmaci&oacute;n. <br/>Recomendamos que tome nota del nombre de inicio de sesi&oacute;n de administrador y la contrase&ntilde;a que especifique; necesitar&aacute; esta informaci&oacute;n para volver a usar la instancia de la Base de datos SQL o el servidor m&aacute;s adelante.</p> 
	</div>

Ahora ha creado un servicio móvil que pueden usar sus aplicaciones móviles.

<!-- URLs. -->

  [Portal de administración]: https://manage.windowsazure.com/
  [ ]: ./media/mobile-services-create-new-service/plus-new.png
  [1]: ./media/mobile-services-create-new-service/mobile-create.png
  [2]: ./media/mobile-services-create-new-service/mobile-create-page1.png
  [3]: ./media/mobile-services-create-new-service/mobile-create-page2.png
