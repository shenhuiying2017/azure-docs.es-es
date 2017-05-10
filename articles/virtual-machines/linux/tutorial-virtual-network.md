---
title: "Azure Virtual Networks y máquinas virtuales de Linux | Microsoft Docs"
description: "Tutorial: Administración de Azure Virtual Networks y máquinas virtuales de Linux con la CLI de Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 1ac628b606a198bb437c02d00467d48462c34334
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017

---

# <a name="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli"></a>Administrar Azure Virtual Networks y máquinas virtuales de Linux con la CLI de Azure

En este tutorial, obtendrá información sobre cómo crear varias máquinas virtuales (VM) en una red virtual y configurar la conectividad de red entre ellas. Cuando se completa una máquina virtual de "front-end", es accesible desde Internet en el puerto 22 para SSH y el puerto 80 para las conexiones HTTP. Una máquina virtual de "back-end" con una base de datos de MySQL está aislada y solo es accesible desde la máquina virtual de front-end en el puerto 3306.

Se pueden completar los pasos de este tutorial con la versión más reciente de la [CLI 2.0 de Azure](/cli/azure/install-azure-cli).

## <a name="create-vm-and-vnet"></a>Crear máquinas virtuales y redes virtuales

Una Azure Virtual Network (VNet) es una representación de su propia red en la nube. Una red virtual es un aislamiento lógico de la nube de Azure dedicada a su suscripción. Dentro de una red virtual, hay subredes, reglas para la conectividad a esas subredes y conexiones desde las máquinas virtuales a las subredes. La CLI de Azure facilita la creación de todos los recursos relacionados con la red necesarios para admitir el acceso a las máquinas virtuales. 

Para poder crear otros recursos de Azure, tiene que crear un grupo de recursos con az group create. En el ejemplo siguiente, se crea un grupo de recursos denominado *myRGNetwork* en la ubicación *westus*:

```azurecli
az group create --name myRGNetwork --location westus
```

