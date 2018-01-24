---
title: "Administración del entorno de Azure Time Series Insights mediante plantillas de Azure Resource Manager| Microsoft Docs"
description: "En este artículo se describe cómo administrar el entorno de Azure Time Series Insights mediante programación con Azure Resource Manager."
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 12/08/2017
ms.openlocfilehash: b09d4a1aea56a4e306f80a1b43d519d313fd73ab
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Creación de recursos de Time Series Insights mediante plantillas de Azure Resource Manager

En este artículo se describe cómo crear e implementar recursos de Time Series Insights mediante plantillas de Azure Resource Manager, PowerShell y el proveedor de recursos de Time Series Insights.

Time Series Insights admite los siguientes recursos:
   | Recurso | DESCRIPCIÓN |
   | --- | --- |
   | Environment | Un entorno de Time Series Insights es una agrupación lógica de eventos que se leen de los agentes de eventos, se almacenan y están disponibles para consulta. Para más información, consulte [Planeamiento del entorno de Azure Time Series Insights](time-series-insights-environment-planning.md). |
   | Origen de eventos | Un origen de eventos es una conexión a un agente de eventos del que Time Series Insights lee e ingiere eventos en el entorno. Los orígenes de eventos actualmente admitidos son IoT Hub y Event Hub. |
   | Conjuntos de datos de referencia | Los conjuntos de datos de referencia proporcionan metadatos sobre los eventos del entorno. Los metadatos de los conjuntos de datos de referencia se combinarán con los eventos durante la entrada. Los conjuntos de datos de referencia se definen como recursos según sus propiedades de eventos principales. Los metadatos reales que constituye el conjunto de datos de referencia se cargan o se modifican mediante las API de plano de datos. |
   | Directiva de acceso | Las directivas de acceso conceden permisos para emitir consultas de datos, manipular datos de referencia en el entorno y compartir consultas guardadas y perspectivas asociadas con el entorno. Para más información, consulte [Concesión de acceso a los datos de un entorno de Time Series Insights mediante Azure Portal](time-series-insights-data-access.md). |

Una plantilla de Resource Manager es un archivo JSON que define la infraestructura y la configuración de recursos en un grupo de recursos. Para más información, consulte los documentos siguientes:

- [Información general de Azure Resource Manager: implementación de plantilla](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)

La plantilla de inicio rápido [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) está publicada en GitHub. Esta plantilla crea un entorno de Time Series Insights, un origen de eventos secundario configurado para consumir eventos de un centro de eventos y directivas de acceso que conceden acceso a los datos del entorno. Si no se especifica un centro de eventos existente, se crea uno con la implementación.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Implementación de la plantilla de inicio rápido localmente mediante PowerShell

En el procedimiento siguiente se describe cómo usar PowerShell para implementar una plantilla de Azure Resource Manager que crea un entorno de Time Series Insights, un origen de eventos secundarios configurado para consumir eventos de un centro de eventos y directivas de acceso que conceden acceso a la datos del entorno. Si no se especifica un centro de eventos existente, se crea uno con la implementación.

El flujo de trabajo aproximado es el siguiente:

1. Instale PowerShell.
1. Cree la plantilla y un archivo de parámetros.
1. En PowerShell, inicie sesión en la cuenta de Azure.
1. Si no existe, cree un nuevo grupo de recursos.
1. Pruebe la implementación.
1. Implemente la plantilla.

### <a name="install-powershell"></a>Instale PowerShell.

Instale Azure PowerShell siguiendo las instrucciones de [Getting started with Azure PowerShell](/powershell/azure/get-started-azureps) (Introducción a Azure PowerShell).

### <a name="create-a-template"></a>Creación de una plantilla

Clone o copie la plantilla [201-timeseriesinsights-environment-with-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) de GitHub.

### <a name="create-a-parameters-file"></a>Creación de un archivo de parámetros

Para crear un archivo de parámetros, copie el archivo [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json).

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "eventHubNamespaceName": {
          "value": "GEN-UNIQUE"
      },
      "eventHubName": {
          "value": "GEN-UNIQUE"
      },
      "consumerGroupName": {
          "value": "GEN-UNIQUE"
      },
      "environmentName": {
        "value": "GEN-UNIQUE"
      },
      "eventSourceName": {
        "value": "GEN-UNIQUE"
      }
  }
}
```

#### <a name="required-parameters"></a>Parámetros obligatorios

   | . | DESCRIPCIÓN |
   | --- | --- |
   | eventHubNamespaceName | El espacio de nombres del centro de eventos de origen. |
   | eventHubName | El nombre del centro de eventos de origen. |
   | consumerGroupName | El nombre del grupo de consumidores que usará el servicio Time Series Insights para leer los datos del centro de eventos. **NOTA:** Para evitar la contención de los recursos, este grupo de consumidores debe estar dedicado al servicio Time Series Insights y no se debe compartir con otros lectores. |
   | environmentName | El nombre del entorno. El nombre no puede incluir:   '<', '>', '%', '&', ':', '\\', '?', '/' ni ningún carácter de control. Todos los demás caracteres se permiten.|
   | eventSourceName | El nombre del recurso de secundarios del origen de eventos. El nombre no puede incluir:   '<', '>', '%', '&', ':', '\\', '?', '/' ni ningún carácter de control. Todos los demás caracteres se permiten. |

#### <a name="optional-parameters"></a>Parámetros opcionales

   | . | DESCRIPCIÓN |
   | --- | --- |
   | existingEventHubResourceId | Un identificador de recurso opcional de un centro de eventos existente que se conectará al entorno de Time Series Insights mediante el origen de eventos. **NOTA:** El usuario que implementa la plantilla debe tener privilegios para realizar la operación listkeys en el centro de eventos. Si no se pasa ningún valor, se crea un nuevo centro de eventos mediante la plantilla. |
   | environmentDisplayName | Un nombre descriptivo opcional que se muestra en las herramientas o las interfaces de usuario en el lugar del nombre del entorno. |
   | environmentSkuName | El nombre de la SKU. Para más información, consulte la [página de precios de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
   | environmentSkuCapacity | La capacidad unitaria de la SKU. Para más información, consulte la [página de precios de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
   | environmentDataRetentionTime | El intervalo de tiempo mínimo que los eventos del entorno estarán disponibles para consulta. El valor debe especificarse en formato ISO 8601, por ejemplo, "P30D" para una directiva de retención de 30 días. |
   | eventSourceDisplayName | Un nombre descriptivo opcional que se muestra en las herramientas o las interfaces de usuario en el lugar del nombre del origen de eventos. |
   | eventSourceTimestampPropertyName | La propiedad de evento que se usará como intervalo de tiempo del origen de eventos. Si no se especifica un valor para timestampPropertyName, o si se especifica una cadena null o vacía, se usará la hora de creación del evento. |
   | eventSourceKeyName | El nombre de la clave de acceso compartido que usará el servicio Time Series Insights para conectarse al centro de eventos. |
   | accessPolicyReaderObjectIds | Una lista de identificadores de objetos de los usuarios o aplicaciones de Azure AD que deben tener acceso de lectura en el entorno. Se puede obtener el valor de objectId de la entidad de servicio mediante la llamada al cmdlet **Get-AzureRMADUser** o **Get-AzureRMADServicePrincipal**. Aún no se admite la creación de una directiva de acceso para grupos de Azure AD. |
   | accessPolicyContributorObjectIds | Una lista de identificadores de objeto de los usuarios o aplicaciones de Azure AD que deben tener acceso de colaborador al entorno. Se puede obtener el valor de objectId de la entidad de servicio mediante la llamada al cmdlet **Get-AzureRMADUser** o **Get-AzureRMADServicePrincipal**. Aún no se admite la creación de una directiva de acceso para grupos de Azure AD. |

Como ejemplo, el siguiente archivo de parámetros se usaría para crear un entorno y un origen de eventos que lee los eventos de un centro de eventos existente. También se crean dos directivas de acceso que conceden acceso de colaborador al entorno.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventHubNamespaceName": {
            "value": "tsiTemplateTestNamespace"
        },
        "eventHubName": {
            "value": "tsiTemplateTestEventHub"
        },
        "consumerGroupName": {
            "value": "tsiTemplateTestConsumerGroup"
        },
        "environmentName": {
          "value": "tsiTemplateTestEnvironment"
        },
        "eventSourceName": {
          "value": "tsiTemplateTestEventSource"
        },
        "existingEventHubResourceId": {
          "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
        },
        "accessPolicyContributorObjectIds": {
            "value": [
                "AGUID001-0000-0000-0000-000000000000",
                "AGUID002-0000-0000-0000-000000000000"
            ]
        }
    }
  }
```

Para obtener más información, consulte el artículo [Parámetros](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Inicio de sesión en Azure y establecimiento de la suscripción de Azure

En una secuencia de comandos de PowerShell, ejecute el siguiente comando:

```powershell
Login-AzureRmAccount
```

Se le solicitará que inicie sesión en la cuenta de Azure. Después de iniciar sesión, ejecute el siguiente comando para ver sus suscripciones disponibles:

```powershell
Get-AzureRMSubscription
```

Este comando devuelve una lista de suscripciones de Azure disponibles. Seleccione una suscripción para la sesión actual mediante la ejecución del siguiente comando. Reemplace `<YourSubscriptionId>` por el GUID de la suscripción de Azure que desea usar:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Configuración del grupo de recursos

Si no tiene un grupo de recursos existente, cree uno nuevo con el comando **New-AzureRmResourceGroup**. Proporcione el nombre del grupo de recursos y la ubicación que desee utilizar. Por ejemplo: 

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Si es correcto, se muestra un resumen del nuevo grupo de recursos.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Prueba de la implementación

Valide la implementación mediante la ejecución del cmdlet `Test-AzureRmResourceGroupDeployment`. Al probar la implementación, proporcione los parámetros exactamente como lo haría al ejecutar la implementación.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

### <a name="create-the-deployment"></a>Creación de la implementación

Para crear la nueva implementación, ejecute el cmdlet `New-AzureRmResourceGroupDeployment` y proporcione los parámetros necesarios cuando se le solicite. Los parámetros incluyen un nombre para la implementación, el nombre del grupo de recursos y la ruta de acceso o la dirección URL al archivo de plantilla. Si no se especifica el parámetro **Modo**, se usa el valor predeterminado **Incremental**. Para más información, vea [Implementaciones incrementales y completas](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments).

El siguiente comando le solicita los cinco parámetros necesarios en la ventana de PowerShell:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

Para especificar un archivo de parámetros en su lugar, use el siguiente comando:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

También puede utilizar parámetros en línea cuando ejecute el cmdlet de implementación. El comando es el siguiente:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Para ejecutar una implementación [completa](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments), establezca el parámetro **Modo** en **Completo**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

## <a name="verify-the-deployment"></a>Comprobar la implementación

Si los recursos se implementan correctamente, aparecerá un resumen de la implementación en la ventana de PowerShell:

```powershell
DeploymentName          : azuredeploy
ResourceGroupName       : MyDemoRG
ProvisioningState       : Succeeded
Timestamp               : 12/9/2017 01:06:54
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          eventHubNamespaceName  String               <eventHubNamespaceName>
                          eventHubName     String                     <eventHubName>
                          consumerGroupName  String                   <consumerGroupName>
                          existingEventHubResourceId  String
                          environmentName  String                     <environmentName>
                          environmentDisplayName  String
                          environmentSkuName  String                  S1
                          environmentSkuCapacity  Int                 1
                          environmentDataRetentionTime  String        P30D
                          eventSourceName  String                     <eventSourceName>
                          eventSourceDisplayName  String
                          eventSourceTimestampPropertyName  String
                          eventSourceKeyName  String                  manage
                          accessPolicyReaderObjectIds  Array          []
                          accessPolicyContributorObjectIds  Array     [
                            "AGUID001-0000-0000-0000-000000000000",
                            "AGUID002-0000-0000-0000-000000000000"
                          ]

Outputs                 :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          dataAccessFQDN   String
                          <guid>.env.timeseries.azure.com
```

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>Implementación de la plantilla de inicio rápido mediante Azure Portal

La página principal de la plantilla de inicio rápido de GitHub incluye también un botón **Deploy to Azure** (Implementar en Azure). Al hacer clic en él se abre una página de implementación personalizado en Azure Portal. En esta página, puede definir o seleccionar valores para cada uno de los parámetros en las tablas de [parámetros obligatorios](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters) o [parámetros opcionales](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters). Después de rellenar la configuración, al hacer clic en el botón **Purchase** (Comprar) se inicia la implementación de la plantilla.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>pasos siguientes

- Para más información sobre la administración de recursos de Time Series Insights mediante programación por medio de las API de REST, consulte [Time Series Insights Management](https://docs.microsoft.com/rest/api/time-series-insights-management/) (Administración de Time Series Insights).
