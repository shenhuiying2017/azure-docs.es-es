<properties
   pageTitle="Creación de entidad de servicio con la CLI de Azure | Microsoft Azure"
   description="Describe cómo usar la CLI de Azure para crear una aplicación de Active Directory y una entidad de servicio, además de cómo concederle acceso a recursos mediante el control de acceso basado en roles. Muestra cómo autenticar aplicaciones con una contraseña o un certificado."
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
   ms.date="07/19/2016"
   ms.author="tomfitz"/>

# Uso de la CLI de Azure para crear a una entidad de servicio para acceder a recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI de Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)

Cuando tenga una aplicación o un script que necesite acceder a recursos, lo más probable es que no desee ejecutar este proceso con las credenciales del usuario. Ese usuario puede tener permisos diferentes que desearía asignar al proceso y las responsabilidades del trabajo del usuario podrían cambiar. En su lugar, puede crear una identidad para la aplicación que incluye credenciales de autenticación y asignaciones de roles. La aplicación inicia sesión con esta identidad cada vez que se ejecuta. Este tema le muestra cómo usar [Uso de la CLI de Azure para Mac, Linux y Windows con el Administrador de recursos de Azure](xplat-cli-install.md) para configurar todo lo que necesita para que una aplicación se ejecute con sus propias credenciales e identidad.

En este artículo, creará dos objetos: la aplicación de Active Directory (AD) y la entidad de servicio. La aplicación de AD contiene las credenciales (un id. de aplicación y una contraseña o certificado). La entidad de servicio contiene la asignación de roles. Desde la aplicación de AD puede crear muchas identidades de servicio. Este tema se centra en una aplicación de inquilino único donde la aplicación está diseñada para ejecutarse en solo una organización. Normalmente, utiliza aplicaciones de inquilino único para aplicaciones de línea de negocio que se ejecutan dentro de su organización. También puede crear aplicaciones multiinquilino cuando la aplicación debe ejecutarse en muchas organizaciones. En general, utiliza aplicaciones multiinquilino para aplicaciones de software como servicio (SaaS). Para configurar una aplicación multiinquilino, consulte [Guía del desarrollador para la autorización con la API de Azure Resource Manager](resource-manager-api-authentication.md).