Cuando se crea una máquina virtual mediante la CLI de Azure, los recursos de red que necesita se crean automáticamente al mismo tiempo. Cree *myFrontendVM* y sus recursos de red compatibles con [az vm create](https://docs.microsoft.com/cli/azure/vm#create):

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Después de crear la máquina virtual, tome nota de la dirección IP pública. Esta dirección se usará en pasos posteriores de este tutorial:

```bash
{
  "fqdns": "",
  "id": "/subscriptions/{id}/resourceGroups/myRGNetwork/providers/Microsoft.Compute/virtualMachines/myFrontendVM",
  "location": "westus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myRGNetwork"
}
```

Se crearon estos recursos de red:

- **myFrontendVMNSG**: el grupo de seguridad de red que protege el tráfico entrante para *az vm create*.
- **myVMPublicIP**: la dirección IP pública que permite el acceso a Internet para *myFrontendVM*.
- **myVMVMNic**: la interfaz de red virtual que proporciona conectividad de red para *myFrontendVM*.
- **myVMVNET**: la red virtual a la que se conecta *myFrontendVM*.

## <a name="install-web-server"></a>Instalación del servidor web

Cree una conexión SSH con *myFrontendVM*. Reemplace la dirección IP de ejemplo con la dirección IP pública de la máquina virtual:

```bash
ssh 40.68.254.142
```

Ejecute los siguientes comandos para instalar NGINX:

```bash
sudo apt-get -y update && sudo apt-get -y install nginx
```

Cierre la sesión SSH:

```bash
exit
```

## <a name="manage-internet-traffic"></a>Administrar el tráfico de Internet

Un grupo de seguridad de red (NSG) contiene una lista de reglas de seguridad que permiten o deniegan el tráfico de red a recursos conectados a una red virtual. Los NSG se pueden asociar a las subredes o a NIC individuales conectados a máquinas virtuales. La apertura o el cierre del acceso a las máquinas virtuales a través de puertos se realiza mediante las reglas NSG. Al crear *myFrontendVM*, el puerto de entrada 22 se abrió automáticamente para la conectividad SSH.

Abra el 80 en *myFrontendVM* con [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port):

```azurecli
az vm open-port --resource-group myRGNetwork --name myFrontendVM --port 80
```

Ahora puede ir a la dirección IP pública de la máquina virtual para ver el sitio de NGINX.

![Sitio predeterminado de NGINX](./media/quick-create-cli/nginx.png)

## <a name="manage-internal-traffic"></a>Administrar el tráfico interno

La comunicación interna de las máquinas virtuales también puede configurarse con un NSG. En esta sección, aprenderá a crear una subred adicional en la red y asignar un grupo de seguridad de red a la subred para permitir una conexión de *myFrontendVM* a *myBackendVM* en el puerto 3306. Después, la subred se asigna a la máquina virtual cuando se crea.

Agregue un grupo de seguridad de red denominado *myBackendNSG* con [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create). 

```azurecli
az network nsg create \
 --resource-group myRGNetwork \
 --name myBackendNSG
```

Configure un puerto para habilitar *myFrontendVM* y *myBackendVM* para comunicarse entre sí en la red virtual. Agregue una regla de NSG que permita el tráfico a *myBackendSubnet* de *myVMSubnet* con [az network rule create](/cli/azure/network/rule#create):

```azurecli
az network nsg rule create \
 --resource-group myRGNetwork \
 --nsg-name myBackendNSG \
 --name com-rule \
 --access Allow \
 --protocol Tcp \
 --direction Inbound \
 --priority 100 \
 --source-address-prefix 10.0.0.0/24 \
 --source-port-range "*" \
 --destination-address-prefix "*" \
 --destination-port-range 3306
```

## <a name="add-back-end-subnet"></a>Adición de una subred de "back-end"

Una subred es un recurso secundario de una red virtual que le ayudará a definir segmentos de espacios de direcciones dentro de un bloque CIDR mediante prefijos de direcciones IP. Las NIC pueden agregarse a subredes y conectarse a máquinas virtuales, lo cual le proporciona conectividad a varias cargas de trabajo.

Agregue *myBackEndSubnet* a *myFrontendVMVNet* con [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#create):

```azurecli
az network vnet subnet create \
 --address-prefix 10.0.1.0/24 \
 --name myBackendSubnet \
 --resource-group myRGNetwork \
 --vnet-name myFrontendVMVNET \
 --network-security-group myBackendNSG
```

## <a name="create-back-end-vm"></a>Creación de la máquina virtual de "back-end"

Cree *myBackendVM* mediante *myBackendSubnet* con [az vm create](/cli/azure/vm#create):

```azurecli
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --subnet myBackendSubnet \
  --vnet-name myFrontendVMVNET \
  --public-ip-address ""

```

## <a name="install-database"></a>Instalar la base de datos

Para este tutorial, copie la clave privada de la máquina virtual de desarrollo en *myFrontendVM*. En un entorno de producción se recomienda crear claves específicas para su uso en las máquinas virtuales en lugar de usar --generate-ssh-keys al crear las máquinas virtuales. 

La máquina virtual de back-end está pensada para que no sea accesible de forma pública. En esta sección, aprenderá a usar SSH para iniciar sesión en *myFrontendVM* y, después, a usar SSH para iniciar sesión en *myBackendVM* desde la máquina virtual *myFrontendVM*.

Reemplace la dirección IP de ejemplo con la dirección IP pública de la máquina virtual *myFrontendVM*:

```bash
scp ~/.ssh/id_rsa 40.68.254.142:~/.ssh/id_rsa
```

Cree una conexión SSH con *myFrontendVM*. Reemplace la dirección IP de ejemplo con la dirección IP pública de la máquina virtual *myFrontendVM*:

```bash
ssh 40.68.254.142
```

Desde *myFrontendVM*, conéctese a *myBackendVM*:

```bash
ssh myBackendVM
```

Ejecute el siguiente comando para instalar MySQL:

```bash
sudo apt-get -y install mysql-server
```

Siga las instrucciones para la configuración de MySQL.

Cierre las sesiones SSH:

```bash
exit
```

MySQL se instala para mostrar cómo se puede instalar una aplicación en *myBackendVM*, no se usa realmente en este tutorial.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a crear y proteger las redes de Azure cuando están relacionadas con máquinas virtuales. Avance hasta el siguiente tutorial para obtener información acerca de la supervisión de seguridad de la máquina virtual con Azure Security Center.

[Administración de la seguridad de la máquina virtual](./tutorial-azure-security.md)
