---
title: "Creación de una entidad de servicio con la CLI de Azure | Microsoft Docs"
description: "Describe cómo usar la CLI de Azure para crear una aplicación de Active Directory y una entidad de servicio, además de cómo concederle acceso a recursos mediante el control de acceso basado en roles. Muestra cómo autenticar aplicaciones con una contraseña o un certificado."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c224a189-dd28-4801-b3e3-26991b0eb24d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/30/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 1fd051e2955ca48936b7bb977088d88b25fd76c6


---
# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>Uso de la CLI de Azure para crear a una entidad de servicio para acceder a recursos
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [CLI de Azure](resource-group-authenticate-service-principal-cli.md)
> * [Portal](resource-group-create-service-principal-portal.md)
> 
> 

Cuando tenga una aplicación o un script que necesite acceder a recursos, lo más probable es que no desee ejecutar este proceso con sus credenciales. Es posible que haya elegido permisos diferentes para la aplicación y no desea que la aplicación continúe usando sus credenciales si cambian sus responsabilidades. En su lugar, crea una identidad para la aplicación que incluye credenciales de autenticación y asignaciones de roles. Cada vez que se ejecuta la aplicación, se autentica con estas credenciales. Este tema le muestra cómo usar la [CLI de Azure para Mac, Linux y Windows](../xplat-cli-install.md) para configurar que una aplicación se ejecute con sus propias credenciales e identidad.

Con la CLI de Azure, tiene dos opciones para autenticar la aplicación de AD:

* contraseña
* certificado

