---
title: "Implementación de una aplicación en conjuntos de escalado de máquinas virtuales"
description: "Use extensiones para implementar una aplicación en conjuntos de escalado de máquinas virtuales de Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: adegeo
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: fa7d9d3bef4cb326844ede76171e8c566e87116b
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---

# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Implementación de la aplicación en conjuntos de escalado de máquinas virtuales

En este artículo se describen diferentes formas de instalar software en el momento de aprovisionar el conjunto de escalado.

Es recomendable revisar el artículo sobre [introducción al diseño de conjuntos de escalado](virtual-machine-scale-sets-design-overview.md), en el que se describen algunos de los límites impuestos por los conjuntos de escalado de máquinas virtuales.

## <a name="capture-and-reuse-an-image"></a>Captura y reutilización de una imagen

Puede usar una máquina virtual que tenga en Azure para preparar una imagen base para el conjunto de escalado. Mediante este proceso se crea un disco administrado en la cuenta de almacenamiento, al que puede hacerse referencia como imagen base para el conjunto de escalado. 

Siga estos pasos:

1. Cree una máquina virtual de Azure.
   * [Linux][linux-vm-create]
   * [Windows][windows-vm-create]

2. Acceda de forma remota a la máquina virtual y personalice el sistema a su gusto.

   Si quiere, puede instalar la aplicación ahora. Sin embargo, sepa que si instala la aplicación ahora, puede hacer que resulte más complicada de actualizar, ya que puede tener que eliminarla primero. En su lugar, puede seguir este paso para instalar cualquier requisito previo que la aplicación pudiera tener, como un entorno en tiempo de ejecución específico o una función de sistema operativo.

3. Siga el tutorial de captura de máquinas para [Linux][linux-vm-capture] o [Windows][windows-vm-capture].

4. Cree un [conjunto de escalado de máquinas virtuales][vmss-create] con el URI de la imagen que capturó en el paso anterior.

Para obtener más información sobre los discos, consulte [Información general de Managed Disks](../virtual-machines/windows/managed-disks-overview.md) y [Uso de datos de discos conectados](virtual-machine-scale-sets-attached-disks.md).

## <a name="install-when-the-scale-set-is-provisioned"></a>Instalación cuando el conjunto de escalado está aprovisionado

Es posible aplicar extensiones de máquina virtual al conjunto de escalado de máquinas virtuales. Con una extensión de máquina virtual, puede personalizar las máquinas virtuales de un conjunto de escalado como grupo completo. Para obtener más información sobre extensiones, consulte [Extensiones de máquinas virtuales](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Hay tres extensiones principales que puede usar en función de si el sistema operativo está basado en Linux o Windows.

### <a name="windows"></a>Windows

En los sistemas operativos basados en Windows, use la extensión **Custom Script v1.8**, o bien la extensión **PowerShell DSC**.

#### <a name="custom-script"></a>Script personalizado

La extensión Custom Script se ejecuta como script en cada instancia de máquina virtual del conjunto de escalado. Un archivo de configuración o una variable indican qué archivos se descargan en la máquina virtual y, seguidamente, qué comando se ejecuta. Podría usarlo para ejecutar, por ejemplo, un instalador, un script, un archivo por lotes o cualquier archivo ejecutable.

PowerShell usa una tabla hash para la configuración. En este ejemplo se configura la extensión de script personalizado para ejecutar un script de PowerShell que instale IIS.

```powershell
# Setup extension configuration hashtable variable
$customConfig = @{
  "fileUris" = @("https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1");
  "commandToExecute" = "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> `"%TEMP%\StartupLog.txt`" 2>&1";
};

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Compute -Type CustomScriptExtension -TypeHandlerVersion 1.8 -Name "customscript1" -Setting $customConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "MyVmssTest143"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>Use el conmutador `-ProtectedSetting` para cualquier valor de configuración que pudiera contener información confidencial.

---------


La CLI de Azure usa un archivo json para la configuración. En este ejemplo se configura la extensión de script personalizado para ejecutar un script de PowerShell que instale IIS. Guarde el siguiente archivo json como _settings.json_.

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1"
  ],
  "commandToExecute": "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> \"%TEMP%\StartupLog.txt\" 2>&1"
}
```

A continuación, ejecute este comando de CLI de Azure.

```azurecli
az vmss extension set --publisher Microsoft.Compute --version 1.8 --name CustomScriptExtension --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>Use el conmutador `--protected-settings` para cualquier valor de configuración que pudiera contener información confidencial.

### <a name="powershell-dsc"></a>PowerShell DSC

Puede usar PowerShell DSC para personalizar las instancias de vm del conjunto de escalado. La extensión **DSC** publicada por **Microsoft.Powershell** implementa la configuración de DSC proporcionada y la ejecuta en todas las instancias de máquinas virtuales. Un archivo de configuración o una variable indica a la extensión dónde se encuentra el paquete *.zip* y qué combinación de _script-función_ ejecutar.

PowerShell usa una tabla hash para la configuración. En este ejemplo se implementa un paquete DSC que instala IIS.

