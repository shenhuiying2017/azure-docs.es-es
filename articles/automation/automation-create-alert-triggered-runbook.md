---
title: Uso de una alerta para desencadenar un runbook de Azure Automation | Microsoft Docs
description: Aprenda a desencadenar un runbook para que se ejecute cuando se genere una alerta de Azure.
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 01/11/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 9ea51a85110bb8169dce0a445549e2590c51207e
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Uso de una alerta para desencadenar un runbook de Azure Automation

Puede usar [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) para supervisar métricas de nivel básico y registros para la mayoría de los servicios de Azure. Puede llamar a runbooks de Azure Automation mediante [grupos de acciones](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) o mediante alertas clásicas para automatizar las tareas basadas en alertas. En este artículo se muestra cómo configurar y ejecutar un runbook mediante alertas.

## <a name="alert-types"></a>Tipos de alerta

Puede usar runbooks de Automation con tres tipos de alerta:
* Alertas de métricas clásicas
* Alertas de registros de actividad
* Alertas de métricas casi en tiempo real

Cuando una alerta llama a un runbook, la llamada real es una solicitud HTTP POST al webhook. El cuerpo de la solicitud POST contiene un objeto con formato JSON que tiene propiedades útiles relacionadas con la alerta. En la tabla siguiente se enumeran vínculos al esquema de carga de cada tipo de alerta:

