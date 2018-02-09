---
title: "Uso de Ansible para administrar sus inventarios dinámicos de Azure"
description: "Más información acerca del uso de Ansible para administrar sus inventarios dinámicos de Azure"
ms.service: ansible
keywords: "ansible, azure, devops, bash, cloudshell, inventario dinámico"
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 8753d039582abdf22f105bf7f139a35c224e7c59
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Uso de Ansible para administrar sus inventarios dinámicos de Azure
Ansible puede utilizarse para extraer información de inventario de varios orígenes (incluidos orígenes en la nube como Azure) en un *inventario dinámico*. En este artículo, usará [Azure Cloud Shell](./ansible-run-playbook-in-cloudshell.md) para configurar un inventario dinámico de Azure con Ansible en el que crear dos máquinas virtuales, etiquetará una de esas máquinas virtuales e instalará Nginx en la máquina virtual etiquetada.

## <a name="prerequisites"></a>requisitos previos

- **Suscripción a Azure**: si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.

- **Credenciales de Azure** - [cree credenciales de Azure y configure Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>Creación de las máquinas virtuales de prueba

1. Inicie sesión en el [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview).

1. Cree un grupo de recursos de Azure para almacenar las máquinas virtuales para este tutorial.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Cree dos máquinas virtuales Linux en Azure mediante una de las técnicas siguientes:

    - **Guión de procedimientos de Ansible**: el artículo [Creación de una máquina virtual básica en Azure con Ansible](/azure/virtual-machines/linux/ansible-create-vm) explica cómo crear una máquina virtual a partir de un guión de procedimientos de Ansible. Si usa un guión de procedimientos para definir una o las dos máquinas virtuales, asegúrese de que se utiliza la conexión SSH en lugar de una contraseña.

    - **CLI de Azure**: Use cada uno de los siguientes comandos en Cloud Shell para crear las dos máquinas virtuales:

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-virtual-machine"></a>Etiquetado de una máquina virtual
Puede [usar etiquetas para organizar los recursos de Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) por categorías definidas por el usuario. 

Escriba el comando [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az_resource_tag) siguiente para etiquetar la máquina virtual `ansible-inventory-test-vm1` con la clave `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/&lt;YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Generación de un inventario dinámico
Una vez definidas y etiquetadas las máquinas virtuales, es el momento de generar el inventario dinámico. Ansible proporciona un script de Python llamado [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) que genera un inventario dinámico de sus recursos de Azure mediante la realización de solicitudes API a Azure Resource Manager. Los pasos siguientes le guían en el uso del script `azure_rm.py` para conectarse a sus dos máquinas virtual de Azure de prueba:

1. Use el comando `wget` de GNU para recuperar el script `azure_rm.py`:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Use el comando `chmod` para cambiar los permisos de acceso al script `azure_rm.py`. El siguiente comando usa el parámetro `+x` para permitir la ejecución del archivo especificado (`azure_rm.py`):

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Use el [comando ansible](https://docs.ansible.com/ansible/2.4/ansible.html) para conectarse al grupo de recursos: 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Una vez conectado, debería ver resultados similares a lo siguiente:

    ```Output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

## <a name="enable-the-virtual-machine-tag"></a>Habilitar la etiqueta de la máquina virtual
Una vez que haya definido la etiqueta que desee, debe "habilitarla". Una manera de habilitar una etiqueta es exportarla a una variable de entorno denominada `AZURE_TAGS` mediante el comando **export**:

```azurecli-interactive
export AZURE_TAGS=nginx
```

Una vez exportada la etiqueta, puede probar el comando `ansible` de nuevo:

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

Ahora verá solo una máquina virtual (aquella cuya etiqueta coincida con el valor exportado a la variable de entorno **AZURE_TAGS**):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Configuración de Nginx en la máquina virtual etiquetada
El objetivo de las etiquetas es permitir que se pueda trabajar de forma rápida y sencilla con subgrupos de las máquinas virtuales. Por ejemplo, supongamos que desea instalar Nginx solo en las máquinas virtuales a las que ha asignado una etiqueta de `nginx`. Los siguientes pasos muestran lo fácil que puede hacerlo:

1. Cree un archivo (para que contenga el guión de procedimientos) denominado `nginx.yml` como se indica a continuación:

  ```azurecli-interactive
  vi nginx.yml
  ```

1. Inserte el código siguiente en el archivo `nginx.yml` que acaba de crear:

    ```yml
    - name: Install and start Nginx on an Azure virtual machine
    hosts: azure
    become: yes
    tasks:
    - name: install nginx
        apt: pkg=nginx state=installed
        notify:
        - start nginx

    handlers:
    - name: start nginx
        service: name=nginx state=started
    ```

1. Ejecute el guión de procedimientos `nginx.yml`:

  ```azurecli-interactive
  ansible-playbook -i azure_rm.py nginx.yml
  ```

1. Tras ejecutar el guión de procedimientos, debería ver resultados similares a lo siguiente:

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] **********

    TASK [Gathering Facts] **********
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] **********
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] **********
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP **********
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Prueba de la instalación de Nginx
En esta sección se muestra una técnica para probar que Nginx está instalado en la máquina virtual.

1. Use el comando [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az_vm_list_ip_addresses) para recuperar la dirección IP de la máquina virtual `ansible-inventory-test-vm1`. El valor devuelto (dirección IP de la máquina virtual) se utiliza entonces como parámetro del comando SSH para conectarse a la máquina virtual.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. El comando [nginx -v](https://nginx.org/en/docs/switches.html) se usa generalmente para imprimir la versión de Nginx. Sin embargo, también se puede utilizar para determinar si Nginx está instalado. Escríbalo mientras está conectado a la máquina virtual `ansible-inventory-test-vm1`.

    ```azurecli-interactive
    nginx -v
    ```

1. Una vez que ejecute el comando `nginx -v`, verá la versión de Nginx (segunda línea) que indica que Nginx está instalado.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. Presione la combinación de teclado **&lt;Ctrl>D** para desconectar la sesión SSH.

1. Al realizar los anteriores pasos para la máquina virtual `ansible-inventory-test-vm2`, aparece un mensaje informativo que indica dónde puede obtener Nginx (lo que implica que no lo tiene instalado en este momento):

    ```Output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>pasos siguientes
> [!div class="nextstepaction"] 
> [Creación de una máquina virtual básica en Azure con Ansible](/azure/virtual-machines/linux/ansible-create-vm)