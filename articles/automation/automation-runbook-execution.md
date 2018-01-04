---
title: "Ejecución de un runbook en Azure Automation | Microsoft Docs"
description: "Describe los detalles de cómo se procesa un runbook en Azure Automation."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: d10c8ce2-2c0b-4ea7-ba3c-d20e09b2c9ca
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2017
ms.author: magoedte;bwren
ms.openlocfilehash: a443071aee3e0f845de4387322d2866157a9fe87
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="runbook-execution-in-azure-automation"></a>Ejecución de un runbook en Azure Automation
Cuando se inicia un runbook en Azure Automation, se crea un trabajo. Un trabajo es una instancia única de ejecución de un runbook. Un trabajador de Azure Automation está asignado para ejecutar cada trabajo. Aunque los trabajadores se comparten por varias cuentas de Azure, los trabajos de diferentes cuentas de Automation están aislados entre sí. No tiene el control sobre qué trabajador presta servicio a la solicitud para el trabajo. Un único runbook puede tener varios trabajos que se ejecutan al mismo tiempo.  Se puede reutilizar el entorno de ejecución para los trabajos de la misma cuenta de Automation. Al ver la lista de runbooks en Azure Portal, se muesta el estado de todos los trabajos iniciados por cada runbook. Puede ver la lista de trabajos para cada runbook para hacer un seguimiento del estado de cada uno. Para obtener una descripción de los distintos estados de trabajo, consulte [Estados del trabajo](#job-statuses).

En el siguiente diagrama se muestra el ciclo de vida de un trabajo de runbook para [Runbooks gráficos](automation-runbook-types.md#graphical-runbooks) y [Runbooks del flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Estados de trabajo: flujo de trabajo de PowerShell](./media/automation-runbook-execution/job-statuses.png)

En el siguiente diagrama se muestra el ciclo de vida de un trabajo de runbook para [runbooks de PowerShell](automation-runbook-types.md#powershell-runbooks).

![Estados de trabajo: script de PowerShell](./media/automation-runbook-execution/job-statuses-script.png)

Los trabajos tienen acceso a los recursos de Azure mediante una conexión a la suscripción de Azure. Solo tienen acceso a los recursos de su centro de datos si estos recursos están accesibles desde la nube pública.

## <a name="job-statuses"></a>Estados del trabajo
En la tabla siguiente se describen los diferentes estados posibles para un trabajo.

| Status | DESCRIPCIÓN |
|:--- |:--- |
| Completed |El trabajo se completó correctamente. |
| Con error |Para [Runbooks del flujo de trabajo de PowerShell](automation-runbook-types.md), no se pudo compilar el runbook.  Para [Runbooks de script de PowerShell](automation-runbook-types.md), no se pudo iniciar el runbook o el trabajo encontró una excepción. |
| Error, esperando recursos |Se produjo un error en el trabajo porque ha alcanzado el límite de [distribución equilibrada](#fair-share) tres veces y se ha iniciado en el mismo punto de control o en el inicio del runbook cada vez. |
| En cola |El trabajo espera que los recursos en un trabajo de Automation estén disponible para que se puede iniciar. |
| Iniciando |El trabajo se ha asignado a un trabajador y el sistema está en proceso de iniciarse. |
| Reanudando |El sistema está en proceso de reanudar el trabajo después de que se suspendió. |
| En ejecución |El trabajo se está ejecutando. |
| En ejecución, esperando recursos |El trabajo se ha descargado porque ha alcanzado el límite de [distribución equilibrada](#fair-share) . Se reanuda en breve desde su último punto de control. |
| Detenido |El trabajo lo detuvo el usuario antes de completarse. |
| Deteniéndose |El sistema está en proceso de detener el trabajo. |
| Suspended |El trabajo lo ha suspendido el usuario, el sistema o un comando en el runbook. Un trabajo suspendido se puede iniciar de nuevo y se reanuda desde su último punto de control o desde el principio del runbook si no tiene ningún punto de control. El runbook solo lo suspenderá el sistema al producirse una excepción. De forma predeterminada, se establece ErrorActionPreference en **Continuar**, lo que significa que el trabajo sigue ejecutándose en un error. Si se establece esta variable de preferencia en **Detener**, se suspende el trabajo en un error.  Solo se aplica a [Runbooks de flujo de trabajo de PowerShell y gráficos](automation-runbook-types.md) . |
| Suspendiendo |El sistema está intentando suspender el trabajo a petición del usuario. El runbook debe alcanzar su siguiente punto de control antes de que se pueda suspender. Si ya ha pasado su último punto de control, se completa antes de que se pueda suspender.  Solo se aplica a [Runbooks de flujo de trabajo de PowerShell y gráficos](automation-runbook-types.md) . |

## <a name="viewing-job-status-from-the-azure-portal"></a>Visualización de un estado de trabajo desde el portal de Azure
Puede ver un resumen del estado de todos los trabajos de runbook o profundizar en los detalles de un trabajo de runbook específico en el portal de Azure o mediante la configuración de la integración con el área de trabajo Log Analytics de Microsoft Operations Management Suite (OMS) para reenviar el estado del trabajo de runbook y las transmisiones de trabajos.  Para más información sobre la integración con Log Analytics de OMS, consulte [Reenvío del estado de un trabajo y de transmisiones de trabajos desde Automation a Log Analytics (OMS)](automation-manage-send-joblogs-log-analytics.md).  

### <a name="automation-runbook-jobs-summary"></a>Resumen de trabajos de runbook de Automation
A la derecha de la cuenta de Automation, puede ver un resumen de todos los trabajos de runbook de una cuenta de Automation seleccionada en el icono **Estadísticas de trabajo**.<br><br> ![Icono Estadísticas de trabajo](./media/automation-runbook-execution/automation-account-job-status-summary.png).<br> Este icono muestra un contador y una representación gráfica del estado de trabajo de todos los trabajos ejecutados.  

Si hace clic en el icono aparece la hoja **Trabajos**, que incluye una lista resumida de todos los trabajos ejecutados, con el estado, la ejecución del trabajo y la hora de inicio y finalización.<br><br> ![Hoja Trabajos de cuenta de Automation](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)<br><br>  Puede filtrar la lista de trabajos si selecciona **Filtrar trabajos** y filtra según un runbook específico, el estado de un trabajo o desde la lista desplegable, el intervalo de fecha y hora dentro del cual desea buscar.<br><br> ![Filtrar estado del trabajo](./media/automation-runbook-execution/automation-account-jobs-filter.png)

De manera alternativa, puede ver los detalles resumidos de trabajo de un runbook específico; para ello, seleccione ese runbook en la hoja **Runbooks** de la cuenta de Automation y, luego, seleccione el icono **Trabajos**.  Se abre la hoja **Trabajos** y, desde ahí, puede hacer clic en el registro del trabajo para ver sus detalles y salida.<br><br> ![Hoja Trabajos de cuenta de Automation](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)<br> 

### <a name="job-summary"></a>Resumen del trabajo
Puede ver una lista de todos los trabajos que se han creado para un runbook determinado y su estado más reciente. Puede filtrar esta lista por estado del trabajo y el intervalo de fechas del último cambio del trabajo. Para ver su información detallada y la salida, haga clic en el nombre de un trabajo. La vista detallada del trabajo incluye los valores para los parámetros de runbook que se proporcionaron con ese trabajo.

Puede utilizar los pasos siguientes para ver los trabajos de un runbook.

1. En Azure Portal, seleccione **Automation** y después elija el nombre de una cuenta de Automation.
2. En el concentrador, seleccione **Runbooks** y, después, en la hoja **Runbooks**, seleccione un runbook de la lista.
3. En la hoja del runbook seleccionado, haga clic en el icono **Trabajos**.
4. Haga clic en uno de los trabajos de la lista y en la hoja de detalles del trabajo del runbook podrá ver su detalle y salida.

## <a name="retrieving-job-status-using-windows-powershell"></a>Recuperación del estado del trabajo con Windows PowerShell
Puede usar [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) para recuperar los trabajos creados para un runbook y los detalles de un trabajo determinado. Si inicia un runbook con Windows PowerShell mediante el uso de [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx), devuelve el trabajo resultante. Use la salida [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) para obtener la salida de un trabajo.

Los comandos de ejemplo siguientes recuperan el último trabajo para un runbook de ejemplo y muestran su estado, los valores proporcionados para los parámetros de runbook y la salida del trabajo.

    $job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
    –RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
    $job.Status
    $job.JobParameters
    Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output

## <a name="fair-share"></a>distribución equilibrada
Para compartir recursos entre todos los Runbooks en la nube, Azure Automation descargará temporalmente cualquier trabajo cuando haya estado ejecutándose durante tres horas.  Durante este tiempo, los trabajos de [runbooks basados en PowerShell](automation-runbook-types.md#powershell-runbooks) se detienen y no se reiniciarán.  El estado del trabajo aparece como **Detenido**.  Este tipo de runbook siempre se reinicia desde el principio, ya que no admite los puntos de control.  

Los [runbooks basados en el flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) se reanudarán a partir de su último [punto de control](https://docs.microsoft.com/system-center/sma/overview-powershell-workflows#bk_Checkpoints).  Después de ejecutarse durante tres horas, el servicio suspenderá el trabajo del runbook y su estado aparecerá como **En ejecución, esperando recursos**.  Si hay un espacio aislado disponible, el servicio Automation reiniciará automáticamente el runbook, que se reanuda a partir del último punto de control.  Este es el comportamiento normal del flujo de trabajo de PowerShell a la hora de suspender o reiniciar.  Si el runbook sobrepasa de nuevo las tres horas de tiempo de ejecución, el proceso se repite hasta tres veces.  Tras el tercer reinicio, si el runbook sigue sin completarse transcurridas tres horas, se producirá un error en el trabajo del runbook y el estado del trabajo aparecerá como **Error, esperando recursos**.  En este caso, recibe la siguiente excepción con el error.

*El trabajo no puede continuar ejecutándose porque se expulsó repetidamente del mismo punto de control. Asegúrese de que el runbook no realiza operaciones largas sin conservar su estado.*

De esta forma se protege el servicio impidiéndose que los runbooks se ejecuten de manera indefinida sin completarse, ya que no pueden llegar al siguiente punto de control sin que se descarguen de nuevo.

Si el runbook no tiene puntos de control o el trabajo no había alcanzado el primer punto de control antes de la descarga, se reinicia desde el principio.  

Cuando se crea un runbook, debe asegurarse de que el tiempo para ejecutar las actividades entre dos puntos de control no supera las tres horas. Puede que necesite agregar puntos de control a un Runbook para asegurarse de que no alcanza este límite de tres horas ni divide operaciones de ejecución prolongada. Por ejemplo, su runbook podría realizar una reindexación en una gran base de datos SQL. Si esta operación no se completa dentro del límite de distribución equilibrada, el trabajo se descarga y se reinicia desde el principio. En este caso, debe dividir la operación de reindexación en varios pasos, como volver a indexar una tabla a la vez y, a continuación, inserte un punto de control después de cada operación, de modo que el trabajo se pueda reanudar después de la última operación para completar.

## <a name="next-steps"></a>pasos siguientes
* Para más información sobre los distintos métodos que se pueden usar para iniciar un runbook en Azure Automation, consulte [Inicio de un runbook en Azure Automation](automation-starting-a-runbook.md)

