---
title: "Incorporación de soluciones de actualización y control de cambios a Azure Automation | Microsoft Docs"
description: "Aprenda a incorporar soluciones de actualización y control de cambios a Azure Automation."
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: edae1156-2dc7-4dab-9e5c-bf253d3971d0
ms.service: automation
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2017
ms.author: eamono
ms.custom: mvc
ms.openlocfilehash: e277aa44dfe625780d72a78010f0638c73a6b182
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Incorporación de soluciones de actualización y control de cambios a Azure Automation

En este tutorial, aprenderá a incorporar soluciones de actualización, control de cambios e inventario en máquinas virtuales a Azure Automation. Así, sabrá:

> [!div class="checklist"]
> * Incorporar una máquina virtual de Azure
> * Habilitar soluciones
> * Instalar y actualizar módulos
> * Importación de un runbook de incorporación
> * Inicio del runbook

## <a name="prerequisites"></a>Requisitos previos

Los siguientes requisitos son necesarios para completar este tutorial:

* Suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o suscribirse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Una [cuenta de Automation](automation-offering-get-started.md) para administrar máquinas.
* Una [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para incorporar.

## <a name="onboard-an-azure-vm"></a>Incorporar una máquina virtual de Azure

Para incorporar máquinas virtuales de Azure automáticamente, debe incorporar una máquina virtual existente con la solución de control de cambios o de administración de actualizaciones. En este paso, se incorpora una máquina virtual con la solución de administración de actualizaciones y control de cambios.

### <a name="enable-change-tracking-and-inventory"></a>Habilitación de Change Tracking e Inventario

La solución Change Tracking e Inventario permite realizar un [control de cambios](automation-vm-change-tracking.md) e [inventario](automation-vm-inventory.md) en sus máquinas virtuales. En este paso, se habilita la solución en una máquina virtual.

1. En el menú izquierdo, seleccione **Cuentas de Automation** y, a continuación, seleccione la cuenta de automatización de la lista.
1. Seleccione **Inventario** en **Administración de configuración**.
1. Seleccione un área de trabajo existente de Log Analytics o cree una nueva. Haga clic en el botón **Habilitar**.

![Incorporación de la solución de actualización](media/automation-onboard-solutions/inventory-onboard.png)

Cuando se complete la notificación de que se ha incorporado la solución de control de cambios e inventario, haga clic en **Administración de actualizaciones** en **Administración de configuración**.

### <a name="enable-update-management"></a>Habilitación de la Administración de actualizaciones

La solución Administración de actualizaciones permite administrar las actualizaciones y las revisiones para las máquinas virtuales Windows de Azure. Puede evaluar rápidamente el estado de las actualizaciones disponibles, programar la instalación de las actualizaciones necesarias y revisar los resultados de la implementación para comprobar si las actualizaciones se aplicaron correctamente en la máquina virtual. En este paso, se habilita la solución en la máquina virtual.

1. En su cuenta de Automation, seleccione **Administración de actualizaciones** en **Administración de actualizaciones**.
1. El área de trabajo de Log Analytics seleccionada es la misma área de trabajo que se usa en el paso anterior. Haga clic en **Habilitar** para incorporar la solución Administración de actualizaciones.

![Incorporación de la solución de actualización](media/automation-onboard-solutions/update-onboard.png)

Mientras se instala la solución Administración de actualizaciones, se muestra un banner azul. Cuando la solución este habilitada, seleccione **Change Tracking** en **Administración de configuración** para ir al paso siguiente.

### <a name="select-azure-vm-to-be-managed"></a>Seleccione la máquina virtual de Azure que va a ser administrada

Ahora que las soluciones están habilitadas, puede agregar la máquina virtual de Azure que quiera incorporar a esas soluciones.

1. Desde su cuenta de Automation, en la página **Change Tracking**, seleccione **+ Agregar máquina virtual de Azure** para agregar la máquina virtual.

1. Seleccione la máquina virtual en la lista y seleccione **Habilitar**. Esta acción habilita la solución Change Tracking e Inventario en la máquina virtual.

   ![Habilitar la solución de actualizaciones para la máquina virtual](media/automation-onboard-solutions/enable-change-tracking.png)

1. Cuando finalice la notificación de incorporación de la máquina virtual, en su cuenta de Automation, seleccione **Administración de actualizaciones** en **Administración de actualizaciones**.

1. Seleccione **+ Agregar una máquina virtual de Azure** para agregar la máquina virtual.

1. Seleccione la máquina virtual en la lista y seleccione **Habilitar**. Esta acción habilita la solución Administración de actualizaciones de la máquina virtual.

   ![Habilitar la solución de actualizaciones para la máquina virtual](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Si no espera hasta que la otra solución finalice, al habilitar la solución siguiente recibirá un mensaje que indica: *La instalación de otra solución está en curso en esta máquina virtual o en una distinta. Cuando finalice esa instalación estará habilitado el botón Habilitar y podrá solicitar la instalación de la solución en esta máquina virtual.*

## <a name="install-and-update-modules"></a>Instalar y actualizar módulos

Para automatizar correctamente la incorporación de soluciones, es necesario actualizar a los módulos de Azure más recientes e importar `AzureRM.OperationalInsights`.

## <a name="update-azure-modules"></a>Actualización de los módulos de Azure

En la cuenta de Automation, seleccione **Módulos** en **Recursos compartidos**. Seleccione **Actualizar módulos de Azure** para actualizar los módulos de Azure a la versión más reciente. Seleccione **Sí** en el mensaje para actualizar todos los módulos de Azure existentes a la versión más reciente.

![Actualización de los módulos](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>Instalación del módulo AzureRM.OperationalInsights

Desde la página **Módulos**, seleccione **Explorar la galería** para abrir la galería de módulos. Busque AzureRM.OperationalInsights e importe este módulo en la cuenta de Automation.

![Importar el módulo OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Importación de un runbook de incorporación

1. En la cuenta de Automation, seleccione **Runbooks** en **Automatización de procesos**.
1. Seleccione **Explorar la galería**.
1. Busque **actualización y control de cambios**, haga clic en el runbook y seleccione **Importar** en la página **Ver código fuente**. Seleccione **Aceptar** para importar el runbook en la cuenta de Automation.

  ![Importación de un runbook de incorporación](media/automation-onboard-solutions/import-from-gallery.png)

1. En la página **Runbook**, seleccione **Editar** y, a continuación, seleccione **Publicar**. En el cuadro de diálogo **Publicar runbook**, seleccione **Sí** para publicar el runbook.

## <a name="start-the-runbook"></a>Inicio del runbook

Para iniciar este runbook, debe haber incorporado soluciones de actualización o de control de cambios a una máquina virtual de Azure. Se requiere una máquina virtual existente y un grupo de recursos con la solución incorporada para los parámetros.

1. Abra el runbook Enable-MultipleSolution.

   ![Runbooks de varias soluciones](media/automation-onboard-solutions/runbook-overview.png)

1. Haga clic en el botón Inicio y escriba los siguientes valores de parámetros.

   * **VMNAME**: dejar en blanco. El nombre de una máquina virtual existente para incorporar a la solución de actualización o de control de cambios. Deje este valor en blanco para incorporar todas las máquinas virtuales del grupo de recursos.
   * **VMRESOURCEGROUP**: el nombre del grupo de recursos de las máquinas virtuales que van a ser incorporadas.
   * **SUBSCRIPTIONID**: dejar en blanco. El identificador de la suscripción de la nueva máquina virtual que se va a incorporar. Deje este valor en blanco para usar la suscripción del área de trabajo. Cuando se proporcione un identificador de suscripción diferente, también se debe agregar la cuenta de ejecución de esta cuenta de Automation como colaborador de esta suscripción.
   * **ALREADYONBOARDEDVM**: el nombre de la máquina virtual que se incorporó manualmente a la solución de actualización o de control de cambios.
   * **ALREADYONBOARDEDVMRESOURCEGROUP**: el nombre del grupo de recursos del que es miembro la máquina virtual.
   * **SOLUTIONTYPE**: escriba **Actualizaciones** o **Control de cambios**

   ![Parámetros del runbook Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

1. Seleccione **Aceptar** para iniciar el trabajo del runbook.
1. Supervise el progreso y los errores en la página de trabajos de runbook.

## <a name="next-steps"></a>pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Incorporar máquinas virtuales de Azure manualmente
> * Instalar y actualizar los módulos de Azure necesarios
> * Importar un runbook que incorpora máquinas virtuales de Azure
> * Iniciar el runbook que incorpora máquinas virtuales de Azure automáticamente

Siga este vínculo para más información sobre programación de runbooks.

> [!div class="nextstepaction"]
> [Programación de runbooks](automation-schedules.md).
