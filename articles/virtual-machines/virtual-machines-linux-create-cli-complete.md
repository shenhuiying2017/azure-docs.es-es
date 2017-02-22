---
title: "Creación de un entorno de Linux mediante la CLI de Azure 2.0 | Microsoft Docs"
description: "Cree almacenamiento, una máquina virtual Linux, una red virtual y subred, un equilibrador de carga, una NIC, una dirección IP pública y un grupo de seguridad de red, todo desde el principio mediante la versión preliminar de la CLI de Azure 2.0."
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
ms.date: 12/8/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 39ce158ae52b978b74161cdadb4b886a7ddbf87a
ms.openlocfilehash: a00936df023ddbb13f5765f2e78900a68cccdb88


---
# <a name="create-a-complete-linux-environment-by-using-the-azure-cli-20-preview"></a>Creación de un entorno de Linux completo mediante la versión preliminar de la CLI de Azure 2.0
En este artículo, vamos a crear una red sencilla con un equilibrador de carga y un par de máquinas virtuales útiles para el desarrollo y la computación simple. Lo vamos a guiar por el proceso comando a comando, hasta que tenga funcionando dos máquinas virtuales Linux seguras a las que se pueda conectar desde cualquier parte de Internet. Luego, podrá pasar a redes y entornos más complejos.

