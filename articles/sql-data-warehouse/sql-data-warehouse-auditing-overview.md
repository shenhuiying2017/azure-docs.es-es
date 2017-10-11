---
title: "Auditoría en Azure SQL Data Warehouse | Microsoft Docs"
description: "Introducción a la auditoría en Almacenamiento de datos SQL de Azure"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: 0e6af148-b218-4b43-bb5f-907917d20330
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: security
ms.date: 08/21/2017
ms.author: rortloff;barbkess
ms.openlocfilehash: f851c82ebeaa647f663d499a4d327c3479e36121
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Auditoría en Almacenamiento de datos SQL de Azure
> [!div class="op_single_selector"]
> * [Auditoría](sql-data-warehouse-auditing-overview.md)
> * [Detección de amenazas](sql-data-warehouse-security-threat-detection.md)
> 
> 

La auditoría de SQL Data Warehouse permite grabar los eventos de la base de datos en un registro de auditoría de una cuenta de Azure Storage. La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas. La auditoría de SQL Data Warehouse también se integra con Microsoft Power BI, con el fin de facilitar la generación de análisis e informes detallados.

Las herramientas de auditoría posibilitan y facilitan la observancia de estándares reguladores pero no garantizan el cumplimiento. Para obtener más información acerca de los programas de Azure compatibles con el cumplimiento de estándares, consulte el <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centro de confianza de Azure</a>.

* [Conceptos básicos de auditoría de Base de datos]
* [Configuración de la auditoría para su base de datos]
* [Análisis de registros e informes de auditoría]

## <a id="subheading-1"></a>Conceptos básicos de auditoría de Almacenamiento de datos SQL de Azure
La auditoría de Almacenamiento de datos SQL la permite que:

* **Conservar** una traza de auditoría de eventos seleccionados. Puede definir categorías de acciones de base de datos para auditar.
* **Informar** sobre la actividad de la base de datos. Puede usar informes preconfigurados y un panel para empezar rápidamente con el informe de actividades y eventos.
* **Analizar** informes. Puede buscar eventos sospechosos, actividades inusuales y tendencias.

Puede configurar la auditoría para las categorías de eventos siguientes:

**SQL sin formato** y **SQL parametrizado** para los que los registros de auditoría recopilados se clasifican como  

* **Acceso a datos**
* **Cambios de esquema (DDL)**
* **Cambios de datos (DML)**
* **Cuentas, roles y permisos (DCL)**
* **Procedimiento almacenado**, **Inicio de sesión** y **Administración de transacciones**.

Para cada categoría de eventos, las operaciones de **aciertos** y **errores** se configuran por separado.

Para más información acerca de las actividades y eventos auditados, consulte la <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Referencia de formato del registro de auditoría (descarga de archivo .doc)</a>.

Los registros de auditoría se almacenan en su cuenta de almacenamiento de Azure. Puede definir un período de retención de registro de auditoría.

Puede definirse una directiva de auditoría para una base de datos específica o como directiva de servidor predeterminada. La directiva de auditoría de servidor predeterminada se aplica a todas las bases de datos de un servidor que no tengan una directiva de auditoría de base de datos de reemplazo específica definida.

Antes de configurar la auditoría, compruebe si usa un ["Cliente de nivel inferior"](sql-data-warehouse-auditing-downlevel-clients.md)

## <a id="subheading-2"></a>Configuración de la auditoría para su base de datos
1. Inicie <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.
2. Vaya a la hoja **Configuración** de la instancia de SQL Data Warehouse que quiere auditar. En la hoja **Configuración**, seleccione **Auditoría y detección de amenazas**.
   
    ![][1]
3. A continuación, para habilitar la auditoría, haga clic en el botón **ACTIVADO** .
   
    ![][3]
4. En la hoja de configuración de auditoría, seleccione **DETALLES DE ALMACENAMIENTO** para abrir la hoja Almacenamiento de registros de auditoría. Seleccione la cuenta de almacenamiento de Azure donde se guardarán los registros y el período de retención. 
>[!TIP]
>Use la misma cuenta de almacenamiento para todas las bases de datos auditadas con el fin de obtener el máximo rendimiento de las plantillas de informes preconfiguradas.
   
    ![][4]
5. Haga clic en el botón **Aceptar** para guardar los detalles de configuración de almacenamiento.
6. En **REGISTRO POR EVENTO**, haga clic en **CORRECTO** y **ERROR** para registrar todos los eventos, o elija categorías individuales de eventos.
7. Si va a configurar la auditoría para una base de datos, puede que tenga que modificar la cadena de conexión del cliente para asegurarse de que los datos de auditoría se capturan correctamente. Consulte el tema [Modificación del FDQN de servidor en la cadena de conexión](sql-data-warehouse-auditing-downlevel-clients.md) sobre conexiones de cliente de nivel inferior.
8. Haga clic en **Aceptar**.

## <a id="subheading-3"></a>Análisis de registros e informes de auditoría
Los registros de auditoría se agregan en una recopilación de tablas de Almacenamiento con el prefijo **SQLDBAuditLogs** en la cuenta de almacenamiento de Azure que eligió durante la configuración. Puede ver los archivos de registro usando una herramienta como el <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Explorador de almacenamiento de Azure</a>.

Hay una plantilla de informe de panel preconfigurada disponible como <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">hoja de cálculo de Excel descargable</a> para ayudarle a analizar datos de registro rápidamente. Para utilizar la plantilla en los registros de auditoría, necesita Excel 2013 o posterior y Power Query, que puede descargar <a href="http://www.microsoft.com/download/details.aspx?id=39379">aquí</a>.

La plantilla contiene datos de ejemplo ficticios y puede configurar Power Query para importar el registro de auditoría directamente desde la cuenta de almacenamiento de Azure.

## <a id="subheading-4"></a>Regeneración de clave de almacenamiento
En el entorno de producción, es probable que actualice periódicamente las claves de almacenamiento. Cuando actualice las claves, debe volver a guardar la directiva. El proceso es el siguiente:

1. En la hoja de configuración de auditoría (descrita anteriormente en la sección de configuración de auditoría), cambie la **Clave de acceso de almacenamiento** de *Principal* a *Secundaria* y haga clic en **GUARDAR**.

   ![][4]
2. Vaya a la hoja de configuración de almacenamiento y **regenere** la *Clave de acceso primaria*.
3. Vuelva a la hoja de configuración de auditoría, cambie el valor de **Clave de acceso de almacenamiento** de *Secundaria* a *Principal* y presione **GUARDAR**.
4. Vuelva a la interfaz de usuario de almacenamiento y **regenere** la *Clave de acceso secundaria* (como preparación para el siguiente ciclo de actualización de las claves).

## <a id="subheading-5"></a>Automatización (PowerShell o API de REST)
También puede configurar la auditoría en Azure SQL Data Warehouse mediante las siguientes herramientas de automatización:

* **Cmdlets de PowerShell**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]

<!--Anchors-->
[Conceptos básicos de auditoría de Base de datos]: #subheading-1
[Configuración de la auditoría para su base de datos]: #subheading-2
[Análisis de registros e informes de auditoría]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->
[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy
[107]: /powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy