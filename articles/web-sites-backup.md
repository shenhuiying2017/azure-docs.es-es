<properties linkid="web-sites-backup" urlDisplayName="Azure Web Sites Backups" pageTitle="Azure Web Sites Backups" metaKeywords="Azure Web Sites, Backups" description="Learn how to create backups of your Azure web sites." metaCanonical="" services="web-sites" documentationCenter="" title="Azure Web Sites Backups" authors="timamm" solutions="" manager="paulettm" editor="mollybos" />

Copias de seguridad de Sitios web Azure
=======================================

La característica Copia de seguridad y restauración de Sitios web Azure le permite crear fácilmente copias de seguridad del sitio web, de forma manual o automática. Puede restaurar su sitio web a un estado anterior o crear un nuevo sitio web basado en una de las copias de seguridad del sitio original.

Para obtener información acerca de la restauración de un sitio web Azure a partir de una copia de seguridad, consulte [Restauración de un sitio web de Microsoft Azure](http://www.windowsazure.com/es-es/documentation/articles/web-sites-restore/).

En este artículo
----------------

-   [¿Qué se incluye en la copia de seguridad?](#whatsbackedup)
-   [Requisitos y restricciones](#requirements)
-   [Creación de una copia de seguridad manual](#manualbackup)
-   [Configuración de las copias de seguridad automatizadas](#automatedbackups)
-   [Almacenamiento de las copias de seguridad](#aboutbackups)
-   [Notas](#notes)
-   [Pasos siguientes](#nextsteps)
    -   [Información adicional acerca de las cuentas de almacenamiento](#moreaboutstorage)

## ¿Qué se incluye en la copia de seguridad? 
Sitios web Azure guarda la siguiente información en la copia de seguridad:

-   Configuración del sitio web
-   Contenido del archivo del sitio web
-   Todas las bases de datos SQL Server o MySQL que estén conectadas con su sitio (puede elegir las que desee incluir en la copia de seguridad)

Esta información se guarda en una copia de seguridad en la cuenta de almacenamiento de Azure que especifique.

> [WACOM.NOTE] Cada copia de seguridad es una copia completa sin conexión de su sitio web, no una actualización incremental.

## Requisitos y restricciones

-   La característica Copia de seguridad y restauración requiere que es sitio esté en el nivel estándar. Para obtener más información acerca del ajuste de escala del sitios web a un nivel estándar, consulte [Escalación de sitios web](http://www.windowsazure.com/es-es/documentation/articles/web-sites-scale/).

-   La característica Copia de seguridad y restauración requiere una cuenta de almacenamiento de Azure que debe pertenecer a la misma suscripción que el sitio web del que quiere tener una copia de seguridad. Si todavía no tiene una cuenta de almacenamiento, puede crear una haciendo clic en el botón **Almacenamiento** (icono de cuadrícula) del panel izquierdo del portal de Azure y, a continuación, seleccionando **New** en la barra de comandos de la parte inferior. Para obtener más información acerca de las cuentas de almacenamiento de Azure, consulte los [vínculos](#moreaboutstorage) al final de este artículo.

## Creación de una copia de seguridad manual

1.  En el Portal de Azure del sitio web, vaya a la pestaña **Copias de seguridad**.

    ![Página Copias de seguridad](./media/web-sites-backup/01ChooseBackupsPage.png)

2.  Seleccione la cuenta de almacenamiento en la que desea guardar la copia de seguridad del sitio web. La cuenta de almacenamiento debe pertenecer a la misma suscripción que el sitio web del que quiere tener una copia de seguridad.

    ![Selección de la cuenta de almacenamiento](./media/web-sites-backup/02ChooseStorageAccount.png)

3.  En la opción **Bases de datos incluidas**, seleccione las bases de datos que están conectadas con su sitio web (SQL Server o MySQL) y de las que desee guardar una copia de seguridad.

    ![Selección de las bases de datos que desea incluir](./media/web-sites-backup/03IncludedDatabases.png)

    > [WACOM.NOTE] Para que una base de datos aparezca en esta lista, su cadena de conexión debe existir en la sección **Cadenas de conexión** de la pestaña Configure en el portal.

4.  En la barra de comandos, haga clic en **Backup Now**.

    ![Botón Backup Now](./media/web-sites-backup/04BackUpNow.png)

    Verá un mensaje de progreso durante el proceso de realización de la copia de seguridad:

    ![Mensaje de progreso de la copia de seguridad](./media/web-sites-backup/05BackupProgress.png)

Puede realizar una copia de seguridad manual en cualquier momento. Durante la vista previa, no es posible realizar más de 2 copias de seguridad en un período de 24 horas (sujeto a cambios).

## Configuración de las copias de seguridad automatizadas

1.  En la página Copias de seguridad, establezca **Copia de seguridad automatizada** en ON.

    ![Activación de las copias de seguridad automatizadas](./media/web-sites-backup/06SetAutomatedBackupOn.png)

2.  Seleccione la cuenta de almacenamiento en la que desea guardar la copia de seguridad del sitio web. La cuenta de almacenamiento debe pertenecer a la misma suscripción que el sitio web del que quiere tener una copia de seguridad.

    ![Selección de la cuenta de almacenamiento](./media/web-sites-backup/02ChooseStorageAccount.png)

3.  En el cuadro **Frequency**, especifique la frecuencia con la que desea realizar las copias de seguridad automatizadas. (Durante la vista previa, la única unidad de tiempo disponible es el número de días).

    ![Selección de la frecuencia de la copia de seguridad](./media/web-sites-backup/07Frequency.png)

    El número de días debe estar comprendido entre 1 y 90, ambos incluidos (desde una vez al día hasta cada 90 días).

4.  Utilice la opción **Start Date** para especificar la fecha y la hora a las que desea que comience la copia de seguridad automatizada.

    ![Selección de la fecha de inicio](./media/web-sites-backup/08StartDate.png)

    La hora está disponible en incrementos de media hora.

    ![Selección de la hora de inicio](./media/web-sites-backup/09StartTime.png)

    > [WACOM.NOTE] Azure almacena las horas de las copias de seguridad en formato UTC, pero las muestra de acuerdo con la hora del sistema del equipo que esté utilizando para visualizar el portal.

5.  En la sección **Bases de datos incluidas**, seleccione las bases de datos que están conectadas con su sitio web (SQL Server o MySQL) y de las que desee guardar una copia de seguridad. Para que una base de datos aparezca en la lista, su cadena de conexión debe existir en la sección **Cadenas de conexión** de la pestaña Configure en el portal.

    ![Selección de las bases de datos que desea incluir](./media/web-sites-backup/03IncludedDatabases.png)

    > [WACOM.NOTE] Si desea incluir una o varias bases de datos en la copia de seguridad y ha especificado una frecuencia inferior a 7 días, se le advertirá que la realización de copias de seguridad de forma frecuente puede aumentar los costes de su base de datos.

6.  En la barra de comandos, haga clic en el botón **Save** para guardar los cambios de configuración (o seleccione **Discard** si ha decidido no guardarlos).

    ![Botón Save](./media/web-sites-backup/10SaveIcon.png)

## Almacenamiento de las copias de seguridad

Después de realizar una o varias copias de seguridad, estas serán visibles en la pestaña Containers de su cuenta de almacenamiento. Las copias de seguridad permanecerán en un contenedor denominado **websitebackups**. Cada copia de seguridad consta de un archivo .zip que contiene los datos guardados en la copia de seguridad y un archivo .xml que contiene un manifiesto de contenido del archivo .zip.

Los nombres de los archivos de copia de seguridad .zip y .xml constan del nombre de su sitio web, seguido de un carácter de subrayado y una marca de tiempo correspondiente al momento en el que se realizó la copia de seguridad. La marca de tiempo contiene la fecha en el formato AAAAMMDD (en dígitos sin espacios) y la hora en modo de 24 horas con formato UTC (por ejemplo, fabrikam\_201402152300.zip). El contenido de estos archivos se puede descomprimir y examinar en caso de que desee obtener acceso a sus bases de datos sin tener que realizar una restauración del sitio web.

> [WACOM.NOTE] La modificación de los archivos del contenedor **websitebackups** puede ocasionar que la base de datos deje de ser válida y, por lo tanto, no se pueda restaurar.

## Notas

-   Asegúrese de que configura las cadenas de conexión de cada una de las bases de datos correctamente en la pestaña Configure del sitio web, de modo que la característica Copia de seguridad y restauración pueda incluir sus bases de datos.
-   Durante la vista previa, la administración del contenido guardado en una copia de seguridad de su cuenta de almacenamiento permanece bajo su responsabilidad. Si borra una copia de seguridad de su cuenta de almacenamiento y no ha realizado una copia en otro lugar, no podrá restaurar la copia de seguridad más adelante.
-   Aunque puede realizar una copia de seguridad que incluya más de un sitio web en la misma cuenta de almacenamiento, se recomienda crear una cuenta de almacenamiento independiente para cada sitio web a fin de facilitar el mantenimiento.
-   Durante la vista previa, las operaciones de copia de seguridad y restauración solo están disponibles a través del Portal de administración de Azure.

## Pasos siguientes 
Para obtener información acerca de la restauración de un sitio web Azure a partir de una copia de seguridad, consulte [Restauración de un sitio web de Microsoft Azure](http://www.windowsazure.com/es-es/documentation/articles/web-sites-restore/).

### Información adicional acerca de las cuentas de almacenamiento

[¿Qué es una cuenta de almacenamiento?](http://www.windowsazure.com/es-es/documentation/articles/storage-whatis-account/)

[Creación de una cuenta de almacenamiento](http://www.windowsazure.com/es-es/documentation/articles/storage-create-storage-account/)

[Supervisión de una cuenta de almacenamiento](http://www.windowsazure.com/es-es/documentation/articles/storage-monitor-storage-account/)

[Understanding Azure Storage Billing](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

