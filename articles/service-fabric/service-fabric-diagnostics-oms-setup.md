---
title: "Azure Service Fabric: configuración de la supervisión con Log Analytics de OMS | Microsoft Docs"
description: "Obtenga información sobre cómo configurar OMS para visualizar y analizar eventos para la supervisión de los clústeres de Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2017
ms.author: dekapur
ms.openlocfilehash: 53b06c5a1395f34c96d4011366835a920d5c670b
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="set-up-oms-log-analytics-for-a-cluster"></a>Configuración de Log Analytics de OMS para un clúster

Puede configurar un área de trabajo de OMS mediante Azure Resource Manager, PowerShell o desde Azure Marketplace. Si está realizando el mantenimiento de una plantilla actualizada de Resource Manager de la implementación para un uso futuro, utilice la misma plantilla para configurar el entorno de OMS. La implementación a través de Marketplace es más fácil si ya tiene un clúster implementado con Diagnostics habilitado. En el caso de que no tenga acceso en el nivel de suscripción en la cuenta en la que está implementando OMS, use PowerShell, o realice la implementación mediante la plantilla de Resource Manager.

> [!NOTE]
> Diagnostics debe estar habilitado para que el clúster pueda visualizar los eventos a nivel de clúster o de plataforma, a fin de poder configurar OMS para que supervise correctamente el clúster.

## <a name="deploying-oms-using-azure-marketplace"></a>Implementación de OMS a través de Azure Marketplace

Si desea agregar un área de trabajo de OMS después de haber implementado un clúster, diríjase a Azure Marketplace (en Azure Portal) y busque *"Service Fabric Analytics"*.

1. Haga clic en **Nuevo** en el menú de navegación de la izquierda. 

2. Busque *Service Fabric Analytics*. Haga clic en el recurso que se muestra.

