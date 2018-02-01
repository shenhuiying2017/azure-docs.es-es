---
title: "Respuesta a alertas de Azure con un runbook de automatización | Microsoft Docs"
description: Aprenda a desencadenar un runbook para que se ejecute cuando se generen alertas de Azure.
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 01/11/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 486a3c80c9d6c19ca0a7ba7d4e3cdcde927053b9
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="respond-to-azure-alerts-with-an-automation-runbook"></a>Respuesta a alertas de Azure con un runbook de automatización

[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) proporciona métricas de nivel básico y registros para la mayoría de los servicios de Microsoft Azure. Los runbooks de automatización de Azure se pueden llamar mediante [grupos de acciones](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) o desde alertas clásicas para automatizar las tareas en función de las alertas. En este artículo se muestra cómo configurar y ejecutar un runbook según estas alertas.

## <a name="alert-types"></a>Tipos de alerta

Los runbooks son acciones admitidas en los tres tipos de alertas. Cuando una alerta llama al runbook, la llamada real es una solicitud HTTP POST al webhook. El cuerpo de la solicitud POST contiene un objeto con formato JSON que incluye propiedades útiles relacionadas con la alerta. La tabla siguiente contiene vínculos al esquema de carga de cada tipo de alerta:

|Alerta  |DESCRIPCIÓN|Esquema de carga  |
|---------|---------|---------|
|[Alerta de métrica clásica](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Reciba una notificación cuando cualquier métrica de nivel de plataforma cumple una condición específica (por ejemplo, el porcentaje de CPU en una máquina virtual supera el 90 % durante los últimos 5 minutos).| [Esquema de carga](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Alerta de registro de actividad](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Reciba una notificación cuando cualquier nuevo evento en el registro de actividad de Azure coincida con las condiciones específicas (por ejemplo, cuando se produce una operación "Eliminar máquina virtual" en myProductionResourceGroup o cuando aparece un nuevo evento de Service Health con estado "Activo").| [Esquema de carga](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[Alertas de métricas casi en tiempo real](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Reciba una notificación con más rapidez que las alertas de métricas cuando una o varias métricas de nivel de plataforma cumplan las condiciones especificadas (por ejemplo, el % de CPU en una máquina virtual es mayor que 90 y Datos de entrada de red es mayor que 500 MB durante los últimos 5 minutos).| [Esquema de carga](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Dado que los datos proporcionados por cada alerta son diferentes, cada una debe administrarse de manera diferente. En la siguiente sección, aprenderá a crear un runbook para administrar estos distintos tipos de alertas.

## <a name="create-a-runbook-to-handle-alerts"></a>Creación de un runbook para administrar las alertas

Para usar la automatización con alertas, necesita un runbook que incorpore lógica para administrar la carga JSON de alerta que se pasa al runbook. El siguiente runbook de ejemplo se debe llamar desde una alerta de Azure. Como se ha descrito en la sección anterior, cada tipo de alerta tiene un esquema diferente. El script toma los datos de webhook del parámetro de entrada del runbook `WebhookData` de una alerta y evalúa la carga JSON para determinar qué tipo de alerta se ha usado. El ejemplo siguiente se usaría en una alerta de una máquina virtual. Los datos de la máquina virtual se recuperan de la carga y se usa esa información para detener la máquina virtual. La conexión debe estar configurada en la cuenta de Automation donde se ejecutó el runbook.

El runbook usa la [cuenta de ejecución ](automation-create-runas-account.md)**AzureRunAsConnection** para autenticarse en Azure con el fin de realizar la acción de administración en la máquina virtual.

El siguiente script de PowerShell se puede modificar para usarse con muchos recursos diferentes.

Tome el ejemplo siguiente y cree un runbook llamado **Stop-AzureVmInResponsetoVMAlert** con el script de PowerShell de ejemplo.

1. Abra su cuenta de Automation.
1. Haga clic en **Runbooks** en **AUTOMATIZACIÓN DE PROCESOS**. Se muestra la lista de runbooks.
1. Haga clic en el botón **Agregar un Runbook** que se encuentra en la parte superior de la lista. En la página **Agregar un runbook**, seleccione **Creación rápida**.
1. Escriba "Stop-AzureVmInResponsetoVMAlert" como **Nombre** del runbook y seleccione **PowerShell** como **Tipo de runbook**. Haga clic en **Create**(Crear).
1. Copie el siguiente ejemplo de PowerShell en el panel de edición. Haga clic en **Publicar** para guardar y publicar el runbook.

```powershell-interactive
<#
.SYNOPSIS
  This runbook will stop a resource management VM in response to an Azure alert trigger.

.DESCRIPTION
  This runbook will stop an resource management VM in response to an Azure alert trigger.
  Input is alert data with information needed to identify which VM to stop.
  
  DEPENDENCIES
  - The runbook must be called from an Azure alert via a webhook.
  
  REQUIRED AUTOMATION ASSETS
  - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
  - An Automation certificate asset called "AzureRunAsCertificate".

.PARAMETER WebhookData
   Optional (user does not need to enter anything, but the service will always pass an object)
   This is the data that is sent in the webhook that is triggered from the alert.

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
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Get the info needed to identify the VM (depends on the payload schema)
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
        # Schema not supported
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

        # Do work only if this is a resource management VM
        if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
        {
            # This is the VM
            Write-Verbose "This is a resource management VM." -Verbose

            # Authenticate to Azure with service principal and certificate and set subscription
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

            # Stop the VM
            Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
            Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
            # [OutputType(PSAzureOperationResponse")]
        }
        else {
            # ResourceType not supported
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    }
    else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
    }
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="create-an-action-group"></a>Creación de un grupo de acciones

Un grupo de acciones es una colección de acciones que se realizan en función de una alerta. Los runbooks son simplemente una de las muchas acciones que están disponibles con los grupos de acciones.

1. En el portal, seleccione **Monitor**.

1. Seleccione **Grupos de acciones** en **Configuración**.

1. Seleccione **Agregar grupo de acciones** y rellene los campos.

1. Escriba un nombre en el cuadro Nombre del grupo de acciones y especifique un nombre en el cuadro Nombre corto. El nombre corto se utiliza en lugar del nombre completo del grupo de acciones cuando se envían notificaciones mediante este grupo.

1. El cuadro de texto Suscripción se rellena automáticamente con la suscripción actual. La suscripción es aquella en la que se guarda el grupo de acciones.
Seleccione el grupo de recursos en el que se guarda el grupo de acciones.

En este ejemplo, creará dos acciones, una acción de runbook y una acción de notificación.

### <a name="runbook-action"></a>Acción de runbook

Los siguientes pasos crean una acción de runbook dentro del grupo de acciones.

1. En **Acciones**, asigne un nombre a la acción.

1. Seleccione **Runbook de Automation** como **Tipo de acción**.

1. En **Detalles**, seleccione **Editar detalles**.

1. En la página **Configurar Runbook**, seleccione **Usuario** en **Origen de runbook**.

1. Elija su **suscripción** y **cuenta de Automation** y, finalmente, seleccione el runbook llamado "Stop-AzureVmInResponsetoVMAlert" que creó en el paso anterior.

1. Cuando termine, haga clic en **Aceptar**.

### <a name="notification-action"></a>Acción de notificación

Los pasos siguientes crean una acción de notificación dentro del grupo de acciones.

1. En **Acciones**, asigne un nombre a la acción.

1. Seleccione **Correo electrónico** como **Tipo de acción**.

1. En **Detalles**, seleccione **Editar detalles**.

1. En la página **Correo electrónico**, escriba la dirección de correo electrónico de notificación y haga clic en **Aceptar**. Agregar una dirección de correo electrónico y el runbook como una acción resulta útil ya que cuando se inicie el runbook recibirá una notificación. El grupo de acciones debe parecerse a esta imagen:

   ![Página Agregar grupo de acciones](./media/automation-create-alert-triggered-runbook/add-action-group.png)

1. Seleccione **Aceptar** para crear el grupo de acciones.

Con el grupo de acciones creado, puede crear [alertas de registro de actividad](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) o [alertas casi en tiempo real](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal) y usar el grupo de acciones que se ha creado.

## <a name="classic-alert"></a>Alerta clásica

Las alertas clásicas se basan en métricas y no usan grupos de acciones, pero tienen acciones de runbook basadas en ellas. Para crear una alerta clásica, siga estos pasos:

1. Seleccione **+Agregar alerta de métrica**.

1. Asigne la alerta de métrica "myVMCPUAlert" y proporcione una breve descripción de la alerta.

1. Establezca la condición de la alerta de métrica en "Mayor que", establezca el umbral en 10 y establezca el período en "En los últimos 5 minutos".

1. En **Realizar acción**, seleccione **Ejecutar un Runbook de esta alerta**.

1. En la página **Configurar Runbook**, seleccione **Usuario** como **Origen de runbook**. Elija la cuenta de automatización y seleccione el runbook **AzureVmInResponsetoVMAlert Stop**. Haga clic en **Aceptar** y de nuevo en **Aceptar** para guardar la regla de alerta.

## <a name="next-steps"></a>pasos siguientes

* Para más información sobre cómo iniciar runbooks de automatización con webhooks, consulte [Inicio de un runbook con un webhook](automation-webhooks.md)
* Para más información sobre las distintas maneras de iniciar un runbook, vea [Inicio de un runbook](automation-starting-a-runbook.md).
* Para aprender a crear una alerta de registro de actividad, consulte [Creación de alertas del registro de actividad](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Para ver cómo se crea una alerta casi en tiempo real, consulte [Creación de una regla de alertas en Azure Portal](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).