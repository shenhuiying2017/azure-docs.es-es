---
title: "Implementación de una aplicación en un conjunto de escalado de máquinas virtuales de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo implementar una aplicación de escalado automático simple en un conjunto de escalado de máquinas virtuales mediante una plantilla de Azure Resource Manager."
services: virtual-machine-scale-sets
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/24/2017
ms.author: ryanwi
ms.openlocfilehash: 07883a33382cc660b043c99872312a9e77228253
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="deploy-an-autoscaling-app-using-a-template"></a>Implementación de una aplicación de escalado automático con una plantilla

Las [plantillas de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) constituyen una excelente manera de implementar grupos de recursos relacionados. Este tutorial se basa en [Implementación de un conjunto de escalado sencillo](virtual-machine-scale-sets-mvss-start.md) y describe cómo implementar una aplicación de escalado automático simple en un conjunto de escalado con una plantilla de Azure Resource Manager.  También puede configurar el escalado automático con PowerShell, la CLI o el portal. Para obtener más información, consulte la [información general sobre el escalado automático](virtual-machine-scale-sets-autoscale-overview.md).

## <a name="two-quickstart-templates"></a>Dos plantillas de inicio rápido
Cuando implementa un conjunto de escalado, puede instalar software nuevo en una imagen de plataforma con una [extensión de VM](../virtual-machines/virtual-machines-windows-extensions-features.md). Una extensión de VM es una aplicación pequeña que realizan tareas de automatización y configuración posteriores a la implementación en máquinas virtuales de Azure, como es la implementación de una aplicación. En [Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) se ofrecen dos plantillas de ejemplo distintas, las que muestran cómo implementar una aplicación de escalado automático en un conjunto de escalado con extensiones de VM.

### <a name="python-http-server-on-linux"></a>Servidor HTTP de Python en Linux
La plantilla [Servidor HTTP de Python en Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) implementa una aplicación de escalado automático sencilla que se ejecuta en un conjunto de escalado de Linux.  [Bottle](http://bottlepy.org/docs/dev/), un marco web de Python y un servidor HTTP sencillo se implementan en cada VM del conjunto de escalado con una extensión de VM de script personalizado. Este conjunto de escalado escala verticalmente cuando el uso de CPU promedio de todas las VM supera el 60% y se reduce verticalmente cuando es menor del 30%.

Además del recurso de conjunto de escalado, la plantilla de ejemplo *azuredeploy.json* también declara la red virtual, la dirección IP pública, el equilibrador de carga y los recursos de configuración de escalado automático.  Para más información sobre cómo crear estos recursos en una plantilla, consulte [Conjunto de escalado de Linux con escalado automático](virtual-machine-scale-sets-linux-autoscale.md).

En la plantilla *azuredeploy.json*, la propiedad `extensionProfile` del recurso `Microsoft.Compute/virtualMachineScaleSets` especifica una extensión de script personalizado. `fileUris` especifica la ubicación de los scripts. En este caso, dos archivos: *workserver.py*, que define un servidor HTTP sencillo, e *installserver.sh*, que instala Bottle e inicia el servidor HTTP. `commandToExecute` especifica el comando que se debe ejecutar una vez que se implementa el conjunto de escalado.

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "lapextension",
                "properties": {
                  "publisher": "Microsoft.Azure.Extensions",
                  "type": "CustomScript",
                  "typeHandlerVersion": "2.0",
                  "autoUpgradeMinorVersion": true,
                  "settings": {
                    "fileUris": [
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
                    ],
                    "commandToExecute": "bash installserver.sh"
                  }
                }
              }
            ]
          }
