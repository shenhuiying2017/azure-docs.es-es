---
title: Servicios Azure Security Center y Azure SQL Database | Microsoft Docs
description: "En este artículo se explica cómo Security Center puede ayudarle a proteger bases de datos en Azure SQL Database."
services: sql-database
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 46dd298a5664d914e55d45c5b7599d5983287476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Servicios Azure Security Center y Azure SQL Database
[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) ayuda a evita y a detectar las amenazas, además de a responder a ellas. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones de Azure, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

En este artículo se explica cómo Security Center puede ayudarlo a proteger bases de datos en Azure SQL Database.

## <a name="why-use-security-center"></a>Razones para usar Security Center
Security Center ayuda a proteger los datos de SQL Database ofreciendo visibilidad de la seguridad en todos los servidores y bases de datos. Con Security Center puede realizar estas tareas:

* Definir las directivas de auditoría y cifrado de SQL Database
* Supervisar la seguridad de los recursos de SQL Database de todas sus suscripciones
* Identificar y corregir problemas de seguridad rápidamente
* Integrar las alertas de [detección de amenazas de Azure SQL Database](../sql-database/sql-database-threat-detection.md)

Además de ayudarlo a proteger los recursos de SQL Database, Security Center también proporciona funcionalidades de administración y supervisión de seguridad de máquinas virtuales de Azure, Cloud Services, App Services, redes virtuales y mucho más. Obtenga más información sobre Security Center [aquí](security-center-intro.md).

