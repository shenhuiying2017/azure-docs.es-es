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
ms.date: 06/01/2017
ms.author: TomSh
ms.custom: azlog
ms.translationtype: Human Translation
ms.sourcegitcommit: 532ff423ff53567b6ce40c0ea7ec09a689cee1e7
ms.openlocfilehash: 1adabc7f249d9b01f3ad1a819f75b2ef47f72998
ms.contentlocale: es-es
ms.lasthandoff: 06/05/2017



---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Introducción a la integración de registro de Microsoft Azure
Aprenda sobre la integración de registro de Azure, sus principales funcionalidades y cómo funciona.

## <a name="overview"></a>Información general

Integración de registro de Azure es una solución gratuita que permite integrar los registros sin procesar de los recursos de Azure en los sistemas locales de administración de eventos e información de seguridad (SIEM).

Azure Log Integration recopila eventos de Windows de los canales del Visor de eventos de Windows, [registros de actividad de Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [alertas de Azure Security Center](../security-center/security-center-intro.md) y [registros de Diagnósticos de Azure Diagnostic](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) de los recursos de Azure. Esta integración ayuda a la solución SIEM a proporcionar un panel unificado para todos los recursos, tanto locales como en la nube, de forma que puede agregar, correlacionar, analizar y alertar sobre los eventos de seguridad.

>[!NOTE]
En este momento, las únicas nubes compatibles con Azure comercial y Azure Government. No se admiten otras nubes en este momento.

![Integración de registro de Azure][1]

## <a name="what-logs-can-i-integrate"></a>¿Qué registros se pueden integrar?
Azure genera gran cantidad de registros para cada servicio. Estos registros representan tres tipos de registros:

* **Registros de control/administración**: ofrecen visibilidad sobre las operaciones CREATE, UPDATE y DELETE de [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Los registros de actividad de Azure son un ejemplo de este tipo de registro.
* **Registros de plano de datos**: ofrecen visibilidad sobre los eventos que surgen cuando se usa un recurso de Azure. Un ejemplo de este tipo de registro son los canales **Sistema**, **Seguridad** y **Aplicación** del Visor de eventos de Windows en una máquina virtual Windows. Otro ejemplo es el registro de diagnóstico configurado a través de Azure Monitor.
* **Eventos procesados**: proporcionan información de alertas y eventos analizados procesada en nombre del usuario. Un ejemplo de este tipo de evento son las alertas de Azure Security Center, donde Azure Security Center ha procesado y analizado la suscripción para proporcionar alertas pertinentes para el estado de seguridad actual.

Azure Log Integration admite actualmente la integración de registros de actividad de Azure, registros de eventos de Windows de las máquinas virtuales Windows en su suscripción de Azure, alertas de Azure Security Center, registros de Diagnósticos de Azure y registros de auditoría de Azure Active Directory.

>[!NOTE]
Aunque Azure Log Integration es una solución gratuita, se aplicarán costes de Azure Storage derivados del almacenamiento de la información de archivos de registro.

En la siguiente tabla se explica en detalle la categoría Registro y la integración de SIEM.

| Tipo de registro  |Análisis de registros que admiten JSON (Splunk, ELK)| ArcSight  | QRadar  |   
|---|---|---|---|
|  Registros de auditoría de AAD |  Sí | Necesita la creación de un archivo de analizador JSON de FlexConnector. Vea la documentación de ArcSight para obtener más información.  |  Debe crear una extensión del origen del registro. Vea la documentación de QRadar para obtener más información. |  
| Registros de actividad  | Sí  |  Archivo de analizador JSON de FlexConnector disponible en el centro de descarga junto con la descarga de la integración de registros de Azure. |  [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html) (enviar mediante Syslog) |  
| Alertas de ASC  | Sí  |  Necesita la creación de un archivo de analizador JSON de FlexConnector. Vea la documentación de ArcSight para obtener más información. | [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html) (enviar mediante Syslog)   |   
| Registros de diagnóstico (registros de recursos) | Sí | Necesita que el usuario final cree un archivo de analizador JSON de FlexConnector. Vea la documentación de ArcSight para obtener información sobre cómo hacerlo. | Debe crear una extensión del origen del registro. Vea la documentación de QRadar para obtener más información. |
| Registros de VM | Sí mediante eventos reenviados y no mediante JSON | Sí mediante eventos reenviados | Sí mediante eventos reenviados |

Para obtener más información sobre los tipos de registro compatibles, visite las [preguntas más frecuentes](security-azure-log-integration-faq.md)


Puede encontrar ayuda de la comunidad en [Foro MSDN de Azure Log Integration](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). El foro ofrece a la comunidad de AzLog la posibilidad de ayudarse en relación con preguntas, respuestas, consejos y sugerencias sobre cómo sacar el máximo partido de Azure Log Integration. Además, el equipo de Azure Log Integration supervisa este foro y le ayudará a cada vez que se pueda.

También puede abrir una [solicitud de soporte técnico](../azure-supportability/how-to-create-azure-support-request.md). Para ello, seleccione **Azure Log Integration** como el servicio para el que está solicitando el soporte técnico.

## <a name="next-steps"></a>Pasos siguientes
En este documento, se ha presentado la integración de registros de Azure. Para más información al respecto y los tipos de registros admitidos, consulte:

* [Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324): Centro de descarga para obtener información, los requisitos del sistema y las instrucciones de instalación de Azure Log Integration.
* [Get started with Azure log integration](security-azure-log-integration-get-started.md) (Introducción a la integración de registros de Azure): este tutorial lo guía a través de la instalación de la integración de registros de Azure y de almacenamiento de Azure WAD, de registros de actividad de Azure, de alertas de Azure Security Center, y de registros de auditoría de Azure Active Directory.
* [Pasos de configuración para soluciones de asociados](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : esta entrada de blog le muestra cómo configurar la integración de registros de Azure para que funcione con soluciones de asociados, como Splunk, HP ArcSight y IBM QRadar.
* [Activity and ASC alerts over syslog to QRadar](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/) (Alertas de ASC y de actividad a través de Syslog para QRadar): en esta publicación de blog se proporcionan los pasos para enviar alertas de actividad y de Azure Security Center a través de Syslog para QRadar.
* [Preguntas más frecuentes sobre la integración de registro de Azure (P+F)](security-azure-log-integration-faq.md). Este artículo de preguntas más frecuentes responde a preguntas sobre la integración de registro de Azure.
* [Integración de las alertas de Azure Security Center con la integración de registro de Azure](../security-center/security-center-integrating-alerts-with-log-integration.md): en este documento se explica cómo sincronizar las alertas de Azure Security Center con Azure Log Integration.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

