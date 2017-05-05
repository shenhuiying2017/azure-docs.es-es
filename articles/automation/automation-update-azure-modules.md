---
title: "Actualización de módulos de Azure en Azure Automation | Microsoft Docs"
description: "En este artículo se describe cómo puede actualizar ahora módulos comunes de Azure PowerShell proporcionados de forma predeterminada en Azure Automation."
services: automation
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 6765ea93dd4e4e2594fb147dd19120aec058a2f5
ms.lasthandoff: 04/21/2017


---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Actualización de módulos de Azure PowerShell en Azure Automation

Los módulos de Azure PowerShell más comunes se proporcionan de forma predeterminada en cada cuenta de Automation.  El equipo de Azure actualiza los módulos de Azure con regularidad, por lo que en la cuenta de Automation se proporciona una manera de actualizar los módulos de la cuenta cuando hay nuevas versiones disponibles en el portal.  

## <a name="updating-azure-modules"></a>Actualización de módulos de Azure

1. En la hoja Módulos de la cuenta de Automation existe una opción denominada **Update Azure Modules** (Actualizar módulos de Azure).  Siempre está habilitada.<br><br> ![Opción Update Azure Modules (Actualizar módulos de Azure) en la hoja Módulos](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Haga clic en **Update Azure Modules** (Actualizar módulos de Azure) y aparecerá una notificación de confirmación que le preguntará si desea continuar.<br><br> ![Notificación de Update Azure Modules (Actualizar módulos de Azure)](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Haga clic en **Sí** y comenzará el proceso de actualización de módulos.  El proceso de actualización tarda aproximadamente 15-20 minutos en actualizar los siguientes módulos:

  * Las tablas de Azure
  *    Azure.Storage
  *    AzureRm.Automation
  *    AzureRm.Compute
  *    AzureRm.Profile
  *    AzureRm.Resources
  *    AzureRm.Sql
  * AzureRm.Storage

    Si los módulos ya están actualizados, el proceso se completará en unos segundos.  Cuando el proceso de actualización se complete, recibirá una notificación.<br><br> ![Estado de actualización de Update Azure Modules (Actualizar módulos de Azure)](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

Siempre que se crea una programación, los trabajos posteriores que se ejecutan en esa programación usan los módulos de la cuenta de Automation en el momento en que se creó la programación.  Para empezar a usar los módulos actualizados con los runbooks programados, debe desvincular y volver a vincular la programación con ese runbook.   

Si usa cmdlets de estos módulos de Azure PowerShell en sus runbooks para administrar recursos de Azure, le interesará realizar este proceso de actualización cada mes, por ejemplo, para asegurarse de que dispone de los módulos más recientes.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los módulos de integración y cómo crear módulos personalizados para integrar Automation con otros sistemas, servicios o soluciones, consulte [Módulos de integración](automation-integration-modules.md).
