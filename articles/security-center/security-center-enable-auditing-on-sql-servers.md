---
title: "Habilitación de la auditoría en servidores SQL Server desde Azure Security Center| Microsoft Docs"
description: "En este documento, mostramos cómo implementar la recomendación de Azure Security Center **Habilitar la auditoría en servidores SQL**."
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
ms.date: 11/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 114368ffdf8637a229bd0dfc61c0d568d348e8ee


---
# <a name="enable-auditing-on-sql-servers-in-azure-security-center"></a>Habilitación de la auditoría en los servidores SQL Server desde Azure Security Center
Azure Security Center recomienda activar la auditoría en todas las bases de datos de los servidores SQL de Azure si aún no se ha hecho. La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas.

Cuando la haya activado, podrá configurar las opciones de Detección de amenazas y los correos electrónicos donde se recibirán alertas de seguridad. Detección de amenazas detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad a la base de datos. De este modo, podrá detectar posibles amenazas y reaccionar a ellas a medida que se produzcan.

Esta recomendación solo se aplica en el servicio SQL de Azure; no se incluyen las instancias de SQL Server que se ejecutan en las máquinas virtuales de los servicios de infraestructura de Azure (IaaS de Azure).

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo.  No se trata de una guía paso a paso.
> 
> 

## <a name="implement-the-recommendation"></a>Implementación de la recomendación
1. En la hoja **Recomendaciones**, seleccione **Habilitar la auditoría en servidores SQL**.  Se abrirá la hoja **Habilitar la auditoría en servidores SQL** .
   ![Enable auditing on SQL servers][1]
2. Seleccione un servidor SQL Server en donde habilitar la auditoría. Se abrirá la hoja **Configuración de auditoría** .
   ![Auditing settings][2]
3. En la hoja **Configuración de auditoría**, seleccione la opción **ACTIVAR** de **Auditoría**.
   ![Activación de la configuración de auditoría][3]
4. Siga los pasos de [Introducción a la auditoría de bases de datos SQL](../sql-database/sql-database-auditing-get-started.md) para configurar el almacenamiento donde se guardarán los registros de auditoría. La cuenta de almacenamiento de la suscripción en la que se recopilarán datos es la predeterminada.
5. Siga los pasos de [Introducción a Detección de amenazas de Base de datos SQL](../sql-database/sql-database-threat-detection-get-started.md) para activar Detección de amenazas, así como para configurar esta funcionalidad y la lista de correos electrónicos que recibirán alertas de seguridad tras detectarse actividades anómalas.

## <a name="see-also"></a>Consulte también
En este artículo, mostramos cómo implementar la recomendación Habilitar la auditoría en servidores SQL de Azure Security Center. Para obtener más información sobre cómo proteger bases de datos SQL, consulte los siguientes recursos:

* [Protección de bases de datos SQL](../sql-database/sql-database-security.md)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md) : recomendaciones que le ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md) : obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md) : aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : busque las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]:./media/security-center-enable-auditing-on-sql-server/enable-auditing.png
[3]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png



<!--HONumber=Nov16_HO3-->


