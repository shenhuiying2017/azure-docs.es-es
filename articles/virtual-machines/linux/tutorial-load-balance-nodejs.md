---
title: "Tutorial: Compilación de una aplicación de alta disponibilidad en máquinas virtuales de Azure | Microsoft Docs"
description: "Aprenda cómo crear una aplicación con alta disponibilidad y segura en tres máquinas virtuales Linux con un equilibrador de carga en Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 65ff0e03af6b43478f645274adfeb46d28418894
ms.lasthandoff: 04/07/2017

---

# <a name="build-a-load-balanced-highly-available-application-on-linux-virtual-machines-in-azure"></a>Compilación de una aplicación de alta disponibilidad y carga equilibrada en máquinas virtuales Linux en Azure
En este tutorial, creará una aplicación de alta disponibilidad que es resistente a los eventos de mantenimiento. La aplicación utiliza un equilibrador de carga, un conjunto de disponibilidad y tres máquinas virtuales Linux. Este tutorial compila una aplicación Node.js, aunque puede utilizarlo para implementar una plataforma de aplicación diferente usando los mismos componentes de alta disponibilidad y las mismas directrices.

## <a name="step-1---azure-prerequisites"></a>Paso 1: Requisitos previos de Azure
Para completar este tutorial, abra una ventana de terminal y asegúrese de que ha instalado la versión más reciente de [CLI de Azure 2.0](/cli/azure/install-azure-cli). Si no tiene iniciada sesión en la suscripción de Azure, hágalo con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla:

```azurecli
az login
```

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Para poder crear otros recursos de Azure, tiene que crear un grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```


## <a name="step-2---create-availability-set"></a>Paso 2: Creación de un conjunto de disponibilidad
Las máquinas virtuales se pueden crear en dominios de error lógico y actualización. Cada dominio lógico representa una parte del hardware del centro de datos de Azure subyacente. Cuando crea dos o más máquinas virtuales, los recursos de proceso y almacenamiento se distribuyen en estos dominios. Esta distribución mantiene la disponibilidad de la aplicación en el caso de que un componente de hardware deba someterse a mantenimiento. Los conjuntos de disponibilidad le permiten definir estos dominios de error lógico y actualización.

Cree el conjunto de disponibilidad con [az vm availability-set create](/cli/azure/vm/availability-set#create). En el ejemplo siguiente se crea un conjunto de disponibilidad denominado `myAvailabilitySet`:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 3 \
    --platform-update-domain-count 2
```


## <a name="step-3---create-load-balancer"></a>Paso 3: Creación de un equilibrador de carga
Un equilibrador de carga de Azure distribuye el tráfico a través de un conjunto de máquinas virtuales definidas usando reglas de equilibrador de carga. Un sondeo de estado supervisa un puerto determinado en cada máquina virtual y solo distribuye tráfico a una máquina virtual operativa.

