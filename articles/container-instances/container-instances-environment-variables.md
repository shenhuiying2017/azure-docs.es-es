---
title: Establecimiento de variables de entorno en Azure Container Instances
description: Aprenda a establecer variables de entorno en Azure Container Instances.
services: container-instances
author: david-stanford
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/13/2018
ms.author: dastanfo
ms.openlocfilehash: 37fde41b6dc2ea0a4d3b4b38a0e3df81a297c125
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="set-environment-variables"></a>Establecimiento de variables de entorno

El establecimiento de variables de entorno en las instancias de contenedor le permite proporcionar configuración dinámica de la aplicación o el script ejecutados por el contenedor.

Actualmente se pueden establecer variables de entorno desde la CLI y PowerShell. En ambos casos, podría usar una marca en los comandos para establecer las variables de entorno. Establecer las variables de entorno en ACI es similar al argumento de línea de comandos `--env` para `docker run`. Por ejemplo, si usa la imagen de contenedor microsoft/aci-wordcount, puede modificar el comportamiento mediante la especificación de las siguientes variables de entorno:

*NumWords*: el número de palabras enviadas a STDOUT.

*MinLength*: el número mínimo de caracteres en una palabra para que se tenga en cuenta. Un número mayor omite palabras comunes como "de" y "la".

## <a name="azure-cli-example"></a>Ejemplo de la CLI de Azure

Para ver la salida predeterminada del contenedor, ejecute el siguiente comando:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Al especificar `NumWords=5` y `MinLength=8` para las variables de entorno del contenedor, los registros de contenedor deben mostrar una salida diferente.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Una vez que el estado del contenedor se muestre como *Terminado* (use [az container show][az-container-show] para comprobar su estado), muestre sus registros para ver la salida.  Para ver la salida del contenedor sin variables de entorno, establezca -name para que sea mycontainer1 en lugar de mycontainer2.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

## <a name="azure-powershell-example"></a>Ejemplo de Azure PowerShell

Para ver la salida predeterminada del contenedor, ejecute el siguiente comando:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Al especificar `NumWords=5` y `MinLength=8` para las variables de entorno del contenedor, los registros de contenedor deben mostrar una salida diferente.

```azurepowershell-interactive
$envVars = @{NumWord=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Una vez que el estado del contenedor es *Terminado* (use [Get-AzureRmContainerInstanceLog][azure-instance-log] para comprobar su estado), muestre sus registros para ver la salida. Para ver la salida del contenedor sin variables de entorno, establezca ContainerGroupName para que sea mycontainer1 en lugar de mycontainer2.

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog `
    -ResourceGroupName myResourceGroup `
    -ContainerGroupName mycontainer2
```

## <a name="example-output-without-environment-variables"></a>Salida de ejemplo sin variables de entorno

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

## <a name="example-output-with-environment-variables"></a>Salida de ejemplo con variables de entorno

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que sabe cómo personalizar la entrada para el contenedor, aprenda a conservar la salida de los contenedores que se ejecutan hasta completarse.
> [!div class="nextstepaction"]
> [Montaje de un recurso compartido de archivos de Azure en Azure Container Instances](container-instances-mounting-azure-files-volume.md)

<!-- LINKS Internal -->
[azure-cloud-shell]: ../cloud-shell/overview.md
[azure-cli-install]: /cli/azure/
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show