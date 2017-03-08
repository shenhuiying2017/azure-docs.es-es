---
title: "Administración de capturas de paquetes con Azure Network Watcher: CLI de Azure | Microsoft Docs"
description: "En esta página se explica cómo administrar la característica de captura de paquetes de Network Watcher mediante la CLI de Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: d2a65104743d9497debdc85c134fd1a06114c514
ms.openlocfilehash: 279664a51eab79c42a14ed9c9bb5f65cc43aaab2
ms.lasthandoff: 02/23/2017

---

# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-cli"></a>Administración de capturas de paquetes con Azure Network Watcher mediante la CLI de Azure

> [!div class="op_single_selector"]
> - [Portal de Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI](network-watcher-packet-capture-manage-cli.md)
> - [API de REST de Azure](network-watcher-packet-capture-manage-rest.md)

La captura de paquetes de Network Watcher permite crear sesiones de captura para realizar el seguimiento del tráfico hacia y desde una máquina virtual. La sesión de captura cuenta con filtros para asegurarse de capturar solo el tráfico que se desea. La captura de paquetes ayuda a diagnosticar anomalías de la red, tanto de forma activa como reactiva. Otros usos son la recopilación de estadísticas de red, la obtención de información sobre las intrusiones de red y la depuración de las comunicaciones cliente-servidor, entre otros. Esta funcionalidad permite desencadenar capturas de paquetes de forma remota, lo que reduce la carga de tener que ejecutar una captura de paquetes manualmente y en el equipo deseado, y permite ahorrar tiempo.

Este artículo le guiará por las diferentes tareas de administración que están actualmente disponibles para la captura de paquetes.

- [**Inicio de una captura de paquetes**](#start-a-packet-capture)
- [**Detención de una captura de paquetes**](#stop-a-packet-capture)
- [**Eliminación de una captura de paquetes**](#delete-a-packet-capture)
- [**Descarga de una captura de paquetes**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se da por hecho que tiene los siguientes recursos:

- Una instancia de Network Watcher en la región donde desea crear una captura de paquetes.
- Una máquina virtual con la extensión de captura de paquetes habilitada.

> [!IMPORTANT]
> La captura de paquetes requiere que se esté ejecutando un agente en la máquina virtual. El agente se instala como una extensión. Para más instrucciones acerca de las extensiones de máquina virtual, visite [Características y extensiones de las máquinas virtuales](../virtual-machines/virtual-machines-windows-extensions-features.md).

## <a name="install-vm-extension"></a>Instalación de la extensión de máquina virtual

### <a name="step-1"></a>Paso 1

Ejecute el cmdlet `azure vm extension set` para instalar el agente de captura de paquetes en la máquina virtual invitada.

En las máquinas virtuales Windows:

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r anyExtensionReferenceName -n NetworkWatcherAgentWindows -o 1.4
```

### <a name="step-2"></a>Paso 2

Para asegurarse de que el agente está instalado, ejecute el cmdlet `vm extension get` y pásele el nombre de la máquina virtual y del grupo de recursos. Compruebe la lista resultante para asegurarse de que el agente está instalado.

```azurecli
azure vm extension get -g resourceGroupName -m virtualMachineName
```

El siguiente es un ejemplo de la respuesta de la ejecución de `azure vm extension get`.

```
info:    Executing command vm extension get
+ Looking up the VM "virtualMachineName"
data:    Publisher                       Name                     Version  State
data:    ------------------------------  -----------------------  -------  ---------
data:    Microsoft.Azure.NetworkWatcher  NetworkWatcherAgentTest  1.4      Succeeded
info:    vm extension get command OK
```

## <a name="start-a-packet-capture"></a>Inicio de una captura de paquetes

Una vez completados los pasos anteriores, el agente de captura de paquetes está instalado en la máquina virtual.

### <a name="step-1"></a>Paso 1

El siguiente paso es recuperar la instancia de Network Watcher. Esta variable se pasa al cmdlet `network watcher show` en el paso 4.

```azurecli
azure network watcher show -g resourceGroup -n networkWatcherName
```

### <a name="step-2"></a>Paso 2

Recupere una cuenta de almacenamiento. Esta cuenta de almacenamiento se utiliza para almacenar el archivo de captura de paquetes.

```azurecli
azure storage account list
```

### <a name="step-3"></a>Paso 3

Se pueden usar filtros para limitar los datos que se almacenan en la captura de paquetes. En el ejemplo siguiente se configura una captura de paquetes con varios filtros.  Los tres primeros filtros recopilan el tráfico TCP saliente solo de la dirección IP local 10.0.0.3 a los puertos de destino 20, 80 y 443.  El último filtro recopila solo el tráfico UDP.

```azurecli
azure network watcher packet-capture create -g resourceGroupName -w networkWatcherName -n packetCaptureName -t targetResourceId -o storageAccountResourceId -f "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Se pueden definir varios filtros para una captura de paquetes. Si se usa una estructura de filtros compleja, es mejor usar filtros como un archivo json para evitar errores de sintaxis. Por ejemplo, use el indicador "-r" (en lugar de "-f") y pase la ubicación de un archivo json que contiene los siguientes filtros:

```json
[
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"20"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"80"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"443"
    },
    {
        "protocol":"UDP"
    }
]
```


El ejemplo siguiente es la salida esperada de la ejecución del cmdlet `network watcher packet-capture create`.

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture create command OK
```

## <a name="get-a-packet-capture"></a>Obtención de una captura de paquetes

Al ejecutar el cmdlet `network watcher packet-capture show` se recupera el estado de una captura de paquetes que se está ejecutando o se ha completado.

```azurecli
azure network watcher packet-capture show -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

El ejemplo siguiente es la salida del cmdlet `network watcher packet-capture show`. El ejemplo siguiente es una vez completada la captura. El valor de PacketCaptureStatus es Stopped, con un valor de StopReason de TimeExceeded. Este valor muestra que la captura de paquetes fue correcta y agotó su tiempo.

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture show command OK
```

## <a name="stop-a-packet-capture"></a>Detención de una captura de paquetes

Al ejecutar el cmdlet `network watcher packet-capture stop` se detiene una sesión si está en curso.

```azurecli
azure network watcher packet-capture stop -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> El cmdlet no devuelve ninguna respuesta cuando se ejecuta en una sesión de captura actualmente en ejecución o en una sesión que ya se ha detenido.

## <a name="delete-a-packet-capture"></a>Eliminación de una captura de paquetes

```azurecli
azure network watcher packet-capture delete -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> Eliminar una captura de paquetes no elimina el archivo en la cuenta de almacenamiento.

## <a name="download-a-packet-capture"></a>Descarga de una captura de paquetes

Una vez finalizada la sesión de captura de paquetes, el archivo de captura se puede cargar en Blob Storage o en un archivo local en la máquina virtual. La ubicación de almacenamiento de la captura de paquetes se define al crear la sesión. Una herramienta práctica para acceder a estos archivos de captura guardados en una cuenta de almacenamiento es el Explorador de Microsoft Azure Storage, que puede descargarse aquí: http://storageexplorer.com/

Si se especifica una cuenta de almacenamiento, los archivos de captura de paquetes se guardan en una cuenta de almacenamiento en la siguiente ubicación:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de cómo automatizar capturas de paquetes con las alertas de máquina virtual, consulte cómo [crear una captura de paquetes desencadenada por alertas](network-watcher-alert-triggered-packet-capture.md).

Para comprobar si se permite cierto tráfico hacia o desde la máquina virtual, vea cómo [consultar IP Flow Verify](network-watcher-check-ip-flow-verify-portal.md).

<!-- Image references -->