3. Haga clic en **Crear**.

    ![SF Analytics en OMS con Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. En la ventana de creación de Service Fabric Analytics, haga clic en **Seleccione un área de trabajo** para el campo *Área de trabajo de OMS* y haga clic en **Create a new workspace** (Crear un área de trabajo). Rellene las entradas necesarias; el único requisito en este caso es que la suscripción del clúster de Service Fabric sea la misma que la del área de trabajo de OMS. Después de haber validado las entradas, el área de trabajo de OMS comenzará la implementación. Esto solo tardará unos minutos.

5. Cuando termine, haga clic en **Crear** de nuevo en la parte inferior de la ventana de creación de Service Fabric Analytics. Asegúrese de que la nueva área de trabajo aparece en *Área de trabajo de OMS*. Esto agregará la solución al área de trabajo que acaba de crear.

Si usa Windows, continúe con los siguientes pasos para enlazar OMS a la cuenta de Storage en la que se almacenan los eventos de clúster. La habilitación correcta de esta experiencia para Linux está todavía en curso. Entretanto, continúe con la incorporación del agente de OMS al clúster.  

1. El área de trabajo todavía debe conectarse a los datos de diagnóstico del clúster. Vaya al grupo de recursos donde creó la solución Service Fabric Analytics. Debería ver *ServiceFabric (\<nombreDelÁreaDeTrabajoDeOMS\>)*. Haga clic en la solución para la página de introducción, desde donde podrá cambiar la configuración de la solución y del área de trabajo, y vaya al portal de OMS.

2. En el menú de navegación izquierdo, haga clic en **Registros de las cuentas de almacenamiento**, de *Orígenes de datos del área de trabajo*.

3. En la página *Registros de las cuentas de almacenamiento*, haga clic en **Agregar** en la parte superior para agregar los registros del clúster al área de trabajo.

4. Haga clic en **cuenta de Storage** para agregar la cuenta correspondiente que se creó en el clúster. Si utiliza el nombre predeterminado, la cuenta de Storage se denomina *sfdg\<resourceGroupName\>*. También puede confirmarlo al comprobar la plantilla de Azure Resource Manager utilizada para implementar el clúster; para ello, compruebe el valor de `applicationDiagnosticsStorageAccountName`. Quizá tenga que desplazarse hacia abajo y hacer clic en **Cargar más**, si no aparece el nombre. Haga clic en el nombre de cuenta de almacenamiento correcto para seleccionarlo.

5. A continuación tendrá que especificar el *Tipo de datos*, que debe establecerse como **Eventos de Service Fabric**.

6. El *Origen* se establecerá automáticamente en *WADServiceFabric\*EventTable*.

7. Haga clic en **Aceptar** para conectar el área de trabajo a los registros del clúster.

    ![Incorporación de registros de la cuenta de Storage a OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

La cuenta debería aparecer ahora como parte de los *registros de la cuenta de Storage* en los orígenes de datos del área de trabajo.

Con esto ya se ha agregado la solución Service Fabric Analytics a un área de trabajo de OMS Log Analytics correctamente conectada ahora a la plataforma del clúster y a la tabla de registros de la aplicación. Puede agregar orígenes adicionales al área de trabajo de la misma manera.


## <a name="deploying-oms-using-a-resource-manager-template"></a>Implementación de OMS con una plantilla de Resource Manager

Al implementar un clúster con una plantilla de Resource Manager, esta última también debe crear un área de trabajo de OMS, agregar a ella la solución de Service Fabric y configurarla para leer datos de las tablas de almacenamiento apropiadas.

[Aquí](https://azure.microsoft.com/resources/templates/service-fabric-oms/) hay una plantilla de ejemplo que puede usar y modificar según sea necesario. En la sección de [plantillas de Service Fabric y OMS](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS) puede encontrar más plantillas que ofrecen diferentes opciones de configuración de un área de trabajo de OMS.

Los principales cambios realizados son los siguientes:

1. Agregue `omsWorkspaceName` y `omsRegion` a los parámetros. Esto conlleva agregar el siguiente fragmento de código a los parámetros definidos en el archivo *template.json*. Puede modificar los valores predeterminados según estime oportuno. También debe agregar los dos parámetros nuevos en *parameters.json* para definir los valores para la implementación de recursos:
    
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

    Los valores `omsRegion` deben ajustarse a un conjunto específico de valores. Debe elegir uno que esté más próximo a la implementación del clúster.

2. Si va a enviar los registros de aplicaciones a OMS, confirme que `applicationDiagnosticsStorageAccountType` y `applicationDiagnosticsStorageAccountName` se incluyen como parámetros en la plantilla. Si no están incluidos, agréguelos a la sección de variables de este modo y edite sus valores según corresponda. También puede incluirlos como parámetros, si lo desea, siguiendo el formato usado anteriormente.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. Agregue la solución OMS de Service Fabric a las variables de la plantilla:

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. Agregue lo siguiente al final de la sección de resources; después, se declara aquí el recurso de clúster de Service Fabric.

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

5. Implemente la plantilla como una actualización de Resource Manager para el clúster. Esto se realiza mediante la API `New-AzureRmResourceGroupDeployment` en el módulo AzureRM PowerShell. Un comando de ejemplo sería:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager podrá detectar que se trata de una actualización de un recurso existente. Solo procesará los cambios entre la plantilla en que se basa la implementación existente y la nueva plantilla proporcionada.

## <a name="deploying-oms-using-azure-powershell"></a>Implementación de OMS mediante Azure PowerShell

También puede implementar el recurso de Log Analytics para OMS a través de PowerShell. Esto se logra mediante el uso del comando `New-AzureRmOperationalInsightsWorkspace`. Para ello, asegúrese de que ha instalado [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Use este script para crear una nueva área de trabajo de Log Analytics para OMS y agregue la solución Service Fabric a esta: 

```ps

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<OMS Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Login-AzureRmAccount
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

Una vez completado el proceso, si el clúster es un clúster de Windows, siga los pasos descritos en la sección anterior para enlazar Log Analytics para OMS a la cuenta de almacenamiento adecuada.

También puede agregar otras soluciones o realizar otras modificaciones en el área de trabajo de OMS mediante PowerShell. Para más información, consulte [Administración de Log Analytics mediante PowerShell](../log-analytics/log-analytics-powershell-workspace-configuration.md)

## <a name="next-steps"></a>Pasos siguientes
* [Implemente el Agente de OMS](service-fabric-diagnostics-oms-agent.md) en los nodos para recopilar los contadores de rendimiento y recopilar estadísticas de Docker y registros para los contenedores
* Familiarícese con las funciones de [búsqueda de registros y consulta](../log-analytics/log-analytics-log-searches.md) que se ofrecen como parte de Log Analytics
* [Uso del Diseñador de vistas para crear vistas personalizadas en Log Analytics](../log-analytics/log-analytics-view-designer.md)
