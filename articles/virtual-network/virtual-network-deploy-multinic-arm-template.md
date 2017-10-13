---
title: "Creación de una máquina virtual con varias NIC (plantilla de Azure Resource Manager) | Microsoft Docs"
description: "Cree una máquina virtual con varias NIC mediante una plantilla de Azure Resource Manager."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 486f7dd5-cf2f-434c-85d1-b3e85c427def
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 85bfa264c6cf2b0586816a47b3ab72f3aee8ec96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-multiple-nics-using-a-template"></a>Crear una VM con varias NIC mediante una plantilla
[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../resource-manager-deployment-model.md).  En este artículo se describe el uso del modelo de implementación de Resource Manager, recomendado por Microsoft para la mayoría de las nuevas implementaciones en lugar del [modelo de implementación clásica](virtual-network-deploy-multinic-classic-ps.md).
> 

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

En los pasos siguientes se usa un grupo de recursos denominado *IaaSStory* para los servidores web y un grupo de recursos denominado *IaaSStory-BackEnd* para los servidores de base de datos.

## <a name="prerequisites"></a>Requisitos previos
Antes de crear los servidores de base de datos, necesita crear el grupo de recursos *IaaSStory* con todos los recursos necesarios para este escenario. Para crear estos recursos, complete los siguientes pasos:

1. Vaya a [la página de plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. En la página de la plantilla, a la derecha de **Grupo de recursos primarios**, haga clic en **Implementar en Azure**.
3. Si es necesario, cambie los valores de parámetro y siga los pasos en el Portal de vista previa de Azure para implementar el grupo de recursos.

> [!IMPORTANT]
> Asegúrese de que los nombres de cuenta de almacenamiento sean únicos. No puede tener nombres de cuenta de almacenamiento duplicados en Azure.
> 

## <a name="understand-the-deployment-template"></a>Descripción de la plantilla de implementación
Antes de implementar la plantilla proporcionada con esta documentación, asegúrese de que entiende qué hace. En los pasos siguientes se ofrece información general adecuada sobre la plantilla:

1. Vaya a [la página de plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Haga clic en **azuredeploy.json** para abrir el archivo de plantilla.
3. Observe el parámetro *osType* mostrado a continuación. Este parámetro se utiliza para seleccionar qué imagen de máquina virtual usar para el servidor de base de datos, junto con varias configuraciones relacionadas del sistema operativo.

    ```json
    "osType": {
      "type": "string",
      "defaultValue": "Windows",
      "allowedValues": [
        "Windows",
        "Ubuntu"
      ],
      "metadata": {
      "description": "Type of OS to use for VMs: Windows or Ubuntu."
      }
    },
    ```

4. Desplácese hacia abajo hasta la lista de variables y compruebe la definición de las variables **dbVMSetting** , que aparecen a continuación. Recibe uno de los elementos de la matriz contenidos en la variable **dbVMSettings** . Si está familiarizado con la terminología de desarrollo de software, puede ver la variable **dbVMSettings** como una tabla hash o un diccionario.

    ```json
    "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"
    ```

5. Suponga que decide implementar máquinas virtuales de Windows que ejecuten SQL en el back-end. Entonces, el valor de **osType** sería *Windows*, y la variable **dbVMSetting** contendría el elemento que se muestra a continuación, que representa el primer valor de la variable **dbVMSettings**.

    ```json
    "Windows": {
      "vmSize": "Standard_DS3",
      "publisher": "MicrosoftSQLServer",
      "offer": "SQL2014SP1-WS2012R2",
      "sku": "Standard",
      "version": "latest",
      "vmName": "DB",
      "osdisk": "osdiskdb",
      "datadisk": "datadiskdb",
      "nicName": "NICDB",
      "ipAddress": "192.168.2.",
      "extensionDeployment": "",
      "avsetName": "ASDB",
      "remotePort": 3389,
      "dbPort": 1433
    },
    ```

6. Observe que **vmSize** contiene el valor *Standard_DS3*. Permiten solo ciertos tamaños de máquina virtual para el uso de varias tarjetas NIC. Para comprobar los tamaños de máquina virtual que admiten varias NIC, vea los artículos [Tamaños de las máquinas virtuales Windows en Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) y [Tamaños de las máquinas virtuales Linux en Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

7. Desplácese hacia abajo hasta **recursos** y observe el primer elemento. Describe una cuenta de almacenamiento. Esta cuenta de almacenamiento se usará para mantener los discos de datos usados por cada máquina virtual de la base de datos. En este escenario, cada máquina virtual de base de datos tiene un disco de sistema operativo almacenado en almacenamiento normal y dos discos de datos almacenados en el almacenamiento SSD (Premium).

    ```json
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('prmStorageName')]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "Storage Account - Premium"
      },
      "properties": {
      "accountType": "[parameters('prmStorageType')]"
      }
    },
    ```

8. Desplácese hacia abajo hasta el siguiente recurso, como se muestra a continuación. Este recurso representa la NIC usada para el acceso a la base de datos en cada máquina virtual de la base de datos. Observe el uso de la función **copiar** en este recurso. La plantilla le permite implementar todas las máquinas virtuales que desee, según el parámetro **dbCount** . Por lo tanto, debe crear la misma cantidad de NIC para el acceso de la base de datos, uno para cada máquina virtual.

    ```json
    {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
    "location": "[variables('location')]",
    "tags": {
      "displayName": "NetworkInterfaces - DB DA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "privateIPAllocationMethod": "Static",
            "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
            "subnet": {
              "id": "[variables('backEndSubnetRef')]"
            }
          }
         }
       ] 
     }
    },
    ```

9. Desplácese hacia abajo hasta el siguiente recurso, como se muestra a continuación. Este recurso representa la NIC usada para la administración en cada máquina virtual de la base de datos. Una vez más, necesita una de estas NIC para cada máquina virtual de base de datos. Observe el elemento **networkSecurityGroup** que vincula un grupo de seguridad de red que permite el acceso a RDP o SSH solo a esta NIC.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "NetworkInterfaces - DB RA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "networkSecurityGroup": {
             "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
             },
             "privateIPAllocationMethod": "Static",
             "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
             "subnet": {
              "id": "[variables('backEndSubnetRef')]"
             }
           }
          }
        ]
      }
    },
