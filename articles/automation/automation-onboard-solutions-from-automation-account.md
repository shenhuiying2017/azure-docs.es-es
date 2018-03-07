---
title: "Obtenga información sobre la incorporación de las soluciones Update Management, Change Tracking e Inventory en Azure Automation"
description: "Obtenga información acerca de cómo incorporar una máquina virtual de Azure con las soluciones Update Management, Change Tracking e Inventory que forman parte de Azure Automation"
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 02/28/2018
ms.topic: article
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 670a0c2a11ebfe09406233ab4b454b2e9c2ba0e0
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Incorporación de las soluciones Update Management, Change Tracking e Inventory

Azure Automation proporciona soluciones para administrar las actualizaciones de seguridad del sistema operativo, el seguimiento de cambios y el inventario de los componentes instalados en los equipos. Hay varias maneras de incorporar máquinas, se puede incorporar la solución desde una máquina virtual, [desde su cuenta de Automation](automation-onboard-solutions-from-vm.md) o mediante un [runbook](automation-onboard-solutions.md). Este artículo trata la incorporación de estas soluciones desde la cuenta de Automation.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure en https://portal.azure.com.

## <a name="enable-solutions"></a>Habilitar soluciones

Vaya a la cuenta de Automation y seleccione **Inventory** o **Change Tracking** en **ADMINISTRACIÓN DE CONFIGURACIÓN**.

Para habilitar la solución, elija el área de trabajo de Log Analytics y la cuenta de Automation y haga clic en **Habilitar**. La solución tarda hasta 15 minutos en habilitarse.

