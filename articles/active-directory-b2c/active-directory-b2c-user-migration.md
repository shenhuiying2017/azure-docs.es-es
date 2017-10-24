---
title: "Azure Active Directory B2C: Métodos de migración de usuarios"
description: "Describe fundamentos y conceptos avanzados sobre la migración de usuarios mediante la API Graph y opcionalmente mediante directivas personalizadas de Azure AD B2C."
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/04/2017
ms.author: yoelh
ms.openlocfilehash: b102edd997e69fb3568780a42dfe93fc9a90e332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: Migración de usuarios
Si planea migrar su proveedor de identidades a Azure AD B2C, también debe migrar las cuentas de usuario. En este artículo se explica cómo migrar las cuentas de usuario existentes desde cualquier proveedor de identidades a Azure AD B2C. En este artículo no pretende ser preceptivo, sino describir dos de los varios enfoques diferentes.  El desarrollador es responsable de la idoneidad.

## <a name="user-migration-flows"></a>Flujos de migración de usuarios
Azure AD B2C permite migrar los usuarios mediante la [API Graph](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). El proceso de migración de usuarios se divide en dos flujos:

* **Pre-migración**: este flujo es util cuando se tiene acceso a las credenciales de usuario (nombre de usuario y contraseña) en un texto no cifrado. También cuando las credenciales están cifradas, pero puede descifrarlas. El proceso implica: leer los usuarios del anterior proveedor de identidades y crear nuevas cuentas en el directorio de Azure AD B2C.

* **Pre-migración y restablecimiento de contraseña**: este flujo es util cuando la contraseña del usuario no está accesible. Por ejemplo:
    * Las contraseñas se almacenan en formato HASH
    * Las contraseñas se almacenan en un proveedor de identidades a las que no tiene acceso. El proveedor de identidades anterior valida las credenciales de usuario mediante una llamada a un servicio web

En ambos flujos, primero ejecuta el proceso de __pre-migración__, lee los usuarios del proveedor de identidades anterior y crea nuevas cuentas en el directorio de Azure AD B2C. Si no tiene la contraseña, cree la cuenta con una contraseña aleatoria generada. Pida a los usuarios cambiar su contraseña. O bien, al iniciar sesión por primera vez, B2C pide al usuario que restablezca la contraseña.

## <a name="password-policy"></a>Directiva de contraseñas
La directiva de contraseñas de Azure AD B2C (para cuentas locales) se basa en la directiva de Azure AD. Las directivas de restablecimiento de la contraseña, registro e inicio de sesión de Azure AD B2C utilizan la seguridad de la contraseña "segura" y las contraseñas no caducan. Lea [Directiva de contraseñas en Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) para obtener más información.

Si las cuentas que se van a migrar tienen menos seguridad de contraseña que la [seguridad de contraseña exigida por Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), puede deshabilitar el requisito de contraseña segura. Para cambiar la directiva de contraseñas predeterminada, establezca la propiedad `passwordPolicies` en `DisableStrongPassword`. Por ejemplo, puede modificar la solicitud de creación de usuario de la siguiente manera: 

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-using-graph-api-to-migrate-users"></a>Paso 1: Uso de la API Graph para migrar usuarios
Crear la cuenta de usuario de Azure AD B2C a través de la API Graph (con la contraseña o con una contraseña aleatoria). Esta sección describe el proceso de creación de cuentas de usuario en el directorio de Azure AD B2C, mediante la API Graph.

### <a name="step-11-register-your-application-in-your-tenant"></a>Paso 1.1: Registro de la aplicación en el inquilino
Para comunicarse con la API Graph, debe tener una cuenta de servicio con privilegios administrativos. En Azure AD, puede hacerlo mediante el registro de una aplicación y la autenticación en Azure AD. Las credenciales de la aplicación son: **Identificador de aplicación** y **Secreto de aplicación**. La aplicación actúa como tal, no como un usuario, para llamar a la API Graph.

