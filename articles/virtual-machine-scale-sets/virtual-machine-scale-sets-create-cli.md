---
title: "Creación de un conjunto de escalado de máquinas virtuales con la CLI de Azure 2.0 | Microsoft Docs"
description: "Aprenda a crear rápidamente un conjunto de escalado de máquinas virtuales con Azure PowerShell."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 222bfa1c3070fa4634cf5c48d934a6387c48a4b0
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Creación de un conjunto de escalado de máquinas virtuales con la CLI de Azure 2.0
El conjunto de escalado de máquinas virtuales le permite implementar y administrar un conjunto de máquinas virtuales de escalado automático idénticas. Puede escalar el número de máquinas virtuales del conjunto de escalado manualmente o definir reglas de escalado automático basado en el uso de recursos tales como la CPU, la demanda de memoria o el tráfico de red. En este artículo introductorio, debe crear un conjunto de escalado de máquinas virtuales con la CLI de Azure 2.0. También puede crear un conjunto de escalado mediante [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) o [Azure Portal](virtual-machine-scale-sets-create-portal.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure de la versión 2.0.20 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Creación de un conjunto de escalado
Antes de poder crear un conjunto de escalado, cree un grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Ahora, cree un conjunto de escalado de máquinas virtuales con [az vmss create](/cli/azure/vmss#create). En el ejemplo siguiente se crea un conjunto de escalado denominado *myScaleSet* y se generan claves SSH si estas no existen aún:

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

Se tardan unos minutos en crear y configurar todos los recursos de conjunto de escalado y máquinas virtuales.


## <a name="install-nginx-webserver"></a>Instalación de un servidor web NGINX
Para probar el conjunto de escalado, use la extensión de script personalizada para descargar y ejecutar un script que instale NGINX en las instancias de VM. Esta extensión es útil para la configuración posterior a la implementación, la instalación de software o cualquier otra tarea de configuración o administración. Para obtener más información, consulte [Información general de la extensión de script personalizado](../virtual-machines/linux/extensions-customscript.md).

Aplique la extensión de script personalizada que instala NGINX según se indica a continuación:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-web-traffic"></a>Permitir tráfico web
Para permitir que el tráfico llegue al servidor web, cree una regla del equilibrador de carga con [az network lb rule create](/cli/azure/network/lb/rule#create). En el ejemplo siguiente, se crea una regla denominada *myLoadBalancerRuleWeb*:

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```


## <a name="test-your-web-server"></a>Prueba del servidor web
Para ver el servidor web en acción, obtenga la dirección IP pública del equilibrador de carga con [az network public-ip show](/cli/azure/network/public-ip#show). En el ejemplo siguiente se obtiene la dirección IP de *myLoadBalancerRuleWeb* que se ha creado como parte del conjunto de escalado:

```azurecli-interactive 
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Escriba la dirección IP pública del equilibrador de carga en un explorador web. El equilibrador de carga distribuye el tráfico a una de las instancias de VM, como se muestra en el ejemplo siguiente:

![Página web predeterminada de NGINX](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Limpieza de recursos
Cuando ya no lo necesite, puede usar el comando [az group delete](/cli/azure/group#delete) para quitar el grupo de recursos, el conjunto de escalado y todos los recursos relacionados como se indica a continuación:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>pasos siguientes
En este artículo introductorio, creó un conjunto de escalado básico y usó la extensión de script personalizada para instalar un servidor web NGINX básico en las instancias de VM. Para obtener mayor escalabilidad y automatización, expanda el conjunto de escalado con los siguientes artículos de procedimientos:

- [Implementación de la aplicación en conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-deploy-app.md)
- Escalado automático con la [CLI de Azure](virtual-machine-scale-sets-autoscale-cli.md), [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) o [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Uso de las actualizaciones automáticas del sistema operativo para las instancias de VM del conjunto de escalado](virtual-machine-scale-sets-automatic-upgrade.md)