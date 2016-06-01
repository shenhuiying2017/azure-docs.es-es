<properties
    pageTitle="Creación de un espacio de nombres de Bus de servicio con una cola mediante una plantilla de Azure Resource Manager | Microsoft Azure"
    description="Creación de un espacio de nombres de Bus de servicio y una cola mediante una plantilla de Azure Resource Manager"
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
    ms.date="04/15/2016"
    ms.author="sethm;shvija"/>

# Creación de un espacio de nombres de Bus de servicio mediante una plantilla de Azure Resource Manager

En este artículo se muestra cómo utilizar una plantilla de Azure Resource Manager que crea un espacio de nombres de Bus de servicio y una cola. Aprenderá a definir los recursos que se implementan y los parámetros que se especifican cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarla para satisfacer sus necesidades.

Para obtener más información sobre la creación de plantillas, consulte [Creación de plantillas de Azure Resource Manager][].

Para ver la plantilla completa, consulte la [Plantilla de cola y espacio de nombres de Bus de servicio][] en GitHub.

>[AZURE.NOTE] Las siguientes plantillas de Azure Resource Manager están disponibles para su descarga e implementación.
>
>-    [Creación de un espacio de nombres de Bus de servicio con regla de autorización y cola](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Creación de un espacio de nombres de Bus de servicio con un grupo de consumidores y Centro de eventos](service-bus-resource-manager-namespace-event-hub.md)
>-    [Creación de un espacio de nombres de Bus de servicio con un tema y una suscripción](service-bus-resource-manager-namespace-topic.md)
>-    [Creación de un espacio de nombres de bus de servicio](service-bus-resource-manager-namespace.md)
>
>Para buscar las últimas plantillas, consulte [Plantillas de inicio rápido de Azure][] y busque Bus de servicio.

## ¿Qué va a implementar?

Con esta plantilla, implementará un espacio de nombres de Bus de servicio con una cola.

Las colas ofrecen una entrega de mensajes FIFO (PEPS, primero en entrar, primero en salir) a uno o más destinatarios de la competencia.

[Obtenga más información sobre las colas de Bus de servicio](service-bus-queues-topics-subscriptions.md).

Para ejecutar automáticamente la implementación, haga clic en el botón siguiente:

[![Implementación en Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## Parámetros

Con el Administrador de recursos de Azure, se definen los parámetros de los valores que desea especificar al implementar la plantilla. La plantilla incluye una sección denominada `Parameters` que contiene todos los valores de los parámetros. Debe definir un parámetro para los valores que variarán según el proyecto que vaya a implementar o según el entorno en el que vaya a realizar la implementación. No defina parámetros para valores que vayan a permanecer igual. Cada valor de parámetro se usa en la plantilla para definir los recursos que se implementan.

Vamos a describir cada parámetro de la plantilla.

### serviceBusNamespaceName

El nombre del espacio de nombres de Bus de servicio que crear.

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### serviceBusQueueName

El nombre de la cola creada en el espacio de nombres de Bus de servicio.

```
"serviceBusQueueName": {
"type": "string"
}
```

### serviceBusApiVersion

La versión de la API de Bus de servicio de la plantilla.

```
"serviceBusApiVersion": {
"type": "string"
}
```

## Recursos para implementar

Crea un espacio de nombres de Bus de servicio estándar de tipo **Mensajería** con una cola.

```
"resources ": [{
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
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

## Comandos para ejecutar la implementación

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## Pasos siguientes

Ahora que ha creado e implementado recursos con Azure Resource Manager, estos artículos le enseñarán como administrarlos:

- [Administración de Bus de servicio de Azure con Automatización de Azure](service-bus-automation-manage.md)
- [Administración de Service Bus con PowerShell](service-bus-powershell-how-to-provision.md)
- [Administración de recursos de Bus de servicio con el Explorador de Bus de servicio](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Creación de plantillas de Azure Resource Manager]: ../resource-group-authoring-templates.md
  [Plantilla de cola y espacio de nombres de Bus de servicio]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
  [Plantillas de inicio rápido de Azure]: https://azure.microsoft.com/documentation/templates/
  [Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0518_2016-->