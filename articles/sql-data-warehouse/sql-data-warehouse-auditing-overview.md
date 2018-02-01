---
title: "Auditoría en Azure SQL Data Warehouse | Microsoft Docs"
description: "Introducción a la auditoría en Azure SQL Data Warehouse"
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
ms.date: 01/16/2018
ms.author: rortloff;barbkess
ms.openlocfilehash: 5400f29d8c7579809ef7b2a084115473df7baa85
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Auditoría en Azure SQL Data Warehouse

La auditoría de SQL Data Warehouse permite grabar los eventos de la base de datos en un registro de auditoría de una cuenta de Azure Storage. La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas. La auditoría de SQL Data Warehouse también se integra con Microsoft Power BI para realizar informes y análisis.

Las herramientas de auditoría posibilitan y facilitan la observancia de estándares reguladores pero no garantizan el cumplimiento. Para obtener más información acerca de los programas de Azure compatibles con la observancia de estándares, consulte el <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centro de confianza de Azure</a>.

## <a id="subheading-1"></a>Conceptos básicos de auditoría
La auditoría de SQL Data Warehouse la permite que:

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

Puede definir una directiva de auditoría para una base de datos específica o como directiva de servidor predeterminada. Una directiva de auditoría de servidor predeterminada se aplica a todas las bases de datos de un servidor que no tengan una directiva de auditoría de base de datos de reemplazo específica definida.

Antes de configurar la auditoría, compruebe si usa un ["Cliente de nivel inferior"](sql-data-warehouse-auditing-downlevel-clients.md)

## <a id="subheading-2"></a>Configuración de la auditoría para su base de datos
1. Inicie <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.
2. Vaya a **Configuración** de la instancia de SQL Data Warehouse que quiere auditar. Seleccione **Auditoría y detección de amenazas**.
   
    ![][1]
3. A continuación, para habilitar la auditoría, haga clic en el botón **ACTIVADO** .
   
    ![][3]
4. En el panel de configuración de auditoría, seleccione **DETALLES DE ALMACENAMIENTO** para abrir el panel Almacenamiento de registros de auditoría. Seleccione la cuenta de Azure Storage donde se guardarán los registros y el período de retención. 
>[!TIP]
>Use la misma cuenta de almacenamiento para todas las bases de datos auditadas con el fin de obtener el máximo rendimiento de las plantillas de informes preconfiguradas.
   
    ![][4]
5. Haga clic en el botón **Aceptar** para guardar los detalles de configuración de almacenamiento.
6. En **REGISTRO POR EVENTO**, haga clic en **CORRECTO** y **ERROR** para registrar todos los eventos, o elija categorías individuales de eventos.
7. Si va a configurar la auditoría para una base de datos, puede que tenga que modificar la cadena de conexión del cliente para asegurarse de que los datos de auditoría se capturan correctamente. Consulte el tema [Modificación del FDQN de servidor en la cadena de conexión](sql-data-warehouse-auditing-downlevel-clients.md) sobre conexiones de cliente de nivel inferior.
8. Haga clic en **OK**.

## <a id="subheading-3"></a>Análisis de registros e informes de auditoría
Los registros de auditoría se agregan en una recopilación de tablas de Almacenamiento con el prefijo **SQLDBAuditLogs** en la cuenta de almacenamiento de Azure que eligió durante la configuración. Puede ver archivos de registro usando una herramienta como el <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Explorador de Azure Storage</a>.

Hay una plantilla de informe de panel preconfigurada disponible como <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">hoja de cálculo de Excel descargable</a> para ayudarle a analizar datos de registro rápidamente. Para utilizar la plantilla en los registros de auditoría, necesita Excel 2013 o posterior y Power Query, que puede descargar <a href="http://www.microsoft.com/download/details.aspx?id=39379">aquí</a>.

La plantilla contiene datos de ejemplo ficticios y puede configurar Power Query para importar el registro de auditoría directamente desde la cuenta de almacenamiento de Azure.

## <a id="subheading-4"></a>Regeneración de clave de almacenamiento
En el entorno de producción, es probable que actualice periódicamente las claves de almacenamiento. Cuando actualice las claves, debe volver a guardar la directiva. El proceso es el siguiente:

1. En el panel de configuración de auditoría, descrito anteriormente en la sección de configuración de auditoría, cambie la **Clave de acceso de almacenamiento** de *Principal* a *Secundaria* y haga clic en **GUARDAR**.

   ![][4]
2. Vaya al panel de configuración de almacenamiento y **vuelva a generar** la *clave de acceso principal*.
3. Vuelva al panel de configuración de auditoría, 
4. cambie el valor de **Clave de acceso de almacenamiento** de *Secundaria* a *Principal* y presione **GUARDAR**.
4. Vuelva a la interfaz de usuario de almacenamiento y **regenere** la *Clave de acceso secundaria* (como preparación para el siguiente ciclo de actualización de las claves).

## <a id="subheading-5"></a>Automation (PowerShell o API de REST)
También puede configurar la auditoría en Azure SQL Data Warehouse mediante las siguientes herramientas de automatización:

* **Cmdlets de PowerShell**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy)
   * [Get-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy)
   * [Remove-AzureRMSqlDatabaseAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing)
   * [Remove-AzureRMSqlServerAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing)
   * [Set-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy)
   * [Set-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy)
   * [Use-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>Compatibilidad con clientes de nivel inferior para enmascaramiento de datos dinámicos y auditoría
La auditoría funciona con los clientes SQL que admiten el redireccionamiento de TDS.

Cualquier cliente que implementa TDS 7.4 también debe admitir el redireccionamiento. Entre las excepciones a esto se incluyen JDBC 4.0, en el que la función de redireccionamiento no es totalmente compatible y Tedious para Node.JS, en cuya redireccionamiento no se ha implementado.

Para "clientes de nivel inferior" que admiten TDS versión 7.3 e inferiores, se debe modificar el FQDN del servidor en la cadena de conexión como se indica a continuación:

- FQDN del servidor original en la cadena de conexión: <*nombre del servidor*>. database.windows.net
- FQDN del servidor modificado en la cadena de conexión: <*nombre del servidor*>. database.**secure**.windows.net

Una lista parcial de "Clientes de nivel inferior" incluye:

* .NET 4.0 y versiones posteriores,
* ODBC 10.0 y versiones posteriores.
* JDBC (aunque JDBC admite TDS 7.4, la característica de redireccionamiento de TDS no es totalmente compatible)
* Tedious (para Node.JS)

**Comentario:** la anterior modificación del FDQN del servidor puede resultar útil también para aplicar una directiva de auditoría de nivel de SQL Server sin necesidad de un paso de configuración en cada base de datos (mitigación temporal).     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


