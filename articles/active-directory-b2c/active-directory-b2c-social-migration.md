---
title: 'Azure Active Directory B2C: migración de usuarios con identidades de redes sociales'
description: Descripción de conceptos básicos sobre la migración de usuarios con identidades de redes sociales a Azure AD B2C con Graph API
services: active-directory-b2c
documentationcenter: ''
author: davidmu
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/03/2018
ms.author: davidmu
ms.openlocfilehash: 80889ac29b6d92f17fb9c9c693fa733085ce7f1c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: migración de usuarios con identidades de redes sociales
Si planea migrar su proveedor de identidades a Azure AD B2C, también debe migrar usuarios con identidades de redes sociales. En este artículo se explica cómo migrar cuentas existentes de identidades de redes sociales, como cuentas de Facebook, LinkedIn, Google y Microsoft, a Azure AD B2C. Este artículo también se aplica a las identidades federadas; sin embargo, estas migraciones son menos habituales.

## <a name="prerequisites"></a>requisitos previos
Este artículo es una continuación del artículo sobre migración de usuarios y se centra en la migración de identidades de redes sociales. Antes de empezar, lea el artículo sobre [migración de usuarios](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Introducción a la migración de identidades de redes sociales

* En Azure AD B2C, los nombres de inicio de sesión de las **cuentas locales** (nombre de usuario o dirección de correo electrónico) se almacenan en la colección `signInNames` en el registro de usuarios. `signInNames` contiene uno o varios registros `signInName` que especifican los nombres de inicio de sesión para el usuario. Cada nombre de inicio de sesión debe ser único en el inquilino.

* Las identidades de las **cuentas de las redes sociales** se almacenan en la colección `userIdentities`. La entrada especifica `issuer` (nombre de proveedor de identidades) como facebook.com y `issuerUserId`, que es un identificador de usuario único para el emisor. El atributo `userIdentities` contiene uno o más registros de identidad de usuario que especifican el tipo de cuenta de redes sociales y el identificador de usuario único del proveedor de identidades de redes sociales.

* **Combine una cuenta local con una identidad de redes sociales**. Como ya se ha mencionado, los nombres de inicio de sesión de cuentas locales y las identidades de cuentas de redes sociales se almacenan en diferentes atributos. `signInNames` se utiliza para la cuenta local, mientras que `userIdentities` es para la cuenta de redes sociales. Una sola cuenta de Azure AD B2C puede ser solo una cuenta local, solo una cuenta de redes sociales, o combinar una cuenta local con identidad de redes sociales en un registro de usuario. Este comportamiento le permite administrar una única cuenta, mientras que un usuario puede iniciar sesión con las credenciales de cuenta local o con las identidades de redes sociales.

* Tipo `UserIdentity`: contiene información sobre la identidad de un usuario de la cuenta de redes sociales en un inquilino de Azure AD B2C:
    * `issuer` La representación de cadena del proveedor de identidades que ha emitido el identificador de usuario, como facebook.com.
    * `issuerUserId` El identificador de usuario único utilizado por el proveedor de identidades de redes sociales en formato en base64.

    ```JSON
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
    ```

* Dependiendo del proveedor de identidades, el **identificador del usuario de redes sociales** es un valor único para un usuario determinado por aplicación o cuenta de desarrollo. Configure la directiva de B2C de Azure AD con el mismo identificador de aplicación que asignó previamente el proveedor de redes sociales. U otra aplicación en la misma cuenta de desarrollo.

## <a name="use-graph-api-to-migrate-users"></a>Uso de Graph API para migrar usuarios
Va a crear la cuenta de usuario de Azure AD B2C mediante [Graph API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). Para comunicarse con Graph API, debe tener una cuenta de servicio con privilegios administrativos. En Azure AD, debe registrar una aplicación y la autenticación en Azure AD. Las credenciales de la aplicación son el identificador de aplicación y el secreto de aplicación. La aplicación actúa como tal, no como un usuario, para llamar a Graph API. Siga las instrucciones del paso 1 en el artículo sobre [migración de usuarios](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-user-migration#step-1-use-graph-api-to-migrate-users).

## <a name="required-properties"></a>Propiedades obligatorias
En la lista siguiente se muestran las propiedades que son obligatorias cuando se crea un usuario.
* **accountEnabled**: true
* **displayName**: el nombre para mostrar en la libreta de direcciones del usuario.
* **passwordProfile**: el perfil de contraseña del usuario. 

> [!NOTE]
> Para la cuenta de redes sociales solamente (sin credenciales de cuenta local), todavía debe especificar la contraseña. Azure AD B2C omite la contraseña que se especifica para las cuentas de redes sociales.

* **userPrincipalName**: el nombre principal de usuario (someuser@contoso.com). El nombre principal de usuario debe contener uno de los dominios comprobados del inquilino. Para especificar el nombre principal de usuario, genere un nuevo valor GUID, concaténelo con `@` y su nombre de inquilino.
* **mailNickname**: el alias de correo del usuario. Este valor puede ser el mismo identificador que usa para el nombre principal de usuario. 
* **signInNames**: uno o varios registros signInNames que especifican los nombres de inicio de sesión del usuario. Cada nombre de inicio de sesión debe ser único en la compañía o inquilino. Para la cuenta de redes sociales únicamente, esta propiedad puede dejarse vacía.
* **userIdentities**: uno o más registros de identidad de usuario que especifican el tipo de cuenta de redes sociales y el identificador de usuario único del proveedor de identidades de redes sociales.
* [opcional] **otherMails**: para la cuenta de redes sociales únicamente, las direcciones de correo electrónico del usuario. 

Para más información, consulte la [referencia de Graph API](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser).

## <a name="migrate-social-account-only"></a>Migración de cuenta de redes sociales (solo)
Para crear solo una cuenta de redes sociales, sin credenciales de cuenta local. Envíe una solicitud HTTPS POST a Graph API. El cuerpo de la solicitud contiene las propiedades del usuario de cuentas de redes sociales que se va a crear. Como mínimo, debe especificar las propiedades obligatorias. 


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Envíe los datos de formulario siguientes: 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8",
    "signInNames": [],
    "creationType": null,
    "displayName": "Sara Bell",
    "givenName": "Sara",
    "surname": "Bell",
    "passwordProfile": {
        "password": "Test1234",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": null,
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ],
    "otherMails": ["sara@live.com"],
    "userPrincipalName": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8@tenant-name.onmicrosoft.com"
}
```
## <a name="migrate-social-account-with-local-account"></a>Migración de una cuenta de redes sociales con cuenta local
Para crear una cuenta local combinada con identidades de redes sociales. Envíe una solicitud HTTPS POST a Graph API. El cuerpo de la solicitud contiene las propiedades del usuario de cuentas de redes sociales que se va a crear. Como mínimo, debe especificar las propiedades obligatorias. 

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Envíe los datos de formulario siguientes: 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "5164db16-3eee-4629-bfda-dcc3326790e9",
    "signInNames": [{
            "type": "emailAddress",
            "value": "david@contoso.com"
        }
    ],
    "creationType": "LocalAccount",
    "displayName": "David Hor",
    "givenName": "David",
    "surname": "Hor",
    "passwordProfile": {
        "password": "1234567",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [{
            "issuer": "contoso.com",
            "issuerUserId": "ZGF2aWRAY29udG9zby5jb20="
        }
    ],
    "otherMails": [],
    "userPrincipalName": "5164db16-3eee-4629-bfda-dcc3326790e9@tenant-name.onmicrosoft.com"
}
```

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes
### <a name="how-can-i-know-the-issuer-name"></a>¿Cómo se puede saber el nombre del emisor?
El nombre del emisor, o el nombre del proveedor de identidades, se configura en la directiva. Si no conoce el valor que se va a especificar en `issuer`, siga este procedimiento:
1. Inicio de sesión con una de las cuentas de redes sociales
2. En el token de JWT, copie el valor `sub`. El valor `sub` normalmente contiene el identificador de objeto del usuario en Azure AD B2C. O, desde Azure Portal, abra las propiedades del usuario y copie el identificador de objeto.
3. Abra el [Explorador de Azure AD Graph](https://graphexplorer.azurewebsites.net).
4. Inicie sesión con su administrador. N
5. Ejecute la siguiente solicitud GET. Reemplace el objeto userObjectId por el identificador de usuario que ha copiado. **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Busque el elemento `userIdentities` dentro de la devolución JSON de Azure AD B2C.
7. [Opcional] También puede descodificar el valor `issuerUserId`.

> [!NOTE]
> Use una cuenta de administrador de inquilinos B2C que sea local para el inquilino B2C. La sintaxis del nombre de la cuenta es admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-social-identity-to-an-existing-local-account"></a>¿Es posible agregar identidades de redes sociales a una cuenta local existente?
Sí. Puede agregar la identidad de redes sociales una vez creada la cuenta local. Ejecute la solicitud HTTPS PATCH. Reemplace el objeto userObjectId por el identificador de usuario que quiere actualizar. 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Envíe los datos de formulario siguientes: 

```JSON
{
    "userIdentities": [
        {
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

### <a name="is-it-possible-to-add-multiple-social-identities"></a>¿Es posible agregar varias identidades de redes sociales?
Sí. Puede agregar varias identidades de redes sociales para una única cuenta de Azure AD B2C. Ejecute la solicitud HTTPS PATCH. Reemplace el objeto userObjectId por el identificador de usuario. 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Envíe los datos de formulario siguientes: 

```JSON
{
    "userIdentities": [
        {
            "issuer": "google.com",
            "issuerUserId": "MjQzMjE2NTc4NTQ="
        },
        {
            "issuer": "facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

## <a name="optional-user-migration-application-sample"></a>[Opcional] Ejemplo de aplicación de migración de usuario
[Descargue y ejecute la aplicación de ejemplo V2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). La aplicación de ejemplo V2 utiliza un archivo JSON que contiene datos de usuario ficticios, como cuenta local, cuenta de redes sociales e identidades locales y de redes sociales en una única cuenta.  Para editar el archivo JSON, abra la solución `AADB2C.UserMigration.sln` de Visual Studio. En el proyecto `AADB2C.UserMigration`, abra el archivo `UsersData.json`. El archivo contiene una lista de entidades de usuario. Cada entidad de usuario tiene las siguientes propiedades:
* **signInName**: para la cuenta local, la dirección de correo electrónico para el inicio de sesión
* **displayName**: nombre para mostrar del usuario
* **firstName**: nombre del usuario
* **lastName**: apellido del usuario
* **password**: para la cuenta local, la contraseña del usuario (puede estar vacía)
* **issuer**: para la cuenta de redes sociales, el nombre del proveedor de identidades
* **issuerUserId**: para la cuenta de redes sociales, el identificador de usuario único utilizado por el proveedor de identidades de redes sociales. El valor debe estar en texto no cifrado. La aplicación de ejemplo codifica este valor en base64.
* **email**: solo para la cuenta de redes sociales (no combinada), la dirección de correo electrónico del usuario

```JSON
{
  "userType": "emailAddress",
  "Users": [
    {
      // Local account only
      "signInName": "James@contoso.com",
      "displayName": "James Martin",
      "firstName": "James",
      "lastName": "Martin",
      "password": "Pass!w0rd"
    },
    {
      // Social account only
      "issuer": "Facebook.com",
      "issuerUserId": "1234567890",
      "email": "sara@contoso.com",
      "displayName": "Sara Bell",
      "firstName": "Sara",
      "lastName": "Bell"
    },
    {
      // Combine local account with social identity
      "signInName": "david@contoso.com",
      "issuer": "Facebook.com",
      "issuerUserId": "0987654321",
      "displayName": "David Hor",
      "firstName": "David",
      "lastName": "Hor",
      "password": "Pass!w0rd"
    }
  ]
}
```

> [!NOTE]
> Si no actualiza el archivo UsersData.json del ejemplo con sus datos, puede iniciar sesión con las credenciales de la cuenta local de ejemplo, pero no con los ejemplos de las cuentas de redes sociales. Para migrar sus cuentas de redes sociales, proporcione datos reales.

Para más información sobre cómo usar la aplicación de ejemplo, consulte [Azure Active Directory B2C: Migración de usuarios](active-directory-b2c-user-migration.md).
