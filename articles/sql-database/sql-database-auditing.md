---
title: "Introducción a la auditoría de Azure SQL Database | Microsoft Docs"
description: "Introducción a la auditoría de base de datos SQL de Azure"
services: sql-database
documentationcenter: 
author: giladm
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: security
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: giladm
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: f4324a59b5fa4c2e1ab5b1d7fc7e5fe986ea80f8
ms.contentlocale: es-es
ms.lasthandoff: 06/22/2017


---
# <a name="get-started-with-sql-database-auditing"></a>Introducción a la auditoría de bases de datos SQL
La auditoría de base de datos SQL de Azure realiza el seguimiento de eventos de base de datos y los registra en un registro de auditoría de la cuenta de Azure Storage. La auditoría también:

* Puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas.

* Posibilita y facilita la observancia de estándares reguladores aunque no garantiza el cumplimiento. Para obtener más información acerca de los programas de Azure compatibles con la observancia de estándares, consulte el [Centro de confianza de Azure](https://azure.microsoft.com/support/trust-center/compliance/).


## <a id="subheading-1"></a>Información general de auditoría de base de datos SQL de Azure
Puede usar la auditoría de base de datos SQL para:


* **Conservar** una traza de auditoría de eventos seleccionados. Puede definir categorías de acciones de base de datos para auditar.
* **Informar** sobre la actividad de la base de datos. Puede usar informes preconfigurados y un panel para empezar rápidamente con el informe de actividades y eventos.
* **Analizar** informes. Puede buscar eventos sospechosos, actividades inusuales y tendencias.

Puede configurar la auditoría para diferentes tipos de categorías de eventos, como se explica en la sección [Configuración de la auditoría para su base de datos](#subheading-2).

Los registros de auditoría se escriben en Azure Blob Storage en la suscripción de Azure.


## <a id="subheading-8"></a>Definir la directiva de auditoría de nivel de servidor frente la de nivel de base de datos

Puede definirse una directiva de auditoría para una base de datos específica o como directiva de servidor predeterminada:

* Una directiva de servidor se aplica a todas las bases de datos recién creadas en el servidor.

* Si *está habilitada la auditoría de blobs de servidor*, *siempre se aplica a la base de datos* (es decir, la base de datos se va a auditar), sin tener en cuenta la configuración de auditoría de la base de datos.

* Habilitar la auditoría de blobs en la base de datos, además de en el servidor, *no* invalidará ni cambiará ninguna de las opciones de la auditoría del servidor de blobs. Ambas auditorías existirán en paralelo. En otras palabras, la base de datos se auditará dos veces en paralelo (una vez por la directiva de servidor y otra vez por la directiva de base de datos).

   > [!NOTE]
   > Debe habilitar tanto la auditoría de blobs de servidor como la auditoría de blobs de base de datos, a menos que:
    > * Quiera usar una *cuenta de almacenamiento* o un *período de retención* diferentes para una base de datos específica.
    > * Quiera auditar tipos de eventos o categorías para una base de datos específica que difieren de los tipos de eventos o categorías que se están auditando para el resto de las bases de datos del servidor. Por ejemplo, es posible que tenga inserciones de tabla que solo tengan que deban auditarse para una base de datos concreta.
   > 
   > En caso contrario, se recomienda habilitar solo la auditoría de blobs de nivel de servidor y dejar que la auditoría de nivel de base de datos esté deshabilitada para todas las bases de datos.


## <a id="subheading-2"></a>Configuración de la auditoría para su base de datos
En la sección siguiente se describe la configuración de auditoría mediante Azure Portal.

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Vaya a la hoja **Configuración** de la base de datos SQL o el servidor SQL que quiere auditar. En la hoja **Configuración**, seleccione **Auditoría y detección de amenazas**.

    <a id="auditing-screenshot"></a>
    ![Panel de navegación][1]
3. Si quiere configurar una directiva de auditoría del servidor (que se aplicará a todas las bases de datos recién creadas y existentes en este servidor), puede seleccionar el vínculo **Ver configuración del servidor** en la hoja de la auditoría de base de datos. Después, puede ver o modificar la configuración de auditoría del servidor.

    ![Panel de navegación][2]
4. Si prefiere habilitar la auditoría de blobs en el nivel de la base de datos (además o en lugar de la auditoría a nivel de servidor), para **Auditoría**, seleccione **Activado** y para **Tipo de auditoría**, seleccione **Blob**.

    Si está habilitada la auditoría de blobs del servidor, la auditoría configurada de base de datos se producirá de forma paralela a la auditoría de blobs del servidor.  

    ![Panel de navegación][3]
5. Para abrir la hoja **Almacenamiento de registros de auditoría**, seleccione **Detalles de almacenamiento**. Seleccione la cuenta de Azure Storage donde se guardarán los registros, y el período de retención, después del cual los registros antiguos se eliminarán. A continuación, haga clic en **Aceptar**. 
   >[!TIP] 
   >Con el fin de obtener el máximo rendimiento de las plantillas de informes de auditorías, use la misma cuenta de almacenamiento para todas las bases de datos auditadas. 

    <a id="storage-screenshot"></a>
    ![Panel de navegación][4]
6. Si quiere personalizar los eventos auditados, puede hacerlo a través de PowerShell o de la API de REST. Para obtener más información, vea la sección [Automatización (API de REST o PowerShell)](#subheading-7).
7. Después de configurar los valores de auditoría, puede activar la nueva característica de detección de amenazas y configurar los mensajes de correo para recibir alertas de seguridad. Cuando se usa la detección de amenazas, se reciben alertas proactivas sobre actividades anómalas de la base de datos que pueden indicar posibles amenazas de seguridad. Para obtener más detalles, vea [Introducción a la detección de amenazas](sql-database-threat-detection-get-started.md).
8. Haga clic en **Guardar**.





## <a id="subheading-3"></a>Análisis de registros e informes de auditoría
Los registros de auditoría se agregan a la cuenta de almacenamiento de Azure que eligió durante la instalación. Puede explorar los registros de auditoría con una herramienta como el [Explorador de Azure Storage](http://storageexplorer.com/).

Los registros de auditoría de blobs se guardan como una colección de archivos de blob dentro de un contenedor llamado **sqldbauditlogs**.

Para obtener más información sobre la jerarquía de las carpetas de almacenamiento de registros de auditoría de blobs, las convenciones de nomenclatura y el formato del registro, vea la [referencia del formato de registro de auditoría de blobs (descarga del archivo de documento)](https://go.microsoft.com/fwlink/?linkid=829599).

Existen varios métodos que puede usar para ver los registros de auditoría de blobs:

* Usar [Azure Portal](https://portal.azure.com).  Abra la base de datos pertinente. En la parte superior de la hoja **Auditoría y detección de amenazas** de la base de datos, haga clic en **Ver registros de auditoría**.

    ![Panel de navegación][7]

    Se abre una hoja **Registros de auditoría**, desde la que podrá ver los registros.

    - Puede elegir ver fechas específicas si hace clic en **Filtrar** en la parte superior de la hoja **Registros de auditoría**.
    - Puede cambiar entre los registros de auditoría que se crearon mediante la directiva de servidor o una directiva de base de datos de auditoría.

       ![Panel de navegación][8]

* Use la función del sistema **sys.fn_get_audit_file** (T-SQL) para devolver los datos de registro de auditoría en formato tabular. Para más información sobre el uso de esta función, vea la [documentación de sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).


* Use **Combinar archivos de auditoría** en SQL Server Management Studio (a partir de SSMS 17):  
    1. En el menú SSMS, seleccione **Archivo** > **Abrir** > **Combinar archivos de auditoría**.

        ![Panel de navegación][9]
    2. Se abre el cuadro de diálogo **Agregar archivos de auditoría**. Seleccione una de las opciones **Agregar** para elegir si quiere combinar los archivos de auditoría desde un disco local o importarlos desde Azure Storage (se le pedirá que proporcione los detalles y la clave de cuenta de Azure Storage).

    3. Una vez agregados todos los archivos que se van a combinar, haga clic en **Aceptar** para completar la operación de combinación.

    4. El archivo combinado se abre en SSMS, donde puede verlo y analizarlo, y también exportarlo a un archivo XEL o CSV, o a una tabla.

* Use la [aplicación de sincronización](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) que hemos creado. Se ejecuta en Azure y usa las API públicas de Log Analytics de Operations Management Suite (OMS) para insertar los registros de auditoría SQL en OMS. La aplicación de sincronización inserta los registros de auditoría SQL en Log Analytics de OMS para su consumo mediante el panel de Log Analytics de OMS. 

* Use Power BI. Puede ver y analizar los datos de registro de auditoría en Power BI. Obtenga más información sobre [Power BI y tenga acceso a una plantilla que se puede descargar](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).

* Descargue los archivos de registro del contenedor de blobs de Azure Storage mediante el portal o con una herramienta como el [Explorador de Azure Storage](http://storageexplorer.com/).
    * Después de descargar localmente un archivo de registro, puede hacer doble clic en él para abrir, ver y analizar los registros en SSMS.
    * También puede descargar varios archivos al mismo tiempo a través del Explorador de Azure Storage. Haga clic con el botón derecho en una subcarpeta específica (por ejemplo, una que incluya todos los archivos de registro para una fecha concreta) y seleccione **Guardar como** para guardar en una carpeta local.

* Otros métodos:
   * Después de descargar varios archivos (o una subcarpeta que incluya los archivos de registro de un día completo, tal como se describe en el elemento anterior de esta lista), puede combinarlos localmente como se describe en las instrucciones anteriores para combinar archivos de auditoría de SSMS.

   * Ver los registros de auditoría de blobs mediante programación:

     * Use la biblioteca de C# de [lector de eventos extendidos](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/).
     * [Consulta de archivos de eventos extendidos](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) mediante PowerShell.




## <a id="subheading-5"></a>Prácticas de producción
<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Auditoría de bases de datos con replicación geográfica</a>
Cuando se usan bases de datos con replicación geográfica, es posible configurar la auditoría en la base de datos principal, la base de datos secundaria, o ambas, según el tipo de auditoría.

Siga estas instrucciones (recuerde que la auditoría de blobs solo se puede activar o desactivar desde la configuración de auditoría de la base de datos principal):

* **Base de datos principal**. Active la auditoría de blobs en el servidor o en la propia base de datos, como se describe en la sección [Configuración de la auditoría para su base de datos](#subheading-2).
* **Base de datos secundaria**. Active la auditoría de blobs en la base de datos principal, como se describe en la sección [Configuración de la auditoría para su base de datos](#subheading-2). 
   * La auditoría de blobs debe estar habilitada en la *propia base de datos principal*, no en el servidor.
   * Después de habilitar la auditoría de blobs en la base de datos principal, también se habilitará en la base de datos secundaria.

     >[!IMPORTANT]
     >De forma predeterminada, la configuración de almacenamiento de la base de datos secundaria será idéntica a la de la base de datos principal, lo que provocará tráfico interregional. Para evitarlo, puede habilitar la auditoría de blobs en el servidor secundario y configurar el almacenamiento local en la configuración de almacenamiento del servidor secundario. Esto anulará la ubicación de almacenamiento para la base de datos secundaria y, como resultado, cada base de datos guardará sus registros de auditoría en el almacenamiento local.  
<br>

### <a id="subheading-6">Regeneración de clave de almacenamiento</a>
En el entorno de producción, es probable que actualice periódicamente las claves de almacenamiento. Al actualizar las claves se debe volver a guardar la directiva de auditoría. El proceso es el siguiente:

1. Abra la hoja **Detalles de almacenamiento**. En el cuadro **Clave de acceso de almacenamiento**, seleccione **Secundaria** y haga clic en **Aceptar**. Después, haga clic en **Guardar** en la parte superior de la hoja de configuración de auditoría.

    ![Panel de navegación][5]
2. Vaya a la hoja de configuración de almacenamiento y vuelva a generar la clave de acceso primaria.

    ![Panel de navegación][6]
3. Vuelva a la hoja de configuración de auditoría, cambie el valor de la clave de acceso de almacenamiento de Secundaria a Principal y haga clic en **Aceptar**. Después, haga clic en **Guardar** en la parte superior de la hoja de configuración de auditoría.
4. Vuelva a la hoja de configuración de almacenamiento y vuelva a generar la clave de acceso secundaria (como preparación para el siguiente ciclo de actualización de claves).

## <a id="subheading-7"></a>Automatización (PowerShell o API de REST)
También puede configurar la auditoría en Azure SQL Database mediante las siguientes herramientas de automatización:

* **Cmdlets de PowerShell**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]

   Para ver un script de ejemplo, consulte [Configuración de la auditoría y detección de amenazas mediante PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

* **API de REST: auditoría de blobs**:

   * [Create or Update Database Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt695939.aspx) (Creación o actualización de la directiva de auditoría de blobs de la base de datos)
   * [Create or Update Server Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt771861.aspx) (Creación o actualización de la directiva de audioría de blobs del servidor)
   * [Get Database Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt695938.aspx) (Obtención de la directiva de auditoría de bobs de la base de datos)
   * [Get Server Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt771860.aspx) (Obtención de la directiva de auditoría de blobs del servidor)
   * [Get Server Blob Auditing Operation Result](https://msdn.microsoft.com/library/azure/mt771862.aspx) (Obtención de resultados de funcionamiento de la auditoría de blobs del servidor)


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
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png

[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy
[107]: /powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy

