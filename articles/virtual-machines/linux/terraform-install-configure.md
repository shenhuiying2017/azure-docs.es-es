---
title: "Instalación y configuración de Terraform para aprovisionar máquinas virtuales y otras infraestructuras en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo instalar y configurar Terraform para crear recursos de Azure"
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
ms.date: 10/23/2017
ms.author: echuvyrov
ms.openlocfilehash: c156776103a466af8923ba7249d96835ff339268
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Instalación y configuración de Terraform para aprovisionar máquinas virtuales y otras infraestructuras en Azure
 
Terraform proporciona una manera fácil de definir, previsualizar e implementar infraestructura en la nube con un [lenguaje de plantillas simple](https://www.terraform.io/docs/configuration/syntax.html). En este artículo se describen los pasos necesarios para usar Terraform para aprovisionar recursos en Azure. 

> [!TIP]
Para obtener más información sobre cómo usar Terraform con Azure, visite el [hub de Terraform](/azure/terraform). Terraform se instala de forma predeterminada en [Cloud Shell](/azure/terraform/terraform-cloud-shell). Mediante el uso de Cloud Shell, puede omitir las partes de instalación y configuración de este documento.

## <a name="install-terraform"></a>Instalar Terraform

Para instalar Terraform, [descargue](https://www.terraform.io/downloads.html) el paquete apropiado para su sistema operativo en un directorio de instalación independiente. La descarga contiene un único archivo ejecutable, para el que también debe definir una ruta de acceso global. Para obtener instrucciones sobre cómo establecer la ruta de acceso en Linux y Mac, vaya a [esta página web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Para obtener instrucciones sobre cómo establecer la ruta de acceso en Windows, vaya a [esta página web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows). 

Compruebe la configuración de ruta de acceso con el comando `terraform`. Debería ver una lista de las opciones disponibles de Terraform como salida:

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Configurar el acceso de Terraform a Azure

Configure [una entidad de servicio de Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli) para habilitar Terraform para aprovisionar recursos en Azure. La entidad de servicio concede sus scripts de Terraform con las credenciales para aprovisionar recursos en su suscripción de Azure.

Hay varias maneras de crear una aplicación de Azure AD y una entidad de servicio de Azure AD. La forma más sencilla y rápida hoy en día es usar la CLI de Azure 2.0, que [puede descargar e instalar en Windows, Linux o Mac](/cli/azure/install-azure-cli).

Inicie sesión para administrar su suscripción de Azure con el comando siguiente:

   `az login`

Si tiene varias suscripciones de Azure, se devuelven sus detalles mediante el comando `az login`. Establezca la variable de entorno `SUBSCRIPTION_ID` para almacenar el valor devuelto del campo `id` de la suscripción que quiere usar. 

Establezca la suscripción que quiere usar para esta sesión.

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Consulte la cuenta para obtener los valores de los identificadores de suscripción e inquilino.

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

A continuación, cree credenciales separadas para Terraform.

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Se devuelven el valor de appId, password, sp_name y tenant. Anote el valor de appId y password.

Para probar sus credenciales, abra un nuevo shell y ejecute el comando siguiente, con los valores devueltos para sp_name, password y tenant:

```azurecli-interactive
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

## <a name="configure-terraform-environment-variables"></a>Configuración de las variables de entorno de Terraform

Configure Terraform para usar el id. de inquilino, el id. de suscripción, el id. de cliente y el secreto de cliente de la entidad de servicio al crear recursos de Azure. Establezca las siguientes variables de entorno, que los [módulos de Terraform de Azure](https://registry.terraform.io/modules/Azure) usarán automáticamente.

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

Puede usar este script de shell de ejemplo para establecer esas variables:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id
```

## <a name="run-a-sample-script"></a>Ejecución de un script de ejemplo

Cree un archivo `test.tf` en un directorio vacío y péguelo en el siguiente script. 

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Guarde el archivo y, a continuación, ejecute `terraform init`. Este comando descarga los módulos de Azure necesarios para crear un grupo de recursos de Azure. Verá la salida siguiente:

```
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Obtenga una vista previa del script con `terraform plan` y, a continuación, cree el grupo de recursos `testResouceGroup` con `terraform apply`:

```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Pasos siguientes

Ha instalado Terraform y configurado las credenciales de Azure para poder iniciar la implementación de la infraestructura en su suscripción de Azure. A continuación, ha probado la instalación mediante la creación de un grupo de recursos de Azure vacío.

> [!div class="nextstepaction"]
> [Crear una máquina virtual de Azure con Terraform](terraform-create-complete-vm.md)

