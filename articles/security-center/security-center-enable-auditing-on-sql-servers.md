<properties
   pageTitle="Habilitación de la auditoría en los servidores SQL Server desde Azure Security Center| Microsoft Azure"
   description="En este documento, mostramos cómo implementar la recomendación de Azure Security Center **Habilitar la auditoría en servidores SQL**."
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

# Habilitación de la auditoría en los servidores SQL Server desde Azure Security Center

Azure Security Center recomienda activar la auditoría en todas las bases de datos de los servidores SQL de Azure si aún no se ha hecho. La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas.

Cuando la haya activado, podrá configurar las opciones de Detección de amenazas y los correos electrónicos donde se recibirán alertas de seguridad. Detección de amenazas detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad a la base de datos. De este modo, podrá detectar posibles amenazas y reaccionar a ellas a medida que se produzcan.

Esta recomendación solo se aplica en el servicio SQL de Azure; no se incluyen las instancias de SQL Server que se ejecutan en las máquinas virtuales de los servicios de infraestructura de Azure (IaaS de Azure).

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure. En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.

## Implementación de la recomendación

1. En la hoja **Recomendaciones**, seleccione **Habilitar la auditoría en servidores SQL**. Se abrirá la hoja **Habilitar la auditoría en servidores SQL**. ![Habilitación de la auditoría en servidores SQL][1]

2. Seleccione un servidor SQL Server en donde habilitar la auditoría. Se abrirá la hoja **Configuración de auditoría**. ![Configuración de auditoría][2]
3. En la hoja **Configuración de auditoría**, seleccione la opción **ACTIVAR** de **Auditoría**. ![Activación de la configuración de auditoría][3]

4. Siga los pasos de [Introducción a la auditoría de bases de datos SQL](../sql-database/sql-database-auditing-get-started.md) para configurar el almacenamiento donde se guardarán los registros de auditoría. La cuenta de almacenamiento de la suscripción en la que se recopilarán datos es la predeterminada.

5. Siga los pasos de [Introducción a Detección de amenazas de Base de datos SQL](../sql-database/sql-database-threat-detection-get-started.md) para activar Detección de amenazas, así como para configurar esta funcionalidad y la lista de correos electrónicos que recibirán alertas de seguridad tras detectarse actividades anómalas.

## Pasos siguientes

En este artículo, mostramos cómo implementar la recomendación Habilitar la auditoría en servidores SQL de Azure Security Center. Para obtener más información sobre cómo proteger bases de datos SQL, consulte los siguientes recursos:

- [Protección de bases de datos SQL](../sql-database/sql-database-security.md)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
- [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que le ayudan a proteger los recursos de Azure.
- [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): sepa cómo administrar alertas de seguridad y responder a estas.
- [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing.png
[3]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png

<!---HONumber=AcomDC_0720_2016-->