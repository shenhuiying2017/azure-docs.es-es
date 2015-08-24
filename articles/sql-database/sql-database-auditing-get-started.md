<properties 
	pageTitle="Introducción a la auditoría de bases de datos SQL | Microsoft Azure" 
	description="Introducción a la auditoría de bases de datos SQL" 
	services="sql-database" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="jeffreyg"/>
 
# Introducción a la auditoría de bases de datos SQL 
<p> La auditoría de Base de datos SQL de Azure realiza un seguimiento de los eventos de base de datos y escribe eventos auditados en un registro de auditoría en la cuenta de Almacenamiento de Azure. La auditoría está generalmente disponible para los niveles de servicio Básico, Estándar y Premium.

La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas.

Las herramientas de auditoría posibilitan y facilitan la observancia de estándares reguladores pero no garantizan el cumplimiento. Para obtener más información acerca de los programas de Azure compatibles con el cumplimiento de estándares, consulte el <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centro de confianza de Azure</a>.

+ [Conceptos básicos de la auditoría de Base de datos SQL de Azure] 
+ [Configuración de la auditoría para su base de datos]
+ [Análisis de registros e informes de auditoría]

##<a id="subheading-1"></a>Conceptos básicos de la auditoría de Base de datos SQL de Azure

Las secciones siguientes describen la configuración de auditoría mediante el Portal de vista previa de Azure. También puede [configurar la auditoría para su base de datos con el Portal de Azure clásico].

La auditoría de Base de datos SQL le permite:

- **Conservar** una traza de auditoría de eventos seleccionados. Puede definir categorías de acciones de base de datos para auditar.
- **Informar** sobre la actividad de la base de datos. Puede usar informes preconfigurados y un panel para empezar rápidamente con el informe de actividades y eventos.
- **Analizar** informes. Puede buscar eventos sospechosos, actividades inusuales y tendencias.

Puede configurar la auditoría para las categorías de eventos siguientes:

**SQL sin formato** y **SQL parametrizado** para los que los registros de auditoría recopilados se clasifican como

- **Acceso a datos**
- **Cambios de esquema (DDL)**
- **Cambios de datos (DML)**
- **Cuentas, roles y permisos (DCL)**

**Procedimiento almacenado**, **Inicio de sesión** y **Administración de transacciones**.

Para cada categoría de eventos, las operaciones de **aciertos** y **errores** se configuran por separado.

Para obtener más detalles acerca de las actividades y eventos auditados, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Referencia de formato del registro de auditoría (descarga de archivo .doc)</a>.

Los registros de auditoría se almacenan en su cuenta de almacenamiento de Azure. Puede definir un período de retención de registro de auditoría.

Puede definirse una directiva de auditoría para una base de datos específica o como directiva de servidor predeterminada. La directiva de auditoría de servidor predeterminada se aplicará a todas las bases de datos de un servidor que no tengan una directiva de auditoría de base de datos de reemplazo específica definida.

