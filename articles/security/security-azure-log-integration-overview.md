---
title: "Integración de registros de recursos de Azure en sistemas SIEM | Microsoft Docs"
description: "Aprenda sobre la integración de registro de Azure, sus principales funcionalidades y cómo funciona."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: e427e2f7dafb6db9bc5c15d841fbbf83a02fc0e1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Introducción a la integración de registro de Microsoft Azure

La integración de registros de Azure le permite integrar los registros sin procesar de los recursos de Azure en los sistemas locales de Administración de eventos e información de seguridad (SIEM) en el caso de que un conector con [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) no esté aún disponible en el proveedor SIEM.

El método preferido para la integración de registros de Azure es mediante el conector de Azure Monitor del proveedor SIEM y siguiendo estas [instrucciones](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). De todas formas, si su proveedor SIEM no proporciona un conector a Azure Monitor, es posible que pueda usar Azure Log Integration como solución temporal (si el SIEM es compatible con Azure Log Integration) hasta que esté disponible un conector de este tipo.

>[!IMPORTANT]
>Si su interés principal está en la recopilación de registros de máquina virtual, la mayoría de los proveedores de SIEM incluyen este servicio en su solución. El conector del proveedor de SIEM debe usarse siempre como alternativa preferida.

Integración de registro de Azure recopila eventos de Windows de los registros del Visor de eventos de Windows, los [registros de actividad de Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), las [alertas de Azure Security Center](../security-center/security-center-intro.md) y los [registros de Azure Diagnostics](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) de los recursos de Azure. Esta integración ayuda a la solución SIEM a proporcionar un panel unificado para todos los recursos, tanto locales como en la nube, de forma que puede agregar, correlacionar, analizar y alertar sobre los eventos de seguridad.

>[!NOTE]
En este momento, las únicas nubes compatibles son Azure comercial y Azure Government. No se admiten otras nubes.

![Integración de registro de Azure][1]

## <a name="what-logs-can-i-integrate"></a>¿Qué registros se pueden integrar?

Azure genera gran cantidad de registros para cada servicio. Estos registros representan tres tipos de registros:

* **Registros de control/administración**: ofrecen visibilidad sobre las operaciones CREATE, UPDATE y DELETE de [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Los registros de actividad de Azure son un ejemplo de este tipo de registro.
* **Registros de plano de datos**: ofrecen visibilidad sobre los eventos que surgen cuando se usa un recurso de Azure. Un ejemplo de este tipo de registro son los canales **Sistema**, **Seguridad** y **Aplicación** del Visor de eventos de Windows en una máquina virtual Windows. Otro ejemplo es el registro de diagnóstico configurado a través de Azure Monitor.
* **Eventos procesados**: proporcionan información de alertas y eventos analizados procesada en nombre del usuario. Un ejemplo de este tipo de evento son las alertas de Azure Security Center, donde Azure Security Center ha procesado y analizado la suscripción para proporcionar alertas pertinentes para el estado de seguridad actual.

Integración de registro de Azure admite ArcSight, QRadar y Splunk. En todas las circunstancias, consulte a su proveedor SIEM para determinar si tienen un conector nativo. Cuando haya conectores nativos disponibles no debe usar Azure Log Integration.

Si no hay ninguna otra opción disponible, puede considerar Azure Log Integration. En la tabla siguiente incluye nuestras recomendaciones.

|**SIEM** | **Cliente ya está usando el integrador de Log** | **Cliente investigando opciones de integración de SIEM**|
|---------|--------------------------|-------------------------------------------|
|**SPLUNK** | Inicie la migración al [Complemento Azure Monitor para Splunk](https://splunkbase.splunk.com/app/3534/) | Use [conector SPLUNK](https://splunkbase.splunk.com/app/3534/) |
|**IBM QRADAR** | Migre o empiece a usar el conector de QRadar documentado al final de http://aka.ms/azmoneventhub | Use el conector de QRadar documentado al final de http://aka.ms/azmoneventhub  |
|**ARCSIGHT** | Siga usando el integrador de Log hasta que haya disponible un conector, a continuación, migre a la solución basada en el conector.  | Considere usar Azure Log Analytics como una alternativa. No se incorpore a Azure Log Integration, a menos que esté dispuesto a pasar por el proceso de migración cuando el conector esté disponible. |

>[!NOTE]
>Aunque Integración de registro de Azure es una solución gratuita, se aplicarán costes de Azure Storage derivados del almacenamiento de la información de los archivos de registro.

Si necesita asistencia, puede abrir una [solicitud de soporte técnico](../azure-supportability/how-to-create-azure-support-request.md). Para ello, seleccione **Azure Log Integration** como el servicio para el que está solicitando el soporte técnico.

## <a name="next-steps"></a>pasos siguientes

En este documento, se ha presentado la integración de registros de Azure. Para más información al respecto y los tipos de registros admitidos, consulte:

* [Get started with Azure log integration](security-azure-log-integration-get-started.md) (Introducción a la integración de registros de Azure): este tutorial lo guía a través de la instalación de la integración de registros de Azure y de almacenamiento de Azure WAD, de registros de actividad de Azure, de alertas de Azure Security Center, y de registros de auditoría de Azure Active Directory.
* [Preguntas más frecuentes sobre la integración de registro de Azure (P+F)](security-azure-log-integration-faq.md). Este artículo de preguntas más frecuentes responde a preguntas sobre la integración de registro de Azure.
* [Flujo de datos de supervisión de Azure a un centro de eventos para que lo consuma una herramienta externa](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
