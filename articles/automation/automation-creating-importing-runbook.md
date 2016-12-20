---
title: "Creación o importación de un runbook en Automatización de Azure"
description: "En este artículo se describe cómo crear un runbook nuevo en la automatización de Azure o importar uno desde un archivo."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 24414362-b690-4474-8ca7-df18e30fc31d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2016
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b590329e204c189ea2ef67b7c6c6bb6dacaf1628


---
# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Creación o importación de un runbook en Automatización de Azure
Para agregar un runbook a Azure Automation, puede [crear uno](#creating-a-new-runbook) o importar un runbook existente desde un archivo o desde la [Galería de runbooks](automation-runbook-gallery.md). Este artículo ofrece información sobre cómo crear e importar runbooks de un archivo.  Puede obtener todos los detalles sobre el acceso a los módulos y los runbooks de la comunidad en [Galería de runbooks y módulos para la Automatización de Azure](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Creación de un nuevo runbook
Puede crear un nuevo runbook en la Automatización de Azure mediante uno de los portales de Azure o Windows PowerShell. Cuando se haya creado el runbook, puede editarlo con la información de [Aprendizaje del flujo de trabajo de Windows PowerShell](automation-powershell-workflow.md) y [Creación gráfica en Azure Automation](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-classic-portal"></a>Para crear un nuevo Runbook de Automatización de Azure con el Portal de Azure clásico
Solo puede trabajar con [Runbooks del flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) en el portal de Azure.

1. En el Portal de Azure clásico, haga clic en **Nuevo**, **App Services**, **Automation**, **Runbook**, **Creación rápida**.
2. Escriba la información necesaria y luego haga clic en **Crear**. El nombre del runbook debe empezar por una letra y puede tener letras, números, caracteres de subrayado y guiones.
3. Si quiere editar el runbook ahora, haga clic en **Editar runbook**. De lo contrario, haga clic en **Aceptar**.
4. Su nuevo runbook aparecerá en la pestaña **Runbooks** .

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Para crear un nuevo runbook de Automatización de Azure con el portal de Azure
1. En el Portal de Azure, abra su cuenta de Automatización.
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Haga clic en el botón **Agregar un Runbook** y, después, en **Crear un runbook nuevo**.
4. Escriba un **Nombre** para el runbook y seleccione su [Tipo](automation-runbook-types.md). El nombre del runbook debe empezar por una letra y puede tener letras, números, caracteres de subrayado y guiones.
5. Haga clic en **Crear** para crear el runbook y abra el editor.

### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Para crear un nuevo runbook de automatización de Azure con Windows PowerShell
Puede usar el cmdlet [New-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx) para crear un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) vacío. Puede especificar el parámetro **Name** para crear un runbook vacío que puede editar posteriormente, o bien puede especificar el parámetro **Path** para importar un archivo de runbook. El parámetro **Type** también se debe incluir para especificar uno de los cuatro tipos de runbook.

Los comandos de ejemplo siguientes muestran cómo crear un nuevo runbook vacío.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Importar un runbook de un archivo en la automatización de Azure
Puede crear un nuevo runbook en automatización de Azure importando un script de PowerShell o un flujo de trabajo de PowerShell (extensión. ps1) o un runbook gráfico exportado (.graphrunbook).  Necesita especificar el [tipo de runbook](automation-runbook-types.md) que se creará de la importación teniendo en cuenta las consideraciones siguientes.

* Solo se puede importar un archivo de .graphrunbook en un [runbook gráfico](automation-runbook-types.md#graphical-runbooks)nuevo y solo se pueden crear runbooks gráficos desde un archivo .graphrunbook.
* Un archivo .ps1 que contiene un flujo de trabajo de PowerShell solo se puede importar en un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Si el archivo contiene varios flujos de trabajo de PowerShell, se producirá un error en la importación. Debe guardar cada flujo de trabajo en su propio archivo e importar cada uno por separado.
* Un archivo .ps1 que no contenga un flujo de trabajo se puede importar en un [runbook de PowerShell](automation-runbook-types.md#powershell-runbooks) o en un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Si se importa en un runbook de flujo de trabajo de PowerShell, se convertirá en un flujo de trabajo y los comentarios se incluirán en el runbook especificando los cambios realizados.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-classic-portal"></a>Para importar un Runbook de un archivo con el Portal de Azure clásico
Puede usar el siguiente procedimiento para importar un archivo de script en la Automatización de Azure.  Tenga en cuenta que solo puede importar un archivo. ps1 en un runbook de flujo de trabajo de PowerShell mediante este portal.  Debe usar el Portal de Azure para otros tipos.

1. En el Portal de administración de Azure, seleccione **Automatización** y luego haga clic en el nombre de una cuenta de automatización.
2. Haga clic en **Import**.
3. Haga clic en **Buscar archivo** y busque el archivo de script que va a importar.
4. Si quiere editar el runbook ahora, haga clic en **Editar runbook**. De lo contrario, haga clic en Aceptar.
5. El runbook nuevo aparecerá en la pestaña **Runbooks** para la cuenta de automatización.
6. Debe [publicar el runbook](#publishing-a-runbook) para poder ejecutarlo.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Para importar un runbook de un archivo con el portal de Azure
Puede usar el siguiente procedimiento para importar un archivo de script en la Automatización de Azure.  

> [!NOTE]
> Tenga en cuenta que solo puede importar un archivo. ps1 en un runbook de flujo de trabajo de PowerShell mediante el portal.
> 
> 

1. En el Portal de Azure, abra su cuenta de Automatización.
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Haga clic en el botón **Agregar un runbook** y, después, en **Importar**.
4. Haga clic en **Archivo de runbook** para seleccionar el archivo que se va a importar.
5. Si el campo **Nombre** está habilitado, tiene la opción de cambiarlo.  El nombre del runbook debe empezar por una letra y puede tener letras, números, caracteres de subrayado y guiones.
6. El [tipo de runbook](automation-runbook-types.md) se seleccionará automáticamente, pero puede cambiarlo después de tomar en cuenta las restricciones aplicables. 
7. El nuevo runbook aparecerá en la lista de runbooks para la cuenta de automatización.
8. Debe [publicar el runbook](#publishing-a-runbook) para poder ejecutarlo.

> [!NOTE]
> Después de importar un runbook gráfico o un runbook gráfico de flujo de trabajo de PowerShell, tiene la opción de convertir a otro tipo si así lo desea. No es posible convertir a texto.
> 
> 

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Para importar un runbook de un archivo de script con Windows PowerShell
Puede usar el cmdlet [Import-AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603735.aspx) para importar un archivo de script como un runbook de flujo de trabajo de PowerShell en borrador. Si ya existe el runbook, la importación dará error, a menos que utilice el parámetro *-Force* . 

Los comandos de ejemplo siguientes muestran cómo importar un archivo de script en un runbook.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>Publicación de un runbook
Cuando cree o importe un runbook nuevo, debe publicarlo para poder ejecutarlo.  Cada runbook de Automatización tiene una versión de borrador y una versión publicada. Solo es posible ejecutar la versión publicada y solo es posible editar la versión de borrador. Los cambios realizados en la versión de borrador no afectan la versión publicada. Cuando la versión de borrador deba estar lista para su disponibilidad, puede publicarla, lo que sobrescribirá la versión publicada con la versión de borrador.

## <a name="to-publish-a-runbook-using-the-azure-classic-portal"></a>Para publicar un Runbook mediante el Portal de Azure clásico
1. Abra el Runbook en el Portal de Azure clásico.
2. En la parte superior de la pantalla, haga clic en **Autor**.
3. En la parte inferior de la pantalla, haga clic en **Publicar** y, después, haga clic en **Sí** en el mensaje de comprobación.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Para publicar un runbook mediante el portal de Azure
1. Abra el runbook en el portal de Azure.
2. Haga clic en el botón **Editar** .
3. Haga clic en el botón **Publicar** y, después, haga clic en **Sí** en el mensaje de comprobación.

## <a name="to-publish-a-runbook-using-windows-powershell"></a>Para publicar un runbook mediante Windows PowerShell
Puede usar el cmdlet [Publish-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603705.aspx) para publicar un runbook con Windows PowerShell. Los comandos de ejemplo siguientes muestran cómo crear un runbook de ejemplo.

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre las ventajas de la Galería de runbooks y módulos de PowerShell, vea [Galerías de runbooks y módulos para Azure Automation](automation-runbook-gallery.md).
* Para más información acerca de la edición de runbooks de PowerShell y flujo de trabajo de PowerShell, consulte [Edición de runbooks de texto en Automatización de Azure](automation-edit-textual-runbook.md)
* Para más información sobre la creación de runbooks de gráficos, consulte [Creación gráfica en la Automatización de Azure](automation-graphical-authoring-intro.md)




<!--HONumber=Nov16_HO3-->


