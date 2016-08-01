<properties
   pageTitle="Habilitación de la auditoría en bases de datos SQL en Azure Security Center | Microsoft Azure"
   description="En este documento, mostramos cómo implementar la recomendación **Habilitar la auditoría en bases de datos SQL** de Azure Security Center."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2016"
   ms.author="terrylan"/>

# Habilitación de la auditoría en bases de datos SQL en Azure Security Center

Azure Security Center recomienda activar la auditoría en todas las bases de datos SQL si aún no se ha hecho. La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas.

Cuando la haya activado, podrá configurar las opciones de Detección de amenazas y los correos electrónicos donde se recibirán alertas de seguridad. Detección de amenazas detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad a la base de datos. De este modo, podrá detectar posibles amenazas y reaccionar a ellas a medida que se produzcan.

Esta recomendación se aplica solo al servicio de SQL de Azure; no incluye al servicio SQL que se ejecuta en las máquinas virtuales.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure. En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.

## Implementación de la recomendación

1. En la hoja **Recomendaciones**, seleccione **Habilitar la auditoría en bases de datos SQL**. Se abrirá la hoja **Habilitar la auditoría en bases de datos SQL**. ![Habilitación de la auditoría en bases de datos SQL][1]

2. Seleccione una base de datos SQL en donde habilitar la auditoría. Se abrirá la hoja **Auditoría y detección de amenazas**. ![Detección de amenazas y auditoría][2]
3. En la hoja **Auditoría y detección de amenazas** hoja, seleccione **Activar** en **Auditoría**. ![Activación de la detección de amenazas y auditoría][3]


5. Siga los pasos de [Introducción a Detección de amenazas de Base de datos SQL](../sql-database/sql-database-threat-detection-get-started.md) para activar Detección de amenazas, así como para configurar esta funcionalidad y la lista de correos electrónicos que recibirán alertas de seguridad tras detectarse actividades anómalas.

## Pasos siguientes

En este documento, mostramos cómo implementar la recomendación "Habilitar la auditoría en bases de datos SQL" de Security Center. Para obtener más información sobre cómo proteger bases de datos SQL, consulte los siguientes recursos:

- [Protección de bases de datos SQL](../sql-database/sql-database-security.md)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
- [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que lo ayudan a proteger los recursos de Azure.
- [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection.png
[3]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png

<!---HONumber=AcomDC_0720_2016-->