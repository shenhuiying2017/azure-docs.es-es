---
title: "Creación de un clúster de máquina virtual con Terraform y HCL"
description: "Utilice Terraform y el lenguaje de configuración de HashiCorp (HCL) para crear un clúster de máquinas virtuales Linux con un equilibrador de carga en Azure"
keywords: "terraform, devops, máquina virtual, redes, módulos"
author: tomarcher
manager: routlaw
ms.service: virtual-machines-linux
ms.custom: devops
ms.topic: article
ms.date: 11/13/2017
ms.author: tarcher
ms.openlocfilehash: 2435d694e6a1671a234d02f90860e5cafe98c2df
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-vm-cluster-with-terraform-and-hcl"></a>Creación de un clúster de máquina virtual con Terraform y HCL

En este tutorial se muestra cómo crear un pequeño clúster de proceso mediante el [lenguaje de configuración de HashiCorp](https://www.terraform.io/docs/configuration/syntax.html) (HCL). La configuración crea un equilibrador de carga, dos VM Linux en un [conjunto de disponibilidad](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) y todos los recursos de red necesarios.

En este tutorial, hizo lo siguiente:

> [!div class="checklist"]
> * Configuración de la autenticación con Azure
> * Creación de un archivo de configuración de Terraform
> * Inicialización de Terraform
> * Creación de un plan de ejecución de Terraform
> * Aplicación de un plan de ejecución de Terraform

## <a name="1-set-up-azure-authentication"></a>1. Configuración de la autenticación con Azure

> [!NOTE]
> Si [usa variables de entorno de Terraform](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables) o ejecuta este tutorial en [Azure Cloud Shell](terraform-cloud-shell.md), omita esta sección.

En esta sección, genera una entidad de servicio de Azure y dos archivos de configuración de Terraform que contienen las credenciales de la entidad de seguridad.

1. [Configure una entidad de servicio de Azure AD](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure) a fin de habilitar Terraform para aprovisionar recursos en Azure. Al crear la entidad de seguridad, tome nota de los valores para el identificador de suscripción, el suscriptor, el identificador de aplicación y la contraseña.

2. Abra el símbolo del sistema.

3. Cree un directorio vacío en el que vaya a almacenar los archivos de Terraform.

4. Cree un nuevo archivo que contenga las declaraciones de variables. Puede dar a este archivo el nombre que desee con una extensión `.tf`.

5. Copie el código siguiente en el archivo de declaraciones de variables:

  ```tf
  variable subscription_id {}
  variable tenant_id {}
  variable client_id {}
  variable client_secret {}
  
  provider "azurerm" {
      subscription_id = "${var.subscription_id}"
      tenant_id = "${var.tenant_id}"
      client_id = "${var.client_id}"
      client_secret = "${var.client_secret}"
  }
  ```

6. Cree un nuevo archivo que contenga los valores de las variables de Terraform. Es común dar a un archivo de variables de Terraform el nombre `terraform.tfvars`, ya que Terraform carga automáticamente cualquier archivo denominado `terraform.tfvars` (o seguir un patrón de `*.auto.tfvars`) si está presente en el directorio actual. 

7. Copie el código siguiente en el archivo de variables. Asegúrese de reemplazar los marcadores de posición del modo siguiente: para `subscription_id`, use el identificador de suscripción de Azure que especificó al ejecutar `az account set`. Para `tenant_id`, utilice el valor `tenant` devuelto desde `az ad sp create-for-rbac`. Para `client_id`, utilice el valor `appId` devuelto desde `az ad sp create-for-rbac`. Para `client_secret`, utilice el valor `password` devuelto desde `az ad sp create-for-rbac`.

  ```tf
  subscription_id = "<azure-subscription-id>"
  tenant_id = "<tenant-returned-from-creating-a-service-principal>"
  client_id = "<appId-returned-from-creating-a-service-principal>"
  client_secret = "<password-returned-from-creating-a-service-principal>"
  ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Creación de un archivo de configuración de Terraform

En esta sección, creará un archivo que contiene las definiciones de recursos para su infraestructura.

1. Cree un nuevo archivo llamado `main.tf`. 

2. Copie las siguientes definiciones de recursos de ejemplo en el archivo `main.tf` recién creado: 

  ```tf
  resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
  }

  resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"
  }

  resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    virtual_network_name = "${azurerm_virtual_network.test.name}"
    address_prefix       = "10.0.2.0/24"
  }

  resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    public_ip_address_allocation = "static"
  }

  resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.test.id}"
    }
  }

  resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = "${azurerm_resource_group.test.name}"
    loadbalancer_id     = "${azurerm_lb.test.id}"
    name                = "BackEndAddressPool"
  }

  resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = "${azurerm_subnet.test.id}"
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = ["${azurerm_lb_backend_address_pool.test.id}"]
    }
  }

  resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = "${azurerm_resource_group.test.location}"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
  }

  resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
  }

  resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = "${azurerm_resource_group.test.location}"
    availability_set_id   = "${azurerm_availability_set.avset.id}"
    resource_group_name   = "${azurerm_resource_group.test.name}"
    network_interface_ids = ["${element(azurerm_network_interface.test.*.id, count.index)}"]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = "${element(azurerm_managed_disk.test.*.name, count.index)}"
      managed_disk_id = "${element(azurerm_managed_disk.test.*.id, count.index)}"
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = "${element(azurerm_managed_disk.test.*.disk_size_gb, count.index)}"
    }

    os_profile {
      computer_name  = "hostname"
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

## <a name="3-initialize-terraform"></a>3. Inicialización de Terraform 

El [comando terraform init](https://www.terraform.io/docs/commands/init.html) se utiliza para inicializar un directorio que contiene los archivos de configuración de Terraform: los archivos creados con las secciones anteriores. Debe ejecutar siempre el comando `terraform init` después de escribir una nueva configuración de Terraform. 

> [!TIP]
> El comando `terraform init` es idempotente, lo que significa que se le puede llamar varias veces y genera siempre el mismo resultado. Por lo tanto, si está trabajando en un entorno de colaboración y cree que los archivos de configuración podrían haber cambiado, es siempre recomendable llamar al comando `terraform init` antes de ejecutar o aplicar un plan.

Para inicializar Terraform, ejecute el siguiente comando:

  ```cmd
  terraform init
  ```

  ![Inicialización de Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Creación de un plan de ejecución de Terraform

El [comando terraform plan](https://www.terraform.io/docs/commands/plan.html) se usa para crear un plan de ejecución. Para generar un plan de ejecución, Terraform agrega todos los archivos `.tf` en el directorio actual. 

Si está trabajando en un entorno de colaboración donde la configuración puede cambiar entre el momento de crear el plan de ejecución y el momento de aplicarlo, debe usar el [parámetro -out del comando terraform plan](https://www.terraform.io/docs/commands/plan.html#out-path) para guardar el plan de ejecución en un archivo. En caso contrario, si está trabajando en un entorno de una sola persona, puede omitir el parámetro `-out`.

Si el nombre del archivo de variables de Terraform no es `terraform.tfvars` y no sigue el patrón `*.auto.tfvars`, debe especificar el nombre del archivo mediante el [parámetro -var-file del comando terraform plan](https://www.terraform.io/docs/commands/plan.html#var-file-foo) al ejecutar el comando `terraform plan`.

Al procesar el comando `terraform plan`, Terraform realiza una actualización y determina qué acciones son necesarias para alcanzar el estado deseado especificado en los archivos de configuración.

Si no tiene que guardar el plan de ejecución, ejecute el siguiente comando:

  ```cmd
  terraform plan
  ```

Si tiene que guardar el plan de ejecución, ejecute el siguiente comando (reemplazando el marcador de posición &lt;path> por la ruta de acceso de salida que desee):

  ```cmd
  terraform plan -out=<path>
  ```

![Creación de un plan de ejecución de Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Aplicación de un plan de ejecución de Terraform

El paso final de este tutorial consiste en usar el [comando terraform apply](https://www.terraform.io/docs/commands/apply.html) para aplicar el conjunto de acciones generadas por el comando `terraform plan`.

Si desea aplicar el plan de ejecución más reciente, ejecute el siguiente comando:

  ```cmd
  terraform apply
  ```

Si desea aplicar un plan de ejecución guardado previamente, ejecute el siguiente comando (reemplazando el marcador de posición &lt;path> por la ruta de acceso que contiene el plan de ejecución guardado):

  ```cmd
  terraform apply <path>
  ```

![Aplicación de un plan de ejecución de Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Pasos siguientes

- Examinar la lista de [módulos Terraform de Azure](https://registry.terraform.io/modules/Azure)
- Crear un [conjunto de escalado de máquinas virtuales con Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)