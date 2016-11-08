---
title: Introducción a la integración de registro de Microsoft Azure | Microsoft Docs
description: Aprenda sobre la integración de registro de Azure, sus principales funcionalidades y cómo funciona.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear

ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2016
ms.author: TomSh

---
# Introducción a la integración de registro de Microsoft Azure (versión preliminar)
Aprenda sobre la integración de registro de Azure, sus principales funcionalidades y cómo funciona.

## Información general
Tanto la solución de Plataforma como servicio (PaaS) como la de Infraestructura como servicio (IaaS) hospedadas en Azure generan una gran cantidad de datos en los registros de seguridad. Estos registros contienen información esencial que pueden proporcionar un gran conocimiento sobre las infracciones de directivas, las amenazas internas y externas, los problemas de cumplimiento de las regulaciones y las anomalías de la red, el host y la actividad del usuario.

La integración de registros de Azure le permite integrar registros sin procesar de los recursos de Azure en los sistemas locales de administración de eventos e información de seguridad (SIEM). La integración de registros de Azure recopila diagnósticos de Azure de las máquinas virtuales Windows *(WAD)*, así como diagnósticos de soluciones de asociados, como el Firewall de aplicaciones web (WAF). Esta integración proporciona un panel unificado para todos los recursos, tanto locales como en la nube, de forma que puede agregar, correlacionar, analizar y alertar sobre los eventos de seguridad.

![Integración de registro de Azure][1]

## ¿Qué registros se pueden integrar?
Azure genera gran cantidad de registros para cada servicio. Estos registros se clasifican en dos tipos principales:

* **Registros de control/administración**, que ofrecen visibilidad sobre las operaciones CREATE, UPDATE y DELETE de Azure Resource Manager. Los registros de auditoría de Azure son un ejemplo de este tipo de registro.
* **Registros de plano de datos**, que ofrecen visibilidad sobre los eventos que surgen cuando se usa un recurso de Azure. Ejemplos de este tipo de registro son los registros de sistema, seguridad y aplicaciones de eventos de Windows en una máquina virtual.

La integración de registros de Azure admite actualmente la integración de registros de auditoría de Azure, registros de máquinas virtuales y alertas de Azure Security Center.

Si tiene dudas sobre la integración del registro de Azure, envíe un correo electrónico a [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com).

## Pasos siguientes
En este documento, se ha presentado la integración de registros de Azure. Para más información al respecto y los tipos de registros admitidos, consulte:

* [Microsoft Azure Log Integration for Azure logs (Preview)](https://www.microsoft.com/download/details.aspx?id=53324) (Integración de registro de Microsoft Azure para registros de Azure) (versión preliminar): centro de descarga para conocer los detalles, los requisitos del sistema y las instrucciones de instalación de la integración de registro de Azure.
* [Get started with Azure log integration](security-azure-log-integration-get-started.md) (Introducción a la integración de registros de Azure): este tutorial le lleva por la instalación de la integración de registros de Azure y la integración de registros de Almacenamiento de Azure, registros de auditoría de Azure y alertas de Security Center.
* [Pasos de configuración para soluciones de asociados](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): esta entrada de blog le muestra cómo configurar la integración de registros de Azure para que funcione con soluciones de asociados, como Splunk, HP ArcSight y IBM QRadar.
* [Preguntas más frecuentes sobre la integración de registro de Azure (P+F)](security-azure-log-integration-faq.md). Este artículo de preguntas más frecuentes responde a preguntas acerca de la integración de registro de Azure.
* [Integración de las alertas de Security Center con la integración de registro de Azure (versión preliminar)](../security-center/security-center-integrating-alerts-with-log-integration.md). Este documento le explica cómo sincronizar las alertas de Security Center, además de los eventos de seguridad de máquina virtual recopilados por Diagnósticos de Azure y los registros de auditoría de Azure, con sus análisis de registros o una solución SIEM.
* [New features for Azure diagnostics and Azure Audit Logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) (Nuevas características de Diagnósticos de Azure y registros de auditoría de Azure): esta entrada de blog es una introducción a los registros de auditoría de Azure y a otras características que le ayudarán a obtener información sobre las operaciones de los recursos de Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

<!---HONumber=AcomDC_0921_2016-->