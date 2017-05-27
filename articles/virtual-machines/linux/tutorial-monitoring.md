---
title: "Supervisión de máquinas virtuales Linux en Azure | Microsoft Docs"
description: "Aprenda a supervisar los diagnósticos de arranque y las métricas de rendimiento en una máquina virtual Linux en Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 7599ea9fef4b2e8368ffdc18f357fe8938cc024f
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017

---

# <a name="how-to-monitor-a-linux-virtual-machine-in-azure"></a>Supervisión de una máquina virtual Linux en Azure

Para asegurarse de que las máquinas virtuales (VM) de Azure se ejecutan correctamente, puede revisar los diagnósticos de arranque y las métricas de rendimiento. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Habilitar los diagnósticos de arranque en la máquina virtual
> * Ver los diagnósticos de arranque
> * Visualización de las métricas del host
> * Habilitar una extensión Diagnostics en la máquina virtual
> * Visualización de las métricas de la máquina virtual
> * Crear alertas basadas en métricas de diagnóstico
> * Configurar la supervisión avanzada

Para realizar este tutorial es necesaria la versión 2.0.4 o superior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). También puede usar [Cloud Shell](/azure/cloud-shell/quickstart) desde el explorador.


## <a name="create-vm"></a>Creación de una máquina virtual
Para ver las métricas y los diagnósticos en acción, necesita una máquina virtual. En primer lugar, cree un grupo de recursos con [az group create](/cli/azure/gropu#create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroupMonitor* en la ubicación *eastus*.

```azurecli
az group create --name myResourceGroupMonitor --location eastus
```

Ahora cree una máquina virtual con el comando [az vm create](https://docs.microsoft.com/cli/azure/vm#create). En el ejemplo siguiente se crea una máquina virtual denominada *myVM*:

```azurecli
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

```azurecli
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Al habilitar los diagnósticos de arranque, se necesita el identificador URI para el contenedor de Blob Storage. El siguiente comando de consulta la cuenta de almacenamiento a la que se devuelve este identificador URI. El valor del identificador URI se almacena en el nombre de variable *bloburi*, que se usa en el paso siguiente.

```azurecli
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Habilite ahora los diagnósticos de arranque con [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#enable). El valor `--storage` es el identificador URI del blob que se recopila en el paso anterior.

```azurecli
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```


## <a name="view-boot-diagnostics"></a>Ver los diagnósticos de arranque

Cuando se habilitan los diagnósticos de arranque, cada vez que la máquina virtual se detiene y se inicia, la información acerca del proceso de arranque se escribe en un archivo de registro. En este ejemplo, en primer lugar desasigne la máquina virtual con el comando [az vm deallocate](/cli/azure/vm#deallocate) como se indica a continuación:

```azurecli
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Ahora inicie la máquina virtual con el comando [az vm start]( /cli/azure/vm#stop) como se indica a continuación:

```azurecli
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Puede obtener los datos de los diagnósticos de arranque de *myVM* con el comando [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#get-boot-log) como se indica a continuación:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```


## <a name="view-host-metrics"></a>Visualización de las métricas del host

Una máquina virtual Linux tiene un host dedicado de Azure que interactúa con. Se recopilan automáticamente las métricas del host y se pueden ver en Azure Portal como se indica a continuación:

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroupMonitor** y, después, seleccione **myVM** en la lista de recursos.
2. Para ver el funcionamiento de la máquina virtual host, haga clic en **Métricas** en la hoja de la máquina virtual y seleccione cualquiera de las métricas de *[Host]* en **Métricas disponibles**.

    ![Visualización de las métricas del host](./media/tutorial-monitoring/monitor-host-metrics.png)


## <a name="install-diagnostics-extension"></a>Instalación de la extensión Diagnostics

Las métricas básicas del host están disponibles, pero para ver métricas más individualizadas específicas de la máquina virtual, es preciso instalar la extensión de Azure Diagnostics en la máquina virtual. La extensión de Azure Diagnostics permite recuperar más datos de supervisión y diagnóstico de la máquina virtual. Puede ver estas métricas de rendimiento y crear alertas basadas en el funcionamiento de la máquina virtual. La extensión de diagnósticos se instala a través de Azure Portal como se indica a continuación:

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroup** y, después, seleccione **myVM** en la lista de recursos.
2. Haga clic en **Configuración de diagnóstico**. La lista muestra que los *diagnósticos de arranque* ya están habilitados de la sección anterior. Haga clic en la casilla *Métricas básicas*.
3. En la sección *Cuenta de almacenamiento*, busque y seleccione la cuenta *mydiagdata [1234]* creada en la sección anterior.
4. Haga clic en el botón **Save** (Guardar).

    ![Ver métricas de diagnósticos](./media/tutorial-monitoring/enable-diagnostics-extension.png)


## <a name="view-vm-metrics"></a>Visualización de las métricas de la máquina virtual

Las métricas de la máquina virtual se pueden ver de la misma manera que las de la máquina virtual host:

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroup** y, después, seleccione **myVM** en la lista de recursos.
2. Para ver el funcionamiento de la máquina virtual, haga clic en **Métricas** en la hoja de la máquina virtual y seleccione cualquiera de las métricas de diagnóstico en **Métricas disponibles**.

    ![Visualización de las métricas de la máquina virtual](./media/tutorial-monitoring/monitor-vm-metrics.png)


## <a name="create-alerts"></a>Creación de alertas

Puede crear alertas basadas en métricas de rendimiento concretas. Se pueden utilizar alertas para enviar una notificación por ejemplo cuando el uso medio de la CPU supera un umbral concreto o cuando el espacio en disco libre disponible no llega a una cantidad determinada. Las alertas se muestran en Azure Portal o se pueden enviar por correo electrónico. También puede desencadenar runbooks de Azure Automation o Azure Logic Apps en respuesta a las alertas que se generan.

En el siguiente ejemplo se crea una alerta para el uso medio de la CPU.

1. En Azure Portal, haga clic en **Grupos de recursos**, seleccione **myResourceGroup** y, después, seleccione **myVM** en la lista de recursos.
2. Haga clic en **Reglas de alertas** en la hoja de la máquina virtual y, después, en **Agregar alerta de métrica** en la parte superior de la hoja de alertas.
4. Especifique un **nombre** para la alerta, como *myAlertRule*
5. Para desencadenar una alerta cuando el porcentaje de la CPU supera 1,0 durante cinco minutos, deje el resto de valores predeterminados seleccionados.
6. Opcionalmente, active la casilla *Lectores, contribuyentes y propietarios de correo electrónico* para enviar una notificación por correo electrónico. La acción predeterminada es presentar una notificación en el portal.
7. Haga clic en el botón **Aceptar**.


## <a name="advanced-monitoring"></a>Supervisión avanzada 

Mediante [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) se puede realizar una supervisión más avanzada de la máquina virtual. Si aún no lo ha hecho, puede suscribirse para disfrutar de [evaluación gratuita](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) de Operations Management Suite.

Cuando tenga acceso al portal de OMS, puede buscar la clave y el identificador del área de trabajo en la hoja Configuración. Reemplace <workspace-key> y <workspace-id> por los valores del espacio de trabajo de OMS y, a continuación, puede usar **az vm extension set** para agregar la extensión de OMS a la máquina virtual:

```azurecli
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

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha configurado y revisado las máquinas virtuales con Azure Security Center. Ha aprendido a:

> [!div class="checklist"]
> * Habilitar los diagnósticos de arranque en la máquina virtual
> * Ver los diagnósticos de arranque
> * Visualización de las métricas del host
> * Habilitar una extensión Diagnostics en la máquina virtual
> * Visualización de las métricas de la máquina virtual
> * Crear alertas basadas en métricas de diagnóstico
> * Configurar la supervisión avanzada

En el siguiente tutorial obtendrá información acerca de Azure Security Center.

> [!div class="nextstepaction"]
> [Administración de la seguridad de máquinas virtuales](./tutorial-azure-security.md)
