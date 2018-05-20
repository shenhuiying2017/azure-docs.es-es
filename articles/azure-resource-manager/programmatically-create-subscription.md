---
title: Creación de suscripciones de Azure Enterprise mediante programación | Microsoft Docs
description: Aprenda a crear suscripciones adicionales de Azure Enterprise o de Desarrollo/pruebas - Enterprise mediante programación.
services: azure-resource-manager
author: jlian
manager: jlian
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/30/2018
ms.author: jlian
ms.openlocfilehash: f55f878d53b3813ea2ff2510998d47820de76a6a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Creación de suscripciones de Azure Enterprise mediante programación (versión preliminar)

Como cliente de Azure sujeto a [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), puede crear suscripciones de EA (MS-AZR-0017P) y de Desarrollo/pruebas de EA (MS-AZR-0148P) mediante programación. Para conceder a otro usuario o entidad de servicio los permisos para crear suscripciones que se facturen en su cuenta, asígneles acceso de [Control de acceso basado en roles (RBAC)](../active-directory/role-based-access-control-configure.md) a su cuenta de inscripción. 

> [!IMPORTANT]
> Las suscripciones de Azure que se crean a través de esta API se rigen por el contrato en virtud del cual ha obtenido servicios de Microsoft Azure de Microsoft o de un distribuidor autorizado. Para obtener más información, vea [Información legal de Microsoft Azure](https://azure.microsoft.com/support/legal/).

En este artículo aprenderá a:

> [!div class="checklist"]
> * Crear suscripciones con Azure Resource Manager mediante programación
> * Usar RBAC para compartir la capacidad para crear suscripciones que se facturen en su cuenta de EA

Vea también el [código de ejemplo de .NET en GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).

## <a name="ask-your-ea-enrollment-admin-to-add-you-as-account-owner"></a>Pida al administrador de inscripciones de EA que le agregue como propietario de cuenta

Para empezar, pida al administrador de inscripciones que [le agregue como propietario de cuenta mediante el portal de EA](https://ea.azure.com/helpdocs/addNewAccount) (se requiere inicio de sesión). Siga las instrucciones del correo electrónico de invitación que reciba para crear manualmente una suscripción inicial.

> [!IMPORTANT]
> Debe confirmar la propiedad de cuenta y crear manualmente una suscripción inicial de EA antes de continuar con el paso siguiente. No basta con limitarse a agregar la cuenta a la inscripción.

## <a name="find-accounts-you-have-access-to"></a>Búsqueda de cuentas a las que tiene acceso

Cuando haya sido agregado a una inscripción de Azure EA como propietario de cuenta, Azure usa la relación cuenta-inscripción para determinar dónde se cobra la suscripción. Para crear suscripciones, averigüe primero las cuentas de inscripción a las que tiene acceso. Si actualmente es propietario de una cuenta de EA e intenta utilizar esta API, Azure comprueba si se cumplen las condiciones siguientes:

- Su cuenta se ha agregado a una inscripción de EA
- Tiene una o más suscripciones de EA o de Desarrollo/pruebas de EA, lo que indica que ha pasado al menos una vez por el registro manual
- Ha iniciado sesión en el *directorio particular* del propietario de cuenta, que es el directorio en el que las suscripciones se crean de manera predeterminada

Si se cumplen las tres condiciones anteriores, se devuelve un recurso `enrollmentAccount` y puede empezar a crear suscripciones en esa cuenta. Todas las suscripciones creadas en la cuenta se facturan a la inscripción de EA en la que se encuentra la cuenta.

# <a name="resttabrest"></a>[REST](#tab/rest)

Solicite mostrar todas las cuentas de inscripción:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure responde con una lista de todas las cuentas de inscripción a las que tiene acceso:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Use el [comando Get-AzureRmEnrollmentAccount](/powershell/module/azurerm.billing/get-azurermenrollmentaccount) para mostrar todas las cuentas de inscripción a las que tiene acceso.

```azurepowershell-interactive
Get-AzureRmEnrollmentAccount
```

Azure responde con una lista de los identificadores de objeto con direcciones de correo electrónico de cuentas.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[CLI de Azure](#tab/azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Use el comando [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) para mostrar todas las cuentas de inscripción a las que tiene acceso.

```azurecli-interactive 
az billing enrollment-account list
```
Azure responde con una lista de los identificadores de objeto con direcciones de correo electrónico de cuentas.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

---

Use la propiedad `principalName` para identificar la cuenta a la que quiere que se facturen las suscripciones. Use el elemento `id` como valor de `enrollmentAccount` que se utiliza para crear la suscripción en el paso siguiente.

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Creación de suscripciones con una cuenta de inscripción concreta 

En el ejemplo siguiente se crea una solicitud de creación de una suscripción de nombre *Suscripción Dev Team* y la oferta de suscripción es *MS-AZR - 0017P* (EA normal). La cuenta de inscripción es `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (el valor de marcador de posición, que es un GUID), que es la cuenta de inscripción de SignUpEngineering@contoso.com. Opcionalmente, también agrega dos usuarios como propietarios de RBAC a la suscripción.

# <a name="resttabrest"></a>[REST](#tab/rest)

Para crear la suscripción, use el elemento `id` del objeto `enrollmentAccount` en la ruta de acceso de la solicitud.

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Nombre del elemento  | Obligatorio | Escriba   | DESCRIPCIÓN                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Sin       | string | Nombre para mostrar de la suscripción. Si no se especifica, se establece en el nombre de la oferta, por ejemplo, "Microsoft Azure Enterprise".                                 |
| `offerType`   | Sí      | string | Oferta de la suscripción. Las dos opciones para EA son [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso en producción) y [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desarrollo y pruebas, tiene que [activarse mediante el portal de EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Sin        | string | Identificador de objeto de cualquier usuario que quiera agregarse como propietario de RBAC en la suscripción al crearla.  |

En la respuesta, se recupera un objeto `subscriptionOperation` para la supervisión. Cuando haya finalizado la creación de la suscripción, el objeto `subscriptionOperation` devolvería un objeto `subscriptionLink`, que tiene el identificador de suscripción.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para utilizar este módulo de versión preliminar, instálelo ejecutando primero `Install-Module AzureRM.Subscription -AllowPrerelease`. Para asegurarse de que `-AllowPrerelease` funciona, instale una versión reciente de PowerShellGet desde [Obtención del módulo PowerShellGet](/powershell/gallery/psget/get_psget_module).

Use el cmdlet [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview) junto con el identificador de objeto `enrollmentAccount` como parámetro `EnrollmentAccountObjectId` para crear una suscripción. 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Nombre del elemento  | Obligatorio | Escriba   | DESCRIPCIÓN                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Sin       | string | Nombre para mostrar de la suscripción. Si no se especifica, se establece en el nombre de la oferta, por ejemplo, "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Sí      | string | Oferta de la suscripción. Las dos opciones para EA son [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso en producción) y [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desarrollo y pruebas, tiene que [activarse mediante el portal de EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Sí       | string | El identificador de objeto de la cuenta de inscripción con la que se crea la suscripción y contra la que se realizan los cargos. Se trata de un valor GUID que se obtiene de `Get-AzureRmEnrollmentAccount`. |
| `OwnerObjectId`      | Sin        | string | Identificador de objeto de cualquier usuario que quiera agregarse como propietario de RBAC en la suscripción al crearla.  |
| `OwnerSignInName`    | Sin        | string | Dirección de correo electrónico de cualquier usuario que quiera agregarse como propietario de RBAC en la suscripción al crearla. Puede utilizar este parámetro en lugar de `OwnerObjectId`.|
| `OwnerApplicationId` | Sin        | string | Identificador de aplicación de cualquier entidad de servicio que quiera agregarse como propietario de RBAC en la suscripción al crearla. Puede utilizar este parámetro en lugar de `OwnerObjectId`.| 

Para obtener una lista completa de todos los parámetros, vea [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para utilizar esta extensión de versión preliminar, instálela ejecutando primero `az extension add --name subscription`.

Use el comando [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) con el identificador de objeto `enrollmentAccount` como parámetro `enrollment-account-object-id` para crear una suscripción.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nombre del elemento  | Obligatorio | Escriba   | DESCRIPCIÓN                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Sin       | string | Nombre para mostrar de la suscripción. Si no se especifica, se establece en el nombre de la oferta, por ejemplo, "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Sí      | string | Oferta de la suscripción. Las dos opciones para EA son [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso en producción) y [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desarrollo y pruebas, tiene que [activarse mediante el portal de EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Sí       | string | El identificador de objeto de la cuenta de inscripción con la que se crea la suscripción y contra la que se realizan los cargos. Se trata de un valor GUID que se obtiene de `az billing enrollment-account list`. |
| `owner-object-id`      | Sin        | string | Identificador de objeto de cualquier usuario que quiera agregarse como propietario de RBAC en la suscripción al crearla.  |
| `owner-upn`    | Sin        | string | Dirección de correo electrónico de cualquier usuario que quiera agregarse como propietario de RBAC en la suscripción al crearla. Puede utilizar este parámetro en lugar de `owner-object-id`.|
| `owner-spn` | Sin        | string | Identificador de aplicación de cualquier entidad de servicio que quiera agregarse como propietario de RBAC en la suscripción al crearla. Puede utilizar este parámetro en lugar de `owner-object-id`.| 

Para obtener una lista completa de todos los parámetros, vea [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Delegación de acceso a una cuenta de inscripción mediante RBAC

Para conceder a otro usuario o entidad de servicio la capacidad para crear suscripciones con una cuenta específica, [asígneles un rol de propietario de RBAC en el ámbito de la cuenta de inscripción](../active-directory/role-based-access-control-manage-access-rest.md). En el ejemplo siguiente se asigna a un usuario del inquilino con `principalId` de `<userObjectId>` (para SignUpEngineering@contoso.com) un rol de propietario en la cuenta de inscripción. 

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```
Cuando el rol de propietario se asigna correctamente en el ámbito de la cuenta de inscripción, Azure responde con información de la asignación de roles:

```json
{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>",
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "createdOn": "2018-03-05T08:36:26.4014813Z",
    "updatedOn": "2018-03-05T08:36:26.4014813Z",
    "createdBy": "<assignerObjectId>",
    "updatedBy": "<assignerObjectId>"
  },
  "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "<roleAssignmentGuid>"
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Use cmdlet [New-AzureRmRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) para conceder a otro usuario acceso de propietario a su cuenta de inscripción.

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[CLI de Azure](#tab/azure-cli)

Use el comando [az role assignment create](../active-directory/role-based-access-control-manage-access-azure-cli.md) para conceder a otro usuario acceso de propietario a su cuenta de inscripción.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

Cuando un usuario se convierte en propietario de RBAC de su cuenta de inscripción, puede crear suscripciones en ella mediante programación. Una suscripción creada por un usuario delegado aún tiene el propietario de cuenta original como administrador de servicios, pero también tiene el usuario delegado como propietario de forma predeterminada. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Auditoría que ha creado suscripciones con registros de actividad

Para realizar el seguimiento de las suscripciones creadas a través de esta API, use la [API de registro de actividad de inquilinos](/rest/api/monitor/tenantactivitylogs). Actualmente no es posible usar PowerShell, la CLI ni Azure Portal para realizar el seguimiento de la creación de suscripciones.

1. Como administrador de inquilinos del inquilino de Azure AD, [eleve los privilegios de acceso](../active-directory/role-based-access-control-tenant-admin-access.md) y asigne un rol de lector al usuario de auditoría con el ámbito `/providers/microsoft.insights/eventtypes/management`.
1. Como usuario de auditoría, llame a la [API de registro de actividad de inquilinos](/rest/api/monitor/tenantactivitylogs) para ver las actividades de creación de suscripciones. Ejemplo:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> Para llamar cómodamente a esta API desde la línea de comandos, pruebe [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitaciones de la API de creación de suscripciones de Azure Enterprise

- Con esta API solo pueden crearse suscripciones de Azure Enterprise.
- Hay un límite de 50 suscripciones por cuenta. Después, solo pueden crearse suscripciones mediante el Centro de cuentas.
- Debe haber al menos una suscripción de EA o de Desarrollo/pruebas de EA en la cuenta, lo que indica que el propietario de cuenta ha pasado al menos una vez por el registro manual.
- Los usuarios que no son propietarios de cuenta, pero se han agregado a una cuenta de inscripción a través de RBAC, no pueden crear suscripciones mediante el Centro de cuentas.
- No puede seleccionar el inquilino para la suscripción en que se va a crear. La suscripción siempre se crea en el inquilino principal del propietario de cuenta. Para mover la suscripción a otro inquilino, vea cómo [cambiar un inquilino de la suscripción](..\active-directory\active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener un ejemplo sobre cómo crear suscripciones con. NET, vea [ejemplo de código en GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Para obtener más información sobre Azure Resource Manager y sus API, vea [Información general de Azure Resource Manager](resource-group-overview.md).
* Para obtener más información sobre cómo administrar grandes cantidades de suscripciones, vea [Organización de los recursos con grupos de administración de Azure](management-groups-overview.md).
* Para consultar una guía completa de procedimientos recomendados para grandes organizaciones sobre el gobierno de suscripciones, vea [Scaffolding empresarial de Azure: gobierno de suscripción preceptivo](resource-manager-subscription-governance.md)
