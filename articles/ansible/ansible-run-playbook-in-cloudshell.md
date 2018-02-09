---
title: "Ejecución de Ansible en Azure Cloud Shell"
description: "Información acerca de cómo realizar diversas tareas de Ansible en Azure Cloud Shell"
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, guion de procedimientos
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: d5a818616d382954d0880bcae58bb13b632ad757
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="run-ansible-in-the-azure-cloud-shell"></a>Ejecución de Ansible en Azure Cloud Shell

En este tutorial, obtendrá información acerca de cómo realizar diversas tareas de Ansible en Azure Cloud Shell. Entre estas tareas, se incluyen la conexión de una máquina virtual y la creación de guiones de procedimientos de Ansible para crear y eliminar un grupo de recursos de Azure.

## <a name="prerequisites"></a>requisitos previos

- **Suscripción a Azure**: si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.

- **Credenciales de Azure** - [: cree credenciales de Azure y configure Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

- **Configuración de Azure Cloud Shell**: si es la primera vez que usa Azure Cloud Shell, el artículo [Guía de inicio rápido para Bash en Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/quickstart) muestra cómo iniciar y configurar Cloud Shell.

## <a name="use-ansible-to-connect-to-a-vm"></a>Uso de Ansible para conectarse a una máquina virtual
Ansible ha creado un script de Python llamado [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) que genera un inventario dinámico de sus recursos de Azure mediante la realización de solicitudes API a Azure Resource Manager. Los pasos siguientes le guían en el uso del script `azure_rm.py` para conectarse a una máquina virtual de Azure:

1. Abra Azure Cloud Shell.

1. Si no dispone de una máquina virtual, escriba los comandos siguientes en Cloud Shell para crear una máquina virtual con la que probar:

  ```azurecli-interactive
  az group create --resource-group ansible-test-rg --location eastus
  ```

  ```azurecli-interactive
  az vm create --resource-group ansible-test-rg --name ansible-test-vm --image UbuntuLTS --generate-ssh-keys
  ```

1. Use el comando `wget` de GNU para recuperar el script `azure_rm.py`:

  ```azurecli-interactive
  wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
  ```

1. Use el comando `chmod` para cambiar los permisos de acceso al script `azure_rm.py`. El siguiente comando usa el parámetro `+x` para permitir la ejecución del archivo especificado (`azure_rm.py`):

  ```azurecli-interactive
  chmod +x azure_rm.py
  ```

1. Use el [comando ansible](https://docs.ansible.com/ansible/2.4/ansible.html) para conectarse a la máquina virtual: 

  ```azurecli-interactive
  ansible -i azure_rm.py ansible-test-vm -m ping
  ```

  Una vez conectado, debería ver resultados similares a lo siguiente:

  ```Output
  The authenticity of host 'nn.nnn.nn.nn (nn.nnn.nn.nn)' can't be established.
  ECDSA key fingerprint is SHA256:&lt;some value>.
  Are you sure you want to continue connecting (yes/no)? yes
  test-ansible-vm | SUCCESS => {
      "changed": false,
      "failed": false,
      "ping": "pong"
  }
  ```

1. Si ha creado un grupo de recursos y la máquina virtual de esta sección

  ```azurecli-interactive
  az group delete -n <resourceGroup>
  ```

## <a name="run-a-playbook-in-cloud-shell"></a>Ejecución de un guión de procedimientos en Cloud Shell
El comando [ansible-playbook](https://docs.ansible.com/ansible/2.4/ansible-playbook.html) ejecuta guiones de procedimientos de Ansible, ejecutando las tareas de los hosts de destino. Esta sección explica cómo usar Cloud Shell para crear y ejecutar dos guiones de procedimientos: uno para crear un grupo de recursos y otro para eliminar el grupo de recursos. 

1. Cree un archivo denominado `rg.yml` de la siguiente manera:

  ```azurecli-interactive
  vi rg.yml
  ```

1. Copie el siguiente contenido en la ventana de Cloud Shell (que ahora hospeda una instancia del editor VI):

  ```yml
  - name: My first Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
          name: demoresourcegroup
          location: eastus
  ```

1. Guarde el archivo y salga del editor VI, para lo que debe escribir `:wq` y presionar &lt;Entrar>.

1. Use el comando `ansible-playbook` para ejecutar el guión de procedimientos `rg.yml`:

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. Debería ver resultados similares a lo siguiente:

  ```Output
  PLAY [My first Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Create a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

1. Compruebe la implementación:

  ```azurecli-interactive
  az group show -n demoresourcegroup
  ```

1. Ahora que ha creado el grupo de recursos, cree un segundo guión de procedimientos de Ansible para eliminar el grupo de recursos:

  ```azurecli-interactive
  vi rg2.yml
  ```

1. Copie el siguiente contenido en la ventana de Cloud Shell (que ahora hospeda una instancia del editor VI):

  ```yml
  - name: My second Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
          name: demoresourcegroup
          state: absent
  ```

1. Guarde el archivo y salga del editor VI, para lo que debe escribir `:wq` y presionar &lt;Entrar>.

1. Use el comando `ansible-playbook` para ejecutar el guión de procedimientos `rg2.yml`:

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. Debería ver resultados similares a lo siguiente:

  ```Output
  The output is as following. 
  PLAY [My second Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Delete a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"] 
> [Creación de una máquina virtual básica en Azure con Ansible](/azure/virtual-machines/linux/ansible-create-vm)