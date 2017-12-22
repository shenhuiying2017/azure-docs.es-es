---
title: "Creación de alertas para los servicios de Azure | Microsoft Docs"
description: "Desencadenamiento de correos electrónicos y notificaciones, y llamadas a direcciones URL de sitios web (webhooks) o a la automatización cuando se cumplen las condiciones especificadas."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d26ab15b-7b7e-42a9-81c8-3ce9ead5d252
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
ms.openlocfilehash: d3fca8675c1f15b8fd0f952cfbf520f5c68478b3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-metric-alerts-in-azure-monitor-for-azure-services---powershell"></a>Creación de alertas de métricas en Azure Monitor para servicios de Azure: PowerShell
> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Información general
En este artículo se muestra cómo configurar alertas de métricas de Azure con PowerShell.  

Puede recibir una alerta basada en las métricas de supervisión para los servicios de Azure o los eventos sobre ellos.

* **Valores de métrica** : la alerta se desencadena cuando el valor de una métrica específica cruza un umbral asignado en cualquier dirección. Es decir, se desencadena tanto la primera vez que se cumple la condición como después, cuando dicha condición ya deja de cumplirse.    
* **Eventos de registro de actividades**: una alerta puede desencadenarse con *cada* evento o solo cuando se producen ciertos eventos concretos. Para obtener más información sobre las alertas de registro de actividad, [haga clic aquí](monitoring-activity-log-alerts.md).

Puede configurar una alerta de métrica para hacer lo siguiente cuando se desencadena:

* Enviar notificaciones de correo electrónico al administrador y los coadministradores del servicio.
* Enviar un correo electrónico a direcciones de correo electrónico adicionales que especifique.
* Llamar a un webhook.
* Iniciar la ejecución de un runbook de Azure (solo desde Azure Portal).

Puede obtener información sobre las reglas de alerta y configurarlas mediante:

* [Portal de Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Interfaz de la línea de comandos (CLI)](insights-alerts-command-line-interface.md)
* [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Para información adicional, siempre puede escribir ```Get-Help``` y, luego, el comando de PowerShell sobre el que desea obtener ayuda.

## <a name="create-alert-rules-in-powershell"></a>Creación de reglas de alerta en PowerShell
1. Inicie sesión en Azure.   

    ```PowerShell
    Login-AzureRmAccount

    ```
2. Obtenga una lista de las suscripciones disponibles. Compruebe que trabaja con la suscripción adecuada. Si no es así, establezca la suscripción correcta con la salida de `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. Para ver las reglas existentes en un grupo de recursos, use el comando siguiente:

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. Para crear una regla, primero debe contar con varios datos importantes.

  * El **identificador de recurso** del recursos para el que desea establecer una alerta.
  * Las **definiciones de métricas** disponibles para ese recurso.

     Una forma de obtener el identificador de recurso es usar Azure Portal. Seleccione el recurso en el portal, suponiendo que ya existe. En la sección *Configuración* de la hoja siguiente, seleccione *Propiedades*. El campo **RESOURCE ID** está en la hoja siguiente. Otra forma que puede usar es el [Explorador de recursos de Azure](https://resources.azure.com/).

     Este es un ejemplo de identificador de recursos de una aplicación web:

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Puede usar `Get-AzureRmMetricDefinition` para ver la lista de todas las definiciones de métricas de un recurso específico.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     En el ejemplo siguiente se genera una tabla con el nombre y la unidad de esa métrica.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     Ejecute `Get-Help Get-AzureRmMetricDefinition -Detailed` para obtener una lista completa de las opciones disponibles para Get-AzureRmMetricDefinition.
5. En el ejemplo siguiente se configura una alerta en un recurso de sitio web. La alerta se desencadena cada vez que se recibe constantemente cualquier tráfico durante 5 minutos y nuevamente cuando no se recibe tráfico durante 5 minutos.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. Para crear un webhook o enviar un correo electrónico cuando se desencadene una alerta, debe crear el correo electrónico o los webhooks. Cree la regla inmediatamente después con la etiqueta -Actions, tal como se muestra en el ejemplo siguiente. No puede asociar webhooks o correos electrónicos con reglas ya creadas mediante PowerShell.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. Observe las reglas individuales para comprobar que las alertas se crearon correctamente.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. Elimine las alertas. Estos comandos eliminan las reglas que se crearon anteriormente en este artículo.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Pasos siguientes
* [Obtenga información general sobre la supervisión de Azure](monitoring-overview.md) , incluidos los tipos de información que puede recopilar y supervisar.
* Obtenga más información sobre cómo [configurar webhooks en las alertas](insights-webhooks-alerts.md).
* Obtenga más información sobre la [configuración de alertas sobre los eventos de registro de actividad](monitoring-activity-log-alerts.md).
* Obtenga más información sobre los [runbooks de Azure Automation](../automation/automation-starting-a-runbook.md).
* Obtenga [información general sobre la colección de registros de diagnóstico](monitoring-overview-of-diagnostic-logs.md) para recopilar métricas detalladas de alta frecuencia sobre el servicio.
* Obtenga [información general sobre la colección de métricas](insights-how-to-customize-monitoring.md) para garantizar que el servicio está disponible y que responder adecuadamente.
