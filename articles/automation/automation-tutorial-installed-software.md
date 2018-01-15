---
title: "Detección del software instalado en las máquinas con Azure Automation | Documentos de Microsoft"
description: "Use Inventario para detectar el software instalado en las máquinas de todo el entorno."
services: automation
keywords: "inventario, automatización, cambio, seguimiento"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 12/14/2017
ms.topic: tutorial
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: bdd638d0612a8ddee1a0ddb4fd4579f8da14b887
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Detecte el software que está instalado en sus máquinas, sean de Azure o no

En este tutorial, aprenderá a detectar el software que está instalado en su entorno. Puede recopilar y ver el inventario de software, archivos, demonios de Linux, servicios de Windows y claves del Registro de Windows en los equipos. Realizar un seguimiento de las configuraciones de sus máquinas puede ayudarle a identificar problemas de funcionamiento en el entorno y comprender mejor el estado de las máquinas.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Incorporar una máquina virtual para Change Tracking e Inventario
> * Visualizar el software instalado
> * Buscar software instalado en los registros del inventario

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita:

* Una suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o suscribirse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Una [cuenta de Automation](automation-offering-get-started.md) para contener los runbooks de monitor y de acción y la tarea de monitor.
* Una [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para incorporar.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="enable-change-tracking-and-inventory"></a>Habilitación de Change Tracking e Inventario

En primer lugar, debe habilitar Change Tracking e Inventario para la máquina virtual en este tutorial. Si habilitó previamente otra solución de automatización para una máquina virtual, este paso no es necesario.

1. En el menú de la izquierda, seleccione **Máquinas virtuales** y seleccione una máquina virtual de la lista.
2. En el menú de la izquierda, en la sección **Operaciones**, haga clic en **Inventario**. Se abre la página **Habilitar Change Tracking e Inventario**.

Se realiza la validación para determinar si Inventario está habilitado para esta máquina virtual.
La validación incluye comprobaciones de un área de trabajo de Log Analytics y la cuenta de Automation vinculada, y si la solución está en el área de trabajo.

Un área de trabajo de [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) se usa para recopilar datos que se generan mediante características y servicios, como, por ejemplo, Inventario.
El área de trabajo proporciona una única ubicación para revisar y analizar datos desde varios orígenes.

El proceso de validación también comprueba si la máquina virtual se aprovisiona con Microsoft Monitoring Agent (MMA) y Hybrid Worker.
Este agente se usa para comunicarse con la máquina virtual y obtener información sobre el software instalado.
El proceso de validación también comprueba si la máquina virtual se aprovisiona con Microsoft Monitoring Agent (MMA) y un trabajo de runbook híbrido de Automation.

Si no se cumplen estos requisitos previos, aparece un banner que le ofrece la opción de habilitar la solución.

![Banner de configuración de la incorporación del inventario](./media/automation-tutorial-installed-software/enableinventory.png)

Para habilitar la solución, haga clic en el banner.
Si se detecta que falta alguno de los siguientes requisitos previos después de la validación, estos se agregarán automáticamente:

* Área de trabajo de [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Automation](./automation-offering-get-started.md)
* [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md) está habilitado en la máquina virtual.

Se abre la ventana **Change Tracking e Inventario**. Configure la ubicación, el área de trabajo de Log Analytics y la cuenta de Automation que use y haga clic en **Habilitar**. Si los campos aparecen atenuados, significa que otra solución de automatización está habilitada para la máquina virtual y que deben usarse la misma área de trabajo y cuenta de Automation.

![Ventana de habilitación de la solución Change Tracking](./media/automation-tutorial-installed-software/installed-software-enable.png)

La habilitación de la solución puede tardar hasta 15 minutos. Durante este tiempo, no debería cerrar la ventana del explorador.
Después de habilitar la solución, la información sobre el software instalado y los cambios en la máquina virtual se pasa a Log Analytics.
Los datos pueden tardar entre 30 minutos y 6 horas en estar disponibles para el análisis.

## <a name="view-installed-software"></a>Visualizar el software instalado

Una vez habilitada la solución Change Tracking e Inventario, puede ver los resultados en la página **Inventario**.

Desde dentro de la máquina virtual, seleccione **Inventario** en **OPERACIONES**.

En la página **Inventario**, haga clic en la pestaña **Software**.

En la pestaña **Software**, hay una lista en forma de tabla con el software que se encontró. El software se agrupa por nombre y versión.

En la tabla puede verse cada registro de software con gran detalle. Estos detalles incluyen el nombre del software, la versión, el editor, la última hora de actualización (la hora de actualización más reciente que una máquina virtual notificó al grupo) y las máquinas (el recuento de las máquinas con ese software).

![Inventario de software](./media/automation-tutorial-installed-software/inventory-software.png)

Haga clic en una fila para ver las propiedades del registro de software y los nombres de las máquinas con dicho software.

Para buscar un software específico o un grupo de software, vaya al cuadro de texto directamente encima de la lista de software.
El filtro le permite buscar en función del nombre del software, la versión o el editor.

Por ejemplo, la búsqueda "Contoso" devuelve todo el software con un nombre, una versión o un editor que contenga "Contoso".

## <a name="search-inventory-logs-for-installed-software"></a>Búsqueda de software instalado en los registros del inventario

Inventario genera datos de registro que se envían a Log Analytics. Para buscar los registros mediante la ejecución de consultas, seleccione **Log Analytics** en la parte superior de la ventana **Inventario**.

Los datos de Inventario se almacenan con el tipo **ConfigurationData**.
La siguiente consulta de Log Analytics de ejemplo devuelve los editores que contienen "Microsoft" y el número de registros de software (agrupados por SoftwareName y Computer) para cada editor.

```
ConfigurationData
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
| where ConfigDataType == "Software"
| search Publisher:"Microsoft"
| summarize count() by Publisher
```

Para obtener más información sobre la ejecución y la búsqueda de archivos de registro en Log Analytics, consulte [Azure Log Analytics](https://docs.loganalytics.io/index).

### <a name="single-machine-inventory"></a>Inventario de máquina única

Para ver el inventario de software de una única máquina, puede acceder a Inventario desde la página de recursos de VM de Azure o usar Log Analytics para filtrar hasta encontrar la máquina correspondiente. La siguiente consulta de Log Analytics de ejemplo devuelve la lista de software para una máquina denominada ContosoVM.

```
ConfigurationData
| where ConfigDataType == "Software" 
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
```

## <a name="next-steps"></a>pasos siguientes

En este tutorial aprendió cómo ver el inventario de software, así como a:

> [!div class="checklist"]
> * Incorporar una máquina virtual para Change Tracking e Inventario
> * Visualizar el software instalado
> * Buscar software instalado en los registros del inventario

Continúe hacia la introducción sobre la solución Change Tracking e Inventario para obtener más información.

> [!div class="nextstepaction"]
> [Solución de Inventario y administración de cambios](../log-analytics/log-analytics-change-tracking.md?toc=%2fazure%2fautomation%2ftoc.json)