```powershell
# Setup extension configuration hashtable variable
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Powershell -Type DSC -TypeHandlerVersion 2.24 -Name "dsc1" -Setting $dscConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "myscaleset1"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>Use el conmutador `-ProtectedSetting` para cualquier valor de configuración que pudiera contener información confidencial.

-----------

La CLI de Azure usa un archivo json para la configuración. En este ejemplo se implementa un paquete DSC que instala IIS. Guarde el siguiente archivo json como _settings.json_.

```json
{
  "wmfVersion": "latest",
  "configuration": {
    "url": "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip",
    "script": "configure-http.ps1",
    "function": "WebsiteTest"
  }
}
```

A continuación, ejecute este comando de CLI de Azure.

```azurecli
az vmss extension set --publisher Microsoft.Powershell --version 2.24 --name DSC --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>Use el conmutador `--protected-settings` para cualquier valor de configuración que pudiera contener información confidencial.

### <a name="linux"></a>Linux

En Linux pueden usarse la extensión **Custom Script v2.0** o **cloud-init** durante la creación.

Custom Script es una extensión sencilla que descarga archivos a las instancias de máquinas virtuales y ejecuta un comando.

#### <a name="custom-script"></a>Script personalizado

Guarde el siguiente archivo json como _settings.json_.

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
  ],
  "commandToExecute": "bash installserver.sh"
}
```

Use la CLI de Azure para agregar esta extensión a un conjunto de escalado de máquinas virtuales existente. Cada máquina virtual del conjunto de escalado ejecuta la extensión de forma automática.

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>Use el conmutador `--protected-settings` para cualquier valor de configuración que pudiera contener información confidencial.

#### <a name="cloud-init"></a>Cloud-Init

Cloud-Init se usa al crear el conjunto de escalado. En primera lugar, cree un archivo local llamado _cloud-init.txt_ y agréguele su configuración. Por ejemplo, consulte [este gist](https://gist.github.com/Thraka/27bd66b1fb79e11904fb62b7de08a8a6#file-cloud-init-txt).

Use la CLI de Azure para crear un conjunto de escalado. El campo `--custom-data` acepta el nombre de archivo de un script de cloud-init.

```azurecli
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

## <a name="how-do-i-manage-application-updates"></a>Instrucciones de administración de actualizaciones de aplicaciones

Si implementó la aplicación por medio de una extensión, altere la definición de la extensión de algún modo. Este cambio hace que la extensión vuelva a implementarse en todas las instancias de máquinas virtuales. **Es necesario** cambiar algún elemento de la extensión, por ejemplo, el nombre de un archivo al que se hace referencia, o Azure no detectará el cambio de la extensión.

Si incorporó la aplicación a su propia imagen de sistema operativo, use una canalización de implementación automatizada para las actualizaciones de aplicaciones. Diseñe la arquitectura para facilitar introducir un conjunto de escalado de ensayo en producción con rapidez. Un buen ejemplo de este enfoque es el [trabajo de controlador Azure Spinnaker](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/).

[Packer](https://www.packer.io/) y [Terraform](https://www.terraform.io/) también son compatibles con Azure Resource Manager, por lo que también puede definir sus imágenes "como código" y compilarlas en Azure, para, a continuación, usar el disco duro virtual en el conjunto de escalado. Sin embargo, hacerlo así sería problemático para las imágenes de marketplace, donde los scripts de extensiones o los personalizados se vuelven más importantes ya que no manipula directamente los bits de marketplace.

## <a name="what-happens-when-a-scale-set-scales-out"></a>¿Qué pasa cuando un conjunto de escalado escala horizontalmente?
Al agregar una o varias máquinas virtuales a un conjunto de escalado, la aplicación se instala automáticamente. Por ejemplo, si el conjunto de escalado tiene extensiones definidas, estas se ejecutan en una máquina virtual nueva cada vez que se crea el conjunto. Si el conjunto de escalado se basa en una imagen personalizada, cualquier máquina virtual nueva es una copia de la imagen personalizada de origen. Si las máquinas virtuales del conjunto de escalado son hosts de contenedor, podría hacer que hubiera código de inicio que cargara los contenedores en una extensión Custom Script. O bien podría haber una extensión que instalara un agente que se registre con un orquestador de clúster, como Azure Container Service.


## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>¿Cómo aplicar una actualización de sistema operativo a través de dominios de actualización?
Suponga que quiere actualizar la imagen de sistema operativo al tiempo que mantiene el conjunto de escalado de máquinas virtuales en ejecución. PowerShell y la CLI de Azure pueden actualizar las imágenes de las máquinas virtuales, una máquina virtual cada vez. En el artículo [Actualización de un conjunto de escalado de máquinas virtuales](./virtual-machine-scale-sets-upgrade-scale-set.md) también se proporciona más información sobre qué opciones hay disponibles para realizar actualizaciones del sistema operativo en un conjunto de escalado de máquinas virtuales.

## <a name="next-steps"></a>Pasos siguientes

* [Uso de PowerShell para administrar un conjunto de escalado](virtual-machine-scale-sets-windows-manage.md).
* [Creación de una plantilla de conjunto de escalado](virtual-machine-scale-sets-mvss-start.md).


[linux-vm-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[windows-vm-create]: ../virtual-machines/windows/tutorial-manage-vm.md
[linux-vm-capture]: ../virtual-machines/linux/capture-image.md
[windows-vm-capture]: ../virtual-machines/windows/capture-image.md 
[vmss-create]: virtual-machine-scale-sets-create.md


