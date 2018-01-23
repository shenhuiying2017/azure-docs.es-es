---
title: Salidas de runbook y mensajes en Azure Automation | Microsoft Docs
description: "Describe cómo crear y recuperar los mensajes de salida y error de los runbooks en Azure Automation."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 13a414f5-0e2c-4be2-9558-a3e3ec84b6b2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/11/2016
ms.author: magoedte;bwren
ms.openlocfilehash: 415eddaec9702a42ceee51858a39840fcd6a202b
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Salidas de runbook y mensajes en Azure Automation
La mayoría de los runbooks de Azure Automation generan alguna forma de salida, como el mensaje de error que verá el usuario o un objeto complejo que será consumido por otro flujo de trabajo. Windows PowerShell le ofrece [varios flujos](http://blogs.technet.com/heyscriptingguy/archive/2014/03/30/understanding-streams-redirection-and-write-host-in-powershell.aspx) para enviar la salida desde un script o flujo de trabajo. Azure Automation funciona con cada uno de estos flujos de forma diferente, y es aconsejable que siga los procedimientos recomendados sobre cómo usar cada uno de ellos cuando cree un runbook.

En la siguiente tabla verá una breve descripción de cada uno de los flujos y su comportamiento en Azure Portal, cuando se ejecuta un runbook publicado y cuando se [prueba un runbook](automation-testing-runbook.md). Asimismo, en las siguientes secciones se proporciona más información sobre cada flujo.

| Stream | DESCRIPCIÓN | Publicado | Prueba |
|:--- |:--- |:--- |:--- |
| Salida |Objetos destinados a ser consumidos por otros runbooks. |Se escribe en el historial de trabajos. |Se muestra en el panel de salida de la prueba. |
| Warning (Advertencia) |Mensaje de advertencia destinado al usuario. |Se escribe en el historial de trabajos. |Se muestra en el panel de salida de la prueba. |
| Error |Mensaje de error destinado al usuario. A diferencia de lo que sucede con las excepciones, el runbook continúa después de un mensaje de error de forma predeterminada. |Se escribe en el historial de trabajos. |Se muestra en el panel de salida de la prueba. |
| Detallado |Mensajes que proporcionan información general o de depuración. |Solo se escribe en el historial de trabajos si el registro detallado del runbook está activado. |Solo se muestra en el panel de salida de la prueba si el elemento $VerbosePreference está establecido como “Continue” en el runbook. |
| Progreso |Registros que se generan automáticamente antes y después de cada actividad del runbook. El runbook no debe intentar crear sus propios registros de progreso, ya que están dirigidos a un usuario interactivo. |Solo se escribe en el historial de trabajos si el registro del progreso del runbook está activado. |No se muestra en el panel de salida de la prueba. |
| Depurar |Mensajes dirigidos a un usuario interactivo. No debe usarse en runbooks. |No se escribe en el historial de trabajos. |No se escribe en el panel de salida de la prueba. |

## <a name="output-stream"></a>Flujo de salida
El flujo de salida está diseñado para la salida de los objetos creados por un script o flujo de trabajo, cuando se ejecuta correctamente. En Azure Automation, este flujo se usa principalmente para objetos destinados a ser consumidos por [runbooks primarios que llaman al runbook actual](automation-child-runbooks.md). Cuando [llama a un runbook en línea](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) desde un runbook primario, este devuelve los datos del flujo de salida al runbook primario. Solo debe usar el flujo de salida para comunicar información general al usuario si sabe que ningún otro runbook nunca llamará a ese runbook. Le aconsejamos, como procedimiento recomendado, que use la opción de [Flujo detallado](#Verbose) para comunicar información general al usuario.

Puede escribir datos en el flujo de salida mediante [Write-Output](http://technet.microsoft.com/library/hh849921.aspx) o colocando el objeto en su propia línea en el runbook.

    #The following lines both write an object to the output stream.
    Write-Output –InputObject $object
    $object

### <a name="output-from-a-function"></a>Salida de una función
Cuando escribe en el flujo de salida de una función que está incluida en su runbook, la salida se devuelve al runbook. Si el runbook asigna esa salida a una variable, no se escribe en el flujo de salida. Asimismo, si se escribe en cualquier otro flujo desde la función, se escribirá en el flujo correspondiente del runbook.

Échele un vistazo al siguiente runbook de ejemplo:

    Workflow Test-Runbook
    {
        Write-Verbose "Verbose outside of function" -Verbose
        Write-Output "Output outside of function"
        $functionOutput = Test-Function
        $functionOutput

    Function Test-Function
     {
        Write-Verbose "Verbose inside of function" -Verbose
        Write-Output "Output inside of function"
      }
    }


El flujo de salida del trabajo del runbook sería:

    Output inside of function
    Output outside of function

El flujo detallado del trabajo del runbook sería:

    Verbose outside of function
    Verbose inside of function

Una vez que haya publicado el Runbook y antes de iniciarlo, debe activar también el registro detallado en la configuración del Runbook para obtener la salida de flujo detallada.

### <a name="declaring-output-data-type"></a>Declarar los tipos de datos de salida
Un flujo de trabajo puede especificar el tipo de datos de su salida mediante el [atributo OutputType](http://technet.microsoft.com/library/hh847785.aspx). Este atributo no tiene ningún efecto durante el tiempo de ejecución, pero proporciona al autor del runbook una indicación de la salida esperada en tiempo de diseño. A medida que el conjunto de herramientas de los runbooks evoluciona, aumenta la importancia de la declaración de tipos de datos de salida en tiempo de diseño. Como resultado, es recomendable incluir esta declaración en cualquier runbook que cree.

Esta es una lista de tipos de salidas de ejemplo:

* System.String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

El siguiente runbook de ejemplo genera un objeto de cadena e incluye una declaración de su tipo de salida. Si su runbook genera una matriz de un tipo determinado, deberá especificar el tipo en lugar de una matriz de ese tipo.

    Workflow Test-Runbook
    {
       [OutputType([string])]

       $output = "This is some string output."
       Write-Output $output
    }

Para declarar un tipo de salida en Runbooks gráficos o de flujo de trabajo gráfico de PowerShell, puede seleccionar la opción de menú **Entrada y salida** y escribir el nombre del tipo de salida. Se recomienda que utilice el nombre completo de clase .NET para identificarlo fácilmente cuando haga referencia a él desde un Runbook primario. Esto permite exponer todas las propiedades de esa clase en el bus de datos del Runbook y proporciona mucha flexibilidad cuando se usan para la lógica condicional, el registro y la referencia como valores de otras actividades del Runbook.<br> ![Opción de entrada y salida de Runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

En el siguiente ejemplo, tenemos dos Runbooks gráficos para demostrar esta característica. Si se aplica el modelo de diseño modular de runbook, tendremos un runbook que actúa como *plantilla de runbook de autenticación* que administra la autenticación con Azure mediante la cuenta de ejecución. El segundo Runbook, que normalmente realizaría la lógica básica para automatizar un escenario determinado, en este caso va a ejecutar la *plantilla de Runbook de autenticación* y a mostrar los resultados en el panel de salida **Prueba** .  En circunstancias normales, haríamos que este Runbook hiciera algo en un recurso que está aprovechando la salida del Runbook secundario.    

Esta es la lógica básica del runbook **AuthenticateTo-Azure**.<br> ![Autenticar ejemplo de plantilla de Runbook](media/automation-runbook-output-and-messages/runbook-authentication-template.png).  

Incluye el tipo de salida *Microsoft.Azure.Commands.Profile.Models.PSAzureContext*, que devolverá las propiedades del perfil de autenticación.<br> ![Ejemplo de tipo de salida de Runbooks](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

Aunque este runbook es muy simple, hay un elemento de configuración al que se debe llamar aquí. La última actividad está ejecutando el cmdlet **Write-Output** y escribe los datos de perfil en una variable $_ mediante una expresión de PowerShell para el parámetro **Inputobject** necesario para ese cmdlet.  

Para el segundo runbook de este ejemplo, denominado *Test-ChildOutputType*, solo tenemos dos actividades.<br> ![Runbook de tipo de salida secundario de ejemplo](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

La primera actividad llama al runbook **AuthenticateTo-Azure** y la segunda actividad está ejecutando el cmdlet **Write-Verbose** con el **origen de datos** de **Resultado de la actividad** y el valor de **Ruta de acceso de campo** es **Context.Subscription.SubscriptionName**, que especifica la salida de contexto del runbook **AuthenticateTo-Azure**.<br> ![Origen de datos de parámetro del cmdlet Write-Verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

La salida resultante es el nombre de la suscripción.<br> ![Resultados de Runbook de Test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

Una nota acerca del comportamiento del control del tipo de salida. Cuando escribe un valor en el campo Tipo de salida en la hoja de propiedades Entrada y salida, tiene que hacer clic fuera del control después de escribirlo para que el control reconozca la entrada.  

## <a name="message-streams"></a>Flujos de mensajes
A diferencia del flujo de salida, los flujos de mensajes están diseñados para comunicar información al usuario. Hay varios flujos de mensajes para los diferentes tipos de información, y Azure Automation controla cada uno de ellos de forma diferente.

### <a name="warning-and-error-streams"></a>Flujos de error y de advertencia
Los flujos de error y de advertencia están diseñados para registrar los problemas que se producen en un runbook. Se escriben en el historial de trabajos cuando se ejecuta un runbook y se incluyen en el panel de salida de la prueba de Azure Portal cuando se prueba un runbook. De forma predeterminada, el runbook continuará ejecutándose después de un error o advertencia. Para especificar que el runbook debe suspenderse en caso de advertencia o error, puede establecer una [variable de preferencia](#PreferenceVariables) en el runbook antes de crear el mensaje. Por ejemplo, para hacer que un runbook se suspenda en caso de error, tal y como haría una excepción, establezca **$ErrorActionPreference** en Stop.

Cree un mensaje de advertencia o de error mediante los cmdlets [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) o [Write-Error](http://technet.microsoft.com/library/hh849962.aspx). También se pueden escribir actividades en estos flujos.

    #The following lines create a warning message and then an error message that will suspend the runbook.

    $ErrorActionPreference = "Stop"
    Write-Warning –Message "This is a warning message."
    Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."

### <a name="verbose-stream"></a>Flujo detallado
El flujo de mensajes detallados se usa para proporcionar información general acerca del funcionamiento del runbook. Como los runbooks no tienen disponible el [Flujo de depuración](#Debug) , debe usar mensajes detallados para proporcionar información referente a la depuración. De forma predeterminada, los mensajes detallados de los runbooks publicados no se almacenarán en el historial de trabajos. Para almacenar mensajes detallados, configure los runbooks publicados mediante la opción “Escribir registros detallados” que se encuentra en la pestaña “Configurar” del runbook, en el Portal de administración de Azure. En la mayoría de los casos, deberá mantener la configuración predeterminada y no escribir los registros detallados de un runbook por motivos de rendimiento. Active esta opción solo para solucionar problemas o para depurar un runbook.

Cuando se [prueba un runbook](automation-testing-runbook.md), los mensajes detallados no se muestran aunque el runbook esté configurado para escribir registros detallados. Para mostrar mensajes detallados al [probar un runbook](automation-testing-runbook.md), debe establecer la variable $VerbosePreference en “Continue”. Cuando esa variable está establecida, los mensajes detallados se muestran en el panel de salida de la prueba del Portal de Azure.

Cree un mensaje detallado mediante el cmdlet [Write-Verbose](http://technet.microsoft.com/library/hh849951.aspx) .

    #The following line creates a verbose message.

    Write-Verbose –Message "This is a verbose message."

### <a name="debug-stream"></a>Flujo de depuración
El flujo de depuración está diseñado para usarse con un usuario interactivo y no debe usarse en runbooks.

## <a name="progress-records"></a>Registros de progreso
Si configura un runbook para escribir registros de progreso (en la pestaña Configurar del runbook del Portal de Azure), se escribirá un registro en el historial de trabajos antes y después de la ejecución de cada actividad. En la mayoría de los casos, deberá mantener la configuración predeterminada y no escribir los registros de progreso de un runbook para así maximizar el rendimiento. Active esta opción solo para solucionar problemas o para depurar un runbook. Cuando se prueba un runbook, los mensajes de progreso no se muestran aunque el runbook esté configurado para escribir registros de progreso.

El cmdlet [Write-Progress](http://technet.microsoft.com/library/hh849902.aspx) no es válido en los runbooks ya que está pensado para usarse con un usuario interactivo.

## <a name="preference-variables"></a>Variables de preferencia
Windows PowerShell usa [variables de preferencia](http://technet.microsoft.com/library/hh847796.aspx) para determinar cómo responder a los datos que se hayan enviado a diferentes flujos de salida. Puede establecer estas variables en un runbook para controlar cómo responderá a los datos que se hayan enviado a diferentes flujos.

En la siguiente tabla se enumeran las variables de preferencia que pueden usarse en runbooks, junto con sus valores válidos y predeterminados. Tenga en cuenta que esta tabla solo incluye los valores que son válidos en un runbook Hay otros valores adicionales que son válidos para las variables de preferencia cuando se usan en Windows PowerShell fuera de Azure Automation.

| Variable | Valor predeterminado | Valores válidos |
|:--- |:--- |:--- |
| WarningPreference |Continuar |Stop<br>Continuar<br>SilentlyContinue |
| ErrorActionPreference |Continuar |Stop<br>Continuar<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Stop<br>Continuar<br>SilentlyContinue |

En la siguiente tabla se muestra el comportamiento de los valores de las variables de preferencia que son válidos en los runbooks.

| Valor | Comportamiento |
|:--- |:--- |
| Continuar |Registra el mensaje y continúa ejecutando el runbook. |
| SilentlyContinue |Continúa la ejecución del runbook sin registrar el mensaje. Hace que el mensaje se pase por alto. |
| Stop |Registra el mensaje y suspende el runbook. |

## <a name="retrieving-runbook-output-and-messages"></a>Recuperar salidas de runbooks y mensajes
### <a name="azure-portal"></a>Azure Portal
Puede ver los detalles de un trabajo de runbook en el Portal de Azure de la pestaña llamada Trabajos de un runbook. La opción Resumen del trabajo le muestra los parámetros de entrada y el [Flujo de salida](#Output), además de información general sobre el trabajo y las excepciones que se han producido. En la opción Historial se incluyen los mensajes del [Flujo de salida](#Output) y los [Flujos de error y de advertencia](#WarningError), además del [Flujo detallado](#Verbose) y los [Registros de progreso](#Progress), si el runbook está configurado para escribir registros detallados y de progreso.

### <a name="windows-powershell"></a>Windows PowerShell
En Windows PowerShell, puede recuperar la salida y los mensajes de un runbook con el cmdlet [Get-AzureAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx) . Este cmdlet requiere la identificación del trabajo y tiene un parámetro denominado Stream en el cual se especifica qué flujo hay que devolver. Puede elegir la opción “Cualquiera” para devolver todos los flujos del trabajo.

En el ejemplo siguiente se inicia un runbook y, a continuación, se espera a que finalice. Una vez completado, el flujo de salida se recopila del trabajo.

    $job = Start-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

    $doLoop = $true
    While ($doLoop) {
       $job = Get-AzureRmAutomationJob -ResourceGroupName "ResourceGroup01" `
       –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
       $status = $job.Status
       $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
    }

    Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output
    
    # For more detailed job output, pipe the output of Get-AzureRmAutomationJobOutput to Get-AzureRmAutomationJobOutputRecord
    Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzureRmAutomationJobOutputRecord
    

### <a name="graphical-authoring"></a>Creación gráfica
Para Runbooks gráficos, hay disponible un registro adicional en forma de seguimiento del nivel de actividad.  Hay dos niveles de seguimiento: básico y detallado.  En el seguimiento básico, puede ver la hora de inicio y de finalización de cada actividad en el Runbook, así como información relacionada con los reintentos de actividad, como el número de intentos y la hora de inicio de la actividad.  En el seguimiento detallado, obtendrá un seguimiento básico y, además, datos de entrada y de salida para cada actividad.  Tenga en cuenta que actualmente los registros de seguimiento se escriben usando el flujo detallado, por lo que debe habilitar el registro detallado cuando habilite el seguimiento.  En el caso de los Runbooks gráficos con el seguimiento habilitado no es necesario registrar los registros de progreso, ya que el seguimiento básico tiene la misma finalidad y es más informativo.

![Vista de secuencias de trabajos de creación de gráficos](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Puede ver en la captura de pantalla anterior que, cuando se habilita el registro detallado y el seguimiento para los Runbooks gráficos, hay mucha más información disponible en la vista de secuencias de trabajos de producción.  Esta información adicional puede ser esencial para solucionar problemas de producción con un Runbook. Por lo tanto, solo debería habilitarlo con este fin, y no hacerlo como regla general. Los registros de seguimiento pueden ser especialmente numerosos.  Con el seguimiento de runbooks gráficos puede obtener de dos a cuatro registros por actividad, en función de si configuró el seguimiento básico o detallado.  A menos que necesite esta información para realizar un seguimiento del progreso de un Runbook para solucionar problemas, le conviene mantener el seguimiento desactivado.

**Para habilitar el seguimiento del nivel de actividad, siga estos pasos.**

1. En Azure Portal, abra su cuenta de Automation.
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. En la hoja Runbooks, haga clic para seleccionar un Runbook gráfico de la lista.
4. En la hoja Configuración del Runbook seleccionado, haga clic en **Registro y seguimiento**.
5. En la hoja Registro y seguimiento, en Registrar registros detallados, haga clic en **Activar** para habilitar el registro detallado. En Seguimiento del nivel de actividad, cambie el nivel de seguimiento a **Básico** o **Detallado**, en función del nivel de seguimiento que necesite.<br>
   
   ![Hoja de registro y seguimiento de creación de gráficos](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-operations-management-suite-oms-log-analytics"></a>Microsoft Operations Management Suite (OMS) Log Analytics
Automation puede enviar el estado de un trabajo del runbook y de transmisiones de trabajos al área de trabajo devLog Analytics de Microsoft Operations Management Suite (OMS).  Con Log Analytics puede:

* Obtener información sobre los trabajos de Automation 
* Desencadenar un correo electrónico o una alerta en función de en el estado de trabajo del runbook (por ejemplo, error o en suspensión) 
* Escribir consultas avanzadas en las transmisiones de trabajos 
* Correlacionar trabajos en cuentas de Automation 
* Visualizar el historial de trabajos a lo largo del tiempo    

Para más información sobre cómo configurar la integración con Log Analytics para recopilar, poner en correlación y actuar con los datos de trabajo, vea [Reenvío del estado de un trabajo y de transmisiones de trabajos de Automation a Log Analytics (OMS)](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>pasos siguientes
* Para más información acerca de la ejecución de un runbook, cómo supervisar trabajos del runbook y otros detalles técnicos, consulte [Ejecución de un runbook en Automatización de Azure](automation-runbook-execution.md)
* Para comprender cómo diseñar y usar runbooks secundarios, consulte [runbooks secundarios en Azure Automation](automation-child-runbooks.md)

