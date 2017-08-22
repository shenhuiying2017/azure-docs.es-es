Para habilitar la edición de perfiles en su aplicación, deberá crear una directiva de edición de perfiles. Esta directiva describe las experiencias que tendrán los consumidores durante la edición de perfiles y el contenido de los tokens que recibirá la aplicación al finalizar correctamente.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

En la sección de directivas de configuración, seleccione **Directivas de edición de perfil** y haga clic en **+ Agregar**.

![Seleccionar directivas de edición de perfil y hacer clic en el botón Agregar](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-policy.png)

Escriba un **nombre** de directiva al que la aplicación haga referencia. Por ejemplo, escriba: `SiPe`.

Seleccione **Proveedores de identidades** y active **Inicio de sesión de cuenta local**. También puede seleccionar proveedores de identidades sociales, si ya se han configurado. Haga clic en **Aceptar**.

![Seleccionar inicio de sesión de cuenta local como proveedor de identidades y hacer clic en el botón Aceptar](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-identity-providers.png)

Seleccione **Atributos de perfil**. Elija los atributos que el consumidor puede ver y editar en su perfil. Por ejemplo, seleccione **País o región**, **Nombre para mostrar** y **Código postal**. Haga clic en **Aceptar**.

![Seleccionar algunos atributos y hacer clic en el botón Aceptar](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-attributes.png)

Seleccione **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens de autorización a su aplicación después de una experiencia de edición de perfiles correcta. Por ejemplo, seleccione **Nombre para mostrar** y **Código postal**.

![Seleccionar algunas notificaciones de aplicación y hacer clic en el botón Aceptar](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-application-claims.png)

Haga clic en **Crear** para agregar la directiva. La directiva aparece como **B2C_1_SiPe**. El prefijo **B2C_1_** se anexa al nombre.

Para abrir la directiva, haga clic en **B2C_1_SiPe**. Compruebe la configuración especificada en la tabla y haga clic en **Ejecutar ahora**.

![Seleccionar la directiva y ejecutarla](media/active-directory-b2c-create-profile-editing-policy/run-b2c-editing-policy.png)

| Configuración      | Valor  |
| ------------ | ------ |
| **Aplicaciones** | Aplicación B2C de Contoso |
| **Seleccionar dirección URL de respuesta** | `https://localhost:44316/` |

Se abrirá una nueva pestaña del explorador y podrá comprobar la experiencia del consumidor de edición de perfiles que configuró.

> [!NOTE]
> Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.
>