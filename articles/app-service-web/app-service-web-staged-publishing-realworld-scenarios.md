---
title: Uso eficaz de entornos DevOps para las aplicaciones web | Microsoft Docs
description: "Aprenda a usar ranuras de implementación para configurar y administrar varios entornos de desarrollo para la aplicación"
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 16a594dc-61f5-4984-b5ca-9d5abc39fb1e
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
translationtype: Human Translation
ms.sourcegitcommit: 385eb87ec32f5f605b28cc8c76b1c89c7e90bfec
ms.openlocfilehash: 5284022ea473db893800b0f64b5bf4f811d994aa
ms.lasthandoff: 12/20/2016


---
# <a name="use-devops-environments-effectively-for-your-web-apps"></a>Uso eficaz de entornos DevOps para las aplicaciones web
En este artículo se muestra cómo configurar y administrar implementaciones de aplicaciones web cuando hay varias versiones de la aplicación en diversos entornos, como desarrollo, ensayo, control de calidad (QA) y producción. Cada versión de la aplicación puede considerarse como un entorno de desarrollo para la finalidad específica de su proceso de implementación. Por ejemplo, los desarrolladores pueden usar el entorno de QA para probar la calidad de la aplicación antes de insertar los cambios en producción.
El uso de varios entornos de desarrollo puede presentar dificultades, dado que debe realizar el seguimiento del código, administrar los recursos (proceso, aplicación web, base de datos, caché, etc.) e implementar código entre entornos.

## <a name="set-up-a-non-production-environment-stage-dev-qa"></a>Configuración de un entorno que no es de producción (ensayo, desarrollo, control de calidad)
Una vez que la aplicación web de producción está en funcionamiento, el siguiente paso consiste en crear un entorno que no sea de producción. Para usar espacios de implementación, asegúrese de estar ejecutando el modo del plan Estándar o Premium de Azure App Service. Los espacios de implementación son aplicaciones web activas que tienen sus propios nombres de host. Los elementos de contenido y configuración de aplicaciones web se pueden intercambiar entre dos espacios de implementación, incluida la ranura de producción. Cuando implementa la aplicación en un espacio de implementación, obtiene las ventajas siguientes:

