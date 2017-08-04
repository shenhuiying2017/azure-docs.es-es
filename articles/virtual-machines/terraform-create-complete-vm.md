---
title: "Creación de una infraestructura básica de Azure mediante Terraform | Microsoft Docs"
description: "Obtenga información sobre cómo crear recursos de Azure mediante Terraform"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 3787151651767e594dae21f3cfbf79023fc6db40
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="create-basic-infrastructure-in-azure-by-using-terraform"></a>Creación de una infraestructura básica de Azure mediante Terraform
En este artículo se describen los pasos que necesita realizar para aprovisionar una máquina virtual, en conjunto con la infraestructura subyacente, en Azure. Sabrá cómo escribir scripts de Terraform y cómo visualizar los cambios antes de implementarlos en la infraestructura de nube. También aprenderá a crear infraestructura en Azure mediante Terraform.

Para comenzar, cree un archivo llamado \_terraform_azure101.tf_ en el editor de texto de su preferencia (Visual Studio Code, Sublime, Vim, etc.). El nombre exacto del archivo no es importante, porque Terraform acepta el nombre de la carpeta como parámetro: todos los scripts de la carpeta se ejecutan. Pegue el código siguiente en el nuevo archivo:

~~~~
# Configure the Microsoft Azure Provider
# NOTE: if you defined these values as environment variables, you do not have to include this block
provider "azurerm" {
  subscription_id = "your_subscription_id_from_script_execution"
  client_id       = "your_client_id_from_script_execution"
  client_secret   = "your_client_secret_from_script_execution"
  tenant_id       = "your_tenant_id_from_script_execution"
}

# create a resource group 
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}
~~~~
En la sección `provider` del script, indique a Terraform que use un proveedor de Azure para aprovisionar recursos en el script. Para obtener valores para subscription_id, client_id, client_secret y tenant_id, consulte la guía [Instalación y configuración de Terraform](terraform-install-configure.md). Si creó variables de entorno para los valores de este bloque, no necesita incluirlo. 

El recurso `azurerm_resource_group` indica a Terraform que cree un nuevo grupo de recursos. Puede ver más tipos de recursos que están disponibles en Terraform más adelante en este artículo.

## <a name="execute-the-script"></a>Ejecución del script
Una vez que guarde el script, salga a la consola o línea de comandos y escriba lo siguiente:
```
terraform plan terraformscripts
```
Se supone que `terraformscripts` es la carpeta donde se guardó el script. Hemos usado el comando `plan` de Terraform, que examina los recursos definidos en los scripts. Los compara con la información de estado que se guarda mediante Terraform y, luego, genera la ejecución planeada _sin_ crear realmente recursos en Azure. 

Después de ejecutar el comando anterior, debería ver una pantalla similar a la siguiente:

![Plan de Terraform](linux/media/terraform/tf_plan2.png)

Si todo parece correcto, aprovisione este nuevo grupo de recursos en Azure ejecutando lo siguiente: 
```
terraform apply terraformscripts
```
En Azure Portal, debería ver el nuevo grupo de recursos vacío llamado `terraformtest`. En la sección siguiente, se agrega una máquina virtual y toda la infraestructura subyacente para esa máquina virtual al grupo de recursos.

## <a name="provision-an-ubuntu-vm-with-terraform"></a>Aprovisionamiento de una VM Ubuntu con Terraform
Extendamos el script de Terraform que creamos anteriormente con los detalles necesarios para aprovisionar una máquina virtual que ejecute Ubuntu. Estos son los recursos que se aprovisionan en las secciones siguientes:

* Una red con una sola subred
* Una tarjeta adaptadora de red 
* Una cuenta de almacenamiento con un contenedor de almacenamiento
* Una dirección IP pública
* Una máquina virtual que usa todos los recursos anteriores 

Para una documentación detallada de cada uno de los recursos de Terraform para Azure, consulte la [documentación de Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).

También se proporciona la versión completa del [script de aprovisionamiento](#complete-terraform-script) para mayor comodidad.

### <a name="extend-the-terraform-script"></a>Extensión del script de Terraform
Extienda el script que se creó con los recursos siguientes: 
~~~~
# create a virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "acctvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "acctsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}
~~~~
El script anterior crea una red virtual y una subred dentro de esa red virtual. Observe la referencia al grupo de recursos que ya creó mediante "${azurerm_resource_group.helloterraform.name}", tanto en la definición de la red virtual como en la definición de la subred.

~~~~
# create public IP
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "TerraformDemo"
    }
}

