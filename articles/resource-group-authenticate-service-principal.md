<properties
   pageTitle="Autenticación de una entidad de servicio con el Administrador de recursos de Azure"
   description="Describe cómo conceder acceso a una entidad de servicio a través del control de acceso basado en rol y autenticarla. Muestra cómo realizar estas tareas con PowerShell y la CLI de Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="11/18/2015"
   ms.author="tomfitz"/>

# Autenticación de una entidad de servicio con el Administrador de recursos de Azure

En este tema se muestra cómo permitir que una entidad de servicio (por ejemplo, un proceso, una aplicación o un servicio automatizado) tenga acceso a otros recursos de la suscripción. Con el Administrador de recursos de Azure, puede usar el control de acceso basado en rol para conceder las acciones permitidas a una entidad de servicio y autenticar a esa entidad de servicio. En este tema se muestra cómo usar PowerShell y la CLI de Azure para asignar un rol a una entidad de servicio y autenticar la entidad de servicio.

Muestra cómo autenticar con un nombre de usuario y contraseña o un certificado.

Puede utilizar Azure PowerShell o CLI de Azure para Mac, Linux y Windows. Si no tiene instalado Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](./powershell-install-configure.md). Si no tiene la CLI de Azure instalada, consulte [Instalación y configuración de la interfaz de la línea de comandos de Azure](xplat-cli-install.md). Para información sobre el uso del portal para llevar a cabo estos pasos, vea [Creación de aplicación de Active Directory y entidad de servicio mediante el portal](resource-group-create-service-principal-portal.md).

## Conceptos
1. Azure Active Directory (AAD): un servicio de administración de identidades y acceso para la nube. Para obtener más información, consulte [¿Qué es Azure Active Directory?](active-directory/active-directory-whatis.md)
2. Entidad de servicio: una instancia de una aplicación en un directorio que necesita acceder a otros recursos.
3. Aplicación de AD: un registro de directorio que identifica una aplicación en AAD. Para obtener más información, consulte [Conceptos básicos sobre autenticación en Azure AD](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).

## Autenticar entidad de servicio con contraseña: PowerShell

En esta sección, llevará a cabo los pasos para crear una entidad de servicio para una aplicación de Azure Active Directory, asignar un rol a la entidad de servicio y autenticarse como la entidad de servicio proporcionando el identificador de la aplicación y la contraseña.

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

1. Inicie sesión en su cuenta.

        PS C:\> Login-AzureRmAccount

1. Cree una nueva aplicación de AAD ejecutando el comando **New-AzureRmADApplication**. Proporcione un nombre para mostrar para la aplicación, el URI para una página que describe la aplicación (no se comprueba el vínculo), los URI que identifican la aplicación y la contraseña para la identidad de aplicación.

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     Examine el nuevo objeto de aplicación. La propiedad **ApplicationId** es necesaria para la creación de entidades de servicio, las asignaciones de roles y la adquisición de tokens JWT.

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}


2. Cree a una entidad de servicio para la aplicación.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     Ahora ha creado una entidad de servicio en el directorio, pero el servicio no tiene asignado ningún permiso o ámbito. Debe conceder explícitamente permisos a la entidad de servicio a fin de realizar operaciones en cierto ámbito.

3. Conceda los permisos de la entidad de servicio en su suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para el parámetro **ServicePrincipalName**, proporcione el valor de **ApplicationId** o **IdentifierUris** que utilizó al crear la aplicación. Para obtener más información sobre el control de acceso basado en rol, consulte [Administración y auditoría de acceso a recursos](resource-group-rbac.md).

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. Recupere la suscripción en la que se creó la asignación de roles. Esta suscripción se utilizará más adelante para obtener el valor de **TenantId** del inquilino en el que reside la asignación de rol de la entidad de servicio.

        PS C:\> $subscription = Get-AzureRmSubscription

     Si ha creado la asignación de rol en una suscripción que no sea la suscripción seleccionada actualmente, puede especificar los parámetros **SubscriptoinId** o **SubscriptionName** para recuperar una suscripción diferente.

