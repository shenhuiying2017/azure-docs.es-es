---
title: Recursos de variables en Azure Automation | Microsoft Docs
description: "Los activos de variables son valores que están disponibles para todos los runbooks y configuraciones de DSC en Azure Automation.  En este artículo se explican los detalles de las variables y cómo trabajar con ellas en la creación de texto y gráficos."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: b880c15f-46f5-4881-8e98-e034cc5a66ec
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/09/2017
ms.author: magoedte;bwren
ms.openlocfilehash: e38d2b751090cfdc078de4e8c683c6bb9b48fac3
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="variable-assets-in-azure-automation"></a>Recursos de variables en Azure Automation

Los activos de variables son valores que están disponibles para todos los runbooks y configuraciones de DSC en su cuenta de Automatización. Pueden crearse, modificarse y recuperarse desde el Portal de Azure, Windows PowerShell y desde un runbook o una configuración de DSC. Las variables de Automation son útiles para los siguientes escenarios:

- Compartir un valor entre varios runbooks o configuraciones de DSC.

- Compartir un valor entre varios trabajos del mismo runbook o configuración de DSC.

- Administrar un valor desde el portal o desde la línea de comandos de Windows PowerShell que usan los runbooks, o bien las configuraciones DSC, como un conjunto de elementos de configuración comunes, como una lista específica de nombres de VM, un grupo de recursos específico, un nombre de dominio de AD, etc.  

Las variables de Automation se conservan para que sigan estando disponibles, incluso si se produce un error en el runbook o la configuración de DSC.  Esto también permite que se establezca un valor por un runbook que se usará por otro o bien se usará por el mismo runbook o configuración de DSC la siguiente vez que se ejecute.

Cuando se crea una variable, puede especificar que se almacene cifrada.  Cuando se cifra una variable, se almacena de forma segura en Azure Automation y su valor no se puede recuperar del cmdlet [Get-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603849.aspx) que se incluye como parte del módulo Azure PowerShell.  Es la única manera de que se puede recuperar un valor cifrado desde la actividad **Get-AutomationVariable** en un runbook o una configuración de DSC.

> [!NOTE]
> Los recursos protegidos en Azure Automation incluyen credenciales, certificados, conexiones y variables cifradas. Estos recursos se cifran y se almacenan en Azure Automation con una clave única que se genera para cada cuenta de Automation. Esta clave se cifra mediante un certificado maestro y se almacena en Azure Automation. Antes de almacenar un recurso seguro, la clave de la cuenta de automatización se descifra con el certificado maestro y, a continuación, se utiliza para cifrar el recurso.

## <a name="variable-types"></a>Tipos de variables

