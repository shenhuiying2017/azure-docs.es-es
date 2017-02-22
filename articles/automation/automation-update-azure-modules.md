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
ms.date: 02/13/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 7a2999b3b1a54668f6ef45433efabd5a495418fe
ms.openlocfilehash: ec84df70d4a77e3b81a88aa286fc492d92e3e753


---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Actualización de módulos de Azure PowerShell en Azure Automation

Los módulos de Azure PowerShell más comunes se proporcionan de forma predeterminada en cada cuenta de Automation.  El equipo de Azure actualiza los módulos de Azure con regularidad, por lo que en la cuenta de Automation se proporciona una manera de actualizar los módulos de la cuenta cuando hay nuevas versiones disponibles.

## <a name="updating-azure-modules"></a>Actualización de módulos de Azure

1. En la hoja Módulos de la cuenta de Automation existe una opción denominada **Update Azure Modules** (Actualizar módulos de Azure).  Siempre está habilitada.<br><br> ![Opción Update Azure Modules (Actualizar módulos de Azure) en la hoja Módulos](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Haga clic en **Update Azure Modules** (Actualizar módulos de Azure) y aparecerá una notificación de confirmación que le preguntará si desea continuar.<br><br> ![Notificación de Update Azure Modules (Actualizar módulos de Azure)](media/automation-update-azure-modules/automation-update-azure-modules-notification.png)

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

Como parte del proceso de actualización, las programaciones de cualquier runbook programado se actualizarán para utilizar la versión más reciente del módulo.

Si usa cmdlets de estos módulos de Azure PowerShell en sus runbooks para administrar recursos de Azure, le interesará realizar este proceso de actualización cada mes, por ejemplo, para asegurarse de que dispone de los módulos más recientes.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los módulos de integración y cómo crear módulos personalizados para integrar Automation con otros sistemas, servicios o soluciones, consulte [Módulos de integración](automation-integration-modules.md).


<!--HONumber=Feb17_HO2-->


