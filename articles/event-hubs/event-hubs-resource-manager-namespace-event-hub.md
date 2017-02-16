---
title: "Creación de un grupo de consumidores del espacio de nombres de Azure Event Hubs mediante una plantilla | Microsoft Docs"
description: "Creación de un espacio de nombres de Centros de eventos con un centro de eventos y un grupo de consumidores mediante una plantilla de Azure Resource Manager"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/21/2016
ms.author: sethm;shvija
translationtype: Human Translation
ms.sourcegitcommit: ca66a344ea855f561ead082091c6941540b1839d
ms.openlocfilehash: 42ee0c02cfd419286b223738a539089570376929


---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Creación de un espacio de nombres de Centros de eventos con un centro de eventos y un grupo de consumidores mediante una plantilla de Azure Resource Manager
En este artículo se muestra cómo utilizar una plantilla de Azure Resource Manager que crea un espacio de nombres de Centros de eventos con un centro de eventos y un grupo de consumidores. Aprenderá a definir los recursos que se implementan y los parámetros que se especifican cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarla para satisfacer sus necesidades.

Para más información sobre la creación de plantillas, consulte [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Creación de plantillas de Azure Resource Manager).

Para ver la plantilla completa, consulte la [plantilla de grupos de consumidores y un centro de eventos][Event Hub and consumer group template] en GitHub.

> [!NOTE]
> Para buscar las plantillas más recientes, visite la galería de [Plantillas de inicio rápido de Azure][Azure Quickstart Templates] y busque Event Hubs.
> 
> 

## <a name="what-will-you-deploy"></a>¿Qué va a implementar?
Con esta plantilla, implementará un espacio de nombres de Centros de eventos con un grupo de consumidores y un centro de eventos.

[Centros de eventos](event-hubs-what-is-event-hubs.md) es un servicio de procesamiento de eventos que se usa para ofrecer la entrada de telemetría y eventos en Azure a escala masiva, con una latencia baja y una alta confiabilidad.

Para ejecutar automáticamente la implementación, haga clic en el botón siguiente:

[![Implementación en Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Parámetros
Con el Administrador de recursos de Azure, se definen los parámetros de los valores que desea especificar al implementar la plantilla. La plantilla incluye una sección denominada `Parameters` que contiene todos los valores de los parámetros. Debe definir un parámetro para los valores que variarán según el proyecto que vaya a implementar o según el entorno en el que vaya a realizar la implementación. No defina parámetros para valores que vayan a permanecer igual. Cada valor de parámetro de la plantilla define los recursos que se implementan.

La plantilla define los parámetros siguientes.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName
El nombre del espacio de nombres de Centros de eventos que se creará.

```json
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName
El nombre del centro de eventos creado en el espacio de nombres de Centros de eventos.

```json
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName
El nombre del grupo de consumidores creado para el centro de eventos.

```json
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion
La versión de API de la plantilla.

```json
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Recursos para implementar
Crea un espacio de nombres de tipo **Centros de eventos**con un centro de eventos y un grupo de consumidores.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/namespaces",
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

## <a name="commands-to-run-deployment"></a>Comandos para ejecutar la implementación
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI
```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event Hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/



<!--HONumber=Jan17_HO4-->


