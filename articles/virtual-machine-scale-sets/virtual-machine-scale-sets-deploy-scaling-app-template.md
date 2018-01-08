---
title: "Creación de un conjunto de escalado de máquinas virtuales con una plantilla de Azure | Microsoft Docs"
description: "Aprenda a crear rápidamente un conjunto de escalado de máquinas virtuales con una plantilla de Azure Resource Manager."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2017
ms.author: iainfou
ms.openlocfilehash: 614c7c82aabab212753529a21d7a770b7a02027e
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Creación de un conjunto de escalado de máquinas virtuales con la CLI de Azure 2.0
El conjunto de escalado de máquinas virtuales le permite implementar y administrar un conjunto de máquinas virtuales de escalado automático idénticas. Puede escalar el número de máquinas virtuales del conjunto de escalado manualmente o definir reglas de escalado automático basado en el uso de recursos tales como la CPU, la demanda de memoria o el tráfico de red. En este artículo introductorio, debe crear un conjunto de escalado de máquinas virtuales con una plantilla de Azure Resource Manager. También puede crear un conjunto de escalado mediante la [CLI de Azure 2.0](virtual-machine-scale-sets-create-cli.md), [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) o [Azure Portal](virtual-machine-scale-sets-create-portal.md).


## <a name="overview-of-templates"></a>Introducción a las plantillas
Las plantillas de Azure Resource Manager le permiten implementar grupos de recursos relacionados. Las plantillas se escriben en notación de objetos JavaScript (JSON) y definen el entorno de infraestructura de Azure completo para la aplicación. En una única plantilla, puede crear el conjunto de escalado de máquinas virtuales, instalar aplicaciones y configurar reglas de escalado automático. Con el uso de variables y parámetros, esta plantilla se puede reutilizar para actualizar los conjuntos de escalado existentes o crear más. Puede implementar plantillas mediante Azure Portal, la CLI de Azure 2.0 o Azure PowerShell, así como llamarlas desde las canalizaciones de integración continua o entrega continua (CI/CD).

Para obtener más información sobre las plantillas, consulte [Información general de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment).


## <a name="define-a-scale-set"></a>Definición de un conjunto de escalado
Una plantilla define la configuración de cada tipo de recurso. Un tipo de recurso del conjunto de escalado de máquinas virtuales es similar a una máquina virtual individual. Las partes principales del tipo de recurso del conjunto de escalado de máquinas virtuales son:

| Propiedad                     | Descripción de la propiedad                                  | Valor de la plantilla de ejemplo                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| Tipo                         | Tipo de recurso de Azure que se creará                            | Microsoft.Compute/virtualMachineScaleSets |
| Nombre                         | Nombre del conjunto de escalado                                       | myScaleSet                                |
| location                     | Ubicación donde se creará el conjunto de escalado                     | Este de EE. UU                                   |
| sku.name                     | Tamaño de VM para cada instancia de conjunto de escalado                  | Standard_A1                               |
| sku.capacity                 | Número de instancias de VM que se crearán inicialmente           | 2                                         |
| upgradePolicy.mode           | Modo de actualización de la instancia de VM cuando se producen cambios              | Automático                                 |
| imageReference               | Imagen personalizada o plataforma que se usará para las instancias de VM | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | Prefijo de nombre para cada instancia de VM                     | myvmss                                    |
| osProfile.adminUsername      | Nombre de usuario para cada instancia de VM                        | azureuser                                 |
| osProfile.adminPassword      | Contraseña para cada instancia de VM                        | P@ssw0rd!                                 |

 En el siguiente fragmento de código se muestra la definición del recurso del conjunto de escalado principal de una plantilla. Para proporcionarle un ejemplo corto, no se muestra la configuración de la tarjeta adaptadora de red virtual (NIC). Para personalizar una plantilla de conjunto de escalado, puede cambiar el tamaño de la VM o la capacidad inicial, o usar otra plataforma o una imagen personalizada.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2016-04-30-preview",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```


## <a name="install-an-application"></a>Instalación de una aplicación
Cuando se implementa un conjunto de escalado, las extensiones de VM pueden proporcionar tareas de configuración y automatización posteriores a la implementación, como la instalación de una aplicación. Los scripts se pueden descargar desde Azure Storage o GitHub, o se pueden proporcionar a Azure Portal en el tiempo de ejecución de la extensión. Para aplicar una extensión al conjunto de escalado, agregue la sección *extensionProfile* en el ejemplo anterior del recurso. Normalmente, el perfil de extensión define las propiedades siguientes:

