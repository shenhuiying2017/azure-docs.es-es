---
title: "Creación de una máquina virtual de Azure con Accelerated Networking | Microsoft Docs"
description: "Aprenda a crear una máquina virtual Linux con Accelerated Networking."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 908d81c363a556917d211e0bcc92188f849fb690
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Creación de una máquina virtual Linux con Accelerated Networking

> [!IMPORTANT] 
> Las máquinas virtuales deben crearse con Accelerated Networking habilitado. Esta característica no se puede habilitar en las máquinas virtuales existentes. Puede seguir estos pasos para habilitar Accelerated Networking:
>   1. Elimine la máquina virtual.
>   2. Vuelva a crear una máquina virtual con Accelerated Networking habilitado.
>

En este tutorial, obtendrá información sobre cómo crear una máquina virtual Linux con Accelerated Networking. Accelerated Networking habilita la virtualización de E/S de raíz única (SR-IOV) en una máquina virtual (VM), lo que mejora significativamente su rendimiento en la red. Este método de alto rendimiento omite el host de la ruta de acceso de datos, lo que reduce la latencia, la inestabilidad y la utilización de la CPU para usarse con las cargas de trabajo de red más exigentes en los tipos de máquina virtual admitidos. En la siguiente imagen, se muestra la comunicación entre dos máquinas virtuales (VM) con y sin Accelerated Networking:

