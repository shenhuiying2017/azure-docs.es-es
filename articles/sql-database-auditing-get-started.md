<properties title="Get started with SQL database auditing" pageTitle="Introducción a la auditoría de bases de datos SQL | Azure" description="Get started with SQL database auditing" metaKeywords="" services="sql-database" solutions="data-management" documentationCenter="" authors="jeffreyg" videoId="" scriptId="" manager="jeffreyg" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/31/2015" ms.author="jeffreyg" />
 
# Introducción a la auditoría de bases de datos SQL 
<p> La auditoría de Base de datos SQL de Azure realiza un seguimiento de los eventos de base de datos y escribe eventos auditados en un registro de auditoría en la cuenta de Almacenamiento de Azure. La auditoría está disponible para los niveles de servicio Basic, Standard y Premium.

La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas. 

Las herramientas de auditoría posibilitan y facilitan la observancia de estándares reguladores pero no garantizan el cumplimiento. Para obtener más información acerca de los programas de Azure compatibles con el cumplimiento de estándares, consulte el <a href="http://azure.microsoft.com/en-us/support/trust-center/compliance/" target="_blank">Centro de confianza de Azure</a>.

+ [Conceptos básicos de la auditoría de Base de datos SQL de Azure] 
+ [Configuración de la auditoría para su base de datos]
+ [Análisis de registros e informes de auditoría]

##<a id="subheading-1">Conceptos básicos de la auditoría de Base de datos SQL de Azure</a>

La auditoría se configura en el Portal de vista previa de Azure, pero no hay ninguna diferencia si la base de datos se crea mediante el Portal de Azure o con el Portal de vista previa de Azure. La auditoría de Base de datos SQL le permite:

- **Conservar** una traza de auditoría de eventos seleccionados. Defina categorías de acciones de base de datos y eventos para registrar.
- **Informar** sobre la actividad de la base de datos. Use informes preconfigurados en un panel para empezar rápidamente con el informe de actividad y eventos.
- **Analizar** informes. Busque eventos sospechosos, actividades inusuales y tendencias.

Puede auditar las siguientes actividades y eventos:

- **Acceso a datos**
- **Cambios de esquema (DDL)**
- **Cambios de datos (DML)**
- **Cuentas, roles y permisos (DCL)**
- **Excepciones de seguridad**

Para obtener más detalles acerca de las actividades y eventos registrados, consulte la <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Referencia de formato de registro de auditoría (descarga de archivo .doc)</a>. 

También puede elegir la cuenta de almacenamiento donde se guardan los registros de auditoría.

###Cadena de conexión con seguridad habilitada
Cuando se configura la auditoría, Azure proporciona una cadena de conexión con seguridad habilitada para la base de datos. Solamente las aplicaciones cliente que usan esta cadena de conexión registrarán su actividad y eventos, por lo que necesita actualizar las aplicaciones cliente existentes para que usen el nuevo formato de cadena.

Formato de cadena de conexión tradicional: <*nombre de servidor*>.database.windows.net

Cadena de conexión con seguridad habilitada: <*nombre de servidor*>.database.**secure**.windows.net


##<a id="subheading-2"></a>Configuración de la auditoría para su base de datos

1. Inicie el <a href="https://portal.azure.com" target="_blank">Portal de vista previa de Azure</a> en https://portal.azure.com. También puede iniciar el <a href= "https://manage.windowsazure.com/" target="_bank">Portal clásico de Azure</a> en https://manage.windowsazure.com/. Consulte los detalles que aparecen a continuación.
2. Vaya al cuadro de configuración de la base de datos que desea auditar. Desplácese a la sección **Operaciones** y, a continuación, haga clic en **Auditoría** para habilitar la auditoría e inicie el cuadro de configuración de auditoría.

	![][1]

3. En el cuadro de configuración de auditoría, seleccione la cuenta de almacenamiento de Azure donde se guardarán los registros. **Sugerencia:** Use la misma cuenta de almacenamiento para todas las bases de datos auditadas para obtener el máximo rendimiento de las plantillas de informes preconfigurados.

	![][2]

4. En **Opciones de auditoría**, haga clic en **Todo** para registrar todos los eventos o elija tipos de evento individuales.

	![][3]

5. Active la casilla **Guardar esta configuración como predeterminada** si desea que esta configuración se aplique a todas las bases de datos futuras en el servidor y a cualquiera que todavía no tenga configurada la auditoría. Puede anular la configuración más tarde para cada base de datos siguiendo estos mismos pasos. 

6. Haga clic en **Mostrar cadenas de conexión de base de datos** y copie la cadena de conexión con seguridad habilitada apropiada o anote dicha cadena para la aplicación. Utilice esta cadena para cualquier aplicación cliente cuya actividad desee auditar.

	![][5]

7. Haga clic en **Aceptar**.



##<a id="subheading-3">Análisis de registros e informes de auditoría</a>

