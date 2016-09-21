<properties
    pageTitle="Creación de un espacio de nombres de Centros de eventos con un centro de eventos y un grupo de consumidores mediante una plantilla de Azure Resource Manager | Microsoft Azure"
    description="Creación de un espacio de nombres de Centros de eventos con un centro de eventos y un grupo de consumidores mediante una plantilla de Azure Resource Manager"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/31/2016"
    ms.author="sethm;shvija"/>

# Creación de un espacio de nombres de Centros de eventos con un centro de eventos y un grupo de consumidores mediante una plantilla de Azure Resource Manager

En este artículo se muestra cómo utilizar una plantilla de Azure Resource Manager que crea un espacio de nombres de Centros de eventos con un centro de eventos y un grupo de consumidores. Aprenderá a definir los recursos que se implementan y los parámetros que se especifican cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarla para satisfacer sus necesidades.

Para más información sobre la creación de plantillas, consulte [Creación de plantillas de Azure Resource Manager][].

Para ver la plantilla completa, consulte la [plantilla de grupo de consumidores y Centros de eventos][] en GitHub.

>[AZURE.NOTE]
Para buscar las últimas plantillas, visite la galería de [Plantillas de inicio rápido de Azure][] y busque Centros de eventos.

## ¿Qué va a implementar?

Con esta plantilla, implementará un espacio de nombres de Centros de eventos con un grupo de consumidores y un centro de eventos.

[Centros de eventos](../event-hubs/event-hubs-what-is-event-hubs.md) es un servicio de procesamiento de eventos que se usa para ofrecer la entrada de telemetría y eventos en Azure a escala masiva, con una latencia baja y una alta confiabilidad.

Para ejecutar automáticamente la implementación, haga clic en el botón siguiente:

[![Implementación en Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## Parámetros

Con el Administrador de recursos de Azure, se definen los parámetros de los valores que desea especificar al implementar la plantilla. La plantilla incluye una sección denominada `Parameters` que contiene todos los valores de los parámetros. Debe definir un parámetro para los valores que variarán según el proyecto que vaya a implementar o según el entorno en el que vaya a realizar la implementación. No defina parámetros para valores que vayan a permanecer igual. Cada valor de parámetro se usa en la plantilla para definir los recursos que se implementan.

La plantilla define los parámetros siguientes.

### eventHubNamespaceName

El nombre del espacio de nombres de Centros de eventos que se creará.

```
"eventHubNamespaceName": {
"type": "string"
}
```

### eventHubName

El nombre del centro de eventos creado en el espacio de nombres de Centros de eventos.

```
"eventHubName": {
"type": "string"
}
```

### eventHubConsumerGroupName

El nombre del grupo de consumidores creado para el centro de eventos.

```
"eventHubConsumerGroupName": {
"type": "string"
}
```

### apiVersion

La versión de API de la plantilla.

```
"apiVersion": {
"type": "string"
}
```

## Recursos para implementar

Crea un espacio de nombres de tipo **Centros de eventos** con un centro de eventos y un grupo de consumidores.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## Comandos para ejecutar la implementación

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

## Pasos siguientes

Ahora que ha creado e implementado recursos con Azure Resource Manager, estos artículos le enseñarán como administrarlos:

- [Administración de recursos de Centros de eventos con el Explorador de Bus de servicio](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Creación de plantillas de Azure Resource Manager]: ../resource-group-authoring-templates.md
  [Plantillas de inicio rápido de Azure]: https://azure.microsoft.com/documentation/templates/?term=event+hubs
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [plantilla de grupo de consumidores y Centros de eventos]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/

<!---HONumber=AcomDC_0907_2016-->