En primer lugar, registre la aplicación de migración en Azure AD. A continuación, cree la clave de aplicación (secreto de aplicación) y establezca la aplicación con los privilegios adecuados.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Para elegir el inquilino de Azure AD **B2C**, seleccione la cuenta en la esquina superior derecha de la página.
3. En el panel izquierdo, haga clic en **Azure Active Directory** (no en Azure AD B2C). Tendrá que seleccionar **Más servicios** para encontrarla.
4. Seleccione **App registrations** (Registros de aplicaciones).
5. Haga clic en **Nuevo registro de aplicaciones**.

    ![Nuevo registro de aplicaciones](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)
6. Siga las indicaciones y cree una nueva aplicación
    * Para **Nombre**, use **B2CUserMigratioin** o cualquier otro nombre que desee.
    * En **Tipo de aplicación**, use **Aplicación web o API**.
    * Para **Dirección URL de inicio de sesión**, utilice https://localhost (ya que no es pertinente para esta aplicación).
    * Haga clic en **Crear**.
7. Una vez creada la aplicación, en la lista de aplicaciones, seleccione la aplicación recién creada **B2CUserMigratioin**.
Seleccione **Propiedades**, copie el **Identificador de aplicación** y guárdelo para más adelante.

### <a name="step-12-create-application-secret"></a>Paso 1.2: Creación del secreto de aplicación
8. Continúe en la Aplicación registrada de Azure Portal. Haga clic en **Claves** y agregue una nueva clave (también conocida como secreto de cliente). De nuevo, copie la clave para más adelante.

    ![Identificador de la aplicación y claves](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>Paso 1.3: Concesión de permisos administrativos a la aplicación
9. Continúe en la **Aplicación registrada** de Azure Portal
10. Haga clic en **Permisos necesarios**.
11. Haga clic en **Windows Azure Active Directory**.
12. En **Habilitar acceso**, en **Permisos de la aplicación**, seleccione **Leer y escribir en datos de directorio** y haga clic en **Guardar**.
13. Por último, de nuevo en **Permisos necesarios**, haga clic en el botón **Conceder permisos**.

    ![Permisos de aplicación](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

Ahora tiene una aplicación con permisos para crear, leer y actualizar usuarios en el inquilino de B2C.

### <a name="step-14-optional-environment-cleanup"></a>Paso 1.4: [Opcional] Limpieza del entorno
El permiso Leer y escribir en datos de directorio NO incluye la capacidad de eliminar usuarios. Si desea dar a la aplicación la posibilidad de eliminar usuarios (para limpiar el entorno), debe realizar un paso adicional. El paso implica la ejecución de PowerShell para establecer permisos de __Administrador de cuenta de usuario__, de lo contrario, puede ir a la sección siguiente.


> [!IMPORTANT]
> Debe usar una cuenta de administrador de inquilinos B2C que sea **local** para el inquilino B2C. Estas cuentas tienen este aspecto: admin@contosob2c.onmicrosoft.com.

>[!NOTE]
> El siguiente script de PowerShell requiere [Azure Active Directory PowerShell **versión 2**](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).

En el siguiente script de PowerShell:
* Se conecta a su servicio en línea. Para ello, ejecute el cmdlet `Connect-AzureAD` en el símbolo del sistema de Windows PowerShell y proporcione las credenciales. 
* Se utiliza el **Identificador de aplicación** para asignar el rol de administrador de cuenta de usuario a la aplicación. Estos roles tienen identificadores conocidos, por lo que lo único que debe hacer es escribir el **Identificador de aplicación** en el script.

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

Cambie el valor de `$AppId` por el **Identificador de aplicación** de Azure AD.

## <a name="step-2-pre-migration-application-sample"></a>Paso 2: Ejemplo de aplicación de pre-migración
Descargue el código de ejemplo y ejecútelo. Puede [descargar el código de ejemplo como un archivo .zip](http://www.github.com).

### <a name="step-21-edit-the-migration-data-file"></a>Paso 2.1: Edición del archivo de datos de migración
La aplicación de ejemplo usa un archivo JSON que contiene datos de usuarios ficticios. Después de ejecutar correctamente el ejemplo, cambie el código para consumir los datos de su propia base de datos. O exporte el perfil de los usuarios a un archivo JSON y configure la aplicación para usar ese archivo.
Para editar el archivo JSON, abra la solución `AADB2C.UserMigration.sln` de Visual Studio. En el proyecto `AADB2C.UserMigration`, abra el archivo `UsersData.json`.


![Archivo de datos de usuario](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Como puede ver, el archivo contiene la lista de entidades de usuario. Cada entidad de usuario tiene:
* email
* DisplayName
* firstName
* lastName
* password (puede estar vacío)

> [!NOTE]
> En tiempo de compilación, Visual Studio copia el archivo al directorio `bin`.

### <a name="step-22-configure-the-application-settings"></a>Paso 2.2: Configuración de la aplicación
En el proyecto `AADB2C.UserMigration`, abra el archivo App.config. Reemplace los siguientes valores de configuración de la aplicación por sus propios valores:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users data e.g. UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure Table string}" />
    
</appSettings>
```

> [!NOTE]
> * El uso de la cadena de conexión de Azure Table se describe más adelante en las secciones siguientes
> * El nombre del inquilino B2C es el dominio que especificó durante la creación del inquilino y se muestra en Azure Portal. Generalmente termina con el sufijo `.onmicrosoft.com`, por ejemplo, `contosob2c.onmicrosoft.com`.
>

### <a name="step-23-run-the-pre-migration-process"></a>Paso 2.3: Ejecución del proceso de pre-migración
Haga clic con el botón derecho en la solución `AADB2C.UserMigration` y vuelva a compilar el ejemplo. Si todo es correcto, ahora debería tener un archivo ejecutable `UserMigration.exe` que se encuentra en `AADB2C.UserMigration\bin\Debug`. Para ejecutar el proceso de migración, use uno de los parámetros de la línea de comandos siguientes:

* Para **migrar usuarios con contraseña**, use el comando `UserMigration.exe 1`.

* Para **migrar usuarios con contraseña aleatoria**, use el comando `UserMigration.exe 2`. Esta operación también crea una entidad Azure Table. Más adelante se configura la directiva para llamar al servicio de la API de REST. El servicio utiliza Azure Table para realizar un seguimiento y administrar el proceso de migración.

![Demostración del proceso de migración](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Paso 2.4: Comprobación del proceso de pre-migración
Para comprobar el resultado, desde Azure Portal, abra **Azure AD B2C** y haga clic en **Usuarios y grupos**. En el cuadro de búsqueda, escriba un nombre para mostrar de uno de los usuarios y vea el perfil del usuario. También puede usar esta aplicación de ejemplo para recuperar un usuario por **dirección de correo electrónico de inicio de sesión**. Para buscar un usuario por su dirección de correo electrónico de inicio de sesión, ejecute el siguiente comando

```Console
UserMigration.exe 3 {email address}
```

También puede guardar la salida en un archivo JSON, como se indica a continuación:
```Console
UserMigration.exe 3 {email address} >> UserProfile.json
```


Abra el archivo UserProfile.json con su editor de JSON. Con Visual Studio Code, puede aplicar formato a un documento JSON mediante `Shift+Alt+F` o Dar formato al documento en el menú contextual.

![JSON de perfil de usuario](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

También puede recuperar un usuario por el **Nombre para mostrar**, mediante el uso del comando `UserMigration.exe 4 <Display name>`

### <a name="step-25-optional-environment-cleanup"></a>Paso 2.5: [Opcional] Limpieza del entorno
Si desea limpiar el inquilino de Azure AD y eliminar los usuarios del directorio de Azure AD, ejecute el comando `UserMigration.exe 5`

> [!NOTE]
> * Para realizar la limpieza del inquilino, configure permisos de __Administrador de cuenta de usuario__ para la aplicación
> * La aplicación de ejemplo de migración limpia todos los usuarios que aparecen en el archivo JSON

### <a name="step-26-sing-in-with-migrated-users-with-password"></a>Paso 2.6: Inicio de sesión con los usuarios migrados (con contraseña)
Después de ejecutar el proceso de pre-migración con la contraseña de los usuarios, las cuentas están listas para su uso y los usuarios pueden iniciar sesión en la aplicación con Azure AD B2C. Si no tiene acceso a la contraseña de los usuarios, continúe con la siguiente sección.

## <a name="step-3-password-reset"></a>Paso 3: Restablecimiento de contraseña
En caso de migrar los usuarios con contraseña aleatoria, los usuarios deben restablecer su contraseña. Para restablecer la contraseña:
* Envíe un correo electrónico de bienvenida con un vínculo para restablecer la contraseña
* [Opcional] Cambie la directiva para controlar el caso en el que el usuario no restablece la contraseña e intenta iniciar sesión. En el inicio de sesión, la directiva comprueba el estado de migración. Si el usuario no ha cambiado la contraseña, muestra un mensaje de error descriptivo, pidiendo al usuario que haga clic en "¿Ha olvidado la contraseña?"

    > [!NOTE]
    > Para comprobar y cambiar el estado de migración del usuario, debe usar una directiva personalizada. Complete los pasos del artículo [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).
    >

### <a name="step-31-send-a-welcome-email-with-link-to-reset-password"></a>Paso 3.1: Envío de un correo electrónico de bienvenida con un vínculo para restablecer la contraseña
Para obtener el vínculo a la directiva de restablecimiento de contraseña:

1.  Abra **Configuración de Azure AD B2C** y abra las propiedades de la directiva **Restablecimiento de contraseña**.

2. Seleccione la aplicación.
    >[!NOTE]
    >
    >**Ejecutar ahora** Requiere al menos que una aplicación esté registrada previamente en el inquilino. Para obtener información sobre cómo registrar aplicaciones, vea el artículo de [introducción](active-directory-b2c-get-started.md) a Azure AD B2C o el artículo [Registro de la aplicación](active-directory-b2c-app-registration.md) de Azure AD B2C.  

2.  Seleccione **Ejecutar ahora** y compruebe la directiva
3.  **Copie** la dirección URL y envíela a los usuarios

    ![Establecer registros de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-user-migration-status"></a>Paso 4: [Opcional] Cambio de la directiva para comprobar y establecer el estado de migración del usuario

Cuando los usuarios intentan iniciar sesión sin restablecer la contraseña primero, la directiva debe devolver un mensaje de error descriptivo. Por ejemplo: Ha expirado su contraseña, para restablecer la contraseña, haga clic en el vínculo de restablecimiento de contraseña.  Este paso opcional requiere el uso de Azure AD B2C mediante directivas personalizadas, como se describe en el artículo [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).

En esta sección, se cambia la directiva para comprobar el estado de migración en el inicio de sesión. Si el usuario no ha cambiado la contraseña, se devuelve el mensaje de error HTTP 409, que pide al usuario hacer clic en el vínculo "¿Ha olvidado la contraseña?" Para realizar un seguimiento del cambio de contraseña, se usa Azure Table. La ejecución del proceso pre-migración con el parámetro de la línea de comandos `2` crea la entidad de usuario en Azure Table. El servicio:

* En el inicio de sesión, la directiva de Azure AD B2C invoca al servicio REST de migración, enviando un correo electrónico como notificación de entrada. El servicio busca la dirección de correo electrónico en Azure Table. Si existe, genera un mensaje de error "Debe cambiar la contraseña".

* En el restablecimiento de contraseña, después de que el usuario cambie correctamente la contraseña, elimina la entidad de Azure Table.


> [!NOTE]
>Se usa Azure Table para simplificar el ejemplo. Puede almacenar el estado de migración en cualquier base de datos o como una propiedad personalizada en la cuenta de Azure AD B2C.

### <a name="41-application-settings"></a>4.1 Configuración de la aplicación
Para probar la API de REST de demostración. Abra la solución de Visual Studio `AADB2C.UserMigration.sln` en Visual Studio. En el proyecto `AADB2C.UserMigration.API`, abra el archivo App.config. Reemplace las tres configuraciones de la aplicación por sus propios valores:

```XML
<appSettings>
    <add key="BlobStorageConnectionString" value="{The Azure Blob storage connection string"} />
</appSettings>
```

### <a name="step-42-deploy-your-web-application-to-azure-app-services"></a>Paso 4.2: Implementación de la aplicación web en Azure App Services
Publique el servicio de API en Azure App Services. Para más información, consulte [Implementación de la aplicación en Azure App Service](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-deploy).

### <a name="step-43-add-technical-profile-and-technical-profile-validation-to-your-policy"></a>Paso 4.3: Adición del perfil técnico y validación del perfil técnico a la directiva 
1.  Abra el archivo de directiva de extensión (TrustFrameworkExtensions.xml) desde el directorio de trabajo. 
2. Busque la sección `<ClaimsProviders>`.
3. Agregue el siguiente fragmento de código XML en el elemento `ClaimsProviders`.
4. Cambie el valor de `ServiceUrl` para que apunte a la dirección URL del punto de conexión. 

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

<ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>

    <!-- This technical profile uses a validation technical profile to authenticate the user. -->
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="LocalAccountSignIn" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    <TechnicalProfile Id="LocalAccountWritePasswordUsingObjectId">
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="LocalAccountPasswordReset" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

El perfil técnico define una notificación de entrada: `signInName` (enviar como correo electrónico). En el inicio de sesión, la notificación se envía al punto de conexión de REST.

Después de definir el perfil técnico para la API de REST, indique a la directiva de Azure AD B2C que llame a ese perfil técnico. El fragmento XML invalida `SelfAsserted-LocalAccountSignin-Email`, que se define en la directiva de base. El fragmento XML también agrega `ValidationTechnicalProfile` con un valor de ReferenceId que apunta a su perfil técnico `LocalAccountUserMigration`. 

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Paso 4.4: Carga de la directiva en el inquilino
1.  En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y haga clic en **Azure AD B2C**.
2.  Seleccione **Marco de experiencia de identidad**.
3.  Haga clic en **Todas las directivas**.
4.  Seleccione **Cargar directiva**
5.  Active la casilla **Sobrescribir la directiva si existe**.
6.  **Cargue** TrustFrameworkExtensions.xml y asegúrese de que no se produce ningún error en la validación.
7.  Repita el último paso y cargue el archivo SignUpOrSignIn.xml.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Paso 4.5: Prueba de la directiva personalizada con Ejecutar ahora
1.  Abra la **configuración de Azure AD B2C** y vaya a **Marco de experiencia de identidad**.
2.  Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado. Seleccione **Ejecutar ahora**.
3. Intente iniciar sesión con uno de los usuarios migrados y haga clic en **Iniciar de sesión**. Debería ver el siguiente mensaje de error procedente de la API de REST.

    ![Establecer registros de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshooting-your-rest-api"></a>Paso 4.6: [Opcional] Solución de problemas de la API de REST
Puede supervisar y ver la información de registro prácticamente en tiempo real.

1. En el menú de configuración de la aplicación REST, desplácese hacia abajo hasta la sección **Supervisión** y haga clic en **Registros de diagnóstico**. 
2. Habilitar el registro de la aplicación (sistema de archivos) 
3. Establezca el **Nivel** en nivel **Detallado**
4. Haga clic en **Guardar**

    ![Establecer registros de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

5. En el menú Configuración, haga clic en **Transmisión de registro**
6. Compruebe la salida de la API de REST.

Para más información, consulte [Transmisión de registros y la consola](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-streaming-logs-and-console)

> [!IMPORTANT]
> Los __registros de diagnóstico__ deben utilizarse solo durante el desarrollo y pruebas. La salida de la API de REST puede contener información confidencial que no debe exponerse en producción.
>

## <a name="download-the-complete-policy-files"></a>Descarga de los archivos de directiva completos
Opcional: se recomienda que, en lugar de usar estos archivos de ejemplo, cree su escenario con sus propios archivos de directiva personalizada tras completar el tutorial de introducción a las directivas personalizadas.  [Archivos de directiva de ejemplo de referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration)