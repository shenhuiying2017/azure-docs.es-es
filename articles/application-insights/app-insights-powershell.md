---
title: "Automatización de Azure Application Insights con PowerShell | Microsoft Docs"
description: "Automatice la creación de recursos, alertas y pruebas de disponibilidad en PowerShell mediante una plantilla de Azure Resource Manager."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: mbullwin
ms.openlocfilehash: f4f9d1558d2ef9dc5e1b7b248ad5bc8753f59cf9
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
#  <a name="create-application-insights-resources-using-powershell"></a>Creación de recursos de Application Insights mediante PowerShell
Este artículo muestra cómo automatizar la creación y actualización de los recursos de [Application Insights](app-insights-overview.md) automáticamente mediante Azure Resource Management. Puede hacerlo, por ejemplo, como parte de un proceso de compilación. Junto con el recurso básico de Application Insights, puede crear [pruebas web de disponibilidad](app-insights-monitor-web-app-availability.md), [configurar alertas](app-insights-alerts.md), establecer el [esquema de precios](app-insights-pricing.md) y crear otros recursos de Azure.

La clave para crear estos recursos es las plantillas JSON para el [Administrador de recursos de Azure](../azure-resource-manager/powershell-azure-resource-manager.md). En pocas palabras, el procedimiento es: descargar las definiciones JSON de los recursos existentes; parametrizar determinados valores como los nombres; y luego ejecutar la plantilla siempre que se quiera crear un nuevo recurso. Puede empaquetar varios recursos juntos para crearlos todos en un solo paso, por ejemplo, un monitor de aplicaciones con pruebas de disponibilidad, alertas y almacenamiento para la exportación continua. Existen algunos matices a algunas de las parametrizaciones automáticas, que se explican aquí.

## <a name="one-time-setup"></a>Instalación única
Si no ha usado PowerShell con su suscripción de Azure antes:

Instale el módulo de Azure Powershell en la máquina donde quiere ejecutar los scripts.

