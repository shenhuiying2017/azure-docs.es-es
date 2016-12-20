---
title: Programaciones Azure Automation | Microsoft Docs
description: "Las programaciones de Automatización se usan para programar runbooks en Automatización de Azure para que se inicien automáticamente. Describe cómo crear y administrar una programación de modo que pueda iniciar automáticamente un Runbook en un momento determinado o en una programación periódica."
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: 1c2da639-ad20-4848-920b-88e471b2e1d9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: mgoedtel
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 26cbd468e6aa1613bd5f6c9944aedaafbd865a14


---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Programación de un runbook en Automatización de Azure
Para programar un runbook en Automatización de Azure para que se inicie en un momento determinado, vincúlelo a una o más programaciones. Puede configurar una programación para que se ejecute una vez o de forma periódica, cada hora o a diario, para Runbooks en el Portal de Azure clásico y Runbooks en Azure Portal. Además, puede programarlos semanalmente, mensualmente, para determinados días de la semana o del mes, o en un día concreto del mes.  Un runbook puede vincularse a varias programaciones y una programación puede tener varios runbooks vinculados a ella.

> [!NOTE]
> Las programaciones no admiten actualmente las configuraciones de DSC de Automatización de Azure.
> 
> 

## <a name="windows-powershell-cmdlets"></a>Cmdlets de Windows PowerShell
Los cmdlets de la siguiente tabla se usan para crear y administrar programaciones con Windows PowerShell en Automatización de Azure. Se incluyen como parte del [módulo Azure PowerShell](../powershell-install-configure.md).

| Cmdlets | Descripción |
|:--- |:--- |
| **Cmdlets de Azure Resource Manager** | |
| [Get-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603733.aspx) |Recupera una programación. |
| [New-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx) |Crea una nueva programación. |
| [Remove-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603691.aspx) |Quita una programación. |
| [Set-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) |Establece las propiedades de una programación existente. |
| [Get-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt619406.aspx) |Recupera runbooks programados. |
| [Register-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) |Asocia un runbook con una programación. |
| [Unregister-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603844.aspx) |Anula la asociación de un runbook con una programación. |
| **Cmdlets de administración de servicios de Azure** | |
| [Get-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690274.aspx) |Recupera una programación. |
| [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx) |Crea una nueva programación. |
| [Remove-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690279.aspx) |Quita una programación. |
| [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690270.aspx) |Establece las propiedades de una programación existente. |
| [Get-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn913778.aspx) |Recupera runbooks programados. |
| [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690265.aspx) |Asocia un runbook con una programación. |
| [Unregister-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690273.aspx) |Anula la asociación de un runbook con una programación. |

## <a name="creating-a-schedule"></a>Creación de una programación
Puede crear una programación para runbooks en el Portal de Azure, en el Portal de Azure clásico o con Windows PowerShell. También tiene la opción de crear una programación cuando vincule un runbook a una programación mediante el Portal de Azure clásico o el Portal de Azure.

