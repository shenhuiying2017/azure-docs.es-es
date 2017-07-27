---
title: "Instalación y configuración de Terraform para aprovisionar máquinas virtuales y otras infraestructuras en Azure | Microsoft Docs"
description: "Obtenga información acerca de cómo instalar y configurar Terraform para crear recursos de Azure"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: aa82dda778af927db0a204eb88bae445c086e309
ms.contentlocale: es-es
ms.lasthandoff: 06/20/2017

---

# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Instalación y configuración de Terraform para aprovisionar máquinas virtuales y otras infraestructuras en Azure 
En este artículo se detallan los pasos necesarios para instalar y configurar Terraform para aprovisionar recursos, tales como máquinas virtuales, en Azure. Obtendrá información sobre cómo crear y usar credenciales de Azure para habilitar Terraform para aprovisionar recursos en la nube de forma segura.

HashiCorp Terraform proporciona una manera fácil de definir e implementar infraestructura en la nube con un lenguaje de plantillas personalizadas denominado HCL. Este lenguaje personalizado es [fácil de escribir y fácil de entender](terraform-create-complete-vm.md). Además, mediante el comando "terraform plan", Terraform permite visualizar los cambios a la infraestructura antes de confirmarlos. Siga estos pasos para empezar a usar Terraform con Azure.

