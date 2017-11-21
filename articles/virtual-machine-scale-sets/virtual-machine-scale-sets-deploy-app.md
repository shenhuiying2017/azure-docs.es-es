---
title: "Implementación de una aplicación en un conjunto de escalado de máquinas virtuales de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo implementar aplicaciones en las instancias de máquinas virtuales Linux y Windows de un conjunto de escalado"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: 7e03d5e2bbdb1b3b206fa7fa455f7dce7951f02b
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Implementación de la aplicación en conjuntos de escalado de máquinas virtuales
Para ejecutar aplicaciones en las instancias de máquinas virtuales (VM) de un conjunto de escalado, primero debe instalar los componentes de la aplicación y los archivos necesarios. En este artículo se presentan distintas formas de crear una imagen de máquina virtual personalizada para las instancias de un conjunto de escalado o de ejecutar automáticamente la instalación de scripts en instancias de máquinas virtuales existentes. También puede obtener información sobre cómo administrar actualizaciones del sistema operativo de la aplicación en un conjunto de escalado.


## <a name="build-a-custom-vm-image"></a>Creación de una imagen de máquina virtual personalizada
Al usar una de las imágenes de la plataforma Azure para crear las instancias en el conjunto de escalado, no se instala ni configura software adicional. Puede automatizar la instalación de estos componentes, aunque esto se sumará al tiempo que tarden en aprovisionarse instancias de máquinas virtuales en los conjuntos de escalado. Si aplica muchos cambios de configuración a las instancias de máquinas virtuales, habrá una sobrecarga de administración con esos scripts y tareas de configuración.

Para reducir la administración de configuración y el tiempo de aprovisionamiento de una máquina virtual, puede crear una imagen de máquina virtual personalizada que esté lista para ejecutar la aplicación tan pronto como se aprovisione una instancia en el conjunto de escalado. El proceso general de creación de una imagen de máquina virtual personalizada para instancias del conjunto de escalado es de la siguiente manera:

