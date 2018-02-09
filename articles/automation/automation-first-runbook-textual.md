---
title: Mi primer runbook de flujo de trabajo de PowerShell en Azure Automation | Microsoft Docs
description: "Tutorial que le guiará a través de la creación, prueba y publicación de un runbook de texto simple con Flujo de trabajo de PowerShell."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: 
keywords: flujo de trabajo de powershell, ejemplos de flujo de trabajo de powershell, powershell para flujos de trabajo
ms.assetid: 0002d7f7-e2b5-46e3-b5eb-4596b84fd526
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 62cb96075deed6c252ae44c8a000e820cb691a90
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="my-first-powershell-workflow-runbook"></a>Mi primer runbook de flujo de trabajo de PowerShell

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Flujo de trabajo de PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Este tutorial le guiará para crear un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) en Azure Automation. Comenzará con un runbook simple que probará y publicará, mientras se explica cómo hacer un seguimiento del estado del trabajo del runbook. A continuación, puede modificar el runbook para administrar recursos de Azure, en este caso, iniciar una máquina virtual de Azure. Para finalizar, agregará parámetros de runbook para que el runbook sea más sólido.

## <a name="prerequisites"></a>requisitos previos
Para completar este tutorial, necesitará lo siguiente:

* Suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o suscribirse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Cuenta de Automation](automation-offering-get-started.md) para contener el Runbook y autenticarse en recursos de Azure.  Esta cuenta debe tener permiso para iniciar y detener la máquina virtual.
* Una máquina virtual de Azure. Es necesario detener e iniciar esta máquina, por lo que no debería ser una máquina virtual de producción.

## <a name="step-1---create-new-runbook"></a>Paso 1: crear nuevo runbook
Empieza creando un runbook simple cuya salida sea el texto *Hola mundo*.

1. En Azure Portal, abra su cuenta de Automation.

   La página de la cuenta de Automation proporciona una vista rápida de los recursos que hay en esa cuenta. Ya debería tener algunos recursos. Muchas de ellos son los módulos que se incluyen automáticamente en una cuenta nueva de Automation. También debe tener el recurso de credencial que se menciona en los [requisitos previos](#prerequisites).

1. Haga clic en **Runbooks** (en **Automatización de procesos**) para abrir la lista de runbooks.
2. Para crear un nuevo runbook, haga clic en el botón **+ Agregar un runbook** y luego en **Crear un nuevo runbook**.
3. Asigne al runbook el nombre *MyFirstRunbook-Workflow*.
4. En este caso, va a crear un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks), por lo tanto, seleccione **Flujo de trabajo de Powershell** en **Tipo de runbook**.
5. Haga clic en **Crear** para crear el runbook y abra el editor de texto.

## <a name="step-2---add-code-to-the-runbook"></a>Paso 2: Incorporación de código al runbook
Puede escribir el código directamente en el runbook o seleccionar los cmdlets, runbooks y recursos desde el control Biblioteca y agregarlos al runbook con los parámetros relacionados. En este tutorial, escribirá directamente en el runbook.

1. Su runbook está vacío actualmente, solo con la palabra clave *workflow*, el nombre del runbook y las llaves que encerrarán el flujo de trabajo completo.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```
2. Escriba *Write-Output "Hello World."* entre las llaves.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```
3. Guarde el Runbook, para lo que debe hacer clic en **Guardar**.

## <a name="step-3---test-the-runbook"></a>Paso 3: probar el runbook
Antes de publicar el runbook para que esté disponible en producción, puede que quiera probarlo para asegurarse de que funciona correctamente. Cuando se prueba un runbook, se ejecuta su versión **Borrador** y se visualizan sus resultados de forma interactiva.

1. Haga clic en **Panel Prueba** para abrir el panel de prueba.
2. Haga clic en **Iniciar** para iniciar la prueba. Esta debe ser la única opción habilitada.
3. Se crea un [trabajo de runbook](automation-runbook-execution.md) y se muestra su estado.

   El estado del trabajo se iniciará como *En cola* , lo que indica que está esperando que haya disponible un trabajo de runbook en la nube. Su estado cambia a *Iniciando* cuando un trabajo de runbook solicita el trabajo. Cuando el runbook comienza a ejecutarse realmente, el estado es *En ejecución*.  

1. Cuando se complete el trabajo del runbook, se mostrará su resultado. En este caso, debería ver *Hola mundo*.<br><br> ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)
2. Cierre el panel Prueba para volver al lienzo.

## <a name="step-4---publish-and-start-the-runbook"></a>Paso 4: publicar e iniciar el runbook
El runbook que acaba de crear aún está en modo de borrador. Tiene que publicarlo antes de que pueda ejecutarlo en producción. Al publicar un runbook, se sobrescribe la versión publicada existente con la versión de borrador. En este caso, no tiene una versión publicada aún porque acaba de crear el runbook.

1. Haga clic en **Publicar** para publicar el runbook y en **Sí** cuando se le solicite.
2. Si se desplaza a la izquierda para ver el runbook en el panel **Runbooks**, se muestra **Publicado** en **Estado de creación**.
3. Desplácese de nuevo a la derecha para ver el panel de **MyFirstRunbook-Workflow**.  
   Las opciones en la parte superior nos permiten iniciar el runbook, programarlo para que se inicie en algún momento en el futuro o crear un [webhook](automation-webhooks.md) para que se inicie a través de una llamada HTTP.
