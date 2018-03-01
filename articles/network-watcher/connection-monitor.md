---
title: "Supervisión de conexiones de red con Azure Network Watcher: Azure Portal | Microsoft Docs"
description: Aprenda a supervisar la conectividad de red con Azure Network Watcher mediante Azure Portal.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: beaa458d727a05eccf496933deb3c998828868cd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Supervisión de conexiones de red con Azure Network Watcher mediante Azure Portal

Aprenda a usar un monitor de conexiones para supervisar la conectividad de red entre una máquina virtual de Azure y una dirección IP. La dirección IP puede asignarse a otro recurso de Azure o a un recurso de Internet o a uno local.

## <a name="prerequisites"></a>requisitos previos

Debe cumplir los siguientes requisitos previos antes de completar los pasos descritos en este artículo:

* Una instancia de Network Watcher en la región en la que desea supervisar una conexión. Si no dispone ya de una, puede crearla completando los pasos que se describen en [Creación de una instancia de Azure Network Watcher](network-watcher-create.md).
* Una máquina virtual desde la que realizar la supervisión.
* `AzureNetworkWatcherExtension` instalada en la máquina virtual desde la que desea supervisar una conexión. Para instalar la extensión en una máquina virtual Windows, consulte [Extensión de máquina virtual del agente de Azure Network Watcher para Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), y en una máquina virtual Linux, consulte [Extensión de máquina virtual del agente de Azure Network Watcher para Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure 

Inicie sesión en el [Azure Portal](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Creación de un monitor de conexión

1. En el lado izquierdo del portal, seleccione **Más servicios**.
2. Empiece a escribir *network watcher*. Cuando aparezca la opción **Network Watcher** en los resultados de búsqueda, selecciónela.
3. En **SUPERVISIÓN**, seleccione **Monitor de conexión (versión preliminar)**. Las características de la versión preliminar no tienen el mismo nivel de confiabilidad y de disponibilidad regional que las características de la versión general.
4. Seleccione **+Agregar**.
5. Escriba o seleccione la información adecuada para la conexión que desea supervisar y seleccione **Agregar**. En este ejemplo, se va supervisar una conexión entre las máquinas virtuales *myVmSource* y *myVmDestination* a través del puerto 80.
    
    |  Configuración                                 |  Valor               |
    |  -------------------------------------   |  ------------------- |
    |  NOMBRE                                    |  myConnectionMonitor |
    |  Máquina virtual de origen                  |  myVmSource          |
    |  Puerto de origen                             |                      |
    |  Destino, seleccione una máquina virtual   |  myVmDestination     |
    |  Puerto de destino                        |  80                  |

6. Comienza la supervisión. El monitor de conexión realiza un sondeo cada 60 segundos.
7. El monitor de conexión muestra el tiempo medio del recorrido de ida y vuelta, y el porcentaje de sondeos que produjeron error. Puede ver los datos del monitor en una cuadrícula o un gráfico.

## <a name="next-steps"></a>pasos siguientes

- Aprenda a automatizar capturas de paquetes con las alertas de máquina virtual. Para ello consulte [Creación de una captura de paquetes desencadenada por alertas](network-watcher-alert-triggered-packet-capture.md).

- Determine si se permite cierto tráfico hacia o desde la máquina virtual mediante la [comprobación del flujo de IP](network-watcher-check-ip-flow-verify-portal.md).