---
title: "Creación de una máquina virtual Linux en Azure a partir de una plantilla | Microsoft Docs"
description: "En este artículo se describe cómo utilizar la CLI de Azure 2.0 para crear una máquina virtual Linux a partir de una plantilla de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 09/26/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 938304efe5e4a13736a50348bd0531c475149aec
ms.contentlocale: es-es
ms.lasthandoff: 09/27/2017

---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Procedimiento para crear una máquina virtual Linux con plantillas de Azure Resource Manager
En este artículo se muestra cómo implementar rápidamente una máquina virtual Linux con plantillas de Azure Resource Manager y la CLI de Azure 2.0. También puede llevar a cabo estos pasos con la [CLI de Azure 1.0](create-ssh-secured-vm-from-template-nodejs.md).


## <a name="templates-overview"></a>Introducción a las plantillas
Las plantillas de Azure Resource Manager son archivos JSON que definen la infraestructura y la configuración de la solución de Azure. Mediante una plantilla, puede implementar la solución repetidamente a lo largo del ciclo de vida y tener la seguridad de que los recursos se implementan de forma coherente. Para obtener más información sobre el formato de la plantilla y cómo se crea, consulte [Creación de la primera plantilla de Azure Resource Manager](../../azure-resource-manager/resource-manager-create-first-template.md). Para ver la sintaxis JSON para los tipos de recursos, consulte [Define resources in Azure Resource Manager templates](/azure/templates/) (Definición de recursos en plantillas de Azure Resource Manager).


## <a name="create-resource-group"></a>Creación de un grupo de recursos
Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Se debe crear un grupo de recursos antes de una máquina virtual. En el ejemplo siguiente, se crea un grupo de recursos denominado "*myResourceGroup*" en la ubicación *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Create virtual machine
En el ejemplo siguiente se crea una máquina virtual a partir de [esta plantilla de Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) con [az group deployment create](/cli/azure/group/deployment#create). Proporcione el valor de su propia clave pública SSH, como el contenido de *~/.ssh/id_rsa.pub*. Si necesita crear un par de claves SSH, consulte el artículo sobre cómo [crear y usar un par de claves SSH pública y privada para máquinas virtuales Linux en Azure](mac-create-ssh-keys.md).

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
  --parameters '{"sshKeyData": {"value": "ssh-rsa AAAAB3N{snip}B9eIgoZ"}}'
```

En este ejemplo, especificó una plantilla almacenada en GitHub. También puede descargar o crear una plantilla y especificar la ruta de acceso local con el mismo parámetro `--template-file`.

Para acceder mediante SSH a la máquina virtual, obtenga la dirección IP pública con [az network public-ip show](/cli/azure/network/public-ip#show):

```azurecli
az network public-ip show \
    --resource-group myResourceGroup \
    --name sshPublicIP \
    --query [ipAddress] \
    --output tsv
```

Después, puede acceder mediante SSH a la máquina virtual de la forma habitual. Proporcione su propia dirección IP pública obtenida con el comando anterior:

```bash
ssh azureuser@<ipAddress>
```

## <a name="next-steps"></a>Pasos siguientes
En este ejemplo, creó una máquina virtual Linux básica. Para obtener más plantillas de Resource Manager que incluyen marcos de aplicaciones o crear entornos más complejos, examine la [Galería de plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/).
