<properties
	pageTitle="Creación, configuración e implementación de una aplicación web PHP en Azure"
	description="Un tutorial que muestra cómo hacer que una aplicación web PHP (Laravel) se ejecute en el Servicio de aplicaciones de Azure. Obtenga información acerca de cómo configurar el Servicio de aplicaciones de Azure para que cumpla los requisitos del marco de trabajo PHP que elija."
	services="app-service\web"
	documentationCenter="php"
	authors="cephalin"
	manager="wpickett"
	editor=""
	tags="mysql"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="06/03/2016" 
	ms.author="cephalin"/>

# Creación, configuración e implementación de una aplicación web PHP en Azure

[AZURE.INCLUDE [pestañas](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial explica cómo crear, configurar e implementar una aplicación web PHP de Azure y cómo configurar el Servicio de aplicaciones de Azure para cumplir los requisitos de la aplicación web PHP. Al final del tutorial, tendrá una aplicación web [Laravel](https://www.laravel.com/) ejecutándose en vivo en el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md).

Como desarrollador de PHP, puede llevar su marco de trabajo PHP favorito a Azure. Este tutorial usa Laravel simplemente como un ejemplo de aplicación concreta. Aprenderá a:

- Realizar la implementación mediante Git
- Establecer la versión de PHP
- Usar un archivo de inicio que no está en el directorio raíz de la aplicación
- Acceder a variables de entorno específico
- Actualizar la aplicación en Azure.

Puede aplicar lo que aprenda aquí a otras aplicaciones web PHP que implemente en Azure.

## Requisitos previos

- Instalación de [PHP 5.6.x](http://php.net/downloads.php) (la compatibilidad con PHP 7 existe en versión beta)
- Instalación de [Composer](https://getcomposer.org/download/)
- Instalación de la [CLI de Azure](../xplat-cli-install.md)
- Instalación de [Git](http://www.git-scm.com/downloads)
- Obtenga una cuenta de Microsoft Azure. Si aún no tiene ninguna, puede [registrarse para obtener una evaluación gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) o bien [activar sus ventajas de suscriptor de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Vea una aplicación web en acción. [Pruebe el Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751) inmediatamente y cree una aplicación de inicio de corta duración; no se requiere tarjeta de crédito ni se establece ningún compromiso.

## Creación de una aplicación PHP (Laravel) en el equipo de desarrollo

1. Abra un símbolo del sistema de Windows, una ventana de PowerShell, un shell de Linux o un terminal de OS X. Ejecute los comandos siguientes para comprobar que las herramientas necesarias están instaladas correctamente en el equipo. 

        php --version
        composer --version
        azure --version
        git --version

    ![Instalación de herramientas de prueba antes de crear la aplicación PHP (Laravel) para Azure](./media/app-service-web-php-get-started/test-tools.png)

    Si no ha instalado las herramientas, consulte [Requisitos previos](#Prerequisites) para obtener vínculos de descarga.
    
2. Instale Laravel de la siguiente manera:

        composer global require "laravel/installer

3. `CD` en un directorio de trabajo y cree una nueva aplicación de Laravel de este modo:

        cd <working_directory>
        laravel new <app_name>

4. `CD` en el directorio `<app_name>` recién creado y pruebe la aplicación de esta forma:

        cd <app_name>
        php artisan serve
        
    Ahora puede navegar a http://localhost:8000 en un explorador y ver la pantalla de presentación de Laravel.
    
    ![Pruebe la aplicación PHP (Laravel) localmente antes de implementarla en Azure](./media/app-service-web-php-get-started/laravel-splash-screen.png)
    
Hasta ahora, este es el flujo de trabajo normal de Laravel y no se encuentra aquí para <a href="https://laravel.com/docs/5.2" rel="nofollow">aprender a manejar Laravel</a>. Así que continuemos.

## Creación de una aplicación web de Azure y configuración de la implementación de Git

>[AZURE.NOTE] "Espere. ¿Qué ocurre si deseo implementar mediante FTP?" Hay un [tutorial de FTP](web-sites-php-mysql-deploy-use-ftp.md) para sus necesidades.

Con la CLI de Azure, puede crear una aplicación web en el Servicio de aplicaciones de Azure y configurarla para la implementación de Git con solo una línea de comandos. Hagámoslo.

3. Inicie sesión en Azure de la manera siguiente:

        azure login
    
    Siga el mensaje de ayuda para continuar con el proceso de inicio de sesión.
    
    ![Inicie sesión en Azure para implementar la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/log-in-to-azure-cli.png)

4. Ejecute el comando para crear la aplicación web de Azure con la implementación de Git. Cuando se le solicite, especifique el número de la región deseada.

        azure site create --git <app_name>
    
    ![Creación del recurso de Azure para la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/create-site-cli.png)
    
    >[AZURE.NOTE] Si nunca ha configurado credenciales de implementación para su suscripción de Azure, se le solicitará que las cree. El Servicio de aplicaciones usa estas credenciales, no las de su cuenta de Azure, solo en implementaciones de Git e inicios de sesión FTP.
    
    Este comando permite crear un nuevo repositorio de Git en el directorio actual (con `git init`) y conectarse al repositorio en Azure como un Git remoto (con `git remote add`).

<a name="configure"/>
## Configuración de la aplicación web de Azure

Para que la aplicación Laravel funcione en Azure, debe prestar atención a varias cosas. Llevará a cabo este ejercicio con el marco de trabajo PHP de su elección de forma similar.

- Configure la versión PHP 5.5.9 o superior. Consulte [Latest Laravel 5.2 Server Requirements](https://laravel.com/docs/5.2#server-requirements) (Requisitos más recientes del servidor de Laravel 5.2) para obtener la lista entera de requisitos del servidor. El resto de la lista son extensiones que ya se han habilitado mediante las instalaciones de PHP de Azure. 
- Establezca las variables de entorno que la aplicación necesita. Laravel utiliza el archivo `.env` para facilitar la configuración de las variables de entorno. Sin embargo, dado que se supone que no se ha ejecutado el control de código fuente, consulte [Laravel Environment Configuration](https://laravel.com/docs/5.2/configuration#environment-configuration) (Configuración del entorno de Laravel), para establecer la configuración de la aplicación web de Azure en su lugar.
- Asegúrese de que el punto de entrada de la aplicación Laravel, `public/index.php`, se carga en primer lugar. Consulte [Laravel Lifecycle Overview](https://laravel.com/docs/5.2/lifecycle#lifecycle-overview) (Información general sobre el ciclo de vida de Laravel). En otras palabras, debe establecer la dirección URL raíz de la aplicación web para que apunte al directorio `public`.
- Habilite la extensión de Composer en Azure, ya que tiene un archivo composer.json. De este modo, puede dejar que Composer se preocupe de cómo obtener los paquetes necesarios al implementar con `git push`. Es una cuestión de comodidad. Si no habilita la automatización de Composer, bastará con quitar `/vendor` desde el archivo `.gitignore` para que Git incluya ("no ignore") todo el contenido del directorio `vendor` al ejecutar e implementar el código.

Vamos a configurar estas tareas de forma secuencial.

4. Establezca la versión de PHP que requiere la aplicación Laravel.

        azure site set --php-version 5.6

    Con esto, ya ha terminado la configuración de la versión de PHP.
    
4. Genere una nueva `APP_KEY` para la aplicación web de Azure y establézcala como configuración de esta aplicación.

        php artisan key:generate --show
        azure site appsetting add APP_KEY="<output_of_php_artisan_key:generate_--show>"

4. Además, active la depuración de Laravel para reemplazar la típica página críptica de `Whoops, looks like something went wrong.`.

        azure site appsetting add APP_DEBUG=true

    Con esto, ya ha terminado la configuración de las variables de entorno.
    
    >[AZURE.NOTE] Espere, vamos a detenernos un momento y a explicar lo que hace Laravel y lo que hace Azure aquí. Laravel utiliza el archivo `.env` del directorio raíz para proporcionar variables de entorno a la aplicación, en la que encontrará la línea `APP_DEBUG=true` (y también la línea `APP_KEY=...`). Se tiene acceso a esta variable en `config/app.php` mediante el código `'debug' => env('APP_DEBUG', false),`. [env()](https://laravel.com/docs/5.2/helpers#method-env) es un método auxiliar Laravel que utiliza el comando PHP [getenv()](http://php.net/manual/en/function.getenv.php) en segundo plano.
    >
    >Sin embargo, Git omite `.env` ya que lo ha llamado el archivo `.gitignore` del directorio raíz. En pocas palabras, el comando `.env` del repositorio local de Git no se inserta en Azure con el resto de los archivos. Por supuesto, puede simplemente quitar esa línea desde `.gitignore`, pero ya hemos visto que no es recomendable ejecutar este archivo en el control de código fuente. No obstante, necesitará una manera de especificar estas variables de entorno en Azure.
    >
    >La buena noticia es que la configuración de la aplicación de Servicio de aplicaciones de Azure es compatible con [getenv()](http://php.net/manual/en/function.getenv.php) en PHP. Así, aunque puede usar FTP u otros medios para cargar manualmente un archivo `.env` en Azure, basta con especificar las variables que desea como configuración de la aplicación sin tener que cargar el archivo `.env` en Azure, como acaba de hacer. Es más, si la variable está en un archivo `.env` y en la configuración de la aplicación de Azure, esta última tiene prioridad.

4. Para las dos últimas tareas (configuración del directorio virtual y habilitación de Composer) se necesita el [Portal de Azure](https://portal.azure.com), así que inicie sesión en el [portal](https://portal.azure.com) con su cuenta de Azure.

4. Desde el menú de la izquierda, haga clic en **Servicios de aplicaciones** > **& lt; app\_name >** > **Herramientas**.

    ![Habilite Composer para la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-composer-tools.png)
    
    >[AZURE.TIP] Si hace clic en **Configuración** en lugar de en **Herramientas**, podrá acceder a la hoja **Configuración de la aplicación**, lo que le permitirá establecer las versiones de PHP, la configuración de la aplicación y los directorios virtuales como lo acaba de hacer.
    
4. Haga clic en **Extensiones** > **Agregar** para agregar una extensión.

4. Seleccione **Composer** en la [hoja](../azure-portal-overview.md) **Elegir extensión** (*hoja*: una página del portal que se abre horizontalmente).

4. Haga clic en **Aceptar** en la hoja **Aceptar los términos legales**.

5. Haga clic en **Aceptar** en la hoja **Agregar extensión**.

    Cuando Azure haya terminado de agregar la extensión, verá un mensaje descriptivo emergente en la esquina, y también aparecerá **Composer** en la lista de la hoja **Extensiones**.

    ![Hoja de extensiones después de habilitar Composer para la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-composer-end.png)

    Con esto, ya ha terminado la habilitación de Composer.
    
4. De nuevo en la hoja de la aplicación web, haga clic en **Configuración** > **Configuración de la aplicación**.

    ![Acceda a la hoja Configuración para configurar el directorio virtual para la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings.png)

    En la hoja **Configuración de la aplicación**, tenga en cuenta la versión de PHP que estableció anteriormente:

    ![Versión de PHP en la hoja Configuración de la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-a.png)

    y la configuración de la aplicación agregada:
    
    ![Configuración de la aplicación en la hoja Configuración de la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-b.png)

4. Desplácese hasta la parte inferior de la hoja y cambie el directorio raíz virtual para que apunte a **site\\wwwroot\\public** en lugar de a **site\\wwwroot**.

    ![Configure el directorio virtual para la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-virtual-dir-public.png)

4. Haga clic en **Guardar** en la parte superior de la hoja.

    Con esto, ya ha terminado la configuración del directorio virtual.

## Implementación de la aplicación web con Git (y configuración de las variables de entorno)

Ya está listo para implementar el código. Podrá hacerlo cuando vuelva al símbolo del sistema o terminal.

4. Confirme todos los cambios e implemente el código en la aplicación web de Azure como lo haría en cualquier repositorio de Git:

        git add .
        git commit -m "Hurray! My first commit for my Azure app!"
        git push azure master 

    Cuando ejecute `git push`, se le pedirá que proporcione la contraseña de implementación de Git. Si solicitó crear credenciales de implementación anteriormente en `azure site create`, escriba la contraseña que utilizó.
    
5. Podemos ver la ejecución en el explorador, ejecutando este comando:

        azure site browse

    El explorador debe mostrarle la pantalla de presentación de Laravel.
    
    ![Pantalla de presentación de Laravel después de implementar la aplicación web en Azure](./media/app-service-web-php-get-started/laravel-azure-splash-screen.png)
    
    Enhorabuena, ahora ya está ejecutando una aplicación web de Laravel en Azure.
             
## Solución de errores comunes

Aquí puede ver algunos de los errores que pueden aparecer al seguir este tutorial:

- [La CLI de Azure muestra el mensaje "'site' is not an azure command" ('site' no es un comando de Azure)](#clierror)
- [La aplicación web muestra el error HTTP 403](#http403)
- [La aplicación web muestra el mensaje "Vaya, parece que hubo algún error".](#whoops)
- [La aplicación web muestra el mensaje "No supported encryptor found." (No se encontró ningún sistema de cifrado compatible).](#encryptor)

<a name="clierror"></a>
### La CLI de Azure muestra el mensaje "'site' is not an azure command" ('site' no es un comando de Azure)

Cuando se ejecuta `azure site *` en el terminal de la línea de comandos, puede ver el error `error:   'site' is not an azure command. See 'azure help'.`

Por lo general, suele ser el resultado de cambiar al modo "ARM" (Azure Resource Manager). Para resolver este problema, vuelva a cambiar al modo "ASM" (Azure Resource Manager) mediante la ejecución de `azure config mode asm`.

<a name="http403"></a>
### La aplicación web muestra el error HTTP 403

Ha implementado correctamente la aplicación web en Azure, pero cuando va a la aplicación, obtiene un error `HTTP 403` o `You do not have permission to view this directory or page.`

Es probable que la aplicación web no pueda encontrar el punto de entrada a la aplicación Laravel. Asegúrese de que ha cambiado el directorio raíz virtual para que apunte a `site\wwwroot\public`, donde está el archivo `index.php` de Laravel (consulte [Configuración de la aplicación web de Azure](#configure)).

<a name="whoops"></a>
### La aplicación web muestra el mensaje "Vaya, parece que hubo algún error".

Ha implementado correctamente la aplicación web en Azure, pero cuando va a la aplicación, obtiene el críptico mensaje `Whoops, looks like something went wrong.`

Para obtener un error más descriptivo, habilite la depuración de Laravel estableciendo la variable de entorno `APP_DEBUG` en `true` (consulte [Configuración de la aplicación web de Azure](#configure)).

<a name="encryptor"></a>
### La aplicación web muestra el mensaje "No supported encryptor found." (No se encontró ningún sistema de cifrado compatible).

Ha implementado correctamente la aplicación web en Azure, pero cuando va a la aplicación, obtiene el mensaje de error siguiente:

![No se encuentra APP\_KEY en su aplicación PHP (Laravel) de Azure](./media/app-service-web-php-get-started/laravel-error-APP_KEY.png)
    
Es un error desagradable, pero al menos no es tan críptico ya que se activó la depuración de Laravel. Una búsqueda rápida de la cadena de error en los foros de Laravel mostrará que esto se debe a que no se ha establecido la APP\_KEY en `.env`, o en su caso, a que no tiene el archivo `.env` en Azure en absoluto. Puede solucionar este problema mediante la adición de `APP_KEY` como configuración de aplicación de Azure (consulte [Configuración de la aplicación web de Azure](#configure)).
    
## Pasos siguientes

Obtenga información acerca de cómo agregar datos a la aplicación mediante la [creación de una base de datos MySQL en Azure](../store-php-create-mysql-database.md). Además, revise vínculos más útiles para PHP en Azure a continuación:

- [Centro para desarrolladores de PHP](/develop/php/).
- [Creación de una aplicación web desde Azure Marketplace](app-service-web-create-web-app-from-marketplace.md)
- [Configuración de PHP en Aplicaciones web de Servicio de aplicaciones de Azure](web-sites-php-configure.md)
- [Conversión de WordPress en multisitios en Servicio de aplicaciones de Azure](web-sites-php-convert-wordpress-multisite.md)
- [WordPress de clase empresarial en el Servicio de aplicaciones de Azure](web-sites-php-enterprise-wordpress.md)

<!---HONumber=AcomDC_0608_2016-->