![Incorporación de la solución Inventory](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

La solución Change Tracking e Inventario permite realizar un [control de cambios](automation-vm-change-tracking.md) e [inventario](automation-vm-inventory.md) en sus máquinas virtuales. En este paso, se habilita la solución en una máquina virtual.

Cuando se complete la notificación de que se ha incorporado la solución de control de cambios e inventario, haga clic en **Administración de actualizaciones** en **Administración de configuración**.

La solución Administración de actualizaciones permite administrar las actualizaciones y las revisiones para las máquinas virtuales Windows de Azure. Puede evaluar rápidamente el estado de las actualizaciones disponibles, programar la instalación de las actualizaciones necesarias y revisar los resultados de la implementación para comprobar si las actualizaciones se aplicaron correctamente en la máquina virtual. Esta acción activa la solución en la máquina virtual.

Seleccione **Update management** en **UPDATE MANAGEMENT**. El área de trabajo de Log Analytics seleccionada es la misma área de trabajo que se usa en el paso anterior. Haga clic en **Habilitar** para incorporar la solución Administración de actualizaciones. La solución tarda hasta 15 minutos en habilitarse.

![Incorporación de la solución de actualización](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Configuración de ámbito

Cada solución utiliza una configuración de ámbito en el área de trabajo para definir los equipos de destino que obtendrán la solución. La configuración de ámbito es un grupo de una o más búsquedas guardadas que se utiliza para limitar el ámbito de la solución a equipos específicos. Para tener acceso a las configuraciones de ámbito, en la cuenta de Automation, en **Recursos relacionados**, seleccione **Área de trabajo**. A continuación, en el área de trabajo, en **Orígenes de datos del área de trabajo**, seleccione **Configuraciones de ámbito**.

Las dos configuraciones de ámbito creadas de forma predeterminada son **MicrosoftDefaultScopeConfig-ChangeTracking** y **MicrosoftDefaultScopeConfig-Updates**.

## <a name="saved-searches"></a>Búsquedas guardadas

Cuando un equipo se agrega a las soluciones Update Management, Change Tracking o Inventory, se agrega a una de las dos búsquedas guardadas del área de trabajo. Estas búsquedas guardadas son consultas que contienen los equipos de destino de estas soluciones.

Vaya a la cuenta de Automation y seleccione **Búsquedas guardadas** en **General**. Las dos búsquedas guardadas utilizadas por estas soluciones se pueden ver en la tabla siguiente:

|NOMBRE     |Categoría  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Actualizaciones        | Updates__MicrosoftDefaultComputerGroup         |

Seleccione cualquier búsqueda guardada para ver la consulta utilizada para rellenar el grupo. La siguiente imagen muestra la consulta y sus resultados:

![Búsquedas guardadas](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-an-azure-machine"></a>Incorporación de una máquina virtual de Azure

Desde la cuenta de Automation, seleccione **Inventory** o **Change Tracking** en **ADMINISTRACIÓN DE CONFIGURACIÓN** o **Update management** en **UPDATE MANAGEMENT**.

Haga clic en **+ Agregar una máquina virtual de Azure** y seleccione una máquina virtual de la lista. En la página **Update Management**, haga clic en **Habilitar**. Esto agrega la máquina virtual actual a la búsqueda guardada de grupos de equipos de la solución.

## <a name="onboard-a-non-azure-machine"></a>Incorporación de una máquina que no es de Azure

Desde la cuenta de Automation, seleccione **Inventory** o **Change Tracking** en **ADMINISTRACIÓN DE CONFIGURACIÓN** o **Update management** en **UPDATE MANAGEMENT**.

Haga clic en **Agregar un equipo que no es de Azure**. Esto abre una nueva ventana del explorador con las instrucciones sobre cómo instalar y configurar Microsoft Monitoring Agent en el equipo para que el equipo pueda empezar a notificar a la solución. Si está incorporando una máquina administrada actualmente por System Center Operations Manager, no es necesario un nuevo agente, se especifica la información del área de trabajo en el agente existente.

## <a name="onboard-machines-in-the-workspace"></a>Incorporación de máquinas en el área de trabajo

Desde la cuenta de Automation, seleccione **Inventory** o **Change Tracking** en **ADMINISTRACIÓN DE CONFIGURACIÓN** o **Update management** en **UPDATE MANAGEMENT**.

Seleccione **Administrar máquinas**. Se abrirá la página **Administrar máquinas**. Esta página permite habilitar la solución en un conjunto seleccionado de máquinas, en todas los máquinas disponibles o habilitar la solución para todas las máquinas actuales y habilitarlo en todas las máquinas futuras.

![Búsquedas guardadas](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="selected-machines"></a>Máquinas seleccionadas

Para habilitar la solución para una o varias máquinas, seleccione **Habilitar en las máquinas seleccionadas** y haga clic en **Agregar** junto a cada máquina que se va a agregar a la solución. Esta tarea agrega los nombres de máquina seleccionados a la consulta de la búsqueda guardada de grupos de equipos de la solución.

### <a name="all-available-machines"></a>Todas las máquinas disponibles

Para habilitar la solución para todas las máquinas disponibles, seleccione **Habilitar en todas las máquinas disponibles**. Esto deshabilita el control para agregar máquinas individualmente. Esta tarea agrega todos los nombres de las máquinas que informan al área de trabajo en la consulta de la búsqueda guardada de grupos de equipos.

### <a name="all-available-and-future-machines"></a>Todas las máquinas disponibles y futuras

Para habilitar la solución para todas las máquinas disponibles y futuras, seleccione **Habilitar en todas las máquinas disponibles y futuras**. Esta opción elimina las configuraciones de ámbito y las búsquedas guardadas del área de trabajo. Se abrirá la solución para todas las máquinas de Azure y las que no son de Azure que notifican al área de trabajo.

## <a name="next-steps"></a>pasos siguientes

Continúe con los tutoriales de las soluciones para aprender a utilizarlas.

* [Tutorial: administración de las actualizaciones de la máquina virtual](automation-tutorial-update-management.md)

* [Tutorial: identificación del software en una máquina virtual](automation-tutorial-installed-software.md)

* [Tutorial: solución de problemas de los cambios en una máquina virtual](automation-tutorial-troubleshoot-changes.md)