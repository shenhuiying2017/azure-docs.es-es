<properties 
	pageTitle="Migración de Drupal al servicio de aplicación de Azure" 
	description="Migración de un sitio PHP de Drupal al Servicio de aplicaciones de Azure." 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>


# Migración de Drupal al servicio de aplicación de Azure

Como el Servicio de aplicaciones de Azure admite tanto PHP como MySQL, es relativamente sencillo migrar un sitio de Drupal al Servicio de aplicaciones de Azure. Además, como Drupal y PHP se pueden ejecutar en todas las plataformas, el proceso debería funcionar para mover Drupal al Servicio de aplicaciones de Azure independientemente de la plataforma que use actualmente. Dicho esto, las instalaciones de Drupal pueden variar mucho, por lo que es posible que en el siguiente material queden sin tratar pasos exclusivos de migración. Tenga en cuenta que la herramienta Drush no se usa porque no es compatible con el Servicio de aplicaciones de Azure.

Si está tratando de mover una aplicación de Drupal grande y compleja, considere la opción de usar Servicios en la nube de Azure. Para obtener más información acerca de las diferencias entre el Servicio de aplicaciones y Servicios en la nube, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=310123">Servicio de aplicaciones, Servicios en la nube y máquinas virtuales de Azure: cuándo usar cada uno.</a> Para recibir ayuda con el paso de Drupal a Servicios en la nube, consulte <a href="http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx">Migración de un sitio de Drupal de LAMP a Azure</a>.
 
## Creación de una aplicación web y una base de datos MySQL

En primer lugar, siga el tutorial de instrucciones para saber cómo crear una aplicación web con MySQL: [Creación de una aplicación web PHP-MySQL en el Servicio de aplicaciones de Azure e implementación mediante Git][]. Si tiene previsto usar Git para publicar el sitio de Drupal, entonces siga los pasos del tutorial donde se explica cómo configurar un repositorio de Git. Asegúrese de seguir las instrucciones de la sección **Obtener la información de la conexión MySQL remota**, ya que necesitará dicha información más adelante. Puede ignorar el recordatorio del tutorial para la implementación del sitio de Drupal. No obstante, si no está familiarizado con el Servicio de aplicaciones de Azure (ni con Git), puede que le resulte útil leer lo siguiente.

Después de configurar una nueva aplicación web con una base de datos MySQL, tendrá la información de la conexión de la base de datos MySQL y un repositorio Git (opcional). El siguiente paso es copiar la base de datos en la base de datos MySQL de la aplicación web.

## Copia de base de datos en la base de datos MySQL de la aplicación

Hay muchas formas de migrar una base de datos a Azure. Una de las que mejor funcionan con las bases de datos MySQL es usar la herramienta [MySqlDump][]. El comando siguiente proporciona un ejemplo de cómo copiar de un equipo local a Azure:

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

Por supuesto, debe especificar el nombre de usuario y la contraseña de la base de datos de Drupal existente. Además, debe proporcionar el nombre de host, el nombre de usuario, la contraseña y el nombre de la base de datos MySQL que ha creado en el primer paso. Esta información esta disponible en la información de la cadena de conexión que ha recopilado anteriormente. La cadena de conexión debe tener un formato similar al siguiente:

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

El proceso de copia podría tardar varios minutos, dependiendo del tamaño de la base de datos.

Llegados a este punto, la base de datos de Drupal estará activa en Azure. Antes de implementar el código de Drupal, tiene que modificarlo para que se pueda conectar a la nueva base de datos.

## Modificación de la información de conexión de base de datos en settings.php

En este punto, volverá a necesitar la información de conexión de la nueva base de datos. Abra el archivo **/drupal/sites/default/setting.php** en un editor de texto y reemplace los valores de 'database', 'username', 'password' y 'host' de la matriz **$databases** por los valores correctos de la nueva base de datos. Cuando termine, el resultado debería ser similar al siguiente:

    $databases = array (
       'default' => 
       array (
         'default' => 
         array (
           'database' => 'remote_db_name',
           'username' => 'remote_username',
           'password' => 'remote_password',
           'host' => 'remote_host',
           'port' => '',
           'driver' => 'mysql',
           'prefix' => '',
         ),
       ),
     );

Guarde el archivo **settings.php**. De este modo, estará listo para la implementación.

## Implementación de código de Drupal usando Git o FTP

El último paso es implementar el código en aplicaciones web usando Git o FTP.

Si usa FTP, busque el nombre de usuario y el nombre de host de FTP en la hoja de la aplicación web en el [Portal de vista previa de Azure](https://portal.azure.com). A continuación, use cualquier cliente FTP para cargar los archivos de Drupal en la carpeta **/site/wwwroot** del sitio remoto.

Si usa Git, debería haber configurado un repositorio Git en los pasos anteriores. Debe instalar Git en su equipo local. A continuación, siga las instrucciones proporcionadas después de crear el repositorio.

> [AZURE.NOTE]En función de su configuración de Git, puede que tenga que modificar el archivo .gitignore (un archivo oculto y del mismo nivel de la carpeta .git creada en el directorio raíz local después de ejecutar la confirmación git). De este modo se especifican archivos en su aplicación de Drupal que podrían de otro modo ignorarse. Si contiene archivos que deberían implementarse, elimine las entradas para que estos archivos no se ignoren.

Después de implementar Drupal en aplicaciones web, puede continuar implementando las actualizaciones por medio de Git o FTP.

## Información relacionada

Para obtener más información, consulte los temas y las publicaciones siguientes:

- [Aplicaciones web del Servicio de aplicaciones de Azure, una perspectiva de PHP][]
- [Servicio de aplicaciones, Servicios en la nube y máquinas virtuales de Azure: cuándo usar cada uno][]
- [Configuración de PHP en Aplicaciones web del Servicio de aplicaciones de Azure con archivos .user.ini][]
- [Módulo de integración de Azure](https://drupal.org/project/azure_auth)
- [Módulo de almacenamiento de blobs de Azure](https://drupal.org/project/azure_blob)

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714).
* Para obtener una guía del cambio del portal al de vista previa, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715).

  [Creación de una aplicación web PHP-MySQL en el Servicio de aplicaciones de Azure e implementación mediante Git]: /develop/php/tutorials/website-w-mysql-and-git/
  
  [Aplicaciones web del Servicio de aplicaciones de Azure, una perspectiva de PHP]: http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx
  [Servicio de aplicaciones, Servicios en la nube y máquinas virtuales de Azure: cuándo usar cada uno]: http://go.microsoft.com/fwlink/?LinkId=310123
  [Configuración de PHP en Aplicaciones web del Servicio de aplicaciones de Azure con archivos .user.ini]: http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx
  [Azure Integration Module]: http://drupal.org/project/azure
 

<!---HONumber=July15_HO3-->