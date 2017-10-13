---
title: "Supervisión y actualización de Azure y máquinas virtuales Windows | Microsoft Docs"
description: "Tutorial: Supervisar y actualizar una máquina virtual Windows con Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: a37aed8b3321d3518ffd73e09f5bb21266a7e577
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
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
> * Configurar la supervisión avanzada

Para realizar este tutorial es necesaria la versión 3.6 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, vea [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps).

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

    ![Ver las métricas del host](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Instalar la extensión de Diagnostics

Hay disponibles métricas básicas del host, pero para ver métricas más pormenorizadas específicas de la máquina virtual, es preciso instalar la extensión de Azure Diagnostics en la máquina virtual. La extensión de Azure Diagnostics permite recuperar más datos de supervisión y diagnóstico de la máquina virtual. Puede ver estas métricas de rendimiento y crear alertas basadas en el funcionamiento de la máquina virtual. La extensión de diagnósticos se instala a través de Azure Portal como se indica a continuación:

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroup** y, después, seleccione **myVM** en la lista de recursos.
2. Haga clic en **Configuración de diagnóstico**. La lista muestra que los *diagnósticos de arranque* ya están habilitados desde la sección anterior. Haga clic en la casilla *Métricas básicas*.
3. Haga clic en el botón **Habilitar supervisión a nivel de invitado**.

    ![Ver métricas de diagnósticos](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Ver las métricas de la máquina virtual

Las métricas de la máquina virtual se pueden ver de la misma manera que las de la máquina virtual host:

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroup** y, después, seleccione **myVM** en la lista de recursos.
2. Para ver el rendimiento de la máquina virtual, haga clic en **Métricas** en la hoja de la máquina virtual y seleccione cualquiera de las métricas de diagnóstico en **Métricas disponibles**.

    ![Ver las métricas de la máquina virtual](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Creación de alertas

Puede crear alertas basadas en métricas de rendimiento concretas. Se pueden usar alertas para enviar una notificación, por ejemplo, cuando el uso medio de la CPU supera un umbral concreto o cuando el espacio libre en disco disponible cae por debajo de una cantidad determinada. Las alertas se muestran en Azure Portal o se pueden enviar por correo electrónico. También puede desencadenar runbooks de Azure Automation o Azure Logic Apps en respuesta a las alertas que se generan.

En el siguiente ejemplo se crea una alerta para el uso medio de la CPU.

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroup** y, después, seleccione **myVM** en la lista de recursos.
2. Haga clic en **Reglas de alertas** en la hoja de la máquina virtual y, después, en **Agregar alerta de métrica** en la parte superior de la hoja de alertas.
4. Especifique un **nombre** para la alerta, como *myAlertRule*.
5. Para desencadenar una alerta cuando el porcentaje de la CPU supera 1,0 durante cinco minutos, deje el resto de valores predeterminados seleccionados.
6. Opcionalmente, active la casilla *Enviar correo electrónico a propietarios, colaboradores y lectores* para enviar una notificación por correo electrónico. La acción predeterminada es presentar una notificación en el portal.
7. Haga clic en el botón **Aceptar**.

## <a name="manage-windows-updates"></a>Administrar actualizaciones de Windows

La administración de actualizaciones permite administrar las actualizaciones y las revisiones para las máquinas virtuales Windows de Azure.
Directamente desde la máquina virtual, puede evaluar rápidamente el estado de las actualizaciones disponibles, programar la instalación de las actualizaciones necesarias y revisar los resultados de la implementación para comprobar si las actualizaciones se aplicaron correctamente en la máquina virtual.

Para obtener información de precios, consulte [Precios de automatización de la administración de actualizaciones](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Habilitar la administración de actualizaciones

Habilite la administración de actualizaciones para la máquina virtual:
 
1. En el lado izquierdo de la pantalla, seleccione **Máquinas virtuales**.
2. En la lista, seleccione una máquina virtual.
3. En la pantalla de la máquina virtual, en la sección **Operaciones**, haga clic en **Administración de actualizaciones**. Se abre la pantalla **Habilitar la administración de actualizaciones**.

Se realiza la validación para determinar si la administración de actualizaciones está habilitada para esta máquina virtual. La validación incluye comprobaciones de un área de trabajo de Log Analytics y la cuenta de Automation vinculada, y si la solución está en el área de trabajo.

Un área de trabajo de Log Analytics se usa para recopilar datos que se generan mediante características y servicios, como, por ejemplo, Administración de actualizaciones. El área de trabajo proporciona una única ubicación para revisar y analizar datos desde varios orígenes. Para llevar a cabo alguna acción adicional en máquinas virtuales que requieran actualizaciones, Azure Automation permite ejecutar scripts en máquinas virtuales, por ejemplo, para descargar y aplicar actualizaciones.

El proceso de validación también comprueba si la máquina virtual se aprovisiona con Microsoft Monitoring Agent (MMA) y Hybrid Worker. Este agente se usa para comunicarse con la máquina virtual y obtener información sobre el estado de actualización. 

Si no se cumplen estos requisitos previos, aparece un banner que le ofrece la opción de habilitar la solución.

![Banner de configuración de la incorporación de Update Management](./media/tutorial-monitoring/manageupdates-onboard-solution-banner.png)

Haga clic en el banner para habilitar la solución. Si se detecta la falta de alguno de los siguientes requisitos previos después de la validación, estos se agregarán automáticamente:

* Área de trabajo de [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automatización](../../automation/automation-offering-get-started.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) está habilitado en la máquina virtual.

Se abre la pantalla **Habilitar la administración de actualizaciones**. Configure las opciones y haga clic en **Habilitar**.

![Habilitar la solución Update Management](./media/tutorial-monitoring/manageupdates-update-enable.png)

La habilitación de la solución puede tardar hasta 15 minutos, período durante el cual no debe cerrar la ventana del explorador. Después de habilitar la solución, la información sobre las actualizaciones que faltan en la máquina virtual se pasa a Log Analytics.
Los datos pueden tardar entre 30 minutos y 6 horas en estar disponibles para el análisis.

### <a name="view-update-assessment"></a>Ver evaluación de la actualización

Una vez habilitado **Update Management**, se muestra la pantalla **Administración de actualizaciones**. Puede ver una lista de las actualizaciones que faltan en la pestaña **Actualizaciones que faltan**.

 ![Ver el estado de la actualización](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Programación de una implementación de actualizaciones

Para instalar actualizaciones, programe una implementación que se ajuste a su ventana de programación y servicio de versiones.
Puede elegir los tipos de actualizaciones que quiere incluir en la implementación. Por ejemplo, puede incluir actualizaciones de seguridad o críticas y excluir paquetes acumulativos de actualizaciones.

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

### <a name="view-results-of-an-update-deployment"></a>Ver los resultados de una implementación de actualizaciones

Después de iniciar la implementación programada, puede ver su estado en la pestaña **Implementaciones de actualizaciones** en la pantalla **Administración de actualizaciones**.
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

## <a name="advanced-monitoring"></a>Supervisión avanzada 

Mediante [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) se puede realizar una supervisión más avanzada de la máquina virtual. Si aún no lo ha hecho, puede suscribirse para disfrutar de una [evaluación gratuita](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) de Operations Management Suite.

Cuando tenga acceso al portal de OMS, puede buscar la clave y el identificador del área de trabajo en la hoja Configuración. Use el comando [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) para agregar la extensión OMS a la máquina virtual. Actualice los valores de variable del ejemplo siguiente de modo que reflejen la clave y el identificador del área de trabajo de OMS.  

```powershell
$omsId = "<Replace with your OMS Id>"
$omsKey = "<Replace with your OMS key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $omsId} `
  -ProtectedSettings @{"workspaceKey" = $omsKey} `
  -Location eastus
```

Después de unos minutos, debería ver la nueva máquina virtual en el área de trabajo de OMS. 

![Hoja OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha configurado y revisado las máquinas virtuales con Azure Security Center. Ha aprendido a:

> [!div class="checklist"]
> * Crear una red virtual
> * Crear un grupo de recursos y una máquina virtual 
> * Habilitar los diagnósticos de arranque en la máquina virtual
> * Ver los diagnósticos de arranque
> * Ver las métricas del host
> * Instalar la extensión de Diagnostics
> * Ver las métricas de la máquina virtual
> * Crear una alerta
> * Administrar actualizaciones de Windows
> * Configurar la supervisión avanzada

En el siguiente tutorial obtendrá información sobre Azure Security Center.

> [!div class="nextstepaction"]
> [Administración de la seguridad de máquinas virtuales](./tutorial-azure-security.md)