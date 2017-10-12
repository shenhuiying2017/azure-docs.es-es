---
title: Aprendizaje del flujo de trabajo de Windows PowerShell para Azure Automation | Microsoft Docs
description: "Este artículo está destinado como una lección rápida para que los autores familiarizados con PowerShell comprendan las diferencias específicas entre Powershell y el flujo de trabajo de PowerShell y los conceptos aplicables a los runbooks de Automation."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 84bf133e-5343-4e0e-8d6c-bb14304a70db
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 6dce88bdd85a28ce05e1621b08a0f4b148b02627
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Aprendizaje de los conceptos básicos del flujo de trabajo de Windows PowerShell para los runbooks de Automation 
Los runbooks de Azure Automation se implementan como flujos de trabajo de Windows PowerShell.  Un flujo de trabajo de Windows PowerShell es similar a un script de Windows PowerShell, pero presenta algunas diferencias importantes que pueden resultar confusas para un usuario nuevo.  Aunque este artículo está pensado para ayudarle a escribir runbooks con el flujo de trabajo de PowerShell, se recomienda que escribir runbooks con PowerShell, a menos que necesite puntos de control.  Hay varias diferencias de sintaxis al crear runbooks de flujo de trabajo de PowerShell y estas diferencias requieren algo más de trabajo para escribir flujos de trabajo eficaces.  

Un flujo de trabajo es una secuencia de pasos programados y conectados que realizan tareas de larga duración o requieren la coordinación de varios pasos en varios dispositivos o nodos administrados. Las ventajas de un flujo de trabajo en un script normal incluyen la capacidad de realizar una acción en varios dispositivos simultáneamente y la capacidad de recuperarse automáticamente de los errores. Un flujo de trabajo de Windows PowerShell es un script de Windows PowerShell que usa Windows Workflow Foundation. Aunque el flujo de trabajo está escrito con sintaxis de Windows PowerShell y se inicia mediante Windows PowerShell, se procesa mediante Windows Workflow Foundation.