```

10. Desplácese hacia abajo hasta el siguiente recurso, como se muestra a continuación. Este recurso representa un conjunto de disponibilidad que compartir con todas las máquinas virtuales de base de datos. De este modo, se garantiza que siempre habrá una máquina virtual en el conjunto que se ejecuta durante el mantenimiento.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/availabilitySets",
      "name": "[variables('dbVMSetting').avsetName]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "AvailabilitySet - DB"
      }
    },
    ```

11. Desplácese hacia abajo hasta el siguiente recurso. Este recurso representa las máquinas virtuales de base de datos, como se muestran en las primeras líneas siguientes. Observe el uso de la función **copiar** de nuevo asegurándose de que se crean varias VM según el parámetro **dbCount**. Observe también la colección **dependsOn** . Muestra dos NIC que tienen que crearse antes de implementar la máquina virtual, junto con el conjunto de disponibilidad y la cuenta de almacenamiento.

    ```json
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
    "location": "[variables('location')]",
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
      "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
      "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
    ],
    "tags": {
      "displayName": "VMs - DB"
    },
    "copy": {
      "name": "dbvmcount",
      "count": "[parameters('dbCount')]"
    },
    ```

12. Desplácese hacia abajo en el recurso de máquina virtual para el elemento **networkProfile** como se muestra a continuación. Observe que existen dos NIC que hacen referencia a cada máquina virtual. Al crear varias NIC para una VM, necesita establecer la propiedad **principal** de una de esas NIC en *true* y el resto en *false*.

    ```json
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
          "properties": { "primary": true }
        },
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
          "properties": { "primary": false }
        }
      ]
    }
    ```

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Implementar la plantilla ARM por medio de un solo clic para implementar

> [!IMPORTANT]
> Asegúrese de que sigue los [pasos previos](#Pre-requisites) antes de seguir las instrucciones que aparecen a continuación.
> 

La plantilla de ejemplo disponible en el repositorio público usa un archivo de parámetros que contiene los valores predeterminados utilizados para generar el escenario descrito anteriormente. Para implementar esta plantilla con el método de hacer clic para implementar, siga [este vínculo](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), a la derecha de **Grupo de recursos back-end (vea la documentación)**, haga clic en **Implementar en Azure**, reemplace los valores de parámetro predeterminados si es necesario y siga las instrucciones del portal.

La siguiente ilustración muestra el contenido del nuevo grupo de recursos después de la implementación.

![Grupo de recursos back-end](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>Implementación de la plantilla mediante PowerShell
Para implementar la plantilla que ha descargado con PowerShell, instale y configure PowerShell completando los pasos del artículo [Install and configure PowerShell (Instalar y configurar PowerShell)](/powershell/azure/overview) y, después, siga los siguientes pasos:

Ejecute el cmdlet **`New-AzureRmResourceGroup`** para crear un grupo de recursos con esta plantilla.

```powershell
New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
-TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'
```

Resultado esperado:

    ResourceGroupName : IaaSStory-Backend
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions  NotActions
                        =======  ==========
                        *
        Resources         :
                        Name                 Type                                 Location
                        ===================  ===================================  ========
                        ASDB                 Microsoft.Compute/availabilitySets   westus  
                        DB1                  Microsoft.Compute/virtualMachines    westus  
                        DB2                  Microsoft.Compute/virtualMachines    westus  
                        NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                        wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  
    ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implementación la plantilla ARM mediante la CLI de Azure
Para implementar la plantilla ARM mediante la CLI de Azure, siga estos pasos.

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](../cli-install-nodejs.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.
2. Ejecute el comando **`azure config mode`** para cambiar al modo de Administrador de recursos, como se muestra a continuación.

    ```azurecli
    azure config mode arm
    ```

    El resultado esperado es el siguiente:

        info:    New mode is arm

3. Abra el [archivo de parámetros](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), seleccione su contenido y guárdelo en un archivo en el equipo. Para este ejemplo, guardamos el archivo de parámetros en *parameters.json*.
4. Ejecute el cmdlet **`azure group deployment create`** para implementar la nueva red virtual con la plantilla y los archivos de parámetros que descargó y modificó anteriormente. En la lista que se muestra en la salida se explican los parámetros utilizados.

    ```azurecli
    azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json
    ```

    Resultado esperado:
   
        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