## <a name="prerequisites"></a>Requisitos previos
Para empezar a trabajar con el Centro de seguridad, debe disponer de una suscripción a Microsoft Azure. El nivel Gratis de Security Center se habilita con su suscripción. Para obtener más información sobre los niveles Gratis y Estándar de Security Center, consulte [Centro de seguridad Precios](https://azure.microsoft.com/pricing/details/security-center/).

El Centro de seguridad admite el acceso basado en rol. Para obtener más información sobre el control de acceso basado en rol (RBAC) de Azure, consulte [Control de acceso basado en roles de Azure Active Directory](../active-directory/role-based-access-control-configure.md). Las P+F de Security Center proporcionan información sobre [cómo se controlan los permisos en Security Center](security-center-faq.md#permissions).

## <a name="access-security-center"></a>Acceso al Centro de seguridad
Se accede al Centro de seguridad desde el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/). [Inicie sesión en el portal](https://portal.azure.com/) y seleccione la **opción de Security Center**.

![Opción de Security Center][1]

Se abrirá la hoja **Security Center**.
![Hoja Security Center][2]

## <a name="set-security-policy"></a>Establecimiento de directivas de seguridad
Las directivas de seguridad definen el conjunto de controles recomendados para los recursos de la suscripción o el grupo de recursos especificados. En Security Center, el usuario define directivas para sus suscripciones o grupos de recursos de acuerdo con las necesidades de seguridad de la compañía y el tipo de aplicaciones o la confidencialidad de los datos de cada suscripción.

Puede establecer una directiva para mostrar recomendaciones de cifrado de datos transparente (TDE) y de auditoría de SQL.

* Al activar la **detección de amenazas y auditoría de SQL**, Security Center recomienda que la auditoría del acceso a Azure Database esté habilitada para fines de cumplimiento, detección avanzada e investigación.
* Al activar el **cifrado de datos transparente de SQL**, Security Center recomienda que el cifrado en reposo se habilite para Azure SQL Database, las copias de seguridad asociadas y los archivos de registro de transacciones.

Para establecer una directiva de seguridad, haga clic en el icono de **Directiva** de la hoja Security Center. En la hoja **Directiva de seguridad**, seleccione la suscripción en la que quiera habilitar la directiva de seguridad. Seleccione **Directiva de prevención** y **active** las recomendaciones de seguridad que quiera usar en esta suscripción.
![Directiva de seguridad][3]

Para obtener más información, consulte [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md).

## <a name="manage-security-recommendation"></a>Administración de recomendaciones de seguridad
El Centro de seguridad analiza periódicamente el estado de seguridad de los recursos de Azure. Cuando el Centro de seguridad identifica vulnerabilidades de seguridad potenciales, crea recomendaciones. Las recomendaciones le guían en el proceso de configuración de los controles necesarios.

Después de establecer una directiva de seguridad, Security Center analiza el estado de seguridad de los recursos para identificar posibles vulnerabilidades. Las recomendaciones aparecen en un formato de tabla, donde cada línea representa una recomendación determinada. Utilice la tabla siguiente como referencia para entender las recomendaciones que hay disponibles para Azure SQL Database y lo que hacen cada una de ellas si se aplican. Al seleccionar una recomendación, se le redirigirá a un artículo donde se explica cómo implementar la recomendación en Security Center.

| Recomendación | Description |
| --- | --- |
| [Habilitar la auditoría y la detección de amenazas en los servidores SQL](security-center-enable-auditing-on-sql-servers.md) |Recomienda activar la detección de amenazas y la auditoría en los servidores de SQL Database. (Solo disponible para el servicio SQL Database. Se excluyen las instancias de Microsoft SQL Server que se ejecutan en las máquinas virtuales). |
| [Habilitar la auditoría y la detección de amenazas en las bases de datos SQL](security-center-enable-auditing-on-sql-databases.md) |Recomienda activar la detección de amenazas y la auditoría en las bases de datos de SQL Database. (Solo disponible para el servicio SQL Database. Se excluyen las instancias de Microsoft SQL Server que se ejecutan en las máquinas virtuales). |
| [Habilitar el cifrado de datos transparente](security-center-enable-transparent-data-encryption.md) |Recomienda habilitar el cifrado en las bases de datos SQL (solo disponible para el servicio SQL Database). |

Si quiere ver recomendaciones para los recursos de Azure, seleccione el icono de **Recomendaciones** de la hoja Security Center. En la hoja **Recomendaciones**, haga clic en una recomendación para ver la información. En este ejemplo, vamos a seleccionar la recomendación de **habilitar la auditoría y la detección de amenazas en los servidores SQL**.

![Recomendaciones][4]

Tal y como se muestra a continuación, Security Center muestra los servidores SQL en los que no están habilitadas la auditoría y la detección de amenazas. Cuando haya activado la auditoría, podrá configurar las opciones de Detección de amenazas y los correos electrónicos donde se recibirán alertas de seguridad. Detección de amenazas avisa cuando se detectan actividades anómalas en la base de datos que indican posibles amenazas de seguridad a la base de datos. Las alertas se muestran en el panel de Security Center.
![Detección de amenazas y auditoría][5]

Siga los pasos de [SQL Database Threat Detection in the Azure portal](../sql-database/sql-database-threat-detection-portal.md) (Detección de amenazas de SQL Database en Azure Portal)/// para activar Detección de amenazas, así como para configurar esta funcionalidad y la lista de correos electrónicos que recibirán alertas de seguridad tras detectarse actividades anómalas.

Para obtener más información sobre las recomendaciones, consulte el artículo sobre cómo [administrar recomendaciones de seguridad](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Supervisión del estado de seguridad
Después de habilitar las [directivas de seguridad](security-center-policies.md) para los recursos de una suscripción, Security Center analizará la seguridad de los recursos para identificar vulnerabilidades potenciales.  Puede consultar el estado de seguridad de sus recursos en el icono de **Estado de seguridad del recurso**. Al hacer clic en la opción **Datos** del icono de **Estado de seguridad del recurso**, se abrirá la hoja de **recursos de datos** con recomendaciones de SQL para problemas como que no estén habilitados la auditoría ni el cifrado de datos transparente. También tiene las recomendaciones sobre el estado general de la base de datos.
![Estado de seguridad de los recursos][6]

Para obtener más información, consulte el artículo sobre cómo [supervisar el estado de seguridad](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Administración de las alertas de seguridad y respuesta a ellas
Security Center recopila, analiza e integra automáticamente los datos de registro de la característica [Detección de amenazas de SQL de Azure](../sql-database/sql-database-threat-detection.md), así como otros recursos de Azure, para detectar amenazas reales y reducir los falsos positivos. En Security Center, se muestra una lista de alertas de seguridad prioritarias, junto con la información que necesita para investigar rápidamente y recomendaciones para corregir un ataque.

Para ver las alertas, seleccione el icono de **Alertas de seguridad** de la hoja Security Center. En la hoja **Alertas de seguridad**, seleccione una alerta para ver más información sobre los eventos que la desencadenaron y, si existen, los pasos que debe seguir para corregir un ataque. En este ejemplo, vamos a seleccionar el **posible ataque por inyección de código SQL**.
![Alertas de seguridad][7]

Tal y como se muestra a continuación, Security Center proporciona más información sobre lo que desencadenó la alerta, el recurso de destino, la dirección IP de origen si corresponde, y recomendaciones sobre cómo corregirla.
![Posible ataque por inyección de código SQL][8]

Para obtener más información, consulte el artículo sobre cómo [administrar las alertas de seguridad y responder a ellas](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Pasos siguientes
* [Preguntas más frecuentes sobre Security Center](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Guía de operaciones y planeación de Security Center](security-center-planning-and-operations-guide.md): siga una serie de pasos y tareas para optimizar el uso de Security Center en función de los requisitos de seguridad y el modelo de administración en la nube de su organización.
* [Seguridad de datos de Security Center](security-center-data-security.md): obtenga información sobre cómo Security Center recopila y procesa datos de los recursos de Azure, entre los que se incluyen la información de configuración, los metadatos, los registros de eventos y los archivos de volcado de memoria, entre otros.
* [Control de incidentes de seguridad](security-center-incident.md): aprenda a usar la funcionalidad de alertas de seguridad de Security Center para ayudarlo a controlar los incidentes de seguridad.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
