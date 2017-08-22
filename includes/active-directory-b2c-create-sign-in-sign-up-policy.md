Para habilitar el inicio de sesión en la aplicación, deberá crear una directiva de inicio de sesión. Esta directiva describe las experiencias que tendrán los consumidores durante el inicio de sesión y el contenido de los tokens que recibirá la aplicación en inicios de sesión correctos.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

En la sección de directivas de configuración, seleccione **Directivas de inicio de sesión o de registro** y haga clic en **+ Agregar**.

![Seleccionar directivas de registro o inicio de sesión y hacer clic en el botón Agregar](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-policy.png)

Escriba un **nombre** de directiva al que la aplicación haga referencia. Por ejemplo, escriba: `SiUpIn`.

Seleccione **Proveedores de identidades** y **Registro por correo electrónico**. También puede seleccionar proveedores de identidades sociales, si ya se han configurado. Haga clic en **Aceptar**.

![Seleccionar registro por correo electrónico como proveedor de identidades y hacer clic en el botón Aceptar](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-identity-providers.png)

Seleccione **Atributos de registro**. Elija los atributos que quiere recopilar del consumidor durante el registro. Por ejemplo, seleccione **País o región**, **Nombre para mostrar** y **Código postal**. Haga clic en **Aceptar**.

![Seleccionar algunos atributos y hacer clic en el botón Aceptar](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-attributes.png)

Seleccione **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens de autorización enviados de vuelta a su aplicación después de una experiencia de registro o de inicio de sesión correcta. Por ejemplo, seleccione **Nombre para mostrar**, **Proveedor de identidades**, **Código postal**, **El usuario es nuevo** e **Id. de objeto del usuario**.

![Seleccionar algunas notificaciones de aplicación y hacer clic en el botón Aceptar](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-application-claims.png)

Haga clic en **Crear** para agregar la directiva. La directiva aparece como **B2C_1_SiUpIn**. El prefijo **B2C_1_** se anexa al nombre.

Para abrir la directiva, haga clic en **B2C_1_SiUpIn**. Compruebe la configuración especificada en la tabla y haga clic en **Ejecutar ahora**.

![Seleccionar la directiva y ejecutarla](media/active-directory-b2c-create-sign-in-sign-up-policy/run-b2c-signup-signin-policy.png)

| Configuración      | Valor  |
| ------------ | ------ |
| **Aplicaciones** | Aplicación B2C de Contoso |
| **Seleccionar dirección URL de respuesta** | `https://localhost:44316/` |

Se abrirá una nueva pestaña del explorador y podrá comprobar la experiencia del consumidor de registro o de inicio de sesión tal como se configuró.

> [!NOTE]
> Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.
>