## <a name="installing-terraform"></a>Instalación de Terraform
Para instalar Terraform, puede [descargar](https://www.terraform.io/downloads.html) el paquete apropiado para su sistema operativo en un directorio de instalación. La descarga contiene un único archivo ejecutable, para el que también debe definir una ruta de acceso global. Encontrará instrucciones acerca de cómo establecer la ruta de acceso en Linux y Mac en [esta página](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux), mientras que [esta página](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows) contiene instrucciones para configurar la ruta de acceso en Windows. Para comprobar la instalación, ejecute el comando "terraform"; debería ver una lista de opciones disponibles de Terraform como salida.

A continuación, debe permitir el acceso de Terraform a su suscripción de Azure para realizar el aprovisionamiento de infraestructuras.

## <a name="setting-up-terraform-access-to-azure"></a>Configuración del acceso de Terraform a Azure
Para habilitar Terraform para aprovisionar recursos en Azure, debe crear dos entidades en Azure Active Directory (AAD): aplicación de AAD y entidad de servicio de AAD. Seguidamente, los identificadores de estas entidades se usan en los scripts de Terraform. La entidad de servicio es una instancia local de una aplicación de AAD global. Tener una entidad de servicio permite el control de acceso local granular a los recursos globales.

Hay varias maneras de crear la aplicación AAD y la entidad de servicio de AAD. La forma más sencilla y rápida hoy en día es utilizar la CLI de Azure 2.0, que [puede descargar e instalar en Windows, Linux y Mac](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). También puede usar Powershell o la CLI de Azure 1.0 para crear la infraestructura de seguridad necesaria. Las instrucciones siguientes muestran cómo configurar Terraform para Azure con todos estos métodos.

### <a name="windowslinuxmac-users-using-azure-cli-20"></a>Usuarios de Windows, Linux y Mac mediante la CLI de Azure 2.0
Después de descargar e instalar la [CLI de Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli), inicie sesión para administrar su suscripción de Azure con el comando siguiente

```
az login
```

>[!NOTE]
>Si utiliza las nubes de China, Alemania o Government, primero debe configurar la CLI de Azure para trabajar con esa nube. Para hacerlo, ejecute lo siguiente:

```
az cloud set --name AzureChinaCloud|AzureGermanCloud|AzureUSGovernment
```

Si tiene varias suscripciones de Azure, se devuelven sus detalles mediante el comando `az login`. Establezca la variable de entorno `SUBSCRIPTION_ID` en el valor devuelto del campo `id` de la suscripción que desea usar. 

Establezca la suscripción que desea utilizar para esta sesión.

```
az account set --subscription="${SUBSCRIPTION_ID}"
```

Consulte la cuenta para obtener los valores de los identificadores de suscripción e inquilino.

```
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

A continuación, cree credenciales separadas para Terraform.

```
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Esto da como resultado el client_id, client_secret (contraseña), sp_name y tenant (inquilino). Tome nota de los valores de **client_id** y **client_secret**.

Para confirmar las credenciales (entidad de servicio), abra un nuevo shell y ejecute los siguientes comandos, sustituyendo los valores devueltos de **sp_name**, **client_secret** y **tenant**:

```
az login --service-principal -u SP_NAME -p CLIENT_SECRET --tenant TENANT
az vm list-sizes --location westus
```

### <a name="windows-users-using-powershell"></a>Usuarios de Windows con Powershell
Si utiliza un equipo Windows para escribir y ejecutar los scripts de Terraform y prefiere utilizar PowerShell para las tareas de configuración, debe configurar el equipo con las herramientas adecuadas de PowerShell. Para ello, (1) [instale las herramientas de Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps) y (2) descargue y ejecute el [script azure-setup.ps1](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1) desde la consola de Powershell. Para ejecutar el script azure-setup.ps1, descárguelo, ejecute el comando "./azure-setup.ps1 setup" desde la consola de PowerShell e inicie sesión en su suscripción de Azure con privilegios administrativos. A continuación, proporcione un nombre de aplicación (cadena arbitraria, necesaria) cuando se le solicite y (opcionalmente) proporcione una contraseña segura. Si no proporciona una contraseña, la contraseña segura se genera automáticamente con las bibliotecas de seguridad de .Net.

### <a name="linuxmac-users-using-azure-cli-10"></a>Usuarios de Linux y Mac mediante la CLI de Azure 1.0
Para empezar a trabajar con Terraform en máquinas Linux o Mac con la CLI de Azure 1.0, debe asegurarse de que las bibliotecas adecuadas están instaladas en el equipo. Para ello, (1)[instalar las herramientas xPlat de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), (2) [descargar e instalar el procesador JSON jq](https://stedolan.github.io/jq/download/) y (3), descargar y ejecutar el script de bash [azure-setup.sh script](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh) desde la consola. Para ejecutar el script azure-setup.sh, descárguelo, ejecute el comando "./azure-setup setup" desde la consola e inicie sesión en su suscripción de Azure con privilegios administrativos. A continuación, proporcione un nombre de aplicación (cadena arbitraria, necesaria) cuando se le solicite y (opcionalmente) proporcione una contraseña segura cuando se le solicite. Si no proporciona una contraseña, la contraseña segura se genera automáticamente con las bibliotecas de seguridad de .NET.

Todas los scripts anteriores crean una aplicación de AAD y una entidad de servicio, otorgando a la entidad de servicio un nivel de acceso de colaborador o propietario en la suscripción. Debido al nivel alto de acceso concedido, debe proteger siempre la información de seguridad generada por los scripts. Tome nota de los cuatro elementos de información de seguridad proporcionada por los scripts: client_id, client_secret, subscription_id y tenant_id. 

## <a name="setting-environment-variables"></a>Establecimiento de variables de entorno
Con la entidad de servicio de AAD creada y configurada, deberá notificar a Terraform el identificador de inquilino, identificador de suscripción, identificador de cliente y secreto de cliente que se utilizará. Para ello, inserte esos valores en los scripts de Terraform (tal y como se describe en la [próxima sección](terraform-create-complete-vm.md)). Como alternativa, puede establecer las siguientes variables de entorno (y así evitar escribir o compartir las credenciales):

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

A continuación se muestra un script de shell de ejemplo que puede utilizar para establecer esas variables:

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_client_id
export ARM_CLIENT_SECRET=your_client_secret
export ARM_TENANT_ID=your_tenant_id
```

Adicionalmente, si utiliza Terraform con Azure Government, Azure Alemania o Azure China, debe establecer la variable ENVIRONMENT adecuadamente.

## <a name="next-steps"></a>Pasos siguientes
Ya ha instalado Terraform y configurado las credenciales de Azure para iniciar la implementación de infraestructura en su suscripción de Azure. Ahora, obtenga información acerca de cómo [Crear infraestructura con Terraform](terraform-create-complete-vm.md).
