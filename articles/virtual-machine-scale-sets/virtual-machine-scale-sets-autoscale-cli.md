---
title: "Escalado automático de conjuntos de escalado de máquinas virtuales con la CLI de Azure | Microsoft Docs"
description: "Creación de reglas de escalado automático de conjuntos de escalado de máquinas virtuales con la CLI de Azure 2.0"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8552f6b2723fef2c61d49a34d2d60c2a6c209a32
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Creación de reglas de escalado automático de conjuntos de escalado de máquinas virtuales con la CLI de Azure 2.0
Al crear un conjunto de escalado, puede definir el número de instancias de máquina virtual que quiere ejecutar. A medida que cambia la demanda de las aplicaciones, puede aumentar o reducir automáticamente el número de estas instancias. La posibilidad de realizar el escalado automático le permite satisfacer la demanda del cliente o responder a los cambios de rendimiento de la aplicación a lo largo del ciclo de vida de esta.

Este artículo muestra cómo crear reglas de escalado automático con la CLI de Azure 2.0 que supervisan el rendimiento de las instancias de máquina virtual del conjunto de escalado. Estas reglas de escalado automático permiten aumentar o reducir el número de instancias de máquina virtual en respuesta a estas métricas de rendimiento. Estos pasos también se pueden completar con [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) o en [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>requisitos previos
Para crear reglas de escalado automático, necesita un conjunto de escalado de máquinas virtuales ya existente. Puede crear un conjunto de escalado mediante [Azure Portal](virtual-machine-scale-sets-create-portal.md), la [CLI de Azure 2.0](virtual-machine-scale-sets-create-cli.md) o [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md).

Para facilitar la creación de las reglas de escalado automático, defina algunas de las variables para el conjunto de escalado. En el siguiente ejemplo se definen variables para el conjunto de escalado denominado *myScaleSet* del grupo de recursos *myResourceGroup* en la región *eastus*. El identificador de la suscripción se obtiene con [az account show](/cli/azure/account#az_account_show). Si tiene varias suscripciones asociadas a su cuenta, solo se devolverá la primera de ellas. Ajuste los nombres y los identificadores de la suscripción como se indica a continuación:

```azurecli
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

## <a name="define-an-autoscale-profile"></a>Definición de un perfil de escalado automático
Las reglas de escalado automático se implementan como JSON (notación de objetos JavaScript) con la CLI de Azure 2.0. El JSON completo que define e implementa las reglas de escalado automático puede encontrarse más adelante en este mismo artículo. 

El inicio del perfil de escalado automático define la capacidad mínima, máxima y predeterminada del conjunto de escalado. En el ejemplo siguiente se establece la capacidad predeterminada y la mínima para *2* instancias de máquina virtual y un máximo de *10*:

```json
{
  "name": "autoscale rules",
  "capacity": {
    "minimum": "2",
    "maximum": "10",
    "default": "2"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Creación de una regla para realizar un escalado horizontal de forma automática
Si aumenta la demanda de la aplicación, la carga de las instancias de máquina virtual del conjunto de escalado aumenta. Si este aumento de la carga es continuado, en lugar de ser algo puntual, puede configurar reglas de escalado automático para aumentar el número de instancias de máquina virtual en el conjunto de escalado. Cuando se crean estas instancias de máquina virtual y se implementan las aplicaciones, el conjunto de escalado empieza a distribuir el tráfico entre ellas mediante el equilibrador de carga. Puede controlar qué métricas se deben supervisar como, por ejemplo, la CPU o el disco, cuánto tiempo debe cumplir la carga de la aplicación un límite determinado y cuántas instancias de máquina virtual se deben agregar al conjunto de escalado.

Vamos a crear una regla que aumenta el número de instancias de máquina virtual de un conjunto de escalado cuando la carga promedio de la CPU es superior al 70 % durante un período de más de 10 minutos. Cuando se desencadena la regla, aumenta el número de instancias de máquina virtual en un 20 %. En conjuntos de escalado con un número pequeño de instancias de máquina virtual, puede establecer `type` en *ChangeCount* y aumentar `value`en *1* o *2* instancias. En conjuntos de escalado con un gran número de instancias de máquina virtual, un aumento del 10 % o 20 % de instancias puede resultar más adecuado.

Los siguientes parámetros se utilizan para esta regla:

| .         | Explicación                                                                                                         | Valor           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | La métrica de rendimiento del conjunto de escalado sobre el que realizar las acciones de supervisión y aplicación.                                                   | Porcentaje de CPU  |
| *timeGrain*       | Frecuencia de recopilación de las métricas para el análisis.                                                                   | 1 minuto        |
| *timeAggregation* | Define cómo se deben agregar las métricas recopiladas para el análisis.                                                | Media         |
| *timeWindow*      | El periodo durante el que se realiza la supervisión antes de que se comparen los valores de métricas y umbrales.                                   | 10 minutos      |
| *operator*        | El operador que se utiliza para comparar los datos de las métricas con los umbrales.                                                     | Mayor que    |
| *threshold*       | El valor que hace que la regla de escalado automático desencadene una acción.                                                      | 70%             |
| *dirección*       | Define si el conjunto de escalado debe escalarse o reducirse verticalmente.                                             | Aumento        |
| *type*            | Indica que el número de instancias de máquina virtual debe cambiarse por un porcentaje.                                 | Cambio de porcentaje  |
| *value*           | El número de instancias de máquina virtual que se deben escalar o reducir verticalmente cuando se aplica la regla.                                            | 20               |
| *cooldown*        | El periodo que hay que esperar hasta que la regla se vuelva a aplicar, para que las acciones de escalado automático tengan tiempo de surtir efecto. | 5 minutos       |

En el ejemplo siguiente se define la regla para escalar horizontalmente el número de instancias de máquina virtual. *metricResourceUri* usa las variables definidas anteriormente para el identificador de la suscripción, el nombre del grupo de recursos y el nombre del conjunto de escalado:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Creación de una regla para realizar una reducción horizontal de forma automática
La demanda de la aplicación puede reducirse por las tardes o durante los fines de semana. Si esta reducción es constante a lo largo de un período, puede configurar reglas de escalado automático para reducir el número de instancias de máquina virtual del conjunto de escalado. Esta acción de reducción horizontal permite rebajar el costo de ejecutar el conjunto de escalado ya que solo se ejecuta el número de instancias necesario para satisfacer la demanda actual.

Cree otra regla que reduzca el número de instancias de máquina virtual de un conjunto de escalado cuando la carga promedio de la CPU es inferior al 30 % durante un período de más de 10 minutos. En el ejemplo siguiente se define la regla para escalar horizontalmente el número de instancias de máquina virtual. *metricResourceUri* usa las variables definidas anteriormente para el identificador de la suscripción, el nombre del grupo de recursos y el nombre del conjunto de escalado:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Aplicación de reglas de escalado automático a un conjunto de escalado
El último paso consiste en aplicar el perfil y reglas de escalado automático al conjunto de escalado. Posteriormente, la escala se puede escalar o reducir horizontalmente en función de la demanda de la aplicación. Aplique el perfil de escalado automático con [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) como se indica a continuación. El JSON completo utiliza el perfil y las reglas que han indicado en las secciones anteriores.

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "autoscale by percentage based on CPU usage",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Supervisión del número de instancias de un conjunto de escalado
Para ver el número y estado de las instancias de máquina virtual, use [Get-AzureRmVmssVM](/cli/azure/vmss#az_vmss_list_instances) para ver una lista de instancias del conjunto de escalado. El estado indica si la instancia de la máquina virtual se aprovisiona a medida que el conjunto de escalado se escala horizontalmente de forma automática o se desaprovisiona a medida que la escala se reduce horizontalmente de forma automática. En el ejemplo siguiente se ve el estado de la instancia de la máquina virtual del conjunto de escalado denominado *myScaleSet* en el grupo de recursos denominado *myResourceGroup*:

```azurecli
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```


## <a name="autoscale-based-on-a-schedule"></a>Escalado automático según una programación
En los ejemplos anteriores se escaló o redujo horizontalmente de forma automática un conjunto de escalado con métricas de host básicas como el uso de la CPU. También se pueden crear reglas de escalado automático basadas en programación. Estas reglas basadas en programación le permiten escalar horizontalmente de forma automática el número de instancias de máquina virtual anticipándose a un aumento de la demanda de la aplicación como, por ejemplo, durante las horas punta del trabajo para, a continuación, reducir horizontalmente de forma automática el número de instancias a la vez si anticipa una menor demanda como durante los fines de semana.

Para utilizar reglas de escalado automático basadas en programación, cree un perfil de JSON que defina el número de instancias de máquina virtual que se ejecutan en una ventana temporal de inicio y final fijos. En el ejemplo siguiente se define una regla para realizar un escalado horizontal en *10* instancias en las *9* A.M. todos los días laborables (de lunes al viernes).

```json
{
  "name": "Scale out during each work day",
  "capacity": {
      "minimum": "10",
      "maximum": "10",
      "default": "10"
  },
  "rules": [],
  "recurrence": {
      "frequency": "Week",
      "schedule": {
          "timeZone": "Pacific Standard Time",
          "days": [
              "Monday",
              "Tuesday",
              "Wednesday",
              "Thursday",
              "Friday"
          ],
          "hours": [
              9
          ],
          "minutes": [
              0
          ]
      }
  }
}
```

Para que la reducción horizontal se realice durante la noche, cree otra regla que especifique un número inferior de instancias de máquina virtual y una hora de inicio apropiado.

El siguiente ejemplo completo define las reglas para realizar el escalado horizontal, luego la reducción horizontal y, luego, se aplica el perfil de escalado automático con [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create). En este ejemplo se sobrescriben las reglas de escalado automático basadas en métricas creadas en los ejemplos anteriores. *metricResourceUri* usa las variables definidas anteriormente para el identificador de la suscripción, el nombre del grupo de recursos y el nombre del conjunto de escalado:

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "Scale out during each work day",
          "capacity": {
            "minimum": "10",
            "maximum": "10",
            "default": "10"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                9
              ],
              "minutes": [
                0
              ]
            }
          }
        },
        {
          "name": "Scale in during the evening",
          "capacity": {
            "minimum": "3",
            "maximum": "3",
            "default": "3"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                18
              ],
              "minutes": [
                0
              ]
            }
          }
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="next-steps"></a>pasos siguientes
En este artículo, ha aprendido a utilizar reglas de escalado automático para escalar horizontalmente y aumentar o reducir el *número* de instancias de máquina virtual del conjunto de escalado. También puede escalar verticalmente para aumentar o reducir el *tamaño* de la instancia de máquina virtual. Para más información, consulte [Escalado automático vertical con conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para más información acerca de cómo administrar las instancias de máquina virtual, consulte [Manage virtual machine scale sets with Azure PowerShell](virtual-machine-scale-sets-windows-manage.md) (Administración de conjuntos de escalado de máquinas virtuales con Azure PowerShell).

Para más información acerca de cómo generar alertas cuando la regla de escalado automático se desencadena, consulte [Uso de acciones de escalado automático para enviar notificaciones de alerta por correo electrónico y Webhook en Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). También puede consultar [Llamada a un webhook cuando se activan alertas del registro de actividades de Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
