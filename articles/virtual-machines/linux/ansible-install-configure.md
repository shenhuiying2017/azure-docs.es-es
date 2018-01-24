---
title: "Instalación y configuración de Ansible para uso con máquinas virtuales de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo instalar Ansible y configurarlo para administrar recursos de Azure en Ubuntu, CentOS y SLES."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: 13b043f3d6154852647f6bb738d3717be6802fa9
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Instalación y configuración de Ansible para la administración de máquinas virtuales en Azure
En este artículo se indica cómo instalar Ansible y los módulos de SDK de Python de Azure para algunas de las distribuciones más habituales de Linux. Es posible instalar Ansible en otras distribuciones ajustando los paquetes instalados para adaptarlos a la plataforma en concreto. Para crear recursos de Azure de forma segura, también obtendrá información sobre cómo crear credenciales y definirlos para uso en Ansible. 

Para ver más opciones de instalación y pasos para plataformas adicionales, consulte la [guía de instalación de Ansible](https://docs.ansible.com/ansible/intro_installation.html).


## <a name="install-ansible"></a>Instalación de Ansible
En primer lugar, cree un grupo de recursos con [az group create](/cli/azure/group#create). En el siguiente ejemplo se crea un grupo de recursos llamado *myResourceGroupAnsible* en la ubicación *eastus*:

```azurecli
az group create --name myResourceGroupAnsible --location eastus
```

Seguidamente, cree una VM e instale Ansible para una de las siguientes distribuciones:

- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [CentOS 7.3](#centos-73)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS
Cree la máquina virtual con [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una máquina virtual llamada *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Conecte SSH a la VM con el elemento `publicIpAddress` indicado en el resultado de la operación de creación de la VM:

```bash
ssh azureuser@<publicIpAddress>
```

En la VM, instale los paquetes necesarios para los módulos de SDK de Python de Azure y Ansible de la siguiente forma:

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
pip install ansible[azure]
```

Seguidamente, pase a [Creación de credenciales de Azure](#create-azure-credentials).


### <a name="centos-73"></a>CentOS 7.3
Cree la máquina virtual con [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una máquina virtual llamada *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Conecte SSH a la VM con el elemento `publicIpAddress` indicado en el resultado de la operación de creación de la VM:

```bash
ssh azureuser@<publicIpAddress>
```

En la VM, instale los paquetes necesarios para los módulos de SDK de Python de Azure y Ansible de la siguiente forma:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Seguidamente, pase a [Creación de credenciales de Azure](#create-azure-credentials).


### <a name="sles-12-sp2"></a>SLES 12 SP2
Cree la máquina virtual con [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una máquina virtual llamada *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image SLES \
    --admin-username azureuser \
    --generate-ssh-keys
```

Conecte SSH a la VM con el elemento `publicIpAddress` indicado en el resultado de la operación de creación de la VM:

```bash
ssh azureuser@<publicIpAddress>
```

En la VM, instale los paquetes necesarios para los módulos de SDK de Python de Azure y Ansible de la siguiente forma:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Seguidamente, pase a [Creación de credenciales de Azure](#create-azure-credentials).


## <a name="create-azure-credentials"></a>Creación de credenciales de Azure
Ansible se comunica con Azure mediante un nombre de usuario y una contraseña, o a través de una entidad de servicio. Las entidades de servicio de Azure son identidades de seguridad que pueden usarse con aplicaciones, servicios y herramientas de automatización como Ansible. El usuario controla los permisos y los define con respecto a cuáles son las operaciones que la entidad de servicio puede realizar en Azure. Para incrementar la seguridad más allá de un nombre de usuario y una contraseña, en este ejemplo se crea una entidad de servicio básica.

Cree una entidad de servicio en el equipo host con [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) y obtenga las credenciales necesarias para Ansible:

```azurecli
az ad sp create-for-rbac --query [client_id: appId, secret: password, tenant: tenant]
```

A continuación puede ver un ejemplo del resultado de los comandos anteriores:

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Para autenticarse en Azure, también necesita obtener el identificador de la suscripción de Azure con [az account show](/cli/azure/account#show):

```azurecli
az account show --query "{ subscription_id: id }"
```

Usará el resultado de estos dos comandos en el siguiente paso.


## <a name="create-ansible-credentials-file"></a>Creación de un archivo de credenciales de Ansible
Para proporcionar unos credenciales a Ansible, debe definir variables de entorno o crear un archivo de credenciales local. Para obtener más información sobre cómo definir credenciales de Ansible, consulte [Providing Credentials to Azure Modules](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules) (Provisión de credenciales a módulos de Azure). 

Para entornos de desarrollo, cree un archivo *credentials* para Ansible en la VM de host de la siguiente forma:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

El propio archivo *credentials* combina el identificador de la suscripción con el resultado de la creación de una entidad de servicio. El resultado del anterior comando [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) es el mismo que se necesita para los elementos *client_id*, *secret* y *tenant*. En el siguiente ejemplo, estos valores, que coinciden con el anterior resultado, se muestran en el archivo *credentials*. Escriba sus propios valores, como se indica a continuación:

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```


## <a name="use-ansible-environment-variables"></a>Uso de variables de entorno de Ansible
Si va a usar herramientas como Ansible Tower o Jenkins, puede definir variables de entorno de la siguiente forma. Estas variables combinan el identificador de la suscripción con el resultado de la creación de una entidad de servicio. El resultado del anterior comando [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) está en el mismo orden necesario para los elementos *AZURE_CLIENT_ID*, *AZURE_SECRET* y *AZURE_TENANT*. 

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>pasos siguientes
Ya ha instalado Ansible y los módulos de SDK de Python de Azure, y ha definido las credenciales para usarlos con Ansible. Obtenga información sobre cómo [crear una VM con Ansible](ansible-create-vm.md). También puede obtener información sobre cómo [crear una VM completa de Azure y recursos de apoyo con Ansible](ansible-create-complete-vm.md).
