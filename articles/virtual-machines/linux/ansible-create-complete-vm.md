---
title: "Uso de Ansible para crear una máquina virtual compleja de Linux en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo usar Ansible para crear un entorno completo de máquina virtual Linux y administrarla en Azure."
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
ms.openlocfilehash: 88e1f17184be07ec8499ad3049f7210b56fdfc15
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>Creación de un entorno completo de máquina virtual Linux en Azure con Ansible
Ansible permite automatizar la implementación y la configuración de recursos en un entorno. Puede usar Ansible para administrar máquinas virtuales (VM) en Azure al igual que podría hacerlo con cualquier otro recurso. En este artículo se muestra cómo crear un entorno completo de Linux y recursos de apoyo con Ansible. También puede obtener información sobre cómo [crear una VM básica con Ansible](ansible-create-vm.md).


## <a name="prerequisites"></a>requisitos previos
Para administrar recursos de Azure con Ansible, necesita lo siguiente:

- Los módulos de Ansible y SDK de Python de Azure instalados en el sistema de host.
    - Instalar Ansible en [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), [CentOS 7.3](ansible-install-configure.md#centos-73) y [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2).
- Las credenciales de Azure y Ansible configurado para usarlas.
    - [Creación de credenciales de Azure y configuración de Ansible](ansible-install-configure.md#create-azure-credentials).
- CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. 
    - Si necesita actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). También puede usar [Cloud Shell](/azure/cloud-shell/quickstart) desde el explorador.


## <a name="create-virtual-network"></a>Creación de una red virtual
Con la siguiente sección de una guía de Ansible se crea una red virtual llamada *myVnet* en el espacio de direcciones *10.0.0.0/16*:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.10.0.0/16"
```

Para agregar una subred, la siguiente sección crea una subred llamada *mySubnet* en la red virtual *myVnet*:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>Creación de una dirección IP pública
Para acceder a recursos de Internet, cree una dirección IP pública y asígnela a la VM. Con la siguiente sección de una guía de Ansible se crea una dirección IP pública llamada *myPublicIP*:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>Creación de un grupo de seguridad de red
Los grupos de seguridad de red controlan el flujo del tráfico de red entrada y salida de la VM. Con la siguiente sección de una guía de Ansible se crea un grupo de seguridad de red llamado *myNetworkSecurityGroup* y se define una regla para permitir el tráfico de SSH por el puerto TCP 22:

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: TCP
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```


## <a name="create-virtual-network-interface-card"></a>Creación de una tarjeta de interfaz de red virtual
Las tarjetas de interfaz de red (NIC) virtuales conectan la VM a una red virtual determinada, una dirección IP pública y un grupo de seguridad de red. Con la siguiente sección de una guía de Ansible se crea una NIC virtual llamada *myNIC* que se conecta a los recursos de red virtual que se hayan creado:

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```


## <a name="create-virtual-machine"></a>Create virtual machine
El último paso es crear una máquina virtual y usar todos los recursos creados. Con la siguiente sección de una guía de Ansible se crea una VM llamada *myVM* y se adjunta la NIC virtual llamada *myNIC*. Especifique los datos completos de su propia clave pública en el par *key_data* de la siguiente forma:

```yaml
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
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.3'
      version: latest
```

## <a name="complete-ansible-playbook"></a>Guía de Ansible completa
Para unir todas estas secciones, cree una guía de Ansible llamada *azure_create_complete_vm.yml* y pegue el siguiente contenido. Especifique los datos completos de su propia clave pública en el par *key_data*:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
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
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Ansible necesita un grupo de recursos en el que implementar todos los recursos. Cree un grupo de recursos con [az group create](/cli/azure/vm#create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Para crear el entorno de máquina virtual completo con Ansible, ejecute la guía de la siguiente forma:

```bash
ansible-playbook azure_create_complete_vm.yml
```

El resultado es similar al del siguiente ejemplo, en el que se muestra que la VM se ha creado correctamente:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create virtual network] *********************************************
changed: [localhost]

TASK [Add subnet] *********************************************************
changed: [localhost]

TASK [Create public IP address] *******************************************
changed: [localhost]

TASK [Create Network Security Group that allows SSH] **********************
changed: [localhost]

TASK [Create virtual network inteface card] *******************************
changed: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=7    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>pasos siguientes
En este ejemplo se crea un entorno de VM completo, incluidos los recursos de red virtual necesarios. Para ver un ejemplo más directo de creación de una VM en recursos de red existentes con opciones predeterminadas, consulte [Creación de una VM](ansible-create-vm.md).