Los registros de auditoría se agregan en una sola tabla de Almacenamiento de Azure llamada **AuditLogs** en la cuenta de almacenamiento de Azure que eligió durante la configuración. Puede ver archivos de registro usando una herramienta como el <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Explorador de almacenamiento de Azure</a>.

Hay disponible una plantilla de informes del panel preconfigurados como <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">hoja de cálculo de Excel descargable</a> para ayudarle a analizar rápidamente los datos de registro. Para utilizar la plantilla en los registros de auditoría, necesita Excel 2013 o posterior y Power Query, que puede descargar <a href="http://www.microsoft.com/en-us/download/details.aspx?id=39379">aquí</a>. 

La plantilla contiene datos de ejemplo ficticios y puede configurar Power Query para importar el registro de auditoría directamente desde la cuenta de almacenamiento de Azure. 

Para obtener instrucciones más detalladas sobre el trabajo con la plantilla de informes, lea el documento <a href="http://go.microsoft.com/fwlink/?LinkId=506731">Procedimiento (descarga de documento)</a>.

![][6]


##<a id="subheading-4"></a>Configuración de la auditoría para su base de datos con el Portal clásico de Azure

1. Inicie el <a href= "https://manage.windowsazure.com/" target="_bank">Portal clásico de Azure</a> en https://manage.windowsazure.com/. 
2. Haga clic en la base de datos que desea auditar y, a continuación, haga clic en la pestaña **Vista previa de auditoría y seguridad**.
3. En la sección de auditoría, haga clic en "Habilitar".

![][7]

4. Edite el **TIPO DE EVENTO** según corresponda.

![][8]

5. Seleccione una **CUENTA DE ALMACENAMIENTO**.
6. Haga clic en **GUARDAR**.
7. Haga clic en **Mostrar la cadena de conexión segura** de la cadena de conexión.


##<a id="subheading-3">Prácticas para el uso en producción</a>
La descripción de esta sección se refiere a las capturas de pantalla anteriores. Es posible usar el <a href="https://portal.azure.com" target="_blank">Portal de vista previa de Azure</a> o <a href= "https://manage.windowsazure.com/" target="_bank">Portal clásico de Azure</a> .
 

##<a id="subheading-4"></a>Acceso habilitado por seguridad

En el entorno de producción, es probable que necesite auditar todo el tráfico a la base de datos desde todas las aplicaciones y herramientas. Para eso, modifique **Acceso habilitado por seguridad** de *Opcional* a *Requerido* y guarde la directiva. Una vez que se configure *Requerido*, no habrá opción de tener acceso a la base de datos a través de la cadena de conexión original, sino que solo a través de la cadena de conexión habilitada por seguridad.


![][9]


##<a id="subheading-4"></a>Regeneración de clave de almacenamiento

En el entorno de producción, es probable que actualice periódicamente las claves de almacenamiento. El servicio de auditoría no guarda las claves de la cuenta de almacenamiento. Después de Guardar, se genera una clave de firma de acceso compartido (SAS) de solo escritura para la tabla de auditoría (únicamente el cliente puede leer los registros de auditoría). Con este fin, cuando actualice sus claves deberá volver a guardar la directiva. El proceso es el siguiente:


1. En el cuadro de configuración de auditoría (descrito anteriormente en la sección de auditoría de configuración), cambie la **Clave de acceso de almacenamiento** de *Principal* a *Secundaria* y presione **GUARDAR**.
![][10]
2. Vaya al cuadro de configuración de almacenamiento y **regenere** la *clave de acceso primaria*.

3. Vuelva al cuadro de configuración de auditoría, cambie la **Clave de acceso de almacenamiento** de *Secundaria* a *Principal* y presione **GUARDAR**.

4. Vuelva a la interfaz de usuario de almacenamiento y **regenere** la *clave de acceso secundaria* (como preparación para el siguiente ciclo de actualización de las claves).
  
##<a id="subheading-4"></a>Automatización
Para PowerShell, consulte el <a href="https://github.com/Azure/azure-powershell" target="_blank">SDK de PowerShell</a>.

Para la API de REST, consulte <a href="http://download.microsoft.com/download/D/8/D/D8D90BA1-977F-466B-A839-7823FF37FD02/04-Azure SQL DB Auditing REST API.docx">API de REST de Base de datos SQL de Azure</a>





<!--Anchors-->
[Conceptos básicos de la auditoría de Base de datos SQL de Azure]: #subheading-1
[Configuración de la auditoría para su base de datos]: #subheading-2
[Análisis de registros e informes de auditoría]: #subheading-3
[Configuración de la auditoría para su base de datos con el Portal clásico de Azure]: #subheading-4


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






<!--Link references-->
[Vínculo 1 a otro tema de documentación de azure.microsoft.com]: ../virtual-machines-windows-tutorial/
[Vínculo 2 a otro tema de documentación de azure.microsoft.com]: ../web-sites-custom-domain-name/
[Vínculo 3 a otro tema de documentación de azure.microsoft.com]: ../storage-whatis-account/


<!--HONumber=35_1-->
