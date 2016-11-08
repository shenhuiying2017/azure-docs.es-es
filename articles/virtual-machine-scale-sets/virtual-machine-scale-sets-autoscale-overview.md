---
title: Escalado automático y conjuntos de escalas de máquinas virtuales | Microsoft Docs
description: Obtenga información sobre el uso de los diagnósticos y los recursos de escalado automático para escalar automáticamente las máquinas virtuales de un conjunto de escalas.
services: virtual-machine-scale-sets
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu

---
# <a name="automatic-scaling-and-virtual-machine-scale-sets"></a>Escalado automático y conjuntos de escalas de máquinas virtuales
El escalado automático de máquinas virtuales en un conjunto de escalado consiste en la creación o eliminación de máquinas del conjunto según sea necesario para cumplir los requisitos de rendimiento. A medida que crece el volumen de trabajo, una aplicación puede requerir recursos adicionales para poder realizar sus tareas de manera eficaz.

El escalado automático es un proceso automatizado que ayuda a reducir la sobrecarga de administración. Al reducir la sobrecarga, no es necesario supervisar el rendimiento del sistema continuamente o decidir cómo administrar los recursos. El escalado es un proceso flexible. A medida que aumenta la carga pueden agregarse más recursos, pero, al reducirse la demanda, se pueden quitar recursos para minimizar los costos y mantener los niveles de rendimiento.

Configure el escalado automático en un conjunto de escalado mediante una plantilla de Azure Resource Manager, Azure PowerShell o la CLI de Azure.

## <a name="set-up-scaling-by-using-resource-manager-templates"></a>Configuración del escalado mediante plantillas de Resource Manager
En lugar de implementar y administrar cada recurso de la aplicación por separado, utilice una plantilla que implemente todos los recursos en una operación única y coordinada. En la plantilla, se definen los recursos de la aplicación y se especifican los parámetros de implementación para diferentes entornos. La plantilla consta de JSON y expresiones que puede usar para generar valores para su implementación. Para más información, eche un vistazo a [Creación de plantillas de Azure Resource Manager](../resource-group-authoring-templates.md).

En la plantilla, especifique el elemento de la capacidad:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

La capacidad identifica el número de máquinas virtuales en el conjunto. Puede cambiar manualmente la capacidad mediante la implementación de una plantilla con un valor diferente. Si implementa una plantilla solo para cambiar la capacidad, incluya solo el elemento SKU con la capacidad actualizada.

Cambiar automáticamente la capacidad de la escala establecida mediante la combinación del recurso autoscaleSettings y la extensión de diagnóstico.

### <a name="configure-the-azure-diagnostics-extension"></a>Configuración de la extensión Diagnósticos de Azure
El ajuste automático de escalado solo es posible si la colección de métricas es correcta en cada máquina virtual del conjunto de escalado. La extensión Diagnósticos de Azure proporciona las funcionalidades de supervisión y diagnóstico que satisfacen las necesidades de la colección de métricas del recurso de escalado automático. Puede instalar la extensión como parte de la plantilla de Resource Manager.

Este ejemplo muestra las variables que se utilizan en la plantilla para configurar la extensión de diagnósticos:

    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
    "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Thread Count\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

Cuando se implementa la plantilla, se proporcionan parámetros. En este ejemplo, el nombre de la cuenta de almacenamiento donde se guardan los datos y el nombre del conjunto de escalado desde el que se recopilan los datos. También en este ejemplo de Windows Server, se recopila solo el contador de rendimiento llamado Número de subprocesos. Todos los contadores disponibles en Windows o Linux se pueden usar para recopilar información de diagnóstico y se pueden incluir en la configuración de la extensión.

En este ejemplo se muestra la definición de la extensión en la plantilla:

    "extensionProfile": {
      "extensions": [
        {
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
              "storageAccount": "[variables('diagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
              "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
              "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
              "storageAccountEndPoint": "https://core.windows.net"
            }
          }
        }
      ]
    }

Cuando se ejecuta la extensión de diagnósticos, los datos se recopilan en una tabla que se encuentra en la cuenta de almacenamiento que haya especificado. En la tabla WADPerformanceCounters encontrará los datos recopilados:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### <a name="configure-the-autoscalesettings-resource"></a>Configuración del recurso autoScaleSettings
El recurso autoscaleSettings usa la información de la extensión de diagnóstico para decidir si aumentar o disminuir el número de máquinas virtuales en el conjunto de escalado.

En este ejemplo se muestra la configuración del recurso en la plantilla:

    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 650
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
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 550
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
        ],
        "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
      }
    }

En el ejemplo anterior, se crearon dos reglas para definir las acciones de escalado automático. La primera regla define la acción de escalado horizontal y la segunda, la acción de reducción horizontal. Estos valores se proporcionan en las reglas:

