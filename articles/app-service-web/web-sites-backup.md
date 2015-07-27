<properties 
	pageTitle="Hacer copia de seguridad de una aplicación web en el servicio de aplicaciones de Azure" 
	description="Obtenga información sobre cómo crear copias de seguridad de sus aplicaciones web en el servicio de aplicaciones de Azure." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/03/2015" 
	ms.author="cephalin"/>

# Hacer copia de seguridad de una aplicación web en el servicio de aplicaciones de Azure


La característica Copia de seguridad y restauración de las [Aplicaciones web del servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) le permite crear fácilmente las copias de seguridad de la aplicación web manualmente o automáticamente. Puede restaurar su aplicación web a un estado anterior o crear una nueva aplicación web basada en una de las copias de seguridad de la aplicación original.

Para obtener información sobre cómo restaurar una aplicación web de Azure desde la copia de seguridad, consulte [Restauración de una aplicación web](web-sites-restore.md).

<a name="whatsbackedup"></a>
## ¿Qué se incluye en la copia de seguridad? 
Las aplicaciones web pueden copiar la siguiente información:

* Configuración de la aplicación web
* Contenido de archivos de la aplicación web
* Todas las bases de datos SQL Server o MySQL conectadas a su aplicación (puede elegir las que quiere incluir en la copia de seguridad)

Esta información se guarda en una copia de seguridad en la cuenta de almacenamiento y el contenedor de Azure que especifique.

> [AZURE.NOTE]Cada copia de seguridad es una copia completa sin conexión de su aplicación, no una actualización incremental.

<a name="requirements"></a>
## Requisitos y restricciones

* La característica Copia de seguridad y restauración requiere que el sitio esté en el nivel estándar. Para obtener más información sobre cómo escalar la aplicación web para utilizar el modo estándar, consulte [Escalado de una aplicación web en el servicio de aplicaciones de Azure](web-sites-scale.md). Tenga en cuenta que el modo Premium permite que se realice un mayor número de copias de seguridad diarias en modo estándar.

