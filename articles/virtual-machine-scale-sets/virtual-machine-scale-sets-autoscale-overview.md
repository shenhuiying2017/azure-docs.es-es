<properties
	pageTitle="Escalado automático y conjuntos de escalado de máquinas virtuales | Microsoft Azure"
	description="Obtenga información sobre el uso de diagnósticos y recursos para escalar automáticamente las máquinas virtuales de un conjunto de escalado."
	services="virtual-machine-scale-sets"
    documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# Escalado automático y conjuntos de escalado de máquinas virtuales

El escalado automático de máquinas virtuales en un conjunto de escalado consiste en la creación o eliminación de máquinas del conjunto según sea necesario para una aplicación para cumplir con los requisitos de rendimiento y satisfacer los Acuerdos de Nivel de Servicio (SLA). A medida que crece el volumen de trabajo, una aplicación puede requerir recursos adicionales para poder realizar sus tareas de manera eficaz.

El escalado automático es un proceso automatizado que ayuda a reducir la sobrecarga de administración. Al reducir la sobrecarga, la supervisión continua del rendimiento del sistema y la toma de decisiones sobre si agregar o quitar recursos ya no son necesarias. El escalado es un proceso flexible; se pueden aprovisionar más recursos si aumenta la carga en el sistema, pero a medida que la demanda disminuye, se pueden volver a desasignar los recursos para minimizar los costos al tiempo que se mantiene un rendimiento adecuado y se cumple con los acuerdos de nivel de servicio.

Configure el escalado automático en un conjunto de escalado mediante una plantilla de Azure Resource Manager, Azure PowerShell o la CLI de Azure.

## Configuración del escalado mediante plantillas de Resource Manager

En lugar de implementar y administrar cada recurso de la aplicación por separado, utilice una plantilla que implemente y aprovisione todos los recursos en una operación única y coordinada. En la plantilla, se definen los recursos de la aplicación y se especifican los parámetros de implementación para diferentes entornos. La plantilla consta de JSON y expresiones que puede usar para generar valores para su implementación. Para más información, eche un vistazo a [Creación de plantillas de Azure Resource Manager](../resource-group-authoring-templates.md).

En la plantilla, especifique el elemento de la capacidad:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

La capacidad identifica el número de máquinas virtuales en el conjunto. Puede cambiar manualmente la capacidad mediante la implementación de una plantilla con un valor diferente. Si implementa una plantilla solo para cambiar la capacidad, incluya solo el elemento SKU con la capacidad actualizada.

Cambie automáticamente la capacidad del conjunto de escalado mediante una combinación del recurso autoscaleSettings proporcionado por Azure Resource Manager y la extensión Diagnósticos de Azure, instalada en los equipos del conjunto de escalado.

### Configuración de la extensión Diagnósticos de Azure

El ajuste automático de escalado solo es posible si la colección de métricas es correcta en cada máquina virtual del conjunto de escalado. La extensión Diagnósticos de Azure proporciona las funcionalidades de supervisión y diagnóstico que satisfacen las necesidades de la colección de métricas del recurso de escalado automático. Puede instalar la extensión como parte de la plantilla de Resource Manager. Para más información sobre el uso de la extensión, consulte [Crear una máquina virtual de Windows con supervisión y diagnóstico mediante la plantilla de Azure Resource Manager](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).

Este ejemplo muestra las variables que se utilizan en la plantilla para configurar la extensión de diagnósticos:

	"diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
	"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB="4096" xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/> <WindowsEventLog scheduledTransferPeriod="PT1M" > <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="Security!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="System!*[System[(Level = 1 or Level = 2)]]" /></WindowsEventLog>",
	"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\Thread Count" sampleRate="PT15S" unit="Percent"><annotation displayName="Thread Count" locale="es-ES"/></PerformanceCounterConfiguration>",
	"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId="')]",
	"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
	"wadcfgxend": "[concat('"><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

Cuando se implementa la plantilla, se proporcionan parámetros para valores como el nombre de la cuenta de almacenamiento donde se almacenan los datos y el nombre del conjunto de escalado desde el que se recopilan los datos. También en este ejemplo de Windows Server, se recopila solo el contador de rendimiento llamado Número de subprocesos, pero todos los contadores disponibles en Windows o Linux se pueden usar para recopilar información de diagnóstico y se pueden incluir en la configuración de la extensión.

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

Cuando se ejecuta la extensión de diagnósticos, los datos se recopilan en una tabla que se encuentra en la cuenta de almacenamiento que haya especificado. En la tabla WADPerformanceCounters, puede encontrar los datos recopilados. Por ejemplo, este es el número de subprocesos que se recopilan cuando las máquinas virtuales se crean primero en un conjunto de escalado:

