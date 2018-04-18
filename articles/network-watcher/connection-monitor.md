---
title: 'Supervisión de conexiones de red con Azure Network Watcher: Azure Portal | Microsoft Docs'
description: Aprenda a supervisar la conectividad de red con Azure Network Watcher mediante Azure Portal.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: b0eb10d373f47191933eb1c3c22b779e9e8d6685
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Supervisión de conexiones de red con Azure Network Watcher mediante Azure Portal

Aprenda a usar un monitor de conexiones para supervisar la conectividad de red entre una máquina virtual de Azure y una dirección IP. El monitor de conexión proporciona supervisión entre el puerto y la dirección IP de origen y destino. El monitor de conexión permite escenarios como la supervisión de la conectividad desde una máquina virtual en una red virtual a una máquina virtual que ejecuta SQL server en la misma red virtual o en una diferente, a través del puerto 1433. El monitor de conexión proporciona la latencia de conexión como una métrica de Azure Monitor, que se registra cada 60 segundos. También proporciona una topología de salto a salto e identifica los problemas de configuración que afectan a la conexión.

## <a name="prerequisites"></a>requisitos previos

Debe cumplir los siguientes requisitos previos antes de completar los pasos descritos en este artículo:

* Una instancia de Network Watcher en la región en la que desea supervisar una conexión. Si no dispone ya de una, puede crearla completando los pasos que se describen en [Creación de una instancia de Azure Network Watcher](network-watcher-create.md).
* Una máquina virtual desde la que se realiza la supervisión. Para más información sobre cómo crear una máquina virtual, consulte el artículo sobre cómo crear una máquina virtual [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
* `AzureNetworkWatcherExtension` instalada en la máquina virtual desde la que desea supervisar una conexión. Para instalar la extensión en una máquina virtual Windows, consulte [Extensión de máquina virtual del agente de Azure Network Watcher para Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), y en una máquina virtual Linux, consulte [Extensión de máquina virtual del agente de Azure Network Watcher para Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). La extensión no es necesaria en el punto de conexión de destino que desea supervisar.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure 

Inicie sesión en el [Azure Portal](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Creación de un monitor de conexión

Los pasos siguientes permiten la supervisión de la conexión a una máquina virtual de destino a través de los puertos 80 y 1433:

1. En el lado izquierdo del portal, seleccione **Todos los servicios**.
2. Empiece a escribir *network watcher* en el cuadro **Filtro**. Cuando aparezca la opción **Network Watcher** en los resultados de búsqueda, selecciónela.
3. En **SUPERVISIÓN**, seleccione **Monitor de conexión**.
4. Seleccione **+Agregar**.
5. Escriba o seleccione la información para la conexión que desea supervisar y seleccione **Agregar**. En el ejemplo que se muestra en la imagen siguiente, la conexión supervisada procede de la máquina virtual *MultiTierApp0* a *Database0* a través del puerto 80:

    ![Adición de un monitor de conexión](./media/connection-monitor/add-connection-monitor.png)

    Comienza la supervisión. El monitor de conexión realiza un sondeo cada 60 segundos.
6. Vuelva a completar el paso 5, especificando las mismas máquinas virtuales de origen y destino, y los siguientes valores:
    
    |Configuración  |Valor          |
    |---------|---------      |
    |NOMBRE     | AppToDB(1433) |
    |Port     | 1433          |

## <a name="view-connection-monitoring"></a>Visualización de un monitor de conexión

1. Complete los pasos del 1 al 3 de [Creación de un monitor de conexión](#create-a-connection-monitor) para ver la supervisión de la conexión.
2. La siguiente imagen muestra detalles de la conexión *AppToDB(80)*. El **Estado** es accesible. La **Vista del gráfico** muestra el **tiempo medio del recorrido de ida y vuelta** y el **porcentaje de sondeos que produjeron error**. El gráfico proporciona información de salto a salto y muestra que no hay problemas que afecten a la disponibilidad de destino.

    ![Vista del gráfico](./media/connection-monitor/view-graph.png)

3. Al visualizar la conexión *AppToDB(1433)*, que se muestra en la siguiente imagen, verá que para las mismas máquinas virtuales de origen y destino, el estado es inaccesible a través del puerto 1433. La **Vista de cuadrícula** en este escenario proporciona información de salto a salto y el problema que afecta a la disponibilidad. En este caso, una regla de NSG está bloqueando todo el tráfico en el puerto 1433 en el segundo salto.

    ![Vista de cuadrícula](./media/connection-monitor/view-grid.png)

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a automatizar capturas de paquetes con las alertas de máquina virtual. Para ello consulte [Creación de una captura de paquetes desencadenada por alertas](network-watcher-alert-triggered-packet-capture.md).
- Determine si se permite cierto tráfico hacia o desde la máquina virtual mediante la [comprobación del flujo de IP](network-watcher-check-ip-flow-verify-portal.md).