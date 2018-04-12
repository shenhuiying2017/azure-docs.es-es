---
title: Supervisión y actualización de Azure y máquinas virtuales Windows | Microsoft Docs
description: 'Tutorial: Supervisar y actualizar una máquina virtual Windows con Azure PowerShell'
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9f8f8cb7fd267e25c83ecceb98b5faa8848fb126
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-and-update-a-windows-virtual-machine-with-azure-powershell"></a>Tutorial: Supervisar y actualizar una máquina virtual Windows con Azure PowerShell

La supervisión de Azure usa agentes para recopilar datos de arranque y de rendimiento de máquinas virtuales de Azure, almacenar estos datos en Azure Storage y permitir el acceso a ellos a través del portal, el módulo de Azure PowerShell y la CLI de Azure. La administración de actualizaciones permite administrar las actualizaciones y las revisiones para las máquinas virtuales Windows de Azure.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Habilitar los diagnósticos de arranque en una máquina virtual
> * Ver los diagnósticos de arranque
> * Ver las métricas del host de la máquina virtual
> * Instalar la extensión de Diagnostics
> * Ver las métricas de la máquina virtual
> * Crear una alerta
> * Administrar actualizaciones de Windows
> * Supervisión de cambios y del inventario
> * Configurar la supervisión avanzada

Para realizar este tutorial es necesaria la versión 3.6 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps).

Para completar el ejemplo de este tutorial, debe tener una máquina virtual. Si es necesario, este [script de ejemplo](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) puede crear una. Al trabajar en el tutorial, reemplace el grupo de recursos, el nombre de la máquina virtual y la ubicación cuando proceda.

## <a name="view-boot-diagnostics"></a>Ver los diagnósticos de arranque

Cuando las máquinas virtuales Windows arrancan, el agente de diagnóstico de arranque captura una salida de pantalla que se puede usar para solucionar problemas. Esta funcionalidad está habilitada de forma predeterminada. Las capturas de pantalla se almacenan en una cuenta de Azure Storage, que también se crea de forma predeterminada.

Puede obtener los datos de diagnóstico de arranque con el comando [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata). En el ejemplo siguiente, el diagnóstico de arranque se descarga en la raíz de la unidad *c:\*.

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Ver las métricas del host

