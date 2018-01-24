---
title: "Creación del espacio de nombres Messaging de Service Bus con una plantilla de Azure Resource Manager | Microsoft Docs"
description: Utilice una plantilla de Azure Resource Manager para crear un espacio de nombres Messaging de Service Bus.
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm;shvija
ms.openlocfilehash: f184cc9418e4af95423c0ede65bca312dfca7393
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Creación de un espacio de nombres de Service Bus mediante una plantilla de Azure Resource Manager

En este artículo se describe cómo utilizar una plantilla de Azure Resource Manager que crea un espacio de nombres de Service Bus del tipo **Mensajería** con una SKU de nivel estándar. El artículo también define los parámetros que se especifican para la ejecución de la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarla para satisfacer sus necesidades.

Para más información sobre la creación de plantillas, consulte [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Creación de plantillas de Azure Resource Manager).

Para ver la plantilla completa, consulte la [plantilla de espacio de nombres de Service Bus][Service Bus namespace template] en GitHub.

> [!NOTE]
> Las siguientes plantillas de Azure Resource Manager están disponibles para su descarga e implementación. 
> 
> * [Creación de un espacio de nombres de Service Bus con cola](service-bus-resource-manager-namespace-queue.md)
> * [Creación de un espacio de nombres de Service Bus con un tema y una suscripción](service-bus-resource-manager-namespace-topic.md)
> * [Creación de un espacio de nombres de Service Bus con regla de autorización y cola](service-bus-resource-manager-namespace-auth-rule.md)
> * [Create a Service Bus namespace with topic, subscription, and rule](service-bus-resource-manager-namespace-topic-with-rule.md) (Creación de un espacio de nombres de Service Bus con tema, suscripción y regla)
> 
> Para buscar las últimas plantillas, visite la galería [Plantillas de inicio rápido de Azure][Azure Quickstart Templates] y busque "Service Bus".
> 
> 

## <a name="what-will-you-deploy"></a>¿Qué va a implementar?

Con esta plantilla, implementará un espacio de nombres de Service Bus con una SKU de nivel [estándar o premium](https://azure.microsoft.com/pricing/details/service-bus/).

Para ejecutar automáticamente la implementación, haga clic en el botón siguiente:

[![Implementación en Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>Parámetros

Con el Administrador de recursos de Azure, se definen los parámetros de los valores que desea especificar al implementar la plantilla. La plantilla incluye una sección denominada `Parameters` que contiene todos los valores de los parámetros. Debe definir un parámetro para esos valores que variarán según el proyecto que vaya a implementar o según el entorno en el que vaya a realizar la implementación. No defina parámetros para valores que siempre permanezcan igual. Cada valor de parámetro se usa en la plantilla para definir los recursos que se implementan.

En esta plantilla se definen los parámetros siguientes:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

El nombre del espacio de nombres de Service Bus que crear.

```json
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebussku"></a>serviceBusSKU

El nombre de la [SKU](https://azure.microsoft.com/pricing/details/service-bus/) de Service Bus que crear.

```json
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

En la plantilla se definen los valores que se permiten para este parámetro (estándar o premium). Si no se especifica ningún valor, el administrador de recursos asigna un valor predeterminado (estándar).

Para más información sobre los precios de Service Bus, consulte [Precios y facturación de Service Bus][Service Bus pricing and billing].

### <a name="servicebusapiversion"></a>serviceBusApiVersion

La versión de la API de Service Bus de la plantilla.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       } 
```

## <a name="resources-to-deploy"></a>Recursos para implementar

### <a name="service-bus-namespace"></a>Espacio de nombres de Service Bus

Crea un espacio de nombres de Service Bus estándar de tipo **Mensajería**.

```json
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "properties": {
        }
    }
]
```

## <a name="commands-to-run-deployment"></a>Comandos para ejecutar la implementación

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>pasos siguientes
Ahora que ha creado e implementado recursos con Azure Resource Manager, lea los artículos siguientes para obtener información sobre cómo administrar dichos recursos:

* [Administración de Service Bus con PowerShell](service-bus-manage-with-ps.md)
* [Administración de recursos de Service Bus con el Explorador de Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: service-bus-pricing-billing.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