5. Para iniciar sesión como la entidad de servicio a través de PowerShell, cree un nuevo objeto **PSCredential** que contiene las credenciales mediante la ejecución del comando **Get-Credential**.

        PS C:\> $creds = Get-Credential

     Se le pedirá que escriba sus credenciales.

     ![][1]

     Para el nombre de usuario, utilice el valor de **ApplicationId** o **IdentifierUris** que utilizó al crear la aplicación. Para la contraseña, use la que especificó al crear la cuenta.

     Use las credenciales que especificó como entrada para el cmdlet **Login-AzureRmAccount**, que firmará la entidad de servicio en:

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

     Ahora debe autenticarse como la entidad de servicio para la aplicación de AAD que ha creado.

6. Para autenticarse en una aplicación, incluya el código .NET siguiente. Después de recuperar el token, puede tener acceso a recursos de la suscripción.

        public static string GetAccessToken()
        {
          var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
          var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
          var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

          if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }



## Autenticar entidad de servicio con certificado: PowerShell

En esta sección, llevará a cabo los pasos para crear una entidad de servicio para una aplicación de Azure Active Directory, asignar un rol a la entidad de servicio y autenticarse como la entidad de servicio proporcionando un certificado. En este tema se supone que se le ha emitido un certificado.

Muestra dos maneras de trabajar con certificados: credenciales de clave y valores de clave. Puede utilizar cualquier enfoque.

En primer lugar, debe configurar algunos valores en PowerShell que utilizará más adelante al crear la aplicación.

1. Inicie sesión en su cuenta.

        PS C:\> Login-AzureRmAccount

1. Para ambos enfoques, cree un objeto X509Certificate desde su certificado y recupere el valor de clave. Utilice la ruta de acceso a su certificado y la contraseña de ese certificado.

        $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Si usa credenciales de clave, cree el objeto de credenciales de clave y establezca su valor en `$keyValue` del paso anterior.

        $currentDate = Get-Date
        $endDate = $currentDate.AddYears(1)
        $keyId = [guid]::NewGuid()
        $keyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $keyCredential.StartDate = $currentDate
        $keyCredential.EndDate= $endDate
        $keyCredential.KeyId = $keyId
        $keyCredential.Type = "AsymmetricX509Cert"
        $keyCredential.Usage = "Verify"
        $keyCredential.Value = $keyValue

3. Cree una aplicación en el directorio. El primer comando muestra cómo utilizar valores de clave.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    O bien, utilice el segundo ejemplo para asignar credenciales de clave.

         $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyCredentials $keyCredential

    Examine el nuevo objeto de aplicación. La propiedad **ApplicationId** es necesaria para la creación de entidades de servicio, las asignaciones de roles y la adquisición de tokens JWT.

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : 76fa8d97-f07e-4b9a-b871-a57a7acd777a
        ApplicationObjectId     : c36b7b57-a949-4401-b381-18a5210aff10
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in
                          user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "9f13c6c6-35ba-43d6-b8b3-6a87aa641388",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}

4. Cree a una entidad de servicio para la aplicación.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    Ahora ha creado una entidad de servicio en el directorio, pero el servicio no tiene asignado ningún permiso o ámbito. Debe conceder explícitamente permisos a la entidad de servicio a fin de realizar operaciones en cierto ámbito.

5. Conceda los permisos de la entidad de servicio en su suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para el parámetro **ServicePrincipalName**, proporcione el valor de **ApplicationId** o **IdentifierUris** que utilizó al crear la aplicación. Para obtener más información sobre el control de acceso basado en rol, consulte [Administración y auditoría de acceso a recursos](resource-group-rbac.md).

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

6. Para autenticarse en una aplicación, incluya el código .NET siguiente. Tras la recuperación del cliente, puede tener acceso a recursos de la suscripción.

        string clientId = "<Application ID for your AAD app>"; 
        var subscriptionId = "<Your Azure SubscriptionId>"; 
        string tenant = "<Tenant id or tenant name>"; 

        var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

        X509Certificate2 cert = null; 
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
        string certName = "examplecert"; 
        try 
        { 
            store.Open(OpenFlags.ReadOnly); 
            var certCollection = store.Certificates; 
            var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
            cert = certs[0]; 
        } 
        finally 
        { 
            store.Close(); 
        }        

        var certCred = new ClientAssertionCertificate(clientId, cert); 
        var token = authContext.AcquireToken("https://management.core.windows.net/", certCred); 
        var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken); 
        var client = new ResourceManagementClient(creds); 
        

