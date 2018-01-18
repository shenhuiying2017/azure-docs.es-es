---
title: Programaciones Azure Automation | Microsoft Docs
description: "Las programaciones de Automation se usan para programar runbooks en Azure Automation para que se inicien automáticamente. Describe cómo crear y administrar una programación de modo que pueda iniciar automáticamente un Runbook en un momento determinado o en una programación periódica."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 1c2da639-ad20-4848-920b-88e471b2e1d9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: 6ad70d736cd0a267ace3ade0a1ecfea38128ac72
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Programación de un runbook en Azure Automation
Para programar un runbook en Azure Automation para que se inicie en un momento determinado, vincúlelo a una o más programaciones. Es posible configurar una programación para que se ejecute una vez o según una programación diaria o por hora recurrente para los runbooks en Azure Portal. También las puede programar para que se ejecuten una vez a la semana, una vez al mes, en días específicos de la semana o del mes, o bien en un día determinado del mes.  Un runbook puede vincularse a varias programaciones y una programación puede tener varios runbooks vinculados a ella.

> [!NOTE]
> Las programaciones no admiten actualmente las configuraciones de DSC de Azure Automation.
> 
> 

## <a name="windows-powershell-cmdlets"></a>Cmdlets de Windows PowerShell
Los cmdlets de la siguiente tabla se usan para crear y administrar programaciones con Windows PowerShell en Azure Automation. Se incluyen como parte del [módulo Azure PowerShell](/powershell/azure/overview).

