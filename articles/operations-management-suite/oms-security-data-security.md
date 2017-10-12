---
title: "Seguridad de datos de la solución Seguridad y auditoría de Operations Management Suite | Microsoft Docs"
description: "En este documento se explica cómo se administran y protegen los datos en la solución Seguridad y auditoría de Operations Management Suite."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 9cdf7deb-2a30-4672-b89f-71179ee8326a
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 3b6327b1f5150f32afd71639f32c55d823f1d1f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Seguridad de datos de la solución Seguridad y auditoría de Operations Management Suite
Para ayudar a los clientes a evitar, detectar y responder a amenazas, la [solución Seguridad y auditoría de Operations Management Suite (OMS)](operations-management-suite-overview.md) recopila y procesa los datos sobre los recursos, incluido lo siguiente:

* Registro de eventos de seguridad
* Eventos de Seguimiento de eventos para Windows (ETW)
* Eventos de auditoría de AppLocker
* Registro de Firewall de Windows
* Eventos de Advanced Threat Analytics
* Resultados de evaluación de la línea base
* Resultados de evaluación de antimalware
* Resultados de la evaluación de la actualización o revisión
* Transmisiones de Syslogs habilitadas explícitamente en el agente

Estamos totalmente comprometidos a proteger la privacidad y la seguridad de estos datos. Microsoft se adhiere a instrucciones estrictas de seguridad y cumplimiento de normas, desde la codificación hasta la operación de un servicio.
En este artículo se explica cómo se administran y protegen los datos en Seguridad y auditoría de OMS.

## <a name="data-sources"></a>Orígenes de datos
La solución Seguridad y auditoría de OMS analiza los datos de sus Virtual Machines y equipos físicos en los que está instalado el agente de OMS. La solución Seguridad y auditoría de OMS puede recopilar información de configuración sobre los eventos de seguridad, como los eventos de Windows, los registros de auditoría, los registros de IIS y los mensajes de syslog. Estos son algunos ejemplos de dichos datos: tipo y versión, procesos en ejecución, nombre de la máquina, direcciones IP, usuario conectado e identificador de inquilino.  

## <a name="data-protection"></a>Protección de datos
**Segregación de datos**: los datos se mantienen separados de forma lógica en cada componente a lo largo de todo el servicio. Todos los datos se etiquetan por organización. Este etiquetado persiste a lo largo del ciclo de vida de los datos y se aplica en cada nivel del servicio. 

**Acceso a datos**: para proporcionar recomendaciones de seguridad e investigar las posibles amenazas de seguridad, el personal de Microsoft puede acceder a la información recopilada o analizada por los servicios. Cumplimos los [términos](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) y la [declaración de privacidad de Microsoft Online Services](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), donde se estipula que Microsoft no utilizará los datos de los clientes ni información derivada de ellos con fines comerciales, publicitarios o similares. Para proporcionar recomendaciones de seguridad e investigar las posibles amenazas de seguridad, el personal de Microsoft puede acceder a la información recopilada o analizada por los servicios. Solo usamos los datos del cliente necesarios para proporcionar los servicios de Azure. El usuario conserva todos los derechos de sus propios datos.

**Uso de datos**: Microsoft utiliza los patrones y la información sobre amenazas vistos en varios inquilinos para mejorar las funcionalidades de detección y prevención; esto se realiza según lo dispuesto en los compromisos de privacidad que se describen en nuestra [declaración de privacidad](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

> [!NOTE]
> La ubicación de los datos se configura en el nivel del área de trabajo de OMS, durante la creación del área de trabajo, que forma parte del proceso de configuración inicial de Seguridad y auditoría de OMS.
> 
> 

## <a name="see-also"></a>Consulte también
En este documento, ha aprendido cómo se administran y protegen los datos en OMS. Para aprender más acerca de la solución Seguridad y auditoría de OMS, consulte:

* [Información general de Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Supervisión de las alertas de seguridad y su respuesta en la solución Security and Audit de Operations Management Suite](oms-security-responding-alerts.md)
* [Supervisión de los recursos en la solución Seguridad y auditoría de Operations Management Suite](oms-security-monitoring-resources.md)