```

### <a name="aspnet-mvc-application-on-windows"></a>Aplicación ASP.NET MVC en Windows
La plantilla de ejemplo [Aplicación ASP.NET MVC en Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) implementa una aplicación ASP.NET MVC que se ejecuta en IIS en el conjunto de escalado de Windows.  IIS y la aplicación MVC se implementan con la extensión de VM [Configuración de estado deseado (DSC) de PowerShell](virtual-machine-scale-sets-dsc.md).  El conjunto de escalado escala verticalmente (una instancia de VM a la vez) cuando el uso de la CPU supera el 50% durante 5 minutos. 

Además del recurso de conjunto de escalado, la plantilla de ejemplo *azuredeploy.json* también declara la red virtual, la dirección IP pública, el equilibrador de carga y los recursos de configuración de escalado automático. Esta plantilla también demuestra la actualización de la aplicación.  Para más información sobre cómo crear estos recursos en una plantilla, consulte [Conjunto de escalado de Windows con escalado automático](virtual-machine-scale-sets-windows-autoscale.md).

En la plantilla *azuredeploy.json*, la propiedad `extensionProfile` del recurso `Microsoft.Compute/virtualMachineScaleSets` especifica una extensión de [configuración de estado deseado (DSC)](virtual-machine-scale-sets-dsc.md) que instala IIS y una aplicación web predeterminada desde el paquete WebDeploy.  El script *IISInstall.ps1* instala IIS en la máquina virtual y se encuentra en la carpeta *DSC*.  La aplicación web MVC está en la carpeta *WebDeploy*.  Las rutas de acceso al script de instalación y la aplicación web se definen en los parámetros `powershelldscZip` y `webDeployPackage` del archivo *azuredeploy.parameters.json*. 

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "Microsoft.Powershell.DSC",
                "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.9",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('powershelldscUpdateTagVersion')]",
                  "settings": {
                    "configuration": {
                      "url": "[variables('powershelldscZipFullPath')]",
                      "script": "IISInstall.ps1",
                      "function": "InstallIIS"
                    },
                    "configurationArguments": {
                      "nodeName": "localhost",
                      "WebDeployPackagePath": "[variables('webDeployPackageFullPath')]"
                    }
                  }
                }
              }
            ]
          }
```

## <a name="deploy-the-template"></a>Implementación de la plantilla
La manera más sencilla de implementar la plantilla [Servidor HTTP de Python en Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) o [Aplicación ASP.NET MVC en Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) es usar el botón **Implementar en Azure** que se encuentra en los archivos Léame en GitHub.  También puede usar PowerShell o la CLI de Azure para implementar las plantillas de ejemplo.

### <a name="powershell"></a>PowerShell
Copie los archivos [Servidor HTTP de Python en Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) o [Aplicación ASP.NET MVC en Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) desde el repositorio GitHub en una carpeta del equipo local.  Abra el archivo *azuredeploy.parameters.json* y actualice los valores predeterminados de los parámetros `vmssName`, `adminUsername` y `adminPassword`. Guarde el siguiente script de PowerShell en el archivo *deploy.ps1*, en la misma carpeta que la plantilla *azuredeploy.json*. Para implementar la plantilla de ejemplo, ejecute el script *deploy.ps1* desde una ventana de comandos de PowerShell.

```powershell
param(
 [Parameter(Mandatory=$True)]
 [string]
 $subscriptionId,

 [Parameter(Mandatory=$True)]
 [string]
 $resourceGroupName,

 [string]
 $resourceGroupLocation,

 [Parameter(Mandatory=$True)]
 [string]
 $deploymentName,

 [string]
 $templateFilePath = "template.json",

 [string]
 $parametersFilePath = "parameters.json"
)

<#
.SYNOPSIS
    Registers RPs
#>
Function RegisterRP {
    Param(
        [string]$ResourceProviderNamespace
    )

    Write-Host "Registering resource provider '$ResourceProviderNamespace'";
    Register-AzureRmResourceProvider -ProviderNamespace $ResourceProviderNamespace;
}

#******************************************************************************
# Script body
# Execution begins here
#******************************************************************************
$ErrorActionPreference = "Stop"

# sign in
Write-Host "Logging in...";
Login-AzureRmAccount;

# select subscription
Write-Host "Selecting subscription '$subscriptionId'";
Select-AzureRmSubscription -SubscriptionID $subscriptionId;

# Register RPs
$resourceProviders = @("microsoft.compute","microsoft.insights","microsoft.network");
if($resourceProviders.length) {
    Write-Host "Registering resource providers"
    foreach($resourceProvider in $resourceProviders) {
        RegisterRP($resourceProvider);
    }
}

#Create or check for existing resource group
$resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
if(!$resourceGroup)
{
    Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
    if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
    }
    Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else{
    Write-Host "Using existing resource group '$resourceGroupName'";
}

# Start the deployment
Write-Host "Starting deployment...";
if(Test-Path $parametersFilePath) {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
} else {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
}
```

