---
title: "Administración de las actualizaciones y revisiones para las máquinas virtuales Windows de Azure | Microsoft Docs"
description: "En este artículo se proporciona una introducción sobre cómo utilizar Azure Automation y Update Management para administrar las actualizaciones y revisiones para las máquinas virtuales Windows de Azure."
services: automation
documentationcenter: 
author: zjalexander
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 615618e0e78f97e3f41dc2c0e1ca9a6e4b1b47bf
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="manage-windows-updates-with-azure-automation"></a>Administración de las actualizaciones de Windows con Azure Automation

Update Management le permite administrar las actualizaciones y las revisiones para las máquinas virtuales.
En este tutorial aprenderá a evaluar rápidamente el estado de las actualizaciones disponibles, programar la instalación de las actualizaciones necesarias y revisar los resultados de la implementación para comprobar que las actualizaciones se apliquen correctamente.

Para obtener información de precios, consulte [Precios de automatización de la administración de actualizaciones](https://azure.microsoft.com/pricing/details/automation/).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Incorporar máquinas virtuales a Update Management
> * Visualización de la evaluación de la actualización
> * Programación de una implementación de actualizaciones
> * Ver los resultados de una implementación

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, necesita:

* Una suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o suscribirse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Una [cuenta de Automation](automation-offering-get-started.md) para contener los runbooks de monitor y de acción y la tarea de monitor.
* Una [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para incorporar.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="enable-update-management"></a>Habilitar la administración de actualizaciones

En primer lugar, debe habilitar la administración de actualizaciones para la máquina virtual en este tutorial. Si habilitó previamente otra solución de automatización para una máquina virtual, este paso no es necesario.

1. En el menú de la izquierda, seleccione **Máquinas virtuales** y seleccione una máquina virtual de la lista.
2. En el menú de la izquierda, en la sección **Operaciones**, haga clic en **Update Management**. Se abre la página **Habilitar Update Management**.

Se realiza la validación para determinar si la administración de actualizaciones está habilitada para esta máquina virtual.
La validación incluye comprobaciones de un área de trabajo de Log Analytics y la cuenta de Automation vinculada, y si la solución está en el área de trabajo.

Un área de trabajo de [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) se usa para recopilar datos que se generan mediante características y servicios, como, por ejemplo, Update Management.
El área de trabajo proporciona una única ubicación para revisar y analizar datos desde varios orígenes.
Para llevar a cabo alguna acción adicional en máquinas virtuales que requieran actualizaciones, Azure Automation permite ejecutar runbooks en máquinas virtuales, por ejemplo, para descargar y aplicar actualizaciones.

El proceso de validación también comprueba si la máquina virtual se aprovisiona con Microsoft Monitoring Agent (MMA) y un trabajo de runbook híbrido de Automation.
Este agente se usa para comunicarse con la máquina virtual y obtener información sobre el estado de actualización.

Si no se cumplen estos requisitos previos, aparece un banner que le ofrece la opción de habilitar la solución.

![Banner de configuración de la incorporación de Update Management](./media/automation-tutorial-update-management/manageupdates-onboard-solution-banner.png)

Para habilitar la solución, haga clic en el banner.
Si se detecta que falta alguno de los siguientes requisitos previos después de la validación, estos se agregarán automáticamente:

* Área de trabajo de [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Automation](./automation-offering-get-started.md)
* [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md) está habilitado en la máquina virtual.

Se abre la pantalla **Update Management**. Configure la ubicación, el área de trabajo de Log Analytics y la cuenta de Automation que use y haga clic en **Habilitar**. Si los campos aparecen atenuados, significa que otra solución de automatización está habilitada para la máquina virtual y que deben usarse la misma área de trabajo y cuenta de Automation.

![Ventana para habilitar la solución de Update Management](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

La habilitación de la solución puede tardar hasta 15 minutos. Durante este tiempo, no debería cerrar la ventana del explorador.
Después de habilitar la solución, la información sobre las actualizaciones que faltan en la máquina virtual se pasa a Log Analytics.
Los datos pueden tardar entre 30 minutos y 6 horas en estar disponibles para el análisis.

## <a name="view-update-assessment"></a>Ver evaluación de la actualización

Una vez habilitado **Update Management**, se muestra la pantalla **Administración de actualizaciones**.
Si falta alguna actualización, verá una lista de las actualizaciones que faltan en la pestaña **Actualizaciones que faltan**.

Seleccione el **VÍNCULO DE INFORMACIÓN** de la actualización para abrir el artículo de soporte técnico sobre la actualización en una ventana nueva. Aquí encontrará información importante acerca de la actualización.

![Ver el estado de la actualización](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Si hace clic en cualquier otro lugar de la actualización, se abrirá la ventana **Búsqueda de registros** de la actualización seleccionada. La consulta para la búsqueda de registros está predefinida para esa actualización concreta. Puede modificar esta consulta o crear su propia consulta para ver información detallada sobre las actualizaciones implementadas o las que faltan en su entorno.

![Ver el estado de la actualización](./media/automation-tutorial-update-management/logsearch.png)

## <a name="schedule-an-update-deployment"></a>Programación de una implementación de actualizaciones

Ahora sabe que faltan algunas actualizaciones en su máquina virtual. Para instalar actualizaciones, programe una implementación que se ajuste a su ventana de programación y servicio de versiones.
Puede elegir los tipos de actualizaciones que quiere incluir en la implementación.
Por ejemplo, puede incluir actualizaciones de seguridad o críticas y excluir paquetes acumulativos de actualizaciones.

> [!WARNING]
> Cuando las actualizaciones requieren un reinicio, la máquina virtual se reinicia automáticamente.

Programe una nueva implementación de actualizaciones para la máquina virtual. Para ello, vuelva a **Update Management** y seleccione **Programar implementación de actualizaciones** en la parte superior de la pantalla.

En la pantalla **Nueva implementación de actualización**, especifique la siguiente información:

* **Nombre**: proporcione un nombre único para la implementación de actualizaciones.
* **Clasificación de actualizaciones**: seleccione los tipos de software que la implementación de actualizaciones incluyó en la implementación. Para este tutorial, seleccione todos los tipos.

  Los tipos de clasificación son:

  * Actualizaciones críticas
  * Actualizaciones de seguridad
  * Paquetes acumulativos de actualizaciones
  * Feature Packs
  * Service Packs
  * Actualizaciones de definiciones
  * Herramientas
  * Actualizaciones

* **Configuración de la programación**: establezca el tiempo a 5 minutos en el futuro. También puede aceptar el valor predeterminado, que es de 30 minutos después de la hora actual.
También puede especificar si la implementación se produce una vez o configurar una programación periódica.
Seleccione **Periódica** en **Periodicidad**. Deje el valor predeterminado a 1 día y haga clic en **Aceptar**. Esta acción configura una programación periódica.

* **Ventana de mantenimiento (minutos)**: deje este campo con el valor predeterminado. Puede especificar el período de tiempo en el que desea que se produzca la implementación de actualizaciones. Esta configuración ayuda a garantizar que los cambios se realizan en las ventanas de servicio definidas.

![Pantalla de configuración de la programación de actualizaciones](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Después de completar la configuración de la programación, haga clic en el botón **Crear**. Volverá al panel de estado. Seleccione **Implementaciones de actualizaciones programadas** para mostrar la programación de implementación que creó.

## <a name="view-results-of-an-update-deployment"></a>Visualización de los resultados de una implementación de actualizaciones

Después de que se inicie la implementación programada, puede ver su estado en la pestaña **Implementaciones de actualizaciones** en la pantalla **Update Management**.
Si se está ejecutando actualmente, su estado se muestra como **En curso**.
Cuando se completa, si se realiza correctamente, cambia a **Correcto**.
Si se producen errores con una o varias actualizaciones en la implementación, el estado es **Error parcial**.
Haga clic en la implementación de actualizaciones completada para ver el panel correspondiente.

![Panel de estado de Implementación de actualizaciones de la implementación específica](./media/automation-tutorial-update-management/manageupdates-view-results.png)

En el icono **Resultados de actualización** encontrará un resumen del número total de actualizaciones y los resultados de la implementación en la máquina virtual.
En la tabla de la derecha se muestra un análisis detallado de cada actualización y los resultados de la instalación.
En la lista siguiente se muestran los valores disponibles:

* **No intentado**: la actualización no se instaló porque no había tiempo disponible suficiente de acuerdo con la duración definida para la ventana de mantenimiento.
* **Correcto**: la actualización se realizó correctamente.
* **Error**: se produjo un error en la actualización.

Haga clic en **Todos los registros** para ver todas las entradas de registro que creó la implementación.

Haga clic en el icono **Salida** para ver el flujo de trabajo del runbook responsable de administrar la implementación de actualizaciones en la máquina virtual de destino.

Haga clic en **Errores** para ver información detallada sobre los errores de la implementación.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Incorporar máquinas virtuales a Update Management
> * Visualización de la evaluación de la actualización
> * Programación de una implementación de actualizaciones
> * Ver los resultados de una implementación

Continúe hacia la introducción sobre la solución Update Management.

> [!div class="nextstepaction"]
> [Solución Update Management](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)
