---
title: "Documentación sobre Azure Operations Management Suite (OMS): tutoriales y referencia de API | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) es la solución de administración de TI basada en la nube de Microsoft que le ayuda a administrar y proteger su infraestructura local y en la nube. Este artículo identifica los distintos servicios que se incluyen en OMS y proporciona vínculos a contenido detallado."
services: operations-management-suite
author: czeumault
manager: carolz
layout: LandingPage
ms.assetid: 
ms.service: operations-management-suite
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
ms.openlocfilehash: 12f959376d4923e4e2481e37108ade632ac14902
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-operations-management-suite-oms"></a>¿Qué es Operations Management Suite (OMS)?
Microsoft Operations Management Suite (OMS) es la solución de administración de TI basada en la nube de Microsoft que le ayuda a administrar y proteger su infraestructura local y en la nube.  Puesto que OMS se implementa como un servicio basado en la nube, puede hacer que funcione rápidamente con una inversión mínima en servicios de infraestructura.  Las características nuevas se entregan automáticamente, lo que le ahorra el mantenimiento continuo y los costos de actualización.

Además de proporcionar servicios valiosos por sí solo, OMS puede integrarse con componentes de System Center, como System Center Operations Manager para ampliar sus inversiones existentes de administración en la nube.  System Center y OMS pueden trabajar juntos para proporcionar una experiencia de administración totalmente híbrida.

Las secciones siguientes proporcionan una descripción de alto nivel de las áreas de valor diferentes de OMS y los servicios que las implementan.  Puede hacer referencia a la arquitectura de OMS para obtener información general de los distintos componentes de OMS antes de revisar la documentación detallada para cada uno.

## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![Insight and Analytics](media/operations-management-suite-overview/icon-insight-analytics.png) Insight and Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) ayuda a recopilar, correlacionar, buscar y actuar en los datos de rendimiento y registro generados por sistemas operativos y aplicaciones. Proporciona una visión operativa en tiempo real mediante el uso de paneles personalizados y de búsqueda para analizar fácilmente millones de registros en todas las cargas de trabajo y los servidores, independientemente de su ubicación física.

Puede agregar fácilmente soluciones a Log Analytics que definan los datos que se vana a recopilar y la lógica para su análisis.  Las soluciones pueden incluir funcionalidad adicional que se entrega automáticamente a los agentes con una configuración mínima o sin configuración.  Además de utilizar las herramientas de análisis proporcionadas por las soluciones individuales, puede realizar búsquedas personalizadas a través de todo el conjunto de datos para correlacionar datos entre sistemas y aplicaciones.  

## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![Automation & Control](media/operations-management-suite-overview/icon-automation-control.png) Automation & Control
Azure Automation automatiza los procesos administrativos con [Runbooks](../automation/automation-runbook-types.md) que se basan en PowerShell y se ejecutan en la nube de Azure.  Los runbooks pueden obtener acceso a cualquier producto o servicio que se puede administrar con PowerShell, incluidos los recursos de otras nubes como Amazon Web Services (AWS).  Los runbooks pueden ejecutarse en un servidor en el centro de datos local para administrar recursos locales.

Azure Automation proporciona administración de configuración con [DSC de PowerShell](../automation/automation-dsc-overview.md).  Puede crear y administrar recursos de DSC hospedados en Azure y aplicarlos a los sistemas de nube y locales para definir y aplicar automáticamente su configuración.

## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![Protección y recuperación](media/operations-management-suite-overview/icon-protection-recovery.png) Protección y recuperación ante desastres
[Azure Site Backup](http://azure.microsoft.com/documentation/services/backup) protege los datos de las aplicaciones y los conserva durante años sin necesidad de realizar ninguna inversión y afrontando unos costes operativos mínimos.  Permite realizar la copia de seguridad a partir de los servidores físicos y virtuales de Windows, además de cargas de trabajo de aplicaciones como SQL Server y SharePoint.  También puede usar esa solución System Center Data Protection Manager (DPM) para replicar los datos protegidos en Azure para obtener redundancia y almacenamiento a largo plazo.

[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) contribuye a su estrategia de continuidad de negocio y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales de Hyper-V locales, máquinas virtuales de VMware y servidores Windows/Linux físicos. Puede replicar las máquinas en un centro de datos secundario o ampliar su centro de datos mediante la replicación en Azure. Site Recovery también proporciona conmutación por error simple y recuperación para cargas de trabajo. Se integra con mecanismos de recuperación ante desastres, como SQL Server AlwaysOn y proporciona planes de recuperación para una conmutación por error sencilla de cargas de trabajo que están organizados en niveles entre varias máquinas.

## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![Seguridad y cumplimiento normativo de OMS](media/operations-management-suite-overview/icon-security-compliance.png) Seguridad y cumplimiento normativo
Seguridad y cumplimiento normativo le ayuda a identificar, evaluar y mitigar los riesgos de seguridad de su infraestructura.  Estas características de OMS se implementan a través de varias soluciones de Log Analytics que analizan los datos de registro y configuración de sistemas de agente que le ayudarán a garantizar la seguridad continua de su entorno.

* La solución [Seguridad y auditoría](oms-security-getting-started.md) recopila y analiza los eventos de seguridad en sistemas administrados para identificar cualquier actividad sospechosa.
* La solución [Antimalware](../log-analytics/log-analytics-malware.md) informa del estado de la protección antimalware en sistemas administrados.  
* La solución Actualizaciones del sistema realiza un análisis de las actualizaciones de seguridad y otras actualizaciones en los sistemas administrados poder identificar fácilmente los sistemas que requieren la aplicación de revisiones.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* Obtenga información sobre [Azure Automation](../automation/automation-intro.md).
* Obtenga información sobre [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
* Obtenga información sobre [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).

