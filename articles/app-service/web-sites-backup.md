---
title: "Realizar una copia de seguridad de la aplicación en Azure"
description: "Obtenga información sobre cómo crear copias de seguridad de sus aplicaciones en Azure App Service."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: 435370a8758d439a5fcce2e04efd11b4aaaf0357
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2017
---
# <a name="back-up-your-app-in-azure"></a>Realizar una copia de seguridad de la aplicación en Azure
La característica Copia de seguridad y restauración de [Azure App Service](app-service-web-overview.md) le permite crear fácilmente las copias de seguridad de la aplicación manualmente o con base en una programación. Puede restaurar la aplicación a una instantánea de un estado anterior sobrescribiendo la aplicación existente o restaurando en otra aplicación. 

Para obtener información sobre cómo restaurar una aplicación desde la copia de seguridad, vea [Restauración de una aplicación en el Servicio de aplicaciones de Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>¿Qué se incluye en la copia de seguridad?
App Service puede hacer una copia de seguridad de la siguiente información en una cuenta de almacenamiento de Azure y el contenedor que ha configurado para que lo utilice la aplicación. 

* Configuración de la aplicación
* Contenido del archivo
* Base de datos conectada a la aplicación

Las siguientes soluciones de base de datos son compatibles con la característica de copia de seguridad: 
   - [SQL Database](https://azure.microsoft.com/en-us/services/sql-database/)
   - [Azure Database for MySQL (versión preliminar)](https://azure.microsoft.com/en-us/services/mysql)
   - [Azure Database for PostgreSQL (versión preliminar)](https://azure.microsoft.com/en-us/services/postgres)
   - [MySQL en aplicación](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  Cada copia de seguridad es una copia completa sin conexión de su aplicación, no una actualización incremental.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Requisitos y restricciones
* La característica Copia de seguridad y restauración requiere que el plan de App Service tenga el nivel **estándar** o **premium**. Para obtener más información sobre cómo escalar el plan de App Service para usar un nivel superior, vea [Escalación de una aplicación web en Azure App Service](web-sites-scale.md).  
  El nivel **premium** permite realizar un mayor número de copias de seguridad diarias que el nivel **estándar**.
* Necesita una cuenta de almacenamiento de Azure y un contenedor en la misma suscripción que la aplicación de la que quiere realizar una copia de seguridad. Para obtener más información acerca de las cuentas de almacenamiento de Azure, consulte los [vínculos](#moreaboutstorage) al final de este artículo.
* Puede realizar copias de seguridad de hasta 10 GB de contenido de base de datos y aplicaciones. Si el tamaño de la copia de seguridad supera este límite, obtendrá un error.

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Crear una copia de seguridad manual
1. En [Azure Portal](https://portal.azure.com), vaya a la página de la aplicación y seleccione **Copias de seguridad**. Se mostrará la página **Copias de seguridad**.
   
    ![Página Copias de seguridad][ChooseBackupsPage]
   
   > [!NOTE]
   > Si ve el mensaje siguiente, haga clic en él para actualizar su plan de App Service antes de continuar con las copias de seguridad.
   > Vea [Escalado vertical de aplicaciones en Azure](web-sites-scale.md) para obtener más información.  
   > ![Selección de la cuenta de almacenamiento](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. En la página **Copia de seguridad**, haga clic en **Configurar**
![Haga clic en Configurar](./media/web-sites-backup/ClickConfigure1.png)
3. En la página **Configuración de copia de seguridad**, haga clic en **Almacenamiento: no configurado** para configurar una cuenta de almacenamiento.
   
    ![Selección de la cuenta de almacenamiento][ChooseStorageAccount]
4. Elija el destino de copia de seguridad; para ello, seleccione una **Cuenta de almacenamiento** y un **Contenedor**. La cuenta de almacenamiento debe pertenecer a la misma suscripción que la aplicación de la que quiere realizar una copia de seguridad. Si lo desea, puede crear una nueva cuenta de almacenamiento o un nuevo contenedor en las páginas correspondientes. Cuando haya terminado, haga clic en **Seleccionar**.
   
    ![Selección de la cuenta de almacenamiento](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. En la página **Configuración de copia de seguridad** que sigue abierta, puede configurar **Copia de seguridad de la base de datos**, seleccionar las bases de datos que desee incluir en las copias de seguridad (SQL Database o MySQL) y después haga clic en **Aceptar**.  
   
    ![Selección de la cuenta de almacenamiento](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > Para que una base de datos aparezca en esta lista, su cadena de conexión debe existir en la sección **Cadenas de conexión** de la página **Configuración de la aplicación** de la aplicación.
   > 
   > 
6. En la página **Configuración de copia de seguridad**, haga clic en **Guardar**.    
7. En la página **Copias de seguridad**, haga clic en **Copia de seguridad**.
   
    ![Botón Backup Now][BackUpNow]
   
    Verá un mensaje de progreso durante el proceso de realización de la copia de seguridad.

Una vez configurados tanto la cuenta de almacenamiento como el contenedor, puede iniciar una copia de seguridad manual en cualquier momento.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Configuración de copias de seguridad automatizadas
1. En la página **Configuración de copia de seguridad**, establezca **Copia de seguridad programada** en **Activada**. 
   
    ![Selección de la cuenta de almacenamiento](./media/web-sites-backup/05ScheduleBackup1.png)
2. Se mostrarán las opciones de programación de copia de seguridad. Establezca **Copia de seguridad programada** en **Activada**, configure la programación de la copia de seguridad como desee y haga clic en **Aceptar**.
   
    ![Activación de las copias de seguridad automatizadas][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Configuración de copias de seguridad parciales
En ocasiones, no querrá realizar una copia de seguridad de todo el contenido de la aplicación. Estos son algunos ejemplos:

* [Configure copias de seguridad semanales](web-sites-backup.md#configure-automated-backups) de la aplicación que contiene contenido estático que nunca cambia, como entradas de blog antiguas o imágenes.
* Su aplicación tiene más de 10 GB de contenido (que es la cantidad máxima de la que puede realizar una copia de seguridad a la vez).
* No desea realizar copias de seguridad de los archivos de registro.

Las copias de seguridad parciales permiten elegir exactamente de qué archivos desea realizar la copia de seguridad.

### <a name="exclude-files-from-your-backup"></a>Exclusión de los archivos de la copia de seguridad
Suponga que tiene una aplicación que contiene archivos de registro e imágenes estáticas de los que se ha hecho una copia de seguridad una vez y nunca van a cambiar. En tales casos puede excluir las carpetas y los archivos para que no se almacenen en las futuras copias de seguridad. Para excluir archivos y carpetas de las copias de seguridad, cree un archivo `_backup.filter` en la carpeta `D:\home\site\wwwroot` de la aplicación. Especifique la lista de archivos y carpetas que desea excluir en este archivo. 

Una manera fácil de acceder a los archivos es usar Kudu. Haga clic en la configuración **Herramientas avanzadas-> Ir** correspondiente a la aplicación web para acceder a Kudu.

![Portal de uso de Kudu][kudu-portal]

Identifique las carpetas que desee excluir de las copias de seguridad.  Por ejemplo, desea filtrar los archivos y la carpeta resaltados.

![Carpeta de imágenes][ImagesFolder]

Cree un archivo llamado `_backup.filter` y ponga la lista anterior en el archivo, pero quite `D:\home`. Enumere un directorio o archivo por línea. Por lo tanto, el contenido del archivo debe ser:
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

Cargue el archivo `_backup.filter` en el directorio `D:\home\site\wwwroot\` de su sitio mediante [ftp](app-service-deploy-ftp.md) o cualquier otro método. Si lo desea, puede crear el archivo directamente mediante `DebugConsole` de Kudu e insertar el contenido.

Ejecute copias de seguridad de la misma forma que lo haría normalmente, [manual](#create-a-manual-backup) o [automáticamente](#configure-automated-backups). Ahora, todos archivos y las carpetas que se especifican en `_backup.filter` se excluirán de las copias de seguridad futuras programadas o iniciadas manualmente. 

> [!NOTE]
> La restauración de las copias de seguridad parciales del sitio se realizan de la misma manera que se hace para [restaurar una copia de seguridad regular](web-sites-restore.md). El proceso de restauración hace lo correcto.
> 
> Cuando se restaura una copia de seguridad completa, se reemplaza todo el contenido en el sitio por lo que haya en la copia de seguridad. Si un archivo está en el sitio pero no en la copia de seguridad, se elimina. Sin embargo, cuando se restaura una copia de seguridad parcial, cualquier contenido que se encuentre en uno de los directorios en la lista negra, o cualquier archivo en la lista negra, permanecerá tal y como está.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Cómo se almacenan las copias de seguridad
Después de realizar una o varias copias de seguridad de la aplicación, dichas copias de seguridad estarán visibles en la página **Contenedores** de la cuenta de almacenamiento, así como en la aplicación. En la cuenta de almacenamiento, cada copia de seguridad consta de un archivo `.zip` que contiene los datos de copia de seguridad y un archivo `.xml` que contiene un manifiesto del contenido del archivo `.zip`. Puede descomprimir y examinar estos archivos si quiere disponer de acceso a las copias de seguridad sin tener que realizar una restauración de la aplicación.

La copia de seguridad de la base de datos para la aplicación se almacena en la raíz del archivo .zip. En bases de datos de SQL, este es un archivo BACPAC (sin extensión de archivo) y se puede importar. Para crear una base de datos SQL basándose en la exportación del BACPAC, vea [Importar un archivo de bacpac para crear una nueva base de datos de usuario](http://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> La modificación de los archivos del contenedor **websitebackups** puede ocasionar que la base de datos deje de ser válida y, por lo tanto, no se pueda restaurar.
> 
> 

## <a name="automate-with-scripts"></a>Automatizar con scripts

Puede automatizar la administración de copias de seguridad con scripts, mediante la [CLI de Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/overview).

Para obtener ejemplos, vea:

- [Ejemplos de la CLI de Azure](app-service-cli-samples.md)
- [Ejemplos de Azure PowerShell](app-service-powershell-samples.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Pasos siguientes
Para obtener información sobre cómo restaurar una aplicación desde una copia de seguridad, vea [Restauración de una aplicación en el Servicio de aplicaciones de Azure](web-sites-restore.md). 


<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage1.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount-1.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow1.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn1.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
[kudu-portal]:./media/web-sites-backup/kudu-portal.PNG