1. Para crear una imagen de máquina virtual personalizada para las instancias del conjunto de escalado, puede crear e iniciar sesión en una máquina virtual y, a continuación, instalar y configurar la aplicación. Puede usar Packer para definir y crear una imagen de máquina virtual [Linux](../virtual-machines/linux/build-image-with-packer.md) o [Windows](../virtual-machines/windows/build-image-with-packer.md). O bien, puede crear y configurar la máquina virtual manualmente:

    - Cree una máquina virtual Linux con la [CLI de Azure 2.0](../virtual-machines/linux/quick-create-cli.md), [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md) o el [portal](../virtual-machines/linux/quick-create-portal.md).
    - Cree una máquina virtual Windows con [Azure PowerShell](../virtual-machines/windows/quick-create-powershell.md), la [CLI de Azure 2.0](../virtual-machines/windows/quick-create-cli.md) o el [portal](../virtual-machines/windows/quick-create-portal.md).
    - Inicie sesión en una máquina virtual [Linux](../virtual-machines/linux/mac-create-ssh-keys.md#use-the-ssh-key-pair) o [Windows](../virtual-machines/windows/connect-logon.md).
    - Instale y configure las aplicaciones y herramientas necesarias. Si necesita versiones específicas de una biblioteca o tiempo de ejecución, una imagen de máquina virtual personalizada le permitirá definir una versión y 

2. Capture la máquina virtual con la [CLI de Azure 2.0](../virtual-machines/linux/capture-image.md) o [Azure PowerShell](../virtual-machines/windows/capture-image.md). En este paso se crea la imagen de máquina virtual personalizada que se usa después para implementar instancias en un conjunto de escalado.

3. [Cree un conjunto de escalado](virtual-machine-scale-sets-create.md) y especifique la imagen de máquina virtual personalizada creada en los pasos anteriores.


## <a name="already-provisioned"></a>Instalación de una aplicación con la extensión de script personalizado
La extensión de script personalizado descarga y ejecuta scripts en máquinas virtuales de Azure. Esta extensión es útil para la configuración posterior a la implementación, la instalación de software o cualquier otra tarea de configuración o administración. Los scripts se pueden descargar desde Azure Storage o GitHub, o se pueden proporcionar a Azure Portal en el tiempo de ejecución de la extensión.

La extensión de script personalizado se integra con las plantillas de Azure Resource Manager y también se puede ejecutar mediante la CLI de Azure, PowerShell, Azure Portal o la API de REST de máquina virtual de Azure. 

Para obtener más información, consulte [Información general de la extensión de script personalizado](../virtual-machines/windows/extensions-customscript.md).


### <a name="use-azure-powershell"></a>Uso de Azure PowerShell
PowerShell usa una tabla hash para almacenar el archivo que se va a descargar y el comando que se va a ejecutar. En el ejemplo siguiente:

- Indica a las instancias de máquinas virtuales que descarguen un script desde GitHub: *https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1*
- Establece la extensión para ejecutar un script de instalación: `powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1`
- Obtiene información sobre un conjunto de escalado con [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Aplica la extensión a las instancias de máquinas virtuales con [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

La extensión de script personalizado se aplica a las instancias de máquinas virtuales *myScaleSet* del grupo de recursos denominado *myResourceGroup*. Escriba sus propios nombres, como se indica a continuación:

```powershell
# Define the script for your Custom Script Extension to run
$customConfig = @{
    "fileUris" = (,"https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Si la directiva de actualización del conjunto de escalado es *manual*, actualice las instancias de máquinas virtuales con [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Este cmdlet aplica la configuración del conjunto de escalado actualizado a las instancias de máquinas virtuales e instala la aplicación.


### <a name="use-azure-cli-20"></a>Uso de la CLI de Azure 2.0
Para usar la extensión de script personalizado con la CLI de Azure, puede crear un archivo JSON que defina qué archivos se van a obtener y qué comandos se van a ejecutar. Estas definiciones JSON se pueden volver a usar en implementaciones del conjunto de escalado para aplicar instalaciones de la aplicación coherentes.

En el shell actual, cree un archivo denominado *customConfig.json* y pegue la siguiente configuración. Por ejemplo, cree el archivo en Cloud Shell, no en la máquina local. Puede utilizar el editor que prefiera. Escriba `sensible-editor cloudConfig.json` para crear el archivo y ver una lista de editores disponibles.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

Aplique la configuración de la extensión de script personalizado a las instancias de máquinas virtuales del conjunto de escalado con [az vmss extension set](/cli/azure/vmss/extension#set). En el siguiente ejemplo se aplica la configuración *customConfig.json* a las instancias de máquinas virtuales *myScaleSet* del grupo de recursos denominado *myResourceGroup*. Escriba sus propios nombres, como se indica a continuación:

```azurecli
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfig.json
```

Si la directiva de actualización del conjunto de escalado es *manual*, actualice las instancias de máquinas virtuales con [az vmss update-instances](/cli/azure/vmss#update-instances). Este cmdlet aplica la configuración del conjunto de escalado actualizado a las instancias de máquinas virtuales e instala la aplicación.


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instalación de una aplicación en una máquina virtual Windows con PowerShell DSC
[PowerShell Desired State Configuration (DSC)](https://msdn.microsoft.com/en-us/powershell/dsc/overview) es una plataforma de administración que se usa para definir la configuración de las máquinas de destino. Las configuraciones de DSC definen lo que se debe instalar en una máquina y cómo configurar el host. Un motor de administración de configuración local (LCM) se ejecuta en cada nodo de destino que procesa las acciones requeridas en función de las configuraciones insertadas.

La extensión PowerShell DSC permite personalizar instancias de máquinas virtuales en un conjunto de escalado con PowerShell. En el ejemplo siguiente:

- Indica a las instancias de máquinas virtuales que descarguen un paquete DSC desde GitHub: *https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip*
- Establece la extensión para ejecutar un script de instalación: `configure-http.ps1`
- Obtiene información sobre un conjunto de escalado con [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Aplica la extensión a las instancias de máquinas virtuales con [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

La extensión DSC se aplica a las instancias de máquinas virtuales *myScaleSet* del grupo de recursos denominado *myResourceGroup*. Escriba sus propios nombres, como se indica a continuación:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Si la directiva de actualización del conjunto de escalado es *manual*, actualice las instancias de máquinas virtuales con [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Este cmdlet aplica la configuración del conjunto de escalado actualizado a las instancias de máquinas virtuales e instala la aplicación.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instalación de una aplicación en una máquina virtual Linux con cloud-init
[cloud-init](https://cloudinit.readthedocs.io/latest/) es un enfoque ampliamente usado para personalizar una máquina virtual Linux la primera vez que se arranca. Puede usar cloud-init para instalar paquetes y escribir archivos o para configurar los usuarios y la seguridad. Como cloud-init se ejecuta durante el proceso de arranque inicial, no hay pasos adicionales o agentes requeridos que aplicar a la configuración.

cloud-init también funciona entre distribuciones. Por ejemplo, no use **apt-get install** o **yum install** para instalar un paquete. En su lugar, puede definir una lista de paquetes que se van a instalar. Cloud-init usará automáticamente la herramienta de administración de paquetes nativos para la distribución de Linux (distro) que seleccione.

Para obtener más información, incluido un archivo *cloud-init.txt* de ejemplo, consulte [Uso de cloud-init para personalizar máquinas virtuales de Azure](../virtual-machines/linux/using-cloud-init.md).

Para crear un conjunto de escalado y usar un archivo cloud-init, agregue el parámetro `--custom-data` al comando [az vmss create](/cli/azure/vmss#create) y especifique el nombre de un archivo cloud-int. En el siguiente ejemplo se crea un conjunto de escalado denominado *myScaleSet* en *myResourceGroup* y configuran instancias de máquinas virtuales con un archivo denominado *cloud-init.txt*. Escriba sus propios nombres, como se indica a continuación:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


## <a name="install-applications-as-a-set-scales-out"></a>Instalación de aplicaciones a medida que un conjunto escala horizontalmente
Los conjuntos de escalado permiten aumentar el número de instancias de máquinas virtuales que ejecutan la aplicación. Este proceso de escalado horizontal se puede iniciar manualmente o de forma automática según métricas como el uso de memoria o CPU.

Si ha aplicado una extensión de script personalizado al conjunto de escalado, la aplicación se instala en cada nueva instancia de máquinas virtuales. Si el conjunto de escalado se basa en una imagen personalizada con la aplicación preinstalada, cada nueva instancia de máquinas virtuales se implementa en un estado utilizable. 

Si las instancias de máquinas virtuales del conjunto de escalado son hosts de contenedor, puede usar la extensión de script personalizado para extraer y ejecutar las imágenes de contenedor necesarias. La extensión de script personalizado también podría registrar la nueva instancia de máquinas virtuales con un orquestador, como Azure Container Service.


## <a name="deploy-application-updates"></a>Implementación de actualizaciones de la aplicación
Si actualiza el código de la aplicación, las bibliotecas o los paquetes, puede insertar el último estado de la aplicación en las instancias de máquinas virtuales de un conjunto de escalado. Si usa la extensión de script personalizado, actualizaciones a la aplicación y no hay implementación automática. Cambie la configuración de script personalizada, por ejemplo, para señalar un script de instalación con un nombre de versión actualizado. En un ejemplo anterior, la extensión de script personalizado usa un script denominado *automate_nginx.sh* como se indica a continuación:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

Ninguna actualización que realice a la aplicación se expondrá a la extensión de script personalizado a menos que el script de instalación cambie. Un enfoque consiste en incluir un número de versión que aumenta con los lanzamientos de la aplicación. Ahora, la extensión de script personalizado podría hacer referencia a *automate_nginx_v2.sh* como se indica a continuación:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

La extensión de script personalizado se ejecuta ahora en las instancias de máquinas virtuales para aplicar las últimas actualizaciones de la aplicación.


### <a name="install-applications-with-os-updates"></a>Instalación de aplicaciones con actualizaciones del sistema operativo
Si hay nuevas versiones del sistema operativo disponibles, puede usar o crear una nueva imagen personalizada e [implementar actualizaciones del sistema operativo](virtual-machine-scale-sets-upgrade-scale-set.md) en un conjunto de escalado. Cada instancia de máquinas virtuales se actualiza a la última imagen especificada. Puede usar una imagen personalizada con la aplicación preinstalada, la extensión de script personalizado o PowerShell DSC para que la aplicación esté automáticamente disponible a medida que realice la actualización. Es posible que sea necesaria la planeación del mantenimiento de la aplicación a medida que realice este proceso para garantizar que no haya problemas de compatibilidad de versión.

Si usa una imagen de máquina virtual personalizada con la aplicación preinstalada, podría integrar las actualizaciones de la aplicación con una canalización de implementación para crear las nuevas imágenes e implementar actualizaciones del sistema operativo en el conjunto de escalado. Este enfoque permite a la canalización elegir las últimas compilaciones de aplicación, crear y validar una imagen de máquina virtual y, a continuación, actualizar las instancias de máquinas virtuales del conjunto de escalado. Para ejecutar una canalización de implementación que cree e implemente actualizaciones de la aplicación en imágenes de máquina virtual personalizadas, podría usar [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Spinnaker](https://www.spinnaker.io/) o [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Pasos siguientes
Al crear e implementar aplicaciones en los conjuntos de escalado, puede revisar [Scale Set Design Overview](virtual-machine-scale-sets-design-overview.md) (Información general sobre el diseño del conjunto de escalado). Para obtener más información sobre cómo administrar el conjunto de escalado, consulte [Use PowerShell to manage your scale set](virtual-machine-scale-sets-windows-manage.md) (Uso de PowerShell para administrar el conjunto de escalado).
