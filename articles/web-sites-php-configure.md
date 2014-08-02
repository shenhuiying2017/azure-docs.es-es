<properties title="How to Configure PHP in Azure Web Sites" pageTitle="How to Configure PHP in Azure Web Sites" metaKeywords="Azure, Azure Web Sites, configuration, PHP" description="Learn how to configure the default PHP installation or add a custom PHP installation in Azure Web Sites." services="Web Sites" documentationCenter="PHP" authors="" />

Configuración de PHP en Sitios web Azure
========================================

En esta guía se explica cómo configurar el tiempo de ejecución de PHP integrado en Sitios web Azure, ofrecer un tiempo de ejecución de PHP personalizado y habilitar extensiones en Sitios web Azure. Para usar Sitios web Azure, regístrese para obtener acceso a la [prueba gratuita](https://www.windowsazure.com/en-us/pricing/free-trial/). Para sacar el máximo provecho a esta guía, primero debe crear un sitio PHP en Sitios web Azure (consulte los [Tutoriales del Centro para desarrolladores de PHP](https://www.windowsazure.com/en-us/develop/php/tutorials/)). Si desea obtener información general acerca de la configuración de los sitios en Sitios web Azure, consulte [Configuración de Sitios web](https://www.windowsazure.com/en-us/manage/services/web-sites/how-to-configure-websites/).

Tabla de contenido
------------------

-   [żQué es Sitios web Azure?](#WhatIs)
-   [Cambio de la configuración predeterminada de PHP](#ChangeBuiltInPHP)
-   [Habilitación de extensiones en el tiempo de ejecución de PHP integrado](#EnableExtDefaultPHP)
-   [Uso de un tiempo de ejecución de PHP personalizado](#UseCustomPHP)
-   [Pasos siguientes](#NextSteps)

żQué es Sitios web Azure?
-------------------------

Sitios web Azure le permite generar sitios web altamente escalables en Azure. Puede implementar sitios de manera rápida y fácil en un entorno de la nube altamente escalable que le permite empezar con poco y escalar a medida que el tráfico aumenta. Sitios web Azure usa los lenguajes y las aplicaciones de código abierto que elija el usuario y admite implementaciones con Git, FTP y TFS. Puede integrar fácilmente otros servicios como MySQL, Base de datos SQL, Almacenamiento en caché, Servicio CDN y Almacenamiento.

Cambio de la configuración predeterminada de PHP
------------------------------------------------

PHP 5.4 está instalado de forma predeterminada y está disponible para utilizarse inmediatamente después de crear un sitio web de Azure. La mejor forma de ver la revisión publicada disponible, su configuración predeterminada y las extensiones habilitadas es implementar un script que llame a la función [phpinfo()](http://php.net/manual/en/function.phpinfo.php).

PHP 5.5 también está disponible, pero no está habilitado de forma predeterminada. Para habilitarlo, siga estos pasos:

1.  Vaya al panel del sitio web en el Portal de Azure y haga clic en **Configure**.

    ![Pestańa de configuración del panel Sitios web](./media/web-sites-php-configure/configure.png)

2.  Haga clic en PHP 5.5.

    ![Seleccionar la versión de PHP](./media/web-sites-php-configure/select-php-version.png)

3.  Haga clic en **Save** en la parte inferior de la página.

    ![Guardar la configuración](./media/web-sites-php-configure/save-button.png)

Para todos los tiempos de ejecución de PHP integrados, puede cambiar cualquiera de las opciones de configuración que no son directivas solo a nivel de sistema; para ello, siga los pasos que se indican a continuación. Para obtener información acerca de las directivas solo a nivel de sistema, consulte la [lista de directivas de php.ini](http://www.php.net/manual/en/ini.list.php) (en inglés).

1.  Agregue un archivo [.user.ini](http://www.php.net/manual/en/configuration.file.per-user.php) al directorio raíz.
2.  Agregue la configuración al archivo `.user.ini`; para ello, use la misma sintaxis que usaría en un archivo `php.ini`. Por ejemplo, si quisiera activar la configuración `display_errors` y definir `upload_max_filesize` como 10M, el archivo `.user.ini` debería contener este texto:

         ; Example Settings
         display_errors=On
         upload_max_filesize=10M

3.  Implemente la aplicación.
4.  Reinicie el sitio web. (Es necesario reiniciar porque la frecuencia con la que PHP lee los archivos `.user.ini` depende de la configuración de `user_ini.cache_ttl`, que es una configuración a nivel de sistema con un valor predeterminado de 300 segundos (5 minutos). El reinicio del sitio fuerza a PHP a leer la nueva configuración del archivo `.user.ini`).

Como alternativa al uso del archivo `.user.ini`, puede usar la función [ini\_set()](http://www.php.net/manual/en/function.ini-set.php) en los scripts para definir las opciones de configuración que no son directivas a nivel de sistema.

Habilitación de extensiones en el tiempo de ejecución de PHP predeterminado
---------------------------------------------------------------------------

Como bien se ha mencionado en la sección anterior, la mejor forma de ver la versión de PHP predeterminada, su configuración predeterminada y las extensiones habilitadas es implementar un script que llame a la función [phpinfo()](http://php.net/manual/en/function.phpinfo.php). Para habilitar extensiones adicionales, siga los pasos que se detallan a continuación.

1.  Agregue un directorio `bin` al directorio raíz.
2.  Coloque los archivos con extensión `.dll` en el directorio `bin` (por ejemplo, `php_mongo.dll`). Asegúrese de que las extensiones sean compatibles con la versión predeterminada de PHP (que es, para lo que aquí se expone, PHP 5.4) y que sean compatibles con VC9 y no seguras para subprocesos (nts).
3.  Implemente la aplicación.
4.  Vaya al panel del sitio en el Portal de Azure y haga clic en **Configure**.

    ![Pestańa de configuración del panel Sitios web](./media/web-sites-php-configure/configure.png)

5.  En la sección **app settings**, cree una clave **PHP\_EXTENSIONS** y un valor **bin\\your-ext-file**. Para habilitar varias extensiones, incluya una lista separada por comas de archivos `.dll`.

    ![Habilitar extensiones en app settings](./media/web-sites-php-configure/app-settings.png)

6.  Haga clic en **Save** en la parte inferior de la página.

    ![Guardar la configuración](./media/web-sites-php-configure/save-button.png)

Uso de un tiempo de ejecución de PHP personalizado
--------------------------------------------------

En lugar del tiempo de ejecución de PHP, Sitios web Azure puede usar un tiempo de ejecución de PHP facilitado por el usuario para ejecutar scripts PHP. El tiempo de ejecución facilitado se puede configurar mediante un archivo `php.ini` proporcionado también por el usuario. Para usar un tiempo de ejecución de PHP personalizado en Sitios web Azure, siga estos pasos.

1.  Obtenga un versión compatible con VC9 y no segura para subprocesos de PHP para Windows. Las versiones recientes de PHP para Windows se puede encontrar aquí: <http://windows.php.net/download/>. Las versiones anteriores se pueden encontrar en el archivo aquí: <http://windows.php.net/downloads/releases/archives/>.
2.  Modifique el archivo `php.ini` para el tiempo de ejecución. Tenga en cuenta que Sitios web Azure ignorará la configuración que se corresponda con directivas que sean solo a nivel de sistema. Para obtener información acerca de las directivas solo a nivel de sistema, consulte la [lista de directivas de php.ini](http://www.php.net/manual/en/ini.list.php) (en inglés).
3.  También puede agregar extensiones al tiempo de ejecución de PHP y habilitarlas en el archivo `php.ini`.
4.  Agregue el directorio `bin` al directorio raíz y coloque en él el directorio que contiene el tiempo de ejecución de PHP (por ejemplo, `bin\php`).
5.  Implemente la aplicación.
6.  Vaya al panel del sitio en el Portal de Azure y haga clic en **Configure**.

    ![Pestańa de configuración del panel Sitios web](./media/web-sites-php-configure/configure.png)

7.  En la sección **handler mappings**, agregue `*.php` a EXTENSION y agregue la ruta de acceso al archivo ejecutable `php-cgi.exe`. Si coloca el tiempo de ejecución de PHP en el directorio `bin` en la raíz de la aplicación, la ruta de acceso será `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

    ![Especificar el controlador en hander mappings](./media/web-sites-php-configure/handler-mappings.png)

8.  Haga clic en **Save** en la parte inferior de la página.

    ![Guardar la configuración](./media/web-sites-php-configure/save-button.png)

Pasos siguientes
----------------

Ahora que ya sabe cómo configurar PHP en Sitios web Azure, siga los vínculos que se proporcionan a continuación para obtener más información.

-   [Configuración, supervisión y escalado de los sitios web en Azure](http://www.windowsazure.com/en-us/manage/services/web-sites/)
-   [Descarga del SDK de Azure para PHP](http://www.windowsazure.com/en-us/develop/php/common-tasks/download-php-sdk/)

