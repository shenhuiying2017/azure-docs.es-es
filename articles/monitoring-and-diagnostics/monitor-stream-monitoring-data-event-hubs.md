---
title: "Flujo de datos de supervisión de Azure a Event Hubs | Microsoft Docs"
description: "Aprenda sobre el flujo de todos los datos de supervisión de Azure a un centro de eventos para que una herramienta de un asociado de Administración de eventos e información de seguridad o de análisis puedan disponer de ellos."
author: johnkemnetz
manager: robb
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/11/2018
ms.author: johnkem
ms.openlocfilehash: b2813035b4665a36b475e791965d395b84ddb3f1
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>Flujo de datos de supervisión de Azure a un centro de eventos para que lo consuma una herramienta externa

Azure Monitor proporciona una sola canalización para acceder a todos los datos de supervisión del entorno de Azure, lo que permite configurar fácilmente una herramienta de asociado de Administración de eventos e información de seguridad y de supervisión que consuman los datos. Este artículo le guía a través de la configuración de distintos niveles de datos en el entorno de Azure para enviarlos a un único espacio de nombres de Event Hubs o centro de eventos, donde una herramienta externa los pueda recopilar.

## <a name="what-data-can-i-send-into-an-event-hub"></a>¿Qué datos puedo enviar a un centro de eventos? 

En el entorno de Azure hay varios "niveles" de datos de supervisión, cuyo método de acceso varía ligeramente. Normalmente, estos niveles se pueden describir como:

- **Datos de supervisión de aplicaciones:** datos sobre el rendimiento y la funcionalidad del código que ha escrito y que se ejecuta en Azure. El seguimiento del rendimiento, los registros de aplicaciones y la telemetría de usuario son ejemplos de datos de supervisión de aplicaciones. Normalmente se recopilan datos de supervisión de aplicaciones de una de las maneras siguientes:
  - Mediante la instrumentación del código con un SDK como el [SDK de Application Insights](../application-insights/app-insights-overview.md).
  - Mediante la ejecución de un agente de supervisión que escucha los registros de aplicaciones nuevos en la máquina donde se ejecuta la aplicación, como el [agente de Azure Diagnostics para Windows](./azure-diagnostics.md) o el [agente de Azure Diagnostics para Linux](../virtual-machines/linux/diagnostic-extension.md).
