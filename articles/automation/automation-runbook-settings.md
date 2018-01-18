---
title: "Configuración de runbooks | Microsoft Docs"
description: "Describe las opciones de configuración para un runbook en Azure Automation y cómo cambiarlas mediante Azure Portal y Windows PowerShell."
services: automation
documentationcenter: 
author: georgewallace
manager: stevenka
editor: tysonn
ms.assetid: a726f20c-a952-48b8-88ee-36d76aa3ac61
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/11/2016
ms.author: bwren
ms.openlocfilehash: 527c40c4a0c5c9a0eb4745f542a8afcc63281416
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2018
---
# <a name="runbook-settings"></a>Configuración de runbook
Cada runbook en Azure Automation tiene varias opciones de configuración para ayudarle a identificar y cambiar su comportamiento de registro. A continuación se describen todas estas opciones, seguido de procedimientos acerca de cómo modificarlas.

## <a name="settings"></a>Configuración
### <a name="name-and-description"></a>Nombre y descripción
No se puede cambiar el nombre de un runbook después de su creación. La descripción es opcional y puede tener hasta 512 caracteres.

### <a name="tags"></a>Etiquetas
Las etiquetas permiten asignar diferentes palabras y frases para ayudar a identificar un runbook. Por ejemplo, cuando se envía un runbook a la [Galería de PowerShell](https://www.powershellgallery.com/), se especifican etiquetas determinadas para identificar qué categorías deben aparecer en el runbook. Puede especificar varias etiquetas para un runbook separándolas por comas.

### <a name="logging"></a>Registro
De forma predeterminada, los registros detallados y de progreso no se escriben en el historial de trabajos. Puede cambiar la configuración de un runbook determinado para estas entradas de registro. Para obtener más información sobre estos registros, consulte [Salida y mensajes de los runbooks](automation-runbook-output-and-messages.md).

## <a name="changing-runbook-settings"></a>Cambio de la configuración del runbook

### <a name="changing-runbook-settings-with-the-azure-portal"></a>Cambio de la configuración del runbook mediante Azure Portal
Puede cambiar la configuración de un runbook en Azure Portal en la hoja **Configuración** del runbook.

1. En Azure Portal, seleccione **Automation** y, a continuación, haga clic en el nombre de una cuenta de Automation.
2. Seleccione la pestaña **Runbooks** .
3. Haga clic en el nombre de un runbook y se le redirigirá a la hoja de configuración del runbook. Aquí podrá especificar o modificar etiquetas, la descripción del runbook, configurar opciones de seguimiento y registro, y acceder herramientas de soporte técnico para ayudarlo a solucionar problemas.     

### <a name="changing-runbook-settings-with-windows-powershell"></a>Cambio de la configuración del runbook mediante Windows PowerShell
Puede utilizar el cmdlet [Set-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603786.aspx) para cambiar la configuración de un runbook. Si desea especificar varias etiquetas, se puede proporcionar una matriz o una sola cadena con valores delimitados por comas al parámetro Tags. Puede obtener las etiquetas actuales con el [Get-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603728.aspx).

Los siguientes comandos de ejemplo muestran cómo establecer las propiedades de un runbook. Este ejemplo agrega tres etiquetas a las etiquetas existentes y especifica que se deben registrar registros detallados.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="next-steps"></a>pasos siguientes
* Para aprender a crear y recuperar los mensajes de salida y de error de los runbooks, consulte [Salida y mensajes de los runbooks](automation-runbook-output-and-messages.md) 
* Para saber cómo agregar un runbook que ya haya desarrollado la Comunidad u otro medio, o para crear sus propios runbooks, consulte [Creación o importación de un runbook](automation-creating-importing-runbook.md). 

