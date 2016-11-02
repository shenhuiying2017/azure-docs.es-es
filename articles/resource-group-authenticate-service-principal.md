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
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# Uso de Azure PowerShell para crear a una entidad de servicio para acceder a recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI de Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)

Cuando tenga una aplicación o un script que necesite acceder a recursos, lo más probable es que no desee ejecutar este proceso con sus credenciales. Es posible que haya elegido permisos diferentes para la aplicación y no desea que la aplicación continúe usando sus credenciales si cambian sus responsabilidades. En su lugar, crea una identidad para la aplicación que incluye credenciales de autenticación y asignaciones de roles. Cada vez que se ejecuta la aplicación, se autentica con estas credenciales. En este tema se explica cómo usar [Azure PowerShell](powershell-install-configure.md) para configurar todo lo que necesita para que una aplicación se ejecute con sus propias credenciales e identidad.

Con PowerShell, tiene dos opciones para autenticar aplicaciones de AD:

 - contraseña
 - certificado

Este tema muestra cómo usar ambas opciones en PowerShell. Si va a iniciar sesión en Azure desde otro entorno de programación (como Python, Ruby o Node.js), la mejor opción es la autenticación con contraseña. Antes de decidir si desea utilizar una contraseña o un certificado, consulte la sección [Aplicaciones de ejemplo](#sample-applications) para obtener ejemplos de autenticación en los distintos marcos.

## Conceptos de Active Directory

En este artículo, se crean dos objetos: la aplicación de Active Directory (AD) y la entidad de servicio. La aplicación de AD es la representación global de su aplicación. Contiene las credenciales (un identificador de aplicación y una contraseña o certificado). La entidad de servicio es la representación local de su aplicación en una instancia de Active Directory. Contiene la asignación de rol. Este tema se centra en una aplicación de inquilino único donde la aplicación está diseñada para ejecutarse en una sola organización. Normalmente, utiliza aplicaciones de inquilino único para aplicaciones de línea de negocio que se ejecutan dentro de su organización. En una aplicación de un solo inquilino, tendrá una aplicación de AD y una entidad de servicio.

Quizás se pregunte por qué necesita ambos objetos. Este enfoque tiene más sentido para las aplicaciones multiinquilino. Las aplicaciones multiinquilino normalmente se usan para las aplicaciones de software como servicio (SaaS), que se ejecutan en muchas suscripciones diferentes. Para las aplicaciones multiinquilino, tendrá una aplicación de AD y varias entidades de servicio (una en cada instancia de Active Directory que concede acceso a la aplicación). Para configurar una aplicación multiinquilino, consulte [Guía del desarrollador para la autorización con la API de Azure Resource Manager](resource-manager-api-authentication.md).

## Permisos necesarios

Para completar este tema, debe tener permisos suficientes tanto en su suscripción de Azure como en Azure Active Directory. En concreto, debe poder crear una aplicación en Active Directory y asignar la entidad de servicio a un rol.

En su instancia de Active Directory, su cuenta debe ser un administrador (como **Administrador Global** o **Usuario Admin**). Si su cuenta está asignada al rol **Usuario** rol, necesita que un administrador eleve los permisos.

En su suscripción, su cuenta debe tener acceso a `Microsoft.Authorization/*/Write`, que se concede mediante el rol [Propietario](./active-directory/role-based-access-built-in-roles.md#owner) o el rol [Administrador de acceso de usuario](./active-directory/role-based-access-built-in-roles.md#user-access-administrator). Si su cuenta está asignada al rol **Colaborador**, recibirá un error al intentar asignar la entidad de servicio a un rol. De nuevo, el administrador de la suscripción debe concederle acceso suficiente.

Ahora, continúe en la sección sobre autenticación mediante [contraseña](#create-service-principal-with-password) o [certificado](#create-service-principal-with-certificate).

## Creación de entidad de servicio con contraseña

Estos son los pasos que se realizan en esta sección:

- Creación de la aplicación de AD con contraseña.
- Creación de la entidad de servicio.
- Asignación del rol de lector a la entidad de servicio.

Para realizar rápidamente estos pasos, use los tres cmdlets siguientes.

     $app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
     New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
     New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Analicemos estos pasos con más atención para asegurarnos de que entiende el proceso.

1. Inicie sesión en su cuenta.

        Add-AzureRmAccount

1. Cree una nueva aplicación de Active Directory; para ello, proporcione un nombre para mostrar, el URI que describe la aplicación, los URI que identifican la aplicación y la contraseña de la identidad de aplicación.

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "<Your_Password>"

     En el caso de las aplicaciones de un solo inquilino, no se validan los URI.
     
     Si su cuenta no tiene los [permisos necesarios](#required-permissions) en Active Directory, verá un mensaje de error que indica "Authentication\_Unauthorized" o "No se encontró ninguna suscripción en el contexto".

1. Examine el nuevo objeto de aplicación.

        $app
        
     Tenga en cuenta, en particular, que la propiedad **ApplicationId** es necesaria para crear entidades de servicio, asignar roles y obtener el token de acceso.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}

2. Cree una entidad de servicio para la aplicación pasando el identificador de la aplicación de Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

3. Conceda los permisos de la entidad de servicio en la suscripción. En este ejemplo, se agrega la entidad de servicio al rol **Lector**, que concede permiso para leer todos los recursos de la suscripción. Para obtener información sobre otros roles, consulte [RBAC: Roles integrados](./active-directory/role-based-access-built-in-roles.md). Para el parámetro **ServicePrincipalName**, indique el valor de **ApplicationId** que usó al crear la aplicación.

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Si su cuenta no tiene permisos suficientes para asignar un rol, verá un mensaje de error. El mensaje indica que la cuenta **no tiene autorización para realizar la acción 'Microsoft.Authorization/roleAssignments/write' en el ámbito '/subscriptions/{guid}'**.

Eso es todo. La aplicación de AD y la entidad de servicio ya están configuradas. La sección siguiente muestra cómo iniciar sesión con las credenciales mediante PowerShell. Si desea usar las credenciales en la aplicación de código, puede ir directamente a las [aplicaciones de ejemplo](#sample-applications).

### Proporcionar credenciales a través de PowerShell

Ahora, debe iniciar sesión como la aplicación para realizar operaciones.

1. Cree un objeto **PSCredential** que contiene las credenciales; para ello, ejecute el comando **Get-Credential**. Necesitará el valor de **ApplicationId** antes de ejecutar este comando así que asegúrese de que lo tiene disponible para pegar.

        $creds = Get-Credential

2. Se le pedirá que escriba sus credenciales. Para el nombre de usuario, utilice el valor de **ApplicationId** que utilizó al crear la aplicación. Para la contraseña, use la que especificó al crear la cuenta.

     ![escribir credenciales](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

2. Cada vez que inicie sesión como entidad de servicio, debe proporcionar el id. del inquilino del directorio para la aplicación de AD. Un inquilino es una instancia de Active Directory. Si solo tiene una suscripción, puede utilizar:

        $tenant = (Get-AzureRmSubscription).TenantId
    
     Si tiene varias suscripciones, especifique la suscripción donde reside su instancia de Active Directory. Para más información, consulte [Asociación de las suscripciones de Azure con Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

4. Inicie sesión como la entidad de servicio especificando que esta cuenta es una entidad de servicio, y proporcione el objeto de credenciales.

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     Ahora está autenticado como la entidad de servicio para la aplicación de Active Directory que ha creado.

### Guardado del token de acceso para simplificar el inicio de sesión

Para evitar tener que especificar las credenciales de la entidad de servicio cada vez que necesite iniciar sesión, puede guardar el token de acceso.

1. Para usar el token de acceso actual en una sesión posterior, guarde el perfil.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Abra el perfil y examine su contenido. Observe que contiene un token de acceso.
        
2. En lugar de tener que volver a iniciar sesión manualmente, solo tiene que cargar el perfil.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] El token de acceso expirará y, por lo tanto, el uso de un perfil guardado solo funcionará mientras el token sea válido.
        
## Creación de entidad de servicio con certificado

Estos son los pasos que se realizan en esta sección:

- Creación de un certificado autofirmado.
- Creación de aplicaciones de AD con el certificado.
- Creación de la entidad de servicio.
- Asignación del rol de lector a la entidad de servicio.

Para realizar rápidamente estos pasos con Azure PowerShell 2.0 en Windows 10 o Windows Server 2016 Technical Preview, consulte los siguientes cmdlets.

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Analicemos estos pasos con más atención para asegurarnos de que entiende el proceso. Este artículo también muestra cómo llevar a cabo las tareas con versiones anteriores de los sistemas operativos o de Azure PowerShell.

### Creación del certificado autofirmado

La versión de PowerShell disponible con Windows 10 y Windows Server 2016 Technical Preview tiene un cmdlet **New-SelfSignedCertificate** actualizado para generar un certificado autofirmado. Los sistemas operativos anteriores tienen el cmdlet New-SelfSignedCertificate, pero no proporciona los parámetros necesarios para este tema. En su lugar, debe importar un módulo para generar el certificado. Este tema muestra ambos enfoques para generar el certificado en función del sistema operativo que tenga.

- Si tiene **Windows 10 o Windows Server 2016 Technical Preview**, ejecute el siguiente comando para crear un certificado autofirmado:

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
- Si [no tiene Windows 10 o Windows Server 2016 Technical Preview](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/), descargue el **generador de certificados autofirmados** del sitio Microsoft Script Center. Extraiga el contenido e importe el cmdlet que necesita.
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
    
     Después, genere el certificado.
    
        $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"

Ya tiene el certificado y puede continuar con la creación de la aplicación de AD.

### Creación de la aplicación de Active Directory y una entidad de servicio

1. Recupere el valor de clave del certificado.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Inicie sesión en la cuenta de Azure.

        Add-AzureRmAccount

3. Cree una nueva aplicación de Active Directory; para ello, proporcione un nombre para mostrar, el URI que describe la aplicación, los URI que identifican la aplicación y la contraseña de la identidad de aplicación.

     Si tiene Azure PowerShell 2.0 (agosto 2016 o versiones posteriores), use el siguiente cmdlet:

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Si tiene Azure PowerShell 1.0, use el siguiente cmdlet:
    
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    En el caso de las aplicaciones de un solo inquilino, no se validan los URI.
    
    Si su cuenta no tiene los [permisos necesarios](#required-permissions) en Active Directory, verá un mensaje de error que indica "Authentication\_Unauthorized" o "No se encontró ninguna suscripción en el contexto".
        
    Examine el nuevo objeto de aplicación.

        $app

    Tenga en cuenta que la propiedad **ApplicationId** es necesaria para crear entidades de servicio, asignar roles y obtener tokens de acceso.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}


5. Cree una entidad de servicio para la aplicación pasando el identificador de la aplicación de Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

6. Conceda los permisos de la entidad de servicio en la suscripción. En este ejemplo, se agrega la entidad de servicio al rol **Lector**, que concede permiso para leer todos los recursos de la suscripción. Para obtener información sobre otros roles, consulte [RBAC: Roles integrados](./active-directory/role-based-access-built-in-roles.md). Para el parámetro **ServicePrincipalName**, indique el valor de **ApplicationId** que usó al crear la aplicación.

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Si su cuenta no tiene permisos suficientes para asignar un rol, verá un mensaje de error. El mensaje indica que la cuenta **no tiene autorización para realizar la acción 'Microsoft.Authorization/roleAssignments/write' en el ámbito '/subscriptions/{guid}'**.

Eso es todo. La aplicación de AD y la entidad de servicio ya están configuradas. La sección siguiente muestra cómo iniciar sesión con el certificado a través de PowerShell.

### Proporcionar un certificado a través de un script automatizado de PowerShell

Cada vez que inicie sesión como entidad de servicio, debe proporcionar el id. del inquilino del directorio para la aplicación de AD. Un inquilino es una instancia de Active Directory. Si solo tiene una suscripción, puede utilizar:

    $tenant = (Get-AzureRmSubscription).TenantId
    
Si tiene varias suscripciones, especifique la suscripción donde reside su instancia de Active Directory. Para más información, consulte [Administración del directorio de Azure AD](./active-directory/active-directory-administer.md).

    $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

Para realizar la autenticación en el script, especifique que la cuenta es una entidad de servicio y proporcione la huella digital de certificado, el id. de aplicación y el id. de inquilino. Para automatizar el script, puede almacenar estos valores como variables de entorno y recuperarlos durante la ejecución o puede incluirlos en el script.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant

Ahora está autenticado como la entidad de servicio para la aplicación de Active Directory que ha creado.

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
- Para obtener una explicación más detallada de las aplicaciones y entidades de servicio, consulte [Objetos de aplicación y de entidad de servicio](./active-directory/active-directory-application-objects.md).
- Para obtener más información acerca de la autenticación de Active Directory, vea [Escenarios de autenticación en Azure AD](./active-directory/active-directory-authentication-scenarios.md).

<!---HONumber=AcomDC_0914_2016-->