---
title: Mi primer runbook de PowerShell en Azure Automation | Microsoft Docs
description: "Tutorial que le guiará a través de la creación, prueba y publicación de un runbook de PowerShell."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: 
keywords: "powershell de Azure, tutorial de scripts de powershell, automatización de powershell"
ms.assetid: a43b395a-e740-41a3-ae62-40eac9d0ec00
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: magoedte;sngun
ms.openlocfilehash: 8db2cc1c8759fd08129f7794a8675d006998cc55
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="my-first-powershell-runbook"></a>Mi primer runbook de PowerShell

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Flujo de trabajo de PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Este tutorial le guiará por la creación de un [Runbook de PowerShell](automation-runbook-types.md#powershell-runbooks) en Azure Automation. Empezará con un runbook sencillo que probará y publicará, y aprenderá a hacer un seguimiento de estado del trabajo del runbook. A continuación, puede modificar el runbook para administrar recursos de Azure, en este caso, iniciar una máquina virtual de Azure. Para finalizar, agregará parámetros de runbook para que el runbook sea más robusto.

## <a name="prerequisites"></a>requisitos previos
Para completar este tutorial, necesitará lo siguiente:

* Suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o suscribirse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Cuenta de Automation](automation-offering-get-started.md) para contener el Runbook y autenticarse en recursos de Azure. Esta cuenta debe tener permiso para iniciar y detener la máquina virtual.
* Una máquina virtual de Azure. Puede detener e iniciar esta máquina, por lo que no debería ser una máquina virtual de producción.

## <a name="step-1---create-new-runbook"></a>Paso 1: crear nuevo runbook
Empieza creando un runbook simple cuya salida sea el texto *Hola mundo*.

1. En Azure Portal, abra su cuenta de Automation.

   La página de la cuenta de Automation proporciona una vista rápida de los recursos que hay en esa cuenta. Ya debería tener algunos recursos. Muchas de ellos son los módulos que se incluyen automáticamente en una cuenta nueva de Automation. También debe tener el recurso de credencial que se menciona en los [requisitos previos](#prerequisites).

1. Haga clic en **Runbooks** (en **Automatización de procesos**) para abrir la lista de runbooks.
2. Para crear un runbook, haga clic en el botón **+ Agregar un runbook** y en **Crear un runbook nuevo**.
3. Asigne al Runbook el nombre *MyFirstRunbook-PowerShell*.
4. En este caso, va a crear un [runbook de PowerShell](automation-runbook-types.md#powershell-runbooks), por tanto, seleccione **Powershell** como **Tipo de Runbook**.
5. Haga clic en **Crear** para crear el runbook y abra el editor de texto.

## <a name="step-2---add-code-to-the-runbook"></a>Paso 2: Incorporación de código al runbook
Puede escribir el código directamente en el runbook o seleccionar los cmdlets, runbooks y recursos desde el control Biblioteca y agregarlos al runbook con los parámetros relacionados. En este tutorial, escribirá directamente en el runbook.

1. El runbook ahora está vacío, escriba *Write-Output "Hola mundo"*. en el cuerpo del script.<br><br> ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. Guarde el Runbook, para lo que debe hacer clic en **Guardar**.

## <a name="step-3---test-the-runbook"></a>Paso 3: probar el runbook
Antes de publicar el runbook para que esté disponible en producción, puede que quiera probarlo para asegurarse de que funciona correctamente. Cuando se prueba un runbook, se ejecuta su versión **Borrador** y se visualizan sus resultados de forma interactiva.

1. Haga clic en **Panel Prueba** para abrir el panel de prueba.
2. Haga clic en **Iniciar** para iniciar la prueba. Esta debe ser la única opción habilitada.
3. Se crea un [trabajo de runbook](automation-runbook-execution.md) y se muestra su estado.

   El estado del trabajo se inicia como *En cola*, lo que indica que está esperando que haya disponible un trabajo de runbook en la nube. Su estado cambia a *Iniciando* cuando un trabajo de runbook solicita el trabajo. Cuando el runbook comienza a ejecutarse realmente, el estado es *En ejecución*.  

1. Cuando se complete el trabajo del runbook, se mostrará su resultado. En este caso, debería ver *Hello World*.<br><br> ![Salida del panel de prueba](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
2. Cierre el panel Prueba para volver al lienzo.

## <a name="step-4---publish-and-start-the-runbook"></a>Paso 4: publicar e iniciar el runbook
El runbook que acaba de crear aún está en modo de borrador. Tiene que publicarlo para poder ejecutarlo en producción.  Al publicar un runbook, se sobrescribe la versión publicada existente con la versión de borrador. En este caso, no tiene una versión publicada aún porque acaba de crear el runbook.

1. Haga clic en **Publicar** para publicar el runbook y en **Sí** cuando se le solicite.
2. Si se desplaza a la izquierda para ver el runbook en el panel **Runbooks**, se muestra **Publicado** en **Estado de creación**.
3. Desplácese de nuevo a la derecha para ver el panel de **MyFirstRunbook-PowerShell**.  
   Las opciones en la parte superior nos permiten iniciar el runbook, verlo, programarlo para que se inicie en algún momento en el futuro o crear un [webhook](automation-webhooks.md) para que se inicie a través de una llamada HTTP.
4. Para iniciar el runbook, haga clic en **Iniciar** y en **Aceptar** cuando se abra la página Iniciar runbook.
5. Se abre un panel de trabajo para el trabajo del runbook que acaba de crear. Puede cerrar este panel, pero en este caso déjelo abierto para que pueda ver el progreso del trabajo.
6. El estado del trabajo se muestra en **Resumen del trabajo** y coincide con los estados que vio cuando probó el runbook.<br><br> ![Resumen del trabajo](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. Cuando el estado del runbook aparezca como *Completado*, en **Introducción**, haga clic en **Salida**. Se abre el panel Salida y puede ver *Hola mundo*.<br><br> ![Salida de trabajo](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)<br> 
8. Cierre la página Salida.
9. Haga clic en **Todos los registros** para abrir el panel Transmisiones para el trabajo de Runbook. Solo debería ver *Hola mundo* en el flujo de salida, pero se pueden mostrar otras transmisiones de un trabajo de Runbook como Detallado y Error si el Runbook escribe en ellas.<br><br> ![Todos los registros](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. Cierre la página Flujos y la del trabajo para volver a la página MyFirstRunbook-PowerShell.
11. En **Detalles**, haga clic en **Trabajos** para abrir el panel Trabajos de este runbook. Enumera todos los trabajos creados por este runbook. Solo debería ver un trabajo en la lista ya que solo ejecutó el trabajo una vez.<br><br> ![Lista de trabajos](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. Puede hacer clic en este trabajo para abrir el mismo panel Trabajo que vio cuando se inició el runbook. Esto permite volver atrás en el tiempo y ver los detalles de cualquier trabajo que se creó para un runbook determinado.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Paso 5: agregar autenticación para administrar recursos de Azure
Ha probado y publicado su runbook, pero hasta ahora no hace nada útil. Quiere que administre recursos de Azure. Sin embargo, no podrá hacerlo a menos que lo autentique con las credenciales que se mencionan en los [requisitos previos](#prerequisites). Esto se hace con el cmdlet **Add-AzureRmAccount**.

1. Haga clic en **Editar** en la página MyFirstRunbook-PowerShell para abrir el editor de texto.
2. Ya no necesita la línea **Write-Output**, así que elimínela.
3. Escriba o copie y pegue el siguiente código que controla la autenticación con la cuenta de ejecución de Automation:
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
   <br>
4. Haga clic en el **panel de prueba** para probar el runbook.
5. Haga clic en **Iniciar** para iniciar la prueba. Cuando termine, recibirá unos resultados similares a los siguientes, que muestran información básica de su cuenta. Esto confirma que la credencial es válida.<br><br> ![Autenticar](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Paso 6: Incorporación de una actividad para iniciar una máquina virtual
Ahora que el runbook está autenticado en la suscripción a Azure, puede administrar los recursos. Agregue un comando para iniciar una máquina virtual. Puede seleccionar cualquier máquina virtual de la suscripción de Azure. Por ahora, codificará ese nombre directamente en el runbook.

1. Después de *Add-AzureRmAccount*, escriba *Start-AzureRmVM -Name 'NombreVM' -ResourceGroupName 'NombreGrupoRecursos'* y proporcione el nombre y el nombre del grupo de recursos de la máquina virtual que se iniciará.  
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. Guarde el runbook y haga clic en **Panel de prueba** para probarlo.
3. Haga clic en **Iniciar** para iniciar la prueba. Cuando haya terminado, compruebe que la máquina virtual se ha iniciado.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Paso 7: agregar un parámetro de entrada al runbook
Actualmente, el runbook inicia la máquina virtual que codificó directamente en el runbook, pero el runbook sería más útil si especificara la máquina virtual al iniciar el runbook. Agregue parámetros de entrada al runbook para proporcionar esa funcionalidad.

1. Agregue parámetros para *VMName* y *ResourceGroupName* al runbook y use estas variables con el cmdlet **Start-AzureRmVM** como se muestra en el siguiente ejemplo.

   ```
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```
   <br>
1. Guarde el runbook y abra el panel Prueba. Ahora puede proporcionar valores para las dos variables de entrada que se usarán en la prueba.
2. Cierre el panel Prueba.
3. Haga clic en **Publicar** para publicar la nueva versión del runbook.
4. Detenga la máquina virtual que inició en el paso anterior.
5. Haga clic en **Aceptar** para iniciar el runbook. Escriba el valor de **VMName** y **ResourceGroupName** para la máquina virtual que va a iniciar.<br><br> ![Pasar parámetro](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
6. Cuando se complete el runbook, compruebe que la máquina virtual se ha iniciado.

## <a name="differences-from-powershell-workflow"></a>Diferencias del flujo de trabajo de PowerShell
Los runbooks de PowerShell tienen el mismo ciclo de vida, las mismas funcionalidades y la misma administración que los runbooks de flujo de trabajo de PowerShell, pero existen algunas diferencias y limitaciones:

1. Los runbooks de PowerShell se ejecutan más rápido en comparación con los runbooks de flujo de trabajo de PowerShell, pues estos no tienen paso de compilación.
2. Los runbooks de flujo de trabajo de PowerShell admiten los puntos de control y su uso. Los runbooks de flujo de trabajo de PowerShell pueden reanudarse desde cualquier punto en el runbook mientras que los runbooks de PowerShell solo puede reanudarse desde el principio.
3. Los runbooks de flujo de trabajo de PowerShell admiten la ejecución en serie y en paralelo, mientras que los runbooks de PowerShell solo puede ejecutar comandos en serie.
4. En un runbook de flujo de trabajo de PowerShell, una actividad, un comando o un bloque de script pueden tener su propio espacio de ejecución, mientras que en un runbook de PowerShell todo lo que hay en el script se ejecuta en un único espacio de ejecución. También se observan algunas [diferencias sintácticas](https://technet.microsoft.com/magazine/dn151046.aspx) entre un runbook de PowerShell nativo y un runbook de flujo de trabajo de PowerShell.

## <a name="next-steps"></a>pasos siguientes
* Para empezar a trabajar con cuadernos gráficos, consulte [Mi primer runbook gráfico](automation-first-runbook-graphical.md)
* Para empezar a trabajar con runbooks de flujo de trabajo de PowerShell, consulte [Mi primer runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md)
* Para obtener más información sobre los tipos de runbook, sus ventajas y sus limitaciones, consulte [Tipos de runbooks de Azure Automation](automation-runbook-types.md)
* Para obtener más información sobre la característica de compatibilidad con scripts de PowerShell, consulte [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