### <a name="create-a-public-ip-address"></a>Crear una dirección IP pública
Para obtener acceso a la aplicación en Internet, asigne una dirección IP pública para el equilibrador de carga. Cree una dirección IP pública con [az network public-ip create](/cli/azure/public-ip#create). El ejemplo siguiente crea una dirección IP pública denominada `myPublicIP`:

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP
```

### <a name="create-a-load-balancer"></a>Crear un equilibrador de carga
Cree un equilibrador de carga con [az network lb create](/cli/azure/network/lb#create). En el ejemplo siguiente se crea un equilibrador de carga denominado `myLoadBalancer` utilizando la dirección `myPublicIP`:

```azurecli
az network lb create \
    --resource-group myResourceGroup \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>Creación de un sondeo de estado
Para permitir que el equilibrador de carga supervise el estado de la aplicación, utilice un sondeo de estado. El sondeo de estado agrega o quita de forma dinámica las máquinas virtuales de la rotación del equilibrador de carga en base a su respuesta a las comprobaciones de estado. De forma predeterminada, una máquina virtual se quita de la distribución del equilibrador de carga después de dos errores consecutivos en un intervalo de 15 segundos. Cree un sondeo de estado en función de un protocolo o una página de comprobación de mantenimiento específica para la aplicación. En el ejemplo siguiente se crea un sondeo TCP, aunque puede expandir este ejemplo y agregar `--path healthcheck.js`, por ejemplo. `healthcheck.js` tiene que crear y devolver una respuesta **HTTP 200 OK** para el equilibrador de carga para que el host se mantenga en rotación.

Cree el sondeo de estado con [az network lb probe create](/cli/azure/network/lb/probe#create). En el ejemplo siguiente se crea un sondeo de estado denominado `myHealthProbe` que supervisa cada máquina virtual:

```azurecli
az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>Creación de una regla de equilibrador de carga
Las reglas de equilibrador de carga se utilizan para definir cómo se distribuye el tráfico a las máquinas virtuales.

Cree una regla de equilibrador de carga con [az network lb rule create](/cli/azure/network/lb/rule#create). En el ejemplo siguiente se crea una regla denominada `myLoadBalancerRule`, que usa el sondeo de estado `myHealthProbe` y equilibra el tráfico en el puerto `80`:

```azurecli
az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="step-4---configure-networking"></a>Paso 4: Configuración de las redes
Cada máquina virtual tiene una o más tarjetas de interfaz de red virtual que se conectan a una red virtual. Esta red virtual está protegida para filtrar el tráfico basándose en reglas de acceso definidas.

### <a name="create-a-virtual-network"></a>Crear una red virtual
Para proporcionar conectividad de red a las máquinas virtuales, cree una red virtual con [az network vnet create](/cli/azure/vnet#create). En el ejemplo siguiente se crea una red virtual denominada `myVnet` con una subred llamada `mySubnet`:

```azurecli
az network vnet create --resource-group myResourceGroup --name myVnet --subnet-name mySubnet
```

### <a name="configure-network-security"></a>Configuración de la seguridad de red
Los grupos de seguridad de red utilizan reglas para controlar el flujo de tráfico hacia y desde las máquinas virtuales. Usted define estas reglas para, por ejemplo, permitir el tráfico en el puerto 80. Mientras que el equilibrador de carga distribuye el tráfico entre las máquinas virtuales, las reglas del grupo de seguridad de red se aseguran de que se admite solo el tráfico permitido.

Cree un grupo de seguridad de red con [az network nsg create](/cli/azure/network/nsg#create). En el ejemplo siguiente, se crea un grupo de seguridad de red denominado `myNetworkSecurityGroup`:

```azurecli
az network nsg create --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Para permitir que el tráfico de red llegue a la aplicación, cree una regla de grupo de seguridad de red con [az network nsg rule create](/cli/azure/network/nsg/rule#create). En el ejemplo siguiente, se crea un grupo de seguridad de red denominado `myNetworkSecurityGroupRule`:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

### <a name="create-virtual-network-interface-cards"></a>Creación de las tarjetas de interfaz de red virtual 
Los equilibradores de carga funcionan con el recurso de NIC virtual en lugar de con la máquina virtual real. La NIC virtual se conecta al equilibrador de carga y, a continuación, se adjunta a una máquina virtual.

Cree una NIC virtual con [az network nic create](/cli/azure/network/nic#create). En el ejemplo siguiente se crean tres NIC. (Una NIC virtual para cada máquina virtual que cree para la aplicación en los pasos siguientes):

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroup \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```


## <a name="step-5---build-your-app"></a>Paso 5: Creación de la aplicación
**cloud-init** es un enfoque ampliamente utilizado para personalizar una máquina virtual. Puede usar **cloud-init** para instalar paquetes y escribir archivos. Como **cloud-init** se ejecuta durante la implementación inicial, no hay ningún paso adicional para que la aplicación se ejecute. El equilibrador de carga empieza a distribuir el tráfico una vez que la máquina virtual ha finalizado la implementación y la aplicación se está ejecutando. Para más información sobre el uso de **cloud-init**, consulte [Uso de cloud-init para personalizar una máquina virtual Linux durante la creación](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

El siguiente configuración de **cloud-init** instala **nodejs** y **npm** y después instala y configura **nginx** como un proxy web para la aplicación. La configuración también crea una aplicación sencilla Node.js "Hola mundo", y luego inicializa e inicia la aplicación con **Express**. Si desea usar una plataforma de aplicación diferente, ajuste los paquetes y la aplicación implementada como corresponda.

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


## <a name="step-6---create-virtual-machines"></a>Paso 6: Creación de las máquinas virtuales
Con todos los componentes subyacentes en su lugar, ahora puede crear máquinas virtuales de alta disponibilidad para ejecutar la aplicación.

### <a name="create-vms"></a>Crear máquinas virtuales
Cree las máquinas virtuales con [az vm create](/cli/azure/vm#create). El siguiente ejemplo crea tres máquinas virtuales y genera claves SSH si aún no existen:

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroup \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image Canonical:UbuntuServer:14.04.4-LTS:latest \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

Se tarda unos minutos en crear y configurar las tres máquinas virtuales. El sondeo de estado del equilibrador de carga detecta automáticamente cuándo la aplicación se está ejecutando en cada máquina virtual. Una vez que la aplicación se esté ejecutando, la regla del equilibrador de carga empieza a distribuir el tráfico.

### <a name="test-your-app"></a>Prueba de la aplicación
Obtenga la dirección IP pública del equilibrador de carga con [az network public-ip show](/cli/azure/network/public-ip#show). En el ejemplo siguiente se obtiene la dirección IP de `myPublicIP` que se ha creado anteriormente:

```azurecli
az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

Escriba la dirección IP pública en un explorador web. Se muestra la aplicación, incluido el nombre de host de la máquina virtual a la que el equilibrador de carga distribuye el tráfico:

![Ejecución de la aplicación Node.js](./media/tutorial-load-balance-nodejs/running-nodejs-app.png)

Realice una actualización forzada del explorador web para ver cómo el equilibrador de carga distribuye el tráfico entre las tres máquinas virtuales que ejecutan la aplicación.


## <a name="step-7---management-tasks"></a>Paso 7: Tareas de administración
Puede que tenga que realizar labores de mantenimiento de las máquinas virtuales que ejecutan la aplicación, como la instalación de actualizaciones del sistema operativo. Para gestionar un aumento de tráfico a la aplicación, tiene que agregar más máquinas virtuales. Esta sección le muestra cómo quitar o agregar una máquina virtual desde el equilibrador de carga.

### <a name="remove-a-vm-from-the-load-balancer"></a>Eliminación de una máquina virtual del equilibrador de carga
Quite una máquina virtual del grupo de direcciones de back-end con [az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool#remove). En el ejemplo siguiente se quita la NIC virtual para **myVM2** de `myLoadBalancer`:

```azurecli
az network nic ip-config address-pool remove \
    --resource-group myResourceGroup \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

Realice una actualización forzada del explorador web para ver cómo el equilibrador de carga distribuye el tráfico entre las dos máquinas virtuales que quedan ejecutando la aplicación. Ahora puede realizar tareas de mantenimiento en la máquina virtual, como instalar actualizaciones del sistema operativo o realizar un reinicio de máquina virtual.

### <a name="add-a-vm-to-the-load-balancer"></a>Incorporación de una máquina virtual al equilibrador de carga
Después de realizar el mantenimiento en una máquina virtual, o si necesita expandir la capacidad, agregue una máquina virtual al grupo de direcciones de back-end con [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#add). En el ejemplo siguiente se agrega la NIC virtual para **myVM2** de `myLoadBalancer`:

```azurecli
az network nic ip-config address-pool add \
    --resource-group myResourceGroup \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```


## <a name="next-steps"></a>Pasos siguientes
Este tutorial crea una infraestructura de aplicaciones de alta disponibilidad con los recursos individuales de Azure. También puede utilizar conjuntos de escalado de máquinas virtuales para escalar o reducir verticalmente de forma automática el número de máquinas virtuales que ejecutan la aplicación. Continúe con el siguiente tutorial: [Creación de una aplicación de alta disponibilidad en Linux con conjuntos de escalado de máquinas virtuales](tutorial-convert-to-vmss.md).

Para aprender más sobre algunas de las características de alta disponibilidad de las que se ha dado una introducción en este tutorial, consulte la siguiente información:

- [Manage the availability of Linux virtual machines in Azure](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Administración de la disponibilidad de las máquinas virtuales Linux en Azure)
- [Información general sobre Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
- [Control del flujo de tráfico de red con grupos de seguridad de red](../../virtual-network/virtual-networks-nsg.md)
- [Scripts de ejemplo de CLI de Azure](../windows/cli-samples.md)