1. Instale el [Instalador de plataforma web de Microsoft (v5 o superior)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Úselo para instalar Microsoft Azure Powershell.

## <a name="create-an-azure-resource-manager-template"></a>Creación de una plantilla de Azure Resource Manager
Cree un nuevo archivo .json. Vamos a llamarlo `template1.json` en este ejemplo. Copie este contenido en él:

```JSON
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the application name."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "HockeyAppBridge",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the application type."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "East US",
                "allowedValues": [
                    "South Central US",
                    "West Europe",
                    "East US",
                    "North Europe"
                ],
                "metadata": {
                    "description": "Enter the application location."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "1 = Basic, 2 = Enterprise"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 24,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```



## <a name="create-application-insights-resources"></a>Creación de recursos de Application Insights
1. En PowerShell, inicie sesión en Azure:
   
    `Login-AzureRmAccount`
2. Ejecute un comando similar al siguiente:
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` es el grupo donde desea crear los nuevos recursos.
   * `-TemplateFile` debe aparecer antes que los parámetros personalizados.
   * `-appName` es el nombre del recurso que se va a crear.

Puede agregar otros parámetros, cuyas descripciones se encuentran en la sección de parámetros de la plantilla.

## <a name="to-get-the-instrumentation-key"></a>Para obtener la clave de instrumentación
Después de crear un recurso de aplicación, querrá la clave de instrumentación: 

```PS
    $resource = Find-AzureRmResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzureRmResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>Establecimiento del plan de precios

Puede establecer el [plan de precios](app-insights-pricing.md).

Para crear un recurso de aplicación con el plan de precios de Enterprise, use la plantilla anterior:

```PS
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|priceCode|plan|
|---|---|
|1|Básico|
|2|Enterprise|

* Si solo desea usar el plan de precios básico predeterminado, puede omitir el recurso CurrentBillingFeatures de la plantilla.
* Si desea cambiar el plan de precios una vez creado el recurso de componente, puede usar una plantilla que omita el recurso "microsoft.insights/components". Además, omita el nodo `dependsOn` del recurso de facturación. 

Para comprobar el plan de precios actualizado, consulte la hoja de características y precios en el explorador. **Actualice la vista de explorador** para asegurarse de ver el estado más reciente.



## <a name="add-a-metric-alert"></a>Agregar una alerta de métrica

Para configurar una alerta de métrica al mismo tiempo que el recurso de aplicación, combine código similar al siguiente en el archivo de plantilla:

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Al invocar la plantilla, tiene la opción de agregar este parámetro:

    `-responseTime 2`

También puede parametrizar otros campos. 

Para buscar los nombres de tipo y los detalles de configuración de otras reglas de alerta, cree manualmente una regla y, a continuación, inspecciónela en [Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Agregar una prueba de disponibilidad

Este ejemplo es para una prueba de ping (para probar una sola página).  

**Hay dos partes** en una prueba de disponibilidad: la propia prueba y la alerta que informa de errores.

Combine el código siguiente en el archivo de plantilla que crea la aplicación.

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Para detectar los códigos de otras ubicaciones de prueba, o para automatizar la creación de pruebas web más complejas, cree un ejemplo manualmente y luego parametrice el código en [Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Agregar más recursos

Para automatizar la creación de otros recursos de cualquier variante, cree un ejemplo manualmente y luego copie y parametrice su código en [Azure Resource Manager](https://resources.azure.com/). 

1. Abra el [Administrador de recursos de Azure](https://resources.azure.com/). Desplácese hacia abajo por `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` hasta el recurso de la aplicación. 
   
    ![Navegación en el Explorador de recursos de Azure](./media/app-insights-powershell/01.png)
   
    *Componentes* son los recursos básicos de Application Insights para mostrar aplicaciones. Existen recursos distintos para las reglas de alerta asociadas y las pruebas web de disponibilidad.
2. Copie el código JSON del componente en el lugar adecuado en `template1.json`.
3. Elimine estas propiedades:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Abra las secciones de pruebas web y reglas de alertas y copie el código JSON para los elementos individuales de la plantilla. (No copie de los nodos de pruebas web o reglas de alerta: vaya a los elementos que hay debajo de ellos).
   
    Cada prueba web tiene una regla de alerta asociada, por lo que tiene que copiar las dos.
   
    También puede incluir alertas en las métricas. [Nombres de métricas](app-insights-powershell-alerts.md#metric-names).
5. Inserte esta línea en cada recurso:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parametrización de la plantilla
Ahora, debe reemplazar los nombres específicos por parámetros. Para [parametrizar una plantilla](../azure-resource-manager/resource-group-authoring-templates.md), escriba expresiones mediante un [conjunto de funciones auxiliares](../azure-resource-manager/resource-group-template-functions.md). 

No se puede parametrizar solo una parte de una cadena, así que use `concat()` para compilar las cadenas.

Éstos son ejemplos de sustituciones que querrá realizar. Hay varias repeticiones de cada sustitución. Y puede que tenga otras en la plantilla. En estos ejemplos se usan los parámetros y las variables que se han definido en la parte superior de la plantilla.

| find | reemplazar por |
| --- | --- |
| `"hidden-link:/subscriptions/.../components/MyAppName"` |`"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"` |
| `"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (minúscula) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>Elimine el Guid y el identificador. |

### <a name="set-dependencies-between-the-resources"></a>Establecimiento de dependencias entre los recursos
Azure debe instalar los recursos en un orden estricto. Para tener la seguridad de que una instalación finaliza antes de que comience la siguiente, agregue líneas de dependencia:

* En el recurso de la prueba de disponibilidad:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* En el recurso de la alerta para una prueba de disponibilidad:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Pasos siguientes
Otros artículos de automatización:

* [Script de PowerShell para crear un recurso de Application Insights](app-insights-powershell-script-create-resource.md) : método rápido sin necesidad de plantilla.
* [Uso de PowerShell para configurar alertas en Application Insights](app-insights-powershell-alerts.md)
* [Creación de pruebas web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Envío de Azure Diagnostics a Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Implementación en Azure desde GitHub](http://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Creación de anotaciones de versión](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

