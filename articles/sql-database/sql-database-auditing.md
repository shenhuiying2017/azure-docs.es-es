---
title: "Azure SQL Database: Auditorías | Microsoft Docs"
description: "Las auditorías de Azure SQL Database realizan un seguimiento de eventos de bases de datos y escribe los eventos en un registro de auditoría de la cuenta de Azure Storage."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 8c9da10e6ebc67c464bf7b5e569cb2113a082d0c
ms.lasthandoff: 02/17/2017


---
# <a name="sql-database-auditing-concepts"></a>Conceptos de las auditorías de SQL Database
Las auditorías de Azure SQL Database realizan un seguimiento de eventos de bases de datos y escribe los eventos en un registro de auditoría de la cuenta de Azure Storage.

La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas.

La auditoría posibilita y facilita la observancia de estándares reguladores pero no garantiza el cumplimiento. Para obtener más información acerca de los programas de Azure compatibles con la observancia de estándares, consulte el [Centro de confianza de Azure](https://azure.microsoft.com/support/trust-center/compliance/).

* [Información general sobre las auditorías de Azure SQL Database]
* [Configuración de la auditoría para su base de datos]
* [Análisis de registros e informes de auditoría]

## <a name="a-idsubheading-1aazure-sql-database-auditing-overview"></a><a id="subheading-1"></a>Información general sobre las auditorías de Azure SQL Database
La auditoría de Base de datos SQL le permite:

* **Conservar** una traza de auditoría de eventos seleccionados. Puede definir categorías de acciones de base de datos para auditar.
* **Informar** sobre la actividad de la base de datos. Puede usar informes preconfigurados y un panel para empezar rápidamente con el informe de actividades y eventos.
* **Analizar** informes. Puede buscar eventos sospechosos, actividades inusuales y tendencias.

Existen dos **métodos de auditoría**:

* **Auditoría de blobs**: los registros se escriben en Azure Blob Storage. Es el método de auditoría más reciente, que proporciona mayor rendimiento, admite auditoría de nivel de objeto de mayor granularidad y es más rentable.
* **Auditoría de tablas**: los registros se escriben en Azure Table Storage.

> [!IMPORTANT]
> La introducción de la nueva auditoría de blobs aporta un cambio importante en cómo se va a heredar la directiva de auditoría de servidor por la base de datos. 

Puede configurar auditorías para distintas categorías de tipos de eventos.

* Para configurar y administrar auditorías mediante Azure Portal, consulte [Configure auditing in the Azure portal](sql-database-auditing-portal.md) (Configuración de auditorías en Azure Portal).
* Para configurar y administrar auditorías mediante PowerShell, consulte [Configuración mediante PowerShell](sql-database-auditing-powershell.md).
* Para configurar y administrar auditorías mediante la API de REST, consulte [Configuración de auditorías mediante la API de REST](sql-database-auditing-rest.md).

<!--For each Event Category, auditing of **Success** and **Failure** operations are configured separately.-->

Puede definirse una directiva de auditoría para una base de datos específica o como directiva de servidor predeterminada. Una directiva de auditoría de servidor predeterminada se aplica a todas las bases de datos recién creadas en un servidor.

## <a name="blob-auditing"></a>Auditoría de blobs

Si está habilitada la auditoría de blobs de servidor, siempre se aplica a la base de datos (todas las bases de datos del servidor se van a auditar), sin tener en cuenta:
    - La configuración de auditoría de la base de datos.
    - Si está activada o no la casilla "Heredar la configuración del servidor" en la hoja de la base de datos.

> [!IMPORTANT]
> Habilitar la auditoría de blobs en la base de datos, además de habilitarla en el servidor, **no** invalidará ni cambiará cualquiera de las opciones de la auditoría del servidor de blobs: ambas auditorías existirán en paralelo. En otras palabras, la base de datos se auditará dos veces en paralelo (una vez por la directiva de servidor y otra vez por la directiva de base de datos).

Debe habilitar tanto la auditoría de blobs de servidor como la auditoría de blobs de base de datos, a menos que:
 * Deba usar una *cuenta de almacenamiento* o un *período de retención* diferentes para una base de datos específica.
 * Desea auditar distintos tipos de eventos o categorías para una base de datos específica que se está auditando para el resto de las bases de datos en este servidor (por ejemplo, si se deben auditar las inserciones de tabla solo para una base de datos específica).

En caso contrario, se recomienda habilitar solo la auditoría de blobs de nivel de servidor y dejar que la auditoría de nivel de base de datos esté deshabilitada para todas las bases de datos.

## <a name="table-auditing"></a>Auditoría de tablas

Si está habilitada la auditoría de tabla de nivel de servidor, solo se aplica a la base de datos, si se activa la casilla "Heredar la configuración del servidor" en la hoja de la base de datos (está activada de forma predeterminada para todas las bases de datos recién creadas y existentes).

- Si la casilla de verificación está *activada*, los cambios realizados en la configuración de auditoría de base de datos **invalidan** la configuración del servidor para esta base de datos.

- Si la casilla de verificación está *desactivada* y la auditoría de nivel de base de datos está deshabilitada, la base de datos no se auditará.

## <a name="analyze-audit-logs-and-reports"></a>Análisis de registros e informes de auditoría
Los registros de auditoría se agregan a la cuenta de almacenamiento de Azure que eligió durante la instalación.

Puede explorar los registros de auditoría con una herramienta como el [Explorador de Azure Storage](http://storageexplorer.com/).

## <a name="next-steps"></a>Pasos siguientes

* Para configurar y administrar auditorías mediante Azure Portal, consulte [Configure auditing in the Azure portal](sql-database-auditing-portal.md) (Configuración de auditorías en Azure Portal).
* Para configurar y administrar auditorías mediante PowerShell, consulte [Configuración y administración de auditorías de SQL Database con PowerShell](sql-database-auditing-powershell.md).
* Para configurar y administrar auditorías mediante la API de REST, consulte [Configuración y administración de auditorías de SQL Database mediante la API de REST](sql-database-auditing-rest.md).


<!--Anchors-->
[Información general sobre las auditorías de Azure SQL Database]: #subheading-1
[Configuración de la auditoría para su base de datos]: #subheading-2
[Análisis de registros e informes de auditoría]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)  

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_blob_view_audit_logs.png
[11]: ./media/sql-database-auditing-get-started/11_auditing_get_started_blob_audit_records.png
[12]: ./media/sql-database-auditing-get-started/12_auditing_get_started_table_audit_records.png

