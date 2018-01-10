---
title: "Solución de errores comunes de implementación de Azure | Microsoft Docs"
description: "Describe cómo solucionar errores comunes al implementar recursos en Azure con Azure Resource Manager."
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "error de implementación, implementación de Azure, implementar en Azure"
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: tomfitz
ms.openlocfilehash: ca7e3cb541948e6cc0b8d077616f3611e3ab2477
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Solución de errores comunes de implementación de Azure con Azure Resource Manager

En este artículo se describen algunos errores comunes de implementación de Azure que pueden surgir y se proporciona información sobre cómo resolverlos. Si no encuentra el código del error de implementación, vea [Búsqueda de códigos de error](#find-error-code).

## <a name="error-codes"></a>Códigos de error

| Código de error | Mitigación | Más información |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Siga las restricciones de nomenclatura para las cuentas de almacenamiento. | [Resolución del nombre de la cuenta de almacenamiento](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Consulte las propiedades disponibles para la cuenta de almacenamiento. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | El clúster o la región no tienen recursos disponibles o no admiten el tamaño de máquina virtual solicitado. Vuelva a realizar la solicitud más adelante o solicite otro tamaño de máquina virtual. | [Solución de problemas de la implementación de Resource Manager con la creación de una nueva máquina virtual Linux en Azure](../virtual-machines/linux/troubleshoot-deployment-new-vm.md) y [Solución de problemas de implementación al crear una nueva máquina virtual Windows en Azure](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) |
| AnotherOperationInProgress | Espere a que la operación simultánea finalice. | |
| AuthorizationFailed | La cuenta o entidad de servicio no dispone de acceso suficiente para completar la implementación. Compruebe el rol al que la cuenta pertenece y su acceso para el ámbito de implementación. | [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md) |
| BadRequest | Envió valores de implementación que no coinciden con los que Resource Manager esperaba. Compruebe el mensaje de estado interno para obtener ayuda para solucionar el problema. | [Referencia de plantillas](/azure/templates/) y [ubicaciones admitidas](resource-manager-templates-resources.md#location) |
| Conflicto | Se solicita una operación no permitida con el estado actual del recurso. Por ejemplo, solo se permite el cambio de tamaño del disco al crear una VM o al desasignar la VM. | |
| DeploymentActive | Espere a que la implementación simultánea de este grupo de recursos finalice. | |
| DnsRecordInUse | El nombre del registro de DNS debe ser único. Proporcione un nombre diferente o modifique el registro existente. | |
| ImageNotFound | Compruebe la configuración de la imagen de máquina virtual. |  |
| InUseSubnetCannotBeDeleted | Este error puede aparecer al intentar actualizar un recurso, pero la solicitud se procesa mediante la eliminación y creación del recurso. Asegúrese de especificar todos los valores sin cambios. | [Actualización de recursos](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Obtenga el token de acceso para el inquilino adecuado. Solo puede obtener el token del inquilino al que pertenece su cuenta. | |
| InvalidContentLink | Probablemente ha tratado de agregar un vínculo a una plantilla anidada que no está disponible. Compruebe el URI proporcionado para la plantilla anidada. Si la plantilla se encuentra en una cuenta de almacenamiento, asegúrese de que puede accederse al URI. Debe transmitir un token SAS. | [Plantillas vinculadas](resource-group-linked-templates.md) |
| InvalidParameter | Uno de los valores proporcionados para un recurso no coincide con el valor esperado. Este error puede deberse a muchas condiciones diferentes. Por ejemplo, una contraseña puede ser insuficiente o un nombre de blob puede ser incorrecto. Compruebe el mensaje de error para determinar qué valor debe corregirse. | |
| InvalidRequestContent | Los valores de implementación incluyen valores que no se esperan o faltan los valores requeridos. Confirme los valores para el tipo de recurso. | [Referencia de plantilla](/azure/templates/) |
| InvalidRequestFormat | Habilite el registro de depuración cuando se ejecute la implementación y verifique el contenido de la solicitud. | [Registro de depuración](resource-manager-troubleshoot-tips.md#enable-debug-logging) |
| InvalidResourceNamespace | Compruebe el espacio de nombres del recurso especificado en la propiedad **type**. | [Referencia de plantilla](/azure/templates/) |
| InvalidResourceReference | El recurso aún no existe o se hace referencia a él de forma incorrecta. Compruebe si tiene que agregar una dependencia. Compruebe que el uso de la función **reference** incluye los parámetros necesarios para su escenario. | [Resolución de dependencias](resource-manager-not-found-errors.md) |
| InvalidResourceType | Compruebe el tipo de recurso especificado en la propiedad **type**. | [Referencia de plantilla](/azure/templates/) |
| InvalidTemplate | Compruebe la sintaxis de la plantilla en busca de errores. | [Resolución de plantilla no válida](resource-manager-invalid-template-errors.md) |
| LinkedAuthorizationFailed | Compruebe si la cuenta pertenece al mismo inquilino que el grupo de recursos en que está realizando la implementación. | |
| LinkedInvalidPropertyId | El identificador de un recurso no se resuelve correctamente. Compruebe que ha proporcionado todos los valores necesarios para el identificador del recurso, entre otros, el identificador de la suscripción, el nombre del grupo de recursos, el tipo de recurso, el nombre del recurso principal (si procede) y el nombre del recurso. | |
| LocationRequired | Proporcione una ubicación para el recurso. | [Establecimiento de la ubicación](resource-manager-templates-resources.md#location) |
| MissingRegistrationForLocation | Compruebe el estado de registro del proveedor de recursos y las ubicaciones admitidas. | [Resolución de registros](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Registre la suscripción con el proveedor de recursos. | [Resolución de registros](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Compruebe el estado de registro del proveedor de recursos. | [Resolución de registros](resource-manager-register-provider-errors.md) |
| NotFound | Puede que esté intentando implementar un recurso dependiente en paralelo con un recurso principal. Compruebe si tiene que agregar una dependencia. | [Resolución de dependencias](resource-manager-not-found-errors.md) |
| OperationNotAllowed | La implementación trata de realizar una operación que excede la cuota de la suscripción, del grupo de recursos o de la región. Si es posible, revise la implementación para respetar las cuotas. De lo contrario, considere la posibilidad de solicitar un cambio de las cuotas. | [Resolución de cuotas](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Asegúrese de que existe un recurso principal antes de crear los recursos secundarios. | [Resolución del recurso principal](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | La dirección IP especificada incluye un intervalo de direcciones requerido por Azure. Cambie la dirección IP para evitar el intervalo reservado. | [Direcciones IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | La dirección IP especificada está fuera del intervalo de subred. Cambie la dirección IP para que esté dentro del intervalo de subred. | [Direcciones IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Algunas propiedades no se pueden cambiar en un recurso implementado. Al actualizar un recurso, limite los cambios a las propiedades permitidas. | [Actualización de recursos](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | La suscripción incluye una directiva de recursos que impide una acción que está tratando de realizar durante la implementación. Busque la directiva que bloquea la acción. Si es posible, modifique la implementación para cumplir con las limitaciones de la directiva. | [Resolución de directivas](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Proporcione un nombre de recurso que no incluya un nombre reservado. | [Nombres de recurso reservados](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Espere a que la eliminación finalice. | |
| ResourceGroupNotFound | Compruebe el nombre del grupo de recursos de destino para la implementación. Puede que ya exista en la suscripción. Compruebe el contexto de la suscripción. | [CLI de Azure](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| ResourceNotFound | La implementación hace referencia a un recurso que no se puede resolver. Compruebe que el uso de la función **reference** incluye los parámetros necesarios para su escenario. | [Resolución de referencias](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | La implementación trata de crear recursos que exceden la cuota de la suscripción, del grupo de recursos o de la región. Si es posible, revise la infraestructura para respetar las cuotas. De lo contrario, considere la posibilidad de solicitar un cambio de las cuotas. | [Resolución de cuotas](resource-manager-quota-errors.md) |
| SkuNotAvailable | Seleccione la SKU (como el tamaño de la máquina virtual) que está disponible para la ubicación seleccionada. | [Resolución de SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Proporcione un nombre único para la cuenta de almacenamiento. | [Resolución del nombre de la cuenta de almacenamiento](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Proporcione un nombre único para la cuenta de almacenamiento. | [Resolución del nombre de la cuenta de almacenamiento](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Compruebe la suscripción, el grupo de recursos y el nombre de la cuenta de almacenamiento que pretende utilizar. | |
| SubnetsNotInSameVnet | Una máquina virtual solo puede tener una red virtual. Al implementar varias NIC, asegúrese de que pertenecen a la misma red virtual. | [Varias NIC](../virtual-machines/windows/multiple-nics.md) |

## <a name="find-error-code"></a>Búsqueda de códigos de error

Si se produce un error durante la implementación, Resource Manager devuelve un código de error. Puede ver el mensaje de error a través del portal, PowerShell o la CLI de Azure. El mensaje de error externo puede ser demasiado general para solucionar problemas. Busque el mensaje interno que contiene información detallada sobre el error. Para más información, vea [Determinación del código de error](resource-manager-troubleshoot-tips.md#determine-error-code).

## <a name="next-steps"></a>pasos siguientes
* Para más información sobre las acciones de auditoría, consulte [Operaciones de auditoría con Resource Manager](resource-group-audit.md).
* Si desea conocer más detalles sobre las acciones que permiten determinar los errores durante la implementación, consulte [Visualización de operaciones de implementación con el Portal de Azure](resource-manager-deployment-operations.md).