- **Datos de supervisión de sistema operativo invitado:** datos sobre el sistema operativo en el que se ejecuta la aplicación. Ejemplos de datos de supervisión de sistema operativo invitado serían los syslog de Linux o los eventos de sistema de Windows. Para recopilar este tipo de datos, debe instalar un agente como el [agente de Azure Diagnostics para Windows](./azure-diagnostics.md) o el [agente de Azure Diagnostics para Linux](../virtual-machines/linux/diagnostic-extension.md).
- **Datos de supervisión de recursos de Azure:** datos acerca del funcionamiento de un recurso de Azure. Para algunos tipos de recursos de Azure, como las máquinas virtuales, hay un sistema operativo invitado y aplicaciones que supervisan el interior de ese servicio de Azure. Para otros recursos de Azure, como los grupos de seguridad de red, los datos de supervisión de recursos son el nivel más alto de datos disponible (porque no hay ningún sistema operativo invitado ni aplicación que se ejecute en esos recursos). Estos datos se pueden recopilar con la [configuración de diagnóstico de recursos](./monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
- **Datos de supervisión de la plataforma Azure:** datos acerca del funcionamiento y la administración de una suscripción o un inquilino de Azure, y datos sobre el estado y el funcionamiento de Azure en sí. El [registro de actividad](./monitoring-overview-activity-logs.md), incluidos los datos de estado del servicio, y las auditorías de Active Directory son ejemplos de datos de supervisión de la plataforma. Estos datos se pueden recopilar también con la configuración de diagnóstico.

Pueden enviarse datos desde cualquier nivel a un centro de eventos, donde pueden extraerse en una herramienta asociada. En las secciones siguientes se describe cómo configurar los datos de cada nivel para el flujo de datos a un centro de eventos. En los pasos se presupone que ya tiene recursos en ese nivel que desea supervisar.

Antes de empezar, debe [crear un espacio de nombres de Event Hubs y un centro de eventos](../event-hubs/event-hubs-create.md). Estos centro de eventos y espacio de nombres son el destino de todos los datos de supervisión.

## <a name="how-do-i-set-up-azure-platform-monitoring-data-to-be-streamed-to-an-event-hub"></a>¿Cómo configuro los datos de supervisión de la plataforma Azure para el flujo a un centro de eventos?

Los datos de supervisión de la plataforma Azure proceden de dos orígenes principales:
1. El [registro de actividad de Azure](./monitoring-overview-activity-logs.md), que contiene las operaciones de creación, actualización y eliminación de Resource Manager, los cambios de [estado del servicio Azure](../service-health/service-health-overview.md) que puede afectar a los recursos de la suscripción, las transiciones de [estado de los recursos](../service-health/resource-health-overview.md) y otros muchos tipos de eventos de nivel de suscripción. [En este artículo se detallan todas las categorías de eventos que aparecen en el registro de actividad de Azure](./monitoring-activity-log-schema.md).
2. [Informes de Azure Active Directory](../active-directory/active-directory-reporting-azure-portal.md), que contienen el historial de inicio de sesión y la traza de auditoría de los cambios realizados en un inquilino determinado. No, aún es posible el flujo datos de Azure Active Directory a un centro de eventos.

### <a name="stream-azure-activity-log-data-into-an-event-hub"></a>Flujo de datos de registro de actividad de Azure a un centro de eventos

Para enviar datos del registro de actividad de Azure a un espacio de nombres de Event Hubs, configure un perfil de registro en la suscripción. [Siga esta guía](./monitoring-stream-activity-logs-event-hubs.md) para configurar un perfil de registro en la suscripción. Haga esto con cada suscripción que desee supervisar.

> [!TIP]
> Un perfil de registro actualmente solo permite seleccionar un espacio de nombres de Event Hubs, en el que se crea un centro de eventos con el nombre "Insights-operational-logs". No, aún es posible especificar un nombre de centro de eventos propio en un perfil de registro.

## <a name="how-do-i-set-up-azure-resource-monitoring-data-to-be-streamed-to-an-event-hub"></a>¿Cómo configuro los datos de supervisión de recursos de Azure para el flujo a un centro de eventos?

Los recursos de Azure emiten dos tipos de datos de supervisión:
1. [Registros de diagnóstico de recursos](./monitoring-overview-of-diagnostic-logs.md)
2. [Métricas](monitoring-overview-metrics.md)

Ambos tipos de datos se envían a un centro de eventos mediante la configuración de diagnóstico de recursos. [Siga esta guía](./monitoring-stream-diagnostic-logs-to-event-hubs.md) para definir una configuración de diagnóstico en un recurso concreto. Establezca una configuración de diagnóstico en cada recurso del que quiera recopilar registros.

> [!TIP]
> Puede usar Azure Policy para asegurarse de que todos los recursos de un ámbito determinado siempre se definen con una configuración de diagnóstico [con el efecto de DeployIfNotExists en la regla de directiva](../azure-policy/policy-definition.md#policy-rule). Hoy en día, DeployIfNotExists solo se admite en las directivas integradas.

## <a name="how-do-i-set-up-guest-os-monitoring-data-to-be-streamed-to-an-event-hub"></a>¿Cómo configuro los datos de supervisión de sistema operativo invitado para el flujo a un centro de eventos?

Debe instalar un agente para enviar los datos de supervisión de sistema operativo invitado a un centro de eventos. Para Windows o Linux, especifique los datos que desea que se envíen al centro de eventos y el centro de eventos donde enviarlos en un archivo de configuración y pase ese archivo de configuración al agente que se ejecuta en la máquina virtual.

### <a name="stream-linux-data-to-an-event-hub"></a>Flujo de datos de Linux a un centro de eventos

Puede utilizarse el [agente de Azure Diagnostics para Linux](../virtual-machines/linux/diagnostic-extension.md) para enviar datos de supervisión de una máquina Linux a un centro de eventos. Esto se hace al agregar el centro de eventos como receptor en el archivo de configuración protegido JSON de LAD. [Consulte este artículo para más información sobre cómo agregar el centro de eventos receptor al agente de Azure Diagnostics para Linux](../virtual-machines/linux/diagnostic-extension.md#protected-settings).

> [!NOTE]
> No se puede establecer el flujo de datos de supervisión de sistema operativo invitado a un centro de eventos en el portal. En su lugar, debe editar manualmente el archivo de configuración.

### <a name="stream-windows-data-to-an-event-hub"></a>Flujo de datos de Windows a un centro de eventos

Puede utilizarse el [agente de Azure Diagnostics para Windows](./azure-diagnostics.md) para enviar datos de supervisión de una máquina Windows a un centro de eventos. Esto se hace al agregar el centro de eventos como receptor en la sección privateConfig del archivo de configuración WAD. [Consulte este artículo para más información sobre cómo agregar el centro de eventos receptor al agente de Azure Diagnostics para Windows](./azure-diagnostics-streaming-event-hubs.md).

> [!NOTE]
> No se puede establecer el flujo de datos de supervisión de sistema operativo invitado a un centro de eventos en el portal. En su lugar, debe editar manualmente el archivo de configuración.

## <a name="how-do-i-set-up-application-monitoring-data-to-be-streamed-to-event-hub"></a>¿Cómo configuro los datos de supervisión de aplicaciones invitado para el flujo a un centro de eventos?

Los datos de supervisión de aplicaciones requieren la instrumentación del código mediante SDK, por lo que no hay una solución general al enrutamiento de datos de supervisión de aplicaciones a un centro de eventos en Azure. Sin embargo, [Azure Application Insights](../application-insights/app-insights-overview.md) es un servicio que puede usarse para recopilar datos de nivel de aplicación de Azure. Si usa Application Insights, puede haber un flujo de datos de supervisión a un centro de eventos al hacer lo siguiente:

1. [Configure la exportación continua](../application-insights/app-insights-export-telemetry.md) de los datos de Application Insights a una cuenta de almacenamiento.

2. Configure una aplicación lógica desencadenada por temporizador que [extraiga los datos de Blob Storage](../connectors/connectors-create-api-azureblobstorage.md#use-an-action) y [los inserte como mensaje en el centro de eventos](../connectors/connectors-create-api-azure-event-hubs.md#send-events-to-your-event-hub-from-your-logic-app).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>¿Qué puedo hacer con los datos de supervisión que se envían a mi centro de eventos?

El enrutamiento de los datos de supervisión a un centro de eventos con Azure Monitor facilita la integración con las herramientas de asociados de Administración de eventos e información de seguridad y supervisión. La mayoría de las herramientas requieren la cadena de conexión al centro de eventos y determinados permisos para la suscripción de Azure para leer datos desde el centro de eventos. Esta es una lista incompleta de herramientas con la integración de Azure Monitor:

* **IBM QRadar**: el módulo específico del dispositivo y el protocolo del centro de eventos de Microsoft Azure están disponibles para descarga en [el sitio web de soporte técnico de IBM](http://www.ibm.com/support). [Más información sobre la integración con Azure aquí](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk** - [El complemento Azure Monitor para Splunk](https://splunkbase.splunk.com/app/3534/) está disponible en Splunkbase y es un proyecto de código abierto. [La documentación se encuentra aquí](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
* **SumoLogic**: las instrucciones sobre cómo configurar SumoLogic para consumir datos de un centro de eventos se encuentran [disponibles aquí](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub).

## <a name="next-steps"></a>Pasos siguientes
* [(Archivado del registro de actividades en una cuenta de almacenamiento)](monitoring-archive-activity-log.md)
* [Lea la información general sobre el registro de actividades de Azure](monitoring-overview-activity-logs.md)
* [Configure una alerta basada en un evento del registro de actividades](insights-auditlog-to-webhook-email.md)

