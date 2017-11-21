---
title: "Uso de módulos de Terraform para crear un clúster de VM en Azure"
description: "Aprenda a usar módulos de Terraform para crear un clúster de máquinas virtuales Windows en Azure"
keywords: "terraform, devops, máquina virtual, redes, módulos"
author: rloutlaw
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 10/19/2017
ms.custom: devops
ms.author: routlaw
ms.openlocfilehash: 23d79fa4a1794a6dea69e6ae24da714babf54e62
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-vm-cluster-with-terraform-using-the-module-registry"></a>Creación de un clúster de VM con Terraform mediante el registro de módulos

En este artículo se describe el proceso para crear un clúster de VM con el [módulo de proceso de Azure](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2) de Terraform. En este tutorial, aprenderá a: 

> [!div class="checklist"]
> * Configurar la autenticación con Azure
> * Crear la plantilla de Terraform
> * Visualizar los cambios con un plan
> * Aplicar la configuración para crear el clúster de VM

Para obtener más información sobre Terraform, consulte la [documentación de Terraform](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Configurar la autenticación con Azure

> [!TIP]
> Si [usa variables de entorno de Terraform](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables) o ejecuta este tutorial en [Azure Cloud Shell](/azure/cloud-shell/overview), omita este paso.

 Revise el artículo sobre cómo [Instalar Terraform y configurar el acceso a Azure](/azure/virtual-machines/linux/terraform-install-configure) para crear una entidad de servicio de Azure. Use esta entidad de servicio para completar un nuevo archivo `azureProviderAndCreds.tf` en un directorio vacío con el código siguiente:

```tf
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>Creación de la plantilla

Cree una plantilla de Terraform nueva denominada `main.tf` con el código siguiente:

```tf
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    remote_port = "3389"
    nb_instances = 2
    vnet_subnet_id = "${module.network.vnet_subnets[0]}"
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = "${module.mycompute.public_ip_dns_name}"
}

output = "vm_public_ip" {
    value = "${module.mycompute.public_ip_address}"
}

output "vm_private_ips" {
    value = "${module.mycompute.network_interface_private_ip}"
}
```

Ejecute `terraform init` en el directorio de configuración. Al usar la versión de Terraform 0.10.6 o posterior, se muestra el siguiente resultado:

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>Visualizar los cambios con un plan

Ejecute `terraform plan` para obtener una vista previa de la infraestructura de máquinas virtuales que creó la plantilla.

![Plan de Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Crear las máquinas virtuales con apply

Ejecute `terraform apply` para aprovisionar las VM en Azure.

![Terraform Apply](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Pasos siguientes

- Examinar la lista de [módulos Terraform de Azure](https://registry.terraform.io/modules/Azure)
- Crear un [conjunto de escalado de máquinas virtuales con Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)