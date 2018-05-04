---
title: 'Azure Service Fabric: configuración de la supervisión con Log Analytics | Microsoft Docs'
description: Aprenda a configurar Log Analytics para visualizar y analizar los eventos con el fin de supervisar los clústeres de Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/03/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: 3d6a47ba184b4bbbd290a61c581ae8b83b9361af
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="set-up-log-analytics-for-a-cluster"></a>Configuración de Log Analytics para un clúster

Log Analytics es nuestra recomendación para supervisar eventos de nivel de clúster. Puede configurar un área de trabajo de Log Analytics mediante Azure Resource Manager, PowerShell o Azure Marketplace. Si realiza el mantenimiento de una plantilla actualizada de Resource Manager de su implementación para usarla en un futuro, use la misma plantilla para configurar el entorno de Log Analytics. La implementación a través de Marketplace es más fácil si ya tiene un clúster implementado con Diagnostics habilitado. Si no tiene acceso de nivel de suscripción en la cuenta en la que va a implementar, use PowerShell, realice la implementación mediante PowerShell o la plantilla de Resource Manager.

> [!NOTE]
> Para configurar Log Analytics con el fin de supervisar el clúster, debe haber habilitado los diagnósticos para ver los eventos de nivel de plataforma o de clúster. Consulte [cómo configurar diagnósticos en clústeres de Windows](service-fabric-diagnostics-event-aggregation-wad.md) y [cómo configurar diagnósticos en clústeres de Linux](service-fabric-diagnostics-event-aggregation-lad.md) para más información

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Implementación de un área de trabajo de Log Analytics mediante Azure Marketplace

Si quiere agregar un área de trabajo de Log Analytics después de haber implementado un clúster, vaya a Azure Marketplace en el portal y busque **Service Fabric Analytics**. Esta es una solución personalizada para implementaciones de Service Fabric que tiene datos específicos de Service Fabric. En este proceso creará tanto la solución (el panel para ver información) como el área de trabajo (la agregación de los datos del clúster subyacentes).

1. Haga clic en **Nuevo** en el menú de navegación de la izquierda. 

2. Busque **Service Fabric Analytics**. Seleccione el recurso que aparece.

