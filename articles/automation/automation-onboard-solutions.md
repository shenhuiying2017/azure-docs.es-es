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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: eamono
ms.openlocfilehash: 9ae5e9ebcbcf3af61318eac98defc6b249417f41
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2017
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Incorporación de soluciones de actualización y control de cambios a Azure Automation

En este tutorial, aprenderá a incorporar soluciones de actualización, control de cambios e inventario en máquinas virtuales a Azure Automation. Así, sabrá:

> [!div class="checklist"]
> * Incorporar máquinas virtuales de Azure manualmente
> * Instalar y actualizar los módulos de Azure necesarios
> * Importar un runbook que incorpora máquinas virtuales de Azure
> * Iniciar el runbook que incorpora máquinas virtuales de Azure automáticamente

## <a name="prerequisites"></a>Requisitos previos

Los siguientes requisitos son necesarios para completar este tutorial.
+ Suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o suscribirse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
+ Una [cuenta de Automation](automation-offering-get-started.md) para administrar máquinas.

## <a name="onboard-an-azure-virtual-machine-manually"></a>Incorporación de máquinas virtuales de Azure manualmente

1.  Abra la cuenta de Automation.
2.  Haga clic en la página Inventario.
![Incorporación de la solución de inventario](media/automation-onboard-solutions/inventory-onboard.png)
3.  Seleccione un área de trabajo existente de Log Analytics o cree una nueva. Haga clic en el botón Habilitar.
4.  Cuando se complete la notificación de que se ha incorporado la solución de seguimiento de cambios e inventario, haga clic en la página Update Management.
![Incorporación de la solución de actualización](media/automation-onboard-solutions/update-onboard.png)
4.  Haga clic en botón Habilitar para incorporar la solución de actualización.
5.  Cuando la notificación de incorporación de la solución de actualización se haya completado, haga clic en la página Control de cambios.
![Incorporación del control de cambios](media/automation-onboard-solutions/change-tracking-onboard-vm.png)
6.  Haga clic en el botón Agregar máquina virtual de Azure.
![Selección de la máquina virtual de control de cambios](media/automation-onboard-solutions/enable-change-tracking.png)
7.  Seleccione una máquina virtual de Azure y haga clic en el botón Habilitar para incorporarla a la solución de inventario y control de cambios.
8.  Cuando la notificación de incorporación de la máquina virtual se haya completado, haga clic en la página Update Management.
![Incorporación de máquinas virtuales a Update Management](media/automation-onboard-solutions/update-onboard-vm.png)
9.  Haga clic en el botón Agregar máquina virtual de Azure.
![Selección de la máquina virtual para Update Management](media/automation-onboard-solutions/enable-update.png)
10.  Seleccione una máquina virtual de Azure y haga clic en el botón Habilitar para incorporarla a la solución Update Management.

## <a name="install-and-update-required-azure-modules"></a>Instalación y actualización de los módulos de Azure necesarios

Para automatizar correctamente la incorporación de soluciones, es necesario actualizar a los módulos de Azure más recientes e importar AzureRM.OperationalInsights.
1.  Haga clic en la página Módulos.
![Actualización de los módulos](media/automation-onboard-solutions/update-modules.png)
2.  Haga clic en el botón Actualizar módulos de Azure para actualizar los módulos a la versión más reciente. Espere a que finalicen las actualizaciones.
3.  Haga clic en el botón Examinar la galería para abrir la galería de módulos.
![Importación del módulo OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)
4.  Busque AzureRM.OperationalInsights e importe este módulo en la cuenta de Automation.

## <a name="import-a-runbook-that-onboards-solutions-to-azure-vms"></a>Importación de un runbook que incorpora las soluciones a las máquinas virtuales de Azure

1.  Haga clic en la página Runbooks en la categoría "AUTOMATIZACIÓN DE PROCESOS".
2.  Haga clic en el botón "Examinar galería".
3.  Busque "actualización y control de cambios" e importe el runbook en la cuenta de Automation.
![Importación de un runbook de incorporación](media/automation-onboard-solutions/import-from-gallery.png)
4.  Haga clic en "Editar" para ver el origen del runbook y haga clic en el botón "Publicar".
![Importación de un runbook de incorporación](media/automation-onboard-solutions/publish-runbook.png)

## <a name="start-the-runbook-that-onboards-azure-vms-automatically"></a>Inicio del runbook que incorpora automáticamente máquinas virtuales de Azure

Para iniciar este runbook, debe haber incorporado soluciones de actualización o de control de cambios a una máquina virtual de Azure. Se requiere una máquina virtual existente y un grupo de recursos con la solución incorporada para los parámetros.
1.  Abra el runbook Enable-MultipleSolution.
![Runbooks de varias soluciones](media/automation-onboard-solutions/runbook-overview.png)
2.  Haga clic en el botón Inicio y escriba los siguientes valores de parámetros.
    *   VMNAME. El nombre de una máquina virtual existente para incorporar a la solución de actualización o de control de cambios. Deje este valor en blanco para incorporar todas las máquinas virtuales del grupo de recursos.
    *   VMRESOURCEGROUP. El nombre del grupo de recursos del que es miembro la máquina virtual.
    *   SUBSCRIPTIONID. El identificador de la suscripción donde se encuentra la nueva máquina virtual que se va a incorporar. Deje este valor en blanco para usar la suscripción del área de trabajo. Cuando se proporcione un identificador de suscripción diferente, también se debe agregar la cuenta de ejecución de esta cuenta de Automation como colaborador de esta suscripción.
    *   ALREADYONBOARDEDVM. El nombre de la máquina virtual que se incorporó manualmente a la solución de actualización o de control de cambios.
    *   ALREADYONBOARDEDVMRESOURCEGROUP. El nombre del grupo de recursos del que es miembro la máquina virtual.
    *   SOLUTIONTYPE. Escriba "Actualización" o "Control de cambios".
    
    ![Parámetros de runbook de varias soluciones](media/automation-onboard-solutions/runbook-parameters.png)
3.  Haga clic en Aceptar para iniciar el trabajo del runbook.
4.  Supervise el progreso y los errores en la página de trabajos de runbook.

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte [Programación de runbooks](automation-schedules.md).