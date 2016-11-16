---
title: "Creación de una máquina virtual Linux mediante una plantilla de Azure | Microsoft Docs"
description: "Creación de una máquina virtual de Linux en Azure mediante una plantilla de Azure Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/24/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ff7ea65c329a37acf8b2febb52fd140954d81e97


---
# <a name="create-a-linux-vm-using-an-azure-template"></a>Creación de una máquina virtual de Linux mediante una plantilla de Azure
En este artículo se muestra cómo implementar rápidamente una máquina virtual Linux en Azure mediante una plantilla de Azure.  Este artículo requiere:

* una cuenta de Azure ([obtenga aquí una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/));
* la [CLI de Azure](../xplat-cli-install.md) en la que se inició sesión con `azure login`;
* la CLI de Azure *debe estar en* el modo Azure Resource Manager `azure config mode arm`.

También puede implementar rápidamente una plantilla de máquina virtual Linux mediante [Azure Portal](virtual-machines-linux-quick-create-portal.md).

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
El ejemplo de código siguiente muestra cómo llamar a `azure group create` para crear un grupo de recursos e implementar una máquina virtual Linux protegida por SSH al mismo tiempo mediante [esta plantilla de Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). En el ejemplo, recuerde que debe usar nombres que sean únicos en su entorno. En este ejemplo se usa `myResourceGroup` como nombre del grupo de recursos, y `myVM` como nombre de la máquina virtual.

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




<!--HONumber=Nov16_HO2-->


