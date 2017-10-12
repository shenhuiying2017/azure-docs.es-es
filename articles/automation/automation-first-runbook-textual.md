---
title: Mi primer runbook de flujo de trabajo de PowerShell en Azure Automation | Microsoft Docs
description: "Tutorial que le guiará a través de la creación, prueba y publicación de un runbook de texto simple con Flujo de trabajo de PowerShell."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
keywords: flujo de trabajo de powershell, ejemplos de flujo de trabajo de powershell, powershell para flujos de trabajo
ms.assetid: 0002d7f7-e2b5-46e3-b5eb-4596b84fd526
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/31/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 71fba79804e4361fd731ec5627526beafa01fa3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="my-first-powershell-workflow-runbook"></a>Mi primer runbook de flujo de trabajo de PowerShell

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Flujo de trabajo de PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Este tutorial le guiará para crear un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) en Azure Automation. Comenzaremos con un runbook simple que probaremos y publicaremos, y explicaremos cómo hacer un seguimiento del estado del trabajo del runbook. A continuación, modificaremos el runbook para administrar recursos de Azure, en este caso, iniciar una máquina virtual de Azure. Para finalizar, agregaremos parámetros de runbook para que el runbook sea más robusto.

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, necesitará lo siguiente:

* Suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o suscribirse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Cuenta de Automatización](automation-offering-get-started.md) para contener el Runbook y autenticarse en recursos de Azure.  Esta cuenta debe tener permiso para iniciar y detener la máquina virtual.
* Una máquina virtual de Azure. Detendremos e iniciaremos esta máquina, por lo que no debería ser una máquina virtual de producción.

## <a name="step-1---create-new-runbook"></a>Paso 1: crear nuevo runbook
Empezaremos creando un runbook simple que genere el texto *Hello World*.

1. En el Portal de Azure, abra su cuenta de Automatización.  
   La página de la cuenta de Automatización proporciona una vista rápida de los recursos que hay en esa cuenta. Ya debería tener algunos recursos. Muchas de ellos son los módulos que se incluyen automáticamente en una cuenta nueva de Automatización. También debe tener el recurso de credencial que se menciona en los [requisitos previos](#prerequisites).
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.<br><br> ![Control de runbooks](media/automation-first-runbook-textual/runbooks-control-tiles.png)
3. Para crear un runbook, haga clic en el botón **Agregar un runbook** y en **Crear un nuevo runbook**.
4. Asigne al runbook el nombre *MyFirstRunbook-Workflow*.
5. En este caso, vamos a crear un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks), por lo tanto, seleccione **Flujo de trabajo de Powershell** en **Tipo de runbook**.<br><br> ![Nuevo runbook](media/automation-first-runbook-textual/new-runbook-properties.png)
6. Haga clic en **Crear** para crear el runbook y abra el editor de texto.

## <a name="step-2---add-code-to-the-runbook"></a>Paso 2: Incorporación de código al runbook
Puede escribir el código directamente en el runbook o seleccionar los cmdlets, runbooks y recursos desde el control Biblioteca y agregarlos al runbook con los parámetros relacionados. En este tutorial, escribiremos directamente en el runbook.

1. Nuestro runbook está vacío actualmente, solo con la palabra clave *workflow* , el nombre del runbook y las llaves que encerrarán el flujo de trabajo completo.

   ```
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```
2. Escriba *Write-Output "Hello World."* entre las llaves.

   ```
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```
3. Guarde el Runbook, para lo que debe hacer clic en **Guardar**.<br><br> ![Guardar runbook](media/automation-first-runbook-textual/automation-runbook-edit-controls-save.png)

## <a name="step-3---test-the-runbook"></a>Paso 3: probar el runbook
Antes de que publiquemos el runbook para que esté disponible en producción, queremos probarlo para asegurarnos de que funciona correctamente. Cuando se prueba un runbook, se ejecuta su versión **Borrador** y se visualizan sus resultados de forma interactiva.

1. Haga clic en **Panel Prueba** para abrir el panel de prueba.<br><br> ![panel Prueba](media/automation-first-runbook-textual/automation-runbook-edit-controls-test.png)
2. Haga clic en **Iniciar** para iniciar la prueba. Esta debe ser la única opción habilitada.
3. Se crea un [trabajo de runbook](automation-runbook-execution.md) y se muestra su estado.  
   El estado del trabajo se iniciará como *En cola* , lo que indica que está esperando que haya disponible un trabajo de runbook en la nube. Su estado cambiará a *Iniciando* cuando un trabajo de runbook lo solicite. Cuando el runbook comience a ejecutarse realmente, el estado será *En ejecución*.  
4. Cuando se complete el trabajo del runbook, se mostrará su resultado. En nuestro caso, deberíamos ver *Hello World*.<br><br> ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)
5. Cierre el panel Prueba para volver al lienzo.

## <a name="step-4---publish-and-start-the-runbook"></a>Paso 4: publicar e iniciar el runbook
El runbook que acabamos de crear aún está en modo borrador. Tenemos que publicarlo antes de que podamos ejecutarlo en producción. Al publicar un runbook, se sobrescribe la versión publicada existente con la versión de borrador. En nuestro caso, no tenemos una versión publicada aún porque acabamos de crear el runbook.

1. Haga clic en **Publicar** para publicar el runbook y en **Sí** cuando se le solicite.<br><br> ![Publicar](media/automation-first-runbook-textual/automation-runbook-edit-controls-publish.png)
2. Si se desplaza ahora a la izquierda para ver el runbook en el panel **Runbooks**, mostrará el **Estado de creación** **Publicado**.
3. Desplácese de nuevo a la derecha para ver el panel de **MyFirstRunbook-Workflow**.  
   Las opciones en la parte superior nos permiten iniciar el runbook, programarlo para que se inicie en algún momento en el futuro o crear un [webhook](automation-webhooks.md) para que se inicie a través de una llamada HTTP.
4. Queremos iniciar el runbook, por lo que debe hacer clic en **Iniciar** y en **Sí** cuando se le solicite.<br><br> ![Iniciar runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)
5. Se abre un panel de trabajo para el trabajo de runbook que acabamos de crear. Podemos cerrar este panel, pero en este caso lo dejaremos abierto para que podamos ver el progreso del trabajo.
6. El estado del trabajo se muestra en **Resumen del trabajo** y coincide con los estados que vimos cuando probamos el runbook.<br><br> ![Resumen del trabajo](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)
7. Cuando el estado del runbook aparezca como *Completado*, haga clic en **Salida**. Se abre el panel Salida y podemos ver *Hello World*.<br><br> ![Resumen del trabajo](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)  
8. Cierre el panel Salida.
9. Haga clic en **Todos los registros** para abrir el panel Transmisiones para el trabajo de Runbook. Solo deberíamos ver *Hello World* en el flujo de salida, pero se pueden mostrar otras transmisiones de un trabajo de Runbook como Detallado y Error si el Runbook escribe en ellas.<br><br> ![Resumen del trabajo](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)
10. Cierre el panel de secuencias y el panel de trabajo para volver al panel de MyFirstRunbook.
11. Haga clic en **Trabajos** para abrir el panel Trabajos de este runbook. Enumera todos los trabajos creados por este runbook. Solo deberíamos ver un trabajo en la lista ya que solo ejecutamos el trabajo una vez.<br><br> ![Trabajos](media/automation-first-runbook-textual/runbook-control-job-tile.png)
12. Puede hacer clic en esta tarea para abrir el mismo panel Trabajo que vimos cuando se inició el runbook. Esto permite volver atrás en el tiempo y ver los detalles de cualquier trabajo que se creó para un runbook determinado.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Paso 5: agregar autenticación para administrar recursos de Azure
Hemos probado y publicado nuestro runbook, pero hasta ahora no hace nada útil. Queremos que administre recursos de Azure. Sin embargo, no podrá hacerlo aunque a menos que lo autentiquemos con las credenciales que se mencionan en los [requisitos previos](#prerequisites). Esto se hace con el cmdlet **Add-AzureRMAccount** .

1. Abra el editor de texto haciendo clic en **Editar** en el panel MyFirstRunbook-Workflow.<br><br> ![Editar runbook](media/automation-first-runbook-textual/automation-runbook-controls-edit.png)
2. Ya no necesitamos la línea **Write-Output** , así que elimínela.
3. Coloque el cursor en una línea en blanco entre las llaves.
4. Escriba o copie y pegue el siguiente código que controlará la autenticación con la cuenta de ejecución de Automatización:

   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
5. Haga clic en el **panel Prueba** para poder probar el runbook.
6. Haga clic en **Iniciar** para iniciar la prueba. Cuando termine, recibirá unos resultados similares a los siguientes, que muestran información básica de su cuenta. Esto confirma que la credencial es válida.<br><br> ![Autenticar](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Paso 6: Incorporación de una actividad para iniciar una máquina virtual
Ahora que el runbook se autentica en nuestra suscripción a Azure, podemos administrar los recursos. Vamos a agregar un comando para iniciar una máquina virtual. Puede seleccionar cualquier máquina virtual de su suscripción de Azure. Por ahora, vamos a codificar ese nombre en el runbook.

1. Después de *Add-AzureRmAccount*, escriba *Start-AzureRmVM -Name 'NombreVM' -ResourceGroupName 'NombreGrupoRecursos'* y proporcione el nombre y el nombre del grupo de recursos de la máquina virtual que se iniciará.  

   ```
   workflow MyFirstRunbook-Workflow
   {
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   }
   ```
2. Guarde el runbook y haga clic en el **panel Prueba** para poder probarlo.
3. Haga clic en **Iniciar** para iniciar la prueba. Cuando haya terminado, compruebe que la máquina virtual se ha iniciado.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Paso 7: agregar un parámetro de entrada al runbook
Actualmente, nuestro runbook inicia la máquina virtual que codificamos en el runbook, pero nuestro runbook sería más útil si pudiéramos especificar la máquina virtual cuando se inicia el runbook. Ahora agregaremos parámetros de entrada para que el runbook proporcione esa funcionalidad.

1. Agregue parámetros para *NombreVM* y *NombreGrupoRecursos* al runbook y use estas variables con el cmdlet **Start-AzureRmVM** como se muestra en el siguiente ejemplo.

   ```
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
2. Guarde el runbook y abra el panel Prueba. Tenga en cuenta que ahora puede proporcionar valores para las dos variables de entrada que se usarán en la prueba.
3. Cierre el panel Prueba.
4. Haga clic en **Publicar** para publicar la nueva versión del runbook.
5. Detenga la máquina virtual que inició en el paso anterior.
6. Haga clic en **Iniciar** para iniciar el runbook. Escriba el valor de **VMName** y **ResourceGroupName** para la máquina virtual que va a iniciar.<br><br> ![Start Runbook](media/automation-first-runbook-textual/automation-pass-params.png)<br>  
7. Cuando se complete el runbook, compruebe que la máquina virtual se ha iniciado.  

## <a name="next-steps"></a>Pasos siguientes
* Para empezar a trabajar con runbooks gráficos, consulte [Mi primer runbook gráfico](automation-first-runbook-graphical.md)
* Para empezar a trabajar con runbooks de PowerShell, consulte [Mi primer runbook de PowerShell](automation-first-runbook-textual-powershell.md)
* Para aprender más sobre los tipos de runbook, sus ventajas y sus limitaciones, consulte [Tipos de runbooks de Automatización de Azure](automation-runbook-types.md)
* Para más información sobre la característica de compatibilidad con scripts de PowerShell, consulte [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
