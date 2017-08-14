Para habilitar el inicio de sesión en la aplicación, deberá crear una directiva de inicio de sesión. Esta directiva describe las experiencias que tendrán los consumidores durante el inicio de sesión y el contenido de los tokens que recibirá la aplicación en inicios de sesión correctos.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)] Haga clic en **Directivas de inicio de sesión**.

Haga clic en **+Agregar** en la parte superior de la hoja.

El **Nombre** determina el nombre de la directiva de inicio de sesión usada por su aplicación. Por ejemplo, escriba **SiIn**.

Haga clic en **Proveedores de identidades** y seleccione **Inicio de sesión de cuenta local**. También puede seleccionar proveedores de identidades sociales, si ya se han configurado. Haga clic en **OK**.

Haga clic en **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens enviados de vuelta a su aplicación después de una experiencia de inicio de sesión correcta. Por ejemplo, seleccione **Nombre para mostrar**, **Proveedor de identidades**, **Código Postal** e **Id. de objeto del usuario**. Haga clic en **Aceptar**.

Haga clic en **Crear**. Tenga en cuenta que la directiva que se acaba de crear aparece como **B2C_1_SiIn** (el fragmento **B2C\_1\_** se agrega automáticamente) en la hoja **Directivas de inicio de sesión**.

Para abrir la directiva, haga clic en **B2C_1_SiIn**.

Seleccione **Aplicación Contoso B2C** en el menú desplegable **Aplicaciones** y `https://localhost:44321/` en el menú desplegable **Dirección URL de respuesta / URI de redirección**.

Haga clic en **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del consumidor de inicio de sesión en su aplicación.

> [!NOTE]
> Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.
>