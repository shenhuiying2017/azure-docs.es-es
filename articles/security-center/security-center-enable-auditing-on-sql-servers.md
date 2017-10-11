---
title: "Habilitación de la auditoría y la detección de amenazas en servidores SQL Server en Azure Security Center| Microsoft Docs"
description: "Este documento muestra cómo implementar la recomendación de Azure Security Center ** habilitar auditoría y amenaza para la detección en servidores SQL **."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 042fca4d-7dab-4172-8614-e8c21ccb4960
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: terrylan
ms.openlocfilehash: 660b537aef8d175a478ff93d60b8391d55fc92ad
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="enable-auditing-and-threat-detection-on-sql-servers-in-azure-security-center"></a>Habilitación de la auditoría y la detección de amenazas en servidores SQL Server en Azure Security Center
Azure Security Center recomienda activar la auditoría y la detección de amenazas en todas las bases de datos de los servidores SQL de Azure, en caso de que no se hayan habilitado aún las auditorías. La auditoría y la detección de amenazas pueden ayudarle a mantener el cumplimiento de normativas, conocer la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o violaciones de la seguridad sospechosas.

Cuando la haya activado, podrá configurar las opciones de Detección de amenazas y los correos electrónicos donde se recibirán alertas de seguridad. Detección de amenazas detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad a la base de datos. De este modo, podrá detectar posibles amenazas y reaccionar a ellas a medida que se produzcan.

Esta recomendación solo se aplica en el servicio Azure SQL; no se incluyen las instancias de SQL Server que se ejecutan en las máquinas virtuales de los servicios de infraestructura de Azure (IaaS de Azure).

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo.  No se trata de una guía paso a paso.
>
>

## <a name="implement-the-recommendation"></a>Implementación de la recomendación
1. En la hoja **Recomendaciones**, seleccione **Enable Auditing & Threat detection on SQL servers** (Habilitar la auditoría y la detección de amenazas en servidores de SQL Server).  Se abrirá la hoja **Enable Auditing & Threat detection on SQL servers** (Habilitar la auditoría y la detección de amenazas en servidores de SQL Server).

   ![Habilitación de la auditoría en servidores SQL][1]
2. Seleccione un servidor de SQL Server para habilitar la auditoría y la detección de amenazas. Se abre la hoja **Auditoría y detección de amenazas**.

3. En la hoja **Auditoría y detección de amenazas**, seleccione **Activar** en **Auditoría**.

   ![Activación de la configuración de auditoría][2]
4. Siga los pasos del artículo sobre [auditoría de bases de datos SQL en Azure Portal](../sql-database/sql-database-auditing-portal.md) para configurar el almacenamiento donde se guardarán los registros de auditoría. La cuenta de almacenamiento de la suscripción en la que se recopilarán datos es la predeterminada.
5. Siga los pasos de [Introducción a Detección de amenazas de SQL Database](../sql-database/sql-database-threat-detection.md) para activar Detección de amenazas, así como para configurar esta funcionalidad y la lista de correos electrónicos que recibirán alertas de seguridad tras detectarse actividades anómalas.

## <a name="see-also"></a>Consulte también
En este artículo, se ha mostrado cómo implementar la recomendación de "habilitar la auditoría y la detección de amenazas en servidores de SQL Server". Para obtener más información sobre cómo proteger SQL Database, consulte los siguientes recursos:

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
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png
