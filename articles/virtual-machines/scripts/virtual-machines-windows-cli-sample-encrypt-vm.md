---
title: "Ejemplo de script de la CLI de Azure: cifrado de una máquina virtual Windows | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: cifrado de una máquina virtual Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/02/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 9574d779982c939aa970cd4bdf7df6e66793e3b9
ms.contentlocale: es-es
ms.lasthandoff: 06/20/2017

---

# <a name="encrypt-a-windows-virtual-machine-in-azure"></a>Cifrado de una máquina virtual Windows en Azure

Este script crea una instancia segura de Azure Key Vault, claves de cifrado, una entidad de servicio de Azure Active Directory y una máquina virtual Windows. A continuación, la máquina virtual se cifra con la clave de cifrado de Key Vault y las credenciales de la entidad de servicio.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/encrypt-disks/encrypt_windows_vm.sh "Cifrado de discos de máquina virtual")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los comandos siguientes para crear un grupo de recursos, una instancia de Azure Key Vault, una entidad de servicio, una máquina virtual y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#create) | Crea una instancia de Azure Key Vault para almacenar datos seguros, como claves de cifrado. |
| [az keyvault key create](https://docs.microsoft.com/cli/azure/keyvault/key#create) | Crea una clave de cifrado en Key Vault. |
| [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp#create-for-rbac) | Crea una entidad de servicio de Azure Active Directory para autenticar y controlar de forma segura el acceso a las claves de cifrado. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#set-policy) | Establece permisos en Key Vault para conceder a la entidad de servicio acceso a las claves de cifrado. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Crea la máquina virtual y la conecta con la tarjeta de red, la red virtual, la subred y el NSG. Este comando también especifica la imagen de máquina virtual que se usará, y las credenciales administrativas.  |
| [az vm encryption enable](https://docs.microsoft.com/cli/azure/vm/encryption#enable) | Habilita el cifrado en una máquina virtual mediante las credenciales de la entidad de servicio y la clave de cifrado. |
| [az vm encryption show](https://docs.microsoft.com/cli/azure/vm/encryption#show) | Muestra el estado del proceso de cifrado de la máquina virtual. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de máquina virtual en la [documentación sobre máquinas virtuales Windows de Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%windows%2ftoc.json).

