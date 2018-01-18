---
title: "Cómo configurar una MSI asignada por el usuario para una máquina virtual de Azure mediante la CLI de Azure"
description: "Instrucciones paso a paso para configurar una identidad de servicio administrada (MSI) asignada por el usuario para una máquina virtual de Azure, mediante la CLI de Azure."
services: active-directory
documentationcenter: 
author: BryanLa
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4b6f4e2b0e42724276448fd4726c8326de8ea6ee
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-azure-cli"></a>Configuración de una identidad de servicio administrada (MSI) asignada por el usuario para una máquina virtual, mediante la CLI de Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

La identidad de servicio administrada proporciona a los servicios de Azure una identidad administrada en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, aprenderá a habilitar y quitar una MSI asignada por el usuario de una máquina virtual de Azure mediante la CLI de Azure.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Para ejecutar los ejemplos de script de la CLI de este tutorial, tiene dos opciones:

- Use [Azure Cloud Shell](~/articles/cloud-shell/overview.md) desde Azure Portal o mediante el botón "Pruébelo", situado en la esquina superior derecha de cada bloque de código.
- [Instale la versión más reciente de la CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 o posterior), si prefiere usar una consola de la CLI local. A continuación, inicie sesión en Azure mediante [az login](/cli/azure/#login). Use una cuenta asociada a la suscripción de Azure en la que desearía implementar la MSI asignada por el usuario y la máquina virtual:

   ```azurecli
   az login
   ```

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Habilitación de MSI durante la creación de una máquina virtual de Azure

En esta sección se explica el proceso de creación de la máquina virtual y la asignación de la MSI asignada por el usuario a la máquina virtual. Si ya tiene una máquina virtual que desea usar, omita esta sección y vaya a la siguiente.

1. Puede omitir este paso si ya tiene un grupo de recursos que le gustaría usar. Cree un [grupo de recursos](~/articles/azure-resource-manager/resource-group-overview.md#terminology) para contener e implementar la MSI con [az group create](/cli/azure/group/#create). Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<LOCATION>` con sus propios valores. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Cree una MSI asignada por el usuario mediante [az identity create](/cli/azure/identity#az_identity_create).  El parámetro `-g` especifica el grupo de recursos donde se creó la MSI, mientras que el parámetro `-n` especifica su nombre. Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<MSI NAME>` con sus propios valores:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
La respuesta contiene detalles de la MSI asignada por el usuario que se ha creado, de forma similar al ejemplo siguiente. El valor del recurso `id` asignado a la MSI se utiliza en el paso siguiente.

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

3. Cree una máquina virtual mediante [az vm create](/cli/azure/vm/#create). En el ejemplo siguiente se crea una máquina virtual asociada a la nueva MSI asignada por el usuario, según lo especificado por el parámetro `--assign-identity`. Asegúrese de reemplazar `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` y la propiedad `<`MSI ID>` parameter values with your own values. For `<MSI ID>`, use the user-assigned MSI's resource `id` creada en el paso anterior: 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Habilitación de MSI en una máquina virtual de Azure existente

1. Cree una MSI asignada por el usuario mediante [az identity create](/cli/azure/identity#az_identity_create).  El parámetro `-g` especifica el grupo de recursos donde se creó la MSI, mientras que el parámetro `-n` especifica su nombre. Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<MSI NAME>` con sus propios valores:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
La respuesta contiene detalles de la MSI asignada por el usuario que se ha creado, de forma similar al ejemplo siguiente. El valor del recurso `id` asignado a la MSI se utiliza en el paso siguiente.

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

2. Asigne la MSI asignada por el usuario a la máquina virtual con [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<VM NAME>` con sus propios valores. El parámetro `<MSI ID>` será la propiedad `id` del recurso de la MSI asignada por el usuario, como se ha creado en el paso anterior:

    ```azurecli-interactive
    az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

## <a name="remove-msi-from-an-azure-vm"></a>Eliminación de MSI de una máquina virtual de Azure

1. Quite la MSI asignada por el usuario de la máquina virtual mediante [az vm remove-identity](/cli/azure/vm#az_vm_remove_identity). Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<VM NAME>` con sus propios valores. El parámetro `<MSI NAME>` será la propiedad `name` de la MSI asignada por el usuario, como se ha especificado en el comando `az identity create` (consulte los ejemplos de las secciones anteriores):

   ```azurecli-interactive
   az vm remove-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
   ```

## <a name="next-steps"></a>pasos siguientes

- [Información general de Managed Service Identity](msi-overview.md)
- Para ver guías de inicio rápido completas acerca de la creación de máquinas virtuales de Azure, consulte: 

  - [Creación de una máquina virtual Windows con CLI](~/articles/virtual-machines/windows/quick-create-cli.md)  
  - [Creación de una máquina virtual Linux con PowerShell](~/articles/virtual-machines/linux/quick-create-cli.md) 

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.
