![Captura de pantalla de los datos del contador de rendimiento de Windows Server antes de agregar una carga](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### Configuración del recurso autoScaleSettings

El recurso autoscaleSettings usa la información que la extensión de diagnósticos recopiló para tomar decisiones sobre si aumentar o disminuir el número de máquinas virtuales en el conjunto de escalado.

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

En el ejemplo anterior, se crearon dos reglas para definir las acciones de escalado automático. La primera regla define la acción de escalado horizontal y la segunda regla define la acción de reducción horizontal. Estos valores se proporcionan en las reglas:

- **metricName**: este es el mismo que el contador de rendimiento que definimos en la variable wadperfcounter para la extensión de diagnóstico. En el ejemplo anterior, se utilizó el contador Número de subprocesos.  
- **metricResourceUri**: este es el identificador de recursos del conjunto de escala de máquinas virtuales. Este identificador contiene el nombre del grupo de recursos, el nombre del proveedor de recursos y el nombre del conjunto de escalado que se va a escalar.
- **timeGrain**: ésta es la granularidad de las métricas que se recopilan. En el ejemplo anterior, los datos se recopilaban en intervalos de un minuto. Este valor se utiliza en combinación con timeWindow.
- **statistic**: determina cómo se combinan las métricas para dar cabida a la acción de escalado automático. Los valores posibles son: Average, Min y Max.
- **timeWindow**: este es el intervalo de tiempo en que se recopilan los datos de instancia. Debe estar comprendido entre 5 minutos y 12 horas.
- **timeAggregation**: este determina la manera en que se deberían combinar los datos recopilados en el tiempo. El valor predeterminado es Average. Los valores posibles son: Average, Minimum, Maximum, Last, Total, Count.
- **operator**: este es el operador que se utiliza para comparar los datos de métrica y el umbral. Los valores posibles son: Equals, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
- **threshold**: este es el valor que desencadena la acción de escalado. Asegúrese de que proporciona una diferencia suficiente entre el umbral establecido para la acción de escalado horizontal y el establecido para la acción de reducción horizontal. Si establece los valores de forma que sean iguales, por ejemplo si establece ambos en 600 subprocesos en el ejemplo anterior, el sistema anticipará un aumento y disminución constante del tamaño del conjunto de escalado y no implementará ninguna acción de escalado.
- **direction**: este determina la acción que se realiza cuando se alcanza el valor de umbral. Los valores posibles son Increase o Decrease.
- **type**: este es el tipo de acción que debe realizarse y que se debe establecer en ChangeCount.
- **value**: este es el número de máquinas virtuales que se agregan o se quitan del conjunto de escalado. Este valor debe ser 1 o un valor superior.
- **cooldown**: es la cantidad de tiempo de espera desde la última acción de escalada hasta antes de que se produzca la siguiente acción. Debe estar comprendido entre un minuto y una semana.

Según el contador de rendimiento que se utilice, algunos de los elementos de la configuración de plantilla se utilizarán de manera diferente. En este ejemplo, el contador de rendimiento utilizado es el llamado Número de subprocesos y el valor de umbral se establece en 650 para una acción de escalado horizontal y en 550 para la acción de reducción horizontal. Si utiliza un contador como Porcentaje de tiempo de procesador, el valor de umbral se establecerá en el porcentaje de uso de la CPU que podría determinar una acción de escalado.

Cuando se crea una carga en las máquinas virtuales del conjunto que desencadena una acción de escalado, el número de máquinas del conjunto aumenta en función del elemento de valor de la plantilla. Por ejemplo, en un conjunto de escalado en el que la capacidad se establece en 3 y el valor de la acción de escalado se establece en 1:

![Captura de pantalla del número de máquinas de un conjunto antes del escalado automático](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Cuando se crea la carga que hace que el número medio de subprocesos suba por encima del umbral de 650:

![Captura de pantalla de los datos del contador de rendimiento de Windows Server después de agregar una carga](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

Se desencadena una acción de escalado horizontal que provoca que se aumente en uno la capacidad del conjunto:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

Y se agrega una máquina virtual al conjunto de escalado:

![Captura de pantalla del número de máquinas de un conjunto después del escalado automático](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Tras un período de recuperación de cinco minutos, si el número medio de subprocesos en las máquinas sigue siendo superior a 600, se agregará otra máquina al conjunto. Si el número medio de subprocesos permanece por debajo de 550, la capacidad del conjunto de escalado se reduce en uno y se quita una máquina del conjunto.

## Más información sobre las acciones de escalado

- El [Portal de Azure](https://portal.azure.com/): actualmente puede obtener una cantidad limitada de información mediante el portal.
- El [Explorador de recursos de Azure](https://resources.azure.com/): esta es la mejor herramienta para explorar el estado actual del conjunto de escalado.
- [Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/): los cmdlets como **Get AzureRmResource** o **Get Autoscalesetting** se pueden utilizar para obtener información sobre el conjunto de escalado.
- [CLI de azure](../xplat-cli-azure-resource-manager.md): use el comando **azure resource show** para obtener información sobre el conjunto.
- Conéctese a la máquina virtual de jumpbox igual que lo haría con cualquier otra máquina y, a continuación, podrá obtener acceso remoto a las máquinas virtuales del conjunto de escala para supervisar los procesos individuales.

## Pasos siguientes

1. Empiece a crear su primer conjunto de escalado mediante la información descrita en [Creación de un conjunto de escalado de máquinas virtuales de Windows mediante Azure PowerShell](virtual-machine-scale-sets-windows-create.md).
2. Eche un vistazo a [Escalado automático de conjuntos de escalado de máquinas virtuales Windows](virtual-machine-scale-sets-windows-autoscale.md) o a [Escalado automático de conjuntos de escalado de máquinas virtuales de Linux](virtual-machine-scale-sets-linux-autoscale.md) para ver un ejemplo de cómo crear un conjunto de escalado con el escalado automático configurado.

<!---HONumber=AcomDC_0427_2016-->