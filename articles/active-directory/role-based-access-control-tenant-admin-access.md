---
title: "Elevación del acceso para administradores de inquilinos - Azure AD | Microsoft Docs"
description: En este tema se describen los roles integrados para el control de acceso basado en roles (RBAC).
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: b9eb9c7f59d0936364bad788ed3b6635b2453739
ms.openlocfilehash: 9bcad7aaf7f1bd8c51dbfa88381276a70a4def5c


---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>Elevación del acceso como administrador de inquilinos con Control de acceso basado en rol

El control de acceso basado en rol ayuda a los administradores de inquilinos a obtener elevaciones temporales de acceso para que puedan conceder permisos superiores a lo normal. Un administrador de inquilinos puede elevarse el permiso así mismo al rol de administrador de acceso de usuario cuando sea necesario. Ese rol otorga al administrador de inquilinos permisos para concederse a sí mismo otros roles en el ámbito "/".

Esta característica es importante porque permite al administrador de inquilinos ver todas las suscripciones que existen en una organización. También permite a las aplicaciones de automatización (por ejemplo, facturación y auditoría) acceder a todas las suscripciones y proporcionar una vista precisa del estado de la organización desde una perspectiva de administración de recursos o de facturación.  

## <a name="how-to-use-elevateaccess-to-give-tenant-access"></a>Uso de elevateAccess para ofrecer acceso de inquilino

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


## <a name="how-to-undo-the-elevateaccess-action"></a>Deshacer la acción elevateAccess

Cuando se llama a *elevateAccess*, se crea una asignación de roles para usted mismo, por lo que para revocar esos privilegios debe eliminar la asignación.

1.  Llame a [GET roleDefinitions](/rest/api/authorization/roledefinitions#RoleDefinitions_Get), donde roleName = Administrador de accesos de usuario para determinar el GUID del nombre del rol de administrador de accesos de usuario. La respuesta debería tener este aspecto:

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

2. Llame a [GET roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_Get), donde principalId = su propia ObjectId. Esto muestra todas las asignaciones en el inquilino. Busque aquella donde el ámbito es "/" y el RoleDefinitionId termina con el GUID del nombre de rol que encontró en el paso 1. La asignación de roles debería tener este aspecto:

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

3. Por último, llame a [DELETE roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_DeleteById), donde roleAssignmentId = el GUID del nombre que encontró en el paso 2.

## <a name="next-steps"></a>Pasos siguientes

- Obtener más información sobre la [administración del control de acceso basado en rol con REST](role-based-access-control-manage-access-rest.md)

- [Administrar asignaciones de acceso](role-based-access-control-manage-assignments.md) en Azure Portal



<!--HONumber=Feb17_HO2-->


