---
title: "Creación de recursos de Azure Service Bus con las plantillas de Azure Resource Manager | Microsoft Docs"
description: "Uso de las plantillas de Azure Resource Manager para automatizar la creación de recursos de Bus de servicio"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 24f6a207-0fa4-49cf-8a58-963f9e2fd655
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/07/2017
ms.author: sethm
ms.openlocfilehash: c8142d8edfd3a527b13d655bac21acf5332f2d14
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Creación de recursos de Bus de servicio con las plantillas de Azure Resource Manager

Este artículo describe cómo crear e implementar recursos de Service Bus y Event Hubs con plantillas de Azure Resource Manager, PowerShell y el proveedor de recursos de Service Bus.

Las plantillas de Azure Resource Manager ayudan a definir los recursos que se implementarán en una solución, y a especificar parámetros y variables que permitan introducir valores de entrada para distintos entornos. La plantilla consta de JSON y expresiones que puede usar para generar valores para su implementación. Para obtener información detallada sobre cómo escribir plantillas de Azure Resource Manager y una explicación del formato de plantilla, vea [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

> [!NOTE]
> Los ejemplos de este artículo muestran cómo utilizar Azure Resource Manager para crear una entidad de mensajería (cola) y un espacio de nombres de Bus de servicio. Para obtener otros ejemplos de plantillas, visite la [Galería de plantillas de inicio rápido de Azure][Azure Quickstart Templates gallery] y busque "Service Bus".
>
>

## <a name="service-bus-resource-manager-templates"></a>Plantillas de Resource Manager para Service Bus

Estas plantillas de Service Bus y Azure Resource Manager están disponibles para su descarga e implementación. Haga clic en los siguientes vínculos para obtener más información sobre cada uno, con vínculos a las plantillas de GitHub:

* [Creación de un espacio de nombres de bus de servicio](service-bus-resource-manager-namespace.md)
* [Creación de un espacio de nombres de Bus de servicio con cola](service-bus-resource-manager-namespace-queue.md)
* [Creación de un espacio de nombres de Bus de servicio con un tema y una suscripción](service-bus-resource-manager-namespace-topic.md)
* [Creación de un espacio de nombres de Bus de servicio con regla de autorización y cola](service-bus-resource-manager-namespace-auth-rule.md)
* [Creación de un espacio de nombres de Service Bus con un tema, una suscripción y una regla](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Implementación con PowerShell

El siguiente procedimiento describe cómo usar PowerShell para implementar una plantilla de Azure Resource Manager que crea un espacio de nombres de Service Bus de nivel **Estándar** y una cola dentro de ese espacio de nombres. Este ejemplo se basa en la plantilla de [Creación de un espacio de nombres de Service Bus con cola](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue). El flujo de trabajo aproximado es el siguiente:

1. Instale PowerShell.
2. Cree la plantilla y (opcionalmente) un archivo de parámetros.
3. En PowerShell, inicie sesión en la cuenta de Azure.
4. Si no existe, cree un nuevo grupo de recursos.
5. Pruebe la implementación.
6. Si lo desea, establezca el modo de implementación.
7. Implemente la plantilla.

Para obtener información completa sobre la implementación de plantillas de Azure Resource Manager, vea [Implementación de recursos con las plantillas de Azure Resource Manager][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Instale PowerShell.

Instale Azure PowerShell siguiendo las instrucciones de [Getting started with Azure PowerShell](/powershell/azure/get-started-azureps) (Introducción a Azure PowerShell).

### <a name="create-a-template"></a>Creación de una plantilla

Clone o copie la plantilla [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) de GitHub:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusQueueName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Queue"
            }
        },
        "serviceBusApiVersion": {
            "type": "string",
            "defaultValue": "2015-08-01",
            "metadata": {
                "description": "Service Bus ApiVersion used by the template"
            }
        }
    },
    "variables": {
        "location": "[resourceGroup().location]",
        "sbVersion": "[parameters('serviceBusApiVersion')]",
        "defaultSASKeyName": "RootManageSharedAccessKey",
        "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]"
    },
    "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }],
    "outputs": {
        "NamespaceConnectionString": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
        },
        "SharedAccessPolicyPrimaryKey": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
        }
    }
}
```

### <a name="create-a-parameters-file-optional"></a>Creación de un archivo de parámetros (opcional)

Para utilizar un archivo de parámetros opcional, copie el archivo [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json). Reemplace el valor de `serviceBusNamespaceName` por el nombre del espacio de nombres de Bus de servicio que desee crear en esta implementación y sustituya el valor de `serviceBusQueueName` por el nombre de la cola que desee crear.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2015-08-01"
        }
    }
}
```

Para más información, consulte el tema [Parámetros](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Inicio de sesión en Azure y establecimiento de la suscripción de Azure

En una secuencia de comandos de PowerShell, ejecute el siguiente comando:

```powershell
Login-AzureRmAccount
```

Se le solicitará que inicie sesión en la cuenta de Azure. Después de iniciar sesión, ejecute el siguiente comando para ver sus suscripciones disponibles.

```powershell
Get-AzureRMSubscription
```

Este comando devuelve una lista de suscripciones de Azure disponibles. Seleccione una suscripción para la sesión actual mediante la ejecución del siguiente comando. Reemplace `<YourSubscriptionId>` por el GUID de la suscripción de Azure que desea usar.

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Configuración del grupo de recursos

Si no tiene un grupo de recursos existente, cree uno con el comando **New-AzureRmResourceGroup**. Proporcione el nombre del grupo de recursos y la ubicación que desee utilizar. Por ejemplo:

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Si es correcto, se muestra un resumen del nuevo grupo de recursos.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Prueba de la implementación

Valide la implementación mediante la ejecución del cmdlet `Test-AzureRmResourceGroupDeployment`. Al probar la implementación, proporcione los parámetros exactamente como lo haría al ejecutar la implementación.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Creación de la implementación

Para crear la nueva implementación, ejecute el cmdlet `New-AzureRmResourceGroupDeployment` y proporcione los parámetros necesarios cuando se le solicite. Los parámetros incluyen un nombre para la implementación, el nombre del grupo de recursos y la ruta de acceso o la dirección URL al archivo de plantilla. Si no se especifica el parámetro **Modo**, se usa el valor predeterminado **Incremental**. Para más información, vea [Implementaciones incrementales y completas](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments).

El siguiente comando le solicita los tres parámetros en la ventana de PowerShell:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Para especificar un archivo de parámetros en su lugar, use el siguiente comando.

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

También puede utilizar parámetros en línea cuando ejecute el cmdlet de implementación. El comando es el siguiente:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Para ejecutar una implementación [completa](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments), establezca el parámetro **Modo** en **Completo**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Comprobar la implementación
Si los recursos se implementan correctamente, aparecerá un resumen de la implementación en la ventana de PowerShell:

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2016 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2015-08-01

```

## <a name="next-steps"></a>Pasos siguientes
Ahora ha visto los comandos y el flujo de trabajo básico para la implementación de una plantilla de Azure Resource Manager. Para obtener información más detallada, viste los siguientes vínculos:

* [Información general sobre Azure Resource Manager][Azure Resource Manager overview]
* [Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell][Deploy resources with Azure Resource Manager templates]
* [Creación de plantillas del Administrador de recursos de Azure](../azure-resource-manager/resource-group-authoring-templates.md)

[Azure Resource Manager overview]: ../azure-resource-manager/resource-group-overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/resource-group-template-deploy.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
