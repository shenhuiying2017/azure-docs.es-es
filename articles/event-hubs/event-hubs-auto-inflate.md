---
title: "Escalado vertical y automático de las unidades de procesamiento de Azure Event Hubs | Microsoft Docs"
description: "Habilite el inflado automático en un espacio de nombres para escalar verticalmente y de gorma automática unidades de procesamiento."
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2017
ms.author: shvija;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: b085091ea7bfd601efb0eee84144ddd091422d6e
ms.contentlocale: es-es
ms.lasthandoff: 06/13/2017


---

# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Escalado vertical y automático de las unidades de procesamiento de Azure Event Hubs

## <a name="overview"></a>Información general

Azure Event Hubs es una plataforma de streaming de datos muy escalable. Por lo tanto, los clientes de Event Hubs suelen aumentar su uso después de adoptar el servicio. Para ello, hay que incrementar las unidades de rendimiento predeterminadas (TU) con el objetivo de escalar Event Hubs y controlar velocidades de transferencia más elevadas. La característica de *inflado automático* de Event Hubs escala verticalmente y de forma automática el número de TU para responder a las necesidades de uso. Al aumentar las TU, se evitan escenarios de limitación en los que nos encontramos con:

* Velocidades de entrada de datos que superan las TU establecidas
* Velocidades de solicitud de salida de datos que superan las TU establecidas

## <a name="how-auto-inflate-works"></a>Funcionamiento del inflado automático

El tráfico de Event Hubs lo controlan las unidades de procesamiento. Una sola TU permite una entrada de 1 MB/s y una salida que duplica esas cifras. Event Hubs estándar se puede configurar con un número de unidades de procesamiento del 1 a 20. El inflado automático permite empezar poco a poco con las unidades de procesamiento mínimas que se requieran. Después, la característica escala automáticamente la cantidad hasta el límite máximo de unidades de procesamiento que necesite, según el aumento del tráfico. El inflado automático proporciona las siguientes ventajas:

- Un mecanismo de escalado eficaz para empezar poco a poco y escalar verticalmente a medida que aumente el tráfico.
- Escalado automático hasta el límite superior especificado sin problemas de limitación.
- Más control sobre el escalado, ya que se puede controlar el momento y la cantidad que se escala.

## <a name="enable-auto-inflate-on-a-namespace"></a>Habilitación del inflado automático en un espacio de nombres

Puede habilitar o deshabilitar aumentar el inflado automático en un espacio de nombres utilizando cualquiera de los métodos siguientes:

1. [Azure Portal](https://portal.azure.com)
2. Una plantilla de Azure Resource Manager

### <a name="enable-auto-inflate-through-the-portal"></a>Habilitación del inflado automático mediante Azure Portal

Puede habilitar la característica de inflado automático en un espacio de nombres al crear un espacio de nombres de Event Hubs:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Con esta opción habilitada, puede empezar poco a poco con las unidades de procesamiento y escalarlas verticalmente a medida que sus necesidades de uso sean más exigentes. El límite superior del inflado no afecta al precio, que depende del número de TU utilizado por hora.

También puede habilitar el inflado automático mediante la opción **Escalar** de la hoja de configuración del portal:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Habilitación del inflado automático mediante una plantilla de Azure Resource Manager

Puede habilitar el inflado automático durante la implementación de una plantilla de Azure Resource Manager. Por ejemplo, establezca la propiedad `isAutoInflateEnabled` en **True**, y `maximumThroughputUnits` en 10.

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Para ver la plantilla completa, consulte la plantilla [Create Event Hubs namespace and enable inflate](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) (Creación de un espacio de nombres de Event Hubs y habilitación del inflado) en GitHub.

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Información general de Event Hubs](event-hubs-what-is-event-hubs.md)
* [Creación de un centro de eventos](event-hubs-create.md)

