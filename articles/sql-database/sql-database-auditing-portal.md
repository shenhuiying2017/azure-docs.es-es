---
title: "Azure Portal: Administración de SQL Database Auditing | Microsoft Docs"
description: "Configure la auditoría en Azure SQL Database en Azure Portal para realizar el seguimiento de los eventos de base de datos y escribirlos en un registro de auditoría en la cuenta de Azure Storage."
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
ms.date: 2/25/2017
ms.author: ronitr;giladm
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: e20a30a565198c01f213895a87fe8807a37272cb
ms.lasthandoff: 03/30/2017


---

# <a name="configure-and-manage-sql-database-auditing-in-the-azure-portal"></a>Configuración y administración de la auditoría de base de datos SQL en Azure Portal

En la siguiente sección se describe cómo configurar y administrar la auditoría mediante Azure Portal. Para configurar y administrar la auditoría con PowerShell, consulte [Configuración de la auditoría con PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). 

Para obtener información general sobre auditoría, consulte [Auditoría de SQL Database](sql-database-auditing.md).

## <a name="configure-blob-auditing"></a>Configuración de la auditoría de blobs

1. Inicie [Azure Portal](https://portal.azure.com) en https://portal.azure.com.
2. Vaya a la hoja de configuración de la base de datos SQL o el servidor SQL Server que desea auditar. En la hoja Configuración, seleccione **Auditoría y detección de amenazas**.

    ![Panel de navegación](./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png)
3. En la hoja de configuración de la auditoría de base de datos, puede activar la casilla **Heredar la configuración del servidor** para indicar que esta base de datos se auditará según la configuración de su servidor. Si esta opción está activada, verá un vínculo **Ver configuración de auditoría de servidor** que le permite ver o modificar la configuración de auditoría del servidor desde este contexto.

    ![Panel de navegación][2]
4. Si prefiere habilitar la auditoría de blobs en el nivel de base de datos (además, o en lugar, del nivel de servidor) **desactive** la opción **Heredar la configuración de auditoría del servidor**, **active** Auditoría y elija el tipo **Blob**.

    ![Panel de navegación][3]
5. Seleccione **Detalles de almacenamiento** para abrir la hoja de almacenamiento de registros de auditoría. Seleccione la cuenta de almacenamiento de Azure donde se guardarán los registros, y el período de retención, después del cual se eliminarán los registros antiguos. A continuación, haga clic en **Aceptar** en la parte inferior. **Sugerencia:** use la misma cuenta de almacenamiento para todas las bases de datos auditadas con el fin de obtener el máximo rendimiento de las plantillas de informes de auditorías.

    <a id="storage-screenshot"></a>
    ![Panel de navegación][4]
6. Si quiere personalizar los eventos auditados, puede hacerlo a través de PowerShell o de la API de REST.
7. Cuando haya configurado los valores de auditoría, puede activar la nueva detección de amenazas (versión preliminar) y configurar los mensajes de correo electrónico para recibir alertas de seguridad. La detección de amenazas le permite recibir alertas proactivas sobre actividades anómalas de la base de datos que pueden indicar posibles amenazas de seguridad. Consulte [Detección de amenazas](sql-database-threat-detection.md) para más información.
8. Haga clic en **Guardar**.


## <a name="table-auditing"></a>Auditoría de tablas

> [!IMPORTANT]
> Antes de configurar la **auditoría de tablas**, compruebe si usa un ["Cliente de nivel inferior"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md). Además, si tiene una configuración de firewall estricta, tenga en cuenta que el [punto de conexión de IP de la base de datos cambiará](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) al habilitar la auditoría de tablas.
>

1. Inicie [Azure Portal](https://portal.azure.com) en https://portal.azure.com.
2. Vaya a la hoja de configuración de la base de datos SQL o el servidor SQL Server que desea auditar. En la hoja de configuración, seleccione **Auditoría y detección de amenazas** (*[consulte la captura de pantalla en la sección Auditoría de blobs](#auditing-screenshot)*).
3. En la hoja de configuración de la auditoría de base de datos, puede activar la casilla **Heredar la configuración del servidor** para indicar que esta base de datos se auditará según la configuración de su servidor. Si esta opción está activada, verá un vínculo **Ver configuración de auditoría de servidor** que le permite ver o modificar la configuración de auditoría del servidor desde este contexto.

    ![Panel de navegación][2]
4. Si prefiere no heredar la configuración de auditoría del servidor, **desactive** la opción **Heredar la configuración de auditoría del servidor**, **active** Auditoría y elija el tipo **Tabla**.

    ![Panel de navegación][3-tbl]
5. Seleccione **Detalles de almacenamiento** para abrir la hoja de almacenamiento de registros de auditoría. Seleccione la cuenta de almacenamiento de Azure donde se guardan los registros y el período de retención, después del cual los registros antiguos se eliminarán. **Sugerencia:** use la misma cuenta de almacenamiento para todas las bases de datos auditadas a fin de aprovechar al máximo las plantillas de informes de auditoría (*[consulte la captura de pantalla en la sección Auditoría de blobs](#storage-screenshot)*).
6. Haga clic en **Eventos auditados** para personalizar los eventos que se van a auditar. En la hoja Registro por evento, haga clic en **Acierto** y **Error** para registrar todos los eventos, o elija categorías individuales de eventos.

    ![Panel de navegación][5]
7. Cuando haya configurado los valores de auditoría, puede activar la nueva detección de amenazas (versión preliminar) y configurar los mensajes de correo electrónico para recibir alertas de seguridad. La detección de amenazas le permite recibir alertas proactivas sobre actividades anómalas de la base de datos que pueden indicar posibles amenazas de seguridad. Consulte [Detección de amenazas](sql-database-threat-detection.md) para más información.
8. Haga clic en **Guardar**.

## <a name="auditing-geo-replicated-databases"></a>Auditoría de bases de datos con replicación geográfica

Al usar bases de datos con replicación geográfica, es posible configurar la auditoría en la base de datos principal, en la base de datos secundaria, o en ambas, según el tipo de auditoría.

**Auditoría de tablas**: puede configurar una directiva aparte, en el nivel de base de datos o de servidor, para cada una de las dos bases de datos (principal y secundaria).

**Auditoría de blobs**: siga estas instrucciones:

1. **Base de datos principal:** active la auditoría de blobs en el servidor o en la propia base de datos.
2. **Base de datos secundaria**: la auditoría de blobs solo se puede activar o desactivar desde la configuración de auditoría de la base de datos principal.

   * Active la auditoría de blobs en la base de datos principal. La auditoría de blobs debe estar habilitada en la *propia base de datos principal*, no en el servidor.
   * Después de habilitar la auditoría de blobs en la base de datos principal, también se habilitará en la base de datos secundaria.

    > [!IMPORTANT]
    > De forma predeterminada, la configuración de almacenamiento de la base de datos secundaria será idéntica a la de la base de datos principal, lo que provocará tráfico interregional. Para evitarlo, habilite la auditoría de blobs en el servidor secundario y configure un almacenamiento local en su configuración de almacenamiento, así se invalidará la ubicación de almacenamiento de la base de datos secundaria y se guardarán los registros de auditoría de cada base de datos en un almacenamiento local.  

## <a name="viewing-blob-auditing-logs"></a>Visualización de registros de auditoría de blobs

Los registros de auditoría de blobs se guardan como una colección de archivos de blob dentro de un contenedor llamado "**sqldbauditlogs**".

Para más información sobre la jerarquía de carpetas de almacenamiento de registros de auditoría de blobs, y el formato del registro, consulte la [referencia del formato de registro de auditoría de blobs (descarga del archivo de documento)](https://go.microsoft.com/fwlink/?linkid=829599).

Existen varios métodos para ver los registros de auditoría de blobs:

* A través de [Azure Portal](https://portal.azure.com): abra la base de datos correspondiente. En la parte superior de la hoja Auditoría y detección de amenazas de la base de datos, haga clic en **Ver registros de auditoría**.

    ![Panel de navegación][10]

    Se abre una hoja Registros de auditoría, donde podrá ver los registros.

    - Puede elegir ver fechas específicas si hace clic en **Filtrar** en la parte superior de la hoja Registros de auditoría.
    - Puede alternar entre los registros de auditoría que se crearon mediante la directiva de servidor o una directiva de base de datos de auditoría.

    ![Panel de navegación][11]
* Descargue los archivos de registro del contenedor de Azure Storage Blob mediante el portal o con una herramienta como el [Explorador de Azure Storage](http://storageexplorer.com/).

    Cuando haya descargado localmente el archivo de registro, haga doble clic en él para abrir, ver y analizar los registros en SSMS.

* Otros métodos:

   * Puede descargar varios archivos a la vez mediante el Explorador de Azure Storage: haga clic con el botón derecho en una subcarpeta específica (por ejemplo, una que incluya todos los archivos de registro de una fecha determinada) y elija **Guardar como** para guardarla en una carpeta local.

       Después de descargar varios archivos (o un día completo, como se ha descrito anteriormente), puede combinarlos localmente de la manera siguiente:

       **Abra SSMS -> Archivo -> Abrir -> Merge Extended Events (Combinar eventos extendidos) -> Choose all files to merge** (Seleccionar todos los archivos para combinar).
   * Mediante programación:

     * [Biblioteca de C# de lector de eventos extendidos](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/)
     * [Consulta de archivos de eventos extendidos mediante PowerShell](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)

   * Hemos creado una [aplicación de ejemplo](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) que se ejecuta en Azure y utiliza las API públicas de OMS para insertar registros de auditoría SQL en OMS para su uso mediante el panel de OMS.

## <a name="viewing-table-audit-logs"></a>Visualización de registros de auditoría de tabla

Los registros de auditoría de tablas se guardan como una colección de instancias de Azure Storage Table con un prefijo **SQLDBAuditLogs**.

Para más información sobre el formato de registro de auditoría de tablas, consulte la [referencia del formato de registro de auditoría de tablas (descarga del archivo de documento)](http://go.microsoft.com/fwlink/?LinkId=506733).

Existen varios métodos para ver los registros de auditoría de tablas:

* A través de [Azure Portal](https://portal.azure.com): abra la base de datos correspondiente. En la parte superior de la hoja Auditoría y detección de amenazas de la base de datos, haga clic en **Ver registros de auditoría**.

    ![Panel de navegación][10]

    Se abre una hoja Registros de auditoría, donde podrá ver los registros.

    * Puede elegir ver fechas específicas si hace clic en **Filtrar** en la parte superior de la hoja Registros de auditoría.
    * Puede descargar y ver los registros de auditoría en formato Excel si hace clic en **Abrir en Excel** en la parte superior de la hoja Registros de auditoría

    ![Panel de navegación][12]

* Como alternativa, hay una plantilla de informe preconfigurada disponible como [hoja de cálculo de Excel descargable](http://go.microsoft.com/fwlink/?LinkId=403540) para ayudarle a analizar rápidamente los datos de registro. Para usar la plantilla en los registros de auditoría, necesita Excel 2013 o superior y [Power Query(http://www.microsoft.com/download/details.aspx?id=39379).

* También puede importar los registros de auditoría en la plantilla de Excel directamente desde su cuenta de almacenamiento de Azure con Power Query. A continuación, puede explorar los registros de auditoría y crear paneles e informes sobre los datos del registro.

    ![Panel de navegación][9]

## <a name="storage-key-regeneration"></a>Regeneración de clave de almacenamiento
En el entorno de producción, es probable que actualice periódicamente las claves de almacenamiento. Al actualizar las claves se debe volver a guardar la directiva de auditoría. El proceso es el siguiente:

1. En la hoja de detalles de almacenamiento, cambie el valor de **Clave de acceso de almacenamiento** de *primaria* a *secundaria* y luego haga clic en **Aceptar** en la parte inferior. A continuación, haga clic en **GUARDAR** en la parte superior de la hoja de configuración de auditoría.

    ![Panel de navegación][6]
2. Vaya a la hoja de configuración de almacenamiento y **regenere** la *clave de acceso primaria*.

    ![Panel de navegación][8]
3. Vuelva a la hoja de configuración de auditoría, cambie el valor de **Clave de acceso de almacenamiento** de *secundaria* a *primaria* y haga clic en **Aceptar** en la parte inferior. A continuación, haga clic en **GUARDAR** en la parte superior de la hoja de configuración de auditoría.
4. Vuelva a la hoja de configuración de almacenamiento y **vuelva a generar** la *clave de acceso secundaria* (como preparación para el siguiente ciclo de actualización de claves).


<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
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

## <a name="next-steps"></a>Pasos siguientes

* Para configurar y administrar la auditoría con PowerShell, consulte [Configuración de la auditoría de base de datos mediante PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).
* Para obtener información general sobre la auditoría, consulte [Auditoría de base de datos](sql-database-auditing.md).


