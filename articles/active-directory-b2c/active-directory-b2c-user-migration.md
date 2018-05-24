---
title: 'Azure Active Directory B2C: Métodos de migración de usuarios'
description: Describe fundamentos y conceptos avanzados sobre la migración de usuarios mediante Graph API y opcionalmente mediante directivas personalizadas de Azure AD B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 10/04/2017
ms.author: davidmu
ms.openlocfilehash: 47f813839a5495591356e5ecd461902fa8745c65
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32140332"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: Migración de usuarios
Si migra su proveedor de identidades a Azure Active Directory B2C (Azure AD B2C), también debe migrar la cuenta de usuario. En este artículo se explica cómo migrar las cuentas de usuario existentes desde cualquier proveedor de identidades a Azure AD B2C. El artículo no pretende ser preceptivo, sino describir varios enfoques. El desarrollador es responsable de la idoneidad de cada enfoque.

## <a name="user-migration-flows"></a>Flujos de migración de usuarios
Con Azure AD B2C, puede migrar los usuarios mediante [Azure AD Graph API][B2C-GraphQuickStart]. El proceso de migración de usuarios se divide en dos flujos:

* **Antes de la migración**: este flujo se aplica cuando tiene acceso libre a las credenciales de un usuario (nombre de usuario y contraseña) o las credenciales se cifran, pero puede descifrarlas. El proceso de pre-migración implica leer los usuarios del anterior proveedor de identidades y crear nuevas cuentas en el directorio de Azure AD B2C.

* **Pre-migración y restablecimiento de contraseña**: este flujo se aplica cuando la contraseña de un usuario no es accesible. Por ejemplo: 
    * La contraseña se almacena en formato HASH.
    * La contraseña se almacena en un proveedor de identidades al que no tiene acceso. El proveedor de identidades anterior valida las credenciales de usuario mediante una llamada a un servicio web.

En ambos flujos, primero debe ejecutar el proceso de pre-migración, leer los usuarios del proveedor de identidades anterior y crear nuevas cuentas en el directorio de Azure AD B2C. Si no tiene la contraseña, debe crear la cuenta con una contraseña generada de forma aleatoria. A continuación, pida al usuario que cambie la contraseña, o bien, cuando el usuario inicie sesión por primera vez, Azure AD B2C pedirá al usuario que la restablezca.

## <a name="password-policy"></a>Directiva de contraseñas
La directiva de contraseñas de Azure AD B2C (para cuentas locales) se basa en la directiva de Azure AD. Las directivas de restablecimiento de la contraseña, registro e inicio de sesión de Azure AD B2C utilizan la seguridad de la contraseña "segura" y las contraseñas no expiran. Para obtener más información, consulte el artículo sobre la [directiva de contraseñas de Azure AD][AD-PasswordPolicies].

Si las cuentas que se van a migrar cuentan con una seguridad de contraseña inferior a la [seguridad de contraseña que exige Azure AD B2C][AD-PasswordPolicies], puede deshabilitar el requisito de contraseña segura. Para cambiar la directiva de contraseñas predeterminada, establezca la propiedad `passwordPolicies` en `DisableStrongPassword`. Por ejemplo, puede modificar la solicitud de creación de usuario de la siguiente manera:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Paso 1: usar Azure AD Graph API para migrar usuarios
Debe crear la cuenta de usuario de Azure AD B2C a través de Graph API (con la contraseña o con una contraseña aleatoria). Esta sección describe el proceso de creación de cuentas de usuario en el directorio de Azure AD B2C mediante Graph API.

### <a name="step-11-register-your-application-in-your-tenant"></a>Paso 1.1: Registro de la aplicación en el inquilino
Para comunicarse con Graph API, debe tener una cuenta de servicio con privilegios administrativos. En Azure AD, debe registrar una aplicación y la autenticación en Azure AD. Las credenciales de la aplicación son el **Identificador de aplicación** y el **Secreto de aplicación**. La aplicación actúa como tal, no como un usuario, para llamar a Graph API.