Cuando se crea una variable con el portal de Azure, debe especificar un tipo de datos en la lista desplegable para que el portal pueda mostrar el control adecuado para escribir el valor de la variable. La variable no se limita a este tipo de datos, pero hay que establecer la variable mediante Windows PowerShell si se desea especificar un valor de un tipo diferente. Si especifica **No definido**, el valor de la variable se establecerá en **$null** y deberá establecer el valor con el cmdlet [Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx) o con la actividad **Set-AutomationVariable**.  No se puede crear o cambiar el valor de un tipo complejo de variable en el portal, pero puede proporcionar un valor de cualquier tipo mediante Windows PowerShell. Los tipos complejos se devolverán como un [PSCustomObject](http://msdn.microsoft.com/library/system.management.automation.pscustomobject.aspx).

Puede almacenar varios valores en una única variable mediante la creación de una matriz o tabla hash y guardarlos en la variable.

La siguiente es una lista de los tipos de variables disponibles en Automation:

* string
* Entero
* Datetime
* boolean
* Null

## <a name="scripting-the-creation-and-management-of-variables"></a>Generar scripts para crear y administrar variables

Los cmdlets de la tabla siguiente se usan para crear y administrar variables de Automation con Windows PowerShell. Se incluyen como parte del [módulo Azure PowerShell](../powershell-install-configure.md) que está disponible para su uso en los runbooks de Automation y las configuraciones de DSC.

|Cmdlets|DESCRIPCIÓN|
|:---|:---|
|[Get-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603849.aspx)|Recupera el valor de una variable existente.|
|[New-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603613.aspx)|Crea una nueva variable y establece su valor.|
|[Remove-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt619354.aspx)|Quita una variable existente.|
|[Set-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603601.aspx)|Establece el valor de una variable existente.|

Las actividades de flujo de trabajo en la tabla siguiente se usan para acceder a las variables de Automation en un runbook. Solo están disponibles para su uso en un runbook o una configuración de DSC y no se incluyen como parte del módulo Azure PowerShell.

|Actividades de flujo de trabajo|DESCRIPCIÓN|
|:---|:---|
|Get-AutomationVariable|Recupera el valor de una variable existente.|
|Set-AutomationVariable|Establece el valor de una variable existente.|

> [!NOTE] 
> Debe evitar el uso de variables en el parámetro – Name de **Get-AutomationVariable** en un runbook o una configuración de DSC, ya que puede complicar la detección de dependencias entre runbooks o configuraciones de DSC y las variables de Automation en tiempo de diseño.

Las funciones de la tabla siguiente se usan para obtener acceso a las variables de un runbook de Python2 y recuperarlas. 

|Funciones de Python2|DESCRIPCIÓN|
|:---|:---|
|automationassets.get_automation_variable|Recupera el valor de una variable existente. |
|automationassets.set_automation_variable|Establece el valor de una variable existente. |

> [!NOTE] 
> Debe importar el módulo "automationassets" en la parte superior del runbook de Python para poder tener acceso a las funciones del recurso.

## <a name="creating-a-new-automation-variable"></a>Creación de una nueva variable de Automation

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Para crear una nueva variable con el Portal de Azure

1. En la cuenta de Automation, haga clic en el icono **Activos** y, en la hoja **Activos**, seleccione **Variables**.
2. En la hoja **Variables**, seleccione **Agregar una variable**.
3. Complete las opciones en la hoja **Nueva variable** y haga clic en **Crear** para guardar la nueva variable.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Para crear una nueva variable con Windows PowerShell

El cmdlet [New-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603613.aspx) crea una nueva variable y establece su valor inicial. Puede recuperar el valor mediante [Get-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603849.aspx). Si el valor es un tipo simple, se devuelve ese mismo tipo. Si es un tipo complejo, se devuelve **PSCustomObject** .

Los comandos de ejemplo siguientes muestran cómo crear una variable de tipo cadena y que después devuelva su valor.

    New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
    –Encrypted $false –Value 'My String'
    $string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

Los comandos de ejemplo siguientes muestran cómo crear una variable con un tipo complejo y que después devuelva su valor. En este caso, se usa un objeto de máquina virtual desde **Get-AzureRmVm**.

    $vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
    New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
    
    $vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
    $vmName = $vmValue.Name
    $vmIpAddress = $vmValue.IpAddress



## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Uso de una variable en un runbook o una configuración de DSC

Use la actividad **Set-AutomationVariable** para establecer el valor de una variable de Automation en un runbook de PowerShell o una configuración de DSC, y la actividad **Get-AutomationVariable** para recuperarlo.  No debe usar los cmdlets **Set-AzureAutomationVariable** o **Get-AzureAutomationVariable** en un runbook o una configuración DSC, ya que son menos eficientes que las actividades de flujo de trabajo.  Tampoco puede recuperar el valor de variables seguras con **Get-AzureAutomationVariable**.  La única forma de crear una nueva variable desde un runbook o una configuración de DSC es usar el cmdlet [New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx).


### <a name="textual-runbook-samples"></a>Ejemplos de runbook textual

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Establecimiento y recuperación de un valor simple de una variable

Los comandos de ejemplo siguientes muestran cómo establecer y recuperar una variable en un runbook textual. En este ejemplo, se asume que se crearon las variables de tipo entero llamadas *NumberOfIterations* y *NumberOfRunnings* y una variable de tipo cadena denominada *SampleMessage*.

    $NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
    $NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
    $SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
    
    Write-Output "Runbook has been run $NumberOfRunnings times."
    
    for ($i = 1; $i -le $NumberOfIterations; $i++) {
       Write-Output "$i`: $SampleMessage"
    }
    Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>Establecimiento y recuperación de un objeto completo en una variable

El código de ejemplo siguiente muestra cómo actualizar una variable con un valor complejo en un runbook textual. En este ejemplo, se recupera una máquina virtual de Azure con **Get-AzureVM** y se guarda en una variable de Automation existente.  Como se explica en [Tipos de variables](#variable-types), se almacena como un PSCustomObject.

    $vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
    Set-AutomationVariable -Name "MyComplexVariable" -Value $vm

En el código siguiente, el valor se recupera de la variable y se usa para iniciar la máquina virtual.

    $vmObject = Get-AutomationVariable -Name "MyComplexVariable"
    if ($vmObject.PowerState -eq 'Stopped') {
       Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
    }


#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>Establecimiento y recuperación de una colección en una variable

El código de ejemplo siguiente muestra cómo usar una variable con un colección de valores complejos en un runbook textual. En este ejemplo, se recuperan varias máquinas virtuales de Azure con **Get-AzureVM** y se guardan en una variable de Automation existente.  Como se explica en [Tipos de variables](#variable-types), se almacena como una colección de objetos PSCustomObject.

    $vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

En el código siguiente, la colección se recupera de la variable y se usa para iniciar las máquinas virtuales.

    $vmValues = Get-AutomationVariable -Name "MyComplexVariable"
    ForEach ($vmValue in $vmValues)
    {
       if ($vmValue.PowerState -eq 'Stopped') {
          Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
       }
    }
    
#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Establecimiento y recuperación de una variable en Python2
En el siguiente código de ejemplo se muestra cómo usar una variable, establecer una variable y controlar una excepción de una variable inexistente en un runbook de Python2.

    import automationassets
    from automationassets import AutomationAssetNotFound

    # get a variable
    value = automationassets.get_automation_variable("test-variable")
    print value

    # set a variable (value can be int/bool/string)
    automationassets.set_automation_variable("test-variable", True)
    automationassets.set_automation_variable("test-variable", 4)
    automationassets.set_automation_variable("test-variable", "test-string")

    # handle a non-existent variable exception
    try:
        value = automationassets.get_automation_variable("non-existing variable")
    except AutomationAssetNotFound:
        print "variable not found"


### <a name="graphical-runbook-samples"></a>Ejemplos de runbook gráfico

En un runbook gráfico, agregue **Get-AutomationVariable** o **Set-AutomationVariable** haciendo clic con el botón derecho en la variable en el panel Biblioteca del editor gráfico y seleccionando la actividad que desee.

![Adición de una variable al lienzo](media/automation-variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Configuración de valores de una variable
La imagen siguiente muestra las actividades de ejemplo para actualizar una variable con un valor simple en un runbook gráfico. En este ejemplo, se recupera una única máquina virtual de Azure con **Get-AzureRmVM** y se guarda el nombre del equipo en una variable de Automation existente con un tipo de cadena.  No importa si el [vínculo es una canalización o una secuencia](automation-graphical-authoring-intro.md#links-and-workflow) , puesto que solo esperamos un único objeto en la salida.

![Establecimiento de una variable simple](media/automation-variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre cómo conectar actividades en la creación gráfica, consulte [Vínculos y flujo de trabajo en Creación gráfica en Automatización de Azure](automation-graphical-authoring-intro.md#links-and-workflow)
* Para empezar a trabajar con cuadernos gráficos, consulte [Mi primer runbook gráfico](automation-first-runbook-graphical.md) 

