<properties
    pageTitle="Creación de un espacio de nombres de Bus de servicio con una plantilla de Azure Resource Manager | Microsoft Azure"
    description="Utilice la plantilla de Azure Resource Manager para crear un espacio de nombres de Bus de servicio"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/04/2016"
    ms.author="sethm;shvija"/>


# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Creación de un espacio de nombres del Bus de servicio mediante una plantilla de Azure Resource Manager

En este artículo se describe cómo utilizar una plantilla de Azure Resource Manager que crea un espacio de nombres de Service Bus del tipo **Mensajería** con una SKU de nivel básico o estándar. El artículo también define los parámetros que se especifican para la ejecución de la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarla para satisfacer sus necesidades.

Para más información sobre la creación de plantillas, consulte [Creación de plantillas de Azure Resource Manager][].

Para ver la plantilla completa, consulte la [Plantilla de espacio de nombres del Bus de servicio][] en GitHub.

>[AZURE.NOTE] Las siguientes plantillas de Azure Resource Manager están disponibles para su descarga e implementación. 
>
>-    [Creación de un espacio de nombres de Event Hubs con un centro de eventos y un grupo de consumidores](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>-    [Creación de un espacio de nombres de Bus de servicio con cola](service-bus-resource-manager-namespace-queue.md)
>-    [Creación de un espacio de nombres de Bus de servicio con un tema y una suscripción](service-bus-resource-manager-namespace-topic.md)
>-    [Creación de un espacio de nombres de Bus de servicio con regla de autorización y cola](service-bus-resource-manager-namespace-auth-rule.md)
>
>Para buscar las últimas plantillas, visite la galería de [Plantillas de inicio rápido de Azure][] y busque Bus de servicio.

## <a name="what-will-you-deploy?"></a>¿Qué va a implementar?

Con esta plantilla, implementará un espacio de nombres de Service Bus con una SKU de nivel [básico, estándar o premium](https://azure.microsoft.com/pricing/details/service-bus/).

Para ejecutar automáticamente la implementación, haga clic en el botón siguiente:

[![Implementación en Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>Parámetros

Con el Administrador de recursos de Azure, se definen los parámetros de los valores que desea especificar al implementar la plantilla. La plantilla incluye una sección denominada `Parameters` que contiene todos los valores de los parámetros. Debe definir un parámetro para los valores que variarán según el proyecto que vaya a implementar o según el entorno en el que vaya a realizar la implementación. No defina parámetros para valores que vayan a permanecer igual. Cada valor de parámetro se usa en la plantilla para definir los recursos que se implementan.

Esta plantilla define los parámetros siguientes.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

El nombre del espacio de nombres de Bus de servicio que crear.

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebussku"></a>serviceBusSKU

El nombre de la [SKU](https://azure.microsoft.com/pricing/details/service-bus/) de Bus de servicio que crear.

```
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Basic", 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

La plantilla define los valores permitidos para este parámetro (Básico, Estándar o Premium) y asigna un valor predeterminado (Estándar) si no se especifica ningún valor.

Para obtener más información sobre los precios de Service Bus, vea [Precios y facturación de Service Bus][].

### <a name="servicebusapiversion"></a>serviceBusApiVersion

La versión de la API de Bus de servicio de la plantilla.

```
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2015-08-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       } 
```

## <a name="resources-to-deploy"></a>Recursos para implementar

### <a name="service-bus-namespace"></a>Espacio de nombres de Bus de servicio

Crea un espacio de nombres de Bus de servicio estándar de tipo **Mensajería**.

```
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

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha creado e implementado recursos con Azure Resource Manager, lea los artículos siguientes para obtener información sobre cómo administrar dichos recursos:

- [Administración de Service Bus con PowerShell](service-bus-powershell-how-to-provision.md)
- [Administración de recursos de Bus de servicio con el Explorador de Bus de servicio](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Creación de plantillas de Azure Resource Manager]: ../resource-group-authoring-templates.md
  [Plantilla de espacio de nombres del Bus de servicio]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
  [Plantillas de inicio rápido de Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Precios y facturación de Bus de servicio]: https://azure.microsoft.com/documentation/articles/service-bus-pricing-billing/
  [Uso de Azure PowerShell con Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Uso de la CLI de Azure para Mac, Linux y Windows mediante la administración de recursos de Azure]: ../xplat-cli-azure-resource-manager.md



<!--HONumber=Oct16_HO2-->


