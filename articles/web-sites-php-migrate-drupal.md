<properties linkid="migrating-drupal-to-azure-websites" urlDisplayName="Migrating Drupal to Azure Web Sites" pageTitle="Migrating Drupal to Azure Web Sites" metaKeywords="Drupal, PHP, Web Sites" description="Migrate a Drupal PHP site to Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Migrating Drupal to Azure Web Sites" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

Migración de Drupal a Sitios web Azure
======================================

Como Sitios web Azure admite tanto PHP como MySQL, es relativamente sencillo migrar un sitio de Drupal a Sitios web Azure. Además, como Drupal y PHP se pueden ejecutar en todas las plataformas, el proceso debería funcionar para mover Drupal a Sitios web Azure independientemente de la plataforma que use actualmente. Dicho esto, las instalaciones de Drupal pueden variar mucho, por lo que es posible que en el siguiente material queden sin tratar pasos exclusivos de migración. Tenga en cuenta que la herramienta Drush no se usa porque no es compatible con Sitios web Azure.

> [WACOM.NOTE] Si está tratando de mover una aplicación de Drupal grande y compleja, considere la opción de usar Servicios en la nube de Azure. Para obtener más información acerca de las diferencias entre Sitios web y Servicios en la nube, consulte [Sitios web, Servicios en la nube y Máquinas virtuales de Windows Azure: cuándo usar cada uno](http://go.microsoft.com/fwlink/?LinkId=310123). Para recibir ayuda con el paso de Drupal a Servicios en la nube, consulte [Migración de un sitio de Drupal de LAMP a Azure](http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx) (en inglés).

Tabla de contenido
------------------

-   [Creación de un sitio web de Azure](#create-siteanddb)
-   [Copia de la base de datos](#copy-database)
-   [Modificación de Settings.php](#modify-settingsphp)
-   [Implementación del código de Drupal](#deploy-drupalcode)
-   [Información relacionada](#related-information)

Creación de un sitio web de Azure y una base de datos MySQL1. Cree un sitio web de Azure y una base de datos MySQL
------------------------------------------------------------------------------------------------------------------

En primer lugar, siga el tutorial de instrucciones para saber cómo crear un sitio web con MySQL: [Creación de un sitio web PHP-MySQL de Azure e implementación mediante Git](http://www.windowsazure.com/en-us/develop/php/tutorials/website-w-mysql-and-git/). Si tiene previsto usar Git para publicar el sitio de Drupal, entonces siga los pasos del tutorial donde se explica cómo configurar un repositorio de Git. Asegúrese de seguir las instrucciones de la sección **Obtener la información de la conexión MySQL remota**, ya que necesitará dicha información más adelante. Puede ignorar el recordatorio del tutorial para la implementación del sitio de Drupal. No obstante, si no está familiarizado con Sitios web Azure (ni con Git), puede que le resulte útil leer lo siguiente.

Después de configurar un nuevo sitio web con una base de datos MySQL, tendrá la información de la conexión de la base de datos MySQL y un repositorio Git (opcional). El paso siguiente es copiar su base de datos a MySQL en Sitios web Azure.

Copia de la base de datos a MySQL en Sitios web Azure2. Copie la base de datos a MySQL en Sitios web Azure
----------------------------------------------------------------------------------------------------------

Hay muchas formas de migrar una base de datos a Azure. Una de las que mejor funcionan con las bases de datos MySQL es usar la herramienta [MySqlDump][]. El comando siguiente proporciona un ejemplo de cómo copiar de un equipo local a Sitios web Azure:

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

Por supuesto, debe especificar el nombre de usuario y la contraseña de la base de datos de Drupal existente. Además, debe proporcionar el nombre de host, el nombre de usuario, la contraseña y el nombre de la base de datos MySQL que ha creado en el primer paso. Esta información esta disponible en la información de la cadena de conexión que ha recopilado anteriormente. La cadena de conexión debe tener un formato similar al siguiente:

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

El proceso de copia podría tardar varios minutos, dependiendo del tamaño de la base de datos.

Llegados a este punto, la base de datos de Drupal estará activa en Sitios web Azure. Antes de implementar el código de Drupal, tiene que modificarlo para que se pueda conectar a la nueva base de datos.

Modificación de la información de conexión de la base de datos de settings.php3. Modifique la información de conexión de la base de datos de settings.php
---------------------------------------------------------------------------------------------------------------------------------------------------------

En este punto, volverá a necesitar la información de conexión de la nueva base de datos. Abra el archivo **/drupal/sites/default/setting.php** en un editor de texto y reemplace los valores de "database", "username", "password" y "host" de la matriz **$databases** por los valores correctos de la nueva base de datos. Cuando termine, el resultado debería ser similar al siguiente:

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

Implementación del código de Drupal usando Git o FTP4. Implemente el código de Drupal usando Git o FTP
------------------------------------------------------------------------------------------------------

El último paso es implementar el código en Sitios web Azure usando Git o FTP.

Si usa FTP, busque el nombre de usuario y el nombre de host de FTP en el panel del sitio web. A continuación, use cualquier cliente FTP para cargar los archivos de Drupal en la carpeta **/site/wwwroot** del sitio remoto.

Si usa Git, debería haber configurado un repositorio Git en los pasos anteriores. Debe instalar Git en su equipo local. A continuación, siga las instrucciones proporcionadas después de crear el repositorio.

> [WACOM.NOTE] En función de su configuración de Git, puede que tenga que modificar el archivo .gitignore (un archivo oculto y del mismo nivel de la carpeta .git creada en el directorio raíz local después de ejecutar la confirmación git). De este modo se especifican archivos en su aplicación de Drupal que podrían de otro modo ignorarse. Si contiene archivos que deberían implementarse, elimine las entradas para que estos archivos no se ignoren.

Después de implementar Drupal en Sitios web Azure, puede continuar implementando las actualizaciones por medio de Git o FTP.

Información relacionadaInformación relacionada
----------------------------------------------

Para obtener más información, consulte los temas y las publicaciones siguientes:

-   [Sitios web Azure: una perspectiva de PHP (en inglés)](http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx)
-   [Sitios web, Servicios en la nube y Máquinas virtuales de Windows Azure: cuándo usar cada uno](http://go.microsoft.com/fwlink/?LinkId=310123).
-   [Configuración de PHP en Sitios web Azure con archivos .user.ini (en inglés)](http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx)
-   [Módulo de integración de Azure](https://drupal.org/project/azure_auth)
-   [Módulo de almacenamiento de blobs de Azure](https://drupal.org/project/azure_blob)


