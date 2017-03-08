---
title: "Ejemplo de script de la CLI de Azure: creación de una máquina virtual Windows Server 2016 con supervisión de OMS | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de una máquina virtual Windows Server 2016 con supervisión de OMS"
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 82d40c30c92f5da090e7ec4e2f25ead3908cc603
ms.openlocfilehash: ade388f38b7c1f11668c7a356868c9cacd3386f0
ms.lasthandoff: 03/02/2017

---

# <a name="monitor-a-vm-with-operations-management-suite"></a>Supervisión de una máquina virtual con Operations Management Suite

Este script crea una máquina virtual de Azure, instala el agente de Operations Management Suite (OMS) e inscribe el sistema en un área de trabajo de OMS. Después de ejecutar el script, la máquina virtual será visible en la consola de OMS.

Antes de ejecutar este script, asegúrese de que se haya creado una conexión con Azure mediante el comando `az login`. Además, debe cambiar la variable $AdminPassword al principio del script por una contraseña única para cumplir los requisitos de complejidad.

Este ejemplo funciona en un shell de Bash. Para ver las opciones de ejecución de scripts de la CLI de Azure en Windows, consulte [Using the Azure CLI on Windows](../virtual-machines-windows-cli-options.md) (Uso de la CLI de Azure en Windows).

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-monitor-oms/create-windows-vm-monitor-oms.sh "Creación rápida de una máquina virtual")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una máquina virtual y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Crea la máquina virtual y la conecta con la tarjeta de red, la red virtual, la subred y el NSG. Este comando también especifica la imagen de máquina virtual que se usará, y las credenciales administrativas.  |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | Ejecuta una extensión de máquina virtual en una máquina virtual. En este caso, la extensión de agente de Operations Management Suite se usa para instalar el agente de OMS e inscribir la máquina virtual en un área de trabajo de OMS. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de máquina virtual en la [documentación sobre máquinas virtuales Windows de Azure](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

