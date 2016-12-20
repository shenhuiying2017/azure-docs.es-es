---
title: "Habilitación de la auditoría en bases de datos SQL en Azure Security Center | Microsoft Docs"
description: "En este documento, mostramos cómo implementar la recomendación **Habilitar la auditoría en bases de datos SQL** de Azure Security Center."
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
ms.date: 11/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0084e4e3ffd7284e237a9ed8544b191e1fb55a53


---
# <a name="enable-auditing-on-sql-databases-in-azure-security-center"></a>Habilitación de la auditoría en bases de datos SQL en Azure Security Center
Azure Security Center recomienda activar la auditoría en todas las bases de datos SQL si aún no se ha hecho. La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas.

Cuando la haya activado, podrá configurar las opciones de Detección de amenazas y los correos electrónicos donde se recibirán alertas de seguridad. Detección de amenazas detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad a la base de datos. De este modo, podrá detectar posibles amenazas y reaccionar a ellas a medida que se produzcan.

Esta recomendación se aplica solo al servicio de SQL de Azure; no incluye al servicio SQL que se ejecuta en las máquinas virtuales.

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo.  No se trata de una guía paso a paso.
> 
> 

## <a name="implement-the-recommendation"></a>Implementación de la recomendación
1. En la hoja **Recomendaciones**, seleccione **Habilitar la auditoría en bases de datos SQL**.  Se abrirá la hoja **Habilitar la auditoría en bases de datos SQL** .
   ![Enable auditing on SQL databases][1]
2. Seleccione una base de datos SQL en donde habilitar la auditoría. Se abre la hoja **Auditoría y detección de amenazas**.
   ![Detección de amenazas y auditoría][2]
3. En la hoja **Auditoría y detección de amenazas**, seleccione **Activar** en **Auditoría**.
   ![Activación de la detección de amenazas y auditoría][3]
4. Siga los pasos de [Introducción a Detección de amenazas de Base de datos SQL](../sql-database/sql-database-threat-detection-get-started.md) para activar Detección de amenazas, así como para configurar esta funcionalidad y la lista de correos electrónicos que recibirán alertas de seguridad tras detectarse actividades anómalas.

## <a name="see-also"></a>Consulte también
En este documento, mostramos cómo implementar la recomendación "Habilitar la auditoría en bases de datos SQL" de Security Center. Para obtener más información sobre cómo proteger bases de datos SQL, consulte los siguientes recursos:

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
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]:./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection.png
[3]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png



<!--HONumber=Nov16_HO3-->


