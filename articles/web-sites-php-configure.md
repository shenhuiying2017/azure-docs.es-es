<properties
	pageTitle="Configuración de PHP en aplicaciones web del Servicio de aplicaciones de Azure "
	description="Obtenga información acerca de cómo configurar la instalación de PHP predeterminada o agregar una instalación de PHP personalizada para Aplicaciones Web en el Servicio de aplicaciones de Azure."
	services="app-service\web"
	documentationCenter="php"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="tomfitz"/>

#Configuración de PHP en aplicaciones web del Servicio de aplicaciones de Azure 

## Introducción

En esta guía se explica cómo configurar el tiempo de ejecución de PHP integrado en Aplicaciones web para el [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714), ofrecer un tiempo de ejecución de PHP personalizado y habilitar extensiones. Para utilizar el Servicio de aplicaciones, regístrese para obtener acceso a la [evaluación gratuita]. Para obtener el máximo partido de esta guía, primero debe crear una aplicación web PHP en el Servicio de aplicaciones.

## Procedimientos: de la configuración predeterminada de PHP
PHP 5.4 está instalado de forma predeterminada y está disponible para utilizarse inmediatamente después de crear una aplicación web de Azure. La mejor forma de ver la revisión publicada disponible, su configuración predeterminada y las extensiones habilitadas es implementar un script que llame a la función [phpinfo()].

Las versiones de PHP 5.5 y PHP 5.6 también están disponibles, pero no están habilitadas de forma predeterminada. Para actualizar la versión de PHP, siga estos pasos:

1. Vaya a la aplicación web en el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715) y haga clic en el botón **Configuración**.

	![Web App Settings][settings-button]

2. En la hoja **Configuración**, seleccione **Configuración de la aplicación** y elija la nueva versión de PHP.

    ![Application Settings][application-settings]

3. Haga clic en el botón **Guardar**, situado en la parte superior de la hoja **Configuración de aplicaciones web**.

	![Save configuration settings][save-button]

Para todos los tiempos de ejecución de PHP integrados, puede cambiar cualquiera de las opciones de configuración que no son directivas solo a nivel de sistema; para ello, siga los pasos que se indican a continuación. Para obtener información acerca de las directivas solo a nivel de sistema, consulte la [lista de directivas de php.ini] (en inglés).

1. Agregue un archivo [.user.ini] al directorio raíz.
2. Agregue la configuración al archivo `.user.ini`; para ello, use la misma sintaxis que usaría en un archivo `php.ini`. Por ejemplo, si desea activar la configuración `display_errors` y definir `upload_max_filesize` como 10M, el archivo `.user.ini` debería contener este texto:

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

3. Implemente la aplicación web.
4. Reinicie la aplicación web. (Es necesario reiniciar porque la frecuencia con la que PHP lee los archivos `.user.ini` depende de la configuración de  `user_ini.cache_ttl`, que es una configuración a nivel de sistema con un valor predeterminado de 300 segundos (5 minutos). El reinicio de la aplicación web fuerza a PHP a leer la nueva configuración del archivo `.user.ini`).

Como alternativa al uso de un archivo `.user.ini`, puede usar la función [ini_set()] en los scripts para definir las opciones de configuración que no son directivas a nivel de sistema.

## Procedimientos: de extensiones en el tiempo de ejecución de PHP predeterminado
Como se ha mencionado en la sección anterior, la mejor forma de ver la versión de PHP predeterminada, su configuración predeterminada y las extensiones habilitadas es implementar un script que llame a la función [phpinfo()]. Para habilitar extensiones adicionales, siga los pasos que se detallan a continuación.

1. Agregue un directorio `bin` al directorio raíz.
2. Coloque los archivos con extensión `.dll` en el directorio `bin` (por ejemplo, `php_mongo.dll`). Asegúrese de que las extensiones sean compatibles con la versión predeterminada de PHP (que es, para lo que aquí se expone, PHP 5.4) y que sean compatibles con VC9 y no seguras para subprocesos (nts).
3. Implemente la aplicación web.
4. Vaya a la aplicación web en el Portal de Azure y haga clic en el botón **Configuración**.

	![Web App Settings][settings-button]

5. En la hoja **Configuración**, seleccione **Configuración de la aplicación** y desplácese a la sección **Configuración de aplicaciones**.
6. En la sección **Configuración de aplicaciones**, cree una clave **PHP_EXTENSIONS**. El valor de esta clave es una ruta de acceso relativa a la raíz del sitio web: **bin\your-ext-file**.

	![Enable extension in app settings][php-extensions]

7. Haga clic en el botón **Guardar**, situado en la parte superior de la hoja **Configuración de aplicaciones web**.

	![Save configuration settings][save-button]

También se pueden usar las extensiones Zend mediante una clave **PHP_ZENDEXTENSIONS**. Para habilitar varias extensiones, incluya una lista separada por comas de los archivos `.dll` como valor de configuración de aplicaciones.

## Procedimientos: Uso de un tiempo de ejecución de PHP personalizado
En lugar del tiempo de ejecución de PHP, Aplicaciones web del Servicio de aplicaciones puede usar un tiempo de ejecución de PHP facilitado por el usuario para ejecutar scripts PHP. El tiempo de ejecución facilitado se puede configurar mediante un archivo `php.ini` proporcionado también por el usuario. Para usar un tiempo de ejecución de PHP personalizado en Aplicaciones web, siga estos pasos.

1. Obtenga un versión compatible con VC9 y no segura para subprocesos de PHP para Windows. Las versiones recientes de PHP para Windows se puede encontrar aquí: [http://windows.php.net/download/]. Las versiones anteriores se pueden encontrar en el archivo aquí: [http://windows.php.net/downloads/releases/archives/].
2. Modifique el archivo `php.ini` para el tiempo de ejecución. Tenga en cuenta que Aplicaciones web omitirá cualquier configuración que se corresponda con directivas que sean solo de nivel de sistema. Para obtener información acerca de las directivas solo a nivel de sistema, consulte la [lista de directivas de php.ini] (en inglés).
3. También puede agregar extensiones al tiempo de ejecución de PHP y habilitarlas en el archivo `php.ini`.
4. Agregue un directorio `bin` al directorio raíz y coloque en él el directorio que contiene el tiempo de ejecución de PHP (por ejemplo, `bin\php`).
5. Implemente la aplicación web.
4. Vaya a la aplicación web en el Portal de Azure y haga clic en el botón **Configuración**.

	![Web App Settings][settings-button]

7. En la hoja **Configuración**, seleccione **Configuración de aplicaciones** y desplácese a la sección **Asignaciones de controlador**. Agregue `*.php` al campo Extensión y agregue la ruta de acceso al ejecutable `php-cgi.exe`. Si coloca el tiempo de ejecución de PHP en el directorio `bin` en la raíz de la aplicación, la ruta de acceso será `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

	![Specify handler in hander mappings][handler-mappings]

8. Haga clic en el botón **Guardar**, situado en la parte superior de la hoja **Configuración de aplicaciones web**.

	![Save configuration settings][save-button]

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## ¿Qué ha cambiado?
* Para obtener una guía del cambio de Sitios web al Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal antiguo al nuevo, consulte: [Referencia para navegar por el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

[evaluación gratuita]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista de directivas de php.ini]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/

<!--HONumber=49-->