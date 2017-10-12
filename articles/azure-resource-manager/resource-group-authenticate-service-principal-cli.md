---
title: "Creación de la identidad de la aplicación de Azure con la CLI de Azure | Microsoft Docs"
description: "Se describe cómo usar la CLI de Azure para crear una entidad de servicio y una aplicación de Azure Active Directory, además de cómo concederle acceso a recursos mediante el control de acceso basado en rol. Muestra cómo autenticar aplicaciones con una contraseña o un certificado."
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
ms.date: 05/15/2017
ms.author: tomfitz
ms.openlocfilehash: 3c5826d58887ff1af4df8e66999d9c1a1643bcc7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>Uso de la CLI de Azure para crear a una entidad de servicio para acceder a recursos

Cuando haya una aplicación o un script que necesite acceder a recursos, puede configurar una identidad para la aplicación y autenticarla con sus propias credenciales. Esta identidad se conoce como una entidad de servicio. Este enfoque le permite:

* Asignar permisos a la identidad de la aplicación que sean diferentes a los suyos propios. Normalmente, estos permisos están restringidos a exactamente aquello que la aplicación debe hacer.
* Usar un certificado para la autenticación al ejecutar un script desatendido.

En este artículo se muestra cómo usar la [CLI de Azure 1.0](../cli-install-nodejs.md) para configurar una aplicación para que se ejecute con sus propias credenciales e identidad. Instale la versión más reciente de la [CLI de Azure 1.0](../cli-install-nodejs.md) para asegurarse de que el entorno coincide con los ejemplos de este artículo.

## <a name="required-permissions"></a>Permisos necesarios
Para completar este tema, debe tener permisos suficientes tanto en su suscripción de Azure como en Azure Active Directory. En concreto, debe poder crear una aplicación en Azure Active Directory y asignar la entidad de servicio a un rol. 