4. Solo tiene que iniciar el runbook, para ello debe hacer clic en **Iniciar** y en **Sí** cuando se le solicite.<br><br> ![Iniciar runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)
5. Se abre un panel de trabajo para el trabajo de runbook que acaba de crear. Puede cerrar este panel, pero en este caso déjelo abierto para que pueda ver el progreso del trabajo.
6. El estado del trabajo se muestra en **Resumen del trabajo** y coincide con los estados que vio cuando probó el runbook.<br><br> ![Resumen del trabajo](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)
7. Cuando el estado del runbook aparezca como *Completado*, haga clic en **Salida**. Se abre el panel Salida y puede ver *Hola mundo*.<br><br> ![Resumen del trabajo](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)  
8. Cierre el panel Salida.
9. Haga clic en **Todos los registros** para abrir el panel Transmisiones para el trabajo de Runbook. Solo debería ver *Hola mundo* en el flujo de salida, pero se pueden mostrar otras transmisiones de un trabajo de runbook como Detallado y Error si el runbook escribe en ellas.<br><br> ![Resumen del trabajo](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)
10. Cierre el panel de secuencias y el panel de trabajo para volver al panel de MyFirstRunbook.
11. Haga clic en **Trabajos** para abrir el panel Trabajos de este runbook. Enumera todos los trabajos creados por este runbook. Solo debería ver un trabajo en la lista ya que solo ejecutó el trabajo una vez.<br><br> ![Trabajos](media/automation-first-runbook-textual/runbook-control-job-tile.png)
12. Puede hacer clic en este trabajo para abrir el mismo panel Trabajo que vio cuando se inició el runbook. Esto permite volver atrás en el tiempo y ver los detalles de cualquier trabajo que se creó para un runbook determinado.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Paso 5: agregar autenticación para administrar recursos de Azure
Ha probado y publicado su runbook, pero hasta ahora no hace nada útil. Quiere que administre los recursos de Azure. Sin embargo, no podrá hacerlo aunque a menos que lo autentique con las credenciales que se mencionan en los [requisitos previos](#prerequisites). Esto se hace con el cmdlet **Add-AzureRMAccount**.

1. Abra el editor de texto haciendo clic en **Editar** en el panel MyFirstRunbook-Workflow.
2. Ya no necesita la línea **Write-Output**, así que elimínela.
3. Coloque el cursor en una línea en blanco entre las llaves.
4. Escriba o copie y pegue el siguiente código que controlará la autenticación con la cuenta de ejecución de Automation:

   ```powershell-interactive
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
5. Haga clic en el **panel de prueba** para poder probar el runbook.
6. Haga clic en **Iniciar** para iniciar la prueba. Cuando termine, recibirá unos resultados similares a los siguientes, que muestran información básica de su cuenta. Esto confirma que la credencial es válida.<br><br> ![Autenticar](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Paso 6: Incorporación de una actividad para iniciar una máquina virtual
Ahora que el runbook está autenticado en la suscripción a Azure, puede administrar los recursos. Agregue un comando para iniciar una máquina virtual. Puede seleccionar cualquier máquina virtual de su suscripción de Azure. Por ahora, codificará ese nombre en el runbook.

1. Después de *Add-AzureRmAccount*, escriba *Start-AzureRmVM -Name 'NombreVM' -ResourceGroupName 'NombreGrupoRecursos'* y proporcione el nombre y el nombre del grupo de recursos de la máquina virtual que se iniciará.  

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   }
   ```
2. Guarde el runbook y haga clic en el **panel Prueba** para probarlo.
3. Haga clic en **Iniciar** para iniciar la prueba. Cuando haya terminado, compruebe que la máquina virtual se ha iniciado.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Paso 7: agregar un parámetro de entrada al runbook
Actualmente, el runbook inicia la máquina virtual que codificó en el runbook, pero sería más útil si especificara la máquina virtual al iniciar el runbook. Agregue parámetros de entrada al runbook para proporcionar esa funcionalidad.

1. Agregue parámetros para *NombreVM* y *NombreGrupoRecursos* al runbook y use estas variables con el cmdlet **Start-AzureRmVM** como se muestra en el siguiente ejemplo.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )  
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```
2. Guarde el runbook y abra el panel Prueba. Ahora puede proporcionar valores para las dos variables de entrada que se usarán en la prueba.
3. Cierre el panel Prueba.
4. Haga clic en **Publicar** para publicar la nueva versión del runbook.
5. Detenga la máquina virtual que inició en el paso anterior.
6. Haga clic en **Iniciar** para iniciar el runbook. Escriba el valor de **VMName** y **ResourceGroupName** para la máquina virtual que va a iniciar.<br><br> ![Start Runbook](media/automation-first-runbook-textual/automation-pass-params.png)<br>  
7. Cuando se complete el runbook, compruebe que la máquina virtual se ha iniciado.  

## <a name="next-steps"></a>pasos siguientes
* Para empezar a trabajar con runbooks gráficos, consulte [Mi primer runbook gráfico](automation-first-runbook-graphical.md)
* Para empezar a trabajar con runbooks de PowerShell, consulte [Mi primer runbook de PowerShell](automation-first-runbook-textual-powershell.md)
* Para más información sobre los tipos de Runbook, sus ventajas y sus limitaciones, consulte [Tipos de runbooks de Azure Automation](automation-runbook-types.md)
* Para más información sobre la característica de compatibilidad con scripts de PowerShell, consulte [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
