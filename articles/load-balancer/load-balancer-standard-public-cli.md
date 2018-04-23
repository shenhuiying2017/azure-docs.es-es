---
title: 'Creación de una instancia de Load Balancer Estándar público: CLI de Azure | Microsoft Docs'
description: Aprenda a crear un equilibrador de carga público mediante la CLI de Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: kumud
ms.openlocfilehash: ee2e70e710be9badda7318aec307325da66e4c58
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-standard-load-balancer-to-load-balance-vms-using-azure-cli-20"></a>Cree una instancia de Load Balancer Estándar para equilibrar la carga de máquinas virtuales mediante la CLI de Azure 2.0

En este tutorial rápido se muestra cómo crear una instancia de Load Balancer Estándar. Para probar el equilibrador de carga, implemente dos máquinas virtuales (VM) que ejecutan un servidor Ubuntu y equilibre la carga de una aplicación web entre ellas.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure versión 2.0.28 o versiones posteriores. Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con [az group create](https://docs.microsoft.com/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroupSLB* en la ubicación *eastus*:

```azurecli-interactive
  az group create \
    --name myResourceGroupSLB \
    --location eastus
```

## <a name="create-a-public-standard-ip-address"></a>Creación de una dirección IP estándar pública

Para obtener acceso a la aplicación web en Internet, necesita una dirección IP pública para el equilibrador de carga. La versión Estándar de Load Balancer solo admite direcciones IP públicas estándar. Use [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) para crear una dirección IP pública estándar denominada *myPublicIP* en *myResourceGroupSLB*.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard
```

## <a name="create-azure-load-balancer"></a>Creación del equilibrador de carga de Azure

En esta sección se detalla cómo se pueden crear y configurar los componentes siguientes del equilibrador de carga:
  - Un grupo de direcciones IP de front-end que recibe el tráfico de red entrante en el equilibrador de carga.
  - Un grupo de direcciones IP de back-end al que el grupo de servidores front-end envía el tráfico de red de carga equilibrada.
  - Un sondeo de estado que determina el estado de las instancias de máquina virtual de back-end.
  - Una regla de equilibrador de carga que define cómo se distribuye el tráfico a las máquinas virtuales.

### <a name="create-the-load-balancer"></a>Creación del equilibrador de carga

Cree con [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) una instancia de Azure Load Balancer pública llamada **myLoadBalancer** que incluya un grupo de servidores front-end llamado **myFrontEnd** y un grupo de servidores back-end llamado **myBackEndPool** que esté asociado a la dirección IP pública **myPublicIP** que creó en el paso anterior.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupSLB \
    --name myLoadBalancer \
    --sku standard
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
  ```

### <a name="create-the-health-probe"></a>Creación del sondeo de estado

Los sondeos de estado comprueban todas las instancias de máquina virtual para asegurarse de que pueden enviar tráfico de red. La instancia de máquina virtual con comprobaciones de sondeo incorrectas se elimina del equilibrador de carga hasta que vuelve a estar en línea y una comprobación de sondeo determina que es correcta. Cree un sondeo de estado con [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create) para supervisar el estado de las máquinas virtuales. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Creación de la regla de equilibrador de carga

Una regla de equilibrador de carga define la configuración de la dirección IP de front-end para el tráfico entrante y el grupo de direcciones IP de back-end para recibir el tráfico, junto con el puerto de origen y destino requeridos. Cree una regla de equilibrador de carga *myLoadBalancerRuleWeb* con [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create) para escuchar el puerto 80 en el grupo de servidores front-end *myFrontEnd* y enviar tráfico de red con equilibrio de carga al conjunto de direcciones back-end *myBackEndPool*, que también usan el puerto 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="configure-virtual-network"></a>Configurar la red virtual

Antes de implementar algunas máquinas virtuales y poder probar el equilibrador de carga, cree los recursos de red virtual auxiliares.

### <a name="create-a-virtual-network"></a>Crear una red virtual

Cree una red virtual llamada *myVnet* con una subred llamada *mySubnet* en *myResourceGroup* con el comando [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupSLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```
###  <a name="create-a-network-security-group"></a>Crear un grupo de seguridad de red

En el caso de una instancia de Load Balancer Estándar, las máquinas virtuales de la dirección de back-end deben tener tarjetas NIC que pertenezcan a un grupo de seguridad de red. Cree un grupo de seguridad de red para definir las conexiones entrantes a la red virtual.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupSLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Creación de una regla de grupo de seguridad de red

Cree una regla de grupo de seguridad de red para permitir las conexiones entrantes a través del puerto 80.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupSLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```
### <a name="create-nics"></a>Cree tarjetas NIC

Cree tres interfaces de red con el comando [az network nic create](/cli/azure/network/nic#az_network_nic_create) y asócielas con la dirección IP pública y el grupo de seguridad de red. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```


## <a name="create-backend-servers"></a>Creación de servidores back-end

En este ejemplo, se crean tres máquinas virtuales que se usarán como servidores back-end para el equilibrador de carga. Para comprobar que el equilibrador de carga se ha creado correctamente, también se debe instalar NGINX en las máquinas virtuales.

### <a name="create-an-availability-set"></a>Creación de un conjunto de disponibilidad

Cree un conjunto de disponibilidad con [az vm availabilityset create](/cli/azure/network/nic#az_network_availabilityset_create)

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupSLB \
    --name myAvailabilitySet
```

### <a name="create-two-virtual-machines"></a>Creación de dos máquinas virtuales

Puede usar un archivo de configuración cloud-init para instalar NGINX y ejecutar una aplicación Node.js "Hola mundo" en una máquina virtual Linux. En el shell actual, cree un archivo denominado cloud-init.txt, y copie y pegue la siguiente configuración en el shell. Asegúrese de copiar correctamente todo el archivo cloud-init, especialmente la primera línea:

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
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
``` 
 
Cree las máquinas virtuales con [az vm create](/cli/azure/vm#az_vm_create).

 ```azurecli-interactive
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    --no-wait
    done
```
Es posible que la implementación de las máquinas virtuales tarde unos minutos.

## <a name="test-the-load-balancer"></a>Prueba del equilibrador de carga

Para obtener la dirección IP pública del equilibrador de carga, use [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copie la dirección IP pública y péguela en la barra de direcciones del explorador.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
   ![Prueba del equilibrador de carga](./media/load-balancer-standard-public-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, puede usar el comando [az group delete](/cli/azure/group#az_group_delete) para quitar el grupo de recursos, el equilibrador de carga y todos los recursos relacionados.

```azurecli-interactive 
  az group delete --name myResourceGroupSLB
```
## <a name="next-step"></a>Paso siguiente
Más información sobre [Load Balancer Estándar](load-balancer-standard-overview.md)

