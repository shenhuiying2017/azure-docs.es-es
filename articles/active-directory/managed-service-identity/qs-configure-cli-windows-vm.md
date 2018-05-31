---
title: Cómo configurar las identidades asignadas por el usuario y por el sistema en una máquina virtual de Azure mediante la CLI de Azure
description: Instrucciones paso a paso para configurar identidades asignadas por el sistema y por el usuario en una máquina virtual de Azure mediante la CLI de Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 44d1dabdb6a9e5f4b405b876f37daa9097c6e7f8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33931867"
---
# <a name="configure-managed-service-identity-msi-on-an-azure-vm-using-azure-cli"></a>Configuración de Managed Service Identity (MSI) en una máquina virtual de Azure con la CLI de Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Managed Service Identity proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, aprenderá a realizar las siguientes operaciones de Managed Service Identity en una máquina virtual de Azure mediante la CLI de Azure:
- Habilitar y deshabilitar la identidad asignada por el sistema en una máquina virtual de Azure
- Agregar y quitar una identidad asignada por el usuario en una máquina virtual de Azure

## <a name="prerequisites"></a>requisitos previos

- Si no está familiarizado con Managed Service Identity, consulte la [sección de introducción](overview.md). **No olvide revisar la [diferencia entre una identidad asignada por el sistema y una asignada por el usuario](overview.md#how-does-it-work)**.
- Si aún no tiene una cuenta de Azure, [regístrese para una cuenta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para ejecutar los ejemplos de script de la CLI, tiene tres opciones:

    - Usar [Azure Cloud Shell](../../cloud-shell/overview.md) desde Azure Portal (consulte la sección siguiente).
    - Usar Azure Cloud Shell integrado a través del botón "Pruébelo", situado en la esquina superior derecha de cada bloque de código.
    - [Instalar la versión más reciente de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 o posterior), si prefiere usar una consola local de CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Identidad asignada por el sistema

En esta sección, aprenderá a habilitar y deshabilitar la identidad asignada por el sistema en una máquina virtual de Azure mediante la CLI de Azure.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Habilitación de una identidad asignada por el sistema durante la creación de una máquina virtual de Azure

Para crear una máquina virtual de Azure con la identidad asignada por el sistema habilitada:

1. Si usa la CLI de Azure en una consola local, lo primero que debe hacer es iniciar sesión en Azure mediante el [inicio de sesión de az](/cli/azure/reference-index#az_login). Use una cuenta asociada a la suscripción de Azure en la que desearía implementar la máquina virtual:

   ```azurecli-interactive
   az login
   ```

2. Cree un [grupo de recursos](../../azure-resource-manager/resource-group-overview.md#terminology) para contener e implementar la máquina virtual y sus recursos relacionados, con [az group create](/cli/azure/group/#az_group_create). Puede omitir este paso si ya tiene un grupo de recursos que le gustaría usar en su lugar:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Cree una máquina virtual mediante [az vm create](/cli/azure/vm/#az_vm_create). En el ejemplo siguiente, se crea una máquina virtual denominada *myVM* con una identidad asignada por el sistema, como ha solicitado el parámetro `--assign-identity`. Los parámetros `--admin-username` y `--admin-password` especifican el nombre de usuario administrativo y la contraseña de la cuenta para el inicio de sesión en la máquina virtual. Actualice estos valores según convenga para su entorno: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Habilitación de la identidad asignada por el sistema en una máquina virtual de Azure existente

Si tiene que habilitar la identidad asignada por el sistema en una máquina virtual existente:

1. Si usa la CLI de Azure en una consola local, lo primero que debe hacer es iniciar sesión en Azure mediante el [inicio de sesión de az](/cli/azure/reference-index#az_login). Use una cuenta asociada a la suscripción de Azure que contenga la máquina virtual. Asegúrese también de que la cuenta pertenece a un rol que le conceda permisos de escritura en la máquina virtual, como "Colaborador de la máquina virtual":

   ```azurecli-interactive
   az login
   ```

2. Use [az vm identity assign](/cli/azure/vm/identity/#az_vm_identity_assign) con el comando `identity assign` para habilitar la identidad asignada por el sistema en una máquina virtual existente:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Deshabilitación de la identidad asignada por el sistema de una máquina virtual de Azure

> [!NOTE]
> Actualmente no se puede deshabilitar Managed Service Identity de una máquina virtual. Mientras tanto, puede cambiar entre el uso de identidades asignadas por el sistema y por el usuario.

Si tiene una máquina virtual que ya no necesita la identidad asignada por el sistema, pero aún necesita identidades asignadas por el usuario, use el siguiente comando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```
Para quitar la extensión de máquina virtual de MSI, use los modificadores `-n ManagedIdentityExtensionForWindows` o `-n ManagedIdentityExtensionForLinux` (en función del tipo de máquina virtual) con [az vm extension delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity):

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-identity"></a>Identidad asignada por el usuario

En esta sección, aprenderá a agregar y quitar una identidad asignada por el usuario de una máquina virtual de Azure con la CLI de Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Asignación de una identidad asignada por el usuario durante la creación de una máquina virtual de Azure

En esta sección, se explica el proceso de creación de una máquina virtual con la asignación de una identidad asignada por el usuario. Si ya tiene una máquina virtual que desea usar, omita esta sección y vaya a la siguiente.

1. Puede omitir este paso si ya tiene un grupo de recursos que le gustaría usar. Cree un [grupo de recursos](~/articles/azure-resource-manager/resource-group-overview.md#terminology) para contener e implementar la MSI con [az group create](/cli/azure/group/#az_group_create). Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<LOCATION>` con sus propios valores. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Cree una identidad asignada por el usuario mediante [az identity create](/cli/azure/identity#az_identity_create).  El parámetro `-g` especifica el grupo de recursos donde se crea la identidad asignada por el usuario, mientras que el parámetro `-n` especifica su nombre.    
    
    > [!IMPORTANT]
    > La creación de identidades asignadas por el usuario solo admite caracteres alfanuméricos y guiones (0-9, a-z, A-z, -). Además, el nombre debe limitarse a una longitud de 24 caracteres para que la asignación a VM/VMSS funcione correctamente. Compruebe si hay actualizaciones. Para obtener más información, consulte [Preguntas más frecuentes y problemas conocidos](known-issues.md).


    ```azurecli-interactive
    az identity create -g myResourceGroup -n myUserAssignedIdentity
    ```
La respuesta contiene detalles de la identidad asignada por el usuario que se ha creado, de forma similar a lo siguiente. El valor del identificador del recurso asignado a la identidad asignada por el usuario se usa en el paso siguiente.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Cree una máquina virtual mediante [az vm create](/cli/azure/vm/#az_vm_create). En el ejemplo siguiente, se crea una máquina virtual asociada a la nueva identidad asignada por el usuario, según lo especificado por el parámetro `--assign-identity`. Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` y `<MSI ID>` por sus propios valores. Para `<MSI ID>`, use la propiedad `id` del recurso de la identidad asignada por el usuario creada en el paso anterior: 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Asignar una identidad asignada por el usuario a una máquina virtual de Azure existente

1. Cree una identidad asignada por el usuario mediante [az identity create](/cli/azure/identity#az-identity-create).  El parámetro `-g` especifica el grupo de recursos donde se crea la identidad asignada por el usuario, mientras que el parámetro `-n` especifica su nombre. Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<MSI NAME>` por sus propios valores:

    > [!IMPORTANT]
    > Actualmente no se admite la creación de identidades asignadas por el usuario con caracteres especiales (por ejemplo, guion bajo) en el nombre. Use caracteres alfanuméricos. Compruebe si hay actualizaciones.  Para obtener más información, consulte [Preguntas más frecuentes y problemas conocidos](known-issues.md).

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
La respuesta contiene detalles de la MSI asignada por el usuario que se ha creado, de forma similar al ejemplo siguiente. El valor del recurso `id` asignado a la identidad asignada por el usuario se usa en el paso siguiente.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Asignar a su máquina virtual la identidad asignada por el usuario mediante [az vm identity assign](/cli/azure/vm#az-vm-identity-assign). Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<VM NAME>` con sus propios valores. El parámetro `<MSI ID>` será la propiedad `id` del recurso de la identidad asignada por el usuario, como se ha creado en el paso anterior:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Eliminación de una identidad asignada por el usuario de una máquina virtual de Azure

> [!NOTE]
> Actualmente no se pueden eliminar todas las identidades asignadas por el usuario de una máquina virtual, a menos que tenga una identidad asignada por el sistema.

Si la máquina virtual tiene varias identidades asignadas por el usuario, puede quitarlas todas, menos la última, mediante [az vm identity remove](/cli/azure/vm#az-vm-identity-remove). Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<VM NAME>` con sus propios valores. El parámetro `<MSI NAME>` será la propiedad `name` de la identidad asignada por el usuario, que se puede encontrar en la sección de identidad de la máquina virtual mediante `az vm show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
Si la máquina virtual tiene identidades asignadas tanto por el sistema como por el usuario, puede quitar todas las identidades asignadas por el usuario si cambia para usar solo las asignadas por el sistema. Use el comando siguiente:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="related-content"></a>Contenido relacionado
- [Información general de Managed Service Identity](overview.md)
- Para ver guías de inicio rápido completas acerca de la creación de máquinas virtuales de Azure, consulte: 
  - [Creación de una máquina virtual Windows con CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Creación de una máquina virtual Linux con PowerShell](../../virtual-machines/linux/quick-create-cli.md) 

















