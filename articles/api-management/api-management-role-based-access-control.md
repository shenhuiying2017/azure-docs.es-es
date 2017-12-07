---
title: Uso del control de acceso basado en rol en Azure API Management | Microsoft Docs
description: "Obtención de información sobre cómo usar los roles integrados y crear roles personalizados en Azure API Management"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: c775780a39c4d423c62bf88f55d35675c70442c7
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Uso del control de acceso basado en rol en Azure API Management
Azure API Management utiliza el control de acceso basado en rol (RBAC) de Azure para permitir que pueda hacerse una administración de acceso detallada en servicios y entidades de API Management (por ejemplo, API y directivas). En este artículo se proporciona información general de los roles integrados y personalizados en API Management. Para más información sobre la administración de acceso en Azure Portal, consulte la [introducción sobre la administración de acceso en Azure Portal](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

## <a name="built-in-roles"></a>Roles integrados
Actualmente, API Management cuenta con tres roles integrados y próximamente agregará otros dos. Estos roles pueden asignarse en distintos ámbitos, como la suscripción, el grupo de recursos y la instancia concreta de API Management. Por ejemplo, si el rol "Azure API Management Service Reader" se asigna a un usuario en el nivel de grupo de recursos, el usuario tendrá acceso de lectura a todas las instancias de API Management incluidas en el grupo de recursos. 

En la tabla siguiente se proporcionan breves descripciones de los roles integrados. Estos roles se pueden asignar a través de Azure Portal u otras herramientas, como Azure [PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell), la [CLI de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli) y la [API de REST](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-rest). Para obtener detalles sobre la asignación de roles integrados, consulte [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

| Rol          | Acceso de lectura<sup>[1]</sup> | Acceso de escritura<sup>[2]</sup> | Creación, eliminación y escalado del servicio, VPN y configuración personalizada de dominios | Acceso al portal del editor heredado | Descripción
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| Colaborador de servicio de Azure API Management | ✓ | ✓  | ✓  | ✓ | Superusuario. Tiene acceso CRUD completo a los servicios y a las entidades de API Management (por ejemplo, API y directivas). Tiene acceso al portal del editor heredado. |
| Lector de servicio de Azure API Management | ✓ | | || Tiene acceso de solo lectura a los servicios y a las entidades de API Management. |
| Operador de servicio de Azure API Management | ✓ | | ✓ | | Puede administrar servicios de API Management, pero no entidades.|
| Editor de servicio de Azure API Management<sup>*</sup> | ✓ | ✓ | |  | Puede administrar entidades de API Management, pero no servicios.|
| Administrador de contenido de Azure API Management<sup>*</sup> | ✓ | | | ✓ | Puede administrar el portal del desarrollador. Acceso de solo lectura a servicios y entidades.|

<sup>[1] Acceso de lectura a los servicios y entidades de API Management (por ejemplo, API y directivas)</sup>

<sup>[2] Acceso de escritura a los servicios y identidades de API Management, excepto las operaciones siguientes: creación, eliminación y escalado de instancias; configuración de VPN, y configuración personalizada de dominios</sup>

<sup>\* El rol Service Editor estará disponible cuando se realice la migración de toda la interfaz de usuario de administración desde el portal del editor existente a Azure Portal. El rol Content Manager estará disponible después de que el portal del editor se refactorice, de modo que solo contenga funcionalidades relacionadas con la administración del portal del desarrollador.</sup>  

## <a name="custom-roles"></a>Roles personalizados
Si ninguno de los roles integrados satisface sus necesidades específicas, se pueden crear roles personalizados para proporcionar administración de acceso más pormenorizada para entidades de API Management. Por ejemplo, puede crear un rol personalizado que tenga acceso de solo lectura a un servicio de API Management, pero que tenga acceso de escritura solamente a una API específica. Para más información sobre los roles personalizados, consulte [Roles personalizados para RBAC de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles). 

A la hora de crear un rol personalizado, es más fácil comenzar con uno de los roles integrados. Edite los atributos para agregar los elementos **Actions**, **NotActions** o **AssignableScopes** y guarde los cambios como un nuevo rol. El ejemplo siguiente comienza con el rol "Azure API Management Service Reader" y crea un rol personalizado denominado "Calculator API Editor". Puede asignar el rol personalizado a una API concreta. De ese modo, el rol solamente tendrá acceso a esa API. 

```
$role = Get-AzureRmRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzureRmRoleDefinition -Role $role
New-AzureRmRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

El artículo [Operaciones del proveedor de recursos de Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md#microsoftapimanagement) contiene la lista de permisos que se pueden conceder en el nivel de API Management.

## <a name="video"></a>Vídeo


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el control de acceso basado en rol de Azure, consulte los siguientes artículos:
  * [Introducción a la administración de acceso en Azure Portal](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)
  * [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)
  * [Roles personalizados para RBAC de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)
  * [Operaciones del proveedor de recursos de Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md#microsoftapimanagement)

