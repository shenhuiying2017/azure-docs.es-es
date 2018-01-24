---
title: "Información acerca de la configuración de escalado automático | Microsoft Docs"
description: "Un análisis detallado de la configuración de escalado automático y de cómo funciona."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ce2930aa-fc41-4b81-b0cb-e7ea922467e1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: ancav
ms.openlocfilehash: cff2be1818417a19f36da08d8c2eaa227bb945ec
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="understand-autoscale-settings"></a>Información acerca de la configuración de escalado automático
La configuración de escalado automático le permite asegurarse de que tiene la cantidad adecuada de recursos en ejecución para controlar las fluctuaciones de carga de la aplicación. Puede configurar los valores de escalado automático para que este se desencadene en función de métricas que indican carga o rendimiento, o para que se desencadene en una fecha y hora programadas. En este artículo se proporciona una visión detallada de la estructura de una configuración de escalado automático. El artículo comienza por la descripción del esquema y las propiedades de una configuración. A continuación, recorre los diferentes tipos de perfil que se pueden configurar y, finalmente, analiza cómo el escalado automático valora qué perfil debe ejecutar en un momento dado.

## <a name="autoscale-setting-schema"></a>Esquema de la configuración de escalado automático
Para ilustrar este esquema, se utiliza la siguiente configuración de escalado automático. Es importante tener en cuenta que esta configuración de escalado automático tiene:
- Un perfil 
- Dos reglas de métricas para este perfil, una para el escalado horizontal y otra para la reducción horizontal.
- La regla de escalado horizontal se desencadena cuando la métrica de porcentaje de CPU promedio del conjunto de escalado de la máquina virtual es superior al 85 % durante los últimos 10 minutos.
- La regla de reducción horizontal se desencadena si la métrica del porcentaje de CPU promedio del conjunto de escalado de la máquina virtual es inferior al 60 % durante el último minuto.