- Tipo de extensión
- Editor de la extensión
- Versión de la extensión
- Ubicación de los scripts de configuración o instalación
- Comandos que se ejecutarán en las instancias de VM

Echemos un vistazo a dos formas de instalar una aplicación con extensiones: con la extensión de script personalizada para instalar una aplicación de Python en Linux, o con la extensión de DSC de PowerShell para instalar una aplicación de ASP.NET en Windows.

### <a name="python-http-server-on-linux"></a>Servidor HTTP de Python en Linux
El [servidor HTTP de Python en Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) usa la extensión de script personalizada para instalar [Bottle](http://bottlepy.org/docs/dev/), un marco web de Python y un servidor HTTP sencillo. 

Se definen dos scripts en *fileUris* - *installserver.sh* y *workserver.py*. Estos archivos se descargan desde GitHub y, a continuación, *commandToExecute* define `bash installserver.sh` para que la aplicación se instale y se configure:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
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

### <a name="aspnet-application-on-windows"></a>Aplicación ASP.NET en Windows
La plantilla de ejemplo de [Aplicación ASP.NET en Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) usa la extensión DSC de PowerShell para instalar una aplicación ASP.NET MVC que se ejecuta en IIS. 

Se descarga un script de instalación desde GitHub, tal como se define en *url*. A continuación, la extensión ejecuta *InstallIIS* desde el script *IISInstall.ps1*, tal como se define en *function* y *script*. La propia aplicación de ASP.NET se proporciona como un paquete de Web Deploy, que también se descarga desde GitHub, tal como se define en *WebDeployPackagePath*:

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
        "forceUpdateTag": "1.0",
        "settings": {
          "configuration": {
            "url": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/DSC/IISInstall.ps1.zip",
            "script": "IISInstall.ps1",
            "function": "InstallIIS"
          },
          "configurationArguments": {
            "nodeName": "localhost",
            "WebDeployPackagePath": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/WebDeploy/DefaultASPWebApp.v1.0.zip"
          }
        }
      }
    }
  ]
}
```

## <a name="deploy-the-template"></a>Implementación de la plantilla
La manera más sencilla de implementar la plantilla [Servidor HTTP de Python en Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) o [Aplicación ASP.NET MVC en Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) es usar el botón **Implementar en Azure** que se encuentra en los archivos Léame en GitHub.  También puede usar PowerShell o la CLI de Azure para implementar las plantillas de ejemplo.

### <a name="azure-cli-20"></a>CLI de Azure 2.0
Puede utilizar la CLI de Azure 2.0 para instalar al servidor HTTP de Python en Linux, como se indica a continuación:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Para ver la aplicación en acción, obtenga la dirección IP pública del equilibrador de carga con [az network public-ip list](/cli/azure/network/public-ip#show) según se indica a continuación:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Escriba la dirección IP pública del equilibrador de carga en un explorador web con el formato *http://<publicIpAddress>:9000/do_work*. El equilibrador de carga distribuye el tráfico a una de las instancias de VM, como se muestra en el ejemplo siguiente:

![Página web predeterminada de NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)


### <a name="azure-powershell"></a>Azure PowerShell
Puede usar Azure PowerShell para instalar la aplicación de ASP.NET en Windows como se indica a continuación:

```azurepowershell-interactive
# Create a resource group
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateFile https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json
```

Para ver la aplicación en acción, obtenga la dirección IP pública del equilibrador de carga con [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) como se muestra a continuación:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Escriba la dirección IP pública del equilibrador de carga en un explorador web con el formato *http://<publicIpAddress>/MyApp*. El equilibrador de carga distribuye el tráfico a una de las instancias de VM, como se muestra en el ejemplo siguiente:

![Ejecución del sitio de IIS](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Limpieza de recursos
Cuando ya no lo necesite, puede usar el comando [az group delete](/cli/azure/group#delete) para quitar el grupo de recursos, el conjunto de escalado y todos los recursos relacionados como se indica a continuación:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>pasos siguientes
