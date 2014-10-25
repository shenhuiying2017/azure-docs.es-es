<properties title="Get started with SQL database auditing" pageTitle="Get started with SQL database auditing | Azure" description="Get started with SQL database auditing" metaKeywords="" services="sql-database" solutions="data-management" documentationCenter="" authors="jeffreyg" videoId="" scriptId=""  />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jeffreyg"/>

# Introducción a la auditoría de bases de datos SQL

La auditoría de Base de datos SQL de Azure realiza un seguimiento de los eventos de base de datos y escribe eventos auditados en un registro de auditoría en la cuenta de Almacenamiento de Azure. La auditoría está disponible para los niveles de servicio Basic, Standard y Premium. Para usar la auditoría, [suscríbase a la vista previa][].

La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas.

Las herramientas de auditoría posibilitan y facilitan la observancia de estándares reguladores pero no garantizan el cumplimiento. Para obtener más información acerca de los programas de Azure compatibles con la observancia de estándares, consulte el [Centro de confianza de Azure][].

-   [Conceptos básicos de la auditoría de Base de datos SQL de Azure][]
-   [Configuración de la auditoría para su base de datos][]
-   [Análisis de registros e informes de auditoría][]

## <span id="subheading-1"></span>Conceptos básicos de la auditoría de Base de datos SQL de Azure</a>

La auditoría se configura en el Portal de vista previa de Azure, pero no hay ninguna diferencia si la base de datos se crea mediante el Portal de Azure o con el Portal de vista previa de Azure. La auditoría de Base de datos SQL le permite:

-   **Conservar** una traza de auditoría de eventos seleccionados. Defina categorías de acciones de base de datos y eventos para registrar.
-   **Informar** sobre la actividad de la base de datos. Use informes preconfigurados en un panel para empezar rápidamente con el informe de actividad y eventos.
-   **Analizar** informes. Busque eventos sospechosos, actividades inusuales y tendencias.

Puede auditar las siguientes actividades y eventos:

-   **Acceso a datos**
-   **Cambios de esquema (DDL)**
-   **Cambios de datos (DML)**
-   **Cuentas, roles y permisos (DCL)**
-   **Excepciones de seguridad**

Para obtener más detalles acerca de las actividades y eventos registrados, consulte [Referencia del formato del registro de auditoría (descarga de archivo .doc)][].

También puede elegir la cuenta de almacenamiento donde se guardan los registros de auditoría.

### Cadena de conexión con seguridad habilitada

Cuando se configura la auditoría, Azure proporciona una cadena de conexión con seguridad habilitada para la base de datos. Solamente las aplicaciones cliente que usan esta cadena de conexión registrarán su actividad y eventos, por lo que necesita actualizar las aplicaciones cliente existentes para que usen el nuevo formato de cadena.

Formato de cadena de conexión tradicional: *\<nombre de servidor*\>.database.windows.net

Cadena de conexión con seguridad habilitada: *\<nombre de servidor*\>.database.**secure**.windows.net

## <span id="subheading-2"></span></a>Configuración de la auditoría para su base de datos

1.  [Suscripción a la vista previa de auditoría][suscríbase a la vista previa].
2.  Asegúrese también de [suscribirse a la vista previa de nuevos niveles de servicio][] y cree una base de datos Basic, Standard o Premium.
3.  Inicie el [Portal de vista previa de Azure][] en <https://portal.azure.com>.
4.  Haga clic en la base de datos que desee auditar y después en **Vista previa de auditoría** para habilitar la vista previa de auditoría e iniciar el cuadro de configuración de auditoría.

    ![](./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png)

5.  En el cuadro de configuración de auditoría, seleccione la cuenta de almacenamiento de Azure donde se guardarán los registros. **Sugerencia:** Use la misma cuenta de almacenamiento para todas las bases de datos auditadas para obtener el máximo rendimiento de las plantillas de informes preconfigurados.

    ![][1]

6.  En **Opciones de auditoría**, haga clic en **Todo** para registrar todos los eventos o elija tipos de evento individuales.

    ![][2]

7.  Active la casilla **Guardar esta configuración como predeterminada** si desea que esta configuración se aplique a todas las bases de datos futuras en el servidor y a cualquiera que todavía no tenga configurada la auditoría. Puede anular la configuración más tarde para cada base de datos siguiendo estos mismos pasos.

    ![][3]

8.  Haga clic en **Mostrar cadenas de conexión de base de datos** y copie la cadena de conexión con seguridad habilitada apropiada o anote dicha cadena para la aplicación. Utilice esta cadena para cualquier aplicación cliente cuya actividad desee auditar.

    ![][4]

9.  Haga clic en **OK**.

## <span id="subheading-3"></span>Análisis de registros e informes de auditoría</a>

Los registros de auditoría se agregan en una sola tabla de Almacenamiento de Azure llamada **AuditLogs** en la cuenta de almacenamiento de Azure que eligió durante la configuración. Puede ver archivos de registro usando una herramienta como el [Explorador de almacenamiento de Azure][].

Hay una plantilla de informe de panel preconfigurado disponible como una [hoja de cálculo de Excel descargable][] para ayudarle a analizar datos de registro rápidamente. Para utilizar la plantilla en los registros de auditoría, necesita Excel 2013 o posterior y Power Query, que puede descargar [aquí][].

La plantilla contiene datos de ejemplo ficticios y puede configurar Power Query para importar el registro de auditoría directamente desde la cuenta de almacenamiento de Azure.

Para obtener instrucciones más detalladas sobre el trabajo con la plantilla de informes, lea el documento [Cómo (.doc descargable)][].

![][5]

<!--Anchors--> <!--Image references--> <!--Link references-->

  [suscríbase a la vista previa]: http://go.microsoft.com/fwlink/?LinkId=404163
  [Centro de confianza de Azure]: http://azure.microsoft.com/en-us/support/trust-center/compliance/
  [Conceptos básicos de la auditoría de Base de datos SQL de Azure]: #subheading-1
  [Configuración de la auditoría para su base de datos]: #subheading-2
  [Análisis de registros e informes de auditoría]: #subheading-3
  [Referencia del formato del registro de auditoría (descarga de archivo .doc)]: http://go.microsoft.com/fwlink/?LinkId=506733
  [suscribirse a la vista previa de nuevos niveles de servicio]: https://account.windowsazure.com/PreviewFeatures?fid=premiumdb
  [Portal de vista previa de Azure]: https://portal.azure.com
  
  [1]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
  [2]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
  [3]: ./media/sql-database-auditing-get-started/sql-database-get-started-saveconfigasdefault.png
  [4]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
  [Explorador de almacenamiento de Azure]: http://azurestorageexplorer.codeplex.com/
  [hoja de cálculo de Excel descargable]: http://go.microsoft.com/fwlink/?LinkId=403540
  [aquí]: http://www.microsoft.com/en-us/download/details.aspx?id=39379
  [Cómo (.doc descargable)]: http://go.microsoft.com/fwlink/?LinkId=506731
  [5]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
