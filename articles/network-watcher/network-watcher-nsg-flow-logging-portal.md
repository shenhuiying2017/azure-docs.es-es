---
title: "Administración de registros de flujo de grupos de seguridad de red en Azure Network Watcher | Microsoft Docs"
description: "En esta página se explica cómo administrar registros de flujo de grupos de seguridad de red en Azure Network Watcher."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: d2a65104743d9497debdc85c134fd1a06114c514
ms.openlocfilehash: 18227576b14fdf4bb8aa11a673f33075c3a380a8
ms.lasthandoff: 02/23/2017


---

# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Administración de registros de flujo de grupos de seguridad de red en Azure Portal

> [!div class="op_single_selector"]
> - [Portal de Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI](network-watcher-nsg-flow-logging-cli.md)
> - [API de REST](network-watcher-nsg-flow-logging-rest.md)

Los registros de flujo de grupos de seguridad de red son una característica de Network Watcher que permite ver información acerca del tráfico IP de entrada y de salida en un grupo de seguridad de red. Estos registros de flujo se escriben en formato JSON y muestran los flujos de entrada y salida en función de cada regla, la NIC a la que se aplica el flujo, información de 5-tupla sobre el flujo (IP de origen/destino, puerto de origen/destino, protocolo) y si se permitió o denegó el tráfico.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="before-you-begin"></a>Antes de empezar

En este escenario, se da por hecho que ya ha seguido los pasos descritos en [Create an Azure Network Watcher instance](network-watcher-create.md) (Creación de una instancia de Azure Network Watcher) para crear una instancia de Network Watcher. En este escenario también se da por hecho que existe un grupo de recursos con una máquina virtual válida.

## <a name="enable-flow-logs"></a>Habilitar los registros de flujo

Estos pasos lo guían en el proceso para habilitar registros de flujo en un grupo de seguridad de red.

### <a name="step-1"></a>Paso 1

Navegue a una instancia de Network Watcher y seleccione **Registros de flujo**.

![información general de los registros de flujo][1]

### <a name="step-2"></a>Paso 2

Haga clic en un grupo de seguridad de red para seleccionarlo.

![información general de los registros de flujo][2]

### <a name="step-3"></a>Paso 3 

En la hoja **Configuración de los registros de flujo**, establezca el estado en **On** (Activado) y configure una cuenta de almacenamiento.  Cuando haya terminado, haga clic en **Aceptar** y luego en **Guardar**.

![información general de los registros de flujo][3]

## <a name="download-flow-logs"></a>Descarga de registros de flujo

Los registros de flujo se guardan en una cuenta de almacenamiento. Para ver los registros de flujo debe descargarlos.

### <a name="step-1"></a>Paso 1

Para descargar registros de flujo, haga clic en **Puede descargar los registros de flujo desde cuentas de almacenamiento configuradas**.  Esto le llevará a una vista de la cuenta de almacenamiento donde puede navegar hasta el registro para descargarlo.

![configuración de registros de flujo][4]

### <a name="step-2"></a>Paso 2

Vaya a la cuenta de almacenamiento correcta y luego a **Contenedores** > **insights-log-networksecuritygroupflowevent**.

![configuración de registros de flujo][5]

### <a name="step-3"></a>Paso 3

Explore en profundidad la ubicación del registro de flujo, seleccione el registro de flujo y haga clic en **Descargar**.

![configuración de registros de flujo][6]

Para más información sobre la estructura del registro, vea la [introducción a los registros de flujo de grupos de seguridad de red](network-watcher-nsg-flow-logging-overview.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [visualizar los registros de flujo de NSG con Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