Una máquina virtual Windows tiene una máquina virtual host dedicada en Azure con la que interactúa. Las métricas del host se recopilan automáticamente y se pueden ver en Azure Portal.

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroup** y, después, seleccione **myVM** en la lista de recursos.
2. Haga clic en **Métricas** en la hoja de la máquina virtual y seleccione cualquiera de las métricas del host en **Métricas disponibles** para ver el funcionamiento de la máquina virtual host.

    ![Visualización de las métricas del host](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Instalar la extensión de Diagnostics

Hay disponibles métricas básicas del host, pero para ver métricas más pormenorizadas específicas de la máquina virtual, es preciso instalar la extensión de Azure Diagnostics en la máquina virtual. La extensión de Azure Diagnostics permite recuperar más datos de supervisión y diagnóstico de la máquina virtual. Puede ver estas métricas de rendimiento y crear alertas basadas en el funcionamiento de la máquina virtual. La extensión de diagnósticos se instala a través de Azure Portal como se indica a continuación:

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroup** y, después, seleccione **myVM** en la lista de recursos.
2. Haga clic en **Configuración de diagnóstico**. La lista muestra que los *diagnósticos de arranque* ya están habilitados desde la sección anterior. Haga clic en la casilla *Métricas básicas*.
3. Haga clic en el botón **Habilitar supervisión a nivel de invitado**.

    ![Ver métricas de diagnósticos](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Visualización de las métricas de la máquina virtual

Las métricas de la máquina virtual se pueden ver de la misma manera que las de la máquina virtual host:

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroup** y, después, seleccione **myVM** en la lista de recursos.
2. Para ver el rendimiento de la máquina virtual, haga clic en **Métricas** en la hoja de la máquina virtual y seleccione cualquiera de las métricas de diagnóstico en **Métricas disponibles**.

    ![Visualización de las métricas de la máquina virtual](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Creación de alertas

Puede crear alertas basadas en métricas de rendimiento concretas. Se pueden usar alertas para enviar una notificación, por ejemplo, cuando el uso medio de la CPU supera un umbral concreto o cuando el espacio libre en disco disponible cae por debajo de una cantidad determinada. Las alertas se muestran en Azure Portal o se pueden enviar por correo electrónico. También puede desencadenar runbooks de Azure Automation o Azure Logic Apps en respuesta a las alertas que se generan.

En el siguiente ejemplo se crea una alerta para el uso medio de la CPU.

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroup** y, después, seleccione **myVM** en la lista de recursos.
2. Haga clic en **Reglas de alertas** en la hoja de la máquina virtual y, después, en **Agregar alerta de métrica** en la parte superior de la hoja de alertas.
3. Especifique un **nombre** para la alerta, como *myAlertRule*.
4. Para desencadenar una alerta cuando el porcentaje de la CPU supera 1,0 durante cinco minutos, deje el resto de valores predeterminados seleccionados.
5. Opcionalmente, active la casilla *Enviar correo electrónico a propietarios, colaboradores y lectores* para enviar una notificación por correo electrónico. La acción predeterminada es presentar una notificación en el portal.
6. Haga clic en el botón **Aceptar**.

## <a name="manage-windows-updates"></a>Administrar actualizaciones de Windows

La administración de actualizaciones permite administrar las actualizaciones y las revisiones para las máquinas virtuales Windows de Azure.
Directamente desde la máquina virtual, puede evaluar rápidamente el estado de las actualizaciones disponibles, programar la instalación de las actualizaciones necesarias y revisar los resultados de la implementación para comprobar si las actualizaciones se aplicaron correctamente en la máquina virtual.

Para obtener información de precios, consulte [Precios de automatización de la administración de actualizaciones](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Habilitar la administración de actualizaciones

Habilite la administración de actualizaciones para la máquina virtual:

1. En el lado izquierdo de la pantalla, seleccione **Máquinas virtuales**.
2. En la lista, seleccione una máquina virtual.
3. En la pantalla de la máquina virtual, en la sección **Operaciones**, haga clic en **Administración de actualizaciones**. Se abre la pantalla **Habilitar la administración de actualizaciones**.

Se realiza la validación para determinar si la administración de actualizaciones está habilitada para esta máquina virtual.
La validación incluye comprobaciones de un área de trabajo de Log Analytics y la cuenta de Automation vinculada, y si la solución está en el área de trabajo.

Un área de trabajo de [Log Analytics](../../log-analytics/log-analytics-overview.md) se usa para recopilar datos que se generan mediante características y servicios, como, por ejemplo, Update Management.
El área de trabajo proporciona una única ubicación para revisar y analizar datos desde varios orígenes.
Para llevar a cabo alguna acción adicional en máquinas virtuales que requieran actualizaciones, Azure Automation permite ejecutar runbooks en máquinas virtuales, por ejemplo, para descargar y aplicar actualizaciones.

El proceso de validación también comprueba si la máquina virtual se aprovisiona con Microsoft Monitoring Agent (MMA) y un trabajo de runbook híbrido de Automation.
Este agente se usa para comunicarse con la máquina virtual y obtener información sobre el estado de actualización.

Para habilitar la solución, elija el área de trabajo de Log Analytics y la cuenta de Automation y haga clic en **Habilitar**. La solución tarda hasta 15 minutos en habilitarse.

Si se detecta que falta alguno de los siguientes requisitos previos durante la incorporación, estos se agregarán automáticamente:

* Área de trabajo de [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automation](../../automation/automation-offering-get-started.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) está habilitado en la máquina virtual.

Se abre la pantalla **Update Management**. Configure la ubicación, el área de trabajo de Log Analytics y la cuenta de Automation que use y haga clic en **Habilitar**. Si los campos aparecen atenuados, significa que otra solución de automatización está habilitada para la máquina virtual y que deben usarse la misma área de trabajo y cuenta de Automation.

![Habilitar la solución Update Management](./media/tutorial-monitoring/manageupdates-update-enable.png)

La habilitación de la solución puede tardar hasta 15 minutos. Durante este tiempo, no debería cerrar la ventana del explorador. Después de habilitar la solución, la información sobre las actualizaciones que faltan en la máquina virtual se pasa a Log Analytics. Los datos pueden tardar entre 30 minutos y 6 horas en estar disponibles para el análisis.

### <a name="view-update-assessment"></a>Ver evaluación de la actualización

Una vez habilitado **Update Management**, se muestra la pantalla **Administración de actualizaciones**. Después de completar la evaluación de las actualizaciones, verá una lista de las actualizaciones que faltan en la pestaña **Actualizaciones que faltan**.

 ![Ver el estado de la actualización](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Programación de una implementación de actualizaciones

Para instalar actualizaciones, programe una implementación que se ajuste a su ventana de programación y servicio de versiones. Puede elegir los tipos de actualizaciones que quiere incluir en la implementación. Por ejemplo, puede incluir actualizaciones de seguridad o críticas y excluir paquetes acumulativos de actualizaciones.

Programe una nueva implementación de actualizaciones para la máquina virtual. Para ello, haga clic en **Programar implementación de actualizaciones** en la parte superior de la pantalla **Administración de actualizaciones**. En la pantalla **Nueva implementación de actualización**, especifique la siguiente información:

* **Nombre**: proporcione un nombre único para identificar la implementación de actualizaciones.
* **Clasificación de actualizaciones**: seleccione los tipos de software que la implementación de actualizaciones incluyó en la implementación. Los tipos de clasificación son:
  * Actualizaciones críticas
  * Actualizaciones de seguridad
  * Paquetes acumulativos de actualizaciones
  * Feature Packs
  * Service Packs
  * Actualizaciones de definiciones
  * Herramientas
  * Actualizaciones

* **Configuración de la programación**: puede aceptar la fecha y hora predeterminadas, es decir, 30 minutos después de la hora actual, o bien especificar una hora distinta.
  También puede especificar si la implementación se produce una vez o configurar una programación periódica. Haga clic en la opción Periódica en Periodicidad para configurar una programación periódica.

  ![Pantalla de configuración de la programación de actualizaciones](./media/tutorial-monitoring/manageupdates-schedule-win.png)

* **Ventana de mantenimiento (minutos)**: especifique el período de tiempo en el que desea que se produzca la implementación de actualizaciones.  Esto ayuda a garantizar que los cambios se realizan en las ventanas de servicio definidas.

Después de completar la configuración de la programación, haga clic en el botón **Crear** para volver al panel de estado.
Tenga en cuenta que la tabla **Programada** muestra la programación de implementación que ha creado.

> [!WARNING]
> Para las actualizaciones que requieren un reinicio, la máquina virtual se reinicia automáticamente.

### <a name="view-results-of-an-update-deployment"></a>Visualización de los resultados de una implementación de actualizaciones

Después de que se inicie la implementación programada, puede ver su estado en la pestaña **Implementaciones de actualizaciones** en la pantalla **Update Management**.
Si se está ejecutando actualmente, su estado se muestra como **En curso**. Cuando se completa, si se realiza correctamente, cambia a **Correcto**.
Si se produce un error con una o varias actualizaciones en la implementación, el estado es **Error parcial**.
Haga clic en la implementación de actualizaciones completada para ver el panel correspondiente.

![Panel de estado de Implementación de actualizaciones de la implementación específica](./media/tutorial-monitoring/manageupdates-view-results.png)

En el icono **Resultados de actualización** encontrará un resumen del número total de actualizaciones y los resultados de la implementación en la máquina virtual.
En la tabla de la derecha encontrará un análisis detallado de cada actualización y los resultados de la instalación, que podrían ser uno de los valores siguientes:

* **No intentado**: la actualización no se instaló porque no había tiempo disponible suficiente de acuerdo con la duración definida para la ventana de mantenimiento.
* **Correcto**: la actualización se realizó correctamente.
* **Error**: se produjo un error en la actualización.

Haga clic en **Todos los registros** para ver todas las entradas de registro que creó la implementación.

Haga clic en el icono **Salida** para ver el flujo de trabajo del runbook responsable de administrar la implementación de actualizaciones en la máquina virtual de destino.

Haga clic en **Errores** para ver información detallada sobre los errores de la implementación.

## <a name="monitor-changes-and-inventory"></a>Supervisión de cambios y del inventario

Puede recopilar y ver el inventario de software, archivos, demonios de Linux, servicios de Windows y claves del Registro de Windows en los equipos. Realizar un seguimiento de las configuraciones de sus máquinas puede ayudarle a identificar problemas de funcionamiento en el entorno y comprender mejor el estado de las máquinas.

### <a name="enable-change-and-inventory-management"></a>Habilitación de la administración de cambios e inventario

Habilite la administración de cambios e inventario para la máquina virtual:

1. En el lado izquierdo de la pantalla, seleccione **Máquinas virtuales**.
2. En la lista, seleccione una máquina virtual.
3. En la pantalla de la máquina virtual, en la sección **Operaciones**, haga clic en **Inventario** o **Seguimiento de cambios**. Se abre la ventana **Enable Change Tracking and Inventory** (Habilitar seguimiento de cambios e inventario).

Configure la ubicación, el área de trabajo de Log Analytics y la cuenta de Automation que use y haga clic en **Habilitar**. Si los campos aparecen atenuados, significa que otra solución de automatización está habilitada para la máquina virtual y que deben usarse la misma área de trabajo y cuenta de Automation. Aunque las soluciones son independientes en el menú, tienen la misma solución. Al habilitar una, se habilitan ambas para la máquina virtual.

![Habilitación del seguimiento de cambios e inventario](./media/tutorial-monitoring/manage-inventory-enable.png)

Una vez que se ha habilitado la solución, puede tardar algún tiempo mientras se recopila el inventario en la máquina virtual hasta que aparezcan los datos.

### <a name="track-changes"></a>Control de cambios

En la máquina virtual, seleccione **Change Tracking** en **OPERACIONES**. Haga clic en **Editar configuración**, se muestra la página **Change Tracking**. Seleccione el tipo de configuración a la que desea realizar un seguimiento y, a continuación, haga clic en **+ Agregar** para configurar las opciones. Las opciones disponibles para Windows son:

* Registro de Windows
* Archivos de Windows

Para obtener información detallada Change Tracking, consulte [Solucionar de los problemas de los cambios en una máquina virtual](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Visualización del inventario

EN la máquina virtual, seleccione **Inventario** en **OPERACIONES**. En la pestaña **Software**, hay una lista en forma de tabla con el software que se encontró. En la tabla puede verse cada registro de software con gran detalle. Estos detalles incluyen el nombre del software, la versión, el editor, la última hora de actualización.

![Visualización del inventario](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Supervisión de los registros de actividad y cambios

Desde la página **Change Tracking** en la máquina virtual, seleccione **Administrar conexión de registro de actividad**. Esta tarea abrirá la página **Registro de actividad de Azure**. Seleccione **Conectar** para conectar Change Tracking al registro de actividad de Azure en la máquina virtual.

Con esta opción habilitada, navegue hasta la página **Información general** en la máquina virtual y seleccione **Detener** para detenerla. Cuando se le solicite, seleccione **Sí** para detener la máquina virtual. Cuando esté desasignada, seleccione **Iniciar** para reiniciar la máquina virtual.

Si se detiene y se inicia una máquina virtual, se registra un evento en el registro de actividad. Vuelva a la página **Change Tracking**. Seleccione la pestaña **Eventos** en la parte inferior de la página. Después de un tiempo, los eventos se mostrarán en el gráfico y la tabla. Cada evento puede seleccionarse para ver información detallada sobre él.

![Visualización de cambios en el registro de actividad](./media/tutorial-monitoring/manage-activitylog-view-results.png)

El gráfico muestra los cambios que se han producido con el tiempo. Después de agregar una conexión del registro de actividad, el gráfico de líneas en la parte superior muestra los eventos del registro de actividad de Azure. Cada fila de los gráficos de barras representa un tipo de cambio diferente del cual se puede realizar un seguimiento. Estos tipos son demonios de Linux, archivos, claves del Registro de Windows, software y servicios de Windows. En la pestaña de cambios se muestran los detalles de los cambios mostrados en la visualización en orden descendente según la hora en la cual se produjo el cambio (los más recientes se muestran primero).

## <a name="advanced-monitoring"></a>Supervisión avanzada

Puede realizar una supervisión más avanzada de la máquina virtual con soluciones como Update Management y Change and Inventory de [Azure Automation](../../automation/automation-intro.md).

Cuando tenga acceso al área de trabajo de Log Analytics, puede buscar la clave del área de trabajo y el identificador del área de trabajo en **Configuración avanzada** en **CONFIGURACIÓN**. Use el comando [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) para agregar la extensión Microsoft Monitoring Agent a la máquina virtual. Actualice los valores de variable del ejemplo siguiente de modo que reflejen la clave y el identificador del área de trabajo de Log Analytics.

```powershell
$workspaceId = "<Replace with your workspace Id>"
$key = "<Replace with your primary key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $workspaceId} `
  -ProtectedSettings @{"workspaceKey" = $key} `
  -Location eastus
```

Después de unos minutos, debería ver la nueva máquina virtual en el área de trabajo de Log Analytics.

![Hoja OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha configurado y revisado las máquinas virtuales con Azure Security Center. Ha aprendido a:

> [!div class="checklist"]
> * Crear una red virtual
> * Crear un grupo de recursos y una máquina virtual
> * Habilitar los diagnósticos de arranque en la máquina virtual
> * Ver los diagnósticos de arranque
> * Visualización de las métricas del host
> * Instalar la extensión de Diagnostics
> * Ver las métricas de la máquina virtual
> * Crear una alerta
> * Administrar actualizaciones de Windows
> * Supervisión de cambios y del inventario
> * Configurar la supervisión avanzada

En el siguiente tutorial obtendrá información sobre Azure Security Center.

> [!div class="nextstepaction"]
> [Administración de la seguridad de máquinas virtuales](./tutorial-azure-security.md)