## Autenticar entidad de servicio con contraseña: CLI de Azure

Comenzará creando una entidad de servicio. Para ello, debemos crear una aplicación en el directorio. Esta sección le guiará a través de la creación de una nueva aplicación en el directorio.

1. Cambie al modo de Administrador de recursos de Azure e inicie sesión en su cuenta.

        azure config mode arm
        azure login

2. Cree una nueva aplicación de AAD ejecutando el comando **azure ad app create**. Proporcione un nombre para mostrar para la aplicación, el URI para una página que describe la aplicación (no se comprueba el vínculo), los URI que identifican la aplicación y la contraseña para la identidad de aplicación.

        azure ad app create --name "<Your Application Display Name>" --home-page "<https://YourApplicationHomePage>" --identifier-uris "<https://YouApplicationUri>" --password <Your_Password>
        
    Se devuelve a la aplicación de Azure AD. La propiedad ApplicationId es necesaria para la creación de entidades de servicio, las asignaciones de roles y la adquisición de tokens JWT.

        info:    Executing command ad app create
        + Creating application exampleapp                                                
        data:    Application Id:          b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Application Object Id:   d5c519e2-6149-447e-b323-88d2c4ea27de
        data:    Application Permissions:  
        data:                             claimValue:  user_impersonation
        data:                             description:  Allow the application to access exampleapp on behalf of the signed-in user.
        ...
        info:    ad app create command OK

3. Cree a una entidad de servicio para la aplicación. Proporcione el identificador de la aplicación que se devolvió en el paso anterior.

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec
        
    Se devuelve la nueva entidad de servicio. Cuando se conceden permisos, es necesario el identificador de objeto.
    
        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    Ahora ha creado una entidad de servicio en el directorio, pero el servicio no tiene asignado ningún permiso o ámbito. Debe conceder explícitamente permisos a la entidad de servicio a fin de realizar operaciones en cierto ámbito.

4. Conceda los permisos de la entidad de servicio en su suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para el parámetro **ServicePrincipalName**, proporcione el valor de **ApplicationId** o **IdentifierUris** que utilizó al crear la aplicación. Para obtener más información sobre el control de acceso basado en rol, consulte [Administración y auditoría de acceso a recursos](resource-group-rbac.md).

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

5. Determine el valor **TenantId** del inquilino en el que reside la asignación del rol de la entidad de servicio mostrando las cuentas y buscando la propiedad **TenantId** en la salida.

        azure account list --json

6. Inicie sesión utilizando la entidad de servicio como su identidad. Para el nombre de usuario, utilice el valor de **ApplicationId** que utilizó al crear la aplicación. Para la contraseña, use la que especificó al crear la cuenta.

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    Ahora debe autenticarse como la entidad de servicio para la aplicación de AAD que ha creado.

## Autenticar entidad de servicio con certificado: CLI de Azure

En esta sección llevará a cabo los pasos para crear una entidad de servicio para una aplicación Azure Active Directory que usa un certificado para la autenticación. En este tema se supone que se le ha emitido un certificado y que ha instalado [OpenSSL](http://www.openssl.org/).

1. Cree un archivo **.pem** con:

        openssl.exe pkcs12 -in examplecert.pfx -out examplecert.pem -nodes

2. Abra el archivo **.pem** y copie los datos del certificado.

3. Para crear una nueva aplicación de AAD, ejecute el comando **azure ad app create** y ofrezca los datos del certificado que ha copiado en el paso anterior como el valor de clave.

        azure ad app create -n "<your application name>" --home-page "<https://YourApplicationHomePage>" -i "<https://YouApplicationUri>" --key-value <certificate data>

## Pasos siguientes
  
- Para obtener información general sobre el control de acceso basado en roles, consulte [Administración y auditoría del acceso a los recursos](resource-group-rbac.md).  
- Para obtener información sobre cómo usar el portal con entidades de seguridad de servicio, consulte [Creación de una entidad de servicio de Azure mediante el Portal de Azure](./resource-group-create-service-principal-portal.md).  
- Para obtener instrucciones sobre cómo implementar la seguridad con el Administrador de recursos de Azure, consulte [Consideraciones de seguridad para el Administrador de recursos de Azure](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=AcomDC_1203_2015-->