---
title: "Control de máquinas virtuales de Azure con la CLI de Azure | Microsoft Docs"
description: "Tutorial: administración de máquinas virtuales de Azure aplicando RBAC, directivas, bloqueos y etiquetas con la CLI de Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: 86ac3ec390c7aa9bc24a90ef2ee582f97f8b5407
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="virtual-machine-governance-with-azure-cli"></a>Control de máquinas virtuales con la CLI de Azure

[!include[Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Para instalar y usar la CLI de Azure localmente, vea [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli).

## <a name="understand-scope"></a>Descripción del ámbito

[!include[Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

En este tutorial, aplica toda la configuración de administración a un grupo de recursos para que pueda quitar fácilmente esos valores cuando haya finalizado.

Vamos a crear el grupo de recursos.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

Actualmente, el grupo de recursos está vacío.

## <a name="role-based-access-control"></a>Control de acceso basado en rol

Desea asegurarse de que los usuarios de su organización tienen el nivel adecuado de acceso a estos recursos. No desea conceder acceso ilimitado a los usuarios, pero también es necesario que se asegure de que pueden realizar su trabajo. [Control de acceso basado en rol](../../active-directory/role-based-access-control-what-is.md) le permite administrar los usuarios con permiso para completar acciones específicas en un ámbito.

Para crear y quitar las asignaciones de rol, los usuarios deben tener acceso a `Microsoft.Authorization/roleAssignments/*`. Esta acción se concede mediante los roles Propietario o Administrador de acceso de usuario.

Para administrar las soluciones de máquina virtual, hay tres roles específicos a los recursos, que normalmente proporcionan el acceso necesario:

* [Colaborador de la máquina virtual](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)
* [Colaborador de la red](../../active-directory/role-based-access-built-in-roles.md#network-contributor)
* [Colaborador de la cuenta de almacenamiento](../../active-directory/role-based-access-built-in-roles.md#storage-account-contributor)

En lugar de asignar roles a usuarios individuales, a menudo resulta más fácil [crear un grupo de Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md) para los usuarios que tienen que realizar acciones similares. A continuación, asigne a ese grupo el rol apropiado. Para simplificar este artículo, cree un grupo de Azure Active Directory sin miembros. Todavía puede asignar a este grupo un rol para un ámbito. 

En el siguiente ejemplo se crea un grupo de Azure Active Directory denominado *VMDemoContributors* con un alias de correo de *vmDemoGroup*. El alias de correo hace las veces de alias del grupo.

```azurecli-interactive
adgroupId=$(az ad group create --display-name VMDemoContributors --mail-nickname vmDemoGroup --query objectId --output tsv)
```

Tras volver el símbolo del sistema, el grupo tardará un momento en propagarse por todo Azure Active Directory. Después de esperar durante 20 o 30 segundos, use el comando [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) para asignar el nuevo grupo de Azure Active Directory al rol Colaborador de la máquina virtual para el grupo de recursos.  Si ejecuta el siguiente comando antes de su propagación, recibirá un error en el que se indica que la **entidad de seguridad<guid> no existe en el directorio**. Intente ejecutar el comando de nuevo.

```azurecli-interactive
az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

Por lo general, repetirá el proceso para *Colaborador de la red* y *Colaborador de la cuenta de almacenamiento* con el objetivo de asegurarse de que los usuarios se asignan para administrar los recursos implementados. En este artículo, puede omitir esos pasos.

## <a name="azure-policies"></a>Directivas de Azure

[!include[Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Aplicación de directivas

La suscripción ya tiene varias definiciones de directiva. Para ver las definiciones de directivas disponibles, use el comando [az policy definition list](/cli/azure/policy/definition#az_policy_definition_list):

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Ve las definiciones de directiva existentes. El tipo de directiva es **BuiltIn** o **Custom**. Examine las definiciones para buscar las que describan una condición que quiera asignar. En este artículo, puede asignar directivas que:

* Limitan las ubicaciones para todos los recursos.
* Limitan las SKU para máquinas virtuales.
* Realizan auditorías de máquinas virtuales que no usan discos administrados.

En el siguiente ejemplo, recupera tres definiciones de directivas en función del nombre para mostrar. Puede usar el comando [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) para asignar esas definiciones al grupo de recursos. En algunas directivas, puede proporcionar valores de los parámetros para especificar los valores permitidos.

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

En el ejemplo anterior se da por hecho que ya conoce los parámetros de una directiva. Si necesita ver los parámetros, use:

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>Implementación de la máquina virtual

Ha asignado roles y directivas, por lo que está listo para implementar la solución. El tamaño predeterminado es Standard_DS1_v2, que es una de las SKU permitidas. El comando crea claves SSH si aún no existen en una ubicación predeterminada.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Una vez finalizada la implementación, puede aplicar más opciones de configuración de administración a la solución.

## <a name="lock-resources"></a>Bloqueo de recursos

Los [bloqueos de recursos](../../azure-resource-manager/resource-group-lock-resources.md) impiden que los usuarios de su organización eliminen o modifiquen de forma accidental recursos críticos. A diferencia del control de acceso basado en rol, los bloqueos de recursos aplican una restricción a todos los usuarios y roles. Puede establecer el bloqueo de nivel en *CanNotDelete* o *ReadOnly*.

Para crear o eliminar bloqueos de administración, debe tener acceso a las acciones `Microsoft.Authorization/locks/*`. Entre los roles integrados, solamente se conceden esas acciones al **propietario** y al **administrador de acceso de usuarios**.

Para bloquear la máquina virtual y el grupo de seguridad de red, use el comando [az lock create](/cli/azure/lock#az_lock_create):

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

Para probar los bloqueos, intente ejecutar el siguiente comando:

```azurecli-interactive 
az group delete --name myResourceGroup
```

Verá un error en el que se indica que la operación de eliminación no se puede realizar debido a un bloqueo. Solo se puede eliminar el grupo de recursos si quita los bloqueos específicamente. Ese paso se muestra en [Limpieza de recursos](#clean-up-resources).

## <a name="tag-resources"></a>Etiquetado de recursos

Se aplican [etiquetas](../../azure-resource-manager/resource-group-using-tags.md) a los recursos de Azure para organizarlos de forma lógica por categorías. Cada etiqueta consta de un nombre y un valor. Por ejemplo, puede aplicar el nombre "Environment" y el valor "Production" a todos los recursos en producción.

[!include[Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Para aplicar etiquetas a una máquina virtual, use el comando [az resource tag](/cli/azure/resource#az_resource_tag). No se retiene ninguna etiqueta existente del recurso.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Búsqueda de recursos por etiqueta

Para buscar recursos con un nombre y valor de etiqueta, use el comando [az resource list](/cli/azure/resource#az_resource_list):

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

Puede utilizar los valores devueltos para las tareas de administración, como detener todas las máquinas virtuales con un valor de etiqueta.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Visualización de los costos por valores de etiqueta

[!include[Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

El grupo de seguridad de red bloqueado no se puede eliminar hasta que se quite el bloqueo. Para quitar el bloqueo, recupere los ID de los bloqueos y proporcióneselos al comando [az lock delete](/cli/azure/lock#az_lock_delete):

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

Cuando ya no se necesiten, puede usar el comando [az group delete](/cli/azure/group#az_group_delete) para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados. Salga de la sesión SSH a la máquina virtual y, luego, elimine los recursos como se indica a continuación:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha creado una imagen de máquina virtual personalizada. Ha aprendido a:

> [!div class="checklist"]
> * Asignar un rol a los usuarios
> * Aplicar directivas que hagan cumplir los estándares
> * Proteger los recursos críticos con bloqueos
> * Etiquetar recursos para la facturación y la administración

Avance al siguiente tutorial para obtener información sobre máquinas virtuales de alta disponibilidad.

> [!div class="nextstepaction"]
> [Supervisión de máquinas virtuales](tutorial-monitoring.md)

