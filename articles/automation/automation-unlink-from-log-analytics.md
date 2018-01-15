---
title: "Desvinculación de la cuenta de Azure Automation de Log Analytics | Microsoft Docs"
description: "Este artículo proporciona información general sobre cómo desvincular la cuenta de Azure Automation desde un área de trabajo de OMS."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: 415efd54759f4ff87c53f31308450590dc71466b
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="how-to-unlink-your-automation-account-from-a-log-analytics-workspace"></a>Procedimiento para desvincular su cuenta de Automation de un área de trabajo de Log Analytics

Azure Automation se integra con Log Analytics no solo para admitir la supervisión de los trabajos de runbook a través de todas las cuentas de Automation, sino que también es necesario cuando se importan las siguientes soluciones que dependen de Log Analytics:

* [Administración de actualizaciones](../operations-management-suite/oms-solution-update-management.md)
* [Seguimiento de cambios](../log-analytics/log-analytics-change-tracking.md)
* [Inicio y detención de máquinas virtuales durante las horas de trabajo](automation-solution-vm-management.md)
 
Si decide que ya no desea integrar su cuenta de Automation con Log Analytics, puede desvincular la cuenta directamente desde Azure Portal.  Antes de continuar, primero deberá quitar las soluciones mencionadas anteriormente; en caso contrario, este proceso no podrá continuar.  Revise el tema de la solución concreto que ha importado para conocer los pasos necesarios para quitarla.  

Después de quitar estas soluciones, puede realizar los pasos siguientes para desvincular la cuenta de Automation.

## <a name="unlink-workspace"></a>Unlink workspace (Desvincular área de trabajo)

1. En Azure Portal, abra su cuenta de Automation y, en la página de la cuenta de Automation, seleccione **Desvincular área de trabajo**  en la sección **Recursos relacionados** de la izquierda.<br><br> ![Opción para desvincular el área de trabajo](media/automation-unlink-from-log-analytics/automation-unlink-workspace-option.png)<br><br>  
2. En la página Desvincular área de trabajo, haga clic en **Desvincular área de trabajo**.<br><br> ![Página Desvincular área de trabajo](media/automation-unlink-from-log-analytics/automation-unlink-workspace-blade.png).<br><br>  Recibirá un aviso para comprobar que desea continuar.<br><br>
3. Aunque Azure Automation trate de desvincular la cuenta del área de trabajo de Log Analytics, puede seguir el progreso en **Notificaciones** en el menú.

Si ha usado la solución de administración de actualizaciones, también puede quitar los siguientes elementos que ya no necesite después de quitar la solución.

* Actualice las programaciones.  Cada uno tendrá nombres que coinciden con las implementaciones de actualizaciones que se ha creado.

* Se crean grupos de trabajo híbridos para la solución.  Cada uno de ellos se llamará de forma similar a machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Si ha usado la solución de inicio y detención de máquinas virtuales durante las horas de trabajo, también puede quitar los siguientes elementos que ya no necesite después de quitar la solución.

* Programaciones de runbook de inicio y detención de máquinas virtuales 
* Runbooks de inicio y detención de máquinas virtuales
* variables   

## <a name="next-steps"></a>pasos siguientes

Para volver a configurar la cuenta de Automation para integrarla con Log Analytics de OMS, consulte [Reenvío del estado de un trabajo y de transmisiones de trabajos desde Automation a Log Analytics (OMS)](automation-manage-send-joblogs-log-analytics.md). 