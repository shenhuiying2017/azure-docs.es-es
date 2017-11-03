---
title: "Uso de Terraform y creación de un conjunto de escalado de máquinas virtuales de Azure mediante HCL."
description: "Use Terraform para configurar y controlar las versiones de un conjunto de escalado de máquinas virtuales de Azure junto con una red virtual y discos asociados administrados."
keywords: "terraform, devops, conjunto de escalado, maquina virtual, red, módulos de almacenamiento"
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/04/2017
ms.topic: article
ms.openlocfilehash: 7a4e21d547b3d2b2399f9f68b1babd9f82a421b7
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="use-terraform-to-plan-and-create-a-networked-azure-vm-scale-set-with-managed-storage"></a>Uso de Terraform para planear y crear un conjunto de escalado de máquinas virtuales de Azure conectado con almacenamiento administrado

En este artículo, usará [Terraform](https://www.terraform.io/) para crear e implementar un [conjunto de escalado de máquinas virtuales de Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) con discos administrados mediante [Hashicorp Configuration Language](https://www.terraform.io/docs/configuration/syntax.html) (HCL).  

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar la implementación de Terraform
> * Usar variables y salidas para la implementación de Terraform 
> * Crear e implementar la infraestructura de red
> * Crear e implementar un conjunto de escalado de máquinas virtuales y asociarlo a la red
> * Crear e implementar un Jumpbox para conectarse a las máquinas virtuales a través de SSH

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="before-you-begin"></a>Antes de empezar

- [Instale Terraform y configure el acceso a Azure](/azure/virtual-machines/linux/terraform-install-configure).
- [Cree un par de claves SSH](/azure/virtual-machines/linux/mac-create-ssh-keys) si aún no tiene uno.

## <a name="create-the-file-structure"></a>Creación de la estructura de archivos

Cree tres nuevos archivos en un directorio vacío con los nombres siguientes:

- `variables.tf`: este archivo contiene los valores de las variables utilizadas en la plantilla.
- `output.tf`: este archivo describe la configuración que se mostrará después de la implementación.
- `vmss.tf`: código de la infraestructura del conjunto de escalado de máquinas virtuales.

## <a name="create-the-variables-and-output-definitions"></a>Creación de las definiciones de variables y salidas

En este paso, se definen las variables que personalizan los recursos creados por Terraform.

Edite el archivo `variables.tf`, copie el código siguiente y, a continuación, guarde los cambios.

```tf 
variable "location" {
  description = "The location where resources will be created"
  default     = "West US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources will be created"
  default     = "myResourceGroup"
}
```

Edite el archivo `output.tf` y copie el código siguiente para exponer el nombre de dominio completo para las máquinas virtuales. 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definición de la infraestructura de red en una plantilla

En este paso, se crea la siguiente infraestructura de red en un nuevo grupo de recursos de Azure: 

  - Una red virtual con el espacio de direcciones de 10.0.0.0/16 
  - Una subred con el espacio de direcciones de 10.0.2.0/24
  - Dos direcciones IP públicas. Una la usa el equilibrador de carga del conjunto de escalado de máquinas virtuales y la otra se usa para conectarse al Jumpbox de SSH.


Edite y copie el código siguiente en el archivo `vmss.tf`: 

```tf
resource "azurerm_resource_group" "vmss" {
  name     = "${var.resource_group_name}"
  location = "${var.location}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = "${azurerm_resource_group.vmss.name}"
  virtual_network_name = "${azurerm_virtual_network.vmss.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Es una buena idea etiquetar los recursos que se van a implementar en Azure para facilitar su identificación en el futuro.

## <a name="create-the-network-infrastructure"></a>Creación de la infraestructura de red

Inicialice el entorno de Terraform mediante la ejecución del siguiente comando en el directorio donde creó los archivos `.tf`:

```bash
terraform init 
```

A continuación, ejecute el siguiente comando para implementar la infraestructura de Azure.

```bash
terraform apply
```

Compruebe que el nombre de dominio completo de la dirección IP pública se corresponde con la configuración: ![Nombre de dominio completo de Terraform de VMSS para la dirección IP pública](./media/tf-create-vmss-step4-fqdn.png)


El grupo de recursos debe tener los siguientes recursos: ![Recursos de red de Terraform de VMSS](./media/tf-create-vmss-step4-rg.png)


## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Edición de la infraestructura para agregar el conjunto de escalado de máquinas virtuales

En este paso, agregará los siguientes recursos a la plantilla:

- Un equilibrador de carga de Azure y reglas para dar servicio a la aplicación y asociarla a la dirección IP pública configurada anteriormente.
- Un grupo de direcciones de back-end de Azure, que se asignará al equilibrador de carga. 
- Un puerto de sondeo de mantenimiento que usa la aplicación y que se configura en el equilibrador de carga. 
- Un conjunto de escalado de máquinas virtuales situado detrás del equilibrador de carga, que se ejecuta en la red virtual implementada anteriormente.
- [Nginx](http://nginx.org/) en los nodos del conjunto de escalado de máquinas virtuales que usan una extensión de script común.

Agregue el siguiente código al final del archivo `vmss.tf`:

```tf
resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.vmss.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "ssh-running-probe"
  port                = "${var.application_port}"
}