> [!NOTE]
> Una configuración puede tener varios perfiles. Vaya a la sección [Perfiles](#autoscale-profiles) para más información.
> Un perfil puede tener también varias reglas de escalabilidad horizontal y de reducción horizontal. Vaya a la [sección de evaluación](#autoscale-evaluation) para ver cómo se evalúan

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
    {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| Sección | Nombre del elemento | DESCRIPCIÓN |
| --- | --- | --- |
| Configuración | ID | Identificador de recurso de la configuración de escalado automático. La configuración de escalado automático es un recurso de Azure Resource Manager. |
| Configuración | Nombre | Nombre de la configuración de escalado automático. |
| Configuración | location | Ubicación de la configuración de escalado automático. Esta ubicación puede ser diferente de la ubicación de los recursos que se van a escalar. |
| propiedades | targetResourceUri | Identificador de recurso del recurso que se va a escalar. Solo puede tener una configuración de escalado automático por recurso. |
| propiedades | perfiles | Una configuración de escalado automático se compone de uno o varios perfiles. Cada vez que se ejecuta el motor de escalado automático, ejecuta un perfil. |
| Perfil | Nombre | El nombre del perfil. Puede elegir cualquier nombre que le ayude a identificar el perfil. |
| Perfil | Capacity.maximum | La capacidad máxima permitida. Esto garantiza que el escalado automático, al ejecutar este perfil, no escalará el recurso por encima de este número. |
| Perfil | Capacity.minimum | La capacidad mínima permitida. Esto garantiza que el escalado automático, al ejecutar este perfil, no escalará el recurso por debajo de este número. |
| Perfil | Capacity.default | Si hay algún problema al leer la métrica del recurso (en este caso, la CPU de "vmss1") y la capacidad actual es inferior a la predeterminada, el escalado automático escalará horizontalmente al valor predeterminado a fin de garantizar la disponibilidad del recurso. Si la capacidad actual ya es mayor que la predeterminada, el escalado automático no reducirá horizontalmente. |
| Perfil | reglas | El escalado automático permite escalar automáticamente entre las capacidades máxima y mínima mediante las reglas del perfil. Puede tener varias reglas en un perfil. El escenario básico consiste en tener dos reglas, una para determinar cuándo escalar horizontalmente y la otra para determinar cuándo reducir horizontalmente. |
| Regla | metricTrigger | Define la condición de métrica de la regla. |
| metricTrigger | metricName | El nombre de la métrica. |
| metricTrigger |  metricResourceUri | El identificador de recurso del recurso que emite esta métrica. En la mayoría de los casos, es el mismo que el recurso que se va a escalar. En algunos casos puede ser diferente, por ejemplo, puede escalar un conjunto de escalado de máquinas virtuales según el número de mensajes de una cola de almacenamiento. |
| metricTrigger | timeGrain | La duración del muestreo de métricas. Por ejemplo, TimeGrain = "PT1M" significa que las métricas se deberían agregar cada minuto mediante el método de agregación especificado en "statistic". |
| metricTrigger | statistic | El método de agregación del período timeGrain. Por ejemplo, statistic = "Average" y timeGrain = "PT1M" significa que las métricas se deberían agregar cada minuto tomando la media. Esta propiedad determina cómo se muestrea la métrica. |
| metricTrigger | timeWindow | La cantidad de tiempo necesario para recuperar las métricas. Por ejemplo, timeWindow = “PT10M” significa que, cada vez que se ejecuta el escalado automático, este consulta las métricas de los últimos 10 minutos. timeWindow permite que las métricas se normalicen y evita que reaccionen a picos transitorios. |
| metricTrigger | timeAggregation | Método de agregación que se usa para agregar métricas muestreadas. Por ejemplo, TimeAggregation = "Average" agregará las métricas muestreadas teniendo en cuenta la media. En el caso anterior toma las diez muestras de 1 minuto y hace la media. |
| Regla | scaleAction | La acción que se realizará cuando se desencadene la propiedad metricTrigger de la regla. |
| scaleAction | dirección | "Aumentar" para el escalado horizontal, "Reducir" para la reducción horizontal|
| scaleAction | value | El aumento o reducción de la capacidad del recurso |
| scaleAction | cooldown | La cantidad de tiempo que debe transcurrir después de realizar una operación de escalado antes de poder iniciar otra. Por ejemplo, si cooldown = "PT10M", y acaba de producirse una operación de escalado, el escalado automático no volverá a realizar ningún escalado hasta que pasen otros 10 minutos. Cooldown permite que las métricas se estabilicen después de la adición o eliminación de instancias. |

## <a name="autoscale-profiles"></a>Perfiles de escalado automático

Hay tres tipos de perfiles de escalado automático:

1. **Perfil normal:** el perfil más común. Si no necesita escalar el recurso de manera diferente según el día de la semana, o según un día concreto, solo tendrá que configurar un perfil normal en la configuración de escalado automático. Posteriormente, este perfil se puede configurar con reglas de métricas que determinen cuando realizar un escalado horizontal y cuándo una reducción horizontal. Solo debe definir un perfil normal.

    El perfil de ejemplo que se usó anteriormente en este artículo es un ejemplo de perfil normal. También es posible establecer un perfil para escalar a un número de instancias estáticas para el recurso.

2. **Perfil de fecha fija:** con el perfil normal definido, supongamos que tiene un evento importante que tendrá lugar el 26 de diciembre de 2017 (PST) y desea que las capacidades mínimas y máximas del recurso sean diferentes ese día, pero quiere realizar el escalado según las mismas métricas. En este caso, deberá agregar un perfil de fecha fija a la lista de perfiles de la configuración. El perfil se configura solo para que se ejecute en el día del evento. En los demás días, se ejecutará el perfil normal.

    ``` JSON
    "profiles": [{
    "name": " regularProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    },
    {
    ...
    }]
    },
    {
    "name": "eventProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    }, {
    ...
    }],
    "fixedDate": {
        "timeZone": "Pacific Standard Time",
               "start": "2017-12-26T00:00:00",
               "end": "2017-12-26T23:59:00"
    }}
    ]
    ```
    
3. **Perfil de periodicidad:** este tipo de perfil le permite garantizar que este perfil se usará siempre un día concreto de la semana. Los perfiles de periodicidad solo tienen una hora de inicio, por lo que se ejecutarán hasta el inicio del siguiente perfil de periodicidad o perfil de fecha fija. Una configuración de escalado automático que solo incluya un perfil de periodicidad, ejecutará ese perfil incluso aunque haya un perfil normal definido en la misma configuración. Los dos ejemplos siguientes ilustran el uso de este perfil:

    **Ejemplo 1: días laborables frente a fines de semana**. Supongamos que los fines de semana quiere que su capacidad máxima sea 4 pero que en los días laborables la capacidad máxima sea 10 ya que espera más carga. En este caso, la configuración contendría dos perfiles de periodicidad, uno para ejecutarse durante los fines de semana y el otro los días laborables.
    La configuración sería parecida a esta:

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    Si examina la configuración anterior, puede observar que cada perfil de periodicidad tiene una programación, y que esta determina cuándo empieza a ejecutarse el perfil. El perfil deja de ejecutarse cuando llega la hora de ejecutar otro perfil.

    Por ejemplo, en la configuración anterior, "weekdayProfile" está establecido para que empiece a ejecutarse los lunes a las 12 a.m., lo que significa que este perfil empezará a ejecutarse el lunes a las 12 a.m. Se continuará ejecutando hasta el sábado a las 12 a.m., cuando se ha programado que "weekendProfile" empiece a ejecutarse.

    **Ejemplo 2: horario comercial** veamos otro ejemplo: puede que desee que el umbral de métrica sea "x" durante el horario comercial, de 9 a.m. a 5 p.m. y, después, entre las 5 p.m. y las 9 a.m. del día siguiente desea que el umbral de métrica sea "y".
    La configuración sería parecida a esta:
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
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
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    Si mira la configuración anterior, podrá ver que "businessHoursProfile" comienza a ejecutarse los lunes a las 9 a.m. y continua haciéndolo hasta las 5 p.m. que es cuando empieza a ejecutarse "nonBusinessHoursProfile". El perfil "nonBusinessHoursProfile" se ejecuta hasta las 9 a.m. del martes y, después, empieza a ejecutarse "businessHoursProfile" de nuevo. Esto se repite hasta el viernes a las 5 p.m. En ese momento empieza a ejecutarse el perfil "nonBusinessHoursProfile" de forma ininterrumpida hasta el lunes a las 9 a.m. que es cuando "businessHoursProfile" empieza a ejecutarse de nuevo.
    
> [!Note]
> La experiencia de usuario de escalado automático en Azure Portal exige horas de finalización para los perfiles de periodicidad, y empieza a ejecutar el perfil predeterminado de la configuración de escalado automático entre los perfiles de periodicidad.
    
## <a name="autoscale-evaluation"></a>Evaluación del escalado automático
Dado que la configuración de escalado automático puede incluir varios perfiles, y que cada perfil puede incluir varias reglas de métrica, es importante comprender cómo se evalúa una configuración de escalado automático. Cada vez que se ejecuta el trabajo de escalado automático, empieza por seleccionar el perfil que es aplicable. Después de ello, el escalado automático evalúa los valores mínimos y máximos, y todas las reglas de métricas del perfil, y decide si es necesaria una acción de escalado.

### <a name="which-profile-will-autoscale-pick"></a>¿Qué perfil seleccionará el escalado automático?
- El escalado automático busca en primer lugar algún perfil de fecha fija que esté configurado para ejecutarse ahora y, si lo hay, lo ejecuta. Si hay varios perfiles de fecha fija que se supone que se pueden ejecutar, el escalado automático seleccionará el primero de ellos.
- Si no hay ningún perfil de fecha fija, el escalado automático buscará perfiles de periodicidad y, si los encuentra, los ejecutará.
- Si no hay perfiles de fecha fija ni de periodicidad, el escalado automático ejecutará el perfil normal.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>¿Cómo evalúa el escalado automático varias reglas?

Una vez que el escalado automático determina qué perfil se debe ejecutar, empieza a evaluar todas las reglas de escalado horizontal del perfil (reglas con la dirección = "Aumentar").
- Si se desencadenan una o varias reglas de escalado horizontal, el escalado automático calcula la nueva capacidad determinada por la propiedad scaleAction de cada una de esas reglas. A continuación, realizará el escalado horizontal hasta alcanzar el máximo de esas capacidades para asegurar la disponibilidad del servicio.
- Por ejemplo: suponga que hay un conjunto de escalado de máquinas virtuales con una capacidad actual de 10, y que hay dos reglas de escalado horizontal: una que aumenta la capacidad un 10 % y otra que aumenta la capacidad en 3. La primera regla daría como resultado una nueva capacidad de 11 y la segunda regla generaría una capacidad de 13. Para garantizar la disponibilidad del servicio, el escalado automático elegirá la acción que tenga como resultado la capacidad máxima, por lo que, en este caso, elegirá la segunda regla.

Si no se desencadena ninguna regla de escalado horizontal, el escalado automático evaluará todas las reglas de reducción horizontal (reglas con dirección = "Reducir"). El escalado automático solo realizará una acción de reducción horizontal si se desencadenan todas las reglas de reducción horizontal.
- El escalado automático calcula la nueva capacidad determinada por la propiedad scaleAction de cada una de esas reglas. A continuación, elige la acción de escalado que da como resultado el valor máximo de esas capacidades para garantizar la disponibilidad del servicio.
- Por ejemplo: suponga que hay un conjunto de escalado de máquinas virtuales con una capacidad actual de 10, y que hay dos reglas de reducción horizontal: una que reduce la capacidad un 50 % y otra que reduce la capacidad en 3. La primera regla daría como resultado una nueva capacidad de 5 y la segunda regla generaría una capacidad de 7. Para garantizar la disponibilidad del servicio, el escalado automático elegirá la acción que tenga como resultado la capacidad máxima, por lo que, en este caso, elegirá la segunda regla.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre el escalado automático, consulte los siguientes recursos:

* [Introducción a los registros de escalado automático](monitoring-overview-autoscale.md)
* [Métricas comunes de escalado automático de Azure Monitor](insights-autoscale-common-metrics.md)
* [Procedimientos recomendados de escalado automático en Azure Monitor](insights-autoscale-best-practices.md)
* [Uso de acciones de escalado automático para enviar notificaciones de alerta por correo electrónico y Webhook en Azure Insights](insights-autoscale-to-webhook-email.md)
* [API de REST de escalado automático](https://msdn.microsoft.com/library/dn931953.aspx)
