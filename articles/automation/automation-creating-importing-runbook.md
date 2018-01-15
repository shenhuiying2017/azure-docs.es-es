---
title: "Creación o importación de un runbook en Azure Automation"
description: "En este artículo se describe cómo crear un runbook nuevo en Azure Automation o importar uno desde un archivo."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 24414362-b690-4474-8ca7-df18e30fc31d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 0484b1f230a8544e3de2388df2cbdab3b54f9d3d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Creación o importación de un runbook en Azure Automation
Para agregar un runbook a Azure Automation, puede [crear uno](#creating-a-new-runbook) o importar un runbook existente desde un archivo o desde la [Galería de runbooks](automation-runbook-gallery.md). Este artículo ofrece información sobre cómo crear e importar runbooks de un archivo.  Puede obtener todos los detalles sobre el acceso a los módulos y los runbooks de la comunidad en [Galería de runbooks y módulos para Azure Automation](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Creación de un nuevo runbook
Puede crear un nuevo runbook en Azure Automation mediante uno de los portales de Azure o Windows PowerShell. Cuando se haya creado el runbook, puede editarlo con la información de [Aprendizaje del flujo de trabajo de Windows PowerShell](automation-powershell-workflow.md) y [Creación gráfica en Azure Automation](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Para crear un nuevo runbook de Azure Automation con Azure Portal
1. En Azure Portal, abra su cuenta de Automation.
2. Desde el centro, seleccione **Runbooks** para abrir la lista de runbooks.
3. Haga clic en el botón **Agregar un Runbook** y, después, en **Crear un runbook nuevo**.
4. Escriba un **Nombre** para el runbook y seleccione su [Tipo](automation-runbook-types.md). El nombre del runbook debe empezar por una letra y puede tener letras, números, caracteres de subrayado y guiones.
5. Haga clic en **Crear** para crear el runbook y abra el editor.

### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Para crear un nuevo runbook de Azure Automation con Windows PowerShell
Puede usar el cmdlet [New-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx) para crear un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) vacío. Puede especificar el parámetro **Name** para crear un runbook vacío que puede editar posteriormente, o bien puede especificar el parámetro **Path** para importar un archivo de runbook. El parámetro **Type** también se debe incluir para especificar uno de los cuatro tipos de runbook.

Los comandos de ejemplo siguientes muestran cómo crear un nuevo runbook vacío.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Importar un runbook de un archivo en Azure Automation
Puede crear un nuevo runbook en Azure Automation mediante la importación de un script de PowerShell o un flujo de trabajo de PowerShell (extensión. ps1), un runbook gráfico exportado (.graphrunbook) o un script de Python 2 (extensión .py).  Necesita especificar el [tipo de runbook](automation-runbook-types.md) que se creará durante la importación teniendo en cuenta las consideraciones siguientes.

* Solo se puede importar un archivo de .graphrunbook en un [runbook gráfico](automation-runbook-types.md#graphical-runbooks)nuevo y solo se pueden crear runbooks gráficos desde un archivo .graphrunbook.
* Un archivo .ps1 que contiene un flujo de trabajo de PowerShell solo se puede importar en un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Si el archivo contiene varios flujos de trabajo de PowerShell, se producirá un error en la importación. Debe guardar cada flujo de trabajo en su propio archivo e importar cada uno por separado.
* Un archivo .ps1 que no contenga un flujo de trabajo se puede importar en un [runbook de PowerShell](automation-runbook-types.md#powershell-runbooks) o en un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Si se importa en un runbook de flujo de trabajo de PowerShell, se convertirá en un flujo de trabajo y se incluirán comentarios en el runbook especificando los cambios realizados.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Para importar un runbook desde un archivo con Azure Portal
Puede usar el siguiente procedimiento para importar un archivo de script en Azure Automation.  

> [!NOTE]
> Tenga en cuenta que solo puede importar un archivo. ps1 en un runbook de flujo de trabajo de PowerShell mediante el portal.
> 
> 

1. En Azure Portal, abra su cuenta de Automation.
2. Desde el centro, seleccione **Runbooks** para abrir la lista de runbooks.
3. Haga clic en el botón **Agregar un runbook** y, después, en **Importar**.
4. Haga clic en **Archivo de runbook** para seleccionar el archivo que se va a importar.
5. Si el campo **Nombre** está habilitado, tiene la opción de cambiarlo.  El nombre del runbook debe empezar por una letra y puede tener letras, números, caracteres de subrayado y guiones.
6. El [tipo de runbook](automation-runbook-types.md) se seleccionará automáticamente, pero puede cambiarlo después de tomar en cuenta las restricciones aplicables. 
7. El runbook nuevo aparece en la lista de runbooks de la cuenta de Automation.
8. Debe [publicar el runbook](#publishing-a-runbook) para poder ejecutarlo.

> [!NOTE]
> Después de importar un runbook gráfico o un runbook gráfico de flujo de trabajo de PowerShell, tiene la opción de convertir a otro tipo si así lo desea. No es posible convertir a runbook de texto.
>  
> 

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Para importar un runbook de un archivo de script con Windows PowerShell
Puede usar el cmdlet [Import-AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603735.aspx) para importar un archivo de script como un runbook de flujo de trabajo de PowerShell en borrador. Si ya existe el runbook, la importación dará error, a menos que utilice el parámetro *-Force*. 

Los comandos de ejemplo siguientes muestran cómo importar un archivo de script en un runbook.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>Publicación de un runbook
Cuando cree o importe un runbook nuevo, debe publicarlo para poder ejecutarlo.  Cada runbook de Automation tiene una versión de borrador y una versión publicada. Solo es posible ejecutar la versión publicada y solo es posible editar la versión de borrador. Los cambios realizados en la versión de borrador no afectan la versión publicada. Cuando la versión de borrador deba estar lista para su disponibilidad, puede publicarla, lo que sobrescribirá la versión publicada con la versión de borrador.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Para publicar un runbook mediante Azure Portal
1. Abra el runbook en Azure Portal.
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
* Para más información acerca de la edición de runbooks de PowerShell y flujo de trabajo de PowerShell, consulte [Edición de runbooks de texto en Azure Automation](automation-edit-textual-runbook.md)
* Para más información sobre la creación de runbooks de gráficos, consulte [Creación gráfica en Azure Automation](automation-graphical-authoring-intro.md)

