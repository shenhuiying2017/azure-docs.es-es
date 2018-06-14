---
title: Detección del software instalado en las máquinas con Azure Automation | Documentos de Microsoft
description: Use Inventario para detectar el software instalado en las máquinas de todo el entorno.
services: automation
keywords: inventario, automatización, cambio, seguimiento
author: jennyhunter-msft
ms.author: jehunte
ms.date: 04/11/2018
ms.topic: tutorial
ms.service: automation
ms.component: change-inventory-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: cb876a8d8019f5a2a7232c3093c6f64a7b2730e1
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
ms.locfileid: "34053817"
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Detecte el software que está instalado en sus máquinas, sean de Azure o no

En este tutorial, aprenderá a detectar el software que está instalado en su entorno. Puede recopilar y ver el inventario de software, archivos, demonios de Linux, servicios de Windows y claves del Registro de Windows en los equipos. Realizar un seguimiento de las configuraciones de sus máquinas puede ayudarle a identificar problemas de funcionamiento en el entorno y comprender mejor el estado de las máquinas.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Habilitar la solución
> * Incorporar una máquina virtual de Azure
> * Incorporar una máquina que no es de Azure
> * Visualizar el software instalado
> * Buscar software instalado en los registros del inventario

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, necesita:

* Una suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o suscribirse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Una [cuenta de Automation](automation-offering-get-started.md) para contener los runbooks de monitor y de acción y la tarea de monitor.
* Una [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para incorporar.

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en http://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Habilitación de Change Tracking e Inventario

En primer lugar, debe habilitar Change Tracking e Inventario para este tutorial. Si habilitó previamente la solución **Change Tracking**, este paso no es necesario.

Vaya a la cuenta de Automation y seleccione **Inventario** en **ADMINISTRACIÓN DE CONFIGURACIÓN**.

Para habilitar la solución, elija el área de trabajo de Log Analytics y la cuenta de Automation y haga clic en **Habilitar**. La solución tarda hasta 15 minutos en habilitarse.

![Banner de configuración de la incorporación del inventario](./media/automation-tutorial-installed-software/enableinventory.png)

Para habilitar la solución, configure la ubicación, el área de trabajo de Log Analytics y la cuenta de Automation que use y haga clic en **Habilitar**. Si los campos aparecen atenuados, significa que otra solución de automatización está habilitada para la máquina virtual y que deben usarse la misma área de trabajo y cuenta de Automation.

Un área de trabajo de [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) se usa para recopilar datos que se generan mediante características y servicios, como, por ejemplo, Inventario.
El área de trabajo proporciona una única ubicación para revisar y analizar datos desde varios orígenes.

La habilitación de la solución puede tardar hasta 15 minutos. Durante este tiempo, no debería cerrar la ventana del explorador.
Después de habilitar la solución, la información sobre el software instalado y los cambios en la máquina virtual se pasa a Log Analytics.
Los datos pueden tardar entre 30 minutos y 6 horas en estar disponibles para el análisis.

## <a name="onboard-a-vm"></a>Incorporación de una máquina virtual

En la cuenta de Automation, vaya a **Inventario** en **ADMINISTRACIÓN DE CONFIGURACIÓN**.

Seleccione **+ Agregar máquina virtual de Azure**, se abrirá la página **Máquinas virtuales** que le permite seleccionar una máquina virtual existente en la lista. Seleccione la máquina virtual que desea incorporar. En la página que se abre, haga clic en **Habilitar** para habilitar la solución en la máquina virtual. El agente de administración de Microsoft se implementa en la máquina virtual y configura el agente para comunicarse con el área de trabajo de Log Analytics que configuró al habilitar la solución. La incorporación puede tardar unos minutos en finalizar. En este momento, puede seleccionar una nueva máquina virtual de la lista e incorporar otra máquina virtual.

## <a name="onboard-a-non-azure-machine"></a>Incorporación de una máquina que no es de Azure

Para agregar máquinas que no son de Azure, instale el agente para [Windows](../log-analytics/log-analytics-agent-windows.md) o [Linux](automation-linux-hrw-install.md) según el sistema operativo. Una vez instalado el agente, vaya a la cuenta de Automation y seleccione **Inventario** en **ADMINISTRACIÓN DE CONFIGURACIÓN**. Al hacer clic en **Administrar máquinas**, verá una lista de las máquinas que informan al área de trabajo de Log Analytics y que no tienen la solución habilitada. Seleccione la opción adecuada para su entorno.

* **Habilitar en todas las máquinas disponibles**: esta opción habilita la solución en todas las máquinas que informan al área de trabajo de Log Analytics en este momento.
* **Habilitar en todas las máquinas disponibles y futuras**: esta opción habilita la solución en todas las máquinas que informan al área de trabajo de Log Analytics y, posteriormente, en todas las máquinas futuras que se agreguen al área de trabajo.
* **Habilitar en las máquinas seleccionadas**: esta opción habilita la solución solo en las máquinas que ha seleccionado.

![Administrar máquinas](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Visualizar el software instalado

Una vez habilitada la solución Change Tracking e Inventario, puede ver los resultados en la página **Inventario**.

Desde la cuenta de Automation, seleccione **Inventario** en **ADMINISTRACIÓN DE CONFIGURACIÓN**.

En la página **Inventario**, haga clic en la pestaña **Software**.

En la pestaña **Software**, hay una tabla que enumera el software que se ha encontrado. El software se agrupa por nombre y versión.

En la tabla puede verse cada registro de software con gran detalle. Estos detalles incluyen el nombre del software, la versión, el editor, la última hora de actualización (la hora de actualización más reciente que una máquina virtual notificó al grupo) y las máquinas (el recuento de las máquinas con ese software).

![Inventario de software](./media/automation-tutorial-installed-software/inventory-software.png)

Haga clic en una fila para ver las propiedades del registro de software y los nombres de las máquinas con dicho software.

Para buscar un software específico o un grupo de software, vaya al cuadro de texto directamente encima de la lista de software.
El filtro le permite buscar en función del nombre del software, la versión o el editor.

Por ejemplo, la búsqueda "Contoso" devuelve todo el software con un nombre, una versión o un editor que contenga "Contoso".

## <a name="search-inventory-logs-for-installed-software"></a>Búsqueda de software instalado en los registros del inventario

Inventario genera datos de registro que se envían a Log Analytics. Para buscar los registros mediante la ejecución de consultas, seleccione **Log Analytics** en la parte superior de la ventana **Inventario**.

Los datos de Inventario se almacenan con el tipo **ConfigurationData**.
La consulta de Log Analytics del ejemplo siguiente devuelve los resultados de inventario en los que el publicador es igual a "Microsoft Corporation".

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Para obtener más información sobre la ejecución y la búsqueda de archivos de registro en Log Analytics, consulte [Azure Log Analytics](https://docs.loganalytics.io/index).

### <a name="single-machine-inventory"></a>Inventario de máquina única

Para ver el inventario de software de una única máquina, puede acceder a Inventario desde la página de recursos de VM de Azure o usar Log Analytics para filtrar hasta encontrar la máquina correspondiente.
La siguiente consulta de Log Analytics de ejemplo devuelve la lista de software para una máquina denominada ContosoVM.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió cómo ver el inventario de software, así como a:

> [!div class="checklist"]
> * Habilitar la solución
> * Incorporar una máquina virtual de Azure
> * Incorporar una máquina que no es de Azure
> * Visualizar el software instalado
> * Buscar software instalado en los registros del inventario

Continúe hacia la introducción sobre la solución Change Tracking e Inventario para obtener más información.

> [!div class="nextstepaction"]
> [Solución de Inventario y administración de cambios](automation-change-tracking.md)