En primer lugar, registre la aplicación de migración en Azure AD. A continuación, cree una clave de aplicación (secreto de aplicación) y establezca la aplicación con los privilegios de escritura.

1. Inicie sesión en [Azure Portal][Portal].

2. Para elegir el inquilino de Azure AD **B2C**, seleccione la cuenta en la esquina superior derecha de la ventana.

3. En el panel izquierdo, seleccione **Azure Active Directory** (no Azure AD B2C). Es posible que tenga que seleccionar **Más servicios** para encontrarla.

4. Seleccione **App registrations** (Registros de aplicaciones).

5. Seleccione **Nuevo registro de aplicaciones**.

    ![Nuevo registro de aplicaciones](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)

6. Para crear una nueva aplicación, haga lo siguiente:
    * Para **Nombre**, use **B2CUserMigration** o cualquier otro nombre que quiera.
    * En **Tipo de aplicación**, use **Aplicación web o API**.
    * En **Dirección URL de inicio de sesión**, use **https://localhost** (ya que no es pertinente para esta aplicación).
    * Seleccione **Crear**.

7. Una vez creada la aplicación, en la lista **Aplicaciones**, seleccione la aplicación recién creada **B2CUserMigration**.

8. Seleccione **Propiedades**, copie el **Identificador de aplicación** y guárdelo para más adelante.

### <a name="step-12-create-the-application-secret"></a>Paso 1.2: Creación del secreto de aplicación
1. En la ventana **Aplicación registrada** de Azure Portal, seleccione **Claves**.

