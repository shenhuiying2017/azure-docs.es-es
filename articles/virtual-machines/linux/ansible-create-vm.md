---
title: "Uso de Ansible para crear una máquina virtual básica de Linux en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo usar Ansible para crear una máquina virtual básica de Linux y administrarla en Azure."
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
ms.openlocfilehash: 184a30c91de0d4141d6bd8a8b9db93c539e083b5
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="create-a-basic-virtual-machine-in-azure-with-ansible"></a>Creación de una máquina virtual básica en Azure con Ansible
Ansible permite automatizar la implementación y la configuración de recursos en un entorno. Puede usar Ansible para administrar máquinas virtuales (VM) en Azure al igual que podría hacerlo con cualquier otro recurso. En este artículo se muestra cómo crear una VM básica con Ansible. También puede obtener información sobre cómo [crear un entorno de máquina virtual completo con Ansible](ansible-create-complete-vm.md).


## <a name="prerequisites"></a>requisitos previos
Para administrar recursos de Azure con Ansible, necesita lo siguiente:

- Los módulos de Ansible y SDK de Python de Azure instalados en el sistema de host.
    - Instalar Ansible en [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), [CentOS 7.3](ansible-install-configure.md#centos-73) y [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2).
- Las credenciales de Azure y Ansible configurado para usarlas.
    - [Creación de credenciales de Azure y configuración de Ansible](ansible-install-configure.md#create-azure-credentials).
- CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. 
    - Si necesita actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). También puede usar [Cloud Shell](/azure/cloud-shell/quickstart) desde el explorador.


## <a name="create-supporting-azure-resources"></a>Creación de recursos de Azure de apoyo
En este ejemplo, creará un runbook que implementa una máquina virtual en una infraestructura existente. En primer lugar, cree un grupo de recursos con [az group create](/cli/azure/vm#create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Cree una red virtual para la VM con [az network vnet create](/cli/azure/network/vnet#create). En el ejemplo siguiente se crea una red virtual denominada *myVnet* y una subred *mySubnet*:

```azurecli
az network vnet create \
  --resource-group myResourceGroup \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnet \
  --subnet-prefix 10.0.1.0/24
```


## <a name="create-and-run-ansible-playbook"></a>Creación y ejecución de guía de Ansible
Cree una guía de Ansible llamada *azure_create_vm.yml* y pegue el siguiente contenido. En este ejemplo se crea una única VM y se configuran las credenciales de Secure Shell. Especifique los datos completos de su propia clave pública en el par *key_data* de la siguiente forma:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys: 
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Para crear la máquina virtual con Ansible, ejecute la guía de la siguiente forma:

```bash
ansible-playbook azure_create_vm.yml
```

El resultado es similar al del siguiente ejemplo, en el que se muestra que la VM se ha creado correctamente:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```


## <a name="next-steps"></a>pasos siguientes
En este ejemplo se crea una VM en un grupo de recursos existente con una red virtual ya implementada. Para ver un ejemplo más detallado de cómo usar Ansible para crear recursos de apoyo, como una red virtual y reglas del grupo de seguridad de red, consulte [Creación de un entorno de máquina virtual completo con Ansible](ansible-create-complete-vm.md).