|Alerta  |DESCRIPCIÓN|Esquema de carga  |
|---------|---------|---------|
|[Alerta de métrica clásica](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envía una notificación cuando cualquier métrica de nivel de plataforma cumple una condición específica. Por ejemplo, cuando el valor de **% de CPU** en una VM es mayor que **90** durante los últimos cinco minutos.| [Esquema de carga de alertas de métricas clásicas](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Alerta de registro de actividad](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envía una notificación cuando cualquier evento nuevo del registro de actividad de Azure coincide con las condiciones específicas. Por ejemplo, cuando un operación `Delete VM` se produce en **myProductionResourceGroup** o cuando aparece un nuevo evento de Azure Service Health con un estado **Activo**.| [Esquema de carga de alertas de registros de actividad](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[Alertas de métricas casi en tiempo real](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envía una notificación con más rapidez que las alertas de métrica cuando una o varias métricas de nivel de plataforma cumplen las condiciones especificadas. Por ejemplo, cuando el valor de **% de CPU** en una VM es mayor que **90** y el valor de **Entrada de red** es mayor que **500 MB** durante los últimos cinco minutos.| [Esquema de carga de alertas de métricas casi en tiempo real](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Dado que los datos que cada tipo de alerta proporciona son diferentes, cada tipo de alerta se administra de manera diferente. En la siguiente sección, aprenderá a crear un runbook para administrar distintos tipos de alertas.

## <a name="create-a-runbook-to-handle-alerts"></a>Creación de un runbook para administrar las alertas

Para usar Automation con alertas, necesita un runbook que incorpore lógica que administre la carga de JSON de alerta que se pasa al runbook. El siguiente runbook de ejemplo se debe llamar desde una alerta de Azure. 

Como se ha descrito en la sección anterior, cada tipo de alerta tiene un esquema diferente. El script toma los datos del webhook del parámetro de entrada del runbook `WebhookData` desde una alerta. Después, el script evalúa la carga de JSON para determinar qué tipo de alerta se usó. 

Este ejemplo usa una alerta de una VM. Los datos de la VM se recuperan de la carga y luego se usa esa información para detener dicha VM. La conexión debe estar configurada en la cuenta de Automation donde se ejecuta el runbook.

El runbook usa **AzureRunAsConnection** como la [cuenta de ejecución ](automation-create-runas-account.md) para autenticarse en Azure con el fin de realizar la acción de administración en la máquina virtual.

Use este ejemplo para crear un runbook llamado **AzureVmInResponsetoVMAlert Stop**. Puede modificar el script de PowerShell y usarlo con muchos recursos diferentes.

1. Vaya a la cuenta de Azure Automation.
1. En **AUTOMATIZACIÓN DE PROCESOS**, seleccione **Runbooks**.
1. En la parte superior de la lista de runbooks, seleccione **Agregar un Runbook**. 
1. En la página **Agregar un Runbook**, seleccione **Creación rápida**.
1. Para el nombre del runbook, escriba **Stop-AzureVmInResponsetoVMAlert**. Como tipo de runbook, seleccione **PowerShell**. Seleccione **Crear**.  
1. Copie el siguiente ejemplo de PowerShell en el panel **Editar**. 

    ```powershell-interactive
    <#
    .SYNOPSIS
    This runbook stops a resource management VM in response to an Azure alert trigger.

    .DESCRIPTION
    This runbook stops a resource management VM in response to an Azure alert trigger.
    The input is alert data that has the information required to identify which VM to stop.
    
    DEPENDENCIES
    - The runbook must be called from an Azure alert via a webhook.
    
    REQUIRED AUTOMATION ASSETS
    - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
    - An Automation certificate asset called "AzureRunAsCertificate".

    .PARAMETER WebhookData
    Optional. (The user doesn't need to enter anything, but the service always passes an object.)
    This is the data that's sent in the webhook that's triggered from the alert.

    .NOTES
    AUTHOR: Azure Automation Team
    LASTEDIT: 2017-11-22
    #>

    [OutputType("PSAzureOperationResponse")]

    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )

    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData.
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema).
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # The schema isn't supported.
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if ($status -eq "Activated")
        {
            $ResourceType = $AlertContext.resourceType
            $ResourceGroupName = $AlertContext.resourceGroupName
            $SubId = $AlertContext.subscriptionId
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Use this only if this is a resource management VM.
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is the VM.
                Write-Verbose "This is a resource management VM." -Verbose

                # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the VM.
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType isn't supported.
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated', so no action taken.
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```
1. Seleccione **Publicar** para guardar y publicar el runbook.

## <a name="create-an-action-group"></a>Creación de un grupo de acciones

Un grupo de acciones es una colección de acciones que se desencadenan por una alerta. Los runbooks son simplemente una de las muchas acciones que puede usar con los grupos de acciones.

1. En Azure Portal, seleccione **Supervisar** > **CONFIGURACIÓN** > **Grupos de acciones**.
1. Seleccione **Agregar grupo de acciones** y escriba la información necesaria:  
    1. En el cuadro **Nombre del grupo de acciones**, escriba un nombre.
    1. En el cuadro **Nombre corto**, escriba un nombre. El nombre corto se utiliza en lugar del nombre completo del grupo de acciones cuando se envían notificaciones mediante este grupo de acciones.
    1. El cuadro **Suscripción** se rellena automáticamente con la suscripción actual. Esta es la suscripción en la que se guarda el grupo de acciones.
    1. Seleccione el grupo de recursos en el que se guarda el grupo de acciones.

En este ejemplo, creará dos acciones: una acción de runbook y una acción de notificación.

### <a name="runbook-action"></a>Acción de runbook

Para crear una acción de runbook en el grupo de acciones:

1. En **Acciones**, para **NOMBRE DE ACCIÓN**, escriba un nombre para la acción. Para **TIPO DE ACCIÓN** seleccione **Runbook de Automation**.
1. En **DETALLES**, seleccione **Editar detalles**.  
1. En la página **Configurar Runbook**, en **Origen de runbook**, seleccione **Usuario**.  
1. Seleccione su **Suscripción** y **Cuenta de Automation** y luego seleccione el runbook **AzureVmInResponsetoVMAlert Stop**.  
1. Cuando haya terminado, seleccione **Aceptar**.

### <a name="notification-action"></a>Acción de notificación

Para crear una acción de notificación en el grupo de acciones:

1. En **Acciones**, para **NOMBRE DE ACCIÓN**, escriba un nombre para la acción. Para **TIPO DE ACCIÓN**, seleccione **Correo electrónico**.  
1. En **DETALLES**, seleccione **Editar detalles**.  
1. En la página **Correo electrónico**, escriba la dirección de correo electrónico para usar para la notificación y luego seleccione **Aceptar**. Agregar una dirección de correo electrónico además del runbook como una acción resulta de gran utilidad. Se le notificará el momento en el que se inicie el runbook.  

    El grupo de acciones debe parecerse a esta imagen:

   ![Página Agregar grupo de acciones](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. Para crear el grupo de acciones, seleccione **Aceptar**.

Puede usar este grupo de acciones en las [alertas de registro de actividad](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) y las [alertas casi en tiempo real](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal) que cree.

## <a name="classic-alert"></a>Alerta clásica

Las alertas clásicas se basan en métricas y no usan grupos de acciones. Sin embargo, puede configurar una acción de runbook basada en una alerta clásica. 

Para crear una alerta clásica:

1. Seleccione **Agregar alerta de métrica**.
1. Asigne un nombre a la alerta de métrica **myVMCPUAlert**. Escriba una breve descripción para la alerta.
1. Para la condición de la alerta de métrica, seleccione **Mayor que**. Para el valor **Umbral**, seleccione **10**. Para el valor **Período**, seleccione **En los últimos 5 minutos**.
1. En **Realizar acción**, seleccione **Ejecutar un Runbook de esta alerta**.
1. En la página **Configurar Runbook**, para **Origen de runbook**, seleccione **Usuario**. Elija la cuenta de automatización y luego seleccione el runbook **AzureVmInResponsetoVMAlert Stop**. Seleccione **Aceptar**.
1. Para guardar la regla de alerta, haga clic en **Aceptar**.

## <a name="next-steps"></a>pasos siguientes

* Para más información sobre cómo iniciar un runbook de Automation mediante webhooks, consulte [Inicio de un runbook de Azure Automation con un webhook](automation-webhooks.md).
* Para más información acerca de diferentes maneras de iniciar un Runbook, consulte [Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md).
* Para aprender a crear una alerta de registro de actividad, consulte [Creación de alertas del registro de actividad](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Para aprender a crear una alerta casi en tiempo real, consulte [Creación de una regla de alertas en Azure Portal](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).
