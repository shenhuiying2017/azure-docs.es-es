<properties title="How to Configure PHP in Azure Websites" pageTitle="Configuración de PHP en Sitios web Azure" metaKeywords="Azure, Azure Web Sites, configuration, PHP" description="Learn how to configure the default PHP installation or add a custom PHP installation in Azure Websites." services="Web Sites" documentationCenter="PHP" authors="tomfitz" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/18/2014" ms.author="tomfitz" />

#Configuración de PHP en Sitios web Azure

En esta guía se explica cómo configurar el tiempo de ejecución de PHP integrado en Sitios web Azure, ofrecer un tiempo de ejecución de PHP personalizado y habilitar extensiones en Sitios web Azure. Para usar Sitios web Azure, regístrese para obtener acceso a la [prueba gratuita]. Para sacar el máximo provecho a esta guía, primero debe crear un sitio PHP en Sitios web Azure (consulte los [Tutoriales del Centro para desarrolladores de PHP]). Si desea obtener información general acerca de la configuración de los sitios en Sitios web Azure, consulte [Configuración de Sitios web].

##Tabla de contenido

* [¿Qué es Sitios web Azure?](#WhatIs)
* [Cambio de la configuración predeterminada de PHP](#ChangeBuiltInPHP)
* [Habilitación de extensiones en el tiempo de ejecución de PHP integrado](#EnableExtDefaultPHP)
* [Uso de un tiempo de ejecución de PHP personalizado](#UseCustomPHP)
* [Pasos siguientes](#NextSteps)

<h2><a name="WhatIs"></a>¿Qué es Sitios web Azure?</h2>
Sitios web Azure le permite generar sitios web altamente escalables en Azure. Puede implementar sitios de manera rápida y fácil en un entorno de la nube altamente escalable que le permite empezar con poco y escalar a medida que el tráfico aumenta. Sitios web Azure usa los lenguajes y las aplicaciones de código abierto que elija el usuario y admite implementaciones con Git, FTP y TFS. Puede integrar fácilmente otros servicios como MySQL, Base de datos SQL, Almacenamiento en caché, Servicio CDN y Almacenamiento.

<h2><a name="ChangeBuiltInPHP"></a>Reproducción de de la configuración predeterminada de PHP</h2>
PHP 5.4 está instalado de forma predeterminada y está disponible para utilizarse inmediatamente después de crear un sitio web de Azure. La mejor forma de ver la revisión publicada disponible, su configuración predeterminada y las extensiones habilitadas es implementar un script que llame a la función [phpinfo()].

PHP 5.5 también está disponible, pero no está habilitado de forma predeterminada. Para habilitarlo, siga estos pasos:

1. Vaya al panel del sitio web en el Portal de Azure y haga clic en **Configurar**.

	![Configure tab on Web Sites dashboard][configure]

1. Haga clic en PHP 5.5.

	![Select PHP version][select-php-version]

1. Haga clic en **Guardar** en la parte inferior de la página.

	![Save configuration settings][save-button]

Para todos los tiempos de ejecución de PHP integrados, puede cambiar cualquiera de las opciones de configuración que no son directivas solo a nivel de sistema; para ello, siga los pasos que se indican a continuación. (Para obtener información acerca de las directivas solo a nivel de sistema, consulte la [lista de directivas de php.ini]).

1. Agregue un archivo [.user.ini] al directorio raíz.
1. Agregue la configuración al archivo ".user.ini"; para ello, use la misma sintaxis que usaría en un archivo "php.ini". Por ejemplo, si quisiera activar la configuración "display_errors" y definir "upload_max_filesize" como 10M, el archivo ".user.ini" debería contener este texto:

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

1. Implemente la aplicación.
1. Reinicie el sitio web. (Es necesario reiniciar porque la frecuencia con la que PHP lee los archivos ".user.ini" depende de la configuración de "user_ini.cache_ttl", que es una configuración a nivel de sistema con un valor predeterminado de 300 segundos (5 minutos). El reinicio del sitio fuerza a PHP a leer la nueva configuración del archivo ".user.ini").

Como alternativa al uso del archivo ".user.ini", puede usar la función [ini_set()] en los scripts para definir las opciones de configuración que no son directivas a nivel de sistema.

<h2><a name="EnableExtDefaultPHP"></a>Reproducción de de extensiones en el tiempo de ejecución de PHP predeterminado</h2>
Como bien se ha mencionado en la sección anterior, la mejor forma de ver la versión de PHP predeterminada, su configuración predeterminada y las extensiones habilitadas es implementar un script que llame a la función [phpinfo()]. Para habilitar extensiones adicionales, siga los pasos que se detallan a continuación.

1. Agregue un directorio "bin" al directorio raíz.
1. Coloque los archivos con extensión ".dll" en el directorio bin (por ejemplo, "php_mongo.dll"). Asegúrese de que las extensiones sean compatibles con la versión predeterminada de PHP (que es, para lo que aquí se expone, PHP 5.4) y que sean compatibles con VC9 y no seguras para subprocesos (nts).
1. Implemente la aplicación.
1. Vaya al panel del sitio en el Portal de Azure y haga clic en **Configurar**.

	![Configure tab on Web Sites dashboard][configure]

1. En la sección **configuración de la aplicación**, cree una clave **PHP_EXTENSIONS** y un valor **bin\your-ext-file**. Para habilitar varias extensiones, incluya una lista separada por comas de archivos ".dll".

	![Enable extension in app settings][app-settings]

1. Haga clic en **Guardar** en la parte inferior de la página.

	![Save configuration settings][save-button]

<h2><a name="UseCustomPHP"></a>Reproducción de de un tiempo de ejecución de PHP personalizado</h2>
En lugar del tiempo de ejecución de PHP, Sitios web Azure puede usar un tiempo de ejecución de PHP facilitado por el usuario para ejecutar scripts PHP. El tiempo de ejecución facilitado se puede configurar mediante un archivo "php.ini" proporcionado también por el usuario. Para usar un tiempo de ejecución de PHP personalizado en Sitios web Azure, siga estos pasos.

1. Obtenga un versión compatible con VC9 y no segura para subprocesos de PHP para Windows. Las versiones recientes de PHP para Windows se puede encontrar aquí: [http://windows.php.net/download/]. Las versiones anteriores se pueden encontrar en el archivo aquí: [http://windows.php.net/downloads/releases/archives/].
1. Modifique el archivo "php.ini" para el tiempo de ejecución. Tenga en cuenta que Sitios web Azure ignorará la configuración que se corresponda con directivas que sean solo a nivel de sistema. (Para obtener información acerca de las directivas solo a nivel de sistema, consulte la [lista de directivas de php.ini]).
1. También puede agregar extensiones al tiempo de ejecución de PHP y habilitarlas en el archivo "php.ini".
1. Agregue el directorio "bin" al directorio raíz y coloque en él el directorio que contiene el tiempo de ejecución de PHP (por ejemplo, "bin\php").
1. Implemente la aplicación.
1. Vaya al panel del sitio en el Portal de Azure y haga clic en **Configurar**.

	![Configure tab on Web Sites dashboard][configure]

1. En la sección **asignaciones de controlador**, agregue "*.php" a EXTENSIÓN y agregue la ruta de acceso al archivo ejecutable "php-cgi.exe". Si coloca el tiempo de ejecución de PHP en el directorio "bin" en la raíz de la aplicación, la ruta de acceso será `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

	![Specify handler in hander mappings][handler-mappings]

1. Haga clic en **Guardar** en la parte inferior de la página.

	![Save configuration settings][save-button]

<h2><a name="NextSteps"></a>Pasos siguientes</h2>
Ahora que ya sabe cómo configurar PHP en Sitios web Azure, siga los vínculos que se proporcionan a continuación para obtener más información.

- [Configuración, supervisión y escalado de los sitios web en Azure]
- [Descarga del SDK de Azure para PHP]


[prueba gratuita]: https://www.windowsazure.com/en-us/pricing/free-trial/
[Tutotiales del Centro para desarrolladores de PHP]: https://www.windowsazure.com/en-us/develop/php/tutorials/
[Configuración de sitios web]: https://www.windowsazure.com/en-us/manage/services/web-sites/how-to-configure-websites/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista de directivas de php.ini]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[configurar]: ./media/web-sites-php-configure/configure.png
[app-settings]: ./media/web-sites-php-configure/app-settings.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[Configuración, supervisión y escalado de los sitios web en Azure]: http://www.windowsazure.com/en-us/manage/services/web-sites/
[Descarga del SDK de Azure para PHP]: http://www.windowsazure.com/en-us/develop/php/common-tasks/download-php-sdk/

<!--HONumber=35_1-->
