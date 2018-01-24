---
title: "Actualización de módulos de Azure en Azure Automation | Microsoft Docs"
description: "En este artículo se describe cómo puede actualizar ahora módulos comunes de Azure PowerShell proporcionados de forma predeterminada en Azure Automation."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2017
ms.author: magoedte
ms.openlocfilehash: f5e7c66cfd26bd6927d48ffd8bc0f82e9a3e2d13
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Actualización de módulos de Azure PowerShell en Azure Automation

Los módulos de Azure PowerShell más comunes se proporcionan de forma predeterminada en cada cuenta de Automation.  El equipo de Azure actualiza los módulos de Azure con regularidad, por lo que en la cuenta de Automation se proporciona una manera de actualizar los módulos de la cuenta cuando hay nuevas versiones disponibles en el portal.  

Dado que el grupo del producto actualiza los módulos con regularidad, pueden producirse cambios con los cmdlets incluidos, lo que puede afectar negativamente a los runbooks según el tipo de cambio, por ejemplo, al cambiar el nombre de un parámetro o al dejar de usar un cmdlet por completo. Para evitar afectar a los runbooks y a los procesos que automatizan, se recomienda encarecidamente probarlos y validarlos antes de continuar.  Si no tiene una cuenta de Automation dedicada destinada para este propósito, considere la posibilidad de crear una para poder probar muchos escenarios y permutaciones diferentes durante el desarrollo de los runbooks, además de los cambios iterativos, como la actualización de los módulos de PowerShell.  Una vez validados los resultados y aplicados los cambios necesarios, continúe con la coordinación de la migración de los runbooks que requieran modificación y realice la actualización tal y como se describe a continuación en un entorno de producción.     

## <a name="updating-azure-modules"></a>Actualización de módulos de Azure

1. En la página Módulos de la cuenta de Automation existe una opción denominada **Actualizar módulos de Azure**. Siempre está habilitada.<br><br> ![Opción Actualizar módulos de Azure de la página Módulos](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Haga clic en **Actualizar módulos de Azure** y aparecerá una notificación de confirmación que le preguntará si desea continuar.<br><br> ![Notificación de Update Azure Modules (Actualizar módulos de Azure)](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Haga clic en **Sí** y comenzará el proceso de actualización de módulos. El proceso de actualización tarda aproximadamente 15-20 minutos en actualizar los siguientes módulos:

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Si los módulos ya están actualizados, el proceso se completará en unos segundos. Cuando el proceso de actualización se complete, recibirá una notificación.<br><br> ![Estado de actualización de Update Azure Modules (Actualizar módulos de Azure)](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> Azure Automation utiliza los módulos más recientes en su cuenta de Automation al ejecutar un nuevo trabajo programado.    

Si usa cmdlets de estos módulos de Azure PowerShell en sus runbooks para administrar recursos de Azure, le interesará realizar este proceso de actualización cada mes, por ejemplo, para asegurarse de que dispone de los módulos más recientes.

## <a name="next-steps"></a>pasos siguientes

* Para más información sobre los módulos de integración y cómo crear módulos personalizados para integrar Automation con otros sistemas, servicios o soluciones, consulte [Módulos de integración](automation-integration-modules.md).

* Considere la integración del control de origen con [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) o [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) para controlar las versiones de la cartera de configuración y el runbook de Automation y administrarlas de forma centralizada.  