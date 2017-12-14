---
title: "Elevación del acceso para administradores de inquilinos - Azure AD | Microsoft Docs"
description: En este tema se describen los roles integrados para el control de acceso basado en roles (RBAC).
services: active-directory
documentationcenter: 
author: andredm7
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2017
ms.author: andredm
ms.openlocfilehash: 894ccd13684a79590b75821514ef6922abb8fdaf
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>Elevación del acceso como administrador de inquilinos con Control de acceso basado en rol

El control de acceso basado en rol ayuda a los administradores de inquilinos a obtener elevaciones temporales de acceso para que puedan conceder permisos superiores a lo normal. Un administrador de inquilinos puede elevarse el permiso así mismo al rol de administrador de acceso de usuario cuando sea necesario. Ese rol otorga al administrador de inquilinos permisos para concederse a sí mismo otros roles en el ámbito "/".

Esta característica es importante porque permite al administrador de inquilinos ver todas las suscripciones que existen en una organización. También permite a las aplicaciones de automatización (por ejemplo, facturación y auditoría) tener acceso a todas las suscripciones y proporcionar una vista precisa del estado de la organización en la administración de recursos o de facturación.  

## <a name="use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>Uso de elevateAccess para conceder acceso al inquilino con el Centro de administración de Azure AD

1. Vaya al [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) e inicie sesión con sus credenciales.

2. Elija **Propiedades** desde el menú de la izquierda de Azure AD.

3. En la hoja **Propiedades**, busque **El administrador global puede administrar las suscripciones a Azure**, elija **Sí** y, a continuación, **Guardar**.
    > [!IMPORTANT] 
    > Si selecciona **Sí**, se asignará el rol **Administrador de acceso de usuario** a la raíz "/" (ámbito raíz) del usuario con que esté conectado actualmente en el portal. **Esto permite al usuario ver todas las suscripciones de Azure.**
    
    > [!NOTE] 
    > Si selecciona **No**, se quitará el rol **Administrador de acceso de usuario** a la raíz "/" (ámbito raíz) del usuario con que esté conectado actualmente en el portal.

> [!TIP] 
> Puede dar la impresión de que se trata de una propiedad global de Azure Active Directory; sin embargo, su funcionamiento varía con arreglo al usuario que esté conectado actualmente. Si tiene derechos de administrador global en Azure Active Directory, puede invocar la característica elevateAccess para el usuario que está conectado actualmente en el Centro de administración de Azure Active Directory.

