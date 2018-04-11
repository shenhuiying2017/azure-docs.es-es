---
title: Administración de registros de flujo de grupos de seguridad de red en Azure Network Watcher | Microsoft Docs
description: En esta página se explica cómo administrar registros de flujo de grupos de seguridad de red en Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: cb41781c5ac8fb759cecea01402c08dd716bf7d7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Administración de registros de flujo de grupos de seguridad de red en Azure Portal

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [API de REST](network-watcher-nsg-flow-logging-rest.md)

Los registros de flujo de grupos de seguridad de red son una característica de Network Watcher que permite ver información acerca del tráfico IP de entrada y de salida en un grupo de seguridad de red. Estos registros de flujo se escriben con formato JSON y brinda información importante, la que incluye: 

- Flujos de entrada y de salida por regla.
- La NIC a la que se aplica el flujo.
- Información de 5-tupla sobre el flujo (dirección IP de origen o destino, puerto de origen o destino, protocolo).
- Información sobre si se permitió o denegó el tráfico.

## <a name="before-you-begin"></a>Antes de empezar

Para completar los pasos descritos en este artículo, ya debe tener los siguientes recursos:

- Una instancia de Network Watcher existente. Para crear una instancia de Network Watcher, consulte [Creación de una instancia de Network Watcher](network-watcher-create.md).
- Un grupo de recursos existente con una máquina virtual válida. Si no tiene una máquina virtual, consulte el artículo sobre cómo crear una máquina virtual [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) o [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="register-insights-provider"></a>Registro del proveedor de Insights

Para que el registro del flujo de trabajo funcione correctamente, es necesario registrar el proveedor **Microsoft.Insights**. Para registrar el proveedor, realice los pasos siguientes: 

1. Vaya a **Suscripciones** y, luego, seleccione la suscripción para la que desea habilitar los registros de flujo. 
2. En la hoja **Suscripción**, seleccione **Proveedores de recursos**. 
3. Observe la lista de proveedores y compruebe que el proveedor **microsoft.insights** está registrado. Si no es así, seleccione **Registrarse**.

![Visualización de proveedores][providers]

## <a name="enable-flow-logs"></a>Habilitar los registros de flujo

Estos pasos lo guían en el proceso para habilitar registros de flujo en un grupo de seguridad de red.

### <a name="step-1"></a>Paso 1

Vaya a una instancia de Network Watcher y seleccione **Registros de flujo de NSG**.

![Información general de los registros de flujo][1]

### <a name="step-2"></a>Paso 2

Seleccione un grupo de seguridad de red desde la lista.

![Información general de los registros de flujo][2]

### <a name="step-3"></a>Paso 3 

En la hoja **Configuración de los registros de flujo**, establezca el estado en **On** (Activado) y, luego, configure una cuenta de almacenamiento. Seleccione una cuenta de almacenamiento existente que tenga **todas las redes** (valor predeterminado) seleccionadas en **Firewalls y redes virtuales**, en la **configuración** de la cuenta de almacenamiento. Cuando haya seleccionado una cuenta de almacenamiento, seleccione **Aceptar** y, después, seleccione **Guardar**.

![Información general de los registros de flujo][3]

## <a name="download-flow-logs"></a>Descarga de registros de flujo

Los registros de flujo se guardan en una cuenta de almacenamiento. Descargue los registros de flujo para verlos.

### <a name="step-1"></a>Paso 1

Para descargar registros de flujo, seleccione **Puede descargar los registros de flujo desde cuentas de almacenamiento configuradas**. Este paso lo lleva a una vista de cuenta de almacenamiento en la que puede elegir los registros que se van a descargar.

![Configuración de registros de flujo][4]

### <a name="step-2"></a>Paso 2

Vaya a la cuenta de almacenamiento correcta. Luego, seleccione **Contenedores** > **insights-log-networksecuritygroupflowevent**.

![Configuración de registros de flujo][5]

### <a name="step-3"></a>Paso 3

Vaya a la ubicación del registro de flujo, selecciónelo y, luego, seleccione **Descargar**.

![Configuración de registros de flujo][6]

Para información sobre la estructura del registro, visite la [introducción a los registros de flujo de grupos de seguridad de red](network-watcher-nsg-flow-logging-overview.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [visualizar los registros de flujo de NSG con Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png
