---
title: "Creación de pruebas de recursos, alertas y disponibilidad de Application Insights en PowerShell | Microsoft Docs"
description: "Automatice la administración de recursos de Application Insights mediante una plantilla de Azure Resource Manager."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fc2d3c3fee5abbab0eab16c106c88c8753e703cc
ms.openlocfilehash: f6302d7d11691635c286164f11be74ff3a1ad8dd


---
# <a name="create-application-insights-resources-using-powershell"></a>Creación de recursos de Application Insights mediante PowerShell
En este artículo se muestra cómo crear un recurso de [Application Insights](app-insights-overview.md) en Azure automáticamente. Puede hacerlo, por ejemplo, como parte de un proceso de compilación. Junto con el recurso básico de Application Insights, puede crear [pruebas web de disponibilidad](app-insights-monitor-web-app-availability.md), [configurar alertas](app-insights-alerts.md) y crear otros recursos de Azure.

La clave para crear estos recursos es las plantillas JSON para el [Administrador de recursos de Azure](../azure-resource-manager/powershell-azure-resource-manager.md). En pocas palabras, el procedimiento es: descargar las definiciones JSON de los recursos existentes; parametrizar determinados valores como los nombres; y luego ejecutar la plantilla siempre que se quiera crear un nuevo recurso. Puede empaquetar varios recursos juntos para crearlos todos en un solo paso, por ejemplo, un monitor de aplicaciones con pruebas de disponibilidad, alertas y almacenamiento para la exportación continua. Existen algunos matices a algunas de las parametrizaciones automáticas, que se explican aquí.

## <a name="one-time-setup"></a>Instalación única
Si no ha usado PowerShell con su suscripción de Azure antes:

Instale el módulo de Azure Powershell en la máquina donde quiere ejecutar los scripts.

1. Instale el [Instalador de plataforma web de Microsoft (v5 o superior)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Úselo para instalar Microsoft Azure Powershell.

## <a name="copy-the-json-for-existing-resources"></a>Copia de JSON para los recursos existentes
1. Configure [Application Insights](app-insights-overview.md) para un proyecto similar a los que quiere generar automáticamente. Si lo desea, agregue pruebas web y alertas.
2. Cree un nuevo archivo .json. Vamos a llamarlo `template1.json` en este ejemplo. Copie este contenido en él:

    ```JSON

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "appName": { "type": "string" },
            "webTestName": { "type": "string" },
            "url": { "type": "string" },
            "text": { "type" : "string" }
          },
          "variables": {
            "testName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')))]"
            "alertRuleName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')), 
               '-', subscription().subscriptionId)]"
          },
          "resources": [
            {
              // component JSON file contents
            },
            {
              //web test JSON file contents
            },
            {
              //alert rule JSON file contents
            }

            // Any other resources go here
          ]
        }

    ```

    Esta plantilla configurará una prueba de disponibilidad además del recurso principal.


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
   
    Cada prueba web tiene una regla de alerta correspondiente. La prueba web debe ir primero.
   
    También puede incluir alertas en las métricas. [Nombres de métricas](app-insights-powershell-alerts.md#metric-names).
5. Inserte esta línea en cada recurso:
   
    `"apiVersion": "2015-05-01",`

## <a name="parameterize-the-template"></a>Parametrización de la plantilla
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

## <a name="set-dependencies-between-the-resources"></a>Establecimiento de dependencias entre los recursos
Azure debe instalar los recursos en un orden estricto. Para tener la seguridad de que una instalación finaliza antes de que comience la siguiente, agregue líneas de dependencia:

* En el recurso de la prueba de disponibilidad:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* En el recurso de alerta:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`

Observe que una prueba de disponibilidad consta realmente de dos partes: la misma prueba y una regla de alerta que se activa según los resultados de la prueba.

## <a name="create-application-insights-resources"></a>Creación de recursos de Application Insights
1. En PowerShell, inicie sesión en Azure
   
    `Login-AzureRmAccount`
2. Ejecute un comando similar al siguiente:
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -templateFile .\template1.json `
               -appName myNewApp `
               -webTestName aWebTest `
               -url http://myapp.com `
               -text "Welcome!"
   
    ``` 
   
   * -ResourceGroupName es el grupo donde desea crear los nuevos recursos.
   * -templateFile debe aparecer antes que los parámetros personalizados.
   * -appName es el nombre del recurso que se va a crear.
   * -webTestName es el nombre de la prueba web para crear.
   * -Url es la dirección URL de la aplicación web.
   * -text es una cadena que aparece en la página web.

## <a name="to-get-the-instrumentation-key"></a>Para obtener la clave de instrumentación
Después de crear un recurso de aplicación, querrá el iKey: 

```PS

    $resource = Get-AzureRmResource -ResourceId "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<YOUR RESOURCE GROUP>/providers/Microsoft.Insights/components/<YOUR APP NAME>"

    $resource.Properties.InstrumentationKey
```


## <a name="an-example"></a>los cmdlets
Aquí se muestra la plantilla completa creada. Tiene el componente de aplicación, la prueba de disponibilidad, la alerta de prueba de disponibilidad y una alerta en la métrica de tiempo de respuesta.

``` JSON

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "webTestName": { "type": "string" },
    "appName": { "type": "string" },
    "URL": { "type": "string" },
    "text": { "type": "string" }
  },
  "variables": {
    "alertRuleName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')), '-', subscription().subscriptionId)]",
    "testName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')))]",
    "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
  },
  "resources": [
    {
      //
      // App resource
      //
      "name": "[parameters('appName')]",
      "type": "Microsoft.Insights/components",
      "apiVersion": "2014-04-01",
      "kind": "web",
      "location": "East US", // Set to preferred location 
      "properties": {
        "Application_Type": "web",
        "Flow_Type": "Brownfield",
        "Request_Source": "VSIX3.3.1.0",
        "Name": "[parameters('appName')]",
        "PackageId": null,
        "ApplicationId": "[parameters('appName')]"
      },
      "tags": { "applicationType": "web" }
    },
    {
      //
      // Availability test
      //
      "name": "[variables('testName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "East US", // Set to preferred location
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[parameters('webTestName')]",
        "Description": "n",
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
          "WebTest": "[concat('<WebTest   Name=\"', parameters('webTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('text'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('testName')]"
      }
    },
    {
      //
      // Alert rule for the availability test
      //
      "name": "[variables('alertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "East US", // Must be East US at present
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('testName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('testName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('alertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('testName'))]",
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

    },
    {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "East US", // Must be East US at present
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
        "[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]"
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
          "threshold": 3, //seconds
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
  ]
}


```


## <a name="next-steps"></a>Pasos siguientes
Otros artículos de automatización:

* [Script de PowerShell para crear un recurso de Application Insights](app-insights-powershell-script-create-resource.md) : método rápido sin necesidad de plantilla.
* [Uso de PowerShell para configurar alertas en Application Insights](app-insights-powershell-alerts.md)
* [Creación de pruebas web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Envío de Azure Diagnostics a Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Implementación de Azure desde Github](http://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Creación de anotaciones de versión](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)




<!--HONumber=Nov16_HO4-->


