<properties
	pageTitle="Creación de una máquina virtual de Linux protegida mediante una plantilla de Azure | Microsoft Azure"
	description="Creación de una máquina virtual de Linux protegida en Azure mediante una plantilla de Azure Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/08/2016"
	ms.author="vlivech"/>

# Creación de una VM de Linux protegida mediante una plantilla de Azure

Las plantillas permiten crear máquinas virtuales en Azure con la configuración que desea personalizar durante el inicio, por ejemplo, nombres de usuario y nombres de host. En este artículo, nos centraremos en crear una máquina virtual de Ubuntu mediante una plantilla de Azure que crea un grupo de seguridad de red (NSG) con un solo puerto abierto (22 para SSH) y que requiere claves SSH para el inicio de sesión.

Las plantillas de Azure Resource Manager son archivos JSON que pueden usarse para tareas sencillas y excepcionales, como la creación de la máquina virtual de Ubuntu de este artículo, o para crear configuraciones de Azure complejas de entornos completos tales como una implementación de prueba, desarrollo o producción, desde la implementación de red, del sistema operativo o la pila de aplicación.

## Objetivo

- Creación de una máquina virtual de Linux protegida por SSH en Azure mediante una plantilla de Azure

## Requisitos previos

- Una cuenta de Azure ([consiga una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/)), un [archivo de clave pública SSH](virtual-machines-linux-mac-create-ssh-keys.md), la [CLI de Azure](../xplat-cli-install.md); después, escriba `azure config mode arm` para poner la CLI en modo de recursos. A continuación, escriba `azure login` para iniciar sesión en Azure con la CLI.

## Resumen rápido de comandos

Esta implementación requiere un solo comando y elegir una opción que indica dónde reside la plantilla de Azure Resource Manager. Como ejemplo, en este tema se usa una plantilla directamente desde el repositorio de Github de plantillas de inicio rápido de Azure. Las diversas opciones siguientes simplemente indican dónde se encuentran la plantilla y los archivos de parámetros.

1. `azure group create -n <exampleRGname> -l <exampleAzureRegion> [--template-uri <URL> | --template-file <path> | <template-file> -e <parameters.json file>]`

## Creación de la máquina virtual de Linux

El ejemplo de código siguiente muestra cómo llamar a `azure group create` para crear un grupo de recursos e implementar una máquina virtual de Linux protegida por SSH al mismo tiempo mediante [esta plantilla de Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). En el ejemplo, recuerde que debe usar nombres que sean únicos en su entorno. Este ejemplo usa `quicksecuretemplate` como nombre del grupo de recursos, `securelinux` como nombre de la máquina virtual y `quicksecurelinux` como nombre de subdominio.

```bash
azure group create -n quicksecuretemplate -l eastus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
adminUserName: ops
sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDDRZ/XB8p8uXMqgI8EoN3dWQw... user@contoso.com
dnsLabelPrefix: quicksecurelinux
vmName: securelinux
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<guid>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## Tutorial detallado

Para crear un nuevo grupo de recursos e implementar una máquina virtual, puede usar el parámetro `--template-uri`, o bien descargar o crear una plantilla localmente y pasar la plantilla con el parámetro `--template-file`, indicando la ruta de acceso al archivo de plantilla como argumento. La CLI de Azure le pide que especifique los parámetros requeridos por la plantilla.

## Pasos siguientes

Después de crear máquinas virtuales de Linux con plantillas, querrá ver qué otros entornos de aplicación hay disponibles para usar con plantillas. Busque en la [galería de plantillas](https://azure.microsoft.com/documentation/templates/) para ver qué entornos de aplicación implementará después.

<!---HONumber=AcomDC_0406_2016-->