> [!NOTE]
> Cuando asocia una programación a un Runbook, Automatización almacena las versiones actuales de los módulos en su cuenta y las vincula a esa programación.  Esto significa que si tenía un módulo de la versión 1.0 en su cuenta cuando creó una programación y, después, actualizó el módulo a la versión 2.0, la programación seguirá usando 1.0.  Para usar la versión actualizada del módulo, debe crear una nueva programación. 
> 
> 

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Para crear una programación en el Portal de Azure
1. En Azure Portal, desde la cuenta de Automation, haga clic en el icono **Recursos** para abrir la hoja **Recursos**.
2. Haga clic en el icono **Programaciones** para abrir la hoja **Programaciones**.
3. Haga clic en **Agregar una programación** en la parte superior de la hoja.
4. En la hoja **Nueva programación**, escriba un valor en **Nombre** y, opcionalmente, en **Descripción**, para la nueva programación.
5. Seleccione si la programación se ejecutará una vez o de forma periódica seleccionando **Una vez** o **Periodicidad**.  Si selecciona **Una vez**, especifique una hora en **Hora de inicio** y haga clic en **Crear**.  Si selecciona **Periodicidad**, especifique un valor en **Hora de inicio** y la frecuencia con que quiere que se repita el Runbook: cada **hora**, **día**, **semana** o **mes**.  Si selecciona **semana** o **mes** en la lista desplegable, la opción **Periodicidad** aparecerá en la hoja. Tras la selección, aparecerá la hoja de la opción **Periodicidad**, donde puede seleccionar el día de la semana si seleccionó **semana**.  Si seleccionó **mes**, puede elegir **días de la semana** o días específicos del mes en el calendario y, por último, si quiere ejecutarla el último día del mes o no. Después, haga clic en **Aceptar**.   

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>Para crear una programación en el Portal de Azure clásico
1. En el Portal de Azure clásico, seleccione Automatización y después elija el nombre de una cuenta de Automatización.
2. Seleccione la pestaña **Recursos** .
3. En la parte inferior de la ventana, haga clic en **Agregar configuración**.
4. Haga clic en **Agregar programación**.
5. Escriba un valor en **Nombre** y, opcionalmente, en **Descripción** para la nueva programación. Esta se ejecutará con una de estas frecuencias: **Una vez**, **Cada hora**, **Diariamente**, **Semanalmente** o **Mensualmente**.
6. Especifique una **Hora de inicio** y las demás opciones, según el tipo de programación seleccionada.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Para crear una nueva programación con Windows PowerShell
Puede usar el cmdlet [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) para crear una programación en Azure Automation para Runbooks clásicos o el cmdlet [New-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx) para Runbooks en Azure Portal. Debe especificar la hora de inicio de la programación y la frecuencia con que se debe ejecutar.

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
Un runbook puede vincularse a varias programaciones y una programación puede tener varios runbooks vinculados a ella. Si un runbook tiene parámetros, puede proporcionar valores para ellos. Debe proporcionar valores para los parámetros obligatorios y puede proporcionar valores para los parámetros opcionales.  Estos valores se usarán cada vez que esta programación inicia el runbook.  Puede adjuntar el mismo runbook a otra programación y especificar distintos valores de parámetros.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Para vincular una programación a un runbook con el Portal de Azure
1. En Azure Portal, desde la cuenta de Automation, haga clic en el icono **Runbooks** para abrir la hoja **Runbooks**.
2. Haga clic en el nombre del runbook que se va a programar.
3. Si el runbook no está vinculado actualmente a una programación, se le ofrecerá la opción de crear una o vincularlo a una existente.  
4. Si el Runbook tiene parámetros, puede seleccionar la opción **Modificar la configuración de ejecución (Predeterminada: Azure)**; aparece la hoja **Parámetros**, donde puede especificar la información según corresponda.  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>Para vincular una programación a un runbook con el Portal de Azure clásico
1. En el Portal de Azure clásico, seleccione **Automatización** y haga clic en el nombre de una cuenta de Automatización.
2. Seleccione la pestaña **Runbooks** .
3. Haga clic en el nombre del runbook que se va a programar.
4. Haga clic en la pestaña **Programar** .
5. Si el Runbook no está vinculado a una programación, se le ofrecerá la opción de **Vincular a una nueva programación** o **Vincular a una programación existente**.  Si el runbook está vinculado a una programación, haga clic en **Vincular** en la parte inferior de la ventana para acceder a estas opciones.
6. Si el runbook tiene parámetros, se le pedirán sus valores.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Para vincular una programación a un runbook con Windows PowerShell
Puede usar el cmdlet [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) para vincular una programación a un Runbook clásico o [Register-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) para Runbooks en Azure Portal.  Puede especificar valores para los parámetros del runbook con el parámetro Parameters. Consulte [Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md) para obtener más información sobre cómo especificar valores de parámetro.

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
Cuando se deshabilita una programación, los runbooks vinculados a ella no se ejecutarán en dicha programación. Puede deshabilitar una programación manualmente o establecer una fecha de expiración para las programaciones con frecuencia al crearlas. Cuando se alcanza la fecha de expiración, se deshabilitará la programación.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Para deshabilitar una programación desde el Portal de Azure
1. En Azure Portal, desde la cuenta de Automation, haga clic en el icono **Recursos** para abrir la hoja **Recursos**.
2. Haga clic en el icono **Programaciones** para abrir la hoja **Programaciones**.
3. Haga clic en el nombre de una programación para abrir la hoja de detalles.
4. Cambie **Habilitado** a **No**.

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>Para deshabilitar una programación desde el Portal de Azure clásico
Puede deshabilitar una programación en el Portal de Azure clásico en su página Detalles de programación.

1. En el Portal de Azure clásico, seleccione Automatización y haga clic en el nombre de una cuenta de Automatización.
2. Seleccione la pestaña Recursos.
3. Haga clic en el nombre de una programación para abrir la página de detalles.
4. Cambie **Habilitado** a **No**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Para deshabilitar una programación con Windows PowerShell
Puede usar el cmdlet [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) para cambiar las propiedades de una programación existente para un Runbook clásico o [Set-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) para Runbooks en Azure Portal. Para deshabilitar la programación, especifique **false** para el parámetro **IsEnabled**.

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

## <a name="next-steps"></a>Pasos siguientes
* Para empezar a trabajar con Runbooks, vea [Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md) 




<!--HONumber=Nov16_HO3-->