# create network interface
resource "azurerm_network_interface" "helloterraformnic" {
    name = "tfni"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    ip_configuration {
        name = "testconfiguration1"
        subnet_id = "${azurerm_subnet.helloterraformsubnet.id}"
        private_ip_address_allocation = "static"
        private_ip_address = "10.0.2.5"
        public_ip_address_id = "${azurerm_public_ip.helloterraformips.id}"
    }
}
~~~~
Los fragmentos de código de script anteriores crean una dirección IP pública y una interfaz de red que usa esa dirección. Observe las referencias a subnet_id y public_ip_address_id. Terraform tiene inteligencia integrada para comprender que la interfaz de red depende de los recursos que se deben crear antes de crear dicha interfaz.

~~~~
# create a random id
resource "random_id" "randomId" {
  byte_length = 4
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "tfstorage${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "westus"
    account_type = "Standard_LRS"

    tags {
        environment = "staging"
    }
}

# create storage container
resource "azurerm_storage_container" "helloterraformstoragestoragecontainer" {
    name = "vhd"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    storage_account_name = "${azurerm_storage_account.helloterraformstorage.name}"
    container_access_type = "private"
    depends_on = ["azurerm_storage_account.helloterraformstorage"]
}
~~~~
Aquí, ha creado una cuenta de almacenamiento y definido un contenedor de almacenamiento dentro de dicha cuenta. Esta cuenta de almacenamiento es donde se almacenan los discos duros virtuales (VHD) para la máquina virtual que va a crear.

~~~~
# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_A0"

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "14.04.2-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        vhd_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}${azurerm_storage_container.helloterraformstoragestoragecontainer.name}/myosdisk.vhd"
        caching = "ReadWrite"
        create_option = "FromImage"
    }

    os_profile {
        computer_name = "hostname"
        admin_username = "testadmin"
        admin_password = "Password1234!"
    }

    os_profile_linux_config {
        disable_password_authentication = false
    }

    tags {
        environment = "staging"
    }
}
~~~~
Por último, en el fragmento de código anterior crea una máquina virtual que usa todos los recursos ya aprovisionados. Son una cuenta de almacenamiento y un contenedor para un VHD, una interfaz de red con dirección IP pública y subred especificadas, y el grupo de recursos que ya creó. Observe la propiedad vm_size, donde el script especifica un SKU de Azure A0.

### <a name="execute-the-script"></a>Ejecución del script
Una vez que guarde todo el script, salga a la consola o línea de comandos y escriba lo siguiente:
```
terraform apply terraformscripts
```
Más tarde, los recursos, incluida una máquina virtual, aparece en el grupo de recursos `terraformtest` en Azure Portal.

## <a name="complete-the-terraform-script"></a>Completar el script de Terraform

```
variable "resourcesname" {
  default = "helloterraform"
}

# Configure the Microsoft Azure Provider
provider "azurerm" {
  subscription_id = "XXX"
  client_id       = "XXX"
  client_secret   = "XXX"
  tenant_id       = "XXX"
}

# create a resource group if it doesn't exist
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}

# create virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "tfvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "tfsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}


# create public IPs
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "TerraformDemo"
    }
}

# create network interface
resource "azurerm_network_interface" "helloterraformnic" {
    name = "tfni"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    ip_configuration {
        name = "testconfiguration1"
        subnet_id = "${azurerm_subnet.helloterraformsubnet.id}"
        private_ip_address_allocation = "static"
        private_ip_address = "10.0.2.5"
        public_ip_address_id = "${azurerm_public_ip.helloterraformips.id}"
    }
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name = "helloterraformstorage"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "westus"
    account_type = "Standard_LRS"

    tags {
        environment = "staging"
    }
}

# create storage container
resource "azurerm_storage_container" "helloterraformstoragestoragecontainer" {
    name = "vhd"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    storage_account_name = "${azurerm_storage_account.helloterraformstorage.name}"
    container_access_type = "private"
    depends_on = ["azurerm_storage_account.helloterraformstorage"]
}

# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_A0"

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "14.04.2-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        vhd_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}${azurerm_storage_container.helloterraformstoragestoragecontainer.name}/myosdisk.vhd"
        caching = "ReadWrite"
        create_option = "FromImage"
    }

    os_profile {
        computer_name = "hostname"
        admin_username = "testadmin"
        admin_password = "Password1234!"
    }

    os_profile_linux_config {
        disable_password_authentication = false
    }

    tags {
        environment = "staging"
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
Ha creado una infraestructura básica en Azure mediante Terraform. Para escenarios más complejos, incluidos ejemplos sobre cómo usar equilibradores de carga y conjuntos de escalado de máquinas virtuales, consulte los diversos [ejemplos de Terraform para Azure](https://github.com/hashicorp/terraform/tree/master/examples). Para una lista actualizada de los proveedores admitidos de Azure, consulte la [documentación de Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).

