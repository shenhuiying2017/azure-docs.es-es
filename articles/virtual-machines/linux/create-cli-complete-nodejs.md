---
title: "Creación de un entorno de Linux completo con la CLI de Azure 1.0 | Microsoft Docs"
description: "Cree almacenamiento, una máquina virtual Linux, una red virtual y subred, un equilibrador de carga, una NIC, una dirección IP pública, un grupo de seguridad de red, todo desde el principio mediante la CLI de Azure 1.0."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 201ccd523e49d638ace50fbc0ffdceb705b35473
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="create-a-complete-linux-environment-with-the-azure-cli-10"></a>Creación de un entorno de Linux completo con la CLI de Azure 1.0
En este artículo, vamos a crear una red sencilla con un equilibrador de carga y un par de máquinas virtuales útiles para el desarrollo y la computación simple. Lo vamos a guiar por el proceso comando a comando, hasta que tenga funcionando dos máquinas virtuales Linux seguras a las que se pueda conectar desde cualquier parte de Internet. Luego, podrá pasar a redes y entornos más complejos.

Tras su lectura, conocerá la jerarquía de dependencias que ofrece el modelo de implementación de Resource Manager y la potencia que proporciona. Cuando vea cómo se compila el sistema, puede recompilarlo mucho más rápido mediante [plantillas de Azure Resource Manager](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Además, cuando vea cómo encajan las distintas partes del entorno, la creación de plantillas para automatizarlas se volverá más fácil.

El entorno contiene:

* Dos máquinas virtuales dentro de un conjunto de disponibilidad.
* Un equilibrador de carga con una regla de equilibrio de carga en el puerto 80.
* Reglas de grupo de seguridad de red (NSG) para proteger la máquina virtual del tráfico no deseado.

Para crear este entorno personalizado, es preciso tener la versión más reciente de la [CLI de Azure 1.0](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) en modo de Resource Manager (`azure config mode arm`). También necesitará una herramienta de análisis JSON. En este ejemplo se usa [jq](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea
Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](#quick-commands): la CLI para los modelos de implementación clásico y de Resource Manager (este artículo)
- [CLI de Azure 2.0](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): la CLI de última generación para el modelo de implementación de administración de recursos


## <a name="quick-commands"></a>Comandos rápidos
Si necesita realizar rápidamente la tarea, en la siguiente sección se detallan los comandos base para cargar una máquina virtual en Azure. En el resto del documento puede encontrar información más detallada y el contexto de cada paso, comenzando [aquí](#detailed-walkthrough).

Asegúrese de haber iniciado sesión en la [CLI de Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 1.0 y que usa el modo de Resource Manager:

```azurecli
azure config mode arm
```

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetros de ejemplo incluyen `myResourceGroup`, `mystorageaccount` y `myVM`.

Cree el grupo de recursos. En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `westeurope`:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Compruebe el grupo de recursos mediante el analizador JSON:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Cree la cuenta de almacenamiento. En el ejemplo siguiente se crea una cuenta de almacenamiento denominada `mystorageaccount`. El nombre de la cuenta de almacenamiento debe ser único, así que indique su propio nombre único.

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Compruebe la cuenta de almacenamiento mediante el analizador JSON:

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Creación de la red virtual. En el ejemplo siguiente se crea una red virtual denominada `myVnet`:

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Cree una subred. En el ejemplo siguiente se crea una subred denominada `mySubnet`:

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Compruebe la red virtual y la subred mediante el analizador JSON:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Cree una IP pública. En el ejemplo siguiente se crea una IP pública denominada `myPublicIP` con el nombre DNS `mypublicdns`. El nombre DNS debe ser único, así que indique su propio nombre único.

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Cree el equilibrador de carga. En el ejemplo siguiente se crea un equilibrador de carga denominado `myLoadBalancer`:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Cree un grupo IP de front-end para el equilibrador de carga y asocie la IP pública. En el siguiente ejemplo se crea un grupo IP de front-end denominado `mySubnetPool`:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Cree el grupo IP de back-end para el equilibrador de carga. En el siguiente ejemplo se crea un grupo IP de back-end denominado `myBackEndPool`:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Cree reglas NAT (traducción de direcciones de red) de entrada de SSH para el equilibrador de carga. En el ejemplo siguiente se crean dos reglas del equilibrador de carga, `myLoadBalancerRuleSSH1` y `myLoadBalancerRuleSSH2`:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Cree las reglas NAT de entrada web para el equilibrador de carga. En el ejemplo siguiente se crea un regla del equilibrador de carga denominada `myLoadBalancerRuleWeb`:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Cree el sondeo de estado del equilibrador de carga. En el ejemplo siguiente se crea un sondeo de TCP denominado `myHealthProbe`:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Compruebe el equilibrador de carga, los grupos de direcciones IP y las reglas NAT mediante el analizador JSON:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Cree la primera tarjeta de interfaz de red (NIC). Reemplace las secciones `#####-###-###` por su propio identificador de suscripción de Azure. Su identificador de suscripción se indica en la salida de **jq** al examinar los recursos que va a crear. Su identificador de suscripción también puede verlo con `azure account list`.

En el ejemplo siguiente se crea una tarjeta de interfaz de red denominada `myNic1`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Cree la segunda tarjeta de interfaz de red. En el ejemplo siguiente se crea una tarjeta de interfaz de red denominada `myNic2`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Compruebe las dos tarjeta de interfaz de red con el analizador JSON:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Cree el grupo de seguridad de red. En el ejemplo siguiente, se crea un grupo de seguridad de red denominado `myNetworkSecurityGroup`:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Agregue dos reglas de entrada para el grupo de seguridad de red. En el ejemplo siguiente se crean dos reglas, `myNetworkSecurityGroupRuleSSH` y `myNetworkSecurityGroupRuleHTTP`:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Compruebe el grupo de seguridad de red y las reglas de entrada mediante el analizador JSON:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Enlace el grupo de seguridad de red a las dos tarjetas de interfaz de red:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Cree el conjunto de disponibilidad. En el ejemplo siguiente se crea un conjunto de disponibilidad denominado `myAvailabilitySet`:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Cree la primera máquina virtual Linux. En el ejemplo siguiente se crea una máquina virtual denominada `myVM1`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Cree la segunda máquina virtual Linux. En el ejemplo siguiente se crea una máquina virtual denominada `myVM2`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Use el analizador JSON para comprobar todo lo que se creó:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exporte el entorno compilado a una plantilla para volver a crear rápidamente nuevas instancias:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Tutorial detallado
A través de los pasos detallados que aparecen a continuación se explica lo que hace cada comando mientras crea su entorno. Estos conceptos sirven de ayuda mientras crea sus propios entornos personalizados con fines de desarrollo o producción.

Asegúrese de haber iniciado sesión en la [CLI de Azure](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 1.0 y que usa el modo de Resource Manager:

```azurecli
azure config mode arm
```

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetros de ejemplo incluyen `myResourceGroup`, `mystorageaccount` y `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Creación de grupos de recursos y elección de las ubicaciones para la implementación
Los grupos de recursos de Azure son entidades de implementación lógicas que contienen información de configuración y metadatos que permiten la administración lógica de las implementaciones de recursos. En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `westeurope`:

```azurecli
azure group create --name myResourceGroup --location westeurope
```

Salida:

```azurecli                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
Las cuentas de almacenamiento son necesarias tanto para los discos de su máquina virtual como para cualquier otro disco de datos adicionales que desee agregar. Las cuentas de almacenamiento se crean casi inmediatamente después de crear los grupos de recursos.

Aquí se usa el comando `azure storage account create` , que pasa la ubicación de la cuenta, el grupo de recursos que la controla y el tipo de soporte de almacenamiento que quiere. En el ejemplo siguiente se crea una cuenta de almacenamiento denominada `mystorageaccount`:

```azurecli
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Salida:

```azurecli
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Para examinar nuestro grupo de recursos mediante el comando `azure group show`, vamos a usar la herramienta [jq](https://stedolan.github.io/jq/) junto con la opción `--json` de la CLI de Azure. (Para analizar el código JSON se puede usar **jsawk** o cualquier biblioteca de idioma que prefiera).

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Salida:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Para investigar la cuenta de almacenamiento mediante la CLI, en primer lugar será preciso establecer primero los nombres y claves de la cuenta. Reemplace el nombre de la cuenta de almacenamiento en el siguiente ejemplo por un nombre de su elección:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Luego podrá ver la información de almacenamiento fácilmente:

```azurecli
azure storage container list
```

Salida:

```azurecli
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Creación de una red virtual y una subred
Después va a necesitar crear una red virtual que se ejecute en Azure y una subred en la que pueda crear sus máquinas virtuales. En el ejemplo siguiente se crea una red virtual denominada `myVnet` con un prefijo de la dirección `192.168.0.0/16`:

```azurecli
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16
```

Salida:

```azurecli
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Una vez más, vamos a usar la opción --json de `azure group show` y `jq` para ver cómo creamos nuestros recursos. Ya tenemos un recurso de `storageAccounts` y un recurso de `virtualNetworks`.  

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Salida:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Ahora vamos a crear una subred en la red virtual `myVnet` en la que se implementan las máquinas virtuales. Vamos a usar el comando `azure network vnet subnet create`, junto con los recursos que ya hemos creado: el grupo de recursos `myResourceGroup` y la red virtual `myVnet`. En el ejemplo siguiente, se agrega la subred denominada `mySubnet` con el prefijo de dirección de subred `192.168.1.0/24`:

```azurecli
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Salida:

```azurecli
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Dado que la subred está lógicamente dentro de la red virtual, buscamos la información de la subred con un comando un poco diferente. El comando que usamos es `azure network vnet show`, pero continuamos examinando la salida JSON mediante `jq`.

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Salida:

```json
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address"></a>Crear una dirección IP pública
Ahora, vamos a crear la dirección IP pública (PIP) que asignamos a su equilibrador de carga. Le permite conectarse a sus máquinas virtuales desde Internet mediante el comando `azure network public-ip create` . Dado que la dirección predeterminada es dinámica, creamos una entrada DNS con nombre en el dominio **cloudapp.azure.com** con la opción `--domain-name-label`. En el ejemplo siguiente se crea una IP pública denominada `myPublicIP` con el nombre DNS `mypublicdns`. Como el nombre DNS debe ser único, especifique su propio nombre DNS único:

```azurecli
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Salida:

```azurecli
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

La dirección IP pública también es un recurso de nivel superior, por lo que se puede ver con `azure group show`.

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Salida:

```json
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Puede investigar más detalles del recurso, incluido el nombre de dominio completo (FQDN) del subdominio, mediante el comando `azure network public-ip show` completo. El recurso de la dirección IP pública se ha asignado de forma lógica, pero aún no hay ninguna dirección específica asignada. Para obtener una dirección IP, va a necesitar un equilibrador de carga, que todavía no se ha creado.

```azurecli
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Salida:

```json
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Creación de un equilibrador de carga y grupos de direcciones IP
Al crear un equilibrador de carga, puede distribuir el tráfico en varias máquinas virtuales. También proporciona redundancia a la aplicación mediante la ejecución de varias máquinas virtuales que responden a las solicitudes de los usuarios en caso de que haya tareas de mantenimiento o cargas elevadas. En el ejemplo siguiente se crea un equilibrador de carga denominado `myLoadBalancer`:

```azurecli
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Salida:

```azurecli
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Nuestro equilibrador de carga está bastante vacío, así que vamos a crear algunos grupos de direcciones IP. Deseamos crear dos grupos IP para nuestro equilibrador de carga: uno para el front-end y otro para el back-end. El grupo de direcciones IP front-end se ve de forma pública. También es la ubicación a la que asignamos la PIP creada antes. A continuación, usamos el grupo back-end como ubicación de conexión para nuestras máquinas virtuales. De este modo, el tráfico puede fluir a través del equilibrador de carga hacia las máquinas virtuales.

En primer lugar, vamos a crear nuestro grupo IP de front-end. En el siguiente ejemplo se crea un grupo de front-end denominado `myFrontEndPool`:

```azurecli
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool
```

Salida:

```azurecli
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Tenga en cuenta cómo se ha usado el modificador `--public-ip-name` para pasar el `myPublicIP` creado anteriormente. Asignar la dirección IP pública al equilibrador de carga le permite comunicarse con sus máquinas virtuales a través de Internet.

Después, vamos a crear nuestro el grupo IP, esta vez para el tráfico de back-end. En el siguiente ejemplo se crea un grupo de back-end denominado `myBackEndPool`:

```azurecli
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Salida:

```azurecli
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Podemos ver cómo está el equilibrador de carga buscando la salida JSON con `azure network lb show` y examinándolo:

```azurecli
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Salida:

```json
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Creación de reglas NAT del equilibrador de carga
Para que el tráfico fluya a través de nuestro equilibrador de carga, es preciso crear reglas NAT (traducción de direcciones de red) que especifiquen acciones de entrada o de salida. Puede determinar el protocolo que va a usarse y, luego, asignar los puertos externos a los internos que desee. Para nuestro entorno, vamos a crear algunas reglas que habiliten SSH en nuestras máquinas virtuales a través de nuestro equilibrador de carga. Configuramos los puertos TCP 4222 y 4223 para dirigir el tráfico al puerto TCP 22 de nuestras máquinas virtuales (que crearemos más adelante). En el ejemplo siguiente, se crea una regla denominada `myLoadBalancerRuleSSH1` para asignar el tráfico del puerto TCP 4222 al puerto 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Salida:

```azurecli
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Repita el procedimiento con la segunda regla NAT para SSH. En el ejemplo siguiente se crea una regla denominada `myLoadBalancerRuleSSH2` para asignar el tráfico del puerto TCP 4223 al puerto 22:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Continuemos y creemos una regla NAT para el puerto TCP 80 para tráfico web, que enlaza la regla a nuestros grupos IP. Si enlazamos la regla a un grupo de direcciones IP, en lugar de enlazarla a nuestras máquinas virtuales individualmente, podemos agregar o quitar máquinas virtuales del grupo de direcciones IP. El equilibrador de carga ajusta automáticamente el flujo de tráfico. En el ejemplo siguiente, se crea una regla denominada `myLoadBalancerRuleWeb` para asignar el tráfico del puerto TCP 80 al puerto 80:

```azurecli
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Salida:

```azurecli
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Creación del sondeo de estado de un equilibrador de carga
Una acción de sondeo de estado comprueba periódicamente las máquinas virtuales que están detrás de nuestro equilibrador de carga para asegurarse de que funcionan y responden a las solicitudes, tal como se define. De lo contrario, se quitarán de la operación para asegurarse de que no se redirigen los usuarios a dichas máquinas virtuales. Puede definir comprobaciones personalizadas para el sondeo de estado, junto con intervalos y valores de tiempo de espera. Para más información sobre los sondeos de estado, consulte [Sondeos del Equilibrador de carga](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). En el ejemplo siguiente se crea un sondeo del estado del TCP denominado `myHealthProbe`:

```azurecli
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Salida:

```azurecli
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Aquí, especificamos un intervalo de 15 segundos para las comprobaciones de estado. Podemos perder un máximo de cuatro sondeos (un minuto) antes de que el equilibrador de carga considere que el host ha dejado de funcionar.

## <a name="verify-the-load-balancer"></a>Comprobación del equilibrador de carga
Ahora se ha realizado la configuración del equilibrador de carga. Estos son los pasos adoptados:

1. Creó un equilibrador de carga.
2. Creó un grupo de direcciones IP front-end y asignó una dirección IP pública al mismo.
3. Creó un grupo de direcciones IP back-end al que pueden conectarse las máquinas virtuales.
4. Creó reglas NAT que permiten SSH en las máquinas virtuales con fines de administración, junto con una regla que permite el puerto TCP 80 en nuestra aplicación web.
5. Agregó un sondeo de estado para comprobar periódicamente las máquinas virtuales. Este sondeo de estado garantiza que los usuarios no intenten acceder a una máquina virtual que ya no funciona ni proporciona contenido.

Revisemos cuál es ahora el aspecto del equilibrador de carga:

```azurecli
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Salida:

```json
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Creación de una NIC para usarla con la máquina virtual Linux
Las NIC están disponibles de manera programática porque puede aplicar reglas a su uso. También puede tener más de una. En el siguiente comando `azure network nic create` se enlaza la NIC al grupo de direcciones IP back-end de carga y se asocia a la regla NAT para permitir el tráfico SSH.

Reemplace las secciones `#####-###-###` por su propio identificador de suscripción de Azure. Su identificador de suscripción se indica en la salida de `jq` al examinar los recursos que va a crear. Su identificador de suscripción también puede verlo con `azure account list`.

En el ejemplo siguiente se crea una tarjeta de interfaz de red denominada `myNic1`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Salida:

```azurecli
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

Para ver los detalles, examine directamente el recurso. Examine el recurso mediante el comando `azure network nic show` :

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Salida:

```json
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Ahora vamos a crear la segunda NIC, que, de nuevo, se enlaza a nuestro grupo de direcciones IP back-end. Esta vez, la segunda regla NAT permite el tráfico SSH. En el ejemplo siguiente se crea una tarjeta de interfaz de red denominada `myNic2`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Creación de un grupo de seguridad de red (NSG) y las reglas
Ahora creamos un grupo de seguridad de red y las reglas de entrada que rigen el acceso a la tarjeta de interfaz de red. Un grupo de seguridad de red puede aplicarse a una tarjeta de interfaz de red o a una subred. Defina reglas para controlar el flujo de tráfico que entra y sale de las máquinas virtuales. En el ejemplo siguiente, se crea un grupo de seguridad de red denominado `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Vamos a agregar la regla de entrada del NSG para permitir conexiones de entrada en el puerto 22 (para admitir SSH). En el ejemplo siguiente, se crea una regla denominada `myNetworkSecurityGroupRuleSSH` para permitir TCP en el puerto 22:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Ahora vamos a agregar la regla de entrada del NSG para permitir conexiones de entrada en el puerto 80 (para admitir el tráfico web). En el ejemplo siguiente, se crea una regla denominada `myNetworkSecurityGroupRuleHTTP` para permitir TCP en el puerto 80:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> La regla de entrada es un filtro para las conexiones de red entrantes. En este ejemplo, enlazamos el NSG a la NIC virtual de las máquinas virtuales, lo que significa que cualquier solicitud que se realice al puerto 22 se pasa a la NIC de nuestra máquina virtual. Esta regla de entrada es sobre una conexión de red y no sobre un punto de conexión, como en el caso de las implementaciones clásicas. Para abrir un puerto, es preciso dejar `--source-port-range` establecido en "\*" (el valor predeterminado) para aceptar las solicitudes entrantes de **cualquier** puerto solicitante. Los puertos suelen ser dinámicos.
>
>

## <a name="bind-to-the-nic"></a>Enlace a la NIC
Enlace el NSG a las tarjetas de interfaz de red. Necesitamos conectar nuestras tarjetas de interfaz de red con nuestro grupo de seguridad de red. Ejecute ambos comandos para enlazar nuestras dos tarjetas de interfaz de red:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Crear un conjunto de disponibilidad
Los conjuntos de disponibilidad ayudan a propagar las máquinas virtuales a los dominios de error y dominios de actualización. Vamos a crear un conjunto de disponibilidad para sus máquinas virtuales. En el ejemplo siguiente se crea un conjunto de disponibilidad denominado `myAvailabilitySet`:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Los dominios de error definen un grupo de máquinas virtuales que comparten una fuente de alimentación común y un conmutador de red. De manera predeterminada, las máquinas virtuales configuradas dentro de su conjunto de disponibilidad se separan en hasta tres dominios de error. La idea es que un problema de hardware en uno de estos dominios de error no afecte a cada máquina virtual que ejecute la aplicación. Azure distribuye automáticamente las máquinas virtuales en los dominios de error al incluirlos en un conjunto de disponibilidad.

Los dominios de actualización indican grupos de máquinas virtuales y hardware físico subyacente que se pueden reiniciar al mismo tiempo. Es posible que el orden de reinicio de los dominios de actualización no sea secuencial durante el mantenimiento planeado, pero solo se reinicia una actualización cada vez. De nuevo, Azure distribuye automáticamente las máquinas virtuales en los dominios de actualización al incluirlos en un sitio de disponibilidad.

Lea más sobre cómo [administrar la disponibilidad de las máquinas virtuales](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Creación de las máquinas virtuales Linux
Ha creado los recursos de almacenamiento y de red necesarios para dar soporte a máquinas virtuales con acceso a Internet. Ahora crearemos dichas máquinas virtuales y las protegeremos con una clave SSH sin contraseña. En este caso, vamos a crear una máquina virtual con Ubuntu basada en la LTM más reciente. Vamos a buscar esa información de la imagen mediante `azure vm image list`, tal como se describe en el artículo sobre cómo [buscar imágenes de máquina virtual de Azure](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Hemos seleccionado una imagen mediante el comando `azure vm image list westeurope canonical | grep LTS`. En este caso, usamos `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Para el último campo, pasamos `latest` para que en el futuro siempre obtengamos la compilación más reciente. (La cadena que usamos es `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

El siguiente paso resultará familiar para quien ya haya creado un par de claves pública y privada ssh-rsa en Linux o Mac mediante **ssh-keygen -t rsa -b 2048**. Si no tiene ningún par de claves de certificado en el directorio `~/.ssh` , puede crearlas:

* Automáticamente, mediante la opción `azure vm create --generate-ssh-keys` .
* Manualmente, mediante [las instrucciones para crearlas usted mismo](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Como alternativa, puede usar el método `--admin-password` para autenticar sus conexiones SSH una vez creada la máquina virtual. Este método suele ser menos seguro.

Creamos la máquina virtual, para lo que reunimos toda nuestra información y recursos con el comando `azure vm create` :

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Salida:

```azurecli
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Puede conectarse de inmediato a la máquina virtual con las claves SSH predeterminadas. Asegúrese de especificar el puerto adecuado, ya que las estamos pasando a través del equilibrador de carga. Para nuestra primera máquina virtual, configuramos la regla NAT para reenviar el puerto 4222 a nuestra máquina virtual.

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Salida:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Continúe y cree su segunda máquina virtual de la misma manera:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Ya puede usar el comando `azure vm show myResourceGroup myVM1` para examinar lo que ha creado. Llegados a este punto, ejecuta las máquinas virtuales con Ubuntu detrás de un equilibrador de carga en Azure en las que solo puede iniciar sesión con el par de claves SSH (porque las contraseñas están deshabilitadas). Puede instalar nginx o httpd, implementar una aplicación web y ver cómo fluye el tráfico a las dos máquinas virtuales a través del equilibrador de carga.

```azurecli
azure vm show --resource-group myResourceGroup --name myVM1
```

Salida:

```azurecli
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Exportación del entorno como una plantilla
Ahora que ha creado este entorno, ¿y si quiere crear un entorno de desarrollo adicional con los mismos parámetros o un entorno de producción correspondiente? Resource Manager usa plantillas JSON que definen todos los parámetros de su entorno. Puede crear entornos enteros haciendo referencia a esta plantilla JSON. Puede [compilar plantillas JSON manualmente](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o exportar un entorno existente para que la plantilla JSON se cree automáticamente:

```azurecli
azure group export --name myResourceGroup
```

Este comando crea el archivo `myResourceGroup.json` en el directorio de trabajo actual. Al crear un entorno a partir de esta plantilla, se le piden todos los nombres de recursos, incluidos los del equilibrador de carga, las interfaces de red o las máquinas virtuales. Para rellenar estos nombres en el archivo de plantilla, agregue el parámetro `-p` o `--includeParameterDefaultValue` al comando `azure group export` mostrado antes. Edite su plantilla JSON para especificar los nombres de recursos o [cree un archivo parameters.json](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que especifique los nombres de recursos.

Para crear un entorno a partir de la plantilla:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Es posible que quiera leer [más sobre cómo realizar implementaciones desde plantillas](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Obtenga información sobre cómo actualizar los entornos de manera incremental, usar el archivo de parámetros y tener acceso a las plantillas desde una única ubicación de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes
Ya está listo para empezar a trabajar con varios componentes de red y máquinas virtuales. Puede usar este entorno de ejemplo para crear la aplicación con los componentes principales aquí presentados.
