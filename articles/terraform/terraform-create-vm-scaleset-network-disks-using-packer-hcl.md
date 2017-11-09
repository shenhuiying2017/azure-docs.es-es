---
title: "Uso de Terraform para crear un conjunto de escalado de máquinas virtuales de Azure a partir de una imagen personalizada de Packer"
description: "Use Terraform para configurar y controlar las versiones de un conjunto de escalado de máquinas virtuales de Azure a partir de una imagen personalizada generada por Packer (junto con una red virtual y discos asociados administrados)."
keywords: "terraform, devops, conjunto de escalado, máquina virtual, red, almacenamiento, módulos, imágenes personalizadas, packer"
author: VaijanathB
ms.author: tarcher
ms.date: 10/29/2017
ms.topic: article
ms.openlocfilehash: 284eae93de36986e41ba80f98f86495d8f34f57b
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image"></a>Uso de Terraform para crear un conjunto de escalado de máquinas virtuales de Azure a partir de una imagen personalizada de Packer

En este tutorial, usará [Terraform](https://www.terraform.io/) para crear e implementar un [conjunto de escalado de máquinas virtuales de Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) creado con una imagen personalizada que se generó mediante [Packer](https://www.packer.io/intro/index.html) con discos administrados que usan el [lenguaje de configuración de HashiCorp](https://www.terraform.io/docs/configuration/syntax.html) (HCL).  

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar la implementación de Terraform
> * Usar variables y salidas para la implementación de Terraform 
> * Crear e implementar una infraestructura de red
> * Crear una imagen de máquina virtual personalizada mediante Packer
> * Crear e implementar un conjunto de escalado de máquinas virtuales mediante la imagen personalizada
> * Crear e implementar un Jumpbox 

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="before-you-begin"></a>Antes de empezar
> * [Instale Terraform y configure el acceso a Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure).
> * [Cree un par de claves SSH](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) si aún no tiene uno.
> * [Instale Packer](https://www.packer.io/docs/install/index.html) si aún no lo tiene instalado en su máquina local.


## <a name="create-the-file-structure"></a>Creación de la estructura de archivos

Cree tres nuevos archivos en un directorio vacío con los nombres siguientes:

- ```variables.tf```: este archivo contiene los valores de las variables utilizadas en la plantilla.
- ```output.tf```: este archivo describe la configuración que se muestra después de la implementación.
- ```vmss.tf```: este archivo contiene el código de la infraestructura que va implementar.

##  <a name="create-the-variables"></a>Creación de las variables 

En este paso, se definen las variables que personalizan los recursos creados por Terraform.

Edite el archivo `variables.tf`, copie el código siguiente y, a continuación, guarde los cambios.

```tf 
variable "location" {
  description = "The location where resources are created"
  default     = "East US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources are created"
  default     = ""
}

```

> [!NOTE]
> El valor predeterminado de la variable resource_group_name no está establecido, defina su propio valor.

Guarde el archivo .

Al implementar la plantilla de Terraform, querrá obtener el nombre de dominio completo que se utiliza para acceder a la aplicación. Use el tipo de recurso ```output``` de Terraform y obtenga la propiedad ```fqdn``` del recurso. 

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

También necesitará un grupo de recursos donde se creen todos los recursos. 

Edite y copie el código siguiente en el archivo ```vmss.tf```: 

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
> Conviene etiquetar los recursos que se van a implementar en Azure para facilitar su identificación en el futuro.

## <a name="create-the-network-infrastructure"></a>Creación de la infraestructura de red

Inicialice el entorno de Terraform mediante la ejecución del siguiente comando en el directorio donde creó los archivos `.tf`:

```bash
terraform init 
```
 
Los complementos del proveedor se descargan del registro de Terraform en la carpeta ```.terraform``` situada en el directorio donde se ejecutó el comando.

Ejecute el siguiente comando para implementar la infraestructura de Azure.

```bash
terraform apply
```

Compruebe que el nombre de dominio completo de la dirección IP pública se corresponde con la configuración.

![Nombre de dominio completo de Terraform del conjunto de escalado de máquinas virtuales para la dirección IP pública](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

El grupo de recursos contiene los siguientes recursos:

![Recursos de red de Terraform del conjunto de escalado de máquinas virtuales](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-using-packer"></a>Creación de una imagen de Azure mediante Packer
Cree una imagen de Linux personalizada mediante los pasos que se describen en el tutorial [Uso de Packer para crear imágenes de máquinas virtuales Linux en Azure](https://docs.microsoft.com/azure/virtual-machines/linux/build-image-with-packer).
 
Siga el tutorial para crear una imagen de Ubuntu desaprovisionada con NGINX instalado.

![Una vez que cree la imagen de Packer, tendrá una imagen.](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Para los fines de este tutorial, en la imagen de Packer, se ejecuta un comando para instalar nginx. También puede ejecutar su propio script durante la creación.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Edición de la infraestructura para agregar el conjunto de escalado de máquinas virtuales

En este paso, creará los siguientes recursos en la red que se implementó anteriormente:
- Un equilibrador de carga de Azure para dar servicio a la aplicación y asociarla a la dirección IP pública que se implementó en el paso 4
- Un equilibrador de carga de Azure y reglas para dar servicio a la aplicación y asociarla a la dirección IP pública configurada anteriormente.
- Un grupo de direcciones de back-end de Azure, que se asignará al equilibrador de carga. 
- Un puerto de sondeo de mantenimiento que usa la aplicación y que se configura en el equilibrador de carga. 
- Un conjunto de escalado de máquinas virtuales situado detrás del equilibrador de carga, que se ejecuta en la red virtual implementada anteriormente.
- Se debe tener instalado [Nginx](http://nginx.org/) en los nodos del conjunto de escalado de máquinas virtuales a partir de la imagen personalizada.


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

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = "${data.azurerm_resource_group.image.name}"
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
    id="${data.azurerm_image.image.id}"
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

La salida del comando se parece a la imagen siguiente:

![Agregar plan del conjunto de escalado de máquinas virtuales de Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Implemente los recursos adicionales en Azure: 

```bash
terraform apply 
```

El contenido del grupo de recursos se parece a la imagen siguiente:

![Grupo de recursos del conjunto de escalado de máquinas virtuales de Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Abra un explorador y conéctese al nombre de dominio completo que devolvió el comando. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Adición de un Jumpbox a la red existente 

Este paso opcional permite el acceso de SSH a las instancias del conjunto de escalado de máquinas virtuales mediante un Jumpbox.

Agregue los siguientes recursos a la implementación existente:
- Una interfaz de red conectada a la misma subred que el conjunto de escalado de máquinas virtuales
- Una máquina virtual con esta interfaz de red

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

Edite `outputs.tf` para agregar el código siguiente que muestra el nombre de host del Jumpbox cuando finalice la implementación:

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

Una vez completada la implementación, el contenido del grupo de recursos se parecerá a la imagen siguiente:

![Grupo de recursos del conjunto de escalado de máquinas virtuales de Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> El inicio de sesión con una contraseña está deshabilitado en el Jumpbox y en el conjunto de escalado de máquinas virtuales que ha implementado. Inicie sesión con SSH para tener acceso a las máquinas virtuales.

## <a name="clean-up-the-environment"></a>Limpieza del entorno

Los siguientes comandos eliminan los recursos creados en este tutorial:

```bash
terraform destroy
```

Escriba `yes` cuando se le pida confirmación para eliminar los recursos. El proceso de destrucción puede tardar unos minutos en finalizar.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha implementado un conjunto de escalado de máquinas virtuales y un Jumpbox en Azure mediante Terraform. Ha aprendido a:

> [!div class="checklist"]
> * Inicializar la implementación de Terraform
> * Usar variables y salidas para la implementación de Terraform 
> * Crear e implementar una infraestructura de red
> * Crear una imagen de máquina virtual personalizada mediante Packer
> * Crear e implementar un conjunto de escalado de máquinas virtuales mediante la imagen personalizada
> * Crear e implementar un Jumpbox 