2. Agregue una nueva clave (también conocida como secreto de cliente) y, a continuación, cópiela para su uso posterior.

    ![Identificador de la aplicación y claves](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>Paso 1.3: Concesión de permisos administrativos a la aplicación
1. En la ventana **Aplicación registrada** de Azure Portal, seleccione **Permisos necesarios**.

2. Seleccione **Microsoft Azure Active Directory**.

3. En el panel **Habilitar acceso**, en **Permisos de la aplicación**, seleccione **Leer y escribir en datos de directorio** y haga clic en **Guardar**.

4. En el panel **Permisos necesarios**, seleccione **Conceder permisos**.

    ![Permisos de aplicación](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

Ahora tiene una aplicación con permisos para crear, leer y actualizar usuarios desde el inquilino de Azure AD B2C.

### <a name="step-14-optional-environment-cleanup"></a>Paso 1.4: (Opcional) Limpieza del entorno
Los permisos de lectura y escritura de los datos del directorio *no* incluyen el derecho de eliminar usuarios. Para que su aplicación pueda eliminar usuarios (para limpiar el entorno), debe realizar un paso adicional, que implica la ejecución de PowerShell a fin de establecer los permisos de administrador de la cuenta de usuario. En caso contrario, puede saltar a la sección siguiente.

> [!IMPORTANT]
> Debe usar una cuenta de administrador de inquilinos B2C que sea *local* para el inquilino B2C. La sintaxis del nombre de la cuenta es *admin@contosob2c.onmicrosoft.com*.

>[!NOTE]
> El siguiente script de PowerShell requiere [la versión 2 de Azure Active Directory PowerShell][AD-Powershell].

En este script de PowerShell, haga lo siguiente:
1. Se conecta a su servicio en línea. Para ello, ejecute el cmdlet `Connect-AzureAD` en el símbolo del sistema de Windows PowerShell y proporcione las credenciales.

2. Se utiliza el **Identificador de aplicación** para asignar el rol de administrador de cuenta de usuario a la aplicación. Estos roles tienen identificadores conocidos, por lo que lo único que debe hacer es escribir el **identificador de aplicación** en el script.

```PowerShell
Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Cambie el valor de `$AppId` por el **identificador de aplicación** de Azure AD.

## <a name="step-2-pre-migration-application-sample"></a>Paso 2: Ejemplo de aplicación de pre-migración
[Descargue y ejecute el código de ejemplo][UserMigrationSample]. Puede descargarlo como archivo ZIP.

### <a name="step-21-edit-the-migration-data-file"></a>Paso 2.1: Edición del archivo de datos de migración
La aplicación de ejemplo usa un archivo JSON que contiene datos de usuarios ficticios. Después de ejecutar correctamente el ejemplo, puede cambiar el código para consumir los datos de su propia base de datos. O bien puede exportar el perfil de los usuarios a un archivo JSON y configurar la aplicación para usar ese archivo.

Para editar el archivo JSON, abra la solución `AADB2C.UserMigration.sln` de Visual Studio. En el proyecto `AADB2C.UserMigration`, abra el archivo `UsersData.json`.

![Archivo de datos de usuario](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Como puede ver, el archivo contiene una lista de entidades de usuario. Cada entidad de usuario tiene las siguientes propiedades:
* email
* DisplayName
* firstName
* lastName
* password (puede estar vacío)

> [!NOTE]
> En tiempo de compilación, Visual Studio copia el archivo al directorio `bin`.

### <a name="step-22-configure-the-application-settings"></a>Paso 2.2: Configuración de la aplicación
En el proyecto `AADB2C.UserMigration`, abra el archivo *App.config*. Reemplace los siguientes valores de configuración de la aplicación por sus propios valores:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> * El uso de una cadena de conexión de la tabla de Azure se describe en las secciones siguientes.
> * El nombre del inquilino B2C es el dominio que escribió durante la creación del inquilino y se muestra en Azure Portal. El nombre del inquilino normalmente termina con el sufijo *.onmicrosoft.com* (por ejemplo, *contosob2c.onmicrosoft.com*).
>

### <a name="step-23-run-the-pre-migration-process"></a>Paso 2.3: Ejecución del proceso de pre-migración
Haga clic con el botón derecho en la solución `AADB2C.UserMigration` y vuelva a compilar el ejemplo. Si todo es correcto, ahora debería tener un archivo ejecutable `UserMigration.exe` que se encuentra en `AADB2C.UserMigration\bin\Debug\net461`. Para ejecutar el proceso de migración, use uno de los parámetros de la línea de comandos siguientes:

* Para **migrar usuarios con contraseña**, use el comando `UserMigration.exe 1`.

* Para **migrar usuarios con contraseña aleatoria**, use el comando `UserMigration.exe 2`. Esta operación también crea una entidad de tabla de Azure. Más adelante, se configura la directiva para llamar al servicio de la API de REST. El servicio usa una tabla de Azure para realizar un seguimiento del proceso de migración y administrarlo.

![Demostración del proceso de migración](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Paso 2.4: Comprobación del proceso de pre-migración
Para validar la migración, use uno de los dos métodos siguientes:

* Para buscar un usuario por nombre para mostrar, use Azure Portal:

    a. Abra **Azure AD B2C** y seleccione **Usuarios y grupos**.

    b. En el cuadro de búsqueda, escriba el nombre para mostrar del usuario y vea su perfil.

* Para recuperar un usuario por dirección de correo electrónico de inicio de sesión, use esta aplicación de ejemplo:

    a. Ejecute el siguiente comando:

    ```Console
        UserMigration.exe 3 {email address}
    ```

    > [!TIP]
    > También puede recuperar un usuario mediante el nombre para mostrar con el comando siguiente: `UserMigration.exe 4 "<Display name>"`.

    b. Abra el archivo UserProfile.json en un editor de JSON para ver la información de usuario.

    ![Archivo UserProfile.json](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Paso 2.5: (Opcional) Limpieza del entorno
Si quiere limpiar el inquilino de Azure AD y eliminar los usuarios del directorio de Azure AD, ejecute el comando `UserMigration.exe 5`.

> [!NOTE]
> * Para realizar la limpieza de su inquilino, configure permisos de Administrador de cuenta de usuario para la aplicación.
> * La aplicación de ejemplo de migración limpia todos los usuarios que aparecen en el archivo JSON.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Paso 2.6: Inicio de sesión con los usuarios migrados (con contraseña)
Después de ejecutar el proceso de pre-migración con las contraseñas de los usuarios, las cuentas están listas para su uso y los usuarios pueden iniciar sesión en la aplicación con Azure AD B2C. Si no tiene acceso a las contraseñas de los usuarios, continúe con la siguiente sección.

## <a name="step-3-help-users-reset-their-password"></a>Paso 3: Ayudar a los usuarios a restablecer su contraseña
Si migra los usuarios con una contraseña aleatoria, estos deberán restablecerla. Para ayudarles a restablecer la contraseña, envíe un correo electrónico de bienvenida con un vínculo para restablecer la contraseña.

Para obtener el vínculo a la directiva de restablecimiento de contraseña, haga lo siguiente:

1. Seleccione **Configuración de Azure AD B2C** y, a continuación, las propiedades de la directiva **Restablecimiento de contraseña**.

2. Seleccione la aplicación.

    > [!NOTE]
    > La función Ejecutar ahora requiere al menos que una aplicación esté registrada previamente en el inquilino. Para obtener información sobre cómo registrar aplicaciones, consulte el artículo de [introducción][B2C-GetStarted] a Azure AD B2C o el artículo [Registro de la aplicación][B2C-AppRegister] de Azure AD B2C.

3. Seleccione **Ejecutar ahora** y compruebe la directiva.

4. En el cuadro **Ejecutar punto de conexión ahora**, copie la dirección URL y, a continuación, envíela a los usuarios.

    ![Establecer registros de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Paso 4: (Opcional) Cambio de la directiva para comprobar y establecer el estado de migración del usuario

> [!NOTE]
> Para comprobar y cambiar el estado de migración del usuario, debe usar una directiva personalizada. Las instrucciones de instalación del artículo [Get started with custom policies][B2C-GetStartedCustom] (Comenzar a trabajar con directivas personalizadas) deben completarse.
>

Cuando los usuarios intentan iniciar sesión sin restablecer la contraseña primero, la directiva debería devolver un mensaje de error descriptivo. Por ejemplo: 
>*La contraseña ha expirado. Para restablecerla, seleccione el vínculo Restablecer contraseña.*

Para realizar este paso opcional, es necesario el uso de las directivas personalizadas de Azure AD B2C, tal como se describe en el artículo [Getting started with custom policies][B2C-GetStartedCustom] (Comenzar a trabajar con directivas personalizadas).

En esta sección, se cambia la directiva para comprobar el estado de migración del usuario en el inicio de sesión. Si el usuario no ha cambiado la contraseña, se devuelve el mensaje de error HTTP 409, que pide al usuario que seleccione el vínculo **¿Ha olvidado la contraseña?**.

Para realizar un seguimiento del cambio de contraseña, se usa una tabla de Azure. Cuando ejecuta el proceso de pre-migración con el parámetro de la línea de comandos `2`, crea la entidad de usuario en una tabla de Azure. Su servicio realiza lo siguiente:

* En el inicio de sesión, la directiva de Azure AD B2C invoca el servicio RESTful de migración y envía un correo electrónico como notificación de entrada. El servicio busca la dirección de correo electrónico en la tabla de Azure. Si existe la dirección, el servicio genera el mensaje de error: *Debe cambiar la contraseña*.

* Después de que el usuario cambie correctamente la contraseña, elimine la entidad de la tabla de Azure.

>[!NOTE]
>Usamos una tabla de Azure para simplificar el ejemplo. Puede almacenar el estado de migración en cualquier base de datos o como una propiedad personalizada en la cuenta de Azure AD B2C.

### <a name="41-update-your-application-setting"></a>4.1: Actualización de la configuración de la aplicación
1. Para probar la demostración de API RESTful, abra `AADB2C.UserMigration.sln` en Visual Studio.

2. En el proyecto `AADB2C.UserMigration.API`, abra el archivo *appsettings.json*. Reemplace la configuración con las opciones que se configuraron en el [paso 2.2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Paso 4.2: Implementación de la aplicación web en Azure App Service
En el Explorador de soluciones, haga clic con el botón derecho en `AADB2C.UserMigration.API` y seleccione "Publicar...". Siga las instrucciones para publicarla en Azure App Service. Para obtener más información, consulte [Deploy your app to Azure App Service][AppService-Deploy] (Implementar la aplicación en Azure App Service).

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Paso 4.3: Adición del perfil técnico y validación del perfil técnico a la directiva
1. En el Explorador de soluciones, expanda "Elementos de la solución" y abra el archivo de directiva *TrustFrameworkExtensions.xml*.
2. Cambie los campos `TenantId`, `PublicPolicyUri` y `<TenantId>` de `yourtenant.onmicrosoft.com` al nombre del inquilino.
3. En el elemento `<TechnicalProfile Id="login-NonInteractive">`, reemplace todas las instancias de `ProxyIdentityExperienceFrameworkAppId` y `IdentityExperienceFrameworkAppId` con los identificadores de aplicación configurados en [Getting started with custom policies][B2C-GetStartedCustom] (Comenzar a trabajar con directivas personalizadas).
4. En el nodo `<ClaimsProviders>`, agregue el siguiente fragmento de código XML. Cambie el valor de `ServiceUrl` para que apunte a la dirección URL de Azure App Service.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

El perfil técnico anterior define una notificación de entrada: `signInName` (enviar como correo electrónico). En el inicio de sesión, la notificación se envía al punto de conexión de RESTful.

Después de definir el perfil técnico para la API de RESTful, indique a la directiva de Azure AD B2C que lo llame. El fragmento XML invalida `SelfAsserted-LocalAccountSignin-Email`, que se define en la directiva de base. El fragmento XML también agrega `ValidationTechnicalProfile` con un valor de ReferenceId que apunta a su perfil técnico `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Paso 4.4: Carga de la directiva en el inquilino
1. En [Azure Portal][Portal], cambie al [contexto del inquilino de Azure AD B2C][B2C-NavContext] y seleccione **Azure AD B2C**.

2. Seleccione **Marco de experiencia de identidad**.

3. Seleccione **Todas las directivas**.

4. Seleccione **Cargar directiva**.

5. Active la casilla **Sobrescribir la directiva si existe**.

6. Cargue el archivo *TrustFrameworkExtensions.xml* y asegúrese de que pasa la validación.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Paso 4.5: Prueba de la directiva personalizada con Ejecutar ahora
1. Seleccione **Configuración de Azure AD B2C** y vaya a **Marco de experiencia de identidad**.

2. Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado, y seleccione **Ejecutar ahora**.

3. Intente iniciar sesión con una de las credenciales de los usuarios migrados y, luego, seleccione **Iniciar de sesión**. La API de REST ahora debería generar el mensaje de error siguiente:

    ![Establecer registros de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Paso 4.6: (Opcional) Solución de problemas de la API de REST
Puede ver y supervisar la información de registro prácticamente en tiempo real.

1. En el menú de configuración de la aplicación RESTful, en **Supervisión**, seleccione **Registros de diagnóstico**.

2. Establezca la opción **Registro de la aplicación (sistema de archivos)** en **Activada**.

3. Establezca el **Nivel** en **Detallado**.

4. Seleccione **Guardar**.

    ![Establecer registros de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

5. En el menú **Configuración**, seleccione **Secuencia de registro**.

6. Compruebe la salida de la API de RESTful.

Para obtener más información, consulte los [registros de streaming y la consola][AppService-Log].

> [!IMPORTANT]
> Use los registros de diagnóstico solo durante el desarrollo y pruebas. La salida de la API de RESTful puede contener información confidencial que no debe exponerse en producción.
>

## <a name="optional-download-the-complete-policy-files"></a>(Opcional) Descarga de los archivos de directiva completos
Una vez completado el tutorial [Get started with custom policies][B2C-GetStartedCustom] (Comenzar a trabajar con directivas personalizadas), le recomendamos que compile el escenario mediante sus archivos de directiva personalizados. Hemos proporcionado [archivos de directiva de ejemplo][UserMigrationSample] para que los tenga como referencia.

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[AppService-Log]: https://docs.microsoft.com/azure/active-directory-b2c/app-service-web/web-sites-streaming-logs-and-console
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration