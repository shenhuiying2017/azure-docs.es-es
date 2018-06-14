---
title: Edición de runbooks de texto en Azure Automation
description: En este artículo, se proporcionan diferentes procedimientos para trabajar con runbooks de PowerShell y de flujo de trabajo de PowerShell en Azure Automation mediante el editor de texto.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 517c339122e493dfc4140acb12a2e181babea019
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192911"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Edición de runbooks de texto en Azure Automation

El editor de texto de Azure Automation sirve para editar [runbooks de PowerShell](automation-runbook-types.md#powershell-runbooks) y [runbooks del flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Además, incluye las características típicas de otros editores de código, como IntelliSense y codificación en colores, junto con otras características especiales que le ayudarán a acceder a recursos habituales de runbooks. En este artículo, se proporcionan pasos detallados para realizar diferentes funciones con este editor.

El editor de texto incluye una característica para insertar código para cmdlets, los recursos y los runbooks secundarios en un runbook. En lugar de escribir en el código personalmente, puede seleccionar entre una lista de recursos disponibles para que se inserte el código adecuado en el runbook.

Cada runbook de Azure Automation tiene dos versiones: una de borrador y otra publicada. Edite la versión de borrador del runbook y después publíquela para que pueda ejecutarse. No se puede editar la versión publicada. Consulte el artículo sobre la [publicación de un runbook](automation-creating-importing-runbook.md#publishing-a-runbook) para obtener más información.

Para trabajar con [runbooks gráficos](automation-runbook-types.md#graphical-runbooks), consulte [Creación gráfica en Azure Automation](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Para editar un runbook con el Portal de Azure

Use el siguiente procedimiento para abrir un runbook para su edición en el editor de texto.

1. En el Portal de Azure, seleccione su cuenta de automatización.
2. En **AUTOMATIZACIÓN DE PROCESOS**, seleccione **Runbooks** para abrir la lista de runbooks.
3. Seleccione el runbook que desea editar y después en el botón **Editar**.
4. Realice las modificaciones necesarias.
5. Haga clic en **Guardar** cuando termine las modificaciones.
6. Haga clic en **Publicar** si desea que la última versión de borrador del runbook se publique.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Para insertar un cmdlet en un runbook

1. En el lienzo del editor de texto, coloque el cursor donde desee colocar el cmdlet.
2. Expanda el nodo **Cmdlets** en el control Biblioteca.
3. Expanda el módulo que contiene el cmdlet que desea usar.
4. Haga clic con el botón derecho en el cmdlet para insertarlo y seleccione **Agregar a lienzo**. Si el cmdlet tiene más de un conjunto de parámetros, se agregará el conjunto predeterminado. También puede expandir el cmdlet para seleccionar un conjunto de parámetros diferente.
5. Se inserta el código para el cmdlet con toda su lista de parámetros.
6. Proporcione un valor adecuado en lugar del tipo de datos entre corchetes, <>, para los parámetros necesarios. Elimine cualquier parámetro que no sea necesario.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Para insertar código de runbook secundario en un runbook

1. En el lienzo del editor de texto, coloque el cursor donde desee colocar el código para el [runbook secundario](automation-child-runbooks.md).
2. Expanda el nodo **Runbooks** en el control Biblioteca.
3. Haga clic con el botón derecho en el runbook para insertarlo y seleccione **Agregar a lienzo**.
4. Se inserta el código para el runbook secundario con los marcadores de posición de los parámetros de runbook.
5. Reemplace los marcadores de posición por valores adecuados para cada parámetro.

### <a name="to-insert-an-asset-into-a-runbook"></a>Para insertar un recurso en un runbook

1. En el lienzo del editor de texto, coloque el cursor donde desee colocar el código para el runbook secundario.
2. Expanda el nodo **Recursos** en el control Biblioteca.
3. Expanda el nodo para el tipo de recurso que desee.
4. Haga clic con el botón derecho en el recurso para insertarlo y seleccione **Agregar a lienzo**. Para [recursos variables](automation-variables.md), seleccione **Agregar "Obtener variable" a lienzo** o **Agregar "Establecer variable" a lienzo** en función de si desea obtener o establecer la variable.
5. El código para el recurso se inserta en el runbook.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Para editar un runbook de Azure Automation mediante Windows PowerShell

Para editar un runbook con Windows PowerShell, use el editor de su elección y guárdelo en un archivo. ps1. Puede usar el cmdlet [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) para recuperar el contenido del runbook y después el cmdlet [Set-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) para reemplazar el borrador del runbook existente por el modificado.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Para recuperar el contenido de un runbook mediante Windows PowerShell

En los siguientes comandos de ejemplo, se muestra cómo se recupera el script para un runbook y se guarda en un archivo de script. En este ejemplo, se recupera la versión de borrador. También es posible recuperar la versión publicada del runbook, aunque esta no se puede cambiar.

```powershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Sample-TestRunbook"
$scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

$runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
$runbookContent = $runbookDefinition.Content

Out-File -InputObject $runbookContent -FilePath $scriptPath
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Para cambiar el contenido de un runbook mediante Windows PowerShell

En los siguientes comandos de ejemplo, se muestra cómo reemplazar el contenido existente de un runbook por el de un archivo de script. Tenga en cuenta que es el mismo procedimiento de ejemplo que en el artículo sobre cómo [importar un runbook desde un archivo de script con Windows PowerShell](automation-creating-importing-runbook.md).

```powershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Sample-TestRunbook"
$scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName
```

## <a name="related-articles"></a>Artículos relacionados

* [Creación o importación de un runbook en Azure Automation](automation-creating-importing-runbook.md)
* [Aprendizaje del flujo de trabajo de Windows PowerShell](automation-powershell-workflow.md)
* [Creación gráfica en Azure Automation](automation-graphical-authoring-intro.md)
* [Certificates](automation-certificates.md)
* [Conexiones](automation-connections.md)
* [Credenciales](automation-credentials.md)
* [Programaciones](automation-schedules.md)
* [Variables](automation-variables.md)
