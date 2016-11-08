---
title: Runbooks secundarios en la Automatización de Azure | Microsoft Docs
description: Describe los diferentes métodos para iniciar un runbook en la Automatización de Azure desde otro runbook y compartir información entre ellos.
services: automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: tysonn

ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/17/2016
ms.author: magoedte;bwren

---
# Runbooks secundarios en la Automatización de Azure
Un procedimiento recomendado en la Automatización de Azure es escribir runbooks que sean reutilizables y modulares con una función independiente que puedan usar otros runbooks. Con frecuencia, un runbook primario llamará a uno o varios runbooks secundarios para realizar la funcionalidad necesaria. Existen dos maneras de llamar a un runbook secundario y cada una tiene varias diferencias que debería conocer para poder determinar cuál de ellas es mejor para los diferentes escenarios.

## Invocación de un runbook secundario mediante la ejecución en línea
Para invocar un runbook en línea desde otro runbook, use el nombre del runbook y proporcione valores para sus parámetros, exactamente igual a como usaría una actividad o un cmdlet. De esta manera, todos los runbooks de la misma cuenta de Automatización estarán disponibles para que todos los demás los usen. El runbook principal esperará a que el runbook secundario se complete antes de pasar a la siguiente línea, y cualquier resultado se devolverá directamente al elemento primario.

Al invocar un runbook en línea, este se ejecuta en el mismo trabajo que el runbook primario. No habrá ninguna indicación en el historial de trabajos del runbook secundario que se haya ejecutado. Cualquier excepción y salida de flujo pertenecientes al runbook secundario, se asociarán al primario. Como resultado, hay menos trabajos y se facilita el seguimiento y la solución de problemas, ya que las excepciones producidas por el runbook secundario y la salida de flujo se asocian al trabajo primario.

Cuando se publica un runbook, cualquier runbook secundario al que llame deberá estar ya estar publicado. Esto es así porque la Automatización de Azure crea una asociación con los runbooks secundarios cuando se compila un runbook. Si no, parecerá que el runbook primario se publica correctamente, pero generará una excepción cuando se inicie. Si esto sucediera, puede volver a publicar el runbook primario para que haga referencia correctamente a los runbooks secundarios. No es necesario que vuelva a publicar el runbook primario si cambia uno de los runbooks secundarios, porque ya se habrá creado la asociación.

