---
title: "Administración de capturas de paquetes con Azure Network Watcher: Azure Portal | Microsoft Docs"
description: "En esta página se explica cómo administrar la característica de captura de paquetes de Network Watcher mediante Azure Portal"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 818f6513625a2677668dd6b6869ef969fe015bf7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Administración de capturas de paquetes con Azure Network Watcher mediante Azure Portal

> [!div class="op_single_selector"]
> - [Portal de Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [API de REST de Azure](network-watcher-packet-capture-manage-rest.md)

La captura de paquetes de Network Watcher permite crear sesiones de captura para realizar el seguimiento del tráfico hacia y desde una máquina virtual. La sesión de captura cuenta con filtros para asegurarse de capturar solo el tráfico que se desea. La captura de paquetes ayuda a diagnosticar anomalías de la red, tanto de forma activa como reactiva. Otros usos son la recopilación de estadísticas de red, la obtención de información sobre las intrusiones de red y la depuración de las comunicaciones cliente-servidor, entre otros. Esta funcionalidad permite desencadenar capturas de paquetes de forma remota, lo que reduce la carga de tener que ejecutar una captura de paquetes manualmente y en el equipo deseado, y permite ahorrar tiempo.

Este artículo le guiará por las diferentes tareas de administración que están actualmente disponibles para la captura de paquetes.

- [**Inicio de una captura de paquetes**](#start-a-packet-capture)
- [**Detención de una captura de paquetes**](#stop-a-packet-capture)
- [**Eliminación de una captura de paquetes**](#delete-a-packet-capture)
- [**Descarga de una captura de paquetes**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se da por hecho que tiene los siguientes recursos:

- Una instancia de Network Watcher en la región donde desea crear una captura de paquetes
- Una máquina virtual con la extensión de captura de paquetes habilitada.

> [!IMPORTANT]
> La captura de paquetes requiere una extensión de máquina virtual `AzureNetworkWatcherExtension`. Para instalar la extensión en una máquina virtual Windows, consulte [Extensión de máquina virtual del agente de Azure Network Watcher para Windows](../virtual-machines/windows/extensions-nwa.md), y en una máquina virtual con Linux, consulte [Extensión de máquina virtual del agente de Azure Network Watcher para Linux](../virtual-machines/linux/extensions-nwa.md).

### <a name="packet-capture-agent-extension-through-the-portal"></a>Extensión del agente de captura de paquetes a través del portal

Para instalar el agente de captura de paquetes de máquina virtual a través del portal, vaya a la máquina virtual, haga clic en **Extensiones** > **Agregar** y busque **Agente para Windows de Network Watcher**

![Vista del agente][agent]

## <a name="packet-capture-overview"></a>Introducción a la captura de paquetes

Navegue hasta [Azure Portal](https://portal.azure.com) y haga clic en **Redes** > **Network Watcher** > **Captura de paquetes**

La página de información general muestra una lista de todos los paquetes que existen independientemente de su estado.

> [!NOTE]
> La captura de paquetes requiere conectividad a la cuenta de almacenamiento a través del puerto 443.

![Pantalla de introducción a la captura de paquetes][1]

## <a name="start-a-packet-capture"></a>Inicio de una captura de paquetes

Haga clic en **Agregar** para crear una captura de paquetes.

Las propiedades que se pueden definir en una captura de paquetes son:

**Configuración principal**

- **Suscripción**: este valor es la suscripción que se utiliza, se necesita una instancia de Network Watcher en cada suscripción.
- **Grupo de recursos**: el grupo de recursos de destino de la máquina virtual.
- **Máquina virtual de destino**: la máquina virtual que está ejecutando la captura de paquetes.
- **Nombre de la captura de paquetes**: este valor es el nombre de la captura de paquetes.

**Configuración de captura**

- **Cuenta de almacenamiento**: determina si la captura de paquetes se guarda en una cuenta de almacenamiento.
- **Archivo**: determina si una captura de paquetes se guarda localmente en la máquina virtual.
- **Cuentas de almacenamiento**: la cuenta de almacenamiento seleccionada para guardar la captura de paquetes. La ubicación predeterminada es https://{nombre de la cuenta de almacenamiento}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscription id}/resourcegroups/{nombre del grupo de recursos}/providers/microsoft.compute/virtualmachines/{nombre de la máquina virtual}/{YY}/{MM}/{DD}/packetcapture_{HH}_{MM}_{SS}_{XXX}.cap. (Solo se habilita si está seleccionada la opción **Almacenamiento**)
- **Ruta de acceso de archivo local**: la ruta de acceso local en una máquina virtual para guardar la captura de paquetes. (Solo se habilita si está seleccionada la opción **Archivo**). Se tiene que proporcionar una ruta de acceso válida
- **Número máximo de bytes por paquete**: el número de bytes de cada paquete que se captura, si se deja en blanco se capturan todos los bytes.
- **Número máximo de bytes por sesión**: número total de bytes que se capturan, una vez que se alcanza este valor la captura de paquetes se detiene.
- **Límite de tiempo (segundos)**: establece un límite de tiempo para detener la captura del paquete. El valor predeterminado es 18 000 segundos.

> [!NOTE]
> Actualmente las cuentas de Premium Storage no se admiten para almacenar paquetes de captura.

**Filtrado (opcional)**

- **Protocolo**: el protocolo para filtrar la captura de paquetes. Los valores disponibles son TCP, UDP y Any (cualquiera).
- **Dirección IP local**: este valor filtra la captura de paquetes a los paquetes en los que la dirección IP local coincide con este valor de filtro.
- **Puerto local**: este valor filtra la captura de paquetes a los paquetes en los que el puerto local coincide con este valor de filtro.
- **Dirección IP remota**: este valor filtra la captura de paquetes a los paquetes en los que la dirección IP remota coincide con este valor de filtro.
- **Puerto remoto**: este valor filtra la captura de paquetes a los paquetes en los que el puerto remoto coincide con este valor de filtro.

> [!NOTE]
> Los valores de dirección IP y puerto pueden ser un solo valor, un rango de valores o un conjunto. (es decir, 80-1024 para el puerto) Puede definir tantos filtros como desee.

Una vez que se rellenan los valores, haga clic en **Aceptar** para crear la captura de paquetes.

![Creación de una captura de paquetes][2]

Una vez que haya transcurrido el límite de tiempo establecido en la captura de paquetes, esta se detendrá y se podrá revisar. También puede detener manualmente las sesiones de captura de paquetes.

## <a name="delete-a-packet-capture"></a>Eliminación de una captura de paquetes

En la vista de captura de paquetes, haga clic en el **menú contextual** (...) o haga clic con el botón derecho y haga clic en **Eliminar** para detener la captura de paquetes

![Eliminación de una captura de paquetes][3]

> [!NOTE]
> La eliminación de una captura de paquetes no elimina el archivo en la cuenta de almacenamiento.

Se le pide que confirme que desea eliminar la captura de paquetes, haga clic en **Sí**

![Confirmación][4]

## <a name="stop-a-packet-capture"></a>Detención de una captura de paquetes

En la vista de captura de paquetes, haga clic en el **menú contextual** (...) o haga clic con el botón derecho y haga clic en **Detener** para detener la captura de paquetes

## <a name="download-a-packet-capture"></a>Descarga de una captura de paquetes

Una vez finalizada la sesión de captura de paquetes, el archivo de captura se carga en Blob Storage o en un archivo local en la máquina virtual. La ubicación de almacenamiento de la captura de paquetes se define al crear la sesión. Una herramienta práctica para acceder a estos archivos de captura guardados en una cuenta de almacenamiento es el Explorador de Microsoft Azure Storage, que puede descargarse aquí: http://storageexplorer.com/

Si se especifica una cuenta de almacenamiento, los archivos de captura de paquetes se guardan en una cuenta de almacenamiento en la siguiente ubicación:
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Pasos siguientes

Aprenda a automatizar capturas de paquetes con las alertas de máquina virtual en el artículo sobre cómo [crear una captura de paquetes desencadenada por alertas](network-watcher-alert-triggered-packet-capture.md)

Para comprobar si se permite cierto tráfico hacia o desde la máquina virtual, vea cómo [consultar la Comprobación del flujo de IP](network-watcher-check-ip-flow-verify-portal.md)

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png













