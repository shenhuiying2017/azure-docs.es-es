---
title: "Creación de una máquina virtual Linux mediante una plantilla de Azure con la CLI de Azure 1.0 | Microsoft Docs"
description: "En este artículo se explica cómo crear una máquina virtual Linux en Azure mediante una plantilla de Azure Resource Manager y la CLI de Azure 1.0."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: v-livech
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33d4aaa78fcdf3bd9e2e236606f2d3049f464a8a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-linux-vm-using-the-azure-cli-10-an-azure-resource-manager-template"></a>Procedimiento para crear una máquina virtual Linux mediante una plantilla de Azure Resource Manager y la CLI de Azure 1.0
En este artículo se muestra cómo implementar rápidamente una máquina virtual Linux con plantillas de Azure Resource Manager y la CLI de Azure 1.0. Este artículo requiere:

* una cuenta de Azure ([obtenga aquí una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/));
* la [CLI de Azure 1.0](../../cli-install-nodejs.md) en la que se inició sesión con `azure login`;
* la CLI de Azure *debe estar en* el modo Azure Resource Manager `azure config mode arm`.

También puede implementar rápidamente una plantilla de máquina virtual Linux mediante [Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea
Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](#quick-command-summary): la CLI para los modelos de implementación clásico y de Resource Manager (este artículo)
- [CLI de Azure 2.0](create-ssh-secured-vm-from-template.md): la CLI de última generación para el modelo de implementación de administración de recursos

## <a name="quick-command-summary"></a>Resumen rápido de comandos
```azurecli
azure group create \
    -n myResourceGroup \
    -l westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Tutorial detallado
Las plantillas permiten crear máquinas virtuales en Azure con la configuración que desea personalizar durante el inicio, por ejemplo, nombres de usuario y nombres de host. En este artículo, hemos iniciado una plantilla de Azure mediante una máquina virtual de Ubuntu junto con un grupo de seguridad de red (NSG) con el puerto 22 abierto para SSH.

Las plantillas de Azure Resource Manager son archivos JSON que se pueden usar para tareas sencillas y excepcionales, como iniciar una máquina virtual de Ubuntu, como lo hizo en este artículo.  Las plantillas de Azure también se pueden utilizar para crear configuraciones de Azure complejas de entornos completos como una pila de implementación de prueba, desarrollo o producción.

## <a name="create-the-linux-vm"></a>Creación de la máquina virtual de Linux
El ejemplo de código siguiente muestra cómo llamar a `azure group create` para crear un grupo de recursos e implementar una máquina virtual Linux protegida por SSH al mismo tiempo mediante [esta plantilla de Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). En el ejemplo, recuerde que debe usar nombres que sean únicos en su entorno. En este ejemplo se usa *myResourceGroup* como nombre del grupo de recursos, y *myVM* como nombre de la máquina virtual.

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

La salida debe tener un aspecto similar al siguiente bloque de salida:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Ese ejemplo implementó una máquina virtual mediante el parámetro `--template-uri` .  Puede también descargar o crear una plantilla localmente y pasar la plantilla con el parámetro `--template-file` , indicando la ruta de acceso al archivo de plantilla como argumento. La CLI de Azure le pide que especifique los parámetros requeridos por la plantilla.

## <a name="next-steps"></a>Pasos siguientes
Busque en la [galería de plantillas](https://azure.microsoft.com/documentation/templates/) para ver qué entornos de aplicación implementará después.

