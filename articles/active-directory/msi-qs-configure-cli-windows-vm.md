---
title: "Configuración de MSI en una máquina virtual de Azure con la CLI de Azure"
description: "Instrucciones paso a paso para configurar una identidad de servicio administrada (MSI) en una máquina virtual de Azure, mediante la CLI de Azure."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 78a6164e76f6ceab936874e68bd38bb4eb387e00
ms.contentlocale: es-es
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-azure-cli"></a>Configuración de Managed Service Identity (MSI) con una máquina virtual con la CLI de Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Managed Service Identity proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, aprenderá a habilitar y quitar MSI para una máquina virtual Windows de Azure, mediante la CLI de Azure.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Para ejecutar los ejemplos de script de la CLI, tiene tres opciones:

- Usar [Azure Cloud Shell](../cloud-shell/overview.md) desde Azure Portal (consulte la sección siguiente).
- Usar Azure Cloud Shell integrado a través del botón "Pruébelo", situado en la esquina superior derecha de cada bloque de código.
- [Instalar la versión más reciente de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 o posterior), si prefiere usar una consola local de CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Habilitación de MSI durante la creación de una máquina virtual de Azure

Se crea un nuevo recurso Windows Virtual Machine habilitado para MSI en un grupo de recursos nuevo, con los parámetros de configuración especificados. Tenga en cuenta que muchas de estas funciones pueden ejecutarse durante varios segundos o minutos antes de volver.

1. Si no usa Azure Cloud Shell desde Azure Portal, primero inicie sesión en Azure mediante [az login](/cli/azure/#login). Use una cuenta asociada a la suscripción de Azure en la que desearía implementar la máquina virtual:

   ```azurecli-interactive
   az login
   ```

2. Cree un [grupo de recursos](../azure-resource-manager/resource-group-overview.md#terminology) para contener e implementar la máquina virtual y sus recursos relacionados, con [az group create](/cli/azure/group/#create). Puede omitir este paso si ya tiene un grupo de recursos que le gustaría usar en su lugar:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Cree una máquina virtual mediante [az vm create](/cli/azure/vm/#create). En el ejemplo siguiente se crea una máquina virtual denominada *myVM* con un archivo MSI, como solicitó el parámetro `--assign-identity`. Los parámetros `--admin-username` y `--admin-password` especifican el nombre de usuario administrativo y la contraseña de la cuenta para el inicio de sesión en la máquina virtual. Actualice estos valores según convenga para su entorno: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Habilitación de MSI en una máquina virtual de Azure existente

Si tiene que habilitar MSI en una máquina virtual existente:

1. Si no usa Azure Cloud Shell desde Azure Portal, primero inicie sesión en Azure mediante [az login](/cli/azure/#login). Use una cuenta asociada a la suscripción de Azure en la que desearía implementar la máquina virtual:

   ```azurecli-interactive
   az login
   ```

2. Use [az vm assign-identity](/cli/azure/vm/#az_vm_assign_identity) con el parámetro `--assign-identity` para agregar una MSI a una máquina virtual existente:

   ```azurecli-interactive
   az vm assign-identity -g myResourceGroup -n myVm
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Eliminación de MSI de una máquina virtual de Azure

Si tiene una máquina virtual que ya no necesita una identidad de servicio administrada:

1. Si no usa Azure Cloud Shell desde Azure Portal, primero inicie sesión en Azure con [az login](/cli/azure/#login). Use una cuenta asociada a la suscripción de Azure en la que desearía implementar la máquina virtual:

   ```azurecli-interactive
   az login
   ```

2. Use el parámetro `-n ManagedIdentityExtensionForWindows` con [az vm extension delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity) para quitar la identidad MSI:

   ```azurecli-interactive
   az vm extension delete --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
   ```

## <a name="related-content"></a>Contenido relacionado

- [Información general de Managed Service Identity](msi-overview.md)
- Este artículo se ha adaptado a partir de la guía de inicio rápido [Creación de una máquina virtual Windows con CLI](../virtual-machines/windows/quick-create-cli.md), que se ha modificado para incluir instrucciones específicas de MSI. 

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.

