Antes de configurar la auditoría, compruebe si usa un ["Cliente de nivel inferior"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


##<a id="subheading-2"></a>Configuración de la auditoría para su base de datos

1. Inicie el <a href="https://portal.azure.com" target="_blank">Portal de vista previa de Azure</a> en https://portal.azure.com. También puede iniciar el <a href= "https://manage.windowsazure.com/" target="_bank">Portal de Azure clásico</a> en https://manage.windowsazure.com/. Consulte los detalles que aparecen a continuación.

2. Vaya a la hoja de configuración de la base de datos/ servidor de SQL Server que desea auditar. Haga clic en el botón **Configuración** situado en la parte superior y, a continuación, en la hoja Configuración, y seleccione **Auditoría**.

	![][1]

3. En la hoja de configuración de auditoría, seleccione Detalles de almacenamiento para abrir la hoja Almacenamiento de registros de auditoría. Seleccione la cuenta de almacenamiento de Azure donde se guardarán los registros y el período de retención. **Sugerencia**: use la misma cuenta de almacenamiento para todas las bases de datos auditadas con el fin de obtener el máximo rendimiento de las plantillas de informes preconfiguradas.

	![][2]

4. En **REGISTRO POR EVENTO**, haga clic en **CORRECTO** y **ERROR** para registrar todos los eventos, o elija categorías individuales de eventos.


5. Si configura la auditoría para una base de datos de SQL, haga clic en **Para exigir la auditoría, seleccione la opción...** y en **ACCESO HABILITADO DE SEGURIDAD** seleccione **OBLIGATORIO**. Si configura la auditoría para un servidor de SQL Server tiene dos opciones: (a) después del paso 6, vaya a cada base de datos SQL del servidor y aplique este paso, o (b) [modifique el nombre de dominio completo del servidor en la cadena de conexión](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


	![][5]

6. Haga clic en **Aceptar**.



##<a id="subheading-3">Análisis de registros e informes de auditoría</a>

Los registros de auditoría se agregan en una recopilación de tablas de Almacenamiento con el prefijo **SQLDBAuditLogs** en la cuenta de almacenamiento de Azure que eligió durante la configuración. Puede ver los archivos de registro usando una herramienta como el <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Explorador de almacenamiento de Azure</a>.

Hay una plantilla de informe de panel preconfigurada disponible como <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">hoja de cálculo de Excel descargable</a> para ayudarle a analizar datos de registro rápidamente. Para utilizar la plantilla en los registros de auditoría, necesita Excel 2013 o posterior y Power Query, que puede descargar <a href="http://www.microsoft.com/download/details.aspx?id=39379">aquí</a>.

La plantilla contiene datos de ejemplo ficticios y puede configurar Power Query para importar el registro de auditoría directamente desde la cuenta de almacenamiento de Azure.

Para obtener instrucciones más detalladas sobre el trabajo con la plantilla de informe, lea el <a href="http://go.microsoft.com/fwlink/?LinkId=506731">documento de procedimiento (.doc descargable)</a>.

![][6]


##<a id="subheading-4"></a>Configuración de la auditoría para su base de datos con el Portal de Azure clásico

1. Inicie el <a href= "https://manage.windowsazure.com/" target="_bank">Portal de Azure clásico</a> en https://manage.windowsazure.com/.
 
2.   Haga clic en la base de datos o el servidor de SQL Server que desea auditar y, a continuación, haga clic en la pestaña **Auditoría y seguridad**.

3.   Si configura la auditoría para una base de datos de SQL, en **ACCESO HABILITADO DE SEGURIDAD** seleccione **OBLIGATORIO**. Si configura la auditoría para un servidor de SQL Server tiene dos opciones: (a) después del paso 7, vaya a cada base de datos SQL del servidor y aplique este paso, o (b) [modifique el nombre de dominio completo del servidor en la cadena de conexión](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).

4. En la sección de auditoría, haga clic en **HABILITADO**.


	![][7]

5. Edite el **REGISTRO POR EVENTO** según sea necesario.

	![][8]

6. Seleccione una **CUENTA DE ALMACENAMIENTO** y configure la **RETENCIÓN**.

	![][11]

7. Haga clic en **GUARDAR**.




##<a id="subheading-3">Prácticas para el uso en producción</a>
La descripción de esta sección se refiere a las capturas de pantalla anteriores. Se puede utilizar tanto el <a href="https://portal.azure.com" target="_blank">Portal de vista previa de Azure</a> como el <a href= "https://manage.windowsazure.com/" target="_bank">Portal de Azure clásico</a>.
 

##<a id="subheading-4"></a>Regeneración de clave de almacenamiento

En el entorno de producción, es probable que actualice periódicamente las claves de almacenamiento. Cuando actualice las claves deberá volver a guardar la directiva. El proceso es el siguiente:


1. En la hoja de configuración de auditoría (descrita anteriormente en la sección de configuración de auditoría), cambie la **Clave de acceso de almacenamiento** de *Principal* a *Secundaria* y presione **GUARDAR**. ![][10]
2. Vaya a la hoja de configuración de almacenamiento y **regenere** la *Clave de acceso primaria*.

3. Vuelva a la hoja de configuración de auditoría, cambie la **Clave de acceso de almacenamiento** de *Secundaria* a *Principal* y presione **GUARDAR**.

4. Vuelva a la interfaz de usuario de almacenamiento y **regenere** la *Clave de acceso secundaria* (como preparación para el siguiente ciclo de actualización de las claves).
  
##<a id="subheading-4"></a>Automatización
Hay varios cmdlets de PowerShell que puede usar para configurar la auditoría en la base de datos de SQL de Azure. Para acceder a los cmdlets de auditoría debe ejecutar PowerShell en el modo del Administrador de recursos de Azure.

> [AZURE.NOTE]El módulo [Administrador de recursos de Azure](https://msdn.microsoft.com/library/dn654592.aspx) se encuentra disponible actualmente en modo de vista previa. Puede que no ofrezca las mismas capacidades de administración que el módulo de Azure.

Cuando esté en el modo Administrador de recursos de Azure, ejecute `Get-Command *AzureSql*` para enumerar los cmdlets disponibles.







<!--Anchors-->
[Conceptos básicos de la auditoría de Base de datos SQL de Azure]: #subheading-1
[Configuración de la auditoría para su base de datos]: #subheading-2
[Análisis de registros e informes de auditoría]: #subheading-3
[configurar la auditoría para su base de datos con el Portal de Azure clásico]: #subheading-4


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
[2]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
[3]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
[5]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
[6]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
[7]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-enable.png
[8]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-configure.png
[9]: ./media/sql-database-auditing-get-started/sql-database-auditing-security-enabled-access.png
[10]: ./media/sql-database-auditing-get-started/sql-database-auditing-storage-account.png
[11]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-configure-storage.png






<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

 

<!---HONumber=August15_HO7-->