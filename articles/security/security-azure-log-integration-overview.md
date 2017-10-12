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
ms.date: 08/10/2017
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 6c3a2ac18fdb7a7a722447af720b9dee28adef08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Introducción a la integración de registro de Microsoft Azure
Aprenda sobre la integración de registro de Azure, sus principales funcionalidades y cómo funciona.

## <a name="overview"></a>Información general

Integración de registro de Azure es una solución gratuita que permite integrar los registros sin procesar de los recursos de Azure en los sistemas locales de administración de eventos e información de seguridad (SIEM).

Integración de registro de Azure recopila eventos de Windows de los registros del Visor de eventos de Windows, los [registros de actividad de Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), las [alertas de Azure Security Center](../security-center/security-center-intro.md) y los [registros de Azure Diagnostics](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) de los recursos de Azure. Esta integración ayuda a la solución SIEM a proporcionar un panel unificado para todos los recursos, tanto locales como en la nube, de forma que puede agregar, correlacionar, analizar y alertar sobre los eventos de seguridad.

>[!NOTE]
En este momento, las únicas nubes compatibles son Azure comercial y Azure Government. No se admiten otras nubes.

![Integración de registro de Azure][1]

## <a name="what-logs-can-i-integrate"></a>¿Qué registros se pueden integrar?
Azure genera gran cantidad de registros para cada servicio. Estos registros representan tres tipos de registros:

* **Registros de control/administración**: ofrecen visibilidad sobre las operaciones CREATE, UPDATE y DELETE de [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Los registros de actividad de Azure son un ejemplo de este tipo de registro.
* **Registros de plano de datos**: ofrecen visibilidad sobre los eventos que surgen cuando se usa un recurso de Azure. Un ejemplo de este tipo de registro son los canales **Sistema**, **Seguridad** y **Aplicación** del Visor de eventos de Windows en una máquina virtual Windows. Otro ejemplo es el registro de diagnóstico configurado a través de Azure Monitor.
* **Eventos procesados**: proporcionan información de alertas y eventos analizados procesada en nombre del usuario. Un ejemplo de este tipo de evento son las alertas de Azure Security Center, donde Azure Security Center ha procesado y analizado la suscripción para proporcionar alertas pertinentes para el estado de seguridad actual.

Integración de registro de Azure admite ArcSight, QRadar y Splunk. En todas las circunstancias, consulte a su proveedor SIEM para evaluar si tienen un conector nativo. En algunos casos, no necesitará utilizar Integración de registro de Azure cuando haya conectores nativos disponibles. Para más información sobre los tipos de registro compatibles, consulte las preguntas frecuentes.

>[!NOTE]
Aunque Integración de registro de Azure es una solución gratuita, se aplicarán costes de Azure Storage derivados del almacenamiento de la información de los archivos de registro.

Puede encontrar ayuda de la comunidad en [Foro MSDN de Azure Log Integration](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). El foro ofrece a la comunidad de AzLog la posibilidad de ayudarse en relación con preguntas, respuestas, consejos y sugerencias sobre cómo sacar el máximo partido de Integración de registro de Azure. Además, el equipo de Azure Log Integration supervisa este foro y le ayudará a cada vez que se pueda.

También puede abrir una [solicitud de soporte técnico](../azure-supportability/how-to-create-azure-support-request.md). Para ello, seleccione **Azure Log Integration** como el servicio para el que está solicitando el soporte técnico.

## <a name="next-steps"></a>Pasos siguientes
En este documento, se ha presentado la integración de registros de Azure. Para más información al respecto y los tipos de registros admitidos, consulte:

* [Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324): Centro de descarga para obtener información, los requisitos del sistema y las instrucciones de instalación de Azure Log Integration.
* [Get started with Azure log integration](security-azure-log-integration-get-started.md) (Introducción a la integración de registros de Azure): este tutorial lo guía a través de la instalación de la integración de registros de Azure y de almacenamiento de Azure WAD, de registros de actividad de Azure, de alertas de Azure Security Center, y de registros de auditoría de Azure Active Directory.
* [Pasos de configuración para soluciones de asociados](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : esta entrada de blog le muestra cómo configurar la integración de registros de Azure para que funcione con soluciones de asociados, como Splunk, HP ArcSight y IBM QRadar. Este blog representa la posición actual sobre la configuración de las soluciones de los asociados. En todos los casos, consulte la documentación de la solución de los asociados en primer lugar.
* [Activity and ASC alerts over syslog to QRadar](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/) (Alertas de ASC y de actividad a través de Syslog para QRadar): en esta publicación de blog se proporcionan los pasos para enviar alertas de actividad y de Azure Security Center a través de Syslog para QRadar.
* [Preguntas más frecuentes sobre la integración de registro de Azure (P+F)](security-azure-log-integration-faq.md). Este artículo de preguntas más frecuentes responde a preguntas sobre la integración de registro de Azure.
* [Integración de las alertas de Azure Security Center con la integración de registro de Azure](../security-center/security-center-integrating-alerts-with-log-integration.md): en este documento se explica cómo sincronizar las alertas de Azure Security Center con Azure Log Integration.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
