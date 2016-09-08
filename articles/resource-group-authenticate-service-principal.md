<properties
   pageTitle="Creación de una entidad de servicio de Azure con PowerShell | Microsoft Azure"
   description="Describe cómo usar Azure PowerShell para crear una aplicación de Active Directory y una entidad de servicio, además de cómo concederle acceso a recursos mediante el control de acceso basado en roles. Muestra cómo autenticar aplicaciones con una contraseña o un certificado."
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
   ms.date="07/18/2016"
   ms.author="tomfitz"/>

# Uso de Azure PowerShell para crear a una entidad de servicio para acceder a recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI de Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)

Cuando tenga una aplicación o un script que necesite acceder a recursos, lo más probable es que no desee ejecutar este proceso con las credenciales del usuario. Ese usuario puede tener permisos diferentes que desearía asignar al proceso y las responsabilidades del trabajo del usuario podrían cambiar. En su lugar, puede crear una identidad para la aplicación que incluye credenciales de autenticación y asignaciones de roles. La aplicación inicia sesión con esta identidad cada vez que se ejecuta. En este tema se explica cómo usar [Azure PowerShell](powershell-install-configure.md) para configurar todo lo que necesita para que una aplicación se ejecute con sus propias credenciales e identidad.

En este artículo, creará dos objetos: la aplicación de Active Directory (AD) y la entidad de servicio. La aplicación de AD contiene las credenciales (un id. de aplicación y una contraseña o certificado). La entidad de servicio contiene la asignación de roles. Desde la aplicación de AD puede crear muchas identidades de servicio. Este tema se centra en una aplicación de inquilino único donde la aplicación está diseñada para ejecutarse en una sola organización. Normalmente, utiliza aplicaciones de inquilino único para aplicaciones de línea de negocio que se ejecutan dentro de su organización. También puede crear aplicaciones multiinquilino cuando la aplicación debe ejecutarse en muchas organizaciones. En general, utiliza aplicaciones multiinquilino para aplicaciones de software como servicio (SaaS). Para configurar una aplicación multiinquilino, consulte [Guía del desarrollador para la autorización con la API de Azure Resource Manager](resource-manager-api-authentication.md).

