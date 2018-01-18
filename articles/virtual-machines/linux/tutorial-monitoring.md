---
title: "Supervisión y actualización de máquinas virtuales Linux en Azure | Microsoft Docs"
description: "Aprender a supervisar los diagnósticos de arranque y las métricas de rendimiento, así como a administrar las actualizaciones de paquetes en una máquina virtual Linux en Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: cde484dd59ec6e2821678766726c02362222d496
ms.sourcegitcommit: 7d4b3cf1fc9883c945a63270d3af1f86e3bfb22a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
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
> * Configurar la supervisión avanzada


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-vm"></a>Creación de una máquina virtual

Para ver las métricas y los diagnósticos en acción, necesita una máquina virtual. En primer lugar, cree un grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroupMonitor* en la ubicación *eastus*.

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

Antes de habilitar los diagnósticos de arranque, es preciso crear una cuenta de almacenamiento para almacenar los registros de arranque. Las cuentas de almacenamiento deben tener un nombre único global, tener entre 3 y 24 caracteres, y deben contener solo números y letras en minúscula. Cree una cuenta de almacenamiento con el comando [az storage account create](/cli/azure/storage/account#create). En este ejemplo, se utiliza una cadena aleatoria para crear un nombre de cuenta de almacenamiento único. 

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

Cuando se habilitan los diagnósticos de arranque, cada vez que la máquina virtual se detiene y se inicia, la información acerca del proceso de arranque se escribe en un archivo de registro. En este ejemplo, en primer lugar desasigne la máquina virtual con el comando [az vm deallocate](/cli/azure/vm#deallocate) como se indica a continuación:

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Ahora inicie la máquina virtual con el comando [az vm start]( /cli/azure/vm#stop) como se indica a continuación:

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
4. Especifique un **nombre** para la alerta, como *myAlertRule*.
5. Para desencadenar una alerta cuando el porcentaje de la CPU supera 1,0 durante cinco minutos, deje el resto de valores predeterminados seleccionados.
6. Opcionalmente, active la casilla *Enviar correo electrónico a propietarios, colaboradores y lectores* para enviar una notificación por correo electrónico. La acción predeterminada es presentar una notificación en el portal.
7. Haga clic en el botón **Aceptar**.

## <a name="manage-package-updates"></a>Administrar actualizaciones de paquetes

La administración de actualizaciones permite administrar las revisiones y actualizaciones de paquetes para las máquinas virtuales Linux de Azure. Directamente desde la máquina virtual, puede evaluar rápidamente el estado de las actualizaciones disponibles, programar la instalación de las actualizaciones necesarias y revisar los resultados de la implementación para comprobar si las actualizaciones se aplicaron correctamente en la máquina virtual.

Para obtener información de precios, consulte [Precios de automatización de la administración de actualizaciones](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management-preview"></a>Habilitar la administración de actualizaciones (versión preliminar)

Habilitar la administración de actualizaciones para la máquina virtual

1. En el lado izquierdo de la pantalla, seleccione **Máquinas virtuales**.
1. En la lista, seleccione una máquina virtual.
1. En la pantalla de la máquina virtual, en la sección **Operaciones**, haga clic en **Administración de actualizaciones**. Se abre la pantalla **Habilitar la administración de actualizaciones**.

Se realiza la validación para determinar si la administración de actualizaciones está habilitada para esta máquina virtual. La validación incluye comprobaciones de un área de trabajo de Log Analytics y la cuenta de Automation vinculada, y si la solución está en el área de trabajo.

Un área de trabajo de Log Analytics se usa para recopilar datos que se generan mediante características y servicios, como, por ejemplo, Administración de actualizaciones. El área de trabajo proporciona una única ubicación para revisar y analizar datos desde varios orígenes. Para llevar a cabo alguna acción adicional en máquinas virtuales que requieran actualizaciones, Azure Automation permite ejecutar scripts en máquinas virtuales, por ejemplo, para descargar y aplicar actualizaciones.

El proceso de validación también comprueba si la máquina virtual se aprovisiona con Microsoft Monitoring Agent (MMA) y Hybrid Worker. Este agente se usa para comunicarse con la máquina virtual y obtener información sobre el estado de actualización. 

Si no se cumplen estos requisitos previos, aparece un banner que le ofrece la opción de habilitar la solución.

![Banner de configuración de la incorporación de Update Management](./media/tutorial-monitoring/manage-updates-onboard-solution-banner.png)

Haga clic en el banner para habilitar la solución. Si se detecta la falta de alguno de los siguientes requisitos previos después de la validación, estos se agregarán automáticamente:

* Área de trabajo de [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automation](../../automation/automation-offering-get-started.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) está habilitado en la máquina virtual.

Se abre la pantalla **Habilitar la administración de actualizaciones**. Configure las opciones y haga clic en **Habilitar**.

![Habilitar la solución Update Management](./media/tutorial-monitoring/manage-updates-update-enable.png)

La habilitación de la solución puede tardar hasta 15 minutos, período durante el cual no debe cerrar la ventana del explorador. Después de habilitar la solución, la información sobre las actualizaciones que faltan en el Administrador de paquetes en la máquina virtual se pasa a Log Analytics.
Los datos pueden tardar entre 30 minutos y 6 horas en estar disponibles para el análisis.

### <a name="view-update-assessment"></a>Visualización de la evaluación de la actualización

Una vez habilitada la solución **Update Management**, se muestra la pantalla **Administración de actualizaciones**. Puede ver una lista de las actualizaciones que faltan en la pestaña **Actualizaciones que faltan**.

![Ver el estado de la actualización](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Programación de una implementación de actualizaciones

Para instalar actualizaciones, programe una implementación que se ajuste a su ventana de programación y mantenimiento de versiones.

Programe una nueva implementación de actualizaciones para la máquina virtual. Para ello, haga clic en **Programar implementación de actualizaciones** en la parte superior de la pantalla **Administración de actualizaciones**. En la pantalla **Nueva implementación de actualización**, especifique la siguiente información:

* **Nombre**: proporcione un nombre único para identificar la implementación de actualizaciones.
* **Actualizaciones para excluir**: seleccione esta opción para especificar los nombres de los paquetes que se excluirán de la actualización.
* **Configuración de la programación**: puede aceptar la fecha y hora predeterminadas, es decir, 30 minutos después de la hora actual, o bien especificar una hora distinta. También puede especificar si la implementación se produce una vez o configurar una programación periódica. Haga clic en la opción Periódica en Periodicidad para configurar una programación periódica.

  ![Pantalla de configuración de la programación de actualizaciones](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **Ventana de mantenimiento (minutos)**: especifique el período de tiempo en el que desea que se produzca la implementación de actualizaciones.  Esto ayuda a garantizar que los cambios se realizan en las ventanas de mantenimiento definidas. 

Después de completar la configuración de la programación, haga clic en el botón **Crear** para volver al panel de estado.
Tenga en cuenta que la tabla **Programada** muestra la programación de implementación que ha creado.

> [!WARNING]
> La máquina virtual se reiniciará automáticamente después de instalar las actualizaciones si queda tiempo suficiente en la ventana de mantenimiento.

Update Management usa el Administrador de paquetes existente en la máquina virtual para instalar paquetes.

### <a name="view-results-of-an-update-deployment"></a>Ver los resultados de una implementación de actualizaciones

Después de iniciar la implementación programada, puede ver su estado en la pestaña **Implementaciones de actualizaciones** en la pantalla **Administración de actualizaciones**.
Si se está ejecutando actualmente, su estado se muestra como **En curso**. Cuando se completa, si se realiza correctamente, cambia a **Correcto**.
Si se produce un error con una o varias actualizaciones en la implementación, el estado es **Error**.
Haga clic en la implementación de actualizaciones completada para ver el panel correspondiente.

![Panel de estado de Implementación de actualizaciones de la implementación específica](./media/tutorial-monitoring/manage-updates-view-results.png)

En el icono **Resultados de actualización** encontrará un resumen del número total de actualizaciones y los resultados de la implementación en la máquina virtual.
En la tabla de la derecha encontrará un análisis detallado de cada actualización y los resultados de la instalación, que podrían ser uno de los valores siguientes:

* **No intentado**: la actualización no se instaló porque no había tiempo disponible suficiente de acuerdo con la duración definida para la ventana de mantenimiento.
* **Correcto**: la actualización se descargó e instaló correctamente en la máquina virtual.
* **Error**: la actualización no se pudo descargar ni instalar en la máquina virtual.

Haga clic en **Todos los registros** para ver todas las entradas de registro que creó la implementación.

Haga clic en el icono **Salida** para ver el flujo de trabajo del runbook responsable de administrar la implementación de actualizaciones en la máquina virtual de destino.

Haga clic en **Errores** para ver información detallada sobre los errores de la implementación.

## <a name="advanced-monitoring"></a>Supervisión avanzada 

Mediante [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) se puede realizar una supervisión más avanzada de la máquina virtual. Si aún no lo ha hecho, puede suscribirse para disfrutar de una [evaluación gratuita](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) de Operations Management Suite.

Cuando tenga acceso al portal de OMS, puede buscar la clave y el identificador del área de trabajo en la hoja Configuración. Reemplace <workspace-key> y <workspace-id> por los valores del espacio de trabajo de OMS y, a continuación, puede usar **az vm extension set** para agregar la extensión de OMS a la máquina virtual:

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

En la hoja Búsqueda de registros del portal de OMS, debería ver *myVM* como se muestra en la siguiente imagen:

![Hoja OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha configurado, revisado y administrado las actualizaciones para una máquina virtual. Ha aprendido a:

> [!div class="checklist"]
> * Habilitar los diagnósticos de arranque en la máquina virtual
> * Ver los diagnósticos de arranque
> * Visualización de las métricas del host
> * Habilitar una extensión Diagnostics en la máquina virtual
> * Visualización de las métricas de la máquina virtual
> * Crear alertas basadas en métricas de diagnóstico
> * Administrar actualizaciones de paquetes
> * Configurar la supervisión avanzada

Pase al siguiente tutorial para obtener información sobre Azure Security Center.

> [!div class="nextstepaction"]
> [Administración de la seguridad de máquinas virtuales](./tutorial-azure-security.md)
