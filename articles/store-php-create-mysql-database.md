<properties
    pageTitle="Creación y conexión de una base de datos MySQL en Azure"
    description="Obtenga información sobre cómo usar el Portal de Azure para crear una base de datos MySQL y, después, conectarse a ella desde una aplicación web PHP en Azure."
    documentationCenter="php"
    services="app-service\web"
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm;cephalin"/>


# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Creación y conexión de una base de datos MySQL en Azure

En este tutorial se le enseñará a crear una base de datos MySQL en [Azure Portal](https://portal.azure.com) (el proveedor es [ClearDB](http://www.cleardb.com/)) y cómo conectarse a ella desde una aplicación web PHP que se ejecute en [Azure App Service](./app-service/app-service-value-prop-what-is.md). 

> [AZURE.NOTE] También puede crear una base de datos MySQL como parte de una [plantilla de aplicación de Marketplace](./app-service-web/app-service-web-create-web-app-from-marketplace.md).

## <a name="create-a-mysql-database-in-azure-portal"></a>Creación una base de datos MySQL en el Portal de Azure

Para restaurar una base de datos MySQL en el Portal de Azure, haga lo siguiente:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú izquierdo, haga clic en **Nuevo** > **Datos y almacenamiento** > **Base de datos MySQL**.

    ![Creación una base de datos MySQL en Azure: inicio](./media/store-php-create-mysql-database/create-db-1-start.png)

2. En la [hoja](azure-portal-overview.md)Nueva base de datos MySQL, configure la nueva base de datos MySQL tal y como se muestra a continuación (*hoja*: una página del Portal que se abre horizontalmente):

    - **Nombre de la base de datos**: escriba un nombre identificable de forma única.
    - **Suscripción**: elija la suscripción que va a utilizar
    - **Tipo de base de datos**: seleccione **Compartido** para los niveles de bajo coste o gratuitos, o bien **Dedicado** si desea obtener recursos específicos. 
    - **Grupo de recursos**: agregue la base de datos MySQL a un [grupo de recursos](../resource-group-overview.md) existente, o bien colóquela en uno nuevo. Los recursos del mismo grupo pueden administrarse juntos de manera sencilla.
    - **Ubicación**: elija una ubicación cercana a usted. Cuando se agrega la base de datos a un grupo de recursos existente, no podrá acceder a la ubicación de este.
    - **Plan de tarifa**: haga clic en **Plan de tarifa**; después, seleccione una opción de precios (el nivel **Mercurio** es gratuito) y, luego, haga clic en **Seleccionar**. 
    - **Condiciones legales**: haga clic en **Condiciones legales**, revise los detalles de la compra y haga clic en **Comprar**.
    - **Anclar al panel**: seleccione si desea acceder directamente desde el panel. Esta opción le resultará especialmente útil si todavía se está familiarizando con la navegación por el Portal.
    
    La siguiente captura de pantalla es tan solo un ejemplo de cómo puede configurar la base de datos MySQL.  
    ![Creación una base de datos MySQL en Azure: configuración](./media/store-php-create-mysql-database/create-db-2-configure.png)

3. Cuando haya terminado, haga clic en **Crear**.

    ![Creación una base de datos MySQL en Azure: creación](./media/store-php-create-mysql-database/create-db-3-create.png)

    Verá una notificación emergente donde se le informará que se ha iniciado la implementación.

    ![Creación una base de datos MySQL en Azure: en curso](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    Se mostrará otra ventana emergente cuando se complete correctamente la implementación. El Portal también abrirá automáticamente la hoja de la base de datos MySQL.

<a name="connect"></a>
## <a name="connect-to-your-mysql-database"></a>Conexión a la base de datos MySQL

Para ver la información de conexión de la nueva base de datos MySQL, haga clic en **Propiedades** en la hoja de la aplicación web.
    
![Creación una base de datos MySQL en Azure: hoja Base de datos MySQL](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Ahora puede emplear esa información de conexión en cualquier aplicación web. [Aquí](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql)puede encontrar un ejemplo que muestra cómo utilizar la información de conexión de una aplicación PHP simple.

## <a name="connect-a-laravel-web-app-(from-the-php-get-started-tutorial)"></a>Conexión de una aplicación web de Laravel (del tutorial de introducción de PHP)

Si, por ejemplo, acaba de finalizar el tutorial [Create, configure, and deploy a PHP web app to Azure](./app-service-web/app-service-web-php-get-started.md) (Creación, configuración e implementación de aplicaciones web PHP en Azure) y tiene una aplicación web de [Laravel](https://www.laravel.com/) que se ejecuta en Azure, podrá agregar fácilmente las funcionalidades de base de datos a la aplicación Laravel. Siga los pasos que se indican a continuación:

>[AZURE.NOTE] En los siguientes pasos se da por hecho que ha finalizado la tutorial [Create, configure, and deploy a PHP web app to Azure](./app-service-web/app-service-web-php-get-started.md)(Creación, configuración e implementación de aplicaciones web PHP en Azure).

1. Configure la aplicación Laravel en su entorno de desarrollo local para que apunte a la base de datos MySQL. Para ello, abra `.env` desde el directorio raíz de la aplicación Laravel y configure las opciones de base de datos de MySQL.

        DB_CONNECTION=mysql
        DB_HOST=<HOSTNAME_from_properties_blade>
        DB_PORT=<PORT_from_properties_blade>
        DB_DATABASE=<see_note_below>
        DB_USERNAME=<USERNAME_from_properties_blade>
        DB_PASSWORD=<PASSWORD_from_properties_blade>

    >[AZURE.NOTE] En la hoja **Propiedades**, puede que no se muestre el nombre de la base de datos MySQL en el campo **NOMBRE DE LA BASE DE DATOS**. Se recomienda comprobar el parámetro Database del campo **CADENA DE CONEXIÓN** . 
    >
    >![Creación una base de datos MySQL en Azure: en curso](./media/store-php-create-mysql-database/connect-db-1-database-name.png)

2. La forma más rápida de comprobar que ahora tiene acceso a MySQL es usar la técnica [scaffolding de autenticación predeterminada de Laravel](https://laravel.com/docs/5.2/authentication#authentication-quickstart). En el terminal de la línea de comandos, ejecute los siguientes comandos desde el directorio raíz de la aplicación Laravel:

         php artisan migrate
         php artisan make:auth

    El primer comando crea las tablas en Azure según las migraciones predefinidas del directorio `database/migrations`, mientras que el segundo comando aplica la técnica scaffolding en las vistas básicas y las rutas de autenticación y registro de usuarios.

3. Ejecute ahora el servidor de desarrollo:

        php artisan serve

4. En el explorador, vaya a http://localhost:8000 y registre un nuevo usuario tal y como se muestra:

    ![Conexión a la base de datos MySQL en Azure: registro de usuarios](./media/store-php-create-mysql-database/connect-db-2-development-server.png)

    Siga las indicaciones de la interfaz de usuario para completar el registro. Cuando se haya registrado, se iniciará la sesión.
    
    ![Conexión a la base de datos MySQL en Azure: registro de usuarios](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)

    Ahora está desarrollando la aplicación en la base de datos MySQL de Azure.

5. Ahora, solo tiene que replicar la configuración de `.env` en la aplicación web de Azure. Ejecute los siguientes comandos de la CLI de Azure:

        azure site appsetting add DB_CONNECTION=mysql
        azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
        azure site appsetting add DB_PORT=<PORT_from_properties_blade>
        azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
        azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
        azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>

    Descubra cómo funciona en la sección de [configuración de la aplicación web de Azure](./app-service-web/app-service-web-php-get-started.md#configure).

6. Después, confirme y envíe a Azure los cambios locales realizados anteriormente durante la ejecución de `php artisan make:auth`.

        git add .
        git commit -m "scaffold auth views and routes"
        git push azure master

7. Vaya a la aplicación web de Azure.

        azure site browse

8. Inicie sesión con las credenciales de usuario que creó antes.

    ![Conexión a la base de datos MySQL en Azure: acceso a la aplicación web de Azure](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)

    Después de iniciar sesión, deberá ver la pantalla posterior a este paso, con una intuitiva interfaz.
    
    ![Conexión a la base de datos MySQL en Azure: sesión iniciada](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)

    Enhorabuena. La aplicación web PHP de Azure ahora tiene acceso a datos de la base de datos MySQL. 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de PHP](/develop/php/).



<!--HONumber=Oct16_HO2-->


