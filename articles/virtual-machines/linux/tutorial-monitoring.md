---
title: Supervisión y actualización de máquinas virtuales Linux en Azure | Microsoft Docs
description: Aprender a supervisar los diagnósticos de arranque y las métricas de rendimiento, así como a administrar las actualizaciones de paquetes en una máquina virtual Linux en Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d5fb239ffd6a957cbb088bf4843819e2c886cee8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Supervisión y actualización de una máquina virtual Linux en Azure

Para asegurarse de que las máquinas virtuales (VM) de Azure se ejecutan correctamente, puede revisar los diagnósticos de arranque y las métricas de rendimiento, y administrar las actualizaciones de paquetes. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Habilitar los diagnósticos de arranque en la máquina virtual
> * Ver los diagnósticos de arranque
> * Visualización de las métricas del host
> * Habilitar una extensión Diagnostics en la máquina virtual
> * Visualización de las métricas de la máquina virtual
> * Crear alertas basadas en métricas de diagnóstico
> * Administrar actualizaciones de paquetes
> * Supervisión de cambios y del inventario
> * Configurar la supervisión avanzada

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="create-vm"></a>Creación de una máquina virtual

Para ver las métricas y los diagnósticos en acción, necesita una máquina virtual. En primer lugar, cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroupMonitor* en la ubicación *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Ahora cree una máquina virtual con el comando [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create). En el ejemplo siguiente se crea una máquina virtual denominada *myVM*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Habilitación de los diagnósticos de arranque

Cuando las máquinas virtuales Linux arrancan, la extensión de los diagnósticos de arranque captura la salida del arranque y la almacena en Azure Storage. Estos datos se pueden utilizar para solucionar los problemas de arranque de la máquina virtual. Los diagnósticos de arranque no se habilitan automáticamente al crear una máquina virtual Linux mediante la CLI de Azure.