![De comparación](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sin Accelerated Networking, todo el tráfico de red de entrada y salida de la máquina virtual tiene que atravesar el host y el conmutador virtual. El conmutador virtual se encarga de toda la aplicación de directivas, como grupos de seguridad de red, listas de control de acceso, aislamiento y otros servicios virtualizados de red, al tráfico de red. Para más información sobre los conmutadores virtuales, lea el artículo sobre [virtualización de red y conmutador virtual de Hyper-V](https://technet.microsoft.com/library/jj945275.aspx).

Con Accelerated Networking, el tráfico de red llega a la interfaz de red (NIC) de la máquina virtual y se reenvía después a la máquina virtual. Todas las directivas de red que el conmutador virtual aplica se descargan y aplican en el hardware. La aplicación de directivas en hardware permite que la NIC reenvíe el tráfico de red directamente a la máquina virtual, pasando por alto el host y el conmutador virtual, al mismo tiempo que se mantienen todas las directivas aplicadas en el host.

Las ventajas de Accelerated Networking solo se aplican a la máquina virtual donde esté habilitado. Para obtener resultados óptimos, lo ideal es habilitar esta característica en al menos dos máquinas virtuales conectadas a la misma instancia de Azure Virtual Network (VNet). Al comunicarse entre redes virtuales o conectarse de forma local, esta característica tiene un efecto mínimo sobre la latencia total.

## <a name="benefits"></a>Ventajas
* **Menor latencia/Más paquetes por segundo (pps):** al quitarse el conmutador virtual de la ruta de acceso de datos, se elimina el tiempo que los paquetes pasan en el host para el procesamiento de las directivas y se aumenta el número de paquetes que se pueden procesar dentro de la máquina virtual.
* **Inestabilidad reducida:** el procesamiento del conmutador virtual depende de la cantidad de directivas que deben aplicarse y la carga de trabajo de la CPU que se encarga del procesamiento. Al descargarse la aplicación de directivas en el hardware, se elimina esa variabilidad, ya que los paquetes se entregan directamente a la máquina virtual y se elimina el host de la comunicación de la máquina virtual, así como todas las interrupciones de software y los cambios de contexto.
* **Disminución de la utilización de la CPU:** el pasar por alto el conmutador virtual en el host conlleva una disminución de la utilización de la CPU para procesar el tráfico de red.

## <a name="supported-operating-systems"></a>Sistemas operativos compatibles
* **Ubuntu 16.04**: 4.11.0-1013 o versión superior de kernel
* **SLES SP3**: 4.4.92-6.18 o versión superior de kernel
* **RHEL**: 7.4.2017120423 o versión superior de kernel
* **CentOS**: 7.4.20171206 o versión superior de kernel

## <a name="supported-vm-instances"></a>Instancias de máquina virtual admitidas
Accelerated Networking se admite con tamaños de instancia optimizados para procesos de cuatro o más vCPU y con los fines más generales. En instancias como DSv3/D o E/ESv3 que admiten hyperthreading, Accelerated Networking se admite en instancias de máquinas virtuales con ocho o más vCPU.  Las series admitidas son: D/DSv2, D/DSv3, ESv3/E, F/Fs/Fsv2 y Ms/Mms. 

Para más información sobre las instancias de máquinas virtuales, consulte [Tamaños de las máquinas virtuales Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>Regiones
Está disponible en todas las regiones de Azure públicas con la excepción de Asia oriental.   La nube de Azure Government todavía no se admite.

## <a name="limitations"></a>Limitaciones
Cuando se utiliza esta funcionalidad, existen las siguientes limitaciones:

* **Creación de interfaz de red:** Accelerated Networking solo se puede habilitar para una nueva interfaz de red. No se puede habilitar para una NIC existente.
* **Creación de máquina virtual:** una NIC con Accelerated Networking habilitado solo se puede asociar a una máquina virtual cuando esta se crea. La NIC no puede asociarse a una máquina virtual existente. Si se agrega la máquina virtual a un conjunto de disponibilidad, todas las máquinas virtuales del conjunto de disponibilidad también deben tener habilitada la red acelerada.
* **Implementación solo mediante Azure Resource Manager:** las máquinas virtuales (clásicas) no se pueden implementar con Accelerated Networking.

## <a name="create-a-virtual-network"></a>Crear una red virtual

Instale la última versión de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) e inicie sesión en una cuenta de Azure con [az login](/cli/azure/#login). En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetro de ejemplo incluyen *myResourceGroup*, *myNic* y *myVM*.

Cree un grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *centralus*:

```azurecli
az group create --name myResourceGroup --location centralus
```

Debe seleccionar una región de Linux admitida enumerada en [Linux accelerated networking](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview) (Accelerated Networking para Linux).

Cree la red virtual con el comando [az network vnet create](/cli/azure/network/vnet#create). En el ejemplo siguiente se crea una red virtual denominada *myVnet* con una subred:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-network-security-group"></a>Crear un grupo de seguridad de red
Cree un grupo de seguridad de red con [az network nsg create](/cli/azure/network/nsg#create). En el ejemplo siguiente se crea un grupo de seguridad de red denominado *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

El grupo de seguridad de red contiene varias reglas predeterminadas, una de las cuales deshabilita todo el acceso entrante desde Internet. Abra un puerto para permitir el acceso SSH a la máquina virtual con [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Creación de una interfaz de red con Accelerated Networking

Cree una dirección IP pública con [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). No se requiere una dirección IP pública si no tiene pensado acceder a la máquina virtual desde Internet, pero, para completar los pasos de este artículo, sí es necesaria.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Cree una interfaz de red con [az network nic create](/cli/azure/network/nic#create) con Accelerated Networking habilitado. En el ejemplo siguiente se crea una interfaz de red denominada *myNic* en la subred *mySubnet* de la red virtual *myVnet* y asocia el grupo de seguridad de red  *myNetworkSecurityGroup* a la interfaz de red:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nic"></a>Creación de una máquina virtual y conexión de NIC
Cuando cree la máquina virtual, especifique las NIC creadas con `--nics`. Debe seleccionar un tamaño y distribución admitidos enumerados en [Linux accelerated networking](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview) (Accelerated Networking para Linux). 

Cree la máquina virtual con [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una máquina virtual denominada *myVM* con la imagen de UbuntuTLS y un tamaño que admite Accelerated Neworking (*Standard_DS4_v2*):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Para obtener una lista de todos los tamaños de máquinas virtuales y las características, consulte [Tamaños de máquina virtual Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Una vez creada la máquina virtual, se devuelve una salida similar a la del siguiente ejemplo. Anote el valor de **publicIpAddress**. Esta dirección se utiliza para acceder a la máquina virtual en los pasos posteriores.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="confirm-that-accelerated-networking-is-enabled"></a>Confirmación de que Accelerated Networking esté habilitado

Use el siguiente comando para crear una sesión de SSH con la máquina virtual. Reemplace `<your-public-ip-address>` con la dirección IP pública asignada a la máquina virtual que ha creado y reemplace *azureuser* si usó un valor diferente para `--admin-username` cuando creó la máquina virtual.

```bash
ssh azureuser@<your-public-ip-address>
```

En el shell de Bash, escriba `uname -r` y confirme que la versión de kernel es una de las siguientes o una posterior:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Con el comando `lspci`, confirme que el dispositivo Mellanox VF se expone a la máquina virtual. La salida devuelta será similar a la siguiente:

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Compruebe la actividad en la función virtual con el comando `ethtool -S eth0 | grep vf_`. Si recibe un resultado similar al siguiente ejemplo de salida, Accelerated Networking red está habilitado y en funcionamiento.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Las redes aceleradas ya están habilitadas para su máquina virtual.