El portal representa la forma más sencilla de comprobar si su cuenta tiene los permisos adecuados. Consulte [Comprobación de los permisos necesarios en el portal](resource-group-create-service-principal-portal.md#required-permissions).

Ahora, continúe en la sección sobre autenticación mediante [contraseña](#create-service-principal-with-password) o [certificado](#create-service-principal-with-certificate).

## <a name="create-service-principal-with-password"></a>Creación de entidad de servicio con contraseña
En esta sección, llevará a cabo los pasos necesarios para crear la aplicación de AD con una contraseña y asignar el rol Lector a la entidad de servicio.

1. Inicie sesión en su cuenta.
   
   ```azurecli
   azure login
   ```
2. Para crear una identidad de la aplicación, especifique el nombre de la aplicación y una contraseña, como se muestra en el siguiente comando:
     
   ```azurecli
   azure ad sp create -n exampleapp -p {your-password}
   ```
     
   Se devuelve la nueva entidad de servicio. Cuando se conceden permisos, es necesario el identificador de objeto. El GUID que se enumera con los nombres de entidad de servicio es necesario para iniciar sesión. Este GUID es el mismo valor que el identificador de la aplicación. En las aplicaciones de ejemplo, a este valor se le conoce como `Client ID`. 
     
   ```azurecli
   info:    Executing command ad sp create
     
   Creating application exampleapp
     / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
     data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
     data:    Display Name:            exampleapp
     data:    Service Principal Names:
     data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
     data:                             https://www.contoso.org/example
     info:    ad sp create command OK
   ```

3. Conceda los permisos de la entidad de servicio en la suscripción. En este ejemplo, se agrega la entidad de servicio al rol Lector, que concede permiso para leer todos los recursos de la suscripción. Para obtener información sobre otros roles, consulte [RBAC: Roles integrados](../active-directory/role-based-access-built-in-roles.md). En el caso del parámetro objectid, especifique el identificador de objeto que usó al crear la aplicación. Antes de ejecutar este comando, debe esperar un poco para que la nueva entidad de servicio se propague por Azure Active Directory. Si estos comandos se ejecutan manualmente, lo habitual es que haya transcurrido suficiente tiempo entre las tareas. En un script, debe agregar un paso de suspensión entre los comandos (como `sleep 15`). Si recibe un error que indica que la entidad de seguridad no existe en el directorio, vuelva a ejecutar el comando.
   
   ```azurecli
   azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/
   ```
   
Eso es todo. La aplicación de AD y la entidad de servicio ya están configuradas. La sección siguiente muestra cómo iniciar sesión con las credenciales mediante la CLI de Azure. Si desea usar las credenciales en la aplicación de código, no es necesario continuar con este tema. Puede ir a [Aplicaciones de ejemplo](#sample-applications) para obtener ejemplos de inicio de sesión con su Id. de aplicación y contraseña. 

### <a name="provide-credentials-through-azure-cli"></a>Proporcionar credenciales a través de la CLI de Azure
Ahora, debe iniciar sesión como la aplicación para realizar operaciones.

1. Cada vez que inicie sesión como entidad de servicio, debe proporcionar el id. del inquilino del directorio para la aplicación de AD. Un inquilino es una instancia de Azure Active Directory. Para recuperar el identificador del inquilino para la suscripción actualmente autenticada, use:
   
   ```azurecli
   azure account show
   ```
   
   Que devuelve:
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
     Si necesita obtener el identificador del inquilino de otra suscripción, use el siguiente comando:
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. Si tiene que recuperar el identificador de cliente que se utiliza para iniciar sesión, use lo siguiente:
   
   ```azurecli
   azure ad sp show -c exampleapp --json
   ```
   
     El valor que se usa para iniciar sesión es el GUID que figura en los nombres de entidad de seguridad de servicio.
   
   ```azurecli
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
   ```
3. Inicie sesión como la entidad de servicio.
   
   ```azurecli
   azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}
   ```
   
    Se le pedirá la contraseña. Proporcione la contraseña que especificó al crear la aplicación de AD.
   
   ```azurecli
   info:    Executing command login
   Password: ********
   ```

Ahora está autenticado como entidad de servicio para la entidad de servicio que ha creado.

Como alternativa, puede invocar operaciones REST desde la línea de comandos para iniciar sesión. De la respuesta de autenticación, puede recuperar el token de acceso para su uso con otras operaciones. Para ver un ejemplo de recuperación del token de acceso mediante la invocación de operaciones REST, consulte [Generación de un token de acceso](resource-manager-rest-api.md#generating-an-access-token).

## <a name="create-service-principal-with-certificate"></a>Creación de entidad de servicio con certificado
Estos son los pasos que se realizan en esta sección:

* Creación de un certificado autofirmado.
* Creación de la aplicación de AD con el certificado y la entidad de servicio.
* Asignación del rol de lector a la entidad de servicio.

Para completar estos pasos, debe tener instalado [OpenSSL](http://www.openssl.org/) .

1. Creación de un certificado autofirmado.
   
   ```
   openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'
   ```

2. El paso anterior ha creado dos archivos: privkey.pem y cert.pem. Combine las claves pública y privada en un solo archivo.

   ```
   cat privkey.pem cert.pem > examplecert.pem
   ```

3. Abra el archivo **examplecert.pem** y busque la secuencia larga de caracteres entre **-----BEGIN CERTIFICATE-----** y **-----END CERTIFICATE-----**. Copie los datos del certificado. Estos datos se pasan como un parámetro al crear la entidad de servicio.

4. Inicie sesión en su cuenta.

   ```azurecli
   azure login
   ```
5. Para crear la entidad de servicio, especifique el nombre de la aplicación y los datos del certificado, tal y como se muestra en el siguiente comando:
     
   ```azurecli
   azure ad sp create -n exampleapp --cert-value {certificate data}
   ```
     
   Se devuelve la nueva entidad de servicio. Cuando se conceden permisos, es necesario el identificador de objeto. El GUID que se enumera con los nombres de entidad de servicio es necesario para iniciar sesión. Este GUID es el mismo valor que el identificador de la aplicación. En las aplicaciones de ejemplo, a este valor se conoce como identificador de cliente. 
     
   ```azurecli
   info:    Executing command ad sp create
     
   Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
     data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
     data:    Display Name:     exampleapp
     data:    Service Principal Names:
     data:                      4fd39843-c338-417d-b549-a545f584a745
     data:                      https://www.contoso.org/example
     info:    ad sp create command OK
   ```
6. Conceda los permisos de la entidad de servicio en la suscripción. En este ejemplo, se agrega la entidad de servicio al rol Lector, que concede permiso para leer todos los recursos de la suscripción. Para obtener información sobre otros roles, consulte [RBAC: Roles integrados](../active-directory/role-based-access-built-in-roles.md). En el caso del parámetro objectid, especifique el identificador de objeto que usó al crear la aplicación. Antes de ejecutar este comando, debe esperar un poco para que la nueva entidad de servicio se propague por Azure Active Directory. Si estos comandos se ejecutan manualmente, lo habitual es que haya transcurrido suficiente tiempo entre las tareas. En un script, debe agregar un paso de suspensión entre los comandos (como `sleep 15`). Si recibe un error que indica que la entidad de seguridad no existe en el directorio, vuelva a ejecutar el comando.
   
   ```azurecli
   azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/
   ```
  
### <a name="provide-certificate-through-automated-azure-cli-script"></a>Proporcionar un certificado a través de un script automatizado de la CLI de Azure
Ahora, debe iniciar sesión como la aplicación para realizar operaciones.

1. Cada vez que inicie sesión como entidad de servicio, debe proporcionar el id. del inquilino del directorio para la aplicación de AD. Un inquilino es una instancia de Azure Active Directory. Para recuperar el identificador del inquilino para la suscripción actualmente autenticada, use:
   
   ```azurecli
   azure account show
   ```
   
   Que devuelve:
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
   Si necesita obtener el identificador del inquilino de otra suscripción, use el siguiente comando:
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. Para recuperar la huella digital de certificado y quitar los caracteres innecesarios, use el siguiente comando:
   
   ```
   openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
   ```
   
   De este modo, se devuelve un valor de huella digital similar al siguiente:
   
   ```
   30996D9CE48A0B6E0CD49DBB9A48059BF9355851
   ```
3. Si tiene que recuperar el identificador de cliente que se utiliza para iniciar sesión, use lo siguiente:
   
   ```azurecli
   azure ad sp show -c exampleapp
   ```
   
   El valor que se usa para iniciar sesión es el GUID que figura en los nombres de entidad de seguridad de servicio.
     
   ```azurecli
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
   ```
4. Inicie sesión como la entidad de servicio.
   
   ```azurecli
   azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}
   ```

Ahora está autenticado como la entidad de servicio para la aplicación de Azure Active Directory que ha creado.

## <a name="change-credentials"></a>Cambio de credenciales

Para cambiar las credenciales de una aplicación de Active Directory, ya sea por un problema de seguridad o porque las credenciales hayan caducado, use `azure ad app set`.

Para cambiar una contraseña, use:

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --password p@ssword
```

Para cambiar un valor de certificado, use:

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --cert-value {certificate data}
```

## <a name="debug"></a>Depurar

Durante la creación de una entidad de servicio pueden producirse los siguientes errores:

* **"Authentication_Unauthorized"** o **"No subscription found in the context"** (No se encuentra una suscripción en el contexto). - Este error se ve cuando la cuenta no tiene los [permisos necesarios](#required-permissions) en Azure Active Directory para registrar una aplicación. Por lo general, este error aparece cuando los usuarios administradores de Azure Active Directory son los únicos que pueden registrar las aplicaciones y la cuenta no es de un administrador. Pida al administrador que le asigne un rol de administrador o que permita a los usuarios registrar aplicaciones.

* Su cuenta **"no tiene autorización para realizar la acción 'Microsoft.Authorization/roleAssignments/write' en el ámbito '/ subscriptions / {guid}'."**  - Este error aparece cuando la cuenta no tiene permisos suficientes para asignar un rol a una identidad. Pida al administrador de suscripciones que le agregue al rol de administrador de acceso de usuario.

## <a name="sample-applications"></a>Aplicaciones de ejemplo
Para información sobre el inicio de sesión como en la aplicación a través de distintas plataformas, consulte:

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Pasos siguientes
* Si desea conocer los pasos detallados de la integración de una aplicación en Azure para administrar recursos, consulte [Guía del desarrollador para la autorización con la API de Azure Resource Manager](resource-manager-api-authentication.md).
* Para más información sobre el uso de certificados y la CLI de Azure, consulte [Certificate-based auth with Azure Service Principals from Linux command line](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)(Autenticación basada en certificados con entidades de servicio de Azure desde la línea de comandos de Linux). 
* Para obtener una lista de las acciones que puede conceder o denegar a los usuarios, consulte [Operaciones del proveedor de recursos de Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md).
