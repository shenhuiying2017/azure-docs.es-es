---
title: Equilibrio de carga de máquinas virtuales con redundancia de zona mediante la CLI de Azure | Microsoft Docs
description: Aprenda a crear una instancia de Load Balancer Estándar público con front-end con redundancia de zona mediante la CLI de Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: kumud
ms.openlocfilehash: e469311609909e3453015702fca7d015a4e72398
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34273973"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Equilibrio de carga de máquinas virtuales en todas las zonas de disponibilidad con la CLI de Azure

Este artículo le ayudará a crear una instancia de [Load Balancer Estándar](https://aka.ms/azureloadbalancerstandard) público con un front-end con redundancia de zona para lograr redundancia de zona sin dependencia de varios registros de DNS. Una única dirección IP de servidor front-end tiene automáticamente redundancia de zona.  Mediante un front-end con redundancia de zona para su equilibrador de carga, con una sola dirección IP ahora puede alcanzar cualquier máquina virtual de una red virtual dentro de una región que se encuentra en todas las zonas de disponibilidad. Use zonas de disponibilidad para proteger sus datos y aplicaciones de la improbable pérdida o error de todo un centro de datos.

Para más información sobre cómo usar las zonas de disponibilidad con Load Balancer Estándar, consulte [Load Balancer Estándar y zonas de disponibilidad](load-balancer-standard-availability-zones.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Si decide instalar y usar la CLI en el sitio local, para este tutorial es preciso que ejecute la versión 2.0.17 o posterior de la CLI de Azure.  Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> El soporte técnico para las zonas de disponibilidad está disponible para recursos y regiones de Azure, y familias de tamaños de máquina virtual seleccionados. Para más información sobre cómo empezar a trabajar y qué recursos, regiones y familias de tamaños de máquina virtual de Azure puede probar con las zonas de disponibilidad, consulte la [introducción a las zonas de disponibilidad](https://docs.microsoft.com/azure/availability-zones/az-overview). Para soporte técnico, eche un vistazo a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) o [Creación de una solicitud de soporte técnico de Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroupSLB* en la ubicación *westeurope*:

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Creación de una dirección IP pública estándar con redundancia de zona
Para obtener acceso a la aplicación en Internet, necesita una dirección IP pública para el equilibrador de carga. Todas las zonas de disponibilidad de una región atienden a la vez a un servidor front-end con redundancia de zona. Cree una dirección IP pública con redundancia de zona con [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Al crear una dirección IP pública estándar, tiene redundancia de zona de forma predeterminada.

En el ejemplo siguiente se crea una dirección IP pública con redundancia de zona denominada *myPublicIP* en el grupo de recursos *myResourceGroupLoadBalancer*.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-load-balancer-standard"></a>Creación de Azure Load Balancer Estándar
En esta sección se detalla cómo se pueden crear y configurar los componentes siguientes del equilibrador de carga:
- Un grupo de direcciones IP de front-end que recibe el tráfico de red entrante en el equilibrador de carga.
- Un grupo de direcciones IP de back-end al que el grupo de servidores front-end envía el tráfico de red de carga equilibrada.
- Un sondeo de estado que determina el estado de las instancias de máquina virtual de back-end.
- Una regla de equilibrador de carga que define cómo se distribuye el tráfico a las máquinas virtuales.

### <a name="create-the-load-balancer"></a>Creación del equilibrador de carga
Cree una instancia de Load Balancer Estándar con [az network lb create](/cli/azure/network/lb#az_network_lb_create). En el ejemplo siguiente se crea un equilibrador de carga llamado *myLoadBalancer* y se asigna la dirección *myPublicIP* a la configuración de IP de front-end.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Creación de un sondeo de estado en el puerto 80

Los sondeos de estado comprueban todas las instancias de máquina virtual para asegurarse de que pueden enviar tráfico de red. La instancia de máquina virtual con comprobaciones de sondeo incorrectas se elimina del equilibrador de carga hasta que vuelve a estar en línea y una comprobación de sondeo determina que es correcta. Cree un sondeo de estado con az network lb probe create para supervisar el estado de las máquinas virtuales. Para crear un sondeo de estado TCP, debe usar el comando [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create). En el ejemplo siguiente se crea un sondeo de TCP denominado *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Creación de reglas del equilibrador de carga para el puerto 80
Una regla de equilibrador de carga define la configuración de la dirección IP de front-end para el tráfico entrante y el grupo de direcciones IP de back-end para recibir el tráfico, junto con el puerto de origen y destino requeridos. Cree una regla de equilibrador de carga *myLoadBalancerRuleWeb* con [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) para escuchar el puerto 80 en el grupo de servidores front-end *myFrontEndPool* y enviar tráfico de red con equilibrio de carga al grupo de direcciones de back-end *myBackEndPool*, que también usan el puerto 80.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
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

Cree una red virtual llamada *myVnet* con una subred llamada *mySubnet* en myResourceGroup con el comando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Crear un grupo de seguridad de red

Cree un grupo de seguridad de red llamado *myNetworkSecurityGroup* para definir las conexiones entrantes a su red virtual con [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Cree una regla de grupo de seguridad de red llamada *myNetworkSecurityGroupRule* para el puerto 80 con [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create).

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupSLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
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
Cree tres tarjetas de red virtuales con el comando [az network nic create](/cli/azure/network/nic#az_network_nic_create) y asócielas con la dirección IP pública y el grupo de seguridad de red. En el ejemplo siguiente se crean seis NIC virtuales. (Una NIC virtual para cada máquina virtual que cree para la aplicación en los pasos siguientes). Puede crear NIC virtuales y máquinas virtuales adicionales en cualquier momento y agregarlas al equilibrador de carga:

```azurecli-interactive
for i in `seq 1 3`; do
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
En este ejemplo, se crean tres máquinas virtuales ubicadas en la zona 1, en la zona 2 y en la zona 3 que se usarán como servidores back-end para el equilibrador de carga. También se debe instalar NGINX en las máquinas virtuales para comprobar que el equilibrador de carga se ha creado correctamente.

### <a name="create-cloud-init-config"></a>Creación de cloud-init config

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

### <a name="create-the-zonal-virtual-machines"></a>Creación de las máquinas virtuales zonales
Cree las máquinas virtuales con [az vm create](/cli/azure/vm#az_vm_create) en la zona 1, la zona 2 y la zona 3. El siguiente ejemplo crea una máquina virtual en cada zona y genera claves SSH si aún no existen:

Cree una máquina virtual en cada zona (zona 1, zona 2 y zona 3) de la ubicación *westeurope*.

```azurecli-interactive
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --zone $i \
    --custom-data cloud-init.txt
done
```
## <a name="test-the-load-balancer"></a>Prueba del equilibrador de carga

Obtenga la dirección IP pública del equilibrador de carga mediante [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
A continuación, puede escribir la dirección IP pública en un explorador web. Recuerde que tendrán que pasar unos minutos para que las máquinas virtuales estén listas antes de que el equilibrador de carga comience a distribuir el tráfico entre ellas. Se muestra la aplicación, incluido el nombre de host de la máquina virtual a la que el equilibrador de carga distribuye el tráfico como en el ejemplo siguiente:

![Ejecución de la aplicación Node.js](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Para ver el tráfico de distribución del equilibrador de carga entre máquinas virtuales en las tres zonas de disponibilidad que se ejecutan en su aplicación, puede detener una máquina virtual en una zona determinada y actualizar su explorador.

## <a name="next-steps"></a>Pasos siguientes
- Más información sobre [Load Balancer Estándar](./load-balancer-standard-overview.md)