Los parámetros de un Runbook secundario en línea pueden ser de cualquier tipo de datos (incluidos los objetos complejos), y no hay ninguna [serialización JSON](automation-starting-a-runbook.md#runbook-parameters) como sucede cuando se inicia el Runbook mediante el Portal de administración de Azure o mediante el cmdlet Start-AzureRmAutomationRunbook cmdlet.

### Tipos de runbook
Qué tipos pueden llamarse entre sí:

* Un [runbook de PowerShell](automation-runbook-types.md#powershell-runbooks) y los [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) pueden llamarse entre sí en línea (ambos tipos se basan en PowerShell).
* Un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) y los runbooks gráficos de flujo de trabajo de PowerShell pueden llamarse entre sí en línea (ambos tipos se basan en el flujo de trabajo de PowerShell).
* Los tipos de PowerShell y los tipos de flujo de trabajo de PowerShell no pueden llamarse entre sí en línea, y deben utilizar Start-AzureRmAutomationRunbook.

Cuándo es importante el orden de publicación:

* El orden de publicación de runbooks solo es importante para los runbooks de flujo de trabajo de PowerShell y los runbooks gráficos de flujo de trabajo de PowerShell.

Cuando se llama a un runbook secundario gráfico o de flujo de trabajo de PowerShell con la ejecución insertada, solo tiene que usar el nombre del runbook. Cuando se llama a un runbook secundario de PowerShell, debe preceder su nombre con *.\* para especificar que el script se encuentra en el directorio local.

### Ejemplo
En el ejemplo siguiente se invoca un runbook secundario de prueba que acepta tres parámetros: un objeto complejo, un entero y un valor booleano. Los resultados del runbook secundario se asignan a una variable. En este caso, el runbook secundario es un runbook de flujo de trabajo de PowerShell.

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

A continuación, se muestra el mismo ejemplo con un runbook de PowerShell como elemento secundario.

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = .\PS-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true



## Inicio de un runbook secundario mediante un cmdlet
Puede utilizar el cmdlet [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) para iniciar un runbook tal como se describe en [Inicio de un runbook con Windows PowerShell](automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Existen dos modos de usar este cmdlet. En un modo, el cmdlet devuelve el identificador del trabajo en cuanto se crea el trabajo secundario para el Runbook secundario. En el otro, que se habilita mediante la especificación del parámetro **-wait**, el cmdlet esperará a que finalice el trabajo secundario y devolverá los resultados del runbook secundario.

Si inició el trabajo de un runbook secundario con un cmdlet, este se ejecutará en un trabajo independiente al runbook primario. Como resultado, habrá más trabajos que cuando se invoca el runbook insertado, por lo que realizar un seguimiento será más difícil. Asimismo, el primario puede iniciar varios Runbooks secundarios de forma asincrónica sin tener que esperar a que se complete cada uno de ellos. Para realizar este mismo tipo de ejecución en paralelo, al llamar a los runbooks secundarios en línea, el runbook primario necesitará usar la [palabra clave parallel](automation-powershell-workflow.md#parallel-processing).

Los parámetros de un runbook secundario iniciado con un cmdlet se proporcionan como una tabla hash, tal y como se describe en [Parámetros de runbook](automation-starting-a-runbook.md#runbook-parameters). Solo pueden usarse los tipos de datos simples. Si el runbook tiene un parámetro con un tipo de datos complejo, debe llamarse en línea.

### Ejemplo
En el ejemplo siguiente se inicia un Runbook secundario con parámetros y se espera a que finalice mediante el parámetro Start-AzureRmAutomationRunbook -wait. Una vez completado, los resultados se recopilan desde el Runbook secundario.

    $params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
    $joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResouceGroupName "LabRG" –Parameters $params –wait


## Comparación de métodos para llamar a un runbook secundario
En la siguiente tabla se resumen las diferencias entre los dos métodos para llamar a un runbook desde otro runbook.

|  | En línea | Cmdlet |
|:--- |:--- |:--- |
| Trabajo |Los runbooks secundarios se ejecutan en el mismo trabajo que el primario. |Se crea un trabajo independiente para el runbook secundario. |
| Ejecución |El runbook primario espera a que el runbook secundario se complete antes de continuar. |El runbook primario continúa inmediatamente después de que se inicie el runbook secundario *o* el runbook primario espera a que finalice el trabajo secundario. |
| Salida |El runbook primario puede obtener los resultados directamente del runbook secundario. |El runbook primario debe obtener los resultados directamente del trabajo de runbook secundario *o* el runbook primario puede obtener los resultados directamente del runbook secundario. |
| Parámetros |Los valores de los parámetros del runbook secundario se especifican por separado y pueden usar cualquier tipo de datos. |Los valores de los parámetros del runbook secundario deben combinarse en una sola tabla hash y solo pueden incluir tipos de datos simples, de matriz y de objeto que utilicen la serialización JSON. |
| Cuenta de automatización |El runbook primario solo puede usar el runbook secundario en la misma cuenta de automatización. |El runbook primario puede usar el runbook secundario desde cualquier cuenta de automatización de la misma suscripción de Azure, e incluso de otra suscripción si tiene una conexión a ella. |
| Publicación |El runbook secundario debe publicarse antes de publicar el runbook primario. |El runbook secundario debe publicarse en cualquier momento antes de iniciar el runbook primario. |

## Pasos siguientes
* [Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md)
* [Salidas de runbook y mensajes en la Automatización de Azure](automation-runbook-output-and-messages.md)

<!---HONumber=AcomDC_0817_2016-->