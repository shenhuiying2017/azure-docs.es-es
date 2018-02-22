---
title: "Exportación de datos de seguridad de Azure a SIEM: configuración de canalización [versión preliminar] | Microsoft Docs"
description: "En este artículo se documenta el proceso de obtención de registros de Azure Security Center a una solución SIEM."
services: security-center
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2018
ms.author: barclayn
ms.openlocfilehash: 7a0a72a25010952f13eb190f0e0a1a65cc6d42d3
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="azure-security-data-export-to-siem--pipeline-configuration-preview"></a>Exportación de datos de seguridad de Azure a SIEM: configuración de canalización [versión preliminar]

En este documento se describe el procedimiento para exportar datos de seguridad de Azure Security Center a una solución SIEM.

Los eventos procesados generados por Azure Security Center se publican en el [registro de actividad](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) de Azure, uno de los tipos de registros disponibles a través de Azure Monitor. Azure Monitor ofrece una canalización consolidada para el enrutamiento de cualquiera de los datos supervisados en una herramienta SIEM. Esto se realiza mediante el streaming de esos datos a un centro de eventos, desde el que se pueden extraer en una herramienta de asociado.

Esta canalización usa la [canalización única de supervisión de Azure](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) para obtener acceso a los datos de supervisión de su entorno de Azure. Esto le permite configurar fácilmente las herramientas de supervisión y SIEM para consumir los datos.

En las secciones siguientes se describe cómo configurar los datos para que se transmitan en secuencias a un centro de eventos. En estos pasos se supone que ya tiene Azure Security Center configurado en su suscripción a Azure.

Información general de alto nivel

![Información general de alto nivel](media/security-center-export-data-to-siem/overview.png)

## <a name="what-is-the-azure-security-data-exposed-to-siem"></a>¿Qué datos de seguridad de Azure se exponen a SIEM?

En esta versión preliminar, se exponen las [alertas de seguridad.](../security-center/security-center-managing-and-responding-alerts.md) En las próximas versiones, ampliaremos el conjunto de datos con recomendaciones de seguridad.

## <a name="how-to-setup-the-pipeline"></a>¿Cómo se configura la canalización? 

### <a name="create-an-event-hub"></a>Creación de un Centro de eventos 

Antes de empezar, debe [crear un espacio de nombres de Event Hubs](../event-hubs/event-hubs-create.md). El espacio de nombres y el centro de eventos son el destino de todos los datos de supervisión.

### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Transmisión del registro de actividad de Azure a Event Hubs

Consulte el artículo siguiente: [Transmisión del registro de actividad a Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md).

### <a name="install-a-partner-siem-connector"></a>Instalación de un conector SIEM de asociado 

El enrutamiento de los datos de supervisión a un centro de eventos con Azure Monitor facilita la integración con las herramientas de asociados de SIEM y de supervisión.

Consulte el vínculo siguiente para ver la lista de [SIEM compatibles](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

## <a name="example-for-querying-data"></a>Ejemplo de consulta de datos 

A continuación, hay un par de consultas de Splunk que puede usar para extraer datos de alertas:

| **Descripción de la consulta**                                | **Consultar**                                                                                                                              |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Todas las alertas                                              | index=main Microsoft.Security/locations/alerts                                                                                         |
| Resumen del recuento de operaciones por su nombre             | index=main sourcetype="amal:security" \| table operationName \| stats count by operationName                                |
| Obtención de información de las alertas: hora, nombre, estado, identificador y suscripción | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>pasos siguientes

- [SIEM compatibles](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)
- [Transmisión de registros de actividad a Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
- [Alertas de seguridad.](../security-center/security-center-managing-and-responding-alerts.md)