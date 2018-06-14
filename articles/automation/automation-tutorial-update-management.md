---
title: Administración de actualizaciones y revisiones para las máquinas virtuales Windows de Azure
description: En este artículo se proporciona una introducción sobre cómo utilizar Azure Automation y Update Management para administrar las actualizaciones y revisiones para las máquinas virtuales Windows de Azure.
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 84ec2a5852e6aaeb4b9fe6ef11924209d03fb54b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
ms.locfileid: "34054766"
---
# <a name="manage-windows-updates-with-azure-automation"></a>Administración de las actualizaciones de Windows con Azure Automation

Update Management le permite administrar las actualizaciones y las revisiones para las máquinas virtuales.
En este tutorial aprenderá a evaluar rápidamente el estado de las actualizaciones disponibles, programar la instalación de las actualizaciones necesarias, revisar los resultados de la implementación y crear una alerta para verificar que las actualizaciones se aplican correctamente.

Para obtener información de precios, consulte [Precios de automatización de la administración de actualizaciones](https://azure.microsoft.com/pricing/details/automation/).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Incorporar máquinas virtuales a Update Management
> * Visualización de la evaluación de la actualización
> * Configurar alertas
> * Programación de una implementación de actualizaciones
> * Ver los resultados de una implementación

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, necesita:

* Una suscripción de Azure. Si aún no tiene uno, puede [activar su crédito mensual de Azure para suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o suscribirse a una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Una [cuenta de Automation](automation-offering-get-started.md) para contener los runbooks de monitor y de acción y la tarea de monitor.
* Una [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para incorporar.

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="enable-update-management"></a>Habilitar la administración de actualizaciones

En primer lugar, debe habilitar la administración de actualizaciones en la máquina virtual de este tutorial.

1. En el menú de la izquierda de Azure Portal, seleccione **Máquinas virtuales** y seleccione una máquina virtual de la lista.
2. En la página de la máquina virtual, haga clic en **Administración de actualizaciones** en la sección **Operaciones**. Se abre la página **Habilitar Update Management**.

Se realiza la validación para determinar si la administración de actualizaciones está habilitada para esta máquina virtual. Esta validación incluye comprobaciones de un área de trabajo de Log Analytics y la cuenta de Automation vinculada, y si la solución Update Management está en el área de trabajo.

Un área de trabajo de [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) se usa para recopilar datos que se generan mediante características y servicios, como, por ejemplo, Update Management. El área de trabajo proporciona una única ubicación para revisar y analizar datos desde varios orígenes.

El proceso de validación también comprueba si la máquina virtual se aprovisiona con Microsoft Monitoring Agent (MMA) y un trabajo de runbook híbrido de Automation.
Este agente se usa para comunicarse con Azure Automation y obtener información sobre el estado de actualización. El agente requiere que se abra el puerto 443 para comunicarse con el servicio Azure Automation y para descargar actualizaciones.

Si se detecta que falta alguno de los siguientes requisitos previos durante la incorporación, estos se agregarán automáticamente:

* Área de trabajo de [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)
* [Cuenta de Automation](./automation-offering-get-started.md)
* [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md) está habilitado en la máquina virtual.

Se abre la pantalla **Update Management**. Configure la ubicación, el área de trabajo de Log Analytics y la cuenta de Automation que use y haga clic en **Habilitar**. Si los campos aparecen atenuados, significa que otra solución de automatización está habilitada para la máquina virtual y que deben usarse la misma área de trabajo y cuenta de Automation.

![Ventana para habilitar la solución de Update Management](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

La habilitación de la solución puede tardar unos minutos. Durante este tiempo, no debería cerrar la ventana del explorador.
Después de habilitar la solución, la información sobre las actualizaciones que faltan en la máquina virtual se pasa a Log Analytics.
Los datos pueden tardar entre 30 minutos y 6 horas en estar disponibles para el análisis.

## <a name="view-update-assessment"></a>Ver evaluación de la actualización

Una vez habilitado **Update Management**, se muestra la pantalla **Administración de actualizaciones**.
Si falta alguna actualización, verá una lista de las actualizaciones que faltan en la pestaña **Actualizaciones que faltan**.

Seleccione el **VÍNCULO DE INFORMACIÓN** de la actualización para abrir el artículo de soporte técnico sobre la actualización en una ventana nueva. Aquí encontrará información importante acerca de la actualización.

![Ver el estado de la actualización](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Si hace clic en cualquier otro lugar de la actualización, se abrirá la ventana **Búsqueda de registros** de la actualización seleccionada. La consulta para la búsqueda de registros está predefinida para esa actualización concreta. Puede modificar esta consulta o crear su propia consulta para ver información detallada sobre las actualizaciones implementadas o las que faltan en su entorno.

![Ver el estado de la actualización](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerting"></a>Configurar alertas

En este paso, configurará una alerta para que sepa cuándo las actualizaciones se han implementado correctamente. La alerta que se crea se basa en una consulta de Log Analytics. Se pueden escribir consultas personalizadas para alertas adicionales a fin de abarcar muchos escenarios diferentes. En Azure Portal, vaya a **Monitor** y haga clic en **Crear alerta**. Se abre la página **Crear regla**.

En **1. Definir condición de la alerta** y haga clic en **+  Seleccionar destino**. En **Filter by resource type** (Filtrar por tipo de recurso), seleccione **Log Analytics**. Elija el área de trabajo de Log Analytics y haga clic en **Listo**.

![Creación de una alerta](./media/automation-tutorial-update-management/create-alert.png)

Haga clic en el botón **+Agregar criterios** para abrir la página **Configurar lógica se señal**. Elija **Custom log search** (Búsqueda de registros personalizada) en la tabla. Escriba la consulta siguiente en el cuadro de texto **Consulta de búsqueda**. Esta consulta devuelve los equipos y el nombre de la ejecución de actualización que se completó en el período de tiempo especificado.

```loganalytics
UpdateRunProgress
| where InstallationStatus == 'Succeeded'
| where TimeGenerated > now(-10m)
| summarize by UpdateRunName, Computer
```

Escriba **1** en **Umbral** para la lógica de alerta. Cuando termine, haga clic en **Listo**.

![Configuración de la lógica de señal](./media/automation-tutorial-update-management/signal-logic.png)

En **2. Definir detalles de la alerta**, proporcione a la alerta un nombre descriptivo y una descripción. Establezca la **gravedad** en **Informational(Sev 2)** (Informativa [grav. 2]) dado que la alerta es por una ejecución correcta.

![Configuración de la lógica de señal](./media/automation-tutorial-update-management/define-alert-details.png)

En **3. Definir grupo de acciones**, haga clic en **+Nuevo grupo de acciones**. Un grupo de acciones es un conjunto de acciones que puede usar en varias alertas. Por ejemplo, notificaciones por correo electrónico, runbooks, webhooks y muchas más. Para más información sobre los grupos de acciones, consulte [Creación y administración de grupos de acciones](../monitoring-and-diagnostics/monitoring-action-groups.md).

En el cuadro **Nombre del grupo de acciones**, proporcione un nombre descriptivo y un nombre corto. El nombre corto se utiliza en lugar del nombre completo del grupo de acciones cuando se envían notificaciones mediante este grupo.

En **Acciones**, proporcione a la acción un nombre descriptivo como **Notificaciones por correo electrónico** y, en **TIPO DE ACCIÓN**, seleccione **Email/SMS/Push/Voice** (Correo electrónico/SMS/Inserción/Voz). En **DETALLES**, seleccione **Editar detalles**.

En la página **Email/SMS/Push/Voice** (Correo electrónico/SMS/Inserción/Voz), proporciónele un nombre. Marque la casilla **Correo electrónico** y escriba una dirección de correo electrónico válida para usar.

![Configuración del grupo de acciones de correo electrónico](./media/automation-tutorial-update-management/configure-email-action-group.png)

Haga clic en **Aceptar** en la página **Email/SMS/Push/Voice** (Correo electrónico/SMS/Inserción/Voz)para cerrarla y haga clic en **Aceptar** para cerrar la página **Agregar grupo de acciones**.

Si quiere personalizar el asunto del correo electrónico enviado, haga clic en **Asunto del correo electrónico** en **Personalizar las acciones** en la página **Crear regla**. Cuando haya terminado, haga clic en **Crear regla de alertas**. A continuación, se crea la regla que le avisa cuando la implementación de una actualización tiene éxito y qué máquinas formaron parte de la ejecución de la implementación de actualizaciones.

## <a name="schedule-an-update-deployment"></a>Programación de una implementación de actualizaciones

Ahora que la alerta está configurada, programe una implementación que siga su programación de versiones y ventana de servicios para instalar actualizaciones.
Puede elegir los tipos de actualizaciones que quiere incluir en la implementación.
Por ejemplo, puede incluir actualizaciones de seguridad o críticas y excluir paquetes acumulativos de actualizaciones.

> [!WARNING]
> Cuando las actualizaciones requieren un reinicio, la máquina virtual se reinicia automáticamente.

Programe una nueva implementación de actualizaciones para la máquina virtual. Para ello, vuelva a **Update Management** y seleccione **Programar implementación de actualizaciones** en la parte superior de la pantalla.

En la pantalla **Nueva implementación de actualización**, especifique la siguiente información:

* **Nombre**: proporcione un nombre único para la implementación de actualizaciones.

* **Sistema operativo**: elija el sistema operativo que es el destino de la implementación de actualizaciones.

* **Clasificación de actualizaciones**: seleccione los tipos de software que la implementación de actualizaciones incluyó en la implementación. Para este tutorial, seleccione todos los tipos.

  Los tipos de clasificación son:

   |SO  |Escriba  |
   |---------|---------|
   |Windows     | Actualizaciones críticas</br>Actualizaciones de seguridad</br>Paquetes acumulativos de actualizaciones</br>Feature Packs</br>Service Packs</br>Actualizaciones de definiciones</br>Herramientas</br>Actualizaciones        |
   |Linux     | Actualizaciones críticas y de seguridad</br>Otras actualizaciones       |

   Para ver una descripción de los tipos de clasificación, consulte [Actualización de clasificaciones](automation-update-management.md#update-classifications).

* **Configuración de la programación**: esta opción abre la página Configuración de la programación. La hora de inicio predeterminada es 30 minutos después de la hora actual. Se puede establecer cualquier hora a partir de 10 minutos en el futuro.

   También puede especificar si la implementación se produce una vez o configurar una programación periódica.
   Seleccione **Una vez** en **Periodicidad**. Deje el valor predeterminado a 1 día y haga clic en **Aceptar**. Esta acción configura una programación periódica.

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

Una vez que la implementación de actualizaciones se realiza correctamente, se envía un correo electrónico similar a la siguiente imagen para mostrar el éxito de la implementación.

![Configuración del grupo de acciones de correo electrónico](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Incorporar máquinas virtuales a Update Management
> * Visualización de la evaluación de la actualización
> * Configurar alertas
> * Programación de una implementación de actualizaciones
> * Ver los resultados de una implementación

Continúe hacia la introducción sobre la solución Update Management.

> [!div class="nextstepaction"]
> [Solución Update Management](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)
