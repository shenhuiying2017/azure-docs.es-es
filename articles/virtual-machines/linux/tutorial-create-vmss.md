---
title: "Creación de una aplicación de alta disponibilidad en Azure con conjuntos de escalado de máquinas virtuales | Microsoft Docs"
description: "Cree e implemente una aplicación de alta disponibilidad en máquinas virtuales Linux con un conjunto de escalado de máquinas virtuales y la CLI de Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 4c76fb202f501f671504646395b800aeb90d8e69
ms.lasthandoff: 04/07/2017

---

# <a name="create-a-highly-available-application-on-linux-with-virtual-machine-scale-sets"></a>Creación de una aplicación de alta disponibilidad en Linux con conjuntos de escalado de máquinas virtuales
En este tutorial se muestra cómo puede crear una aplicación de alta disponibilidad en un conjunto de escalado de máquinas virtuales. También aprenderá a automatizar la configuración de las máquinas virtuales en el conjunto de escalado. 


## <a name="step-1---create-a-resource-group"></a>Paso 1: Creación de un grupo de recursos
Para completar este tutorial, asegúrese de que ha instalado la versión más reciente de [CLI de Azure 2.0](/cli/azure/install-azure-cli). Si no tiene iniciada sesión en la suscripción de Azure, hágalo con el comando [az login](/cli/azure/#login) y siga las instrucciones en pantalla.

Cree un grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroupVMSS` en la ubicación `westus`:

```azurecli
az group create --name myResourceGroupVMSS --location westus
```


## <a name="step-2---define-your-app"></a>Paso 2: Definición de la aplicación
Use la misma configuración **cloud-init** del tutorial donde se ha creado una carga alta disponibilidad, de equilibrio de carga. Para más información sobre el uso de **cloud-init**, consulte [Uso de cloud-init para personalizar una máquina virtual Linux durante la creación](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Cree un archivo denominado `cloud-init.txt` y pegue la siguiente configuración:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - nginx -s reload
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## <a name="step-3---create-scale-set"></a>Paso 3: Creación del conjunto de escalado
El conjunto de escalado de máquinas virtuales le permite implementar y administrar un conjunto de máquinas virtuales de escalado automático idénticas. Los conjuntos de escalado usan los mismos componentes que ha aprendido en el tutorial para [compilar una aplicación de alta disponibilidad en Azure](tutorial-load-balance-nodejs.md). Estos componentes se incluyen como conjuntos de disponibilidad, dominios de error y actualización, y equilibradores de carga.

Con un conjunto de escalado, estos recursos se crean y se administran en su nombre. Puede aumentar o disminuir automáticamente el número de máquinas virtuales en el conjunto de escalado en función de reglas definidas. También puede [usar una imagen personalizada](capture-image.md) como origen de la máquina virtual, o configurar las máquinas virtuales durante la implementación con **cloud-init** como se muestra en este tutorial.

Cree un conjunto de escalado de máquinas virtuales con [az vmss create](/cli/azure/vmss#create). En el ejemplo siguiente se crea un conjunto de escalado denominado `myScaleSet`:

```azurecli
az vmss create \
  --resource-group myResourceGroupVMSS \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

Se tardan unos minutos en crear y configurar todos los recursos de conjunto de escalado y máquinas virtuales.


## <a name="step-4---configure-firewall"></a>Paso 4: Configuración del firewall
Se creó automáticamente un equilibrador de carga como parte del conjunto de escalado de máquinas virtuales. El equilibrador de carga distribuye el tráfico a través de un conjunto de máquinas virtuales definidas usando reglas de equilibrador de carga. Para permitir que el tráfico llegue a la aplicación web, cree una regla con [az network lb probe create](/cli/azure/network/lb/probe#create). En el ejemplo siguiente se crea una regla denominada `myLoadBalancerRuleWeb`:

```azurecli
az network lb rule create \
  --resource-group myResourceGroupVMSS \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="step-5---test-your-app"></a>Paso 5: Prueba de la aplicación
Obtenga la dirección IP pública del equilibrador de carga con [az network public-ip show](/cli/azure/network/public-ip#show). En el ejemplo siguiente se obtiene la dirección IP de `myScaleSetLBPublicIP` que se ha creado como parte del conjunto de escalado:

```azurecli
az network public-ip show \
    --resource-group myResourceGroupVMSS \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Escriba la dirección IP pública en un explorador web. Se muestra la aplicación, incluido el nombre de host de la máquina virtual a la que el equilibrador de carga distribuye el tráfico:

![Ejecución de la aplicación Node.js](./media/tutorial-load-balance-nodejs/running-nodejs-app.png)

Realice una actualización forzada del explorador web para ver cómo el equilibrador de carga distribuye el tráfico entre las máquinas virtuales del conjunto de escalado que ejecutan la aplicación.


## <a name="step-6---management-tasks"></a>Paso 6: Tareas de administración
Durante el ciclo de vida del conjunto de escalado, debe ejecutar una o varias tareas de administración. Además, puede crear scripts para automatizar varias tareas de ciclo de vida. La CLI de Azure 2.0 proporciona una forma rápida de realizar esas tareas. A continuación, presentamos algunas tareas comunes.

### <a name="increase-or-decrease-vm-instances"></a>Aumento o disminución de instancias de máquina virtual
Puede aumentar o reducir manualmente el número de máquinas virtuales del conjunto de escalado con [az vmss scale](/cli/azure/vmss#scale). En el ejemplo siguiente se aumenta el número de máquinas virtuales en el conjunto de escalado en `5`:

```azurecli
az vmss scale --resource-group myResourceGroupVMSS --name myScaleSet --new-capacity 5
```

Las reglas de escalado automático le permiten definir cómo escalar o reducir verticalmente el número de máquinas virtuales en el conjunto de escalado en respuesta a la demanda, como el tráfico de red o el uso de CPU. Actualmente, no se pueden establecer estas reglas en la CLI de Azure 2.0. Use [Azure Portal](https://portal.azure.com) para configurar el escalado automático.

### <a name="get-connection-info"></a>Obtención de información de conexión
Para obtener información de conexión sobre las máquinas virtuales de los conjuntos de escalado, use [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info). Este comando da como resultado la dirección IP pública y los puertos de cada máquina virtual que le permiten conectarse con SSH:

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroupVMSS --name myScaleSet
```

### <a name="delete-resource-group"></a>Eliminación de un grupo de recursos
Al eliminar un grupo de recursos se eliminan también todos los recursos contenidos en el mismo.

```azurecli
az group delete --name myResourceGroupVMSS
```


## <a name="next-steps"></a>Pasos siguientes
En este tutorial, hemos definido la aplicación web con **cloud-init** y configurado cada máquina virtual durante la implementación. Para obtener información sobre cómo capturar una máquina virtual que se usará como la imagen de origen en el conjunto de escalado, consulte [Procedimiento para generalizar y capturar una máquina virtual Linux](capture-image.md).

Para aprender más sobre algunas de las características del conjunto de escalado de máquinas virtuales presentadas en este tutorial, consulte la siguiente información:

- [Introducción a los conjuntos de escalado de máquinas virtuales de Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Información general sobre Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
- [Control del flujo de tráfico de red con grupos de seguridad de red](../../virtual-network/virtual-networks-nsg.md)
