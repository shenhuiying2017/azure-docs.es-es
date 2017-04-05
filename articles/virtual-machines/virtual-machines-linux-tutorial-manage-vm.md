---
title: "Administración de máquinas virtuales Linux con la CLI de Azure | Microsoft Docs"
description: "Tutorial: Administración de máquinas virtuales Linux con la CLI de Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/28/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 14d3907d9ddbe42c9f919ce4d29ba7d58c7c5296
ms.lasthandoff: 03/29/2017

---

# <a name="manage-linux-virtual-machines-with-the-azure-cli"></a>Administración de máquinas virtuales Linux con la CLI de Azure

En este tutorial, creará una máquina virtual y realizara tareas comunes de administración, como agregar un disco, automatizar la instalación de software y crear una instantánea de máquina virtual. 

Para completar este tutorial, asegúrese de que ha instalado la versión más reciente de [CLI de Azure 2.0](/cli/azure/install-azure-cli).

## <a name="step-1--log-in-to-azure"></a>Paso 1: Inicio de sesión en Azure

En primer lugar, abra un terminal e inicie sesión en su suscripción de Azure con el comando [az login](/cli/azure/#login).

```azurecli
az login
```

## <a name="step-2--create-resource-group"></a>Paso 2: Creación de un grupo de recursos

Cree un grupo de recursos con el comando [az group create](https://docs.microsoft.com/cli/azure/group#create). 

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Se debe crear un grupo de recursos antes de una máquina virtual. En este ejemplo, se crea un grupo de recursos denominado `myResourceGroup` en la región `westeurope`. 

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="step-3---prepare-configuration"></a>Paso 3: Preparación de la configuración

Al implementar una máquina virtual, se puede usar **cloud-init** para automatizar las configuraciones, como instalar paquetes, crear archivos y ejecutar scripts. En este tutorial, se automatizan las configuraciones de dos elementos:

- Instalación de un servidor web NGINX
- Aprovisionamiento de un segundo disco en la máquina virtual

Dado que la configuración de **cloud-init** tiene lugar durante la implementación de la máquina virtual, la configuración de **cloud-init** debe definirse antes de crear la máquina virtual.

Cree un nombre de archivo denominado `cloud-init.txt` y copie el contenido siguiente. Esta configuración instala el paquete NGINX y ejecuta los comandos para formar y montar el segundo disco.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
runcmd:
  - (echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
  - sudo mkfs -t ext4 /dev/sdc1
  - sudo mkdir /datadrive
  - sudo mount /dev/sdc1 /datadrive
```

## <a name="step-4---create-virtual-machine"></a>Paso 4: Creación de la máquina virtual

Cree la máquina virtual con el comando [az vm create](https://docs.microsoft.com/cli/azure/vm#create). 

Al crear una máquina virtual, están disponibles varias opciones, como la imagen de sistema operativo, tamaño de disco y credenciales administrativas. En este ejemplo, se crea una máquina virtual con el nombre de `myVM` y que ejecuta Ubuntu. Se crea un disco de 50 GB y se asocia a la máquina virtual mediante el argumento `--data-disk-sizes-gb`. El argumento `--custom-data` toma la configuración de cloud-init y la aplica en fases a la máquina virtual. Por último, también se crean claves SSH, si no existen.

```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --generate-ssh-keys \
  --data-disk-sizes-gb 50 \
  --custom-data cloud-init.txt
```

Una vez creada la máquina virtual, la CLI de Azure da como resultado la siguiente información. Tome nota de la dirección IP pública; esta dirección se utiliza al acceder a la máquina virtual. 

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

Mientras se implementa la máquina virtual, la configuración de **cloud-init** puede tardar unos minutos en completarse. 

## <a name="step-5--configure-firewall"></a>Paso 5: Configuración del firewall

Un [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md) de Azure controla el tráfico entrante y saliente para una o varias máquinas virtuales. Las reglas del grupo de seguridad de red permiten o deniegan el tráfico de red en un puerto específico o en un intervalo de puertos. Estas reglas también pueden incluir un prefijo de dirección de origen para que solo el tráfico que se origine en un origen predefinido pueda comunicarse con una máquina virtual.

En la sección anterior, se instaló el servidor web NGINX. Sin una regla de grupo de seguridad de red para permitir el tráfico entrante en el puerto 80, no se puede acceder al servidor web en Internet. Este paso explica cómo crear la regla de grupo de seguridad de red para permitir conexiones entrantes en el puerto 80.

### <a name="create-nsg-rule"></a>Creación de la regla de grupo de seguridad de red

Para crear una regla de grupo de seguridad de red de entrada, use el comando [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port). En el ejemplo siguiente se abre el puerto `80` para la máquina virtual.

```azurecli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM 
```

Después, vaya a la dirección IP pública de la máquina virtual. Con la regla de grupo de seguridad de red activada, se muestra el sitio web de NGINX predeterminado.

![Sitio NGINX predeterminado](./media/virtual-machines-linux-tutorial-manage-vm/nginx.png)  

## <a name="step-6--snapshot-virtual-machine"></a>Paso 6: Realización de una instantánea de la máquina virtual

Cuando se toma una instantánea de disco, se crea una copia de solo lectura y de un momento dado del disco. En este paso, se toma una instantánea del disco de sistema operativo de las máquinas virtuales. Con una instantánea del disco de sistema operativo, la máquina virtual se puede restaurar rápidamente a un estado específico o la instantánea se puede utilizar para crear una nueva máquina virtual con un estado idéntico.

### <a name="create-snapshot"></a>Creación de una instantánea

Antes de crear una instantánea, se necesita el identificador o el nombre del disco. Use el comando [az vm show](https://docs.microsoft.com/cli/azure/vm#show) para obtener el identificador del disco. En este ejemplo, el identificador del disco se almacena en una variable y se utiliza en un paso posterior.

```azurecli
osdiskid=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Ahora que tiene el identificador del disco, el comando siguiente crea la instantánea.

```azurcli
az snapshot create -g myResourceGroup --source "$osdiskid" --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Creación del disco a partir de la instantánea

Esta instantánea se puede convertir en un disco, que se puede usar para volver a crear la máquina virtual.

```azurecli
az disk create --resource-group myResourceGroup --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Restauración de la máquina virtual a partir de la instantánea

Para demostrar la recuperación de la máquina virtual, elimine la máquina virtual existente. 

```azurecli
az vm delete --resource-group myResourceGroup --name myVM
```

Al volver a crear la máquina virtual, se volverá a usar la interfaz de red existente. Esto garantiza que se conservan las configuraciones de seguridad de red.

Obtener el nombre de interfaz de red mediante el comando [az network nic list](https://docs.microsoft.com/cli/azure/network/nic#list). En este ejemplo se coloca el nombre en una variable denominada `nic`, que se usa en el paso siguiente.

```azurecli
nic=$(az network nic list --resource-group myResourceGroup --query "[].[name]" -o tsv)
```

Cree una nueva máquina virtual en el disco de instantáneas.

```azurecli
az vm create --resource-group myResourceGroup --name myVM --attach-os-disk mySnapshotDisk --os-type linux --nics $nic
```

Tome nota de la nueva dirección IP pública y explore esta dirección con un explorador de Internet. Verá que NGINX se está ejecutando en la máquina virtual restaurada. 

### <a name="reconfigure-data-disk"></a>Reconfiguración del disco de datos

Ahora puede volver a adjuntar el disco de datos a la máquina virtual. 

En primer lugar, busque el nombre de los discos de datos mediante el comando [az disk list](https://docs.microsoft.com/cli/azure/disk#list). En este ejemplo se coloca el nombre del disco en una variable denominada `datadisk`, que se usa en el paso siguiente.

```azurecli
datadisk=$(az disk list -g myResourceGroup --query "[?contains(name,'myVM')].[name]" -o tsv)
```

Use el comando [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#attach) para adjuntar el disco.

```azurecli
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk $datadisk
```

El disco también debe montarse en el sistema operativo. Para montar el disco, conéctese a la máquina virtual y ejecute `sudo mount /dev/sdc1 /datadrive`, o la operación de montaje de disco preferida. 

## <a name="step-7--management-tasks"></a>Paso 7: Tareas de administración

Durante el ciclo de vida de una máquina virtual, puede ejecutar tareas de administración como iniciar, detener o eliminar una máquina virtual. Además, puede crear scripts para automatizar tareas repetitivas o complejas. Mediante la CLI de Azure, muchas tareas comunes de administración se pueden ejecutar desde la línea de comandos o en scripts. 

### <a name="get-ip-address"></a>Obtención de dirección IP

Este comando devuelve las direcciones IP públicas y privadas de una máquina virtual.  

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myVM
```

### <a name="resize-virtual-machine"></a>Cambiar el tamaño de la máquina virtual

Para cambiar el tamaño de una máquina virtual de Azure, debe conocer el nombre de los tamaños disponibles en la región de Azure elegida. Estos tamaños pueden encontrarse con el comando [az vm list-sizes](https://docs.microsoft.com/cli/azure/vm#list-sizes).

```azurecli
az vm list-sizes --location westeurope --output table
```

El tamaño de la máquina virtual puede cambiarse mediante el comando [az vm resize](https://docs.microsoft.com/cli/azure/vm#resize). 

```azurecli
az vm resize -g myResourceGroup -n myVM --size Standard_F4s
```

### <a name="stop-virtual-machine"></a>Detención de la máquina virtual

```azurecli
az vm stop --resource-group myResourceGroup --name myVM
```

### <a name="start-virtual-machine"></a>Inicio de la máquina virtual

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

### <a name="delete-resource-group"></a>Eliminación de un grupo de recursos

Al eliminar un grupo de recursos se eliminan también todos los recursos contenidos en el mismo.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

Tutorial: [Creación de máquinas virtuales con equilibrio de carga](./virtual-machines-linux-tutorial-load-balance-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Ejemplos: [Scripts de ejemplo de CLI de Azure](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
