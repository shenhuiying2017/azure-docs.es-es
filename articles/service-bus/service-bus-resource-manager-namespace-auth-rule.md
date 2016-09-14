<properties
    pageTitle="Creación de una regla de autorización de Bus de servicio con una plantilla de Azure Resource Manager | Microsoft Azure"
    description="Creación de una regla de autorización de Bus de servicio para un espacio de nombres y una cola mediante una plantilla de Azure Resource Manager"
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
    ms.date="07/11/2016"
    ms.author="sethm;shvija"/>

# Creación de una regla de autorización de Bus de servicio para un espacio de nombres y una cola mediante una plantilla de Azure Resource Manager

En este artículo se muestra cómo utilizar una plantilla de Azure Resource Manager que crea una [regla de autorización](service-bus-authentication-and-authorization.md#shared-access-signature-authentication) para una cola y un espacio de nombres de Bus de servicio. Aprenderá a definir los recursos que se implementan y los parámetros que se especifican cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarla para satisfacer sus necesidades.

Para más información sobre la creación de plantillas, consulte [Creación de plantillas de Azure Resource Manager][].

Para ver la plantilla completa, consulte la [Plantilla de regla de autorización de Bus de servicio][] en GitHub.

>[AZURE.NOTE] Las siguientes plantillas de Azure Resource Manager están disponibles para su descarga e implementación.
>
>-    [Create an Event Hubs namespace with an Event Hub and consumer group](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md) (Creación de un espacio de nombres de Centros de eventos con un Centro de eventos y un grupo de consumidores)
>-    [Creación de un espacio de nombres de Bus de servicio con cola](service-bus-resource-manager-namespace-queue.md)
>-    [Creación de un espacio de nombres de Bus de servicio con un tema y una suscripción](service-bus-resource-manager-namespace-topic.md)
>-    [Creación de un espacio de nombres de bus de servicio](service-bus-resource-manager-namespace.md)
>
>Para buscar las últimas plantillas, visite la galería de [Plantillas de inicio rápido de Azure][] y busque Bus de servicio.

## ¿Qué va a implementar?

Con esta plantilla, implementará una regla de autorización de Bus de servicio para una entidad de mensajería y espacio de nombres (una cola en este caso).

Esta plantilla usa la [firma de acceso compartido (SAS)](service-bus-sas-overview.md) para la autenticación. SAS permite a las aplicaciones autenticarse en el Bus de servicio mediante una clave de acceso configurada en el espacio de nombres o en la entidad de mensajería (cola o tema) al que se asocian derechos específicos. A continuación, puede usar esta clave para generar un token SAS que a su vez, los clientes pueden usar para autenticarse en el Bus de servicio.

Para ejecutar automáticamente la implementación, haga clic en el botón siguiente:

[![Implementación en Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## Parámetros

Con el Administrador de recursos de Azure, se definen los parámetros de los valores que desea especificar al implementar la plantilla. La plantilla incluye una sección denominada `Parameters` que contiene todos los valores de los parámetros. Debe definir un parámetro para los valores que variarán según el proyecto que vaya a implementar o según el entorno en el que vaya a realizar la implementación. No defina parámetros para valores que vayan a permanecer igual. Cada valor de parámetro se usa en la plantilla para definir los recursos que se implementan.

La plantilla define los parámetros siguientes.

### serviceBusNamespaceName

El nombre del espacio de nombres de Bus de servicio que crear.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### namespaceAuthorizationRuleName 

El nombre de la regla de autorización para el espacio de nombres.

```
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### serviceBusQueueName

El nombre de la cola en el espacio de nombres de Bus de servicio.

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

Crea un espacio de nombres de Bus de servicio estándar de tipo **Mensajería** y una regla de autorización de Bus de servicio para el espacio de nombres y la entidad.

```
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "StandardSku",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

## Comandos para ejecutar la implementación

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## Pasos siguientes

Ahora que ha creado e implementado recursos con Azure Resource Manager, estos artículos le enseñarán como administrarlos:

- [Administración de Bus de servicio de Azure con Automatización de Azure](service-bus-automation-manage.md)
- [Administración de Service Bus con PowerShell](service-bus-powershell-how-to-provision.md)
- [Administración de recursos de Bus de servicio con el Explorador de Bus de servicio](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)
- [Autenticación y autorización de Bus de servicio](service-bus-authentication-and-authorization.md)

  [Creación de plantillas de Azure Resource Manager]: ../resource-group-authoring-templates.md
  [Plantillas de inicio rápido de Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Plantilla de regla de autorización de Bus de servicio]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/

<!---HONumber=AcomDC_0831_2016-->