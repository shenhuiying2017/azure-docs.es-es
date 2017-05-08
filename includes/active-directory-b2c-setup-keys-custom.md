> [!NOTE]
> Tenemos previsto mejorar la experiencia y dejar de utilizar los pasos siguientes.

### <a name="create-an-administrator-credential-in-the-azure-ad-b2c-tenant"></a>Creación de una credencial de administrador en el inquilino de Azure AD B2C.

Para la sección siguiente, debe usar unas credenciales que usen el dominio de su inquilino de Azure AD B2C. Para ello, debe crear una cuenta de administrador con ellas. Para ello:

1. En [Azure Portal](https://portal.azure.com), cambie al contexto de su inquilino de Azure AD B2C y abra la hoja de Azure AD B2C. [Mostrarme cómo](..\articles\active-directory-b2c\active-directory-b2c-navigate-to-b2c-context.md).
1. Seleccione **Usuarios y grupos**.
1. Seleccione **Todos los usuarios**.
1. Haga clic en **+ Nuevo usuario**.
    * Defina el **Nombre** = `Admin`.
    * Establezca el **Nombre de usuario** = `admin@{tenantName}.onmicrosoft.com` donde `{tenantName}` sea el nombre del inquilino de Azure AD B2C.
1. En **Rol del directorio**, elija **Administrador Global** y haga clic en **Aceptar**.
1. Haga clic en **Crear** para crear el usuario administrador.
1. Marque **Mostrar contraseña** y copie la contraseña.

### <a name="set-up-the-key-container"></a>Configuración del contenedor de claves

El contenedor de claves se utiliza para almacenar claves. Para configurar uno:

1. Abra un símbolo del sistema de PowerShell nuevo.  Puede hacerlo con la **tecla del logotipo de Windows + R**, escriba `powershell` y presione Entrar.
1. Descargue esta reproducción para obtener la herramienta ExploreAdmin de Powershell.

    ```powershell
    git clone https://github.com/Azure-Samples/active-directory-b2c-advanced-policies
    ```

1. Cambie a la carpeta con la herramienta ExploreAdmin.

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. Importar la herramienta ExploreAdmin en Powershell.

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. La confirmación de `b2c_1a_TokenSigningKeyContainer` aún no existe.  Reemplace `{tenantName}` por el nombre del inquilino.

    ```powershell
    Get-CpimKeyContainer -TenantId {tenantName}.onmicrosoft.com -StorageReferenceId b2c_1a_TokenSigningKeyContainer -ForceAuthenticationPrompt
    ```

    a. Cuando aparezca el mensaje de inicio de sesión, use la cuenta de administrador que creó en la sección anterior.

    b. Cuando se le pida, escriba el número de teléfono para configurar la autenticación multifactor.

    c. Cuando se le pida, cambie la contraseña.

    d. **Se espera un error.**  El error debería indicar que `b2c_1a_TokenSigningKeyContainer` no se encuentra.  Si no hay ningún error, porque ya haya completado estos pasos, omita el resto de la sección.

1. Cree `b2c_1a_TokenSigningKeyContainer`.  Reemplace `{tenantName}` por el nombre del inquilino.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_TokenSigningKeyContainer  b2c_1a_TokenSigningKeyContainer rsa 2048 0 0
    ```

1. Cree `b2c_1a_TokenEncryptionKeyContainer`.  Reemplace `{tenantName}` por el nombre del inquilino.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com b2c_1a_TokenEncryptionKeyContainer b2c_1a_TokenEncryptionKeyContainer rsa 2048 0 0
    ```

1. Cree `b2c_1a_FacebookSecret`.  Reemplace `{tenantName}` por el nombre del inquilino.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_FacebookSecret  b2c_1a_FacebookSecret rsa 2048 0 0
    ```
