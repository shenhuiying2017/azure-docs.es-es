Para habilitar el registro en su aplicación, debe crear una directiva de registro. Esta directiva describe las experiencias que tendrán los consumidores durante el registro y el contenido de los tokens que recibe la aplicación en registros completados correctamente.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Haga clic en **Directivas de registro**.

Haga clic en **+ Agregar** en la parte superior de la hoja.

El **Nombre** determina el nombre de la directiva de registro usado por su aplicación. Por ejemplo, escriba **SiUp**.

Haga clic en **Proveedores de identidades** y seleccione **Registro por correo electrónico**. También puede seleccionar proveedores de identidades sociales, si ya se han configurado. Haga clic en **OK**.

Haga clic en **Atributos de registro**. Aquí elige atributos que quiere recopilar del consumidor durante el registro. Por ejemplo, seleccione **País o región**, **Nombre para mostrar** y **Código postal**. Haga clic en **Aceptar**.

Haga clic en **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens enviados de vuelta a su aplicación después de una experiencia de registro correcta. Por ejemplo, seleccione **Nombre para mostrar**, **Proveedor de identidades**, **Código postal**, **El usuario es nuevo** e **Id. de objeto del usuario**.

Haga clic en **Crear**. La directiva que se acaba de crear aparece como **B2C_1_SiUp** (el fragmento **B2C\_1\_** se agrega automáticamente) en la hoja **Directivas de registro**.

Para abrir la directiva, haga clic en **B2C_1_SiUp**.

Seleccione **Aplicación Contoso B2C** en el menú desplegable **Aplicaciones** y `https://localhost:44321/` en el menú desplegable **Dirección URL de respuesta / URI de redirección**.

Haga clic en **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del consumidor de registro en su aplicación.

> [!NOTE]
> Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.
>