Para obtener información detallada sobre los temas de este artículo, vea [Introducción al flujo de trabajo de Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>Estructura básica de un flujo de trabajo
El primer paso para convertir un script de PowerShell en un flujo de trabajo de PowerShell es delimitarlo con la palabra clave **Workflow** .  Un flujo de trabajo comienza con la palabra clave **Workflow** , seguida del cuerpo del script entre llaves. El nombre del flujo de trabajo sigue a la palabra clave **Workflow**, tal como se muestra en la sintaxis siguiente:

    Workflow Test-Workflow
    {
       <Commands>
    }

El nombre del flujo de trabajo debe coincidir con el nombre del runbook de Automation. Si se va a importar el runbook, el nombre de archivo debe coincidir con el nombre del flujo de trabajo y debe terminar en *.ps1*.

Para agregar parámetros al flujo de trabajo, use la palabra clave **Param** palabra clave igual que lo haría para un script.

## <a name="code-changes"></a>Cambios de código
El código de flujo de trabajo de PowerShell es casi idéntico al código de script de PowerShell salvo por algunos cambios importantes.  En las secciones siguientes se describen los cambios que debe realizar en un script de PowerShell para que se ejecute en un flujo de trabajo.

### <a name="activities"></a>Actividades
Una actividad es una tarea específica de un flujo de trabajo. Al igual que un script se compone de uno o varios comandos, un flujo de trabajo se compone de una o varias actividades que se realizan en secuencia. El flujo de trabajo de Windows PowerShell convierte automáticamente muchos de los cmdlets de Windows PowerShell en actividades cuando se ejecuta un flujo de trabajo. Cuando se especifica uno de estos cmdlets en el runbook, Windows Workflow Foundation ejecuta la actividad correspondiente. Para los cmdlets sin actividad correspondiente, el flujo de trabajo de Windows PowerShell ejecuta automáticamente el cmdlet dentro de una actividad [InlineScript](#inlinescript) . Hay un conjunto de cmdlets que están excluidos y no se pueden usar en un flujo de trabajo a menos que incluya explícitamente en un bloque de InlineScript. Para obtener más información sobre estos conceptos, consulte [Uso de actividades en los flujos de trabajo de scripts](http://technet.microsoft.com/library/jj574194.aspx).

Las actividades de flujo de trabajo comparten un conjunto de parámetros comunes para configurar su funcionamiento. Para más información sobre los parámetros comunes del flujo de trabajo, vea [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Parámetros posicionales
No puede usar parámetros posicionales con actividades y cmdlets en un flujo de trabajo.  Todo esto significa que debe usar nombres de parámetros.

Por ejemplo, considere el siguiente código que obtiene todos los servicios en ejecución.

     Get-Service | Where-Object {$_.Status -eq "Running"}

Si intenta ejecutar este mismo código en un flujo de trabajo, obtiene un mensaje parecido a "El conjunto de parámetros no se puede resolver mediante los parámetros con nombre especificados".  Para corregir este problema, proporcione el nombre del parámetro de la forma siguiente.

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### <a name="deserialized-objects"></a>Objetos deserializados
Los objetos de los flujos de trabajo están deserializados.  Esto significa que sus propiedades siguen estando disponibles, pero no sus métodos.  Por ejemplo, considere el siguiente código de PowerShell que detiene un servicio mediante el método Stop del objeto Service.

    $Service = Get-Service -Name MyService
    $Service.Stop()

Si intenta ejecutar esto en un flujo de trabajo, obtiene un error que indica "La invocación del método no se admite en un flujo de trabajo de Windows PowerShell".  

Una opción es ajustar estas dos líneas de código en un bloque [InlineScript](#inlinescript), en cuyo caso $Service sería un objeto de servicio dentro del bloque.

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    }

Otra opción es usar otro cmdlet que realiza la misma funcionalidad que el método, si hay uno disponible.  En nuestro ejemplo, el cmdlet Stop-Service proporciona la misma funcionalidad que el método Stop, y podría usar lo siguiente para un flujo de trabajo.

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }


## <a name="inlinescript"></a>InlineScript
La actividad **InlineScript** es útil cuando necesita ejecutar uno o más comandos tradicional como script tradicional de PowerShell en lugar de como flujo de trabajo de PowerShell.  Mientras que los comandos de un flujo de trabajo se envían a Windows Workflow Foundation para su procesamiento, los comandos de un bloque de InlineScript se procesan mediante Windows PowerShell.

InlineScript usa la sintaxis que se muestra a continuación.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Puede devolver resultados de InlineScript asignando el resultado a una variable. El siguiente ejemplo detiene un servicio y, a continuación, envía el nombre de servicio.

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Puede pasar valores en un bloque de InlineScript, pero debe usar el modificador de ámbito **$Using** .  El ejemplo siguiente es idéntico al ejemplo anterior salvo que se proporciona el nombre de servicio mediante una variable.

    Workflow Stop-MyService
    {
        $ServiceName = "MyService"

        $Output = InlineScript {
            $Service = Get-Service -Name $Using:ServiceName
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Aunque las actividades InlineScript pueden ser críticas en algunos flujos de trabajo, no son compatibles con las construcciones de flujo de trabajo y solo se deben usar cuando sea necesario por las razones siguientes:

* No puede usar [puntos de comprobación](#checkpoints) dentro de un bloque de InlineScript. Si se produce un error dentro del bloque, se debe reanudar desde el principio del bloque.
* No puede usar la [ejecución en paralelo](#parallel-processing) dentro de un bloque de InlineScript.
* InlineScript afecta a la escalabilidad del flujo de trabajo, ya que retiene la sesión de Windows PowerShell durante todo el bloque de InlineScript.

Para más información sobre el uso de InlineScript, vea [Ejecutar comandos de Windows PowerShell en un flujo de trabajo](http://technet.microsoft.com/library/jj574197.aspx) y [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Procesamiento en paralelo
Una ventaja de los flujos de trabajo de Windows PowerShell es la capacidad para realizar un conjunto de comandos en paralelo en lugar de hacerlo secuencialmente como con un script típico.

Puede usar la palabra clave **Parallel** para crear un bloque de scripts con varios comandos que se ejecuten simultáneamente. Esto usa la siguiente sintaxis que se muestra a continuación. En este caso, Activity1 y Activity2 se inician al mismo tiempo. Activity3 se inicia después de que se hayan completado Activity1 y Activity2.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


Por ejemplo, considere los siguientes comandos de PowerShell que copian varios archivos a un destino de red.  Estos comandos se ejecutan secuencialmente de modo que un archivo debe terminar de copiarse antes de que comience el siguiente.     

    Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

El flujo de trabajo siguiente ejecuta estos mismos comandos en paralelo para que todos ellos empiezan a copiarse al mismo tiempo.  Una vez que todos se han copiado, se muestra el mensaje de finalización.

    Workflow Copy-Files
    {
        Parallel
        {
            Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
            Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
            Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
        }

        Write-Output "Files copied."
    }


Puede utilizar la construcción **ForEach-Parallel** para procesar comandos para cada elemento de una colección simultáneamente. Los elementos de la colección se procesan en paralelo mientras que los comandos del bloque de scripts se ejecutan secuencialmente. Esto usa la siguiente sintaxis que se muestra a continuación. En este caso, Activity1 se inicia al mismo tiempo para todos los elementos de la colección. Para cada elemento, Activity2 se inicia una vez completado Activity1. Activity3 se inicia únicamente después de que se hayan completado Activity1 y Activity2 para todos los elementos.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

El ejemplo siguiente es similar al ejemplo anterior, en el que se copian archivos en paralelo.  En este caso, se muestra un mensaje para cada archivo después de copiarse.  Solo después de que todos se hayan copiado completamente, aparecerá el mensaje de finalización.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach -Parallel ($File in $Files)
        {
            Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }

        Write-Output "All files copied."
    }

> [!NOTE]
> No se recomienda ejecutar runbooks secundarios en paralelo, ya que se ha demostrado que no tiene unos resultados confiables.  A veces el resultado del runbook secundario no se muestra, y la configuración de un runbook secundario puede afectar a los demás runbooks secundarios paralelos.
>

## <a name="checkpoints"></a>puntos de comprobación
Un *punto de control* es una instantánea del estado actual del flujo de trabajo que incluye el valor actual de las variables y cualquier salida generada en ese punto. Si un flujo de trabajo termina en error o se suspende, la próxima vez que se ejecute comenzará desde su último punto de comprobación, en lugar de desde el inicio del flujo de trabajo.  Puede establecer un punto de control en un flujo de trabajo con la actividad **Checkpoint-Workflow** .

En el código de ejemplo siguiente, se produce una excepción después de Activity2 que provoca la finalización del flujo de trabajo. Cuando se vuelve a ejecutar el flujo de trabajo, se inicia ejecutando Activity2, ya que estaba justo después del último punto de comprobación establecido.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Debe establecer los puntos de comprobación en un flujo de trabajo después de las actividades que puedan ser propensas a la excepción y que no deben repetirse si se reanuda el flujo de trabajo. Por ejemplo, el flujo de trabajo puede crear una máquina virtual. Puede establecer un punto de control antes y después de los comandos para crear la máquina virtual. Si se produce un error en la creación,  los comandos se repetirían si el flujo de trabajo se vuelve a iniciar. Si el flujo de trabajo produce un error después de que la creación se haya realizado correctamente, la máquina virtual no se volverá a crear cuando se reanude el flujo de trabajo.

El siguiente ejemplo copia varios archivos en una ubicación de red y establece un punto de comprobación después de cada archivo.  Si la ubicación de red se pierde, el flujo de trabajo finaliza con error.  Cuando se vuelva a iniciar, se reanudará en el último punto de comprobación, lo que significa que solo se omiten los archivos que ya se han copiado.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach ($File in $Files)
        {
            Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
            Checkpoint-Workflow
        }

        Write-Output "All files copied."
    }

Como las credenciales de nombre de usuario no se conservan después de llamar a la actividad [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) o del último punto de control, necesita establecer las credenciales en NULL y, después, recuperarlas de nuevo desde el almacén de recursos tras llamar a **Suspend-Workflow** o al punto de control.  De lo contrario, puede que reciba el siguiente mensaje de error: *No puede reanudarse el trabajo de flujo de trabajo dado que no se pudieron guardar los datos de persistencia completamente o dichos datos estaban dañados. Debe reiniciar el flujo de trabajo.*

El mismo código de abajo muestra cómo controlar esta operación en los Runbooks del flujo de trabajo de PowerShell.

    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)

          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Add-AzureRmAccount -Credential $Cred
         }
     }


Este paso no es necesario si se autentica utilizando una cuenta de ejecución configurada con una entidad de servicio.  

Para obtener más información acerca de los puntos de control, consulte [Adición de puntos de control a un flujo de trabajo de scripts](http://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Pasos siguientes
* Para empezar a trabajar con Runbooks de flujo de trabajo de PowerShell, consulte [Mi primer runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md)
