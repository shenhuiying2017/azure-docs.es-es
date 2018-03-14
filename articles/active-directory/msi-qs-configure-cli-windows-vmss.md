---
title: "Configuración de MSI en un conjunto de escalado de máquinas virtuales de Azure con la CLI de Azure"
description: "Instrucciones paso a paso para configurar una identidad de servicio administrada (MSI) en un conjunto de escalado de máquinas virtuales de Azure, mediante la CLI de Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: c58ad9cc8bfa16b11201735bcc513e6dd692a2a9
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Configuración de una identidad de servicio administrada (MSI) en un conjunto de escalado de máquinas virtuales mediante la CLI de Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Managed Service Identity proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, aprenderá a habilitar una MSI en un conjunto de escalado de máquinas virtuales de Azure, y a quitarla de ella, mediante la CLI de Azure.

## <a name="prerequisites"></a>requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Para ejecutar los ejemplos de script de la CLI, tiene tres opciones:

- Usar [Azure Cloud Shell](../cloud-shell/overview.md) desde Azure Portal (consulte la sección siguiente).
- Usar Azure Cloud Shell integrado a través del botón "Pruébelo", situado en la esquina superior derecha de cada bloque de código.
- [Instalar la versión más reciente de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 o posterior), si prefiere usar una consola local de CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set"></a>Habilitamiento de MSI durante la creación de un conjunto de escalado de máquinas virtuales de Azure

Para crear un conjunto de escalado de máquinas virtuales con MSI habilitada:

1. Si usa la CLI de Azure en una consola local, lo primero que debe hacer es iniciar sesión en Azure mediante el [inicio de sesión de az](/cli/azure/#az_login). Use una cuenta asociada a la suscripción de Azure en la que desearía implementar el conjunto de escalado de máquinas virtuales:

   ```azurecli-interactive
   az login
   ```

2. Cree un [grupo de recursos](../azure-resource-manager/resource-group-overview.md#terminology) para contener e implementar el conjunto de escalado de máquinas virtuales y sus recursos relacionados, con [az group create](/cli/azure/group/#az_group_create). Puede omitir este paso si ya tiene un grupo de recursos que le gustaría usar en su lugar:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Cree un conjunto de escalado de máquinas virtuales con [az vmss create](/cli/azure/vmss/#az_vmss_create). En el ejemplo siguiente se crea un conjunto de escalado de máquinas virtuales denominado *myVMSS* con un archivo MSI, como solicitó el parámetro `--assign-identity`. Los parámetros `--admin-username` y `--admin-password` especifican el nombre de usuario administrativo y la contraseña de la cuenta para el inicio de sesión en la máquina virtual. Actualice estos valores según convenga para su entorno: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

## <a name="enable-msi-on-an-existing-azure-virtual-machine-scale-set"></a>Habilitación de MSI en un conjunto de escalado de máquinas virtuales de Azure ya existente

Si necesita habilitar MSI en un conjunto de escalado de máquinas virtuales de Azure ya existente:

1. Si usa la CLI de Azure en una consola local, lo primero que debe hacer es iniciar sesión en Azure mediante el [inicio de sesión de az](/cli/azure/#az_login). Use una cuenta asociada a la suscripción de Azure que contenga el conjunto de escalado de máquinas virtuales.

   ```azurecli-interactive
   az login
   ```

2. Use [az vmss assign-identity](/cli/azure/vm/#az_vmss_assign_identity) con el parámetro `--assign-identity` para agregar una MSI a una máquina virtual existente:

   ```azurecli-interactive
   az vmss assign-identity -g myResourceGroup -n myVMSS
   ```

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Eliminación de MSI de un conjunto de escalado de máquinas virtuales de Azure

Si tiene un conjunto de escalado de máquinas virtuales de Azure que ya no necesita una identidad de servicio administrada:

1. Si usa la CLI de Azure en una consola local, lo primero que debe hacer es iniciar sesión en Azure mediante el [inicio de sesión de az](/cli/azure/#az_login). Use una cuenta asociada a la suscripción de Azure que contenga el conjunto de escalado de máquinas virtuales.

   ```azurecli-interactive
   az login
   ```

2. Use el conmutador `--identities` con [az vmss remove-identity](/cli/azure/vmss/#az_vmss_remove_identity) para quitar la MSI:

   ```azurecli-interactive
   az vmss remove-identity -g myResourceGroup -n myVMSS --identities readerID writerID
   ```

## <a name="next-steps"></a>Pasos siguientes

- [Información general de Managed Service Identity](msi-overview.md)
- Si desea consultar la guía completa de inicio rápido sobre creación de un conjunto de escalado de máquinas virtuales de Azure, consulte: 

  - [Creación de un conjunto de escalado de máquinas virtuales con la CLI](../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.
