Antes de habilitar los diagnósticos de arranque, es preciso crear una cuenta de almacenamiento para almacenar los registros de arranque. Las cuentas de almacenamiento deben tener un nombre único global, tener entre 3 y 24 caracteres, y deben contener solo números y letras en minúscula. Cree una cuenta de almacenamiento con el comando [az storage account create](/cli/azure/storage/account#az_storage_account_create). En este ejemplo, se utiliza una cadena aleatoria para crear un nombre de cuenta de almacenamiento único.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Al habilitar los diagnósticos de arranque, se necesita el identificador URI para el contenedor de Blob Storage. El siguiente comando de consulta la cuenta de almacenamiento a la que se devuelve este identificador URI. El valor del identificador URI se almacena en el nombre de variable *bloburi*, que se usa en el paso siguiente.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Habilite ahora los diagnósticos de arranque con [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). El valor `--storage` es el identificador URI del blob que se recopila en el paso anterior.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Ver los diagnósticos de arranque

Cuando se habilitan los diagnósticos de arranque, cada vez que la máquina virtual se detiene y se inicia, la información acerca del proceso de arranque se escribe en un archivo de registro. En este ejemplo, en primer lugar desasigne la máquina virtual con el comando [az vm deallocate](/cli/azure/vm#az_vm_deallocate) como se indica a continuación:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Ahora inicie la máquina virtual con el comando [az vm start]( /cli/azure/vm#az_vm_stop) como se indica a continuación:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Puede obtener los datos de los diagnósticos de arranque de *myVM* con el comando [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log) como se indica a continuación:

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Visualización de las métricas del host

Una máquina virtual Linux tiene un host dedicado de Azure que interactúa con. Se recopilan automáticamente las métricas del host y se pueden ver en Azure Portal como se indica a continuación:

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroupMonitor** y, después, seleccione **myVM** en la lista de recursos.
1. Para ver el funcionamiento de la máquina virtual host, haga clic en **Métricas** en la hoja de la máquina virtual y seleccione cualquiera de las métricas de *[Host]* en **Métricas disponibles**.

    ![Visualización de las métricas del host](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Instalar la extensión de Diagnostics

> [!IMPORTANT]
> En este documento se describe la versión 2.3 de la extensión Diagnostics de Linux., que está en desuso. Se admitirá la versión 2.3 hasta el 30 de junio de 2018.
>
> En su lugar, puede instalarse la versión 3.0 de la extensión Diagnostics de Linux. Consulte [la documentación](./diagnostic-extension.md) para más información.

Hay disponibles métricas básicas del host, pero para ver métricas más pormenorizadas específicas de la máquina virtual, es preciso instalar la extensión de Azure Diagnostics en la máquina virtual. La extensión de Azure Diagnostics permite recuperar más datos de supervisión y diagnóstico de la máquina virtual. Puede ver estas métricas de rendimiento y crear alertas basadas en el funcionamiento de la máquina virtual. La extensión de diagnósticos se instala a través de Azure Portal como se indica a continuación:

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroup** y, después, seleccione **myVM** en la lista de recursos.
1. Haga clic en **Configuración de diagnóstico**. La lista muestra que los *diagnósticos de arranque* ya están habilitados desde la sección anterior. Haga clic en la casilla *Métricas básicas*.
1. En la sección *Cuenta de almacenamiento*, busque y seleccione la cuenta *mydiagdata [1234]* creada en la sección anterior.
1. Haga clic en el botón **Save** (Guardar).

    ![Ver métricas de diagnósticos](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Visualización de las métricas de la máquina virtual

Las métricas de la máquina virtual se pueden ver de la misma manera que las de la máquina virtual host:

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroup** y, después, seleccione **myVM** en la lista de recursos.
1. Para ver el rendimiento de la máquina virtual, haga clic en **Métricas** en la hoja de la máquina virtual y seleccione cualquiera de las métricas de diagnóstico en **Métricas disponibles**.

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

## <a name="manage-package-updates"></a>Administrar actualizaciones de paquetes

Administración de actualizaciones le permite administrar las actualizaciones y revisiones de las máquinas virtuales Linux de Azure.
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

![Habilitar la solución Update Management](./media/tutorial-monitoring/manage-updates-update-enable.png)

La habilitación de la solución puede tardar hasta 15 minutos. Durante este tiempo, no debería cerrar la ventana del explorador. Después de habilitar la solución, la información sobre las actualizaciones que faltan en la máquina virtual se pasa a Log Analytics. Los datos pueden tardar entre 30 minutos y 6 horas en estar disponibles para el análisis.

### <a name="view-update-assessment"></a>Ver evaluación de la actualización

Una vez habilitado **Update Management**, se muestra la pantalla **Administración de actualizaciones**. Después de completar la evaluación de las actualizaciones, verá una lista de las actualizaciones que faltan en la pestaña **Actualizaciones que faltan**.

 ![Ver el estado de la actualización](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Programación de una implementación de actualizaciones

Para instalar actualizaciones, programe una implementación que se ajuste a su ventana de programación y servicio de versiones. Puede elegir los tipos de actualizaciones que quiere incluir en la implementación. Por ejemplo, puede incluir actualizaciones de seguridad o críticas y excluir paquetes acumulativos de actualizaciones.

Programe una nueva implementación de actualizaciones para la máquina virtual. Para ello, haga clic en **Programar implementación de actualizaciones** en la parte superior de la pantalla **Administración de actualizaciones**. En la pantalla **Nueva implementación de actualización**, especifique la siguiente información:

* **Nombre**: proporcione un nombre único para identificar la implementación de actualizaciones.
* **Clasificación de actualizaciones**: seleccione los tipos de software que la implementación de actualizaciones incluyó en la implementación. Los tipos de clasificación son:
  * Actualizaciones críticas y de seguridad
  * Otras actualizaciones
* **Actualizaciones para excluir**: puede proporcionar una lista de nombres de paquetes que se deben omitir durante la implementación de actualizaciones. Los nombres de paquetes admiten caracteres comodín (como, por ejemplo, \*kernal\*).

  ![Pantalla de configuración de la programación de actualizaciones](./media/tutorial-monitoring/manage-updates-exclude-linux.png)

* **Configuración de la programación**: puede aceptar la fecha y hora predeterminadas, es decir, 30 minutos después de la hora actual, o bien especificar una hora distinta.
  También puede especificar si la implementación se produce una vez o configurar una programación periódica. Haga clic en la opción Periódica en Periodicidad para configurar una programación periódica.

  ![Pantalla de configuración de la programación de actualizaciones](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **Ventana de mantenimiento (minutos)**: especifique el período de tiempo en el que desea que se produzca la implementación de actualizaciones. Esto ayuda a garantizar que los cambios se realizan en las ventanas de servicio definidas.

Después de completar la configuración de la programación, haga clic en el botón **Crear** para volver al panel de estado.
Tenga en cuenta que la tabla **Programada** muestra la programación de implementación que ha creado.

> [!WARNING]
> Para las actualizaciones que requieren un reinicio, la máquina virtual se reinicia automáticamente.

### <a name="view-results-of-an-update-deployment"></a>Visualización de los resultados de una implementación de actualizaciones

Después de que se inicie la implementación programada, puede ver su estado en la pestaña **Implementaciones de actualizaciones** en la pantalla **Update Management**.
Si se está ejecutando actualmente, su estado se muestra como **En curso**. Cuando se completa, si se realiza correctamente, cambia a **Correcto**.
Si se produce un error con una o varias actualizaciones en la implementación, el estado es **Error parcial**.
Haga clic en la implementación de actualizaciones completada para ver el panel correspondiente.

![Panel de estado de Implementación de actualizaciones de la implementación específica](./media/tutorial-monitoring/manage-updates-view-results.png)

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

Configure la ubicación, el área de trabajo de Log Analytics y la cuenta de Automation que use y haga clic en **Habilitar**. Si los campos aparecen atenuados, significa que otra solución de automatización está habilitada para la máquina virtual y que deben usarse la misma área de trabajo y cuenta de Automation. Aunque las soluciones son independientes en el menú, constituyen la misma solución. Al habilitar una, se habilitan ambas para la máquina virtual.

![Habilitación del seguimiento de cambios e inventario](./media/tutorial-monitoring/manage-inventory-enable.png)

Una vez que se ha habilitado la solución, puede tardar algún tiempo mientras se recopila el inventario en la máquina virtual hasta que aparezcan los datos.

### <a name="track-changes"></a>Control de cambios

En la máquina virtual, seleccione **Change Tracking** en **OPERACIONES**. Haga clic en **Editar configuración**, se muestra la página **Change Tracking**. Seleccione el tipo de configuración a la que desea realizar un seguimiento y, a continuación, haga clic en **+ Agregar** para configurar las opciones. La opción disponible en Linux es **Archivos de Linux**

Para obtener información detallada Change Tracking, consulte [Solucionar de los problemas de los cambios en una máquina virtual](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Visualización del inventario

En la máquina virtual, seleccione **Inventario** en **OPERACIONES**. En la pestaña **Software**, hay una lista en forma de tabla con el software que se encontró. En la tabla puede verse cada registro de software con gran detalle. Estos detalles incluyen el nombre del software, la versión, el editor, la última hora de actualización.

![Visualización del inventario](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Supervisión de los registros de actividad y cambios

Desde la página **Change Tracking** en la máquina virtual, seleccione **Administrar conexión de registro de actividad**. Esta tarea abrirá la página **Registro de actividad de Azure**. Seleccione **Conectar** para conectar Change Tracking al registro de actividad de Azure en la máquina virtual.

Con esta opción habilitada, navegue hasta la página **Información general** en la máquina virtual y seleccione **Detener** para detenerla. Cuando se le solicite, seleccione **Sí** para detener la máquina virtual. Cuando esté desasignada, seleccione **Iniciar** para reiniciar la máquina virtual.

Si se detiene y se inicia una máquina virtual, se registra un evento en el registro de actividad. Vuelva a la página **Change Tracking**. Seleccione la pestaña **Eventos** en la parte inferior de la página. Después de un tiempo, los eventos se mostrarán en el gráfico y la tabla. Cada evento puede seleccionarse para ver información detallada sobre él.

![Visualización de cambios en el registro de actividad](./media/tutorial-monitoring/manage-activitylog-view-results.png)

El gráfico muestra los cambios que se han producido con el tiempo. Después de agregar una conexión del registro de actividad, el gráfico de líneas en la parte superior muestra los eventos del registro de actividad de Azure. Cada fila de los gráficos de barras representa un tipo de cambio diferente del cual se puede realizar un seguimiento. Estos tipos son demonios, archivos y software de Linux. En la pestaña de cambios se muestran los detalles de los cambios mostrados en la visualización en orden descendente según la hora en la cual se produjo el cambio (los más recientes se muestran primero).

## <a name="advanced-monitoring"></a>Supervisión avanzada

Puede realizar una supervisión más avanzada de la máquina virtual con soluciones como Update Management y Change and Inventory de [Azure Automation](../../automation/automation-intro.md).

Cuando tenga acceso al área de trabajo de Log Analytics, puede buscar la clave del área de trabajo y el identificador del área de trabajo en **Configuración avanzada** en **CONFIGURACIÓN**. Reemplace \<workspace-key\> y \<workspace-id\> por los valores del espacio de trabajo de Log Analytics y, a continuación, puede usar **az vm extension set** para agregar la extensión a la máquina virtual:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

Después de unos minutos, debería ver la nueva máquina virtual en el área de trabajo de Log Analytics.

![Hoja OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha configurado, revisado y administrado las actualizaciones para una máquina virtual. Ha aprendido a:

> [!div class="checklist"]
> * Habilitar los diagnósticos de arranque en la máquina virtual
> * Ver los diagnósticos de arranque
> * Visualización de las métricas del host
> * Habilitar una extensión Diagnostics en la máquina virtual
> * Visualización de las métricas de la máquina virtual
> * Crear alertas basadas en métricas de diagnóstico
> * Administrar actualizaciones de paquetes
> * Supervisión de cambios y del inventario
> * Configurar la supervisión avanzada

Pase al siguiente tutorial para obtener información sobre Azure Security Center.

> [!div class="nextstepaction"]
> [Administración de la seguridad de máquinas virtuales](./tutorial-azure-security.md)