Hay muchos conceptos que se deben entender al trabajar con Active Directory. Para obtener una explicación más detallada de las aplicaciones y entidades de servicio, consulte [Objetos de aplicación y de entidad de servicio](./active-directory/active-directory-application-objects.md). Para obtener más información acerca de la autenticación de Active Directory, vea [Escenarios de autenticación en Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Con PowerShell, tiene dos opciones para autenticar aplicaciones de AD:

 - contraseña
 - certificado

Si, después de configurar la aplicación de AD, va a iniciar sesión en Azure desde otro marco de programación (como Python, Ruby o Node.js), la autenticación con contraseña podría ser la mejor opción. Antes de decidir si desea utilizar una contraseña o un certificado, consulte la sección [Aplicaciones de ejemplo](#sample-applications) para obtener ejemplos de autenticación en los distintos marcos.

## Obtención del identificador de inquilino

Cada vez que inicie sesión como entidad de servicio, debe proporcionar el id. del inquilino del directorio para la aplicación de AD. Un inquilino es una instancia de Active Directory. Como va a necesitar ese valor para la autenticación con contraseña o con certificado, vamos a obtener ese valor ahora.

1. Inicie sesión en su cuenta.

        Add-AzureRmAccount

2. Si solo tiene una suscripción, puede utilizar:

        $tenant = (Get-AzureRmSubscription).TenantId
    
     Si tiene varias suscripciones, especifique la que desea usar para la aplicación de AD. Seleccione la suscripción en la que reside Active Directory. Para más información, consulte [Administración del directorio de Azure AD](./active-directory/active-directory-administer.md).

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

Ahora, prosiga en una sección posterior para autenticación mediante [contraseña](#create-service-principal-with-password) o [certificado](#create-service-principal-with-certificate).

## Creación de entidad de servicio con contraseña

En esta sección, llevará a cabo los pasos necesarios para crear la aplicación de AD y la entidad de servicio con una contraseña.

1. Cree una nueva aplicación de Active Directory proporcionando un nombre para mostrar para la aplicación, el URI para una página que describe la aplicación (no se comprueba el vínculo), los URI que identifican la aplicación y la contraseña para la identidad de aplicación.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     Examine el nuevo objeto de aplicación.

        $azureAdApplication
        
     Tenga en cuenta, en particular, que la propiedad **ApplicationId** es necesaria para crear entidades de servicio, asignar roles y obtener el token de acceso.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}


     Desde la aplicación de AD debe crear un servicio principal y asignarle un rol.

2. Cree una entidad de servicio para la aplicación pasando el identificador de la aplicación de Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

3. Conceda los permisos de la entidad de servicio en la suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para el parámetro **ServicePrincipalName**, ofrezca el valor de **ApplicationId** o **IdentifierUris** que usó al crear la aplicación. Para más información sobre el control de acceso basado en roles, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md). Para asignar un rol, debe tener acceso `Microsoft.Authorization/*/Write` que se concede a través del rol [Propietario](./active-directory/role-based-access-built-in-roles.md#owner) o del rol [Administrador de acceso de usuario](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

Eso es todo. La aplicación de AD y la entidad de servicio ya están configuradas. En la siguiente sección se muestra cómo iniciar sesión con las credenciales a través de PowerShell; sin embargo, si desea utilizar las credenciales en la aplicación de código, ya no necesita continuar con este tema. Puede ir a [Aplicaciones de ejemplo](#sample-applications) para obtener ejemplos de inicio de sesión con su Id. de aplicación y contraseña.

### Proporcionar credenciales a través de PowerShell

Ahora, debe iniciar sesión como la aplicación para realizar operaciones.

1. Cree un nuevo objeto **PSCredential** que contiene las credenciales mediante la ejecución del comando **Get-Credential**. Necesitará **ApplicationId** o **IdentifierUris** antes de ejecutar este comando así que asegúrese de que los tiene disponibles para pegar.

        $creds = Get-Credential

2. Se le pedirá que escriba sus credenciales. Para el nombre de usuario, utilice el valor de **ApplicationId** o **IdentifierUris** que utilizó al crear la aplicación. Para la contraseña, use la que especificó al crear la cuenta.

     ![escribir credenciales](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

4. Inicie sesión como la entidad de servicio especificando que esta cuenta es una entidad de servicio y proporcionando el objeto de credenciales. Necesitará el identificador de inquilino que recuperó en [Obtención del identificador de inquilino](#get-tenant-id).

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     Ahora está autenticado como la entidad de servicio para la aplicación de Active Directory que ha creado.

5. Para usar el token de acceso actual en una sesión posterior, puede guardar el perfil.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Puede abrir el perfil y examinar su contenido. Observe que contiene un token de acceso.
        
6. En lugar de iniciar sesión manualmente, la próxima vez que desee ejecutar código como la entidad de servicio, bastará con que cargue el perfil.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] El token de acceso expirará; por tanto, el uso de un perfil guardado solo funciona mientras el token sea válido.
        
## Creación de entidad de servicio con certificado

En esta sección, llevará a cabo los pasos necesarios para crear una aplicación de AD y una entidad de servicio con un certificado.

1. Creación de un certificado autofirmado. Si tiene **Windows 10 o Windows Server 2016 Technical Preview**, ejecute el siguiente comando:

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
     Su variable contiene información sobre el certificado, incluida la huella digital.
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

        Thumbprint                                Subject
        ----------                                -------
        724213129BD2B950BB3F64FAB0C877E9348B16E9  CN=exampleapp

     Si **no** tiene Windows 10 o Windows Server 2016 Technical Preview, no tendrá el cmdlet **SelfSignedCertificate nuevo**. En su lugar, descargue el script de PowerShell [Self-signed certificate generator](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6) (Generador de certificados autofirmados) y ejecute los siguientes comandos para generar un certificado. Este paso no es necesario si ya ha creado el certificado en el ejemplo anterior.
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\New-SelfSignedCertificateEx.ps1
        New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
        $cert = Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp

2. Recupere el valor de clave del certificado.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

4. Cree una aplicación en el directorio.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
        
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


5. Cree una entidad de servicio para la aplicación pasando el identificador de la aplicación de Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

6. Conceda los permisos de la entidad de servicio en la suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para el parámetro **ServicePrincipalName**, ofrezca el valor de **ApplicationId** o **IdentifierUris** que usó al crear la aplicación. Para más información sobre el control de acceso basado en roles, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md). Para asignar un rol, debe tener acceso `Microsoft.Authorization/*/Write` que se concede a través del rol [Propietario](./active-directory/role-based-access-built-in-roles.md#owner) o del rol [Administrador de acceso de usuario](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

Eso es todo. La aplicación de AD y la entidad de servicio ya están configuradas. La sección siguiente muestra cómo iniciar sesión con el certificado a través de PowerShell.

### Proporcionar un certificado a través de un script automatizado de PowerShell

Para realizar la autenticación en el script, especifique que la cuenta es una entidad de servicio y proporcione la huella digital de certificado, el id. de aplicación y el id. de inquilino. Ya tiene estos valores en las variables **$azureAdApplication.ApplicationId**, **$cert.Thumbprint** y **$tenant**. Para automatizar el script, puede almacenar estos valores como variables de entorno y recuperarlos durante la ejecución o puede incluirlos en el script.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint 000000 -ApplicationId 000000 -TenantId 0000000

Ahora está autenticado como la entidad de servicio para la aplicación de Active Directory que ha creado.

Si necesita recuperar el identificador de aplicación posteriormente, use:

    (Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example").ApplicationId
        
Si necesita recuperar la huella digital de certificado posteriormente, use:

    (Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp).Thumbprint

Si necesita recuperar el identificador de inquilino posteriormente, consulte [Obtención del identificador de inquilino](#get-tenant-id).

## Aplicaciones de ejemplo

Las aplicaciones de ejemplo siguientes muestran cómo iniciar sesión como entidad de servicio.

**.NET**

- [Implementación de una máquina virtual habilitada para SSH con una plantilla con .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Administración de recursos y grupos de recursos de Azure con .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Introducción a recursos - Implementación mediante la plantilla de Azure Resource Manager - en Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Introducción a recursos - Administración de grupo de recursos - en Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Implementación de una máquina virtual habilitada para SSH con una plantilla en Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Administración de recursos y grupos de recursos de Azure con Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [Implementación de una máquina virtual habilitada para SSH con una plantilla en Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Administración de recursos y grupos de recursos de Azure con Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Implementación de una máquina virtual habilitada para SSH con una plantilla en Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Administración de recursos y grupos de recursos de Azure con Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## Pasos siguientes
  
- Si desea conocer los pasos detallados de la integración de una aplicación en Azure para administrar recursos, consulte [Guía del desarrollador para la autorización con la API de Azure Resource Manager](resource-manager-api-authentication.md).

<!---HONumber=AcomDC_0824_2016-->