Tras su lectura, conocerá la jerarquía de dependencias que ofrece el modelo de implementación de Resource Manager y la potencia que proporciona. Cuando vea cómo se compila el sistema, puede recompilarlo mucho más rápido mediante [plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Además, cuando vea cómo encajan las distintas partes del entorno, la creación de plantillas para automatizarlas se volverá más fácil.

El entorno contiene:

* Dos máquinas virtuales dentro de un conjunto de disponibilidad.
* Un equilibrador de carga con una regla de equilibrio de carga en el puerto 80.
* Reglas de grupo de seguridad de red (NSG) para proteger la máquina virtual del tráfico no deseado.

![Información general del entorno básico](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea
Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](virtual-machines-linux-create-cli-complete-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): la CLI para los modelos de implementación clásico y de Resource Manager
- [CLI de Azure 2.0 (versión preliminar)](#quick-commands): la CLI de última generación para el modelo de implementación de administración de recursos (este artículo)

## <a name="quick-commands"></a>Comandos rápidos
Si necesita realizar rápidamente la tarea, en la siguiente sección se detallan los comandos base para cargar una máquina virtual en Azure. En el resto del documento puede encontrar información más detallada y el contexto de cada paso, comenzando [aquí](#detailed-walkthrough).

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetros de ejemplo incluyen `myResourceGroup`, `mystorageaccount` y `myVM`.

Para crear este entorno personalizado, necesita la [versión preliminar de la CLI de Azure 2.0](/cli/azure/install-az-cli2) más reciente instalada y con la sesión iniciada en una cuenta de Azure mediante [az login](/cli/azure/#login).

En primer lugar, cree el grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `westeurope`:

```azurecli
az group create --name myResourceGroup --location westeurope
```

El paso siguiente es opcional. La acción predeterminada cuando se crea una VM con la CLI de Azure 2.0 (versión preliminar) consiste en usar Azure Managed Disks. Para más información sobre Azure Managed Disks, consulte [Azure Managed Disks overview](../storage/storage-managed-disks-overview.md) (Introducción a Azure Managed Disks). En cambio, si desea usar discos no administrados, debe crear una cuenta de almacenamiento con [az storage account create](/cli/azure/storage/account#create). En el ejemplo siguiente se crea una cuenta de almacenamiento denominada `mystorageaccount`. El nombre de la cuenta de almacenamiento debe ser único, así que indique su propio nombre único.

```azurecli
az storage account create --resource-group myResourceGroup --location westeurope \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Cree la red virtual con [az network vnet create](/cli/azure/network/vnet#create). En el ejemplo siguiente se crea una red virtual denominada `myVnet` y una subred llamada `mySubnet`:

```azurecli
az network vnet create --resource-group myResourceGroup --location westeurope --name myVnet \
  --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

Cree una dirección IP pública con [az network public-ip create](/cli/azure/network/public-ip#create). En el ejemplo siguiente se crea una IP pública denominada `myPublicIP` con el nombre DNS `mypublicdns`. El nombre DNS debe ser único, así que indique su propio nombre único.

```azurecli
az network public-ip create --resource-group myResourceGroup --location westeurope \
  --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

Cree el equilibrador de carga con [az network lb create](/cli/azure/network/lb#create). En el ejemplo siguiente:

- se crea un equilibrador de carga denominado `myLoadBalancer`
- se asocia la dirección IP pública `myPublicIP`
- se crea un grupo de direcciones IP front-end denominado `mySubnetPool`
- se crea un grupo de direcciones IP back-end denominado `myBackEndPool`

```azurecli
az network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer --public-ip-address myPublicIP \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool
```

Cree reglas NAT (traducción de direcciones de red) de entrada de SSH para el equilibrador de carga con [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#create). En el ejemplo siguiente se crean dos reglas del equilibrador de carga, `myLoadBalancerRuleSSH1` y `myLoadBalancerRuleSSH2`:

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 --protocol tcp \
  --frontend-port 4222 --backend-port 22 --frontend-ip-name myFrontEndPool
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22 --frontend-ip-name myFrontEndPool
```

Cree el sondeo de estado del equilibrador de carga con [az network lb probe create](/cli/azure/network/lb/probe#create). En el ejemplo siguiente se crea un sondeo de TCP denominado `myHealthProbe`:

```azurecli
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

Cree las reglas NAT de entrada web para el equilibrador de carga con [az network lb rule create](/cli/azure/network/lb/rule#create). En el ejemplo siguiente se crea una regla del equilibrador de carga denominada `myLoadBalancerRuleWeb`, asociándose al sondeo `myHealthProbe`:

```azurecli
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myLoadBalancerRuleWeb --protocol tcp --frontend-port 80 --backend-port 80 \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe
```

Compruebe el equilibrador de carga, los grupos de direcciones IP y las reglas NAT con [az network lb show](/cli/azure/network/lb#show):

```azurecli
az network lb show --resource-group myResourceGroup --name myLoadBalancer
```

Cree el grupo de seguridad de red con [az network nsg create](/cli/azure/network/nsg#create). En el ejemplo siguiente, se crea un grupo de seguridad de red denominado `myNetworkSecurityGroup`:

```azurecli
az network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Agregue dos reglas de entrada para el grupo de seguridad de red con [az network nsg rule create](/cli/azure/network/nsg/rule#create). En el ejemplo siguiente se crean dos reglas, `myNetworkSecurityGroupRuleSSH` y `myNetworkSecurityGroupRuleHTTP`:

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleSSH \
  --protocol tcp --direction inbound --priority 1000 --source-address-prefix '*' \
  --source-port-range '*' --destination-address-prefix '*' --destination-port-range 22 \
  --access allow
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleHTTP \
  --protocol tcp --direction inbound --priority 1001 --source-address-prefix '*' \
  --source-port-range '*' --destination-address-prefix '*' --destination-port-range 80 \
  --access allow
```

Cree la primera tarjeta de interfaz de red (NIC) con [az network nic create](/cli/azure/network/nic#create). En el ejemplo siguiente se crea una NIC denominada `myNic1` que se conecta al equilibrador de carga `myLoadBalancer` y los grupos adecuados, así como a `myNetworkSecurityGroup`:

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic1 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH1
```

Cree la segunda NIC, de nuevo con **az network nic create**. En el ejemplo siguiente se crea una tarjeta de interfaz de red denominada `myNic2`:

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic2 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH2
```

Cree el conjunto de disponibilidad con [az vm availability-set create](/cli/azure/vm/availability-set#create). En el ejemplo siguiente se crea un conjunto de disponibilidad denominado `myAvailabilitySet`:

```azurecli
az vm availability-set create --resource-group myResourceGroup --location westeurope \
  --name myAvailabilitySet
```

Cree la primera máquina virtual Linux con [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una VM denominada `myVM1` mediante Azure Managed Disks. Si desea usar discos no administrados, vea la nota adicional a continuación.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic1 \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Si usa Azure Managed Disks, omita este paso. Si desea usar discos no administrados y ha creado una cuenta de almacenamiento en los pasos anteriores, debe agregar algunos parámetros adicionales al comando de continuación. Agregue los siguientes parámetros adicionales al comando de continuación para crear los discos no administrados en la cuenta de almacenamiento denominada `mystorageaccount`: 

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
```

Cree la segunda máquina virtual Linux, de nuevo con **az vm create**. En el ejemplo siguiente se crea una máquina virtual denominada `myVM2`:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic2 \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

De nuevo, si no utiliza Azure Managed Disks de forma predeterminada, agregue los parámetros adicionales siguientes al comando de continuación para crear los discos no administrados en la cuenta de almacenamiento denominada `mystorageaccount`:

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
``` 

Compruebe que todo se ha creado correctamente con [az vm show](/cli/azure/vm#show):

```azurecli
az vm show --resource-group myResourceGroup --name myVM1
az vm show --resource-group myResourceGroup --name myVM2
```

Exporte el nuevo entorno a una plantilla con [az group export](/cli/azure/group#export) para volver a crear rápidamente nuevas instancias:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

## <a name="detailed-walkthrough"></a>Tutorial detallado
A través de los pasos detallados que aparecen a continuación se explica lo que hace cada comando mientras crea su entorno. Estos conceptos sirven de ayuda mientras crea sus propios entornos personalizados con fines de desarrollo o producción.

Asegúrese de que ha instalado la [versión preliminar de la CLI de Azure 2.0](/cli/azure/install-az-cli2) e iniciado sesión en una cuenta de Azure con [az login](/cli/azure/#login).

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetros de ejemplo incluyen `myResourceGroup`, `mystorageaccount` y `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Creación de grupos de recursos y elección de las ubicaciones para la implementación
Los grupos de recursos de Azure son entidades de implementación lógicas que contienen información de configuración y metadatos que permiten la administración lógica de las implementaciones de recursos. Cree el grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `westeurope`:

```azurecli
az group create --name myResourceGroup --location westeurope
```

De forma predeterminada, la salida está en JSON (notación de objetos JavaScript). Para la generación como lista o tabla, por ejemplo, use [az configure --output](/cli/azure/#configure). También puede agregar `--output` a cualquier comando para un cambio específico del formato de salida. En el ejemplo siguiente se muestra la salida JSON desde el comando **az group create**:

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
El paso siguiente es opcional. La acción predeterminada cuando se crea una VM con la CLI de Azure 2.0 (versión preliminar) consiste en usar Azure Managed Disks. Estos discos se controlan mediante la plataforma de Azure y no requieren preparativos ni ubicación para el almacenamiento. Para más información sobre Azure Managed Disks, consulte [Azure Managed Disks overview](../storage/storage-managed-disks-overview.md) (Introducción a Azure Managed Disks). Vaya a [Creación de una red virtual y una subred](#create-a-virtual-network-and-subnet) si desea usar Azure Managed Disks. 

Si desea usar discos no administrados, necesita crear una cuenta de almacenamiento para los discos de la VM y para todos los discos de datos adicionales que desea agregar.

Aquí se usa [az storage account create](/cli/azure/storage/account#create) y se pasa la ubicación de la cuenta, el grupo de recursos que la controla y el tipo de soporte de almacenamiento que quiere. En el ejemplo siguiente se crea una cuenta de almacenamiento denominada `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westeurope \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Salida:

```json
{
  "accessTier": null,
  "creationTime": "2016-12-07T17:59:50.090092+00:00",
  "customDomain": null,
  "encryption": null,
  "id": "/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "westeurope",
  "name": "mystorageaccount",
  "primaryEndpoints": {
    "blob": "https://mystorageaccount.blob.core.windows.net/",
    "file": "https://mystorageaccount.file.core.windows.net/",
    "queue": "https://mystorageaccount.queue.core.windows.net/",
    "table": "https://mystorageaccount.table.core.windows.net/"
  },
  "primaryLocation": "westeurope",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "secondaryEndpoints": null,
  "secondaryLocation": null,
  "sku": {
    "name": "Standard_LRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "Available",
  "statusOfSecondary": null,
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

Para investigar la cuenta de almacenamiento mediante la CLI, en primer lugar será preciso establecer primero los nombres y claves de la cuenta. Use [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Reemplace el nombre de la cuenta de almacenamiento en el siguiente ejemplo por un nombre de su elección:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(az storage account show-connection-string --resource-group myResourceGroup --name mystorageaccount --query connectionString)"
```

A continuación, puede ver la información de almacenamiento con [az storage container list](/cli/azure/storage/container#list):

```azurecli
az storage container list
```

Salida:

```azurecli
[
  {
    "metadata": null,
    "name": "vhds",
    "properties": {
      "etag": "\"0x8D41F912D472F94\"",
      "lastModified": "2016-12-08T17:39:35+00:00",
      "lease": {
        "duration": null,
        "state": null,
        "status": null
      },
      "leaseDuration": "infinite",
      "leaseState": "leased",
      "leaseStatus": "locked"
    }
  }
]
```

## <a name="create-a-virtual-network-and-subnet"></a>Creación de una red virtual y una subred
Después va a necesitar crear una red virtual que se ejecute en Azure y una subred en la que pueda crear sus máquinas virtuales. En el ejemplo siguiente se usa [az network vnet create](/cli/azure/network/vnet#create) para crear una red virtual denominada `myVnet` con el prefijo de dirección `192.168.0.0/16` y una subred llamada `mySubnet` con el prefijo de dirección de la subred `192.168.1.0/24`:

```azurecli
az network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefix 192.168.0.0/16 \
  --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

La salida muestra la subred como creada lógicamente dentro de la red virtual:

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "westeurope",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Crear una dirección IP pública
Ahora, vamos a crear la dirección IP pública (PIP) que asignamos a su equilibrador de carga. Le permite conectarse a sus máquinas virtuales desde Internet mediante el comando [az network public-ip create](/cli/azure/network/public-ip#create). Dado que la dirección predeterminada es dinámica, creamos una entrada DNS con nombre en el dominio **cloudapp.azure.com** con la opción `--domain-name-label`. En el ejemplo siguiente se crea una IP pública denominada `myPublicIP` con el nombre DNS `mypublicdns`. Como el nombre DNS debe ser único, especifique su propio nombre DNS único:

```azurecli
az network public-ip create --resource-group myResourceGroup --location westeurope \
  --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

Salida:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.westeurope.cloudapp.azure.com",
      "reverseFqdn": ""
    },
    "idleTimeoutInMinutes": 4,
    "ipAddress": "52.174.48.109",
    "provisioningState": "Succeeded",
    "publicIPAllocationMethod": "Static",
    "resourceGuid": "78218510-ea54-42d7-b2c2-44773fc14af5"
  }
}
```

El recurso de la dirección IP pública se ha asignado de forma lógica, pero aún no hay ninguna dirección específica asignada. Para obtener una dirección IP, va a necesitar un equilibrador de carga, que todavía no se ha creado.

## <a name="create-a-load-balancer-and-ip-pools"></a>Creación de un equilibrador de carga y grupos de direcciones IP
Al crear un equilibrador de carga, puede distribuir el tráfico en varias máquinas virtuales. También proporciona redundancia a la aplicación mediante la ejecución de varias máquinas virtuales que responden a las solicitudes de los usuarios en caso de que haya tareas de mantenimiento o cargas elevadas. En el ejemplo siguiente se usa [az network lb create](/cli/azure/network/lb#create) para crear un equilibrador de carga denominado `myLoadBalancer` y un grupo de direcciones IP front-end llamado `myFrontEndPool`, y se conecta el recurso `myPublicIP`:

```azurecli
az network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool
```

Salida:

```json
{
  "loadBalancer": {
    "backendAddressPools": [
      {
        "etag": "W/\"7a05df7a-5ee2-4581-b411-4b6f048ab291\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
        "name": "myLoadBalancerbepool",
        "properties": {
          "provisioningState": "Succeeded"
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "frontendIPConfigurations": [
      {
        "etag": "W/\"7a05df7a-5ee2-4581-b411-4b6f048ab291\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
        "name": "myFrontEndPool",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "publicIPAddress": {
            "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
            "resourceGroup": "myResourceGroup"
          }
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "inboundNatPools": [],
    "inboundNatRules": [],
    "loadBalancingRules": [],
    "outboundNatRules": [],
    "probes": [],
    "provisioningState": "Succeeded",
    "resourceGuid": "f4879d84-5ae8-4e06-8b9b-1419baa875d9"
  }
}
```

Tenga en cuenta cómo se ha usado el modificador `--public-ip-address` para pasar el `myPublicIP` creado anteriormente. Asignar la dirección IP pública al equilibrador de carga le permite comunicarse con sus máquinas virtuales a través de Internet.

Usamos un grupo back-end como ubicación de conexión para nuestras máquinas virtuales. De este modo, el tráfico puede fluir a través del equilibrador de carga hacia las máquinas virtuales. Vamos a crear el grupo de direcciones IP de nuestro tráfico back-end con [az network lb address-pool create](/cli/azure/network/lb/address-pool#create). En el siguiente ejemplo se crea un grupo de back-end denominado `myBackEndPool`:

```azurecli
az network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Salida cortada:

```json
  "backendAddressPools": [
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
      "loadBalancingRules": null,
      "name": "myLoadBalancerbepool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    },
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
      "loadBalancingRules": null,
      "name": "myBackEndPool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    }
  ],
  "etag": "W/\"a61814bc-ce4c-4fb2-9d6a-5b1949078345\"",
```


## <a name="create-load-balancer-nat-rules"></a>Creación de reglas NAT del equilibrador de carga
Para que el tráfico fluya a través de nuestro equilibrador de carga, es preciso crear reglas NAT (traducción de direcciones de red) que especifiquen acciones de entrada o de salida. Puede determinar el protocolo que va a usarse y, luego, asignar los puertos externos a los internos que desee. Para nuestro entorno, vamos a crear algunas reglas que habiliten SSH en nuestras máquinas virtuales a través de nuestro equilibrador de carga con [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#create). Configuramos los puertos TCP 4222 y 4223 para dirigir el tráfico al puerto TCP 22 de nuestras máquinas virtuales (que crearemos más adelante). En el ejemplo siguiente, se crea una regla denominada `myLoadBalancerRuleSSH1` para asignar el tráfico del puerto TCP 4222 al puerto 22:

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 --protocol tcp \
  --frontend-port 4222 --backend-port 22 --frontend-ip-name myFrontEndPool
```

Salida:

```json
{
  "backendIpConfiguration": null,
  "backendPort": 22,
  "enableFloatingIp": false,
  "etag": "W/\"72843cf8-b5fb-4655-9ac8-9cbbbbf1205a\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "frontendPort": 4222,
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
  "idleTimeoutInMinutes": 4,
  "name": "myLoadBalancerRuleSSH1",
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup"
}
```

Repita el procedimiento con la segunda regla NAT para SSH. En el ejemplo siguiente se crea una regla denominada `myLoadBalancerRuleSSH2` para asignar el tráfico del puerto TCP 4223 al puerto 22:

```azurecli
az network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22 --frontend-ip-name myFrontEndPool
```

## <a name="create-a-load-balancer-health-probe"></a>Creación del sondeo de estado de un equilibrador de carga
Una acción de sondeo de estado comprueba periódicamente las máquinas virtuales que están detrás de nuestro equilibrador de carga para asegurarse de que funcionan y responden a las solicitudes, tal como se define. De lo contrario, se quitarán de la operación para asegurarse de que no se redirigen los usuarios a dichas máquinas virtuales. Puede definir comprobaciones personalizadas para el sondeo de estado, junto con intervalos y valores de tiempo de espera. Para más información sobre los sondeos de estado, consulte [Sondeos del Equilibrador de carga](../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). En el ejemplo siguiente se usa [az network lb probe create](/cli/azure/network/lb/probe#create) para crear un estado TCP sondeado denominado `myHealthProbe`:

```azurecli
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80 --interval 15 --threshold 4
```

Salida:

```json
{
  "etag": "W/\"757018f6-b70a-4651-b717-48b511d82ba0\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe",
  "intervalInSeconds": 15,
  "loadBalancingRules": null,
  "name": "myHealthProbe",
  "numberOfProbes": 4,
  "port": 80,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "requestPath": null,
  "resourceGroup": "myResourceGroup"
}
```

Aquí, especificamos un intervalo de 15 segundos para las comprobaciones de estado. Podemos perder un máximo de cuatro sondeos (un minuto) antes de que el equilibrador de carga considere que el host ha dejado de funcionar.

Continuemos y creemos una regla NAT para el puerto TCP 80 para tráfico web, que enlaza la regla a nuestros grupos IP. Si enlazamos la regla a un grupo de direcciones IP, en lugar de enlazarla a nuestras máquinas virtuales individualmente, podemos agregar o quitar máquinas virtuales del grupo de direcciones IP. El equilibrador de carga ajusta automáticamente el flujo de tráfico. En el ejemplo siguiente se usa [az network lb rule create](/cli/azure/network/lb/rule#create) para crear una regla denominada `myLoadBalancerRuleWeb` a fin de asignar el puerto TCP 80 al puerto 80, y se conecta el sondeo de estado llamado `myHealthProbe`:

```azurecli
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myLoadBalancerRuleWeb --protocol tcp --frontend-port 80 --backend-port 80 \
  --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe
```

Salida:

```json
{
  "backendAddressPool": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "backendPort": 80,
  "enableFloatingIp": false,
  "etag": "W/\"f0d77680-bf42-4d11-bfab-5d2c541bee56\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
    "resourceGroup": "myResourceGroup"
  },
  "frontendPort": 80,
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
  "idleTimeoutInMinutes": 4,
  "loadDistribution": "Default",
  "name": "myLoadBalancerRuleWeb",
  "probe": {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe",
    "resourceGroup": "myResourceGroup"
  },
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="verify-the-load-balancer"></a>Comprobación del equilibrador de carga
Ahora se ha realizado la configuración del equilibrador de carga. Estos son los pasos adoptados:

1. Creó un equilibrador de carga.
2. Creó un grupo de direcciones IP front-end y asignó una dirección IP pública al mismo.
3. Creó un grupo de direcciones IP back-end al que pueden conectarse las máquinas virtuales.
4. Creó reglas NAT que permiten SSH en las máquinas virtuales con fines de administración, junto con una regla que permite el puerto TCP 80 en nuestra aplicación web.
5. Agregó un sondeo de estado para comprobar periódicamente las máquinas virtuales. Este sondeo de estado garantiza que los usuarios no intenten acceder a una máquina virtual que ya no funciona ni proporciona contenido.

Revisemos cuál es ahora el aspecto del equilibrador de carga con [az network lb show](/cli/azure/network/lb#show):

```azurecli
az network lb show --resource-group myResourceGroup --name myLoadBalancer
```

Salida:

```json
{
  "backendAddressPools": [
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myLoadBalancerbepool",
      "loadBalancingRules": null,
      "name": "myLoadBalancerbepool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    },
    {
      "backendIpConfigurations": null,
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
      "loadBalancingRules": null,
      "name": "myBackEndPool",
      "outboundNatRule": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup"
    }
  ],
  "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
  "frontendIpConfigurations": [
    {
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/LoadBalancerFrontEnd",
      "inboundNatPools": null,
      "inboundNatRules": null,
      "loadBalancingRules": null,
      "name": "LoadBalancerFrontEnd",
      "outboundNatRules": null,
      "privateIpAddress": null,
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Succeeded",
      "publicIpAddress": {
        "dnsSettings": null,
        "etag": null,
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/PublicIPmyLoadBalancer",
        "idleTimeoutInMinutes": null,
        "ipAddress": null,
        "ipConfiguration": null,
        "location": null,
        "name": null,
        "provisioningState": null,
        "publicIpAddressVersion": null,
        "publicIpAllocationMethod": null,
        "resourceGroup": "myResourceGroup",
        "resourceGuid": null,
        "tags": null,
        "type": null
      },
      "resourceGroup": "myResourceGroup",
      "subnet": null
    },
    {
      "etag": "W/\"a0e313a1-6de1-48be-aeb6-df57188d708c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/frontendIPConfigurations/myFrontEndPool",
      "inboundNatPools": null,
      "inboundNatRules": null,
      "loadBalancingRules": null,
      "name": "myFrontEndPool",
      "outboundNatRules": null,
      "privateIpAddress": null,
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Succeeded",
      "publicIpAddress": {
        "dnsSettings": null,
        "etag": null,
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
        "idleTimeoutInMinutes": null,
        "ipAddress": null,
        "ipConfiguration": null,
        "location": null,
        "name": null,
        "provisioningState": null,
        "publicIpAddressVersion": null,
        "publicIpAllocationMethod": null,
        "resourceGroup": "myResourceGroup",
        "resourceGuid": null,
        "tags": null,
        "type": null
      },
      "resourceGroup": "myResourceGroup",
      "subnet": null
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "inboundNatPools": [],
  "inboundNatRules": [],
  "loadBalancingRules": [],
  "location": "westeurope",
  "name": "myLoadBalancer",
  "outboundNatRules": [],
  "probes": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "b5815801-b53d-4c22-aafc-2a9064f90f3c",
  "tags": {},
  "type": "Microsoft.Network/loadBalancers"
}
```

## <a name="create-a-network-security-group-and-rules"></a>Creación de un grupo de seguridad de red (NSG) y las reglas
Ahora creamos un grupo de seguridad de red y las reglas de entrada que rigen el acceso a la tarjeta de interfaz de red. Un grupo de seguridad de red puede aplicarse a una tarjeta de interfaz de red o a una subred. Defina reglas para controlar el flujo de tráfico que entra y sale de las máquinas virtuales. En el ejemplo siguiente se usa [az network nsg create](/cli/azure/network/nsg#create) para crear un grupo de seguridad de red denominado `myNetworkSecurityGroup`:

```azurecli
az network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Vamos a agregar la regla de entrada del NSG con [az network nsg rule create](/cli/azure/network/nsg/rule#create) para permitir conexiones de entrada en el puerto 22 (para admitir SSH). En el ejemplo siguiente, se crea una regla denominada `myNetworkSecurityGroupRuleSSH` para permitir TCP en el puerto 22:

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleSSH \
  --protocol tcp --direction inbound --priority 1000 \
  --source-address-prefix '*' --source-port-range '*' \
  --destination-address-prefix '*' --destination-port-range 22 --access allow
```

Ahora vamos a agregar la regla de entrada del NSG para permitir conexiones de entrada en el puerto 80 (para admitir el tráfico web). En el ejemplo siguiente, se crea una regla denominada `myNetworkSecurityGroupRuleHTTP` para permitir TCP en el puerto 80:

```azurecli
az network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRuleHTTP \
  --protocol tcp --direction inbound --priority 1001 \
  --source-address-prefix '*' --source-port-range '*' \
  --destination-address-prefix '*' --destination-port-range 80 --access allow
```

> [!NOTE]
> La regla de entrada es un filtro para las conexiones de red entrantes. En este ejemplo, enlazamos el NSG a la NIC virtual de las máquinas virtuales, lo que significa que cualquier solicitud que se realice al puerto 22 se pasa a la NIC de nuestra máquina virtual. Esta regla de entrada es sobre una conexión de red y no sobre un punto de conexión, como en el caso de las implementaciones clásicas. Para abrir un puerto, es preciso dejar `--source-port-range` establecido en "\*" (el valor predeterminado) para aceptar las solicitudes entrantes de **cualquier** puerto solicitante. Los puertos suelen ser dinámicos.

Examine el grupo de seguridad de red y las reglas con [az network nsg create](/cli/azure/network/nsg#show):

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Salida:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBound",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "westeurope",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "79c2c293-ee3e-4616-bf9c-4741ff1f708a",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"2b656589-f332-4ba4-92f3-afb3be5c192c\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleHTTP",
      "name": "myNetworkSecurityGroupRuleHTTP",
      "priority": 1001,
      "protocol": "tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": {},
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Creación de una NIC para usarla con la máquina virtual Linux
Las NIC están disponibles de manera programática porque puede aplicar reglas a su uso. También puede tener más de una. En el siguiente comando [az network nic create](https://docs.microsoft.com/en-us/cli/azure/network/nic#create), se enlaza la NIC al grupo de direcciones IP back-end de carga y se asocia a la regla NAT para permitir el tráfico SSH y el grupo de seguridad de red.

En el ejemplo siguiente se crea una tarjeta de interfaz de red denominada `myNic1`:

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic1 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH1
```

Salida:

```json
{
  "newNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": []
    },
    "enableIPForwarding": false,
    "ipConfigurations": [
      {
        "etag": "W/\"a76b5c0d-14e1-4a99-afd4-5cd8ac0465ca\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1",
        "name": "ipconfig1",
        "properties": {
          "loadBalancerBackendAddressPools": [
            {
              "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool",
              "resourceGroup": "myResourceGroup"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
              "resourceGroup": "myResourceGroup"
            }
          ],
          "primary": true,
          "privateIPAddress": "192.168.1.4",
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "subnet": {
            "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
            "resourceGroup": "myResourceGroup"
          }
        },
        "resourceGroup": "myResourceGroup"
      }
    ],
    "networkSecurityGroup": {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "resourceGroup": "myResourceGroup"
    },
    "provisioningState": "Succeeded",
    "resourceGuid": "838977cb-cf4b-4c4a-9a32-0ddec7dc818b"
  }
}
```

Ahora vamos a crear la segunda NIC, que, de nuevo, se enlaza a nuestro grupo de direcciones IP back-end. Esta vez, la segunda regla NAT permite el tráfico SSH. En el ejemplo siguiente se crea una tarjeta de interfaz de red denominada `myNic2`:

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic2 \
  --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup \
  --lb-name myLoadBalancer --lb-address-pools myBackEndPool \
  --lb-inbound-nat-rules myLoadBalancerRuleSSH2
```


## <a name="create-an-availability-set"></a>Crear un conjunto de disponibilidad
Los conjuntos de disponibilidad ayudan a propagar las máquinas virtuales a los dominios de error y dominios de actualización. Vamos a crear un conjunto de disponibilidad para sus máquinas virtuales con [az vm availability-set create](/cli/azure/vm/availability-set#create). En el ejemplo siguiente se crea un conjunto de disponibilidad denominado `myAvailabilitySet`:

```azurecli
az vm availability-set create --resource-group myResourceGroup --location westeurope \
  --name myAvailabilitySet
```

Los dominios de error definen un grupo de máquinas virtuales que comparten una fuente de alimentación común y un conmutador de red. De manera predeterminada, las máquinas virtuales configuradas dentro de su conjunto de disponibilidad se separan en hasta tres dominios de error. La idea es que un problema de hardware en uno de estos dominios de error no afecte a cada máquina virtual que ejecute la aplicación. Azure distribuye automáticamente las máquinas virtuales en los dominios de error al incluirlos en un conjunto de disponibilidad.

Los dominios de actualización indican grupos de máquinas virtuales y hardware físico subyacente que se pueden reiniciar al mismo tiempo. Es posible que el orden de reinicio de los dominios de actualización no sea secuencial durante el mantenimiento planeado, pero solo se reinicia una actualización cada vez. De nuevo, Azure distribuye automáticamente las máquinas virtuales en los dominios de actualización al incluirlos en un sitio de disponibilidad.

Lea más sobre cómo [administrar la disponibilidad de las máquinas virtuales](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="create-the-linux-vms"></a>Creación de las máquinas virtuales Linux
Ha creado los recursos de red para dar soporte a VM con acceso a Internet. Ahora crearemos dichas máquinas virtuales y las protegeremos con una clave SSH sin contraseña. En este caso, vamos a crear una máquina virtual con Ubuntu basada en la LTM más reciente. Vamos a buscar esa información de la imagen mediante [az vm image list](/cli/azure/vm/image#list), tal como se describe en el artículo sobre cómo [buscar imágenes de máquina virtual de Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

También especificamos una clave SSH para usarla para la autenticación. Si no tiene ninguna clave SSH, puede crearlas mediante [estas instrucciones](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Como alternativa, puede usar el método `--admin-password` para autenticar sus conexiones SSH una vez creada la máquina virtual. Este método suele ser menos seguro.

Se crea la VM, para lo que se recopila toda la información y los recursos con el comando [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una VM denominada `myVM1` mediante Azure Managed Disks. Si desea usar discos no administrados, vea la nota adicional a continuación.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic1 \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Si usa Azure Managed Disks, omita este paso. Si desea usar discos no administrados y ha creado una cuenta de almacenamiento en los pasos anteriores, debe agregar algunos parámetros adicionales al comando de continuación. Agregue los siguientes parámetros adicionales al comando de continuación para crear los discos no administrados en la cuenta de almacenamiento denominada `mystorageaccount`: 

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
```

Salida:

```json
{
  "fqdn": "",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1",
  "macAddress": "",
  "privateIpAddress": "",
  "publicIpAddress": "",
  "resourceGroup": "myResourceGroup"
}
```

Puede conectarse de inmediato a la máquina virtual con las claves SSH predeterminadas. Asegúrese de especificar el puerto adecuado, ya que las estamos pasando a través del equilibrador de carga. Para nuestra primera máquina virtual, configuramos la regla NAT para reenviar el puerto 4222 a nuestra máquina virtual.

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222 -i ~/.ssh/id_rsa.pub
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
az vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --availability-set myAvailabilitySet \
    --nics myNic2 \
    --vnet myVnet \
    --subnet-name mySubnet \
    --nsg myNetworkSecurityGroup \
    --image UbuntuLTS \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

De nuevo, si no utiliza Azure Managed Disks de forma predeterminada, agregue los parámetros adicionales siguientes al comando de continuación para crear los discos no administrados en la cuenta de almacenamiento denominada `mystorageaccount`:

```azurecli
  --use-unmanaged-disk \
  --storage-account mystorageaccount
``` 

Llegados a este punto, ejecuta las máquinas virtuales con Ubuntu detrás de un equilibrador de carga en Azure en las que solo puede iniciar sesión con el par de claves SSH (porque las contraseñas están deshabilitadas). Puede instalar nginx o httpd, implementar una aplicación web y ver cómo fluye el tráfico a las dos máquinas virtuales a través del equilibrador de carga.


## <a name="export-the-environment-as-a-template"></a>Exportación del entorno como una plantilla
Ahora que ha creado este entorno, ¿y si quiere crear un entorno de desarrollo adicional con los mismos parámetros o un entorno de producción correspondiente? Resource Manager usa plantillas JSON que definen todos los parámetros de su entorno. Puede crear entornos enteros haciendo referencia a esta plantilla JSON. Puede [compilar plantillas JSON manualmente](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o exportar un entorno existente para que la plantilla JSON se cree automáticamente. Use [az group export](/cli/azure/group#export) para exportar su grupo de recursos de la siguiente manera:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Este comando crea el archivo `myResourceGroup.json` en el directorio de trabajo actual. Al crear un entorno a partir de esta plantilla, se le piden todos los nombres de recursos, incluidos los del equilibrador de carga, las interfaces de red o las máquinas virtuales. Para rellenar estos nombres en el archivo de plantilla, agregue el parámetro `--include-parameter-default-value` al comando **az group export** mostrado antes. Edite su plantilla JSON para especificar los nombres de recursos o [cree un archivo parameters.json](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que especifique los nombres de recursos.

Para crear un entorno desde su plantilla, use [az group deployment create](/cli/azure/group/deployment#create) de la siguiente manera:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Es posible que quiera leer [más sobre cómo realizar implementaciones desde plantillas](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Obtenga información sobre cómo actualizar los entornos de manera incremental, usar el archivo de parámetros y tener acceso a las plantillas desde una única ubicación de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes
Ya está listo para empezar a trabajar con varios componentes de red y máquinas virtuales. Puede usar este entorno de ejemplo para crear la aplicación con los componentes principales aquí presentados.



<!--HONumber=Feb17_HO2-->