| Cmdlets | DESCRIPCIÓN |
|:--- |:--- |
| **Cmdlets de Azure Resource Manager** | |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Recupera una programación. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Crea una nueva programación. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Quita una programación. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Establece las propiedades de una programación existente. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/set-azurermautomationscheduledrunbook) |Recupera runbooks programados. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Asocia un runbook con una programación. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Anula la asociación de un runbook con una programación. |
| **Cmdlets de administración de servicios de Azure** | |
| [Get-AzureAutomationSchedule](/powershell/module/azure/get-azureautomationschedule?view=azuresmps-3.7.0) |Recupera una programación. |
| [New-AzureAutomationSchedule](/powershell/module/azure/new-azureautomationschedule?view=azuresmps-3.7.0) |Crea una nueva programación. |
| [Remove-AzureAutomationSchedule](/powershell/module/azure/remove-azureautomationschedule?view=azuresmps-3.7.0) |Quita una programación. |
| [Set-AzureAutomationSchedule](/powershell/module/azure/set-azureautomationschedule?view=azuresmps-3.7.0) |Establece las propiedades de una programación existente. |
| [Get-AzureAutomationScheduledRunbook](/powershell/module/azure/get-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Recupera runbooks programados. |
| [Register-AzureAutomationScheduledRunbook](/powershell/module/azure/register-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Asocia un runbook con una programación. |
| [Unregister-AzureAutomationScheduledRunbook](/powershell/module/azure/unregister-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Anula la asociación de un runbook con una programación. |

## <a name="creating-a-schedule"></a>Creación de una programación
Puede crear una programación para runbooks en Azure Portal o con Windows PowerShell. También tiene la opción de crear una programación cuando vincule un runbook a una programación mediante el Portal de Azure clásico o el Portal de Azure.

> [!NOTE]
> Azure Automation utiliza los módulos más recientes en su cuenta de Automation al ejecutar un nuevo trabajo programado.  Para evitar afectar a los runbooks y a los procesos que automatizan, primero debe probar los runbooks que tengan programaciones vinculadas con una cuenta de Automation dedicada para realizar pruebas.  De este modo valida que los runbooks programados sigan funcionando correctamente y, si no es así, puede seguir investigando para solucionar el problema y aplicar los cambios necesarios antes de migrar la versión actualizada del runbook al entorno de producción.  
>  Su cuenta de Automation no obtiene automáticamente las nuevas versiones de los módulos, a menos que los actualizara manualmente al seleccionar la opción [Actualizar módulos de Azure](automation-update-azure-modules.md) desde **Módulos**. 
>  

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Para crear una programación en el Portal de Azure
1. En Azure Portal, desde su cuenta de Automation, seleccione **programaciones** en la sección **Recursos compartidos** de la izquierda. 
2. Haga clic en **Agregar una programación** en la parte superior de la página.
4. En el panel **Nueva programación**, escriba un valor en **Nombre** y, opcionalmente, en **Descripción**, para la nueva programación.
5. Seleccione si la programación se ejecuta una vez o de forma periódica seleccionando **Una vez** o **Periodicidad**.  Si selecciona **Una vez**, especifique una hora en **Hora de inicio** y haga clic en **Crear**.  Si selecciona **Periodicidad**, especifique un valor en **Hora de inicio** y la frecuencia con que quiere que se repita el Runbook: cada **hora**, **día**, **semana** o **mes**.  Si selecciona **semana** o **mes** en la lista desplegable, la opción **Periodicidad** aparece en el panel y, tras la selección, aparecerá el panel de la opción **Periodicidad**, donde se selecciona el día de la semana (si seleccionó **semana**).  Si seleccionó **mes**, puede elegir **días de la semana** o días específicos del mes en el calendario y, por último, si desea ejecutarlo el último día del mes o no. Después, haga clic en **Aceptar**.   

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Para crear una nueva programación con Windows PowerShell
Puede usar el cmdlet [New-AzureAutomationSchedule](/powershell/module/azure/new-azureautomationschedule?view=azuresmps-3.7.0) para crear una programación en Azure Automation para Runbooks clásicos o el cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) para Runbooks en Azure Portal. Debe especificar la hora de inicio de la programación y la frecuencia con que se debe ejecutar.

Los siguientes comandos de ejemplo muestran cómo crear una programación para los días 15 y 30 de cada mes con un cmdlet de Azure Resource Manager.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"

Los siguientes comandos de ejemplo muestran cómo crear una programación que se ejecuta cada día a las 3:30 p. m. a partir del 20 de enero de 2015 con un cmdlet de Azure Service Management.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

## <a name="linking-a-schedule-to-a-runbook"></a>Vinculación de una programación a un runbook
Un runbook puede vincularse a varias programaciones y una programación puede tener varios runbooks vinculados a ella. Si un runbook tiene parámetros, puede proporcionar valores para ellos. Debe proporcionar valores para los parámetros obligatorios y puede proporcionar valores para los parámetros opcionales.  Estos valores se usan cada vez que esta programación inicia el runbook.  Puede adjuntar el mismo runbook a otra programación y especificar distintos valores de parámetros.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Para vincular una programación a un runbook con el Portal de Azure
1. En Azure Portal, desde su cuenta de Automation, seleccione **Runbooks** en la sección **Automatización de procesos** de la izquierda.
2. Haga clic en el nombre del runbook que se va a programar.
3. Si el runbook no está vinculado actualmente a una programación, se le ofrecerá la opción de crear una o de vincularlo a una existente.  
4. Si el runbook tiene parámetros, puede seleccionar la opción **Modificar la configuración de ejecución (Predeterminada: Azure)**; aparece el panel **Parámetros**, donde puede especificar la información según corresponda.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Para vincular una programación a un runbook con Windows PowerShell
Puede usar el cmdlet [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) para vincular una programación a un Runbook clásico o [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) para Runbooks en Azure Portal.  Puede especificar valores para los parámetros del runbook con el parámetro Parameters. Consulte [Inicio de un runbook en Azure Automation](automation-starting-a-runbook.md) para obtener más información sobre cómo especificar valores de parámetro.

Los siguientes comandos de ejemplo muestran cómo vincular una programación a un runbook mediante un cmdlet de Azure Resource Manager con parámetros.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"
Los siguientes comandos de ejemplo muestran cómo vincular una programación mediante un cmdlet de Azure Service Management con parámetros.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## <a name="disabling-a-schedule"></a>Deshabilitación de una programación
Cuando se deshabilita una programación, los runbooks vinculados a ella ya no se ejecutan en ella. Puede deshabilitar una programación manualmente o establecer una fecha de expiración para las programaciones con frecuencia al crearlas. Cuando se alcanza la fecha de expiración, la programación se deshabilita.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Para deshabilitar una programación desde el Portal de Azure
1. En Azure Portal, desde su cuenta de Automation, seleccione **programaciones** en la sección **Recursos compartidos** de la izquierda.
2. Haga clic en el nombre de una programación para abrir el panel de detalles.
3. Cambie **Habilitado** a **No**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Para deshabilitar una programación con Windows PowerShell
Puede usar el cmdlet [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) para cambiar las propiedades de una programación existente para un Runbook clásico o [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) para Runbooks en Azure Portal. Para deshabilitar la programación, especifique **false** para el parámetro **IsEnabled**.

Los siguientes comandos de ejemplo muestran cómo deshabilitar una programación para un runbook mediante un cmdlet de Azure Resource Manager.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"

Los siguientes comandos de ejemplo muestran cómo deshabilitar una programación mediante un cmdlet de Azure Service Management.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

## <a name="next-steps"></a>pasos siguientes
* Para empezar a trabajar con Runbooks, vea [Inicio de un runbook en Azure Automation](automation-starting-a-runbook.md) 

