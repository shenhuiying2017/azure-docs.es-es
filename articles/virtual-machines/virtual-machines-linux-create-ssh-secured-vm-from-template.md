<properties
	pageTitle="Creación de una máquina virtual Linux mediante una plantilla de Azure | Microsoft Azure"
	description="Creación de una máquina virtual de Linux en Azure mediante una plantilla de Azure Resource Manager."
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
	ms.date="08/17/2016"
	ms.author="v-livech"/>

# Creación de una máquina virtual de Linux mediante una plantilla de Azure

En este artículo se muestra cómo implementar rápidamente una máquina virtual de Linux en Azure mediante una plantilla de Azure. Para los pasos de este artículo se requiere una cuenta de Azure ([obtenga una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/)) y haber iniciado sesión en la [CLI de Azure](../xplat-cli-install.md) (`azure login`) en modo de Resource Manager (`azure config mode arm`). Puede implementar rápidamente una máquina virtual Linux mediante el [Portal de Azure](virtual-machines-linux-quick-create-portal.md) o la [CLI de Azure](virtual-machines-linux-quick-create-cli.md).

## Resumen rápido de comandos

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## Tutorial detallado

Las plantillas permiten crear máquinas virtuales en Azure con la configuración que desea personalizar durante el inicio, por ejemplo, nombres de usuario y nombres de host. En este artículo, hemos iniciado una plantilla de Azure mediante una máquina virtual de Ubuntu junto con un grupo de seguridad de red (NSG) con el puerto 22 abierto para SSH.

Las plantillas de Azure Resource Manager son archivos JSON que se pueden usar para tareas sencillas y excepcionales, como iniciar una máquina virtual de Ubuntu, como lo hizo en este artículo. Las plantillas de Azure también se pueden utilizar para crear configuraciones de Azure complejas de entornos completos como una pila de implementación de prueba, desarrollo o producción.

## Creación de la máquina virtual de Linux

El ejemplo de código siguiente muestra cómo llamar a `azure group create` para crear un grupo de recursos e implementar una máquina virtual Linux protegida por SSH al mismo tiempo mediante [esta plantilla de Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). En el ejemplo, recuerde que debe usar nombres que sean únicos en su entorno. Este ejemplo usa `quicksecuretemplate` como nombre del grupo de recursos, `securelinux` como nombre de la máquina virtual y `quicksecurelinux` como nombre de subdominio.

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

Salida

```bash
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== vlivech@azure
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Ese ejemplo implementó una máquina virtual mediante el parámetro `--template-uri`. Puede también descargar o crear una plantilla localmente y pasar la plantilla con el parámetro `--template-file`, indicando la ruta de acceso al archivo de plantilla como argumento. La CLI de Azure le pide que especifique los parámetros requeridos por la plantilla.

## Pasos siguientes

Busque en la [galería de plantillas](https://azure.microsoft.com/documentation/templates/) para ver qué entornos de aplicación implementará después.

<!---HONumber=AcomDC_0824_2016-->