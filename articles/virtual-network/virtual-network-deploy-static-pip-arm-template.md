---
title: "Creación de una máquina virtual con una dirección IP pública estática (plantilla de Azure Resource Manager) | Microsoft Docs"
description: "Obtenga información sobre cómo crear una máquina virtual con una dirección IP pública estática mediante una plantilla de Azure Resource Manager."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d551085a-c7ed-4ec6-b4c3-e9e1cebb774c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2f503aa60fdd9b7cf66ef482a1041e34c88e5c01
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-an-azure-resource-manager-template"></a>Creación de una máquina virtual con una dirección IP pública estática mediante una plantilla de Azure Resource Manager

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [CLI de Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [Plantilla](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (clásico)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../resource-manager-deployment-model.md). En este artículo se describe el uso del modelo de implementación de Resource Manager, recomendado por Microsoft para la mayoría de las nuevas implementaciones en lugar del modelo de implementación clásica.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="public-ip-address-resources-in-a-template-file"></a>Recursos de dirección IP pública en un archivo de plantilla
Puede ver y descargar la [plantilla de ejemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json).

En la sección siguiente se muestra la definición del recurso de dirección IP pública, basada en el escenario anterior:

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('webVMSetting').pipName]",
  "location": "[variables('location')]",
  "properties": {
    "publicIPAllocationMethod": "Static"
  },
  "tags": {
    "displayName": "PublicIPAddress - Web"
  }
},
```

Observe la propiedad **publicIPAllocationMethod** , que se establece en *Static*. Esta propiedad puede ser *Dynamic* (valor predeterminado) o *Static*. Si se establece en estática, se garantiza que la dirección IP pública asignada no cambiará nunca.

En la sección siguiente se muestra la asociación de la dirección IP pública con una interfaz de red:

```json
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[variables('webVMSetting').nicName]",
    "location": "[variables('location')]",
    "tags": {
    "displayName": "NetworkInterface - Web"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
      "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
    ],
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
          "privateIPAllocationMethod": "Static",
          "privateIPAddress": "[variables('webVMSetting').ipAddress]",
          "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
          },
          "subnet": {
            "id": "[variables('frontEndSubnetRef')]"
          }
        }
      }
    ]
  }
},
```

Observe la propiedad **publicIPAddress** que señala a **Id** de un recurso denominado **variables('webVMSetting').pipName**. Se trata del nombre del recurso de IP pública que se muestra arriba.

Por último, la interfaz de red anterior se muestra en la propiedad **networkProfile** de la máquina virtual que se va a crear.

```json
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }
```

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Implementar la plantilla por medio de un solo clic para implementar

La plantilla de ejemplo disponible en el repositorio público usa un archivo de parámetros que contiene los valores predeterminados utilizados para generar el escenario descrito anteriormente. Para implementar esta plantilla haciendo clic para implementar, haga clic en **Implementar en Azure** en el archivo Readme.md para la plantilla [Máquina virtual con PIP estático](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP) . Reemplace los valores de parámetro predeterminados si lo desea y escriba los valores para los parámetros en blanco.  Siga las instrucciones en el portal para crear una máquina virtual con una dirección IP pública estática.

## <a name="deploy-the-template-by-using-powershell"></a>Implementación de la plantilla mediante PowerShell

Para implementar la plantilla que descargó con PowerShell, siga estos pasos.

1. Si nunca usó Azure PowerShell, complete los pasos del artículo [How to Install and Configure Azure PowerShell](/powershell/azure/overview) (Cómo instalar y configurar Azure PowerShell).
2. En una consola de PowerShell, ejecute el cmdlet `New-AzureRmResourceGroup` para crear un nuevo grupo de recursos, si fuera necesario. Si ya tiene un grupo de recursos creado, vaya al paso 3.

    ```powershell
    New-AzureRmResourceGroup -Name PIPTEST -Location westus
    ```

    Resultado esperado:
   
        ResourceGroupName : PIPTEST
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. En una consola de PowerShell, ejecute el cmdlet `New-AzureRmResourceGroupDeployment` para implementar la plantilla.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
        -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
        -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json
    ```

    Resultado esperado:
   
        DeploymentName    : DeployVM
        ResourceGroupName : PIPTEST
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
                            Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
                            ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
                            ContentVersion : 1.0.0.0
   
        Parameters        :
                            Name                      Type                       Value     
                            ========================  =========================  ==========
                            vnetName                  String                     WTestVNet
                            vnetPrefix                String                     192.168.0.0/16
                            frontEndSubnetName        String                     FrontEnd  
                            frontEndSubnetPrefix      String                     192.168.1.0/24
                            storageAccountNamePrefix  String                     iaasestd  
                            stdStorageType            String                     Standard_LRS
                            osType                    String                     Windows   
                            adminUsername             String                     adminUser
                            adminPassword             SecureString                         
   
        Outputs           :

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implementación la plantilla ARM mediante la CLI de Azure
Para implementar la plantilla mediante la CLI de Azure, complete los siguientes pasos:

1. Si no ha usado nunca la CLI de Azure, siga los pasos del artículo [Instalación de la CLI de Azure](../cli-install-nodejs.md) para instalarla y configurarla.
2. Ejecute el comando `azure config mode` para cambiar al modo Resource Manager, como se muestra a continuación.

    ```azurecli
    azure config mode arm
    ```

    Este es el resultado esperado del comando anterior:

        info:    New mode is arm

3. Abra el [archivo de parámetros](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json), seleccione su contenido y guárdelo en un archivo del equipo. En este ejemplo, los parámetros se guardan en un archivo denominado *parameters.json*. Cambie los valores de parámetro en el archivo si lo desea, pero como mínimo, se recomienda que cambie el valor para el parámetro adminPassword a una contraseña única y compleja.
4. Ejecute el cmd `azure group deployment create` para implementar la nueva red virtual mediante la plantilla y los archivos de parámetros que descargó y modificó anteriormente. En el siguiente comando, reemplace <path> por la ruta de acceso en la que guardó el archivo. 

    ```azurecli
    azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e <path>\parameters.json
    ```

    Resultado esperado (enumera los valores de parámetro utilizados):

        info:    Executing command group create
        + Getting resource group PIPTEST2
        + Creating resource group PIPTEST2
        info:    Created resource group PIPTEST2
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/[Subscription ID]/resourceGroups/PIPTEST2
        data:    Name:                PIPTEST2
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