resource "azurerm_lb_rule" "lbnatrule" {
    resource_group_name            = "${azurerm_resource_group.vmss.name}"
    loadbalancer_id                = "${azurerm_lb.vmss.id}"
    name                           = "http"
    protocol                       = "Tcp"
    frontend_port                  = "${var.application_port}"
    backend_port                   = "${var.application_port}"
    backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
    frontend_ip_configuration_name = "PublicIPAddress"
    probe_id                       = "${azurerm_lb_probe.vmss.id}"
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = "${azurerm_subnet.vmss.id}"
      load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
    }
  }

  extension { 
    name = "vmssextension"
    publisher = "Microsoft.OSTCExtensions"
    type = "CustomScriptForLinux"
    type_handler_version = "1.2"
    settings = <<SETTINGS
    {
        "commandToExecute": "sudo apt-get -y install nginx"
    }
    SETTINGS
  }

  tags {
    environment = "codelab"
  }
}
```

Personalice la implementación y agregue el código siguiente a `variables.tf`:

```tf 
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Implementación del conjunto de escalado de máquinas virtuales en Azure

Ejecute el siguiente comando para visualizar la implementación del conjunto de escalado de máquinas virtuales:

```bash
terraform plan
```

La salida del comando debe tener el siguiente aspecto:
![Terraform add vmss plan](./media/tf-create-vmss-step6-plan884d3aefd9708a711bc09a66e85eb149c23a3ccff959655ec00418168b2bd481.png)

A continuación, implemente los recursos adicionales en Azure: 

```bash
terraform apply 
```

El contenido del grupo de recursos debe tener este aspecto:

![Terraform vm scaleset resource group](./media/tf-create-vmss-step6-apply.png)

Abra un explorador y conéctese al FQDN que devolvió el comando. 

## <a name="add-an-ssh-jumpbox-to-the-existing-network"></a>Adición de un Jumpbox de SSH a la red existente 

En este paso, puede configurar los siguientes recursos:
- Una interfaz de red conectada a la misma subred que el conjunto de escalado de máquinas virtuales.
- Una máquina virtual conectada con esta interfaz de red. Se puede acceder a este Jumpbox de forma remota. Una vez conectado, puede aplicar SSH a cualquiera de las máquinas virtuales del conjunto de escalado.



Agregue el siguiente código al final del archivo `vmss.tf`:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = "${azurerm_subnet.vmss.id}"
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = "${var.location}"
  resource_group_name   = "${azurerm_resource_group.vmss.name}"
  network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Edite `outputs.tf` y agregue el código siguiente para mostrar el nombre de host del Jumpbox cuando finalice la implementación:

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>Implementación del Jumpbox

Implemente el Jumpbox.

```bash
terraform apply 
```

Una vez completada la implementación, el contenido del grupo de recursos tendrá este aspecto:

![Terraform vm scaleset resource group](./media/tf-create-create-vmss-step8.png)

> [!NOTE]
> El inicio de sesión con una contraseña está deshabilitado en el Jumpbox y en el conjunto de escalado de máquinas virtuales que ha implementado. Inicie sesión con SSH para tener acceso a las máquinas virtuales.

## <a name="clean-up-the-environment"></a>Limpieza del entorno

Los siguientes comandos eliminan los recursos creados en este tutorial:

```bash
terraform destroy
```

Escriba `yes` cuando se le pida confirmación para eliminar los recursos. El proceso de destrucción tarda algunos minutos en completarse.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha implementado un conjunto de escalado de máquinas virtuales en Azure mediante Terraform. Ha aprendido a:

> [!div class="checklist"]
> * Inicializar la implementación de Terraform
> * Usar variables y salidas para la implementación de Terraform 
> * Crear e implementar una infraestructura de red
> * Crear e implementar un conjunto de escalado de máquinas virtuales y asociarlo a un entorno existente
> * Crear e implementar un Jumpbox para conectarse a las máquinas virtuales a través de SSH 
