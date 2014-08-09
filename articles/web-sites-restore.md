<properties linkid="web-sites-restore" urlDisplayName="Restore a Microsoft Azure web site" pageTitle="Restore a Microsoft Azure web site" metaKeywords="Azure Web Sites, Restore, restoring" description="Learn how to restore your Azure web sites from backup." metaCanonical="" services="web-sites" documentationCenter="" title="Restore a Microsoft Azure web site" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

Restauración de un sitio web de Microsoft Azure
===============================================

En este artículo se muestra cómo restaurar un sitio web del que ha hecho previamente una copia de seguridad con la característica de copias de seguridad de sitios web de Azure. Para obtener más información, consulte [Copias de seguridad de sitios web de Microsoft Azure](http://www.windowsazure.com/es-es/documentation/articles/web-sites-backup/).

La característica de restauración de sitios web de Azure le permite restaurar, siempre que lo desee, el sitio web a un estado anterior, o bien crear un nuevo sitio en función de una de las copias de seguridad del sitio original. La creación de un nuevo sitio web que se ejecuta en paralelo con la versión más reciente puede resultar útil para la prueba A/B.

La característica de restauración, disponible en la pestaña Copias de seguridad en el portal Sitios web de Azure, solo está disponible en el modo estándar.

En este artículo
----------------

-   [Restauración de un sitio web de Azure desde una copia de seguridad anterior](#PreviousBackup)
-   [Restauración de un sitio web de Azure directamente desde una cuenta de almacenamiento](#StorageAccount)
-   [Elección de la configuración de restauración del sitio web e inicio de la operación de restauración](#RestoreSettings)
-   [Consulta de los registros de operaciones](#OperationLogs)

## Restauración de un sitio web de Azure desde una copia de seguridad anterior

1.  En la pestaña **Copias de seguridad**, haga clic en **Restore Now** en la barra de comandos en la parte inferior de la página del portal. Aparecerá el cuadro de diálogo **Restore Now**.

    ![Elegir origen de copia de seguridad](./media/web-sites-restore/01ChooseBackupSource.png)

2.  En **Elegir origen de copia de seguridad**, seleccione **Copia de seguridad anterior de este sitio web**.
3.  Seleccione la fecha de la copia de seguridad que desea restaurar y, a continuación, haga clic en la flecha derecha para continuar.
4.  Siga los pasos de la sección [Elección de la configuración de restauración del sitio web](#RestoreSettings) más adelante en este artículo.

## Restauración de un sitio web de Azure directamente desde una cuenta de almacenamiento

1.  En la pestaña **Copias de seguridad**, haga clic en **Restore Now** en la barra de comandos en la parte inferior de la página del portal. Aparecerá el cuadro de diálogo **Restore Now**.

    ![Elegir origen de copia de seguridad](./media/web-sites-restore/01ChooseBackupSource.png)

2.  En **Elegir origen de copia de seguridad**, seleccione **Archivo de cuenta de almacenamiento**. Aquí puede especificar directamente la dirección URL para el archivo de cuenta de almacenamiento, o bien haga clic en el icono de carpeta para desplazarse al almacenamiento de blobs y especifique el archivo de copia de seguridad. Este ejemplo elige el icono de carpeta.

    ![Archivo de cuenta de almacenamiento](./media/web-sites-restore/02StorageAccountFile.png)

3.  Haga clic en el icono de carpeta para abrir el cuadro de diálogo **Buscar en almacenamiento en la nube**.

    ![Buscar en almacenamiento en la nube](./media/web-sites-restore/03BrowseCloudStorage.png)

4.  Expanda el nombre de la cuenta de almacenamiento que desea utilizar y, a continuación, seleccione **websitebackups**, que contiene las copias de seguridad.
5.  Seleccione el archivo zip que contiene la copia de seguridad que desea restaurar y, a continuación, haga clic en **Open**.
6.  Se selecciona el archivo de la cuenta de almacenamiento y se muestra en el cuadro correspondiente. Haga clic en la flecha derecha para continuar.

    ![Archivo de cuenta de almacenamiento seleccionado](./media/web-sites-restore/04StorageAccountFileSelected.png)

7.  Continúe con la sección siguiente, [Elección de la configuración de restauración del sitio web e inicio de la operación de restauración](#RestoreSettings).

## Elección de la configuración de restauración del sitio web e inicio de la operación de restauración 
1. En **Elegir la configuración de restauración del sitio web**, **Restaurar en**, seleccione **Sitio web actual** o **Nueva instancia de sitio web**.

	 ![Elegir la configuración de restauración del sitio web](ChooseRestoreSettings)

     Si selecciona **Sitio web actual**, el sitio web existente se sobrescribirá por la copia de seguridad que haya seleccionado (restauración destructiva). Todos los cambios realizados en el sitio web desde la hora de la copia de seguridad elegida se quitarán de manera permanente y la operación de restauración no se puede realizar. Durante la operación de restauración, el sitio web actual estará temporalmente no disponible y se le advertirá de este efecto.

     Si selecciona **Nueva instancia de sitio web**, se creará un nuevo sitio web en la misma región con el nombre que haya especificado. (De manera predeterminado, el nuevo nombre es **restored-** *oldWebSiteName*.) 

     El sitio que restaure contendrá el mismo contenido y la misma configuración realizados en el portal del sitio original. También incluirá cualquier base de datos que haya elegido incluir en el paso siguiente.

1.  Si desea restaurar una base de datos junto con el sitio web, en **Bases de datos incluidas**, seleccione el nombre del servidor de la base de datos en donde desea restaurar la base de datos utilizando el menú desplegable en **Restore To**. También puede optar por crear un nuevo servidor de base de datos en donde realizar la restauración o elegir **No restaurar** para no restaurar la base de datos, que es el valor predeterminado.

    Después de haber elegido el nombre del servidor, especifique el nombre de la base de datos de destino en donde desea realizar la restauración en el cuadro **Database Name**.

    Si la restauración incluye una o varias bases de datos, puede seleccionar **Ajustar automáticamente las cadenas de conexión** para actualizar las cadenas de conexión almacenadas en la copia de seguridad para apuntar a la nueva base de datos, o servidor de base de datos, lo que proceda. Debe comprobar que toda la funcionalidad relacionada con las bases de datos funciona de la manera prevista una vez finalizada la restauración.

    ![Elegir host del servidor de la base de datos](./media/web-sites-restore/06ChooseDBServer.png)

    > [WACOM.NOTE] No puede restaurar una base de datos SQL con el mismo nombre de SQL Server. Debe elegir otro nombre de base de datos o un host diferente de SQL Server en donde restaurar la base de datos.

    > [WACOM.NOTE] Puede restaurar una base de datos MySQL con el mismo nombre que el servidor, pero tenga presenta que se borrará el contenido existente almacenado en la base de datos MySQL.

2.  Si elige restaurar una base de datos existente, deberá indicar un nombre de usuario y una contraseña. Si elige restaurar en una nueva base de datos, deberá indicar un nuevo nombre de base de datos:

    ![Restaurar en una nueva base de datos SQL](./media/web-sites-restore/07RestoreToNewSQLDB.png)

    Haga clic en la flecha derecha para continuar.

3.  Si elige crear una nueva base de datos, tendrá que proporcionar las credenciales y cualquier otra información de configuración inicial para la base de datos en el siguiente cuadro de diálogo. El ejemplo siguiente muestra una nueva base de datos SQL. (Las opciones para una nueva base de datos MySQL son algo diferentes).

    ![Configuración de la nueva base de datos SQL](./media/web-sites-restore/08NewSQLDBConfig.png)

4.  Haga clic en la marca de verificación para iniciar la operación de restauración. Cuando finaliza, la nueva instancia del sitio web (si es la opción de restauración que ha elegido) será visible en la lista de sitios web del portal.

    ![Sitio web de Contoso restaurado](./media/web-sites-restore/09RestoredContosoWebSite.png)

## Consulta de los registros de operaciones

1.  Para ver información detallada sobre si la operación de restauración del sitio web se ha ejecutado correctamente o no, vaya a la pestaña Panel del sitio web. En la sección **Vista rápida**, en **Servicios de administración**, haga clic en **Registros de operaciones**.

    ![Panel - Vínculo Registros de operaciones](./media/web-sites-restore/10DashboardOperationLogsLink.png)

2.  Obtendrá acceso a la página **Registros de operaciones** de Servicios de administración, donde podrá ver el registro de la operación de restauración en la lista de registros de operaciones:

    ![Página Registros de operaciones de Servicios de administración](./media/web-sites-restore/11ManagementServicesOperationLogsList.png)

3.  Para ver información detallada sobre la operación, seleccione la operación en la lista y, a continuación, haga clic en el botón **Detalles** en la barra de comandos.

    ![Botón Detalles](./media/web-sites-restore/12DetailsButton.png)

    Cuando lo haga, se abrirá la ventana **Detalles de la operación** y mostrará el contenido que se puede copiar del archivo de registro:

    ![Detalles de la operación](./media/web-sites-restore/13OperationDetails.png)



<!-- IMAGES -->
[ChooseBackupSource]: ./media/web-sites-restore/01ChooseBackupSource.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png
