---
title: Registro de inquilinos en Azure Stack para el seguimiento de uso | Microsoft Docs
description: Obtenga información sobre las operaciones utilizadas para administrar los registros de inquilinos y cómo se realiza el seguimiento del uso de inquilinos en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: ef7ca59647a1f8c15d85c809609060a5945bedde
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32159118"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Administración del registro de inquilinos en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack*

En este artículo contiene información sobre las operaciones que puede utilizar para administrar los registros de inquilinos y cómo se realiza el seguimiento del uso de inquilinos. Puede encontrar más información sobre cómo agregar, enumerar o quitar asignaciones de inquilinos. Puede usar puntos de conexión de PowerShell o de Billing API para administrar el seguimiento del uso.

## <a name="add-tenant-to-registration"></a>Adición de inquilinos al registro

Esta operación se utiliza cuando se desea agregar un nuevo inquilino al registro, para que su uso se notifique bajo una suscripción de Azure conectada con su inquilino de Azure Active Directory (Azure AD).

También puede usar esta operación si desea cambiar la suscripción asociada a un inquilino; en este caso, puede volver a llamar a PUT/New-AzureRMResource. Se sobrescribe la asignación anterior.

Tenga en cuenta que solo se puede asociar una suscripción de Azure a un inquilino. Si intenta agregar una segunda suscripción a un inquilino existente, la primera suscripción se sobrescribe. 


| .                  | DESCRIPCIÓN |
|---                         | --- |
| registrationSubscriptionID | La suscripción de Azure que se ha usado para el registro inicial. |
| customerSubscriptionID     | La suscripción de Azure (no Azure Stack) a la que pertenece el cliente que se va a registrar. Debe crearse en la oferta del proveedor de servicios en la nube (CSP). En la práctica, esto significa mediante el centro de partners. Si un cliente tiene más de un inquilino, esta suscripción debe crearse en el inquilino que se usará para iniciar sesión en Azure Stack. |
| resourceGroup              | El grupo de recursos de Azure en el que se almacena el registro. |
| registrationName           | El nombre del registro de Azure Stack. Es un objeto almacenado en Azure. El nombre suele ser azurestack-CloudID, donde CloudID es el identificador de nube de su implementación de Azure Stack. |

> [!Note]  
> Los inquilinos se deben registrar en cada instancia de Azure Stack que utilicen. Si un inquilino usa más de una instancia de Azure Stack, hay que actualizar los registros iniciales de cada implementación con la suscripción del inquilino.

### <a name="powershell"></a>PowerShell

Utilice el cmdlet New-AzureRmResource para actualizar el recurso de registro. Inicie sesión en Azure (`Add-AzureRmAccount`) con la cuenta utilizada para el registro inicial. Este es un ejemplo de cómo agregar un inquilino:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>Llamada a la API

**Operación**: PUT  
**URI de solicitud**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Respuesta**: 201 Creado  
**Cuerpo de respuesta**: Vacío  

## <a name="list-all-registered-tenants"></a>Lista de todos los inquilinos registrados

Obtenga una lista de todos los inquilinos que se han agregado a un registro.

 > [!Note]  
 > Si no hay inquilinos registrados, no recibirá respuesta.

### <a name="parameters"></a>Parámetros

| .                  | DESCRIPCIÓN          |
|---                         | ---                  |
| registrationSubscriptionId | La suscripción de Azure que se ha usado para el registro inicial.   |
| resourceGroup              | El grupo de recursos de Azure en el que se almacena el registro.    |
| registrationName           | El nombre del registro de Azure Stack. Es un objeto almacenado en Azure. El nombre suele ser **azurestack**-***CloudID***, donde ***CloudID*** es el identificador de nube de su implementación de Azure Stack.   |

### <a name="powershell"></a>PowerShell

Use el cmdlet Get-AzureRmResource para enumerar todos los inquilinos registrados. Inicie sesión en Azure (`Add-AzureRmAccount`) con la cuenta utilizada para el registro inicial. Este es un ejemplo de cómo agregar un inquilino:

```powershell
  Get-AzureRmResovurce -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Llamada a la API

Puede obtener una lista de todas las asignaciones de inquilinos mediante la operación GET.

**Operación**: GET  
**URI de solicitud**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**Respuesta**: 200  
**Cuerpo de respuesta**: 

```JSON  
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}”,
            "name": " cspSubscriptionId 1",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}”,
            "name": " cspSubscriptionId2 ",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>Eliminación de una asignación de inquilinos

Puede quitar un inquilino que se haya agregado a un registro. Si ese inquilino todavía está usando recursos en Azure Stack, su uso se cobra a la suscripción utilizada en el registro inicial de Azure Stack.

### <a name="parameters"></a>Parámetros

| .                  | DESCRIPCIÓN          |
|---                         | ---                  |
| registrationSubscriptionId | Identificador de suscripción para el registro.   |
| resourceGroup              | El grupo de recursos para el registro.   |
| registrationName           | Nombre del registro.  |
| customerSubscriptionId     | Identificador de suscripción del cliente.  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Llamada a la API

Se pueden quitar las asignaciones de inquilinos mediante la operación DELETE.

**Operación**: DELETE  
**URI de solicitud**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Respuesta**: 204 Sin contenido  
**Cuerpo de respuesta**: Vacío

## <a name="next-steps"></a>Pasos siguientes

 - Para más información sobre cómo recuperar la información de utilización de recursos de Azure Stack, consulte [Uso y facturación en Azure Stack](/azure-stack-billing-and-chargeback.md).