* La característica Copia de seguridad y restauración requiere una cuenta de almacenamiento y el contenedor de Azure que debe pertenecer a la misma suscripción que la aplicación web del que quiere tener una copia de seguridad. Si aún no tiene una cuenta de almacenamiento, para crear una, haga clic en **Cuenta de almacenamiento** en la hoja **Copias de seguridad** del [Portal de vista previa de Azure](http://portal.azure.com) y elija la **cuenta de almacenamiento** y el **contenedor** en la hoja **Destino**. Para obtener más información sobre las cuentas de almacenamiento de Azure, consulte los [vínculos](#moreaboutstorage) al final de este artículo.

* La característica de copia de seguridad y restauración admite hasta 10 GB de contenido de sitio web y base de datos. Si la característica de copia de seguridad no puede continuar porque la carga supera este límite, se indicará un error en los registros de operación.

<a name="manualbackup"></a>
## Crear una copia de seguridad manual

1. En el portal de Azure, elija la aplicación web en la hoja Aplicaciones web. Esto mostrará los detalles de la aplicación web en una nueva hoja.
2. Seleccione la opción **Configuración**. Se mostrará la hoja **Configuración** donde puede modificar la aplicación web.
	
	![Página Copias de seguridad][ChooseBackupsPage]

3. Elija la opción **Copias de seguridad** en la hoja **Configuración**. Se mostrará la hoja **Copias de seguridad**.
	
4. En la hoja **Copias de seguridad**, elija el destino de la copia de seguridad, para ello seleccione la **Cuenta de almacenamiento** y el **Contenedor**. La cuenta de almacenamiento debe pertenecer a la misma suscripción que la aplicación web de la que quiere tener una copia de seguridad.
	
	![Selección de la cuenta de almacenamiento][ChooseStorageAccount]
	
5. En la opción **Bases de datos incluidas** de la hoja **Copias de seguridad**, seleccione las bases de datos conectadas a su aplicación web (SQL Server o MySQL) de las que quiere hacer una copia.

	> [AZURE.NOTE]Para que una base de datos aparezca en esta lista, su cadena de conexión debe existir en la sección **Cadenas de conexión** de la hoja **Configuración de la aplicación web** del portal.
	
6. En la hoja **Copias de seguridad**, seleccione el **Destino de copia de seguridad**. Debe elegir una cuenta de almacenamiento y un contenedor existentes.
7. En la barra de comandos, haga clic en **Copiar ahora**.
	
	![Botón Backup Now][BackUpNow]
	
	Verá un mensaje de progreso durante el proceso de realización de la copia de seguridad.
	

Puede realizar una copia de seguridad manual en cualquier momento.

<a name="automatedbackups"></a>
## Configuración de copias de seguridad automatizadas

1. En la hoja **Copias de seguridad**, establezca **Copia de seguridad programada** en ACTIVADO.
	
	![Activación de las copias de seguridad automatizadas][SetAutomatedBackupOn]
	
2. Seleccione la cuenta de almacenamiento en la que desea guardar la copia de seguridad de la aplicación web. La cuenta de almacenamiento debe pertenecer a la misma suscripción que la aplicación web de la que quiere tener una copia de seguridad.
	
	![Selección de la cuenta de almacenamiento][ChooseStorageAccount]
	
3. En el cuadro **Frecuencia**, especifique la frecuencia con la que desea realizar las copias de seguridad automatizadas. El número de días debe estar comprendido entre 1 y 90, ambos incluidos (desde una vez al día hasta cada 90 días).
	
4. Use la opción **Fecha de inicio** para especificar la fecha y la hora a las que quiere que comience la copia de seguridad automatizada.
	
	> [AZURE.NOTE]Azure almacena las horas de las copias de seguridad en formato UTC, pero las muestra de acuerdo con la hora del sistema del equipo que esté utilizando para visualizar el portal.
	
5. En la sección **Bases de datos incluidas**, seleccione las bases de datos que están conectadas con su aplicación web (SQL Server o MySQL) y de las que quiera guardar una copia de seguridad. Para que una base de datos aparezca en la lista, su cadena de conexión debe existir en la sección **Cadenas de conexión** de la hoja **Configuración de la aplicación web** del portal.
	
	> [AZURE.NOTE]Si quiere incluir una o varias bases de datos en la copia de seguridad y ha especificado una frecuencia inferior a 7 días, se le advertirá que la realización de copias de seguridad de forma frecuente puede aumentar los costes de su base de datos.
	
6. Además, establezca el valor **Retención (días)** en el número de días que quiera guardar la copia de seguridad.
7. En la barra de comandos, haga clic en el botón **Guardar** para guardar los cambios de configuración (o seleccione **Descartar** si ha decidido no guardarlos).
	
	![Botón Save][SaveIcon]

<a name="notes"></a>
## Notas

* Asegúrese de que configura las cadenas de conexión para cada una de las bases de datos correctamente en la hoja **Configuración de la aplicación web** en **Configuración** de la aplicación web para que la característica de copia de seguridad y restauración pueda incluir las bases de datos.
* Aunque puede realizar una copia de seguridad que incluya más de una aplicación web en la misma cuenta de almacenamiento, se recomienda crear una cuenta de almacenamiento independiente para cada aplicación web con el fin de facilitar el mantenimiento.

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

<a name="partialbackups"></a>
## Realización de una copia de seguridad de una parte de la aplicación web

En ocasiones, no deseará realizar una copia de seguridad de todo el contenido de la aplicación web. Estos son algunos ejemplos:

-	[Configura copias de seguridad semanales](web-sites-backup.md#configure-automated-backups) de la aplicación web que contiene contenido estático que nunca cambia, como entradas de blog antiguas o imágenes.
-	Su aplicación web tiene más de 10 GB de contenido (que es la cantidad máxima de la que puede realizar una copia de seguridad a la vez).
-	No desea realizar copias de seguridad de los archivos de registro.

Las copias de seguridad parciales le permitirá elegir exactamente de qué archivos desea realizar la copia de seguridad.

### Exclusión de los archivos de la copia de seguridad

Para excluir archivos y carpetas de las copias de seguridad, cree u archivo `_backup.filter` en la carpeta wwwroot de la aplicación web y especifique la lista de archivos y carpetas que desea excluir. Es una manera fácil de obtener acceso a ella a través de la [consola Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console).

Suponga que tiene una aplicación web contiene archivos de registro e imágenes estáticas de los últimos años que nunca van a cambiar. Ya tiene una copia de seguridad completa de la aplicación web que incluye las imágenes anteriores. Ahora me gustaría hacer una copia de seguridad de la aplicación web todos los días, pero no quiere pagar para almacenar los archivos de registro o los archivos de imagen estática que no van a cambiar.

![Carpeta de registros][LogsFolder] ![Carpeta de imágenes][ImagesFolder]
	
Los pasos siguientes muestran cómo podría excluir estos archivos de la copia de seguridad.

1. Vaya a `http://{yourapp}.scm.azurewebsites.net/DebugConsole` e identifique las carpetas que desee excluir de las copias de seguridad. En este ejemplo, desearía excluir los siguientes archivos y carpetas que aparecen en la interfaz de usuario:

		D:\home\site\wwwroot\Logs
		D:\home\LogFiles
		D:\home\site\wwwroot\Images\2013
		D:\home\site\wwwroot\Images\2014
		D:\home\site\wwwroot\Images\brand.png

	[AZURE.NOTE]La última línea muestra que puede excluir archivos de individuales, así como carpetas.

2. Cree un archivo llamado `_backup.filter` y ponga la lista anterior en el archivo, pero quite `D:\home`. Enumere un directorio o archivo por línea. Por lo tanto, el contenido del archivo debe ser:

    \\site\\wwwroot\\Logs \\LogFiles \\site\\wwwroot\\Images\\2013 \\site\\wwwroot\\Images\\2014 \\site\\wwwroot\\Images\\brand.png

3. Cargue este archivo en el directorio `D:\home\site\wwwroot` de su sitio con [ftp](web-sites-deploy.md#ftp) o cualquier otro método. Si lo desea, puede crear el archivo directamente en `http://{yourapp}.scm.azurewebsites.net/DebugConsole` e insertar el contenido.

4. Ejecute copias de seguridad de la misma forma que lo haría normalmente, [manual](#create-a-manual-backup) o [automáticamente](#configure-automated-backups).

Ahora, los archivos y carpetas que se especifican en `_backup.filter` se excluirán de la copia de seguridad. En este ejemplo, ya no se realizará la copia de seguridad de los archivos de registro y los archivos de imagen de 2013 y 2014 , ni de brand.png.

>[AZURE.NOTE]La restauración de las copias de seguridad parciales del sitio se realizan de la misma manera que se hace para [restaurar una copia de seguridad regular](web-sites-restore.md). El proceso de restauración hará lo correcto.
>
>Cuando se restaura una copia de seguridad completa, se reemplaza todo el contenido en el sitio por lo que haya en la copia de seguridad. Si un archivo está en el sitio pero no en la copia de seguridad, se elimina. Sin embargo, cuando se restaura una copia de seguridad parcial, cualquier contenido que se encuentre en uno de los directorios en la lista negra, o cualquier archivo en la lista negra, permanecerá tal y como está.

<a name="aboutbackups"></a>

## Cómo se almacenan las copias de seguridad

Después de realizar una o varias copias de seguridad de la aplicación web, las copias estarán visibles en la hoja **Contenedores** de la cuenta de almacenamiento, así como la aplicación web. En la cuenta de almacenamiento, cada copia de seguridad consta de un archivo .zip que contiene los datos de copia de seguridad y el archivo .xml que contiene un manifiesto del contenido del archivo zip. Puede descomprimir y examinar estos archivos si desea disponer de acceso a las copias de seguridad sin tener que realizar una restauración de la aplicación web.

La copia de seguridad de la base de datos para la aplicación web se almacena en la raíz del archivo .zip. En bases de datos de SQL, este es un archivo BACPAC (sin extensión de archivo) y se puede importar. Para crear una nueva base de datos SQL basándose en la exportación del BACPAC, consulte[Importación de un archivo de BACPAC para crear una nueva base de datos de usuario](http://technet.microsoft.com/library/hh710052.aspx).

> [AZURE.WARNING]La modificación de los archivos del contenedor **websitebackups** puede ocasionar que la base de datos deje de ser válida y, por lo tanto, no se pueda restaurar.

<a name="bestpractices"></a>
##Prácticas recomendadas

Si se produce un error o un desastre natural, desea asegurarse de que está preparado con antelación con una copia de seguridad y una estrategia de restauración existentes.

La estrategia de copia de seguridad debe ser similar a la siguiente:

-	Tenga al menos una copia de seguridad completa de la aplicación web.
-	Realice copias de seguridad parciales de la aplicación web después de tener una copia de seguridad completa.

Su estrategia de restauración debe ser similar a la siguiente:
 
-	Cree una [ranura de ensayo](web-sites-staged-publishing.md) para la aplicación web.
-	Restaure la copia de seguridad completa de la aplicación web en la ranura de ensayo.
-	Restaure la copia de seguridad parcial más reciente sobre la restauración de la copia de seguridad completa, también en la ranura de ensayo.
-	Pruebe la restauración para ver que la aplicación de ensayo funciona correctamente.
-	[Cambie](web-sites-staged-publishing.md#Swap) la aplicación web de ensayo a la ranura de producción.

>[AZURE.NOTE]Pruebe siempre el proceso de restauración. Para obtener más información, consulte [Muy bueno](http://axcient.com/blog/one-thing-can-derail-disaster-recovery-plan/). Por ejemplo, algunas plataformas de blog, como [Ghost](https://ghost.org/) tienen advertencias explícitas sobre su comportamiento durante una copia de seguridad. Al probar el proceso de restauración, puede detectar estas advertencias cuando no se haya producido aún un error o un desastre.

<a name="nextsteps"></a>
## Pasos siguientes
Para obtener información sobre cómo restaurar una aplicación web desde la copia de seguridad, consulte [Restauración de una aplicación web en el servicio de aplicaciones de Azure](web-sites-restore.md).

Para comenzar con Azure, vea [Evaluación gratuita de Microsoft Azure](/pricing/free-trial/).


<a name="moreaboutstorage"></a>
### Información adicional acerca de las cuentas de almacenamiento

[¿Qué es una cuenta de almacenamiento?](../storage-whatis-account.md)

[Creación de una cuenta de almacenamiento](../storage-create-storage-account/)

[Supervisión de una cuenta de almacenamiento](../storage-monitor-storage-account.md)

[Descripción de la facturación de Almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
 

<!---HONumber=July15_HO3-->