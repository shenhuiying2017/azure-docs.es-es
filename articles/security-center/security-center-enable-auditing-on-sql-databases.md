---
title: "Habilitación de las auditorías y la detección de amenazas en bases de datos de SQL Server en Azure Security Center| Microsoft Docs"
description: "Este documento muestra cómo implementar la recomendación de Azure Security Center ** habilitar la detección de auditoría y de las amenazas en las bases de datos SQL **."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 224b6755-2b36-4ecd-9af8-139a198e0df1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: terrylan
ms.openlocfilehash: 8f4febdaa4497fee0dc690b59cd6eaa415c5e5cf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="enable-auditing-and-threat-detection-on-sql-databases-in-azure-security-center"></a>Habilitación de la auditoría y la detección de amenazas en bases de datos SQL en Azure Security Center
Azure Security Center recomienda activar la auditoría y la detección de amenazas en todas las bases de datos SQL, en caso de que no se hayan habilitado aún las auditorías. La auditoría y la detección de amenazas pueden ayudarle a mantener el cumplimiento de normativas, conocer la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o violaciones de la seguridad sospechosas.

Cuando la haya activado, podrá configurar las opciones de Detección de amenazas y los correos electrónicos donde se recibirán alertas de seguridad. Detección de amenazas detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad a la base de datos. De este modo, podrá detectar posibles amenazas y reaccionar a ellas a medida que se produzcan.

Esta recomendación se aplica solo al servicio de SQL de Azure; no incluye al servicio SQL que se ejecuta en las máquinas virtuales.

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo.  No se trata de una guía paso a paso.
>
>

## <a name="implement-the-recommendation"></a>Implementación de la recomendación
1. En la hoja **Recomendaciones**, seleccione **Enable Auditing & Threat detection on SQL databases** (Habilitar la auditoría y la detección de amenazas en bases de datos SQL).  Se abrirá la hoja **Enable Auditing & Threat detection on SQL databases** (Habilitar la auditoría y la detección de amenazas en bases de datos SQL).

   ![Habilitación de la auditoría en bases de datos SQL][1]
2. Seleccione una base de datos SQL en donde habilitar la auditoría. Se abre la hoja **Auditoría y detección de amenazas**.

3. En la hoja **Auditoría y detección de amenazas**, seleccione **Activar** en **Auditoría**.

   ![Activación de la detección de amenazas y auditoría][2]
4. Siga los pasos de [SQL Database Threat Detection in the Azure portal](../sql-database/sql-database-threat-detection-portal.md) (Detección de amenazas de SQL Database en Azure Portal) para activar Detección de amenazas, así como para configurar esta funcionalidad y la lista de correos electrónicos que recibirán alertas de seguridad tras detectarse actividades anómalas.

## <a name="see-also"></a>Consulte también
En este artículo, se ha mostrado cómo implementar la recomendación de "habilitar la auditoría y la detección de amenazas en bases de datos SQL". Para obtener más información sobre cómo proteger SQL Database, consulte los siguientes recursos:

* [Protección de SQL Database](../sql-database/sql-database-security-overview.md)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que le ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png
