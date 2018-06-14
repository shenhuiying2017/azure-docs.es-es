---
title: Desvinculación de una cuenta de Azure Automation de Log Analytics
description: En este artículo se proporciona información general sobre cómo desvincular la cuenta de Azure Automation de un área de trabajo de Log Analytics.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f51103045f6a0cac1b1ed4f32200eaf7bef9cf24
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193883"
---
# <a name="how-to-unlink-your-automation-account-from-a-log-analytics-workspace"></a>Procedimiento para desvincular su cuenta de Automation de un área de trabajo de Log Analytics

Azure Automation se integra con Log Analytics no solo para admitir la supervisión de los trabajos de runbook a través de todas las cuentas de Automation, sino que también es necesario cuando se importan las siguientes soluciones que dependen de Log Analytics:

* [Administración de actualizaciones](../operations-management-suite/oms-solution-update-management.md)
* [Seguimiento de cambios](../log-analytics/log-analytics-change-tracking.md)
* [Inicio y detención de máquinas virtuales durante las horas de trabajo](automation-solution-vm-management.md)

Si decide que ya no desea integrar su cuenta de Automation con Log Analytics, puede desvincular la cuenta directamente desde Azure Portal.  Antes de continuar, primero deberá quitar las soluciones mencionadas anteriormente; en caso contrario, este proceso no podrá continuar. Revise el tema de la solución concreto que ha importado para conocer los pasos necesarios para quitarla.

Después de quitar estas soluciones, puede realizar los pasos siguientes para desvincular la cuenta de Automation.

> [!NOTE]
> Algunas soluciones que incluyen versiones anteriores de la solución de supervisión de SQL Azure pueden haber creado recursos de automatización y también puede que tengan que quitarse antes de desvincularse del área de trabajo.

## <a name="unlink-workspace"></a>Unlink workspace (Desvincular área de trabajo)

1. En Azure Portal, abra su cuenta de Automation y, en la página de la cuenta de Automation, seleccione **Desvincular área de trabajo**  en la sección **Recursos relacionados** de la izquierda.

   ![Opción Desvincular área de trabajo](media/automation-unlink-from-log-analytics/automation-unlink-workspace-option.png)

1. En la página Desvincular área de trabajo, haga clic en **Desvincular área de trabajo**.

   ![Página Desvincular área de trabajo](media/automation-unlink-from-log-analytics/automation-unlink-workspace-blade.png).

   Recibirá un aviso para comprobar que desea continuar.

1. Aunque Azure Automation trate de desvincular la cuenta del área de trabajo de Log Analytics, puede seguir el progreso en **Notificaciones** en el menú.

Si ha usado la solución de administración de actualizaciones, también puede quitar los siguientes elementos que ya no necesite después de quitar la solución.

* Actualice las programaciones.  Cada uno tendrá nombres que coinciden con las implementaciones de actualizaciones que se ha creado.

* Se crean grupos de trabajo híbridos para la solución.  Cada uno de ellos se llamará de forma similar a machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Si ha usado la solución de inicio y detención de máquinas virtuales durante las horas de trabajo, también puede quitar los siguientes elementos que ya no necesite después de quitar la solución.

* Programaciones de runbook de inicio y detención de máquinas virtuales
* Runbooks de inicio y detención de máquinas virtuales
* variables

## <a name="next-steps"></a>Pasos siguientes

Para volver a configurar la cuenta de Automation para integrarla con Log Analytics, consulte [Reenvío del estado de un trabajo y de transmisiones de trabajos desde Automation a Log Analytics](automation-manage-send-joblogs-log-analytics.md).