### <a name="azure-cli"></a>CLI de Azure
```azurecli
#!/bin/bash
set -euo pipefail
IFS=$'\n\t'

# -e: immediately exit if any command has a non-zero exit status
# -o: prevents errors in a pipeline from being masked
# IFS new value is less likely to cause confusing bugs when looping arrays or arguments (e.g. $@)

usage() { echo "Usage: $0 -i <subscriptionId> -g <resourceGroupName> -n <deploymentName> -l <resourceGroupLocation>" 1>&2; exit 1; }

declare subscriptionId=""
declare resourceGroupName=""
declare deploymentName=""
declare resourceGroupLocation=""

# Initialize parameters specified from command line
while getopts ":i:g:n:l:" arg; do
    case "${arg}" in
        i)
            subscriptionId=${OPTARG}
            ;;
        g)
            resourceGroupName=${OPTARG}
            ;;
        n)
            deploymentName=${OPTARG}
            ;;
        l)
            resourceGroupLocation=${OPTARG}
            ;;
        esac
done
shift $((OPTIND-1))

#Prompt for parameters is some required parameters are missing
if [[ -z "$subscriptionId" ]]; then
    echo "Subscription Id:"
    read subscriptionId
    [[ "${subscriptionId:?}" ]]
fi

if [[ -z "$resourceGroupName" ]]; then
    echo "ResourceGroupName:"
    read resourceGroupName
    [[ "${resourceGroupName:?}" ]]
fi

if [[ -z "$deploymentName" ]]; then
    echo "DeploymentName:"
    read deploymentName
fi

if [[ -z "$resourceGroupLocation" ]]; then
    echo "Enter a location below to create a new resource group else skip this"
    echo "ResourceGroupLocation:"
    read resourceGroupLocation
fi

#templateFile Path - template file to be used
templateFilePath="template.json"

if [ ! -f "$templateFilePath" ]; then
    echo "$templateFilePath not found"
    exit 1
fi

#parameter file path
parametersFilePath="parameters.json"

if [ ! -f "$parametersFilePath" ]; then
    echo "$parametersFilePath not found"
    exit 1
fi

if [ -z "$subscriptionId" ] || [ -z "$resourceGroupName" ] || [ -z "$deploymentName" ]; then
    echo "Either one of subscriptionId, resourceGroupName, deploymentName is empty"
    usage
fi

#login to azure using your credentials
az account show 1> /dev/null

if [ $? != 0 ];
then
    az login
fi

#set the default subscription id
az account set --name $subscriptionId

set +e

#Check for existing RG
az group show $resourceGroupName 1> /dev/null

if [ $? != 0 ]; then
    echo "Resource group with name" $resourceGroupName "could not be found. Creating new resource group.."
    set -e
    (
        set -x
        az group create --name $resourceGroupName --location $resourceGroupLocation 1> /dev/null
    )
    else
    echo "Using existing resource group..."
fi

#Start deployment
echo "Starting deployment..."
(
    set -x
    az group deployment create --name $deploymentName --resource-group $resourceGroupName --template-file $templateFilePath --parameters $parametersFilePath
)

if [ $?  == 0 ];
 then
    echo "Template has been successfully deployed"
fi
```

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
