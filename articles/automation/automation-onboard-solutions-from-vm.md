---
title: "Obtenga información sobre la incorporación de las soluciones Update Management, Change Tracking e Inventario desde una máquina virtual de Azure"
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
ms.openlocfilehash: a850189406b394e7935763206f9e3a191b415170
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Incorporación de las soluciones Update Management, Change Tracking e Inventory desde una máquina virtual de Azure

Azure Automation proporciona soluciones para administrar las actualizaciones de seguridad del sistema operativo, el seguimiento de cambios y el inventario de los componentes instalados en los equipos. Hay varias maneras de incorporar máquinas, se puede incorporar la solución desde una máquina virtual, [desde su cuenta de Automation](automation-onboard-solutions-from-automation-account.md) o mediante un [runbook](automation-onboard-solutions.md). Este artículo trata la incorporación de estas soluciones desde una máquina virtual de Azure.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure en https://portal.azure.com.

## <a name="enable-the-solutions"></a>Habilitación de las soluciones

Vaya a una máquina virtual existente y seleccione **Update management**, **Inventory** o **Change tracking** en **Operaciones**.

Para habilitar la solución, elija el área de trabajo de Log Analytics y la cuenta de Automation y haga clic en **Habilitar**. La solución tarda hasta 15 minutos en habilitarse.

![Incorporación de la solución de actualización](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Vaya a las otras soluciones y haga clic en **Habilitar**; los cuadros de lista desplegable Log Analytics y Cuenta de Automation se deshabilitan, ya que utilizan las mismas área de trabajo y cuenta de Automation que la solución habilitada anteriormente.

![Incorporación de la solución de actualización](media/automation-onboard-solutions-from-vm/onboard-solutions2.png)

> [!NOTE]
> **Change tracking** e **Inventory** utilizan la misma solución; cuando una está habilitada, la otra también lo está.

## <a name="scope-configuration"></a>Configuración de ámbito

Cada solución utiliza una configuración de ámbito en el área de trabajo para definir los equipos de destino que obtendrán la solución. La configuración de ámbito es un grupo de una o más búsquedas guardadas que se utiliza para limitar el ámbito de la solución a equipos específicos. Para tener acceso a las configuraciones de ámbito, en la cuenta de Automation, en **RECURSOS RELACIONADOS**, seleccione **Área de trabajo** y, a continuación, en el área de trabajo, en **Orígenes de datos del área de trabajo**, seleccione **Configuraciones de ámbito**.

Las dos configuraciones de ámbito creadas de forma predeterminada son **MicrosoftDefaultScopeConfig-ChangeTracking** y **MicrosoftDefaultScopeConfig-Updates**.

Haga clic en los puntos suspensivos (...) en cualquiera de las configuraciones y seleccione **Editar**. En la página **Configuración del ámbito de edición**, seleccione **Seleccionar grupos de equipos** para abrir la página **Grupos de equipos**. Esta página muestra las búsquedas guardadas que se utilizan para crear la configuración de ámbito.

## <a name="saved-searches"></a>Búsquedas guardadas

Cuando un equipo se agrega a las soluciones Update Management, Change Tracking o Inventory, se agrega a una de las dos búsquedas guardadas del área de trabajo. Estas búsquedas guardadas son consultas que contienen los equipos de destino de estas soluciones.

Vaya al área de trabajo y seleccione **Búsquedas guardadas** en **General**. Las dos búsquedas guardadas utilizadas por estas soluciones se pueden ver en la tabla siguiente:

|NOMBRE     |Categoría  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Actualizaciones        | Updates__MicrosoftDefaultComputerGroup         |

Seleccione cualquier búsqueda guardada para ver la consulta utilizada para rellenar el grupo. La siguiente imagen muestra la consulta y sus resultados.

![Búsquedas guardadas](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>pasos siguientes

Continúe con los tutoriales de las soluciones para aprender a utilizarlas.

* [Tutorial: administración de las actualizaciones de la máquina virtual](automation-tutorial-update-management.md)

* [Tutorial: identificación del software en una máquina virtual](automation-tutorial-installed-software.md)

* [Tutorial: solución de problemas de los cambios en una máquina virtual](automation-tutorial-troubleshoot-changes.md)