* **metricName** : es el mismo valor que el contador de rendimiento que definimos en la variable wadperfcounter para la extensión de diagnóstico. En el ejemplo anterior, se utilizó el contador Número de subprocesos.  
* **metricResourceUri** : este valor es el identificador de recursos del conjunto de escalado de máquinas virtuales. Este identificador contiene el nombre del grupo de recursos, el nombre del proveedor de recursos y el nombre del conjunto de escalado que se va a escalar.
* **timeGrain** : este valor es la granularidad de las métricas que se recopilan. En el ejemplo anterior, los datos se recopilaban en intervalos de un minuto. Este valor se utiliza en combinación con timeWindow.
* **statistic** : este valor determina cómo se combinan las métricas para dar cabida a la acción de escalado automático. Los valores posibles son: Average, Min y Max.
* **timeWindow** : este valor es el intervalo de tiempo durante el cual se recopilan los datos de instancia. Debe estar comprendido entre 5 minutos y 12 horas.
* **timeAggregation** : este valor determina la manera en que se deberían combinar los datos recopilados en el tiempo. El valor predeterminado es Average. Los valores posibles son: Average, Minimum, Maximum, Last, Total, Count.
* **operator** : este valor es el operador que se utiliza para comparar los datos de métrica y el umbral. Los valores posibles son: Equals, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
* **threshold** : este es el valor que desencadena la acción de escalado. Asegúrese de que proporciona una diferencia suficiente entre el umbral para la acción de escalado horizontal y el de la acción de reducción horizontal. Si establece los mismos valores, el sistema anticipa cambio constante, lo que evita tener que implementar una acción de escalado. Por ejemplo, si se establecen en 600 subprocesos en el ejemplo anterior, no funciona.
* **direction** : este valor determina la acción que se realiza cuando se alcanza el valor de umbral. Los valores posibles son Increase o Decrease.
* **type** : este valor es el tipo de acción que debe realizarse y que se debe establecer en ChangeCount.
* **value** : este valor es el número de máquinas virtuales que se agregan o se quitan del conjunto de escalado. Este valor debe ser 1 o un valor superior.
* **cooldown** : este valor es la cantidad de tiempo de espera desde la última acción de escalado hasta antes de que se produzca la siguiente acción. Debe estar comprendido entre un minuto y una semana.

Según el contador de rendimiento que se utilice, algunos de los elementos de la configuración de plantilla se utilizarán de manera diferente. En el ejemplo anterior, el contador de rendimiento es el llamado Número de subprocesos y el valor de umbral es 650 para una acción de escalado horizontal y 550, para la acción de reducción horizontal. Si utiliza un contador como % de tiempo de procesador, el valor de umbral se establece en el porcentaje de uso de la CPU que determina una acción de escalado.

Cuando se crea una carga en las máquinas virtuales que desencadena una acción de escalado, la capacidad del conjunto aumenta en función del valor de la plantilla. Por ejemplo, en un conjunto de escalado en el que la capacidad se establece en 3 y el valor de la acción de escalado se establece en 1:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Cuando se crea la carga que hace que el número medio de subprocesos suba por encima del umbral de 650:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

Se desencadena una acción de escalado horizontal que provoca que se aumente en una unidad la capacidad del conjunto:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

Y se agrega una máquina virtual al conjunto de escalado:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Tras un período de recuperación de cinco minutos, si el número medio de subprocesos en las máquinas sigue siendo superior a 600, se agregará otra máquina al conjunto. Si el número medio de subprocesos permanece por debajo de 550, la capacidad del conjunto de escalado se reduce en uno y se quita una máquina del conjunto.

## <a name="set-up-scaling-using-azure-powershell"></a>Configuración del escalado con Azure PowerShell
Para ver ejemplos del uso de PowerShell para configurar el escalado automático, consulte [Ejemplos de inicio rápido de Powershell de Azure Insights](../monitoring-and-diagnostics/insights-powershell-samples.md).

## <a name="set-up-scaling-using-azure-cli"></a>Configuración del escalado con la CLI de Azure
Para ver ejemplos del uso de la CLI de Azure para configurar el escalado automático, consulte [Ejemplos de inicio rápido de CLI multiplataforma de Azure Insights](../monitoring-and-diagnostics/insights-cli-samples.md).

## <a name="investigate-scaling-actions"></a>Más información sobre las acciones de escalado
* [Portal de Azure]() : en estos momentos, puede obtener una cantidad limitada de información mediante el Portal.
* [Explorador de recursos de Azure]() : es la mejor herramienta para explorar el estado actual del conjunto de escalado. Si sigue esta ruta de acceso, debería ver la vista de instancia del conjunto de escalas que creó: suscripciones > {su suscripción} > resourceGroups > {su grupo de recursos} > proveedores > Microsoft.Compute > virtualMachineScaleSets > {su conjunto de escalas} > virtualMachines
* Azure PowerShell: use este comando para más información:
  
        Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
        Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput
* Conéctese a la máquina virtual de jumpbox igual que lo haría con cualquier otra máquina y, a continuación, podrá obtener acceso remoto a las máquinas virtuales del conjunto de escala para supervisar los procesos individuales.

## <a name="next-steps"></a>Pasos siguientes
* Consulte [Escalado automático de máquinas en un conjunto de escalado de máquinas virtuales](virtual-machine-scale-sets-windows-autoscale.md) para ver un ejemplo de cómo crear un conjunto de escalado con el escalado automático configurado.
* Vea ejemplos de características de supervisión de Azure Insights en [Ejemplos de inicio rápido de Powershell de Azure Insights](../monitoring-and-diagnostics/insights-powershell-samples.md)
* Información acerca de las características de notificación en [Uso de acciones de escalado automático para enviar notificaciones de alerta por correo electrónico y Webhook en Azure Insights](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md).
* Información acerca del [Uso de registros de auditoría para enviar notificaciones de alerta por correo electrónico y webhook en Azure Insights](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
* Más información sobre los [escenarios avanzados de escalado automático](virtual-machine-scale-sets-advanced-autoscale.md).

<!--HONumber=Oct16_HO2-->