3. Seleccione **Crear**.

    ![SF Analytics en OMS con Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. En la ventana de creación de Service Fabric Analytics, haga clic en **Seleccione un área de trabajo** para el campo **Área de trabajo de OMS** y haga clic en **Create a new workspace** (Crear un área de trabajo). Rellene las entradas necesarias. El único requisito en este caso es que la suscripción del clúster de Service Fabric sea la misma que la del área de trabajo. Después de haber validado las entradas, el área de trabajo comienza la implementación. Esta operación solo tarda unos minutos.

5. Cuando termine, seleccione **Crear** de nuevo en la parte inferior de la ventana de creación de Service Fabric Analytics. Asegúrese de que la nueva área de trabajo aparece en **Área de trabajo de OMS**. Esta acción agrega la solución al área de trabajo que ha creado.

Si usa Windows, continúe con los siguientes pasos para conectar OMS a la cuenta de almacenamiento en la que se almacenan los eventos de clúster. 

>[!NOTE]
>La habilitación de esta experiencia para clústeres de Linux aún no está disponible. 

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Conexión del área de trabajo de Log Analytics al clúster 

1. El área de trabajo debe conectarse a los datos de diagnóstico que proceden del clúster. Vaya al grupo de recursos donde creó la solución Service Fabric Analytics. Seleccione **ServiceFabric\<nameOfWorkspace\>** y vaya a su página de información general. Desde allí, puede cambiar la configuración de la solución, la configuración del área de trabajo y acceder al portal de OMS.

2. En el menú de navegación izquierdo, haga clic en **Orígenes de datos del área de trabajo**, seleccione **Registros de las cuentas de almacenamiento**.

3. En la página **Registros de las cuentas de almacenamiento**, seleccione **Agregar** en la parte superior para agregar los registros del clúster al área de trabajo.

4. Seleccione **Cuenta de Storage** para agregar la cuenta correspondiente que se creó en el clúster. Si usó el nombre predeterminado, la cuenta de Storage se denomina **sfdg\<resourceGroupName\>**. Puede confirmar esto con la plantilla de Azure Resource Manager que se usa para implementar el clúster, mediante la comprobación del valor usado para **applicationDiagnosticsStorageAccountName**. Si el nombre no aparece, desplácese hacia abajo y seleccione **Cargar más**. Escriba el nombre de la cuenta de almacenamiento.

5. Especifique el tipo de datos. Establézcalo en **Eventos de Service Fabric**.

6. Asegúrese de que el origen se establece automáticamente en **WADServiceFabric\*EventTable**.

7. Haga clic en **Aceptar** para conectar el área de trabajo a los registros del clúster.

    ![Incorporación de registros de la cuenta de Storage a OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

La cuenta se muestra ahora como parte de los registros de la cuenta de Storage en los orígenes de datos del área de trabajo.

Ha agregado la solución Service Fabric Analytics a un área de trabajo de OMS Log Analytics que ahora está correctamente conectada a la plataforma del clúster y a la tabla de registros de la aplicación. Puede agregar orígenes adicionales al área de trabajo de la misma manera.


## <a name="deploy-oms-by-using-a-resource-manager-template"></a>Implementación de OMS con una plantilla de Resource Manager

Al implementar un clúster con una plantilla de Resource Manager, la plantilla crea una nueva área de trabajo de OMS, agrega la solución de Service Fabric al área de trabajo y la configura para leer datos de las tablas de almacenamiento apropiadas.

Puede usar y modificar [esta plantilla de ejemplo](https://github.com/krnese/azure-quickstart-templates/tree/master/service-fabric-oms) para satisfacer sus necesidades.

Realice las modificaciones siguientes:
1. Agregue `omsWorkspaceName` y `omsRegion` a los parámetros y agregue el siguiente fragmento de código a los parámetros definidos en el archivo *template.json*. Puede modificar los valores predeterminados según estime oportuno. Agregue también los dos nuevos parámetros al archivo *parameters.json* para definir sus valores para la implementación de recursos:
    
    ```json
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "sfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
    ```

    Los valores `omsRegion` deben ajustarse a un conjunto específico de valores. Elija la que esté más próximo a la implementación del clúster.

2. Si envía registros de aplicaciones a OMS, confirme que `applicationDiagnosticsStorageAccountType` y `applicationDiagnosticsStorageAccountName` se incluyen como parámetros en la plantilla. Si no están incluidos, agréguelos a la sección de variables y edite sus valores según corresponda. También puede incluirlos como parámetros siguiendo el formato anterior.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. Agregue la solución OMS de Service Fabric a las variables de la plantilla:

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. Agregue lo siguiente al final de la sección de recursos, después de donde se declara el recurso de clúster de Service Fabric:

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', parameters('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "id": "[resourceId('Microsoft.OperationsManagement/solutions/', variables('solution'))]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
    ```
    
    > [!NOTE]
    > Si agregó `applicationDiagnosticsStorageAccountName` como una variable, asegúrese de sustituir todas las referencias a él por `variables('applicationDiagnosticsStorageAccountName')` en lugar de `parameters('applicationDiagnosticsStorageAccountName')`.

5. Implemente la plantilla como una actualización de Resource Manager del clúster mediante la API `New-AzureRmResourceGroupDeployment` del módulo AzureRM PowerShell. Un comando de ejemplo sería:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager detecta que este comando es una actualización para un recurso existente. Solo se procesan los cambios entre la plantilla en que se basa la implementación existente y la nueva plantilla proporcionada.

## <a name="deploy-oms-by-using-azure-powershell"></a>Implementación de OMS mediante Azure PowerShell

También puede implementar el recurso de Log Analytics de OMS a través de PowerShell mediante el comando `New-AzureRmOperationalInsightsWorkspace`. Para usar este método, asegúrese de que ha instalado [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Use este script para crear una nueva área de trabajo de Log Analytics para OMS y agregue la solución Service Fabric a esta: 

```PowerShell

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<OMS Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Cuando haya terminado, siga los pasos descritos en la sección anterior para conectar Log Analytics de OMS a la cuenta de almacenamiento adecuada.

También puede agregar otras soluciones o realizar otras modificaciones al área de trabajo de OMS mediante PowerShell. Para más información, consulte [Administración de Log Analytics mediante PowerShell](../log-analytics/log-analytics-powershell-workspace-configuration.md).

## <a name="next-steps"></a>Pasos siguientes
* [Implemente el Agente de OMS](service-fabric-diagnostics-oms-agent.md) en los nodos para recopilar los contadores de rendimiento y recopilar estadísticas de Docker y registros para los contenedores
* Familiarícese con las funciones de [búsqueda de registros y consulta](../log-analytics/log-analytics-log-searches.md) que se ofrecen como parte de Log Analytics
* [Uso del Diseñador de vistas para crear vistas personalizadas en Log Analytics](../log-analytics/log-analytics-view-designer.md)
