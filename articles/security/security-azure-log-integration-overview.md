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
ms.date: 03/03/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 434961f7d25195eee3310461a3a97e03ffdd8917
ms.lasthandoff: 03/15/2017


---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Introducción a la integración de registro de Microsoft Azure
Aprenda sobre la integración de registro de Azure, sus principales funcionalidades y cómo funciona.

## <a name="overview"></a>Información general
Tanto la solución de Plataforma como servicio (PaaS) como la de Infraestructura como servicio (IaaS) hospedadas en Azure generan una gran cantidad de datos en los registros de seguridad. Estos registros contienen información esencial que pueden proporcionar un gran conocimiento sobre las infracciones de directivas, las amenazas internas y externas, los problemas de cumplimiento de las regulaciones y las anomalías de la red, el host y la actividad del usuario.

Integración de registro de Azure es una solución gratuita que permite integrar los registros sin procesar de los recursos de Azure en los sistemas locales de administración de eventos e información de seguridad (SIEM). La integración de registros de Azure recopila diagnósticos de Azure de las máquinas virtuales de Windows *(WAD)*, los registros de actividad de Azure, las alertas de Azure Security Center y los registros del proveedor de recursos de Azure. Esta integración proporciona un panel unificado para todos los recursos, tanto locales como en la nube, de forma que puede agregar, correlacionar, analizar y alertar sobre los eventos de seguridad.

![Integración de registro de Azure][1]

## <a name="what-logs-can-i-integrate"></a>¿Qué registros se pueden integrar?
Azure genera gran cantidad de registros para cada servicio. Estos registros se clasifican en estos tipos principales:

* **Registros de control/administración**, que ofrece visibilidad sobre las operaciones CREATE, UPDATE y DELETE de Azure Resource Manager. Los registros de actividad de Azure son un ejemplo de este tipo de registro.
* **Registros de plano de datos**, que ofrece visibilidad sobre los eventos que surgen cuando se usa un recurso de Azure. Ejemplos de este tipo de registro son los registros de sistema, seguridad y aplicaciones de eventos de Windows en una máquina virtual, así como los registros de diagnóstico que se han configurado mediante Azure Monitor.
* **Eventos procesados**, que ofrece una alerta o evento analizado que se ha procesado en su nombre. Un ejemplo de este tipo son las alertas de Azure Security Center, donde Azure Security Center ha procesado y analizado su suscripción y proporciona alertas de seguridad muy concisas.

La integración de registros de Azure admite actualmente la integración de registros de actividad de Azure, el registro de eventos de Windows de la máquina virtual de Windows en su suscripción de Azure, alertas de Azure Security Center, registros de diagnóstico de Azure así como registros de auditoría de Azure Active Directory.

En la siguiente tabla se explica en detalle la categoría Registro y la integración de SIEM.

| Tipo de registro  |Análisis de registros que admiten JSON (Splunk, ELK)| ArcSight  | QRadar  |   
|---|---|---|---|
|  Registros de auditoría de AAD |  yes | Necesita la creación de un archivo de analizador JSON de FlexConnector. Vea la documentación de ArcSight para obtener más información.  |  Debe crear una extensión del origen del registro. Vea la documentación de QRadar para obtener más información. |  
| Registros de actividad  | Sí  |  Archivo de analizador JSON de FlexConnector disponible en el centro de descarga junto con la descarga de la integración de registros de Azure. |  [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html) (enviar mediante Syslog) |  
| Alertas de ASC  | Sí  |  Necesita la creación de un archivo de analizador JSON de FlexConnector. Vea la documentación de ArcSight para obtener más información. | [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html) (enviar mediante Syslog)   |   
| Registros de diagnóstico (registros de recursos) | Sí | Necesita que el usuario final cree un archivo de analizador JSON de FlexConnector. Vea la documentación de ArcSight para obtener información sobre cómo hacerlo. | Debe crear una extensión del origen del registro. Vea la documentación de QRadar para obtener más información. |
| Registros de VM | Sí mediante eventos reenviados y no mediante JSON | Sí mediante eventos reenviados | Sí mediante eventos reenviados |

Integración de registro de Azure es una solución gratuita: no es necesario pagar por los archivos binarios de Integración de registro de Azure. Sin embargo, hay costos relacionados con el almacenamiento de Azure necesario para la información del archivo de registro.

Si tiene dudas sobre la integración del registro de Azure, envíe un correo electrónico a [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Pasos siguientes
En este documento, se ha presentado la integración de registros de Azure. Para más información al respecto y los tipos de registros admitidos, consulte:

* [Microsoft Azure Log Integration for Azure logs](https://www.microsoft.com/download/details.aspx?id=53324) (Integración de registros de Microsoft Azure para registros de Azure): Centro de descarga para obtener información, los requisitos del sistema y las instrucciones de instalación de la integración de registros de Azure.
* [Get started with Azure log integration](security-azure-log-integration-get-started.md) (Introducción a la integración de registros de Azure): este tutorial lo guía a través de la instalación de la integración de registros de Azure y de almacenamiento de Azure WAD, de registros de actividad de Azure, de alertas de Azure Security Center, y de registros de auditoría de Azure Active Directory.
* [Integrate Diagnostics Logs](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/25/integrate-azure-logs-streamed-to-event-hubs-to-siem/) (Integración de registros de diagnóstico): esta publicación de blog proporciona los pasos para integrar registros de diagnóstico con la integración de registros de Azure.
* [Pasos de configuración para soluciones de asociados](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : esta entrada de blog le muestra cómo configurar la integración de registros de Azure para que funcione con soluciones de asociados, como Splunk, HP ArcSight y IBM QRadar.
* [Activity and ASC alerts over syslog to QRadar](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/) (Alertas de ASC y de actividad a través de Syslog para QRadar): en esta publicación de blog se proporcionan los pasos para enviar alertas de actividad y de Azure Security Center a través de Syslog para QRadar.
* [Preguntas más frecuentes sobre la integración de registro de Azure (P+F)](security-azure-log-integration-faq.md). Este artículo de preguntas más frecuentes responde a preguntas sobre la integración de registro de Azure.
* [Integración de las alertas de Security Center con la integración de registro de Azure (versión preliminar)](../security-center/security-center-integrating-alerts-with-log-integration.md). Este documento le explica cómo sincronizar las alertas de Security Center, además de los eventos de seguridad de máquina virtual recopilados por Diagnósticos de Azure y los registros de auditoría de Azure, con sus análisis de registros o una solución SIEM.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