Hay muchos conceptos que se deben entender al trabajar con Active Directory. Para obtener una explicación más detallada de las aplicaciones y entidades de servicio, consulte [Objetos de aplicación y de entidad de servicio](./active-directory/active-directory-application-objects.md). Para obtener más información acerca de la autenticación de Active Directory, vea [Escenarios de autenticación en Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Con la CLI de Azure, tiene dos opciones para autenticar la aplicación de AD:

 - contraseña
 - certificado

Si, después de configurar la aplicación de AD, va a iniciar sesión en Azure desde otro marco de programación (como Python, Ruby o Node.js), la autenticación con contraseña podría ser la mejor opción. Antes de decidir si desea utilizar una contraseña o un certificado, consulte la sección [Aplicaciones de ejemplo](#sample-applications) para obtener ejemplos de autenticación en los distintos marcos.

## Obtención del identificador de inquilino

Cada vez que inicie sesión como entidad de servicio, debe proporcionar el id. del inquilino del directorio para la aplicación de AD. Un inquilino es una instancia de Active Directory. Como va a necesitar ese valor para la autenticación con contraseña o con certificado, vamos a obtener ese valor ahora.

1. Inicie sesión en su cuenta.

        azure config mode arm
        azure login

1. Si va a recuperar el identificador de inquilino de su suscripción autenticada actualmente, no necesita proporcionar el identificador de suscripción como parámetro. El modificador **-r** recupera el valor sin las comillas.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

Ahora, prosiga en una sección posterior para autenticación mediante [contraseña](#create-service-principal-with-password) o [certificado](#create-service-principal-with-certificate).


## Creación de entidad de servicio con contraseña

En esta sección, realizará los pasos para crear una entidad de servicio con una contraseña y asignarle un rol.

1. Cree a una entidad de servicio para la aplicación. Proporcione un nombre para mostrar para la aplicación, el URI para una página que describe la aplicación (no se comprueba el vínculo), los URI que identifican la aplicación y la contraseña para la identidad de aplicación. Este comando crea la aplicación de AD y la entidad de servicio.

        azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>
        
    Se devuelve la nueva entidad de servicio. Cuando se conceden permisos, es necesario el identificador de objeto. El nombre de entidad de servicio es necesario para iniciar sesión.
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. Conceda los permisos de la entidad de servicio en su suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para más información sobre el control de acceso basado en roles, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md). Para asignar un rol, debe tener acceso `Microsoft.Authorization/*/Write` que se concede a través del rol [Propietario](./active-directory/role-based-access-built-in-roles.md#owner) o del rol [Administrador de acceso de usuario](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

Eso es todo. La aplicación de AD y la entidad de servicio ya están configuradas. La siguiente sección muestra cómo iniciar sesión con las credenciales a través de la CLI de Azure; sin embargo, si desea utilizar las credenciales en la aplicación de código, ya no necesita continuar con este tema. Puede ir a [Aplicaciones de ejemplo](#sample-applications) para obtener ejemplos de inicio de sesión con su Id. de aplicación y contraseña.

### Proporcionar credenciales a través de la CLI de Azure

1. Para el nombre de usuario, utilice el nombre de entidad de servicio devuelto al crear la entidad de servicio. Si necesita recuperar el identificador de aplicación, use el siguiente comando. Proporcione el nombre de la aplicación de Active Directory en el parámetro **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. Inicie sesión como la entidad de servicio:

        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant "$tenantId"

    Se le pedirá la contraseña. Proporcione la contraseña que especificó al crear la aplicación de AD.

        info:    Executing command login
        Password: ********

Ahora está autenticado como entidad de servicio para la entidad de servicio que ha creado.

## Creación de entidad de servicio con certificado

En esta sección llevará a cabo los pasos necesarios para crear una entidad de servicio para una entidad de servicio que utiliza un certificado con el fin de realizar la autenticación. Para completar estos pasos, debe tener instalado [OpenSSL](http://www.openssl.org/).

1. Creación de un certificado autofirmado.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Combine las claves públicas y privadas.

        cat privkey.pem cert.pem > examplecert.pem

3. Abra el archivo **examplecert.pem** y copie los datos del certificado. Busque la secuencia larga de caracteres entre **-----BEGIN CERTIFICATE-----** y **-----END CERTIFICATE-----**.

1. Cree a una entidad de servicio para la aplicación. Proporcione el identificador de la aplicación que se devolvió en el paso anterior.

        azure ad sp create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>
        
    Se devuelve la nueva entidad de servicio. Cuando se conceden permisos, es necesario el identificador de objeto.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Conceda los permisos de la entidad de servicio en su suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para más información sobre el control de acceso basado en roles, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md). Para asignar un rol, debe tener acceso `Microsoft.Authorization/*/Write` que se concede a través del rol [Propietario](./active-directory/role-based-access-built-in-roles.md#owner) o del rol [Administrador de acceso de usuario](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### Proporcionar un certificado a través de un script automatizado de la CLI de Azure

Para autenticar con la CLI de Azure, proporcione la huella digital del certificado, el archivo del certificado, el identificador de la aplicación y el identificador del inquilino.

    azure login --service-principal --tenant 00000 -u 000000 --certificate-file C:\certificates\examplecert.pem --thumbprint 000000

Ahora está autenticado como la entidad de servicio para la aplicación de Active Directory que ha creado.

Si necesita recuperar la huella digital del certificado y quitar los caracteres innecesarios, use:

    openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
De este modo, se devuelve un valor de huella digital similar al siguiente:

    30996D9CE48A0B6E0CD49DBB9A48059BF9355851

Si necesita recuperar el identificador de aplicación, use:

    azure ad app show --search exampleapp --json | jq -r '.[0].appId'

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
- Para más información sobre el uso de certificados y la CLI de Azure, consulte [Certificate-based auth with Azure Service Principals from Linux command line](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx) (Autenticación basada en certificados con entidades de servicio de Azure desde la línea de comandos de Linux).

<!---HONumber=AcomDC_0720_2016-->