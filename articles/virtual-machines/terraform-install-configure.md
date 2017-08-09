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
ms.date: 06/14/2017
ms.author: echuvyrov
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 9718ae5167e9d6cfb044737e12246549440d7808
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Instalación y configuración de Terraform para aprovisionar máquinas virtuales y otras infraestructuras en Azure 
En este artículo se describen los pasos necesarios para instalar y configurar Terraform para aprovisionar recursos, tales como máquinas virtuales, en Azure. Obtendrá información sobre cómo crear y usar credenciales de Azure para habilitar Terraform para aprovisionar recursos en la nube de forma segura.

HashiCorp Terraform proporciona una manera fácil de definir e implementar infraestructura en la nube mediante un lenguaje de plantillas personalizadas denominado lenguaje de configuración de HashiCorp (HCL). Este lenguaje personalizado es [fácil de escribir y fácil de entender](terraform-create-complete-vm.md). Además, mediante el comando `terraform plan`, puede visualizar los cambios en la infraestructura antes de confirmarlos. Siga estos pasos para empezar a usar Terraform con Azure.

## <a name="install-terraform"></a>Instalar Terraform
Para instalar Terraform, [descargue](https://www.terraform.io/downloads.html) el paquete apropiado para su sistema operativo en un directorio de instalación independiente. La descarga contiene un único archivo ejecutable, para el que también debe definir una ruta de acceso global. Para obtener instrucciones sobre cómo establecer la ruta de acceso en Linux y Mac, vaya a [esta página web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Para obtener instrucciones sobre cómo establecer la ruta de acceso en Windows, vaya a [esta página web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows). Para comprobar la instalación, ejecute el comando `terraform`. Debería ver una lista de opciones disponibles de Terraform como salida.

A continuación, debe permitir el acceso de Terraform a su suscripción de Azure para realizar el aprovisionamiento de infraestructuras.

## <a name="set-up-terraform-access-to-azure"></a>Configurar el acceso de Terraform a Azure
Para habilitar Terraform para aprovisionar recursos en Azure, debe crear dos entidades en Azure Active Directory (Azure AD): una aplicación de Azure AD y una entidad de servicio de Azure AD. Seguidamente, los identificadores de estas entidades se usan en los scripts de Terraform. La entidad de servicio es una instancia local de una aplicación de Azure AD global. Una entidad de servicio permite el control de acceso local granular a los recursos globales.

Hay varias maneras de crear una aplicación de Azure AD y una entidad de servicio de Azure AD. La forma más sencilla y rápida hoy en día es usar la CLI de Azure 2.0, que [puede descargar e instalar en Windows, Linux o Mac](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). También puede usar PowerShell o la CLI de Azure 1.0 para crear la infraestructura de seguridad necesaria. Las instrucciones siguientes muestran cómo configurar Terraform para Azure mediante el uso de todos estos métodos.

### <a name="use-azure-cli-20-for-windows-linux-or-mac-users"></a>Usar la CLI de Azure 2.0 (para usuarios de Windows, Linux o Mac) 
Después de descargar e instalar la [CLI de Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli), inicie sesión para administrar su suscripción de Azure con el comando siguiente:

```
az login
```

>[!NOTE]
>Si usa las nubes de China, Alemania o Government, primero debe configurar la CLI de Azure para trabajar con dicha nube. Para ello, ejecute lo siguiente:

```
az cloud set --name AzureChinaCloud|AzureGermanCloud|AzureUSGovernment
```

Si tiene varias suscripciones de Azure, se devuelven sus detalles mediante el comando `az login`. Establezca la variable de entorno `SUBSCRIPTION_ID` para almacenar el valor devuelto del campo `id` de la suscripción que quiere usar. 

Establezca la suscripción que quiere usar para esta sesión.

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

Se devuelven el client_id, client_secret (contraseña), sp_name y tenant (inquilino). Tome nota del client\_id y client\_secret.

Para confirmar las credenciales (la entidad de servicio), abra un nuevo shell y ejecute los comandos siguientes. Sustituya los valores devueltos para sp_name, client\_secret y tenant:

```
az login --service-principal -u SP_NAME -p CLIENT_SECRET --tenant TENANT
az vm list-sizes --location westus
```

### <a name="use-powershell-for-windows-users"></a>Usar PowerShell (para usuarios de Windows) 
Para usar un equipo Windows para escribir y ejecutar los scripts de Terraform, y usar PowerShell para las tareas de configuración, configure el equipo con las herramientas adecuadas de PowerShell. 

1. Instale las herramientas de PowerShell siguiendo los pasos descritos en [Install and configure Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps) (Instalar y configurar Azure PowerShell). 
2. Descargue y ejecute el script [azure-setup.ps1](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1) desde la consola de PowerShell. 
3. Para ejecutar el script azure-setup.ps1, descárguelo y ejecute el comando `./azure-setup.ps1 setup` desde la consola de PowerShell. Después, inicie sesión con su suscripción de Azure con privilegios administrativos. 
4. Proporcione un nombre de aplicación (una cadena arbitraria, obligatoria) cuando se le solicite. Opcionalmente, proporcione una contraseña segura cuando se le solicite. Si no proporciona una contraseña, se genera una contraseña segura automáticamente con las bibliotecas de seguridad de .NET.

### <a name="use-azure-cli-10-for-linux-or-mac-users"></a>Usar la CLI de Azure 1.0 (para usuarios de Linux o Mac)
Para empezar a trabajar con Terraform en equipos Linux o Mac con la CLI de Azure 1.0, instale las bibliotecas adecuadas en el equipo.  

1. Instale las herramientas xPlat de la CLI de Azure siguiendo los pasos descritos en [Instalación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 
2. Descargue e instale un procesador JSON siguiendo las instrucciones de [Download jq](https://stedolan.github.io/jq/download/) (Descargar jq).
3. Descargue y ejecute el script de Bash [azure-setup.sh](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh) desde la consola. 
4. Para ejecutar el script azure-setup.sh, descárguelo y ejecute el comando `./azure-setup setup` desde la consola. Después, inicie sesión con su suscripción de Azure con privilegios administrativos. 
5. Proporcione un nombre de aplicación (una cadena arbitraria, obligatoria) cuando se le solicite. Opcionalmente, proporcione una contraseña segura cuando se le solicite. Si no proporciona una contraseña, se genera una contraseña segura automáticamente con las bibliotecas de seguridad de .NET.

Todos los scripts anteriores crean una aplicación y una entidad de servicio de Azure AD. La entidad de servicio obtiene acceso de nivel de colaborador o propietario en la suscripción. Debido al nivel alto de acceso concedido, debe proteger siempre la información de seguridad generada por los scripts. Tome nota de los cuatro elementos de información de seguridad proporcionados por los scripts: client_id, client_secret, subscription_id y tenant_id.

## <a name="set-environment-variables"></a>Establecimiento de variables de entorno
Después de crear y configurar una entidad de servicio de Azure AD, debe notificar a Terraform el identificador de inquilino, identificador de suscripción, identificador de cliente y secreto de cliente que se van a usar. Para ello, inserte esos valores en los scripts de Terraform, tal y como se describe en [Creación de una infraestructura básica de Azure mediante Terraform](terraform-create-complete-vm.md). Como alternativa, puede establecer las siguientes variables de entorno (y así evitar insertar en el repositorio o compartir las credenciales accidentalmente):

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

Puede usar este script de shell de ejemplo para establecer esas variables:

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_client_id
export ARM_CLIENT_SECRET=your_client_secret
export ARM_TENANT_ID=your_tenant_id
```

Además, si usa Terraform con Azure Government, Azure Alemania o Azure China, debe establecer la variable de entorno adecuadamente.

## <a name="next-steps"></a>Pasos siguientes
Ya ha instalado Terraform y configurado las credenciales de Azure para poder iniciar la implementación de la infraestructura en su suscripción de Azure. Ahora, obtenga información acerca de cómo [Crear infraestructura con Terraform](terraform-create-complete-vm.md).

