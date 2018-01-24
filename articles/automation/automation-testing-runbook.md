---
title: Prueba de un runbook en Azure Automation | Microsoft Docs
description: "Antes de publicar un runbook en Azure Automation, puede probarlo para asegurarse de que funciona según lo previsto.  En este artículo se describe cómo probar un runbook y ver su salida."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 7f7db785-52c0-4613-aa12-b02fd32a5182
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2016
ms.author: magoedte;bwren
ms.openlocfilehash: 49e8dfa341940386f15932ec4346c8811effbf0b
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="testing-a-runbook-in-azure-automation"></a>Prueba de un runbook en Azure Automation
Cuando se prueba un runbook, se ejecuta la [versión de borrador](automation-creating-importing-runbook.md#publishing-a-runbook) y se completan todas las acciones que realiza. No se crea ningún historial de trabajos, pero los flujos de [salida](automation-runbook-output-and-messages.md#output-stream) y los de [error y advertencia](automation-runbook-output-and-messages.md#message-streams) se muestran en el panel de salida de la prueba. Los mensajes del [flujo detallado](automation-runbook-output-and-messages.md#message-streams) solo se muestran en el panel de salida si la [variable $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) está establecida en Continue.

Aun cuando se ejecuta la versión de borrador, el runbook ejecuta el flujo de trabajo con normalidad y realiza las acciones correspondientes en los recursos del entorno. Por este motivo, solo debe probar runbooks en recursos no pertenecientes a entornos de producción.

El procedimiento para probar cada [tipo de Runbook](automation-runbook-types.md) es el mismo, y no hay diferencias entre realizar las pruebas en el editor de texto o en el editor gráfico del Portal de Azure.  

## <a name="to-test-a-runbook-in-the-azure-portal"></a>Para probar un runbook en el portal de Azure
Puede trabajar con cualquier [tipo de Runbook](automation-runbook-types.md) en el Portal de Azure.

1. Abra la versión de borrador del runbook en el [editor de texto](automation-edit-textual-runbook.md) o el [editor gráfico](automation-graphical-authoring-intro.md).
2. Haga clic en el botón **Probar** para abrir la hoja Prueba.
3. Si el runbook tiene parámetros, se enumerarán en el panel izquierdo, donde puede proporcionar los valores que se usarán para la prueba.
4. Si quiere ejecutar la prueba en un [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), cambie los **Parámetros de ejecución** a **Hybrid Worker** y seleccione el nombre del grupo de destino.  De lo contrario, mantenga el valor predeterminado **Azure** para ejecutar la prueba en la nube.
5. Haga clic en el botón **Iniciar** para iniciar la prueba.
6. Si el runbook es de tipo [Flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) o [Gráfico](automation-runbook-types.md#graphical-runbooks), puede detenerlo o suspenderlo durante la prueba con los botones situados bajo el panel de salida. Cuando se suspende el runbook, este completa la actividad que está realizando en ese momento antes de suspenderse. Una vez suspendido, puede detener o reiniciar el runbook.
7. Revise la salida del runbook en el panel de salida.

## <a name="next-steps"></a>Pasos siguientes
* Para obtener información sobre cómo crear o importar un runbook, consulte [Creación o importación de un runbook en Azure Automation](automation-creating-importing-runbook.md)
* Para obtener más información sobre la creación de gráficos, consulte [Creación gráfica en Azure Automation](automation-graphical-authoring-intro.md)
* Para empezar a trabajar con runbooks de flujo de trabajo de PowerShell, consulte [Mi primer runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md)
* Para más información sobre cómo configurar runbooks para devolver los errores y mensajes de estado, incluidas las prácticas recomendadas, consulte [Salidas de runbook y mensajes en Azure Automation](automation-runbook-output-and-messages.md)

