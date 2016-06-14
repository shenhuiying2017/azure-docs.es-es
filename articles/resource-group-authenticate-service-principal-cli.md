<properties
   pageTitle="Creación de aplicaciones de AD con la CLI de Azure | Microsoft Azure"
   description="Describe cómo usar la CLI de Azure para crear una aplicación de Active Directory y concederle acceso a recursos mediante el control de acceso basado en roles. Muestra cómo autenticar aplicaciones con una contraseña o un certificado."
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
   ms.date="05/26/2016"
   ms.author="tomfitz"/>

# Uso de la CLI de Azure para crear una aplicación de Active Directory con el fin de acceder a recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI de Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)

En este tema se muestra cómo usar la [CLI de Azure para Mac, Linux y Windows](xplat-cli-install.md) con el objetivo de crear una aplicación de Active Directory (AD) , como un proceso automatizado, una aplicación o un servicio, que pueda tener acceso a otros recursos de la suscripción. Con Azure Resource Manager, puede usar el control de acceso basado en roles para conceder las acciones permitidas a la aplicación.

En este artículo, creará dos objetos: la aplicación de AD y la entidad de servicio. La aplicación de AD reside en el inquilino donde se registra la aplicación y define el proceso que se ejecutará. La entidad de servicio contiene la identidad de la aplicación de AD y se utiliza para asignar permisos. Desde la aplicación de AD puede crear muchas identidades de servicio. Para obtener una explicación más detallada de las aplicaciones y entidades de servicio, consulte [Objetos de aplicación y de entidad de servicio](./active-directory/active-directory-application-objects.md). Para obtener más información acerca de la autenticación de Active Directory, vea [Escenarios de autenticación en Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Tiene dos opciones para autenticar aplicaciones:

 - Contraseña: es adecuada cuando un usuario desea iniciar sesión interactivamente durante la ejecución.
 - Certificado: es adecuada para scripts desatendidos que deben autenticarse sin interacción del usuario.
 
## Creación de aplicaciones de AD con contraseña

En esta sección, llevará a cabo los pasos necesarios para crear la aplicación de AD con una contraseña.

1. Cambie al modo de Administrador de recursos de Azure e inicie sesión en su cuenta.

        azure config mode arm
        azure login

2. Cree una nueva aplicación de AD ejecutando el comando **azure ad app create**. Proporcione un nombre para mostrar para la aplicación, el URI para una página que describe la aplicación (no se comprueba el vínculo), los URI que identifican la aplicación y la contraseña para la identidad de aplicación.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Se devuelve la aplicación de AD. La propiedad AppId es necesaria para crear entidades de servicio, asignar roles y obtener tokens de acceso.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

### Creación de entidades de servicio y asignación de roles

1. Cree a una entidad de servicio para la aplicación. Proporcione el identificador de la aplicación que se devolvió en el paso anterior.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    Se devuelve la nueva entidad de servicio. Cuando se conceden permisos, es necesario el identificador de objeto.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK

2. Conceda los permisos de la entidad de servicio en su suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para más información sobre el control de acceso basado en roles, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### Proporcionar credenciales a través de la CLI de Azure de forma manual

Ha creado una aplicación de AD y una entidad de servicio para esa aplicación. Ha asignado la entidad de servicio a un rol. Ahora, debe iniciar sesión como la aplicación para realizar operaciones. Puede proporcionar manualmente las credenciales de la aplicación al ejecutar comandos o scripts a petición.

1. Determine el valor de **TenantId** de la suscripción que contiene la entidad de servicio. Si va a recuperar el identificador de inquilino de su suscripción autenticada actualmente, no necesita proporcionar el identificador de suscripción como parámetro. El modificador **-r** recupera el valor sin las comillas.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

2. Para el nombre de usuario, use el valor de **AppId** que utilizó al crear la entidad de servicio. Si necesita recuperar el identificador de aplicación, use el siguiente comando. Proporcione el nombre de la aplicación de Active Directory en el parámetro **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. Inicie sesión como la entidad de servicio:

        azure login -u "$appId" --service-principal --tenant "$tenantId"

    Se le pedirá la contraseña. Proporcione la contraseña que especificó al crear la aplicación de AD.

        info:    Executing command login
        Password: ********

Ahora está autenticado como la entidad de servicio de la aplicación de AD que ha creado.

## Creación de aplicaciones de AD con un certificado

En esta sección llevará a cabo los pasos necesarios para crear una entidad de servicio de una aplicación de AD que usa un certificado con el fin de realizar la autenticación. Para completar estos pasos, debe tener instalado [OpenSSL](http://www.openssl.org/).

1. Creación de un certificado autofirmado.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Combine las claves públicas y privadas.

        cat privkey.pem cert.pem > examplecert.pem

3. Abra el archivo **examplecert.pem** y copie los datos del certificado. Busque la secuencia larga de caracteres entre **-----BEGIN CERTIFICATE-----** y **-----END CERTIFICATE-----**.

4. Cree una nueva aplicación de Active Directory ejecutando el comando **azure ad app create** e indique los datos del certificado que copió en el paso anterior como el valor de la clave.

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    Se devuelve la aplicación de Active Directory. La propiedad AppId es necesaria para crear entidades de servicio, asignar roles y obtener tokens de acceso.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

### Creación de entidades de servicio y asignación de roles

1. Cree a una entidad de servicio para la aplicación. Proporcione el identificador de la aplicación que se devolvió en el paso anterior.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    Se devuelve la nueva entidad de servicio. Cuando se conceden permisos, es necesario el identificador de objeto.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Conceda los permisos de la entidad de servicio en su suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para más información sobre el control de acceso basado en roles, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### Preparación de valores para el script

En el script, pasará tres valores que son necesarios para iniciar sesión como la entidad de servicio. Necesitará:

- Id. de aplicación
- id. de inquilino 
- Huella digital de certificado

Ha visto el id. de aplicación y la huella digital de certificado en los pasos anteriores. Sin embargo, si necesita recuperar estos valores más tarde, los comandos se muestran a continuación, junto con el comando para obtener el id. de inquilino.

1. Para recuperar la huella digital de certificado y quitar los caracteres innecesarios, use el siguiente comando:

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     De este modo, se devuelve un valor de huella digital similar al siguiente:

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Para recuperar el id. de inquilino, use el siguiente comando:

        azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId'

3. Para recuperar el id. de aplicación, use el siguiente comando:

        azure ad app show --search exampleapp --json | jq -r '.[0].appId'

### Proporcionar un certificado a través de un script automatizado de la CLI de Azure

Ha creado una aplicación de Active Directory y una entidad de servicio para esa aplicación. Ha asignado la entidad de servicio a un rol. Ahora, debe iniciar sesión como entidad de servicio para realizar operaciones como entidad de servicio.

Para autenticar con la CLI de Azure, proporcione la huella digital del certificado, el archivo del certificado, el identificador de la aplicación y el identificador del inquilino.

        azure login --service-principal --tenant {tenant-id} -u {app-id} --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

Ahora está autenticado como la entidad de servicio para la aplicación de Active Directory que ha creado.

## Pasos siguientes
  
- Si desea obtener ejemplos de .NET, consulte [SDK de Azure Resource Manager para .NET](resource-manager-net-sdk.md).
- Si desea consultar ejemplos de autenticación Java, consulte [SDK de Azure Resource Manager para Java](resource-manager-java-sdk.md). 
- Si desea obtener ejemplos de Python, consulte [Resource Management Authentication for Python](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html) (Autenticación de la administración de recursos para Python).
- Para obtener ejemplos de autenticación REST, consulte [API de REST de Resource Manager](resource-manager-rest-api.md).
- Si desea conocer los pasos detallados de la integración de una aplicación en Azure para administrar recursos, consulte [Guía para desarrolladores para la autorización con la API de Azure Resource Manager](resource-manager-api-authentication.md).
- Para obtener más información sobre el uso de certificados y la CLI de Azure, consulte [Certificate-based auth with Azure Service Principals from Linux command line](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx) (Autenticación basada en certificados con entidades de servicio de Azure desde la línea de comandos de Linux). 

<!---HONumber=AcomDC_0601_2016-->