Este tema muestra cómo usar ambas opciones en la CLI de Azure. Si va a iniciar sesión en Azure desde otro entorno de programación (como Python, Ruby o Node.js), la mejor opción es la autenticación con contraseña. Antes de decidir si desea utilizar una contraseña o un certificado, consulte la sección [Aplicaciones de ejemplo](#sample-applications) para obtener ejemplos de autenticación en los distintos marcos.

## <a name="active-directory-concepts"></a>Conceptos de Active Directory
En este artículo, se crean dos objetos: la aplicación de Active Directory (AD) y la entidad de servicio. La aplicación de AD es la representación global de su aplicación. Contiene las credenciales (un identificador de aplicación y una contraseña o certificado). La entidad de servicio es la representación local de su aplicación en una instancia de Active Directory. Contiene la asignación de rol. Este tema se centra en una aplicación de inquilino único donde la aplicación está diseñada para ejecutarse en una sola organización. Normalmente, utiliza aplicaciones de inquilino único para aplicaciones de línea de negocio que se ejecutan dentro de su organización. En una aplicación de un solo inquilino, tendrá una aplicación de AD y una entidad de servicio.

Quizás se pregunte por qué necesita ambos objetos. Este enfoque tiene más sentido para las aplicaciones multiinquilino. Las aplicaciones multiinquilino normalmente se usan para las aplicaciones de software como servicio (SaaS), que se ejecutan en muchas suscripciones diferentes. Para las aplicaciones multiinquilino, tendrá una aplicación de AD y varias entidades de servicio (una en cada instancia de Active Directory que concede acceso a la aplicación). Para configurar una aplicación multiinquilino, consulte [Guía del desarrollador para la autorización con la API de Azure Resource Manager](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Permisos necesarios
Para completar este tema, debe tener permisos suficientes tanto en su suscripción de Azure como en Azure Active Directory. En concreto, debe poder crear una aplicación en Active Directory y asignar la entidad de servicio a un rol. 

En su instancia de Active Directory, su cuenta debe ser un administrador (como **Administrador Global** o **Usuario Admin**). Si su cuenta está asignada al rol **Usuario** rol, necesita que un administrador eleve los permisos.

En su suscripción, su cuenta debe tener acceso a `Microsoft.Authorization/*/Write`, que se concede mediante el rol [Propietario](../active-directory/role-based-access-built-in-roles.md#owner) o el rol [Administrador de acceso de usuario](../active-directory/role-based-access-built-in-roles.md#user-access-administrator). Si su cuenta está asignada al rol **Colaborador** , recibirá un error al intentar asignar la entidad de servicio a un rol. De nuevo, el administrador de la suscripción debe concederle acceso suficiente.

Ahora, continúe en la sección sobre autenticación mediante [contraseña](#create-service-principal-with-password) o [certificado](#create-service-principal-with-certificate).

## <a name="create-service-principal-with-password"></a>Creación de entidad de servicio con contraseña
En esta sección, llevará a cabo los pasos necesarios para crear la aplicación de AD con una contraseña y asignar el rol Lector a la entidad de servicio.

Vamos a analizar estos pasos.

1. Inicie sesión en su cuenta.
   
        azure login
2. Tiene dos opciones para crear la aplicación de AD. Puede crear la aplicación de AD y la entidad de servicio en un solo paso, o bien hacerlo por separado. Créela en un solo paso si no tiene que especificar una página principal y los URI del identificador de la aplicación. Hágalo por separado si tiene que establecer estos valores para una aplicación web. En este paso se muestran las dos opciones.
   
   * Para crear la aplicación de AD y la entidad de servicio en un solo paso, proporcione el nombre de la aplicación y una contraseña, tal y como se muestra en el siguiente comando:
     
          azure ad sp create -n exampleapp -p {your-password}     
   * Para crear la aplicación de AD por separado, proporcione el nombre de la aplicación, un URI de la página principal, los URI del identificador y una contraseña, tal y como se muestra en el siguiente comando:
     
          azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>
     
       El comando anterior devuelve un valor AppId. Para crear una entidad de servicio, proporcione ese valor como un parámetro en el siguiente comando:
     
          azure ad sp create -a <AppId>
     
     Si su cuenta no tiene los [permisos necesarios](#required-permissions) en Active Directory, verá un mensaje de error que indica Authentication_Unauthorized o No se encontró ninguna suscripción en el context.
     
     En ambas opciones, se devuelve la nueva entidad de servicio. Cuando se conceden permisos, se requiere el **identificador de objeto**. El GUID que figura en los **nombres de entidad de seguridad de servicio** es necesario para iniciar sesión. Este GUID es el mismo valor que el identificador de la aplicación. En las aplicaciones de ejemplo, a este valor se le conoce como **identificador de cliente**. 
     
      info:    Ejecución del comando ad sp create
     
     * Creating application exampleapp / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+ data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e data:    Display Name:            exampleapp data:    Service Principal Names: data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4 data:                             https://www.contoso.org/example info:    ad sp create command OK
3. Conceda los permisos de la entidad de servicio en la suscripción. En este ejemplo, se agrega la entidad de servicio al rol **Lector** , que concede permiso para leer todos los recursos de la suscripción. Para obtener información sobre otros roles, consulte [RBAC: Roles integrados](../active-directory/role-based-access-built-in-roles.md). Para el parámetro **ServicePrincipalName**, indique el valor de **ObjectId** que usó al crear la aplicación. 
   
        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/
   
     Si su cuenta no tiene permisos suficientes para asignar un rol, verá un mensaje de error. El mensaje indica que la cuenta **no tiene autorización para realizar la acción 'Microsoft.Authorization/roleAssignments/write' en el ámbito '/subscriptions/{guid}'**. 

Eso es todo. La aplicación de AD y la entidad de servicio ya están configuradas. La sección siguiente muestra cómo iniciar sesión con las credenciales mediante la CLI de Azure. Si desea usar las credenciales en la aplicación de código, no es necesario continuar con este tema. Puede ir a [Aplicaciones de ejemplo](#sample-applications) para obtener ejemplos de inicio de sesión con su Id. de aplicación y contraseña. 

### <a name="provide-credentials-through-azure-cli"></a>Proporcionar credenciales a través de la CLI de Azure
Ahora, debe iniciar sesión como la aplicación para realizar operaciones.

1. Cada vez que inicie sesión como entidad de servicio, debe proporcionar el id. del inquilino del directorio para la aplicación de AD. Un inquilino es una instancia de Active Directory. Para recuperar el identificador del inquilino para la suscripción actualmente autenticada, use:
   
        azure account show
   
     Que devuelve:
   
        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...
   
     Si necesita obtener el identificador del inquilino de otra suscripción, use el siguiente comando:
   
        azure account show -s {subscription-id}
2. Si tiene que recuperar el identificador de cliente que se utiliza para iniciar sesión, use lo siguiente:
   
        azure ad sp show -c exampleapp --json
   
     El valor que se usa para iniciar sesión es el GUID que figura en los nombres de entidad de seguridad de servicio.
   
        [
          {
            "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "7132aca4-1bdb-4238-ad81-996ff91d8db4"
            ]
          }
        ]
3. Inicie sesión como la entidad de servicio.
   
        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}
   
    Se le pedirá la contraseña. Proporcione la contraseña que especificó al crear la aplicación de AD.
   
        info:    Executing command login
        Password: ********

Ahora está autenticado como entidad de servicio para la entidad de servicio que ha creado.

## <a name="create-service-principal-with-certificate"></a>Creación de entidad de servicio con certificado
Estos son los pasos que se realizan en esta sección:

* Creación de un certificado autofirmado.
* Creación de la aplicación de AD con el certificado y la entidad de servicio.
* Asignación del rol de lector a la entidad de servicio.

Para completar estos pasos, debe tener instalado [OpenSSL](http://www.openssl.org/) .

1. Creación de un certificado autofirmado.
   
        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'
2. Combine las claves públicas y privadas.
   
        cat privkey.pem cert.pem > examplecert.pem
3. Abra el archivo **examplecert.pem** y busque la secuencia larga de caracteres entre **-----BEGIN CERTIFICATE-----** y **-----END CERTIFICATE-----**. Copie los datos del certificado. Estos datos se pasan como un parámetro al crear la entidad de servicio.
4. Inicie sesión en su cuenta.
   
        azure login
5. Tiene dos opciones para crear la aplicación de AD. Puede crear la aplicación de AD y la entidad de servicio en un solo paso, o bien hacerlo por separado. Créela en un solo paso si no tiene que especificar una página principal y los URI del identificador de la aplicación. Hágalo por separado si tiene que establecer estos valores para una aplicación web. En este paso se muestran las dos opciones.
   
   * Para crear la aplicación de AD y la entidad de servicio en un solo paso, proporcione el nombre de la aplicación y los datos del certificado, tal y como se muestra en el siguiente comando:
     
          azure ad sp create -n exampleapp --cert-value <certificate data>
   * Para crear la aplicación de AD por separado, proporcione el nombre de la aplicación, un URI de la página principal, los URI del identificador y los datos del certificado, tal y como se muestra en el siguiente comando:
     
          azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example --cert-value <certificate data>
     
       El comando anterior devuelve un valor AppId. Para crear una entidad de servicio, proporcione ese valor como un parámetro en el siguiente comando:
     
          azure ad sp create -a <AppId>
     
     Si su cuenta no tiene los [permisos necesarios](#required-permissions) en Active Directory, verá un mensaje de error que indica Authentication_Unauthorized o No se encontró ninguna suscripción en el context.
     
     En ambas opciones, se devuelve la nueva entidad de servicio. Cuando se conceden permisos, es necesario el identificador de objeto. El GUID que figura en los **nombres de entidad de seguridad de servicio** es necesario para iniciar sesión. Este GUID es el mismo valor que el identificador de la aplicación. En las aplicaciones de ejemplo, a este valor se le conoce como **identificador de cliente**. 
     
      info:    Ejecución del comando ad sp create
     
     * Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+ data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7 data:    Display Name:     exampleapp data:    Service Principal Names: data:                      4fd39843-c338-417d-b549-a545f584a745 data:                      https://www.contoso.org/example info:    ad sp create command OK
6. Conceda los permisos de la entidad de servicio en la suscripción. En este ejemplo, se agrega la entidad de servicio al rol **Lector** , que concede permiso para leer todos los recursos de la suscripción. Para obtener información sobre otros roles, consulte [RBAC: Roles integrados](../active-directory/role-based-access-built-in-roles.md). Para el parámetro **ServicePrincipalName**, indique el valor de **ObjectId** que usó al crear la aplicación. 
   
        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/
   
     Si su cuenta no tiene permisos suficientes para asignar un rol, verá un mensaje de error. El mensaje indica que la cuenta **no tiene autorización para realizar la acción 'Microsoft.Authorization/roleAssignments/write' en el ámbito '/subscriptions/{guid}'**. 

### <a name="provide-certificate-through-automated-azure-cli-script"></a>Proporcionar un certificado a través de un script automatizado de la CLI de Azure
Ahora, debe iniciar sesión como la aplicación para realizar operaciones.

1. Cada vez que inicie sesión como entidad de servicio, debe proporcionar el id. del inquilino del directorio para la aplicación de AD. Un inquilino es una instancia de Active Directory. Para recuperar el identificador del inquilino para la suscripción actualmente autenticada, use:
   
        azure account show
   
     Que devuelve:
   
        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...
   
     Si necesita obtener el identificador del inquilino de otra suscripción, use el siguiente comando:
   
        azure account show -s {subscription-id}
2. Para recuperar la huella digital de certificado y quitar los caracteres innecesarios, use el siguiente comando:
   
        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
   
     De este modo, se devuelve un valor de huella digital similar al siguiente:
   
        30996D9CE48A0B6E0CD49DBB9A48059BF9355851
3. Si tiene que recuperar el identificador de cliente que se utiliza para iniciar sesión, use lo siguiente:
   
        azure ad sp show -c exampleapp
   
     El valor que se usa para iniciar sesión es el GUID que figura en los nombres de entidad de seguridad de servicio.
   
        [
          {
            "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "4fd39843-c338-417d-b549-a545f584a745",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "4fd39843-c338-417d-b549-a545f584a745"
            ]
          }
        ]
4. Inicie sesión como la entidad de servicio.
   
        azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

Ahora está autenticado como la entidad de servicio para la aplicación de Active Directory que ha creado.

## <a name="sample-applications"></a>Aplicaciones de ejemplo
Las aplicaciones de ejemplo siguientes muestran cómo iniciar sesión como entidad de servicio.

**.NET**

* [Implementación de una máquina virtual habilitada para SSH con una plantilla con .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [Administración de recursos y grupos de recursos de Azure con .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [Introducción a recursos - Implementación mediante la plantilla de Azure Resource Manager - en Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [Introducción a recursos - Administración de grupo de recursos - en Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Implementación de una máquina virtual habilitada para SSH con una plantilla en Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Administración de recursos y grupos de recursos de Azure con Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

* [Implementación de una máquina virtual habilitada para SSH con una plantilla en Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Administración de recursos y grupos de recursos de Azure con Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Implementación de una máquina virtual habilitada para SSH con una plantilla en Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Administración de recursos y grupos de recursos de Azure con Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Pasos siguientes
* Si desea conocer los pasos detallados de la integración de una aplicación en Azure para administrar recursos, consulte [Guía del desarrollador para la autorización con la API de Azure Resource Manager](resource-manager-api-authentication.md).
* Para más información sobre el uso de certificados y la CLI de Azure, consulte [Certificate-based auth with Azure Service Principals from Linux command line](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)(Autenticación basada en certificados con entidades de servicio de Azure desde la línea de comandos de Linux). 




<!--HONumber=Nov16_HO3-->