![Captura de pantalla de Centro de administración de Azure AD - Propiedades - Globaladmin puede administrar la suscripción de Azure](./media/role-based-access-control-tenant-admin-access/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="view-role-assignments-at-the--scope-using-powershell"></a>Ver las asignaciones de roles en el ámbito de "/" mediante PowerShell
Para ver la asignación **Administrador de acceso de usuario** en el ámbito **/**, use el cmdlet de PowerShell `Get-AzureRmRoleAssignment`.
    
```
Get-AzureRmRoleAssignment* | where {$_.RoleDefinitionName -eq "User Access Administrator" -and $_SignInName -eq "<username@somedomain.com>" -and $_.Scope -eq "/"}
```

**Salida de ejemplo**:

RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0    
Scope              : /    
DisplayName        : username    
SignInName         : username@somedomain.com    
RoleDefinitionName: Administrador de acceso de usuario    
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9    
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc    
ObjectType         : Usuario    

## <a name="delete-the-role-assignment-at--scope-using-powershell"></a>Eliminar la asignación de roles en el ámbito de "/" mediante PowerShell:
Puede eliminar la asignación con el siguiente cmdlet de PowerShell:
```
Remove-AzureRmRoleAssignment -SignInName <username@somedomain.com> -RoleDefinitionName "User Access Administrator" -Scope "/" 
```

## <a name="use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>Uso de elevateAccess para conceder acceso al inquilino con la API de REST

El proceso básico funciona con los siguientes pasos:

1. Mediante REST, llame a *elevateAccess*, que le concede el rol de administrador de acceso de usuario en el ámbito "/".

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. Cree una [asignación de roles](/rest/api/authorization/roleassignments) para asignar cualquier rol en cualquier ámbito. En el ejemplo siguiente se muestran las propiedades para asignar el rol de lector en el ámbito "/":

    ```
    { "properties":{
    "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/acdd72a7338548efbd42f606fba81ae7",
    "principalId": "cbc5e050-d7cd-4310-813b-4870be8ef5bb",
    "scope": "/"
    },
    "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
    }
    ```

3. Mientras sea administrador de accesos de usuario, también podrá eliminar asignaciones de roles en el ámbito "/".

4. Revoque sus privilegios de administrador de accesos de usuario hasta que se vuelva a necesitar.


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>Deshacer la acción elevateAccess con la API de REST

Cuando se llama a *elevateAccess*, se crea una asignación de roles para usted mismo, por lo que para revocar esos privilegios debe eliminar la asignación.

1.  Llame a las definiciones de roles de GET, donde roleName = Administrador de accesos de usuario para determinar el GUID del nombre del rol de administrador de accesos de usuario.
    1.  GET *https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User+Access+Administrator*

        ```
        {"value":[{"properties":{
        "roleName":"User Access Administrator",
        "type":"BuiltInRole",
        "description":"Lets you manage user access to Azure resources.",
        "assignableScopes":["/"],
        "permissions":[{"actions":["*/read","Microsoft.Authorization/*","Microsoft.Support/*"],"notActions":[]}],
        "createdOn":"0001-01-01T08:00:00.0000000Z",
        "updatedOn":"2016-05-31T23:14:04.6964687Z",
        "createdBy":null,
        "updatedBy":null},
        "id":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
        "type":"Microsoft.Authorization/roleDefinitions",
        "name":"18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"}],
        "nextLink":null}
        ```

        Guarde el GUID del parámetro *name*, en este caso **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**.

2. También necesita enumerar la asignación de roles del administrador de inquilinos en el ámbito del inquilino. Enumere todas las asignaciones en el ámbito del inquilino para PrincipalId de TenantAdmin, que realizó la llamada de acceso de elevación. Esto mostrará todas las asignaciones en el inquilino de ObjectID. 
    1. GET *https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'*
    
        >[!NOTE] 
        >Un administrador de inquilinos no debe tener muchas asignaciones. Si la consulta anterior devuelve muchas asignaciones, también puede consultar todas las asignaciones en el nivel de ámbito de inquilino y, a continuación, filtrar los resultados: GET *https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()*
        
    2. Las llamadas anteriores devuelven una lista de asignaciones de roles. Busque la asignación de roles cuando el ámbito sea "/" y RoleDefinitionId termine con la GUID del nombre de rol que se encuentra en el paso 1 y PrincipalId coincida con el ObjectId del administrador de inquilinos. La asignación de roles tiene este aspecto:

        ```
        {"value":[{"properties":{
        "roleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
        "principalId":"{objectID}",
        "scope":"/",
        "createdOn":"2016-08-17T19:21:16.3422480Z",
        "updatedOn":"2016-08-17T19:21:16.3422480Z",
        "createdBy":"93ce6722-3638-4222-b582-78b75c5c6d65",
        "updatedBy":"93ce6722-3638-4222-b582-78b75c5c6d65"},
        "id":"/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
        "type":"Microsoft.Authorization/roleAssignments",
        "name":"e7dd75bc-06f6-4e71-9014-ee96a929d099"}],
        "nextLink":null}
        ```
        
        Una vez más, guarde el GUID del parámetro *name*, en este caso **e7dd75bc-06f6-4e71-9014-ee96a929d099**.

    3. Por último, utilice el **identificador de RoleAssignment ir** para eliminar la asignación agregada por el acceso de elevación:

        DELETE https://management.azure.com /providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01

## <a name="next-steps"></a>Pasos siguientes

- Obtener más información sobre la [administración del control de acceso basado en rol con REST](role-based-access-control-manage-access-rest.md)

- [Administrar asignaciones de acceso](role-based-access-control-manage-assignments.md) en Azure Portal