- Puede validar los cambios en la aplicación web en un espacio de implementación de ensayo antes de intercambiar la aplicación con el espacio de producción.
- Cuando implementa una aplicación web en un espacio en primer lugar y después la intercambia con producción, todas las instancias del espacio están correctas antes de colocarse en producción. Este proceso elimina tiempos de inactividad al implementar la aplicación web. El redireccionamiento del tráfico es impecable y no se anulan las solicitudes como consecuencia de las operaciones de intercambio. Para automatizar este flujo de trabajo completo, configure [Auto Swap](web-sites-staged-publishing.md#configure-auto-swap) (Intercambio automático) cuando no se necesite la validación antes del intercambio.
- Después de un intercambio, el espacio que contenía la aplicación web tiene ahora la aplicación web de producción anterior. Si los cambios intercambiados con el espacio de producción no son los esperados, puede realizar el mismo intercambio inmediatamente para volver a la "última aplicación web buena conocida".

Para configurar una ranura de implementación de ensayo, consulte [Configuración de entornos de ensayo para aplicaciones web en Azure App Service](web-sites-staged-publishing.md) . Todos los entornos deberían incluir su conjunto de recursos propio. Por ejemplo, si la aplicación web utiliza una base de datos, las aplicaciones web de producción y de ensayo deben usar bases de datos diferentes. Agregue recursos de entorno de desarrollo de ensayo, como una base de datos, almacenamiento o caché, para configurar el entorno de desarrollo de ensayo.

## <a name="examples-of-using-multiple-development-environments"></a>Ejemplos del uso de varios entornos de desarrollo
Cualquier proyecto debería poner en práctica la administración de código fuente con al menos dos entornos: desarrollo y producción. Si usa sistemas de administración de contenido (CMS), marcos de aplicaciones, etc., es posible que la aplicación no admita este escenario sin personalización. Esta posibilidad es aplicable a algunos de los marcos de trabajo habituales que se describen en las secciones siguientes. Vienen a la mente muchas preguntas cuando se trabaja con CMS o marcos, como por ejemplo:

- ¿Cómo se extrae el contenido y se introduce en entornos diferentes?
- ¿Qué archivos se pueden cambiar sin afectar a las actualizaciones de versión del marco?
- ¿Cómo se administran las configuraciones para cada entorno?
- ¿Cómo se administran las actualizaciones de versión de módulos, complementos y el marco de trabajo principal?

Existen muchas maneras de configurar varios entornos para el proyecto. En los ejemplos siguientes, se muestra un método para cada aplicación.

### <a name="wordpress"></a>WordPress
En esta sección, obtendrá información sobre cómo configurar un flujo de trabajo de implementación mediante espacios para WordPress. WordPress, al igual que la mayoría de las soluciones CMS, no admite varios entornos de desarrollo sin llevar a cabo personalización. La característica Web Apps de Azure App Service incluye algunas características que facilitan el almacenamiento de la configuración fuera del código.

1. Antes de crear un espacio de ensayo, configure el código de la aplicación para admita varios entornos. Para admitir varios entornos en WordPress, debe editar `wp-config.php` en la aplicación web de desarrollo local y agregar el código siguiente al principio del archivo. Este proceso permitirá que la aplicación seleccione la configuración correcta en función del entorno seleccionado.

    ```
    // Support multiple environments
    // set the config file based on current environment
    if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) {
    // local development
     $config_file = 'config/wp-config.local.php';
    }
    elseif ((strpos(getenv('WP_ENV'),'stage') !== false) || (strpos(getenv('WP_ENV'),'prod' )!== false ))
    //single file for all azure development environments
     $config_file = 'config/wp-config.azure.php';
    }
    $path = dirname(__FILE__). '/';
    if (file_exists($path. $config_file)) {
    // include the config file if it exists, otherwise WP is going to fail
    require_once $path. $config_file;
    ```

2. Cree una carpeta bajo la raíz de la aplicación web llamada `config` y agregue los archivos `wp-config.azure.php` y `wp-config.local.php`, que representan el entorno de Azure y el entorno local, respectivamente.

3. Copie lo siguiente en `wp-config.local.php`:

    ```
    <?php
    // MySQL settings
    /** The name of the database for WordPress */

    define('DB_NAME', 'yourdatabasename');

    /** MySQL database username */
    define('DB_USER', 'yourdbuser');

    /** MySQL database password */
    define('DB_PASSWORD', 'yourpassword');

    /** MySQL hostname */
    define('DB_HOST', 'localhost');
    /**
     * For developers: WordPress debugging mode.
     * * Change this to true to enable the display of notices during development.
     * It is strongly recommended that plugin and theme developers use WP_DEBUG
     * in their development environments.
     */
    define('WP_DEBUG', true);

    //Security key settings
    define('AUTH_KEY', 'put your unique phrase here');
    define('SECURE_AUTH_KEY','put your unique phrase here');
    define('LOGGED_IN_KEY','put your unique phrase here');
    define('NONCE_KEY', 'put your unique phrase here');
    define('AUTH_SALT', 'put your unique phrase here');
    define('SECURE_AUTH_SALT', 'put your unique phrase here');
    define('LOGGED_IN_SALT', 'put your unique phrase here');
    define('NONCE_SALT', 'put your unique phrase here');

    /**
     * WordPress Database Table prefix.
     *
     * You can have multiple installations in one database if you give each a unique
     * prefix. Only numbers, letters, and underscores please!
     */
    $table_prefix = 'wp_';
    ```

    Establecer las claves de seguridad como se muestra en el código anterior puede ayudar a evitar que se piratee la aplicación web, así que debería usar valores únicos. Si debe generar la cadena para las claves de seguridad mencionadas en el código, puede [ir al generador automático](https://api.wordpress.org/secret-key/1.1/salt) para crear pares clave-valor.

4. Copie el siguiente código en `wp-config.azure.php`:

    ```    
    <?php
    // MySQL settings
    /** The name of the database for WordPress */

    define('DB_NAME', getenv('DB_NAME'));

    /** MySQL database username */
    define('DB_USER', getenv('DB_USER'));

    /** MySQL database password */
    define('DB_PASSWORD', getenv('DB_PASSWORD'));

    /** MySQL hostname */
    define('DB_HOST', getenv('DB_HOST'));

    /**
    * For developers: WordPress debugging mode.
    *
    * Change this to true to enable the display of notices during development.
    * It is strongly recommended that plugin and theme developers use WP_DEBUG
    * in their development environments.
    * Turn on debug logging to investigate issues without displaying to end user. For WP_DEBUG_LOG to
    * do anything, WP_DEBUG must be enabled (true). WP_DEBUG_DISPLAY should be used in conjunction
    * with WP_DEBUG_LOG so that errors are not displayed on the page */

    */
    define('WP_DEBUG', getenv('WP_DEBUG'));
    define('WP_DEBUG_LOG', getenv('TURN_ON_DEBUG_LOG'));
    define('WP_DEBUG_DISPLAY',false);

    //Security key settings
    /** If you need to generate the string for security keys mentioned above, you can go the automatic generator to create new keys/values: https://api.wordpress.org/secret-key/1.1/salt **/
    define('AUTH_KEY',getenv('DB_AUTH_KEY'));
    define('SECURE_AUTH_KEY', getenv('DB_SECURE_AUTH_KEY'));
    define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
    define('NONCE_KEY', getenv('DB_NONCE_KEY'));
    define('AUTH_SALT', getenv('DB_AUTH_SALT'));
    define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
    define('LOGGED_IN_SALT',  getenv('DB_LOGGED_IN_SALT'));
    define('NONCE_SALT',  getenv('DB_NONCE_SALT'));

    /**
    * WordPress Database Table prefix.
    *
    * You can have multiple installations in one database if you give each a unique
    * prefix. Only numbers, letters, and underscores please!
    */
    $table_prefix = getenv('DB_PREFIX');
    ```

#### <a name="use-relative-paths"></a>Uso de rutas de acceso relativas
Lo último que hay que configurar en la aplicación de WordPress son las rutas de acceso relativas. WordPress almacena la información de dirección URL en la base de datos. Este almacenamiento dificulta el traslado de contenido de un entorno a otro. Debe actualizar la base de datos cada vez que se mueva del entorno local al de ensayo, o del de ensayo al de producción. Para reducir el riesgo de que surjan problemas que pueden deberse a la implementación de la base de datos cada vez que se pasa de un entorno a otro, use el [complemento de vínculos relativos a la raíz](https://wordpress.org/plugins/root-relative-urls/), que se puede instalar mediante el panel de administrador de WordPress.

Agregue las siguientes entradas al archivo `wp-config.php` delante del comentario `That's all, stop editing!`:

```

  define('WP_HOME', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_SITEURL', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_CONTENT_URL', '/wp-content');
    define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

Active el complemento en el menú `Plugins` del panel de administrador de WordPress. Guarde la configuración de vínculo permanente para la aplicación de WordPress.

#### <a name="the-final-wp-configphp-file"></a>El archivo `wp-config.php` final
Las actualizaciones principales de WordPress no afectan a los archivos `wp-config.php`, `wp-config.azure.php` ni `wp-config.local.php`. Esta es una versión final del archivo `wp-config.php`:

```
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, and ABSPATH. You can find more information by visiting
 *
 * Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web web app, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) { // local development
  $config_file = 'config/wp-config.local.php';
}
elseif ((strpos(getenv('WP_ENV'),'stage') !== false) ||(strpos(getenv('WP_ENV'),'prod' )!== false )){
  $config_file = 'config/wp-config.azure.php';
}


$path = dirname(__FILE__). '/';
if (file_exists($path. $config_file)) {
  // include the config file if it exists, otherwise WP is going to fail
  require_once $path. $config_file;
}

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/* That's all, stop editing! Happy blogging. */

define('WP_HOME', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_SITEURL', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_CONTENT_URL', '/wp-content');
define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__). '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH. 'wp-settings.php');
```

#### <a name="set-up-a-staging-environment"></a>Configuración de un entorno de ensayo
1. Si ya tiene ejecutándose una aplicación web de WordPress en su suscripción de Azure, inicie sesión en [Azure Portal](http://portal.azure.com)y vaya a la aplicación web de WordPress. Si carece de aplicación web de WordPress, puede crearla desde Azure Marketplace. Para más información, consulte [Creación de una aplicación web de WordPress en Azure App Service](web-sites-php-web-site-gallery.md).
Haga clic en **Settings** > **Deployment slots** > **Add** (Configuración > Espacios de implementación > Agregar) para crear un espacio de implementación llamado *stage*. Un espacio de implementación es otra aplicación web que comparte los mismos recursos que la aplicación web principal creada antes.

    ![Creación de la ranura de implementación de ensayo](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

2. Agregue otra base de datos MySQL, como `wordpress-stage-db`, al grupo de recursos `wordpressapp-group`.

    ![Incorporación de una base de datos de MySQL a un grupo de recursos](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

3. Actualice las cadenas de conexión para el espacio de implementación de ensayo de forma que apunten a la nueva base de datos, `wordpress-stage-db`. La aplicación web de producción, `wordpressprodapp`, y la aplicación web de ensayo, `wordpressprodapp-stage`, deben apuntar a bases de datos diferentes.

#### <a name="configure-environment-specific-app-settings"></a>Configuración de valores de aplicación específicos del entorno
Los desarrolladores pueden almacenar pares clave-valor en Azure como parte de la información de configuración, llamada **App Settings** (Configuración de aplicación), que está asociada con una aplicación web. En tiempo de ejecución, las aplicaciones web recuperan automáticamente estos valores y los ponen a disposición del código que se ejecuta en la aplicación web. Desde el punto de vista de la seguridad, esto resulta beneficioso, ya que la información confidencial, como las cadenas de conexión de base de datos que incluyen contraseñas, no se muestran nunca sin cifrar en un archivo como `wp-config.php`.

Este proceso, que se explica en los párrafos siguientes, es útil porque incluye cambios tanto de archivo como de base de datos para la aplicación de WordPress:

* Actualización de versión de WordPress
* Agregar nuevos complementos, o bien editar o actualizar los existentes
* Agregar nuevos temas o editar o actualizar los existentes

Configure los valores de aplicación para:

* Información de base de datos
* Activar o desactivar el registro de WordPress
* Configurar la seguridad de WordPress

![Configuración de aplicaciones para la aplicación web de Wordpress](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

Asegúrese de agregar la siguiente configuración de aplicación para el espacio de ensayo y la aplicación web de producción. Tenga en cuenta que la aplicación web de producción y la de ensayo usan diferentes bases de datos.

1. Desactive la casilla **Slot Setting** (Configuración de espacios) para todos los parámetros de configuración, excepto WP_ENV. Este proceso cambiara la configuración de la aplicación web, del contenido del archivo y de la base de datos. Si se activa **Slot Setting** (Configuración de espacios), la configuración de aplicación y la configuración de cadena de conexión de la aplicación web *no* se moverá entre entornos cuando se realice una operación de **intercambio**. Los cambios de base de datos que estén presentes no interrumpirán la aplicación web de producción.

2. Implemente la aplicación web del entorno de desarrollo local en la aplicación web y la base de datos de ensayo mediante WebMatrix u otra herramienta de su elección, como FTP, Git o PhpMyAdmin.

    ![Cuadro de diálogo de publicación de WebMatrix para una aplicación web de WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

3. Examine y pruebe la aplicación web provisional. Si consideramos un escenario en el que se va actualizar el tema de la aplicación web, esta sería la aplicación web de ensayo.

    ![Examen de la aplicación web de ensayo antes del intercambio de ranuras](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)

4. Si todo está bien, haga clic en el botón **Swap** (Intercambiar) en la aplicación web de ensayo para mover el contenido al entorno de producción. En este caso, intercambia la aplicación web y la base de datos entre entornos durante cada operación de **intercambio**.

    ![Vista previa de los cambios de intercambio para WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

    > [!NOTE]
    > Si su escenario solo necesita insertar archivos (sin actualizaciones de la base de datos), active la casilla **Slot Setting** (Configuración de espacios) para todos los *valores de configuración de aplicación* y de *configuración de cadenas de conexión* que estén relacionados con la base de datos en la hoja **Web App Settings** (Configuración de aplicación web), en Azure Portal, antes de realizar el **intercambio**. En este caso, DB_NAME, DB_HOST, DB_PASSWORD, DB_USER y la configuración predeterminada de cadena de conexión no deberían aparecer en los cambios de versión preliminar cuando realice un **intercambio**. En este momento, cuando finalice la operación de **intercambio**, la aplicación web de WordPress solo tendrá los archivos de actualizaciones.
    >
    >

    Antes de realizar un **intercambio**, la aplicación web de WordPress de producción es esta.
    ![Aplicación web de producción antes del intercambio de espacios](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

    Después de la operación de **intercambio**, el tema se actualiza en la aplicación web de producción.

    ![Aplicación web de producción después del intercambio de ranuras](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

5. En una situación en que necesite revertir a una versión anterior, puede ir a **App Settings** (Configuración de aplicación) para la aplicación web de producción y hacer clic en el botón **Swap** (Intercambiar) para cambiar la aplicación web y la base de datos del espacio de producción al de ensayo. Recuerde que si se incluyen los cambios de base de datos con una operación de **intercambio**, la próxima vez que implemente en la aplicación web de ensayo, debe implementar los cambios de la base de datos en la base de datos actual de la aplicación web de ensayo. La base de datos podría ser la base de datos de producción anterior o la de ensayo.

#### <a name="summary"></a>Resumen
A continuación, se incluye un proceso generalizado para cualquier aplicación que tenga una base de datos:

1. Instale la aplicación en el entorno local.
2. Incluya configuraciones específicas del entorno (local y Azure Web Apps).
3. Configure los entornos de ensayo y de producción para Web Apps.
4. Si ya está ejecutando una aplicación de producción en Azure, sincronice el contenido de producción (archivos y código + base de datos) con el entorno local y el de ensayo.
5. Desarrolle la aplicación en el entorno local.
6. Coloque la aplicación web de producción en modo de mantenimiento o bloqueo y sincronice el contenido de la base de datos de producción con los entornos de ensayo y de desarrollo.
7. Impleméntela en el entorno de ensayo y pruébela.
8. Impleméntela en el entorno de producción.
9. Repita los pasos del 4 al 6.

### <a name="umbraco"></a>Umbraco
En esta sección, aprenderá cómo la solución CMS de Umbraco usa un módulo personalizado para implementar entre varios entornos DevOps. En este ejemplo se proporciona un enfoque diferente para administrar varios entornos de desarrollo.

El [CMS de Umbraco](http://umbraco.com/) es una solución de CMS .NET popular entre muchos desarrolladores. Proporciona el módulo [Courier2](http://umbraco.com/products/more-add-ons/courier-2) para implementar desde un entorno de desarrollo a entornos de ensayo y producción. Puede crear fácilmente un entorno de desarrollo local para una aplicación web de CMS de Umbraco mediante Visual Studio o WebMatrix.

- [Creación de una aplicación web de Umbraco con Visual Studio](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget)
- [Creación de una aplicación web de Umbraco con WebMatrix](http://umbraco.tv/videos/umbraco-v7/implementor/fundamentals/installation/creating-umbraco-site-from-webmatrix-web-gallery/)

Acuérdese siempre de quitar la carpeta `install` de la aplicación y de no cargarla nunca en aplicaciones web de ensayo o producción. En este tutorial se usa WebMatrix.

#### <a name="set-up-a-staging-environment"></a>Configuración de un entorno de ensayo
1. Cree un espacio de implementación como se mencionó antes para la aplicación web de CMS de Umbraco. Se supone que ya tiene una en funcionamiento. Si no, puede crear una desde Marketplace.
2. Actualice la cadena de conexión para el espacio de implementación de ensayo de modo que apunte a la nueva base de datos **umbraco-stage-db**. La aplicación web de producción (umbracositecms-1) y la aplicación web de ensayo (umbracositecms-1-stage) *deben* apuntar a bases de datos diferentes.

    ![Actualización de la cadena de conexión para la aplicación web de ensayo con la nueva base de datos de ensayo](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

3. Haga clic en **Get Publish settings** (Obtener configuración de publicación) para el espacio de implementación **stage**. Este proceso descargará un archivo de configuración de publicación que almacena toda la información que Visual Studio o WebMatrix necesita para publicar la aplicación desde la aplicación web de desarrollo local en la aplicación web de Azure.

    ![Obtención de configuración de publicación de la aplicación web de ensayo](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)
4. Abra la aplicación web de desarrollo local en WebMatrix o Visual Studio. En este tutorial se usa WebMatrix. En primer lugar, debe importar el archivo de configuración de publicación para la aplicación web de ensayo.

    ![Importación de la configuración de publicación para Umbraco mediante WebMatrix](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

5. Revise los cambios en el cuadro de diálogo e implemente la aplicación web local en la aplicación web de Azure: *umbracositecms-1-stage*. Al implementar archivos directamente en la aplicación web de ensayo, se omitirán los de la carpeta `~/app_data/TEMP/`, puesto que se volverán a generar la primera vez que se inicie la aplicación web de ensayo. Además, debe omitir el archivo `~/app_data/umbraco.config`, que también se volverá a generar.

    ![Revisión de los cambios de publicación en WebMatrix](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

6. Después de publicar correctamente la aplicación web local de Umbraco en la aplicación web de ensayo, busque esta última y ejecute algunas pruebas para descartar cualquier problema.

#### <a name="set-up-the-courier2-deployment-module"></a>Configuración del módulo de implementación Courier2
Con el módulo [Courier2](http://umbraco.com/products/more-add-ons/courier-2), basta con hacer clic con el botón derecho para insertar contenido, hojas de estilos y módulos de desarrollo desde una aplicación web de ensayo en una aplicación web de producción. Este proceso reduce el riesgo de que se interrumpa la aplicación web de producción cuando se implementa una actualización.
Adquiera una licencia para Courier2 para el dominio `*.azurewebsites.net` y su dominio personalizado (por ejemplo, http://abc.com). Después de adquirir la licencia, coloque la licencia descargada (archivo .LIC) en la carpeta `bin`.

![Eliminación del archivo de licencia de la carpeta bin](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

1. [Descargue el paquete de Courier2](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/). Inicie sesión en la aplicación web de ensayo (por ejemplo, http://umbracocms-site-stage.azurewebsites.net/umbraco), haga clic en el menú **Developer** (Desarrollador) y en **Packages** > **Install local package** (Paquetes > Instalar paquete local).

    ![Instalador de paquetes de Umbraco](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

2. Cargue el paquete de Courier2 mediante el instalador.

    ![Carga de paquete del módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

3. Para configurar el paquete, debe actualizar el archivo courier.config en la carpeta **Config** de la aplicación web.

    ```xml
    <!-- Repository connection settings -->
     <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
     <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
          <url>http://umbracositecms-1.azurewebsites.net</url>
          <user>0</user>
          <!--<login>user@email.com</login> -->
          <!-- <password>user_password</password>-->
          <!-- <passwordEncoding>Clear</passwordEncoding>-->
          </repository>
     </repositories>
     ```

4. En `<repositories>`, especifique la dirección URL del sitio de producción y la información de usuario.
    Si está usando el proveedor de pertenencia de Umbraco predeterminado, agregue el identificador del usuario de administración en la sección &lt;user&gt;.
    Si está usando un proveedor de pertenencia de Umbraco personalizado, use `<login>`,`<password>` en el módulo Courier2 para conectarse al sitio de producción.
    Para más información, [repase la documentación del módulo Courier2](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation).

5. De igual forma, instale el módulo Courier2 en el sitio de producción y configúrelo para que apunte a la aplicación web de ensayo en su archivo courier.config correspondiente, tal como se muestra aquí.

    ```xml
     <!-- Repository connection settings -->
     <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
     <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
        <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
          <url>http://umbracositecms-1-stage.azurewebsites.net</url>
          <user>0</user>
          </repository>
     </repositories>
    ```

6. Haga clic en la pestaña **Courier2** en el panel de la aplicación web de CMS de Umbraco y haga clic en **Locations** (Ubicaciones). Debería ver el nombre del repositorio tal como se mencionó en `courier.config`. Siga este proceso tanto en aplicaciones web de producción como de ensayo.

    ![Visualización del repositorio de la aplicación web de destino](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

7. Para implementar contenido desde el sitio de ensayo en el sitio de producción, vaya a **Content** (Contenido) y seleccione una página existente o cree una. Voy a seleccionar una página existente de mi aplicación web cuyo título es **Getting Started – new** (Introducción: nuevo) y a hacer clic en **Save and Publish** (Guardar y publicar).

    ![Cambio del título de la página y publicación](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

8. Haga clic con el botón derecho en la página modificada para ver todas las opciones. Haga clic en **Courier** para abrir el cuadro de diálogo **Deployment** (Implementación). Haga clic en **Deploy** (Implementar) para iniciar la implementación.

    ![Cuadro de diálogo de implementación del módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

9. Revise los cambios y haga clic en **Continue** (Continuar).

    ![Revisión de los cambios del cuadro de diálogo de implementación del módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

    El registro de implementación muestra si la implementación fue correcta.

     ![Visualización de los registros de implementación del módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

10. Examine la aplicación web de producción para ver si se reflejan los cambios.

     ![Examen de la aplicación web de producción](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Para obtener más información sobre cómo usar Courier, revise la documentación.

#### <a name="how-to-upgrade-the-umbraco-cms-version"></a>Actualización de la versión de CMS de Umbraco
Courier no ayudará con la actualización de una versión de CMS de Umbraco a otra. Al actualizar la versión de CMS de Umbraco, debe comprobar si hay incompatibilidades con los módulos personalizados o de asociados y las bibliotecas principales de Umbraco. Estos son los procedimientos recomendados:

* Realice siempre una copia de seguridad de su aplicación web y de la base de datos antes de actualizar. En las aplicaciones web de Azure, puede configurar copias de seguridad automáticas de los sitios web con la característica de copia de seguridad y restaurar el sitio en caso necesario mediante la característica de restauración. Para obtener más detalles, consulte [Realizar una copia de seguridad de la aplicación en Azure](web-sites-backup.md) y [Restaurar una aplicación en Azure](web-sites-restore.md).
* Compruebe si los paquetes de asociados son compatibles con la versión a la que se va a actualizar. En la página de descarga del paquete, revise la compatibilidad del proyecto con la versión de CMS de Umbraco.

Para más información sobre cómo actualizar la aplicación web localmente, [consulte las instrucciones generales de actualización](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general).

Una vez actualizado el sitio de desarrollo local, publique los cambios en la aplicación web de ensayo. Pruebe la aplicación. Si está todo bien, use el botón **Swap** (Intercambiar) para cambiar el sitio de ensayo a la aplicación web de producción. Cuando usa la operación de **intercambio**, puede ver los cambios que quedarán afectados en la configuración de la aplicación web. Esta operación de **intercambio** intercambia las aplicaciones web y las bases de datos. Después del **intercambio**, la aplicación web de producción apuntará a la base de datos umbraco-stage-db y la aplicación web de ensayo, a la base de datos umbraco-prod-db.

![Vista previa de intercambio para la implementación de CMS de Umbraco](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

Las ventajas de intercambiar tanto la aplicación web como la base de datos son las siguientes:

* Puede revertir a la versión anterior de la aplicación web con otro **intercambio** si hay algún problema con la aplicación.
* Para realizar una actualización, debe implementar los archivos y las bases de datos desde la aplicación web de ensayo en la aplicación web y la base de datos de producción. Al implementar archivos y bases de datos, pueden surgir muchos problemas. Gracias a la característica **Swap** (Intercambiar) de los espacios, se puede reducir tanto el tiempo de inactividad durante una actualización como el riesgo de que se produzcan errores al implementar cambios.
* Puede llevar a cabo **pruebas A/B** con la característica [Testing in production](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/).

Este ejemplo demuestra la flexibilidad de la plataforma, donde puede compilar módulos personalizados parecidos al módulo Courier de Umbraco para administrar la implementación entre entornos.

## <a name="references"></a>Referencias
[Agile Software Development con el Servicio de aplicaciones de Azure](app-service-agile-software-development.md)

[Configuración de entornos de ensayo para aplicaciones web en el Servicio de aplicaciones de Azure](web-sites-staged-publishing.md)

[How to block web access to non-production deployment slots (Bloqueo del acceso web a ranuras de implementación que no son de producción)](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

