---
title: Migración de Azure IoT Hub a la configuración de diagnóstico | Microsoft Docs
description: Se describe cómo actualizar Azure IoT Hub para usar la configuración de diagnóstico de Azure en lugar de la supervisión de operaciones para controlar el estado de las operaciones de IoT Hub en tiempo real.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: kgremban
ms.openlocfilehash: a46f6798a71c93ed769ae68877e72801d45b74a4
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migración de la supervisión de operaciones de IoT Hub a la configuración de diagnóstico

Los clientes que usan la [supervisión de operaciones][lnk-opsmon] para realizar un seguimiento del estado de las operaciones en IoT Hub pueden migrar dicho flujo de trabajo a la [configuración de diagnóstico de Azure][lnk-diagnostics-settings], una característica de Azure Monitor. La configuración de diagnóstico proporciona información de diagnóstico a nivel de recurso para muchos servicios de Azure.

La funcionalidad de la supervisión de operaciones de IoT Hub está en desuso y, por tanto, se quitará en el futuro. En este artículo se explican los pasos para migrar las cargas de trabajo de la supervisión de operaciones a la configuración de diagnóstico. Para más información sobre la escala de tiempo de desuso, vea [Monitor your Azure IoT solutions with Azure Monitor and Azure Resource Health][lnk-blog-announcement] (Supervisión de las soluciones de Azure IoT con Azure Monitor y Azure Resource Health).

## <a name="update-iot-hub"></a>Actualización de IoT Hub

Para actualizar IoT Hub en Azure Portal, primero active la configuración de diagnóstico y luego desactive la supervisión de operaciones.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Desactivación de la supervisión de operaciones

Una vez que haya probado la nueva configuración de diagnósticos en el flujo de trabajo, puede desactivar la característica de supervisión de operaciones. 

1. En el menú de IoT Hub, seleccione **Supervisión de operaciones**.
1. En cada categoría de supervisión, seleccione **Ninguno**.
1. Guarde los cambios de la supervisión de operaciones.

## <a name="update-applications-that-use-operations-monitoring"></a>Actualización de las aplicaciones que usan la supervisión de operaciones

Los esquemas para la supervisión de operaciones y la configuración de diagnóstico varían ligeramente. Es importante que actualice las aplicaciones que utilizan la supervisión de operaciones actualmente para asignarlas al esquema que la configuración de diagnóstico utiliza. 

Además, la configuración de diagnóstico ofrece el seguimiento de cinco categorías nuevas. Después de actualizar las aplicaciones para el esquema existente, agregue también las nuevas categorías:

- Operaciones gemelas de la nube al dispositivo
- Operaciones gemelas del dispositivo a la nube
- Consultas gemelas
- Operaciones de trabajos
- Métodos directos

Para las estructuras de esquema específicas, vea la [información sobre el esquema de la configuración de diagnóstico][lnk-diagnostics-schema].

## <a name="next-steps"></a>Pasos siguientes

- [Supervisión del mantenimiento de Azure IoT Hub y diagnóstico de problemas rápidamente][lnk-monitor]

[lnk-opsmon]: iot-hub-operations-monitoring.md
[lnk-diagnostics-settings]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[lnk-diagnostics-schema]: iot-hub-monitor-resource-health.md#understand-the-logs
[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-accelerators-with-azure-monitor-and-azure-resource-health
[lnk-monitor]: iot-hub-monitor-resource-health.md
