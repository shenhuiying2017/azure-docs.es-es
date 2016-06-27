<properties
   pageTitle="Creación de aplicaciones de AD con PowerShell | Microsoft Azure"
   description="Describe cómo usar Azure PowerShell para crear una aplicación de Active Directory y concederle acceso a recursos mediante el control de acceso basado en roles. Muestra cómo autenticar aplicaciones con una contraseña o un certificado."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# Uso de Azure PowerShell para crear una aplicación de Active Directory con el fin de acceder a recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI de Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)


En este tema se muestra cómo usar [Azure PowerShell](powershell-install-configure.md) para crear una aplicación de Active Directory (AD) , como un proceso automatizado, una aplicación o un servicio, que pueda tener acceso a otros recursos de la suscripción. Con Azure Resource Manager, puede usar el control de acceso basado en roles para conceder las acciones permitidas a la aplicación.

En este artículo, creará dos objetos: la aplicación de AD y la entidad de servicio. La aplicación de AD reside en el inquilino donde se registra la aplicación y define el proceso que se ejecutará. La entidad de servicio contiene la identidad de la aplicación de AD y se utiliza para asignar permisos. Desde la aplicación de AD puede crear muchas identidades de servicio. Para obtener una explicación más detallada de las aplicaciones y entidades de servicio, consulte [Objetos de aplicación y de entidad de servicio](./active-directory/active-directory-application-objects.md). Para obtener más información acerca de la autenticación de Active Directory, vea [Escenarios de autenticación en Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Tiene dos opciones para autenticar aplicaciones:

 - Contraseña: es adecuada cuando un usuario desea iniciar sesión interactivamente durante la ejecución.
 - Certificado: es adecuada para scripts desatendidos que deben autenticarse sin interacción del usuario.

## Creación de aplicaciones de AD con contraseña

En esta sección, llevará a cabo los pasos necesarios para crear la aplicación de AD con una contraseña.

1. Inicie sesión en su cuenta.

        Add-AzureRmAccount

1. Cree una nueva aplicación de Active Directory proporcionando un nombre para mostrar para la aplicación, el URI para una página que describe la aplicación (no se comprueba el vínculo), los URI que identifican la aplicación y la contraseña para la identidad de aplicación.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     Examine el nuevo objeto de aplicación.

        $azureAdApplication
        
     Tenga en cuenta que la propiedad **ApplicationId** es necesaria para crear entidades de servicio, asignar roles y obtener el token de acceso.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}


### Creación de entidades de servicio y asignación de roles

Desde la aplicación de AD debe crear un servicio principal y asignarle un rol.

1. Cree una entidad de servicio para la aplicación pasando el identificador de la aplicación de Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

2. Conceda los permisos de la entidad de servicio en su suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para el parámetro **ServicePrincipalName**, ofrezca el valor de **ApplicationId** o **IdentifierUris** que usó al crear la aplicación. Para más información sobre el control de acceso basado en roles, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

### Proporcionar credenciales a través de PowerShell de forma manual

Ha creado una aplicación de Active Directory y una entidad de servicio para esa aplicación. Ha asignado la entidad de servicio a un rol. Ahora, debe iniciar sesión como la aplicación para realizar operaciones. Puede proporcionar manualmente las credenciales de la aplicación al ejecutar comandos o scripts a petición.

1. Cree un nuevo objeto **PSCredential** que contiene las credenciales mediante la ejecución del comando **Get-Credential**.

        $creds = Get-Credential

2. Se le pedirá que escriba sus credenciales. Para el nombre de usuario, utilice el valor de **ApplicationId** o **IdentifierUris** que utilizó al crear la aplicación. Para la contraseña, use la que especificó al crear la cuenta.

     ![escribir credenciales](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

3. Recupere la suscripción en la que se creó la asignación de roles. Esta suscripción se utilizará para obtener el valor de **TenantId** del inquilino en el que reside la asignación de rol de la entidad de servicio.

        $subscription = Get-AzureRmSubscription

     Si la cuenta está vinculada a más de una suscripción, proporcione un nombre de suscripción o el id. para obtener la suscripción con la que desea trabajar.
     
        $subscription = Get-AzureRmSubscription -SubscriptionName "Azure MSDN - Visual Studio Ultimate"

4. Inicie sesión como la entidad de servicio especificando que esta cuenta es una entidad de servicio y proporcionando el objeto de credenciales.

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId
        
     Ahora está autenticado como la entidad de servicio para la aplicación de Active Directory que ha creado.

5. Para usar el token de acceso actual en una sesión posterior, puede guardar el perfil.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Puede abrir el perfil y examinar su contenido. Observe que contiene un token de acceso.
        
6. En lugar de iniciar sesión la próxima vez que se desee ejecutar código como la entidad de servicio, bastará con cargar el perfil.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] El token de acceso expirará; por tanto, el uso de un perfil guardado solo funciona mientras el token sea válido. Para ejecutar permanentemente scripts desatendidos, use un certificado.
        
## Creación de aplicaciones de AD con un certificado

En esta sección, llevará a cabo los pasos necesarios para crear una aplicación de AD con un certificado.

1. Creación de un certificado autofirmado. Si tiene Windows 10 o Windows Server 2016 Technical Preview, ejecute el siguiente comando: 

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
     Recibirá información sobre el certificado, incluida la huella digital.
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

        Thumbprint                                Subject
        ----------                                -------
        724213129BD2B950BB3F64FAB0C877E9348B16E9  CN=exampleapp

     Si no tiene Windows 10 o Windows Server 2016 Technical Preview,, descargue el script de PowerShell del [generador de certificado autofirmado](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6). Ejecute los siguientes comandos para generar un certificado.
     
        Import-Module -Name c:\New-SelfSignedCertificateEx.ps1
        New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
        $cert = Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp

2. Recupere el valor de clave del certificado.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

3. Inicie sesión en la cuenta de Azure.

        Add-AzureRmAccount

4. Cree una aplicación en el directorio.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
        
    Examine el nuevo objeto de aplicación.

        $azureAdApplication

    Tenga en cuenta que la propiedad **ApplicationId** es necesaria para crear entidades de servicio, asignar roles y obtener tokens de acceso.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}    


### Creación de entidades de servicio y asignación de roles

1. Cree una entidad de servicio para la aplicación pasando el identificador de la aplicación de Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

2. Conceda los permisos de la entidad de servicio en su suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para el parámetro **ServicePrincipalName**, ofrezca el valor de **ApplicationId** o **IdentifierUris** que usó al crear la aplicación. Para más información sobre el control de acceso basado en roles, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

### Preparación de valores para el script

En el script, pasará tres valores que son necesarios para iniciar sesión como la entidad de servicio. Necesitará:

- Id. de aplicación
- id. de inquilino 
- Huella digital de certificado

Ha visto el id. de aplicación y la huella digital de certificado en los pasos anteriores. Sin embargo, si necesita recuperar estos valores más tarde, los comandos se muestran a continuación, junto con el comando para obtener el id. de inquilino.

1. Para recuperar el id. de inquilino, use el siguiente comando:

        (Get-AzureRmSubscription).TenantId 

    También, si tiene más de una suscripción, proporcione el nombre de la suscripción:

        (Get-AzureRmSubscription -SubscriptionName "Azure MSDN - Visual Studio Ultimate").TenantId
        
2. Para recuperar el id. de aplicación, use el siguiente comando:

        (Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example").ApplicationId
        
3. Para recuperar la huella digital de certificado, use el siguiente comando:

        (Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp).Thumbprint

### Proporcionar un certificado a través de un script automatizado de PowerShell

Ha creado una aplicación de Active Directory y una entidad de servicio para esa aplicación. Ha asignado la entidad de servicio a un rol. Ahora, debe iniciar sesión como entidad de servicio para realizar operaciones como entidad de servicio.

Para realizar la autenticación en el script, especifique que la cuenta es una entidad de servicio y proporcione la huella digital de certificado, el id. de aplicación y el id. de inquilino.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint {thumbprint} -ApplicationId {applicationId} -TenantId {tenantid}

Ahora está autenticado como la entidad de servicio para la aplicación de Active Directory que ha creado.

## Pasos siguientes
  
- Si desea obtener ejemplos de .NET, consulte [SDK de Azure Resource Manager para .NET](resource-manager-net-sdk.md).
- Si desea consultar ejemplos de autenticación Java, consulte [SDK de Azure Resource Manager para Java](resource-manager-java-sdk.md). 
- Si desea obtener ejemplos de Python, consulte [Resource Management Authentication for Python](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html) (Autenticación de la administración de recursos para Python).
- Para obtener ejemplos de autenticación REST, consulte [API de REST de Resource Manager](resource-manager-rest-api.md).
- Si desea conocer los pasos detallados de la integración de una aplicación en Azure para administrar recursos, consulte [Guía para desarrolladores para la autorización con la API de Azure Resource Manager](resource-manager-api-authentication.md).

<!---HONumber=AcomDC_0615_2016-->