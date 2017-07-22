---
title: "Compilación de una aplicación web PHP con MySQL en Azure | Microsoft Docs"
description: "Aprenda a comenzar a trabajar con una aplicación PHP en Azure, con conexión a una base de datos MySQL en Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 07/21/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: f7479260c7c2e10f242b6d8e77170d4abe8634ac
ms.openlocfilehash: 8a818a63409d914f82d2f0f8c894ba74ad8fa89d
ms.contentlocale: es-es
ms.lasthandoff: 06/21/2017

---
# <a name="build-a-php-and-mysql-web-app-in-azure"></a>Compilación de una aplicación web PHP y MySQL en Azure
[Azure Web Apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) proporciona un servicio de hospedaje web muy escalable y con aplicación de revisiones de un modo automático. En este tutorial, se muestra cómo crear una aplicación web PHP en Azure y conectarla a una base de datos MySQL. Cuando haya terminado, tendrá una aplicación [Laravel](https://laravel.com/) que se ejecuta en Azure App Service Web Apps.

![Aplicación PHP que se ejecuta en Azure App Service](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una base de datos MySQL en Azure
> * Conectar una aplicación PHP a MySQL
> * Implementación de la aplicación en Azure
> * Actualizar el modelo de datos y volver a implementar la aplicación
> * Transmitir registros de diagnóstico desde Azure
> * Administrar la aplicación en Azure Portal

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

* [Instalación de Git](https://git-scm.com/)
* [Instalación de PHP 5.6.4, o cualquier versión posterior](http://php.net/downloads.php)
* [Instalación de Composer](https://getcomposer.org/doc/00-intro.md)
* Habilitación de las siguientes extensiones PHP necesarias para Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer y XML
* [Instalación e inicio de MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tema es preciso que ejecute la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="prepare-local-mysql"></a>Preparación de MySQL local

En este paso, creará una base de datos en el servidor MySQL local para usarlo en este tutorial.

### <a name="connect-to-mysql-server"></a>Conexión a un servidor MySQL
En una ventana de terminal, conéctese al servidor MySQL local. Esta ventana de terminal se puede usar para ejecutar todos los comandos de este tutorial.

```bash
mysql -u root -p
```

Si se le pide una contraseña, escriba la de la cuenta `root`. Si no recuerda la contraseña de la cuenta raíz, consulte [MySQL: How to Reset the Root Password](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) (MySQL: Instrucciones de restablecimiento de la contraseña de la cuenta raíz).

Si el comando se ejecuta correctamente, significa que el servidor MySQL está en ejecución. De lo contrario, siga los [pasos posteriores a la instalación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html) para asegurarse de iniciar el servidor local de MySQL.

### <a name="create-a-database"></a>Creación de una base de datos

En el símbolo del sistema de `mysql`, cree una base de datos.

```sql 
CREATE DATABASE sampledb;
```

Escriba `quit` para salir de la conexión del servidor.

```sql
quit
```

<a name="step2"></a>

## <a name="create-local-php-app"></a>Creación de aplicación PHP local
En este paso, obtendrá una aplicación Laravel de ejemplo, configurará la conexión a base de datos correspondiente y la ejecutará de forma local. 

### <a name="clone-the-sample"></a>Clonación del ejemplo

En la ventana del terminal, use `cd` para cambiar a un directorio de trabajo.  

Ejecute el comando siguiente para clonar el repositorio de ejemplo. 

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd` en el directorio clonado. Instale los paquetes requeridos.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Configuración de la conexión a MySQL

En la raíz del repositorio, cree un archivo llamado *.env*. Copie las siguientes variables en el archivo *.env*. Reemplace el marcador de posición _&lt;root_password>_ por la contraseña del usuario raíz de MySQL.

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Para obtener información acerca de la forma en que Laravel el archivo _.env_, consulte [Laravel Environment Configuration](https://laravel.com/docs/5.4/configuration#environment-configuration) (Configuración del entorno de Laravel).

### <a name="run-the-sample"></a>Ejecución del ejemplo

Ejecute las [migraciones de la base de datos Laravel](https://laravel.com/docs/5.4/migrations) para crear las tablas que necesita la aplicación. Para ver qué tablas se crean en las migraciones, mire en el directorio _database/migrations_ del repositorio de Git.

```bash
php artisan migrate
```

Genere una nueva clave de aplicación Laravel.

```bash
php artisan key:generate 
```

Ejecute la aplicación.

```bash
php artisan serve
```

Vaya a `http://localhost:8000` en un explorador. Agregue algunas tareas a la página.

![Conexión correcta de PHP a MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Para detener PHP, escriba `Ctrl + C` en el terminal. 

## <a name="create-mysql-in-azure"></a>Creación de MySQL en Azure

En este paso, creará una base de datos MySQL en [Azure Database for MySQL (versión preliminar)](/azure/mysql). Posteriormente, configurará la aplicación PHP para que se conecte a esta base de datos.

### <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla. 

```azurecli
az login 
```
### <a name="create-a-resource-group"></a>Crear un grupo de recursos

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Creación de un servidor MySQL

Cree un servidor en Azure Database for MySQL (versión preliminar) con el comando [az mysql server create](/cli/azure/mysql/server#create).

En el siguiente comando, reemplace el nombre del servidor MySQL en el lugar en el que vea el marcador de posición _&lt;mysql_server_name>_ (los caracteres válidos son `a-z`, `0-9` y `-`). Este nombre forma parte del nombre de host del servidor MySQL (`<mysql_server_name>.database.windows.net`), por lo que es preciso que sea globalmente único. 

```azurecli-interactive
az mysql server create \
    --name <mysql_server_name> \
    --resource-group myResourceGroup \
    --location "North Europe" \
    --admin-user adminuser \
    --admin-password MySQLAzure2017
```

Cuando se crea el servidor MySQL, la CLI de Azure muestra información similar a la del siguiente ejemplo:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Configuración del firewall del servidor

Cree una regla de firewall para que el servidor MySQL permita conexiones de clientes usando el comando [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create). 

```azurecli-interactive
az mysql server firewall-rule create \
    --name allIPs \
    --server <mysql_server_name> \
    --resource-group myResourceGroup \
    --start-ip-address 0.0.0.0 \
    --end-ip-address 255.255.255.255
```

> [!NOTE]
> Actualmente, Azure Database for MySQL (versión preliminar) limita las conexiones únicamente a servicios de Azure. Dado que las direcciones IP se asignan de forma dinámica en Azure, es mejor habilitarlas todas. El servicio está en versión preliminar. Se planean los mejores métodos para proteger la base de datos.
>
>

### <a name="connect-to-production-mysql-server"></a>Conexión al servidor MySQL de producción

En la ventana de terminal, conéctese al servidor MySQL de Azure. Use el valor que especificó anteriormente para _&lt;mysql_server_name>_.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

Cuando se le solicite una contraseña, utilice _tr0ngPa $$ w0rd!_, que es la que especificó al crear la base de datos.

### <a name="create-a-production-database"></a>Creación de una base de datos de producción

En el símbolo del sistema de `mysql`, cree una base de datos.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Creación de un usuario con permisos

Cree un usuario de base de datos denominado _phpappuser_ y concédale todos los privilegios de la base de datos `sampledb`.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Escriba `quit` para salir de la conexión del servidor.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Conexión de una aplicación a Azure MySQL

En este paso, conectará la aplicación PHP a la base de datos MySQL que creó en Azure Database for MySQL (versión preliminar). 

<a name="devconfig"></a>
### <a name="configure-the-connection"></a>Configuración de la conexión 

En la raíz del repositorio, cree un archivo _.env.production_ y copie en él las siguientes variables. Reemplace el marcador de posición _&lt;mysql_server_name >_.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.database.windows.net
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql_server_name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Guarde los cambios.

> [!TIP]
> Para proteger la información de conexión de MySQL, este archivo ya se ha excluido del repositorio de Git (vea _.gitignore_ en la raíz del repositorio). Más adelante, aprenderá a configurar variables de entorno en App Service para conectarse a la base de datos en Azure Database for MySQL (versión preliminar). Con las variables de entorno, no es preciso el archivo *.env* en App Service. 
>

### <a name="configure-ssl-certificate"></a>Configuración de un certificado SSL

De manera predeterminada, Azure Database for MySQL aplica las conexiones SSL de los clientes. Para conectarse a una base de datos MySQL en Azure, debe usar un certificado SSL _.pem_.

Abra _config/database.php_ y agregue los parámetros _sslmode_ y _options_ a `connections.mysql`, como se muestra en el código siguiente.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/certificate.pem', 
    ] : []
],
```

Para aprender a generar el archivo [certificate.pem](../mysql/howto-configure-ssl.md), consulte _Configuración de la conectividad SSL en la aplicación para conectarse de forma segura a Azure Database for MySQL_.

> [!TIP]
> La ruta de acceso _/ssl/certificate.pem_ apunta a un archivo _certificate.pem_ existente en el repositorio de Git. Para su comodidad, en este tutorial se proporciona este archivo. Como procedimiento recomendado, no debe confirmar los certificados _.pem_ en el control de código fuente. 
>
>

### <a name="test-the-application"></a>Prueba de la aplicación

Ejecute migraciones de base de datos Laravel con _.env.production_ como archivo de entorno para crear las tablas de la base de datos MySQL en Azure Database for MySQL (versión preliminar). Recuerde que _. env.production_ tiene la información de conexión a su base de datos MySQL de Azure.

```bash
php artisan migrate --env=production --force
```

El archivo _.env.production_ aún no cuenta con una clave de aplicación válida. Genere una nueva para él en el terminal. 

```bash
php artisan key:generate --env=production --force
```

Ejecute la aplicación de ejemplo con _.env.production_ como archivo de entorno.

```bash
php artisan serve --env=production
```

Vaya a `http://localhost:8000`. Si la página se carga sin errores, significa que la aplicación PHP se está conectado a la base de datos MySQL de Azure. 

Agregue algunas tareas a la página.

![Conexión correcta de PHP a Azure Database for MySQL (versión preliminar)](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Para detener PHP, escriba `Ctrl + C` en el terminal. 

### <a name="commit-your-changes"></a>Confirmación de los cambios

Ejecute los siguientes comandos de Git para confirmar los cambios:

```bash
git add .
git commit -m "database.php updates"
```

La aplicación está lista para implementarse.

## <a name="deploy-to-azure"></a>Implementación en Azure
En este paso se implementará la aplicación PHP conectada a MySQL en Azure App Service.

### <a name="create-an-app-service-plan"></a>Creación de un plan del Servicio de aplicaciones

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)] 

### <a name="create-a-web-app"></a>Creación de una aplicación web

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-no-h.md)] 

### <a name="set-the-php-version"></a>Establecimiento de la versión de PHP

Establezca la versión de PHP que requiere la aplicación, para lo que debe usar el comando [az appservice web config update](/cli/azure/webapp/config#set).

El siguiente comando establece la versión de PHP en _7.0_.

```azurecli-interactive
az webapp config set \
    --name <app_name> \
    --resource-group myResourceGroup \
    --php-version 7.0
```

### <a name="configure-database-settings"></a>Configuración de la base de datos

Como se indicó anteriormente, es posible conectar la base de datos MySQL de Azure mediante variables de entorno en App Service.

En App Service, las variables de entorno se establecen como _valores de aplicación_ mediante el comando [az appservice web config appsettings set](/cli/azure/webapp/config/appsettings#set). 

El siguiente comando permite configurar los valores de aplicación `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` y `DB_PASSWORD`. Reemplace los marcadores de posición _&lt;appname>_ y _&lt;mysql_server_name>_.

```azurecli-interactive
az webapp config appsettings set \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

Para acceder a la configuración puede usar el método [getenv](http://www.php.net/manual/function.getenv.php) de PHP. El código de Laravel usa un contenedor [env ](https://laravel.com/docs/5.4/helpers#method-env) sobre el elemento `getenv` de PHP. Por ejemplo, la configuración de MySQL de _config/database.php_ es como el código siguiente:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Configuración de las variables de entorno de Laravel

Laravel necesita una clave de aplicación en App Service. Puede configurarlo con valores de aplicación.

Use `php artisan` para generar una nueva clave de aplicación sin guardarla en _.env_.

```bash
php artisan key:generate --show
```

Establezca la clave de aplicación en la aplicación web App Service, para lo que hay que usar el comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#set). Reemplace los marcadores de posición _&lt;appname>_ y _&lt;outputofphpartisankey:generate>_.

```azurecli-interactive
az webapp config appsettings set \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` indica a Laravel que devuelva información de depuración si la aplicación web implementada encuentra errores. Al ejecutar una aplicación de producción, establézcala en `false`, que es más seguro.

### <a name="set-the-virtual-application-path"></a>Establecimiento de la ruta de acceso de la aplicación virtual

Establezca la ruta de acceso de la aplicación virtual para la aplicación web. Este paso se requiere porque el [ciclo de vida de la aplicación Laravel](https://laravel.com/docs/5.4/lifecycle) comienza en el directorio _public_, en lugar de en el directorio raíz de la aplicación. Otros marcos PHP cuyo ciclo de vida comienza en el directorio raíz pueden funcionar sin necesidad de configurar manualmente la ruta de acceso de la aplicación virtual.

Use el comando [az resource update](/cli/azure/resource#update) para establecer la ruta de acceso de la aplicación virtual. Reemplace el marcador de posición _&lt;appname>_.

```azurecli-interactive
az resource update \
    --name web \
    --resource-group myResourceGroup \
    --namespace Microsoft.Web \
    --resource-type config \
    --parent sites/<app_name> \
    --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" \
    --api-version 2015-06-01
```

De manera predeterminada, Azure App Service apunta la ruta de acceso de la aplicación virtual raíz (_/_) al directorio raíz de los archivos de la aplicación implementada (_sites\wwwroot_). 

### <a name="configure-a-deployment-user"></a>Configuración de un usuario de implementación

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)] 

### <a name="configure-local-git-deployment"></a>Configurar la implementación de Git local 

[!INCLUDE [Configure local git](../../includes/app-service-web-configure-local-git-no-h.md)] 

### <a name="push-to-azure-from-git"></a>Inserción en Azure desde Git

Agregue una instancia remota de Azure en el repositorio de Git local. 

```bash
git remote add azure <paste_copied_url_here> 
```

Insértela en la instancia remota de Azure para implementar la aplicación PHP. Se le pedirá la contraseña que especificó anteriormente como parte de la creación del usuario de implementación. 

```bash
git push azure master
```

Durante la implementación, Azure App Service comunicará su progreso con Git.

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
``` 

> [!NOTE]
> Puede observar que, al final del proceso de implementación, se instalan paquetes de [Composer](https://getcomposer.org/). App Service no ejecuta estas automatizaciones durante la implementación predeterminada, por lo que este repositorio de ejemplo tiene tres archivos adicionales en su directorio raíz para permitirlo: 
>
> - `.deployment`: este archivo indica a App Service que ejecute `bash deploy.sh` como script de implementación personalizado.
> - `deploy.sh`: el script de implementación personalizado. Si revisa el archivo, verá que se ejecuta `php composer.phar install` después de `npm install`. 
> - `composer.phar`: el administrador de paquetes de Composer.
>
> Puede aplicar este enfoque para agregar cualquier paso a la implementación basada en Git en App Service. Para obtener más información, consulte [Custom Deployment Script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) (Script de implementación personalizado).
>
>


### <a name="browse-to-the-azure-web-app"></a>Navegación hasta la aplicación web de Azure

Vaya a `http://<app_name>.azurewebsites.net` y agregue algunas tareas a la lista. 

![Aplicación PHP que se ejecuta en Azure App Service](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

Ya está ejecutando una aplicación PHP orientada a datos en Azure App Service.

## <a name="update-model-and-redeploy"></a>Actualización del modelo y nueva implementación

En este paso, se realiza un cambio sencillo en el modelo de datos `task` y en la aplicación web y, después, se publica la actualización en Azure.

Para el escenario de las tareas, modifique la aplicación de forma que pueda marcar una tarea como completada. 

### <a name="add-a-column"></a>Adición de una columna

En el terminal, navegue a la raíz del repositorio de Git.

Generar una migración de base de datos nueva para la tabla `tasks`:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Este comando muestra el nombre del archivo de migración generado. Busque este archivo en _database/migrations_ y ábralo.

Reemplace el método `up` por el código siguiente:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

El código anterior agrega una columna booleana a la tabla `tasks` denominada `complete`.

Reemplace el método `down` por el siguiente código para la acción de reversión:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

En el terminal, ejecute migraciones de base de datos Laravel para realizar el cambio en la base de datos local.

```bash
php artisan migrate
```

En función de la [convención de nomenclatura de Laravel](https://laravel.com/docs/5.4/eloquent#defining-models), el modelo `Task` (consulte _app/Task.php_) se asigna a la tabla `tasks` de manera predeterminada.

### <a name="update-application-logic"></a>Actualización de la lógica de aplicación

Abra el archivo *routes/web.php*. La aplicación define aquí tanto sus rutas como su lógica de negocios.

Al final del archivo, agregue una ruta con el siguiente código:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

El código anterior lleva a cabo una sencilla actualización en el modelo de datos, para lo que alterna el valor de `complete`.

### <a name="update-the-view"></a>Actualización de la vista

Abra el archivo *resources/views/tasks.blade.php*. Busque la etiqueta de apertura `<tr>` y reemplácela por:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

El código anterior cambia el color de la fila en función de si la tarea se ha completado.

En la siguiente línea, tiene el siguiente código:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Reemplace esta línea al completo por el siguiente código:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

El código anterior agrega el botón de envío que hace referencia a la ruta que definió previamente.

### <a name="test-the-changes-locally"></a>Prueba local de los cambios

Desde el directorio raíz del repositorio de Git, ejecute el servidor de desarrollo.

```bash
php artisan serve
```

Para ver cómo cambia el estado de la tarea, navegue hasta `http://localhost:8000` y active la casilla.

![Casilla agregada a tarea](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

Para detener PHP, escriba `Ctrl + C` en el terminal. 

### <a name="publish-changes-to-azure"></a>Publicación de los cambios en Azure

En el terminal, ejecute migraciones de bases de datos Laravel con la cadena de conexión de producción para realizar el cambio en la base de datos de Azure.

```bash
php artisan migrate --env=production --force
```

Confirme todos los cambios en Git y, después, inserte los cambios en el código en Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Una vez que `git push` esté completo, navegue hasta la aplicación web de Azure y pruebe la nueva funcionalidad.

![Modelo y cambios en la base de datos publicados en Azure](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Si ha agregado tareas, estas se conservarán en la base de datos. Las actualizaciones que efectúe en el esquema de datos dejan los datos existentes intactos.

## <a name="stream-diagnostic-logs"></a>Transmisión de registros de diagnóstico 

Mientras se ejecuta la aplicación PHP en Azure App Service, los registros de la consola se canalizan a su terminal. De este modo, puede obtener los mismos mensajes de diagnóstico para ayudarle a depurar errores de la aplicación.

Para iniciar la transmisión del registro, use el comando [az webapp log tail](/cli/azure/webapp/log#tail).

```azurecli-interactive 
az webapp log tail \
    --name <app_name> \
    --resource-group myResourceGroup
``` 

Cuando las secuencias de registro se inicien, actualice la aplicación web de Azure en el explorador para obtener tráfico web. Ahora puede ver los registros de la consola canalizados al terminal. Si no ve los registros de la consola de inmediato, vuelve a comprobarlo en 30 segundos.

Para detener la secuencia de registro en cualquier momento, escriba `Ctrl`+`C`. 

> [!TIP]
> Las aplicaciones PHP pueden usar el elemento estándar [error_log()](http://php.net/manual/function.error-log.php) para enviar información a la consola. La aplicación de ejemplo usa este enfoque en _app/Http/routes.php_.
>
> Como marco web, [Laravel usa Monolog](https://laravel.com/docs/5.4/errors) como proveedor de registros. Para conocer cómo hace Monolog para enviar mensajes a la consola, consulte [PHP: How to use monolog to log to console (php://out)](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out) (PHP: Uso de Monolog para iniciar sesión en la consola [php://out]).
>
>

## <a name="manage-the-azure-web-app"></a>Administración de la aplicación web de Azure

Vaya a [Azure Portal](https://portal.azure.com) para administrar la aplicación web que ha creado.

En el menú izquierdo, haga clic en **App Services** y, a continuación, haga clic en el nombre de la aplicación web de Azure.

![Navegación desde el portal a la aplicación web de Azure](./media/app-service-web-tutorial-php-mysql/access-portal.png)

Podrá ver la página de información general de la aplicación web. Aquí se pueden realizar tareas de administración básicas como detener, iniciar, reiniciar, examinar y eliminar. 

El menú izquierdo proporciona varias páginas para configurar la aplicación. 

![Página de App Service en Azure Portal](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a:

> [!div class="checklist"]
> * Crear una base de datos MySQL en Azure
> * Conectar una aplicación PHP a MySQL
> * Implementación de la aplicación en Azure
> * Actualizar el modelo de datos y volver a implementar la aplicación
> * Transmitir registros de diagnóstico desde Azure
> * Administrar la aplicación en Azure Portal

Pase al siguiente tutorial para aprender a asignar un nombre DNS personalizado a una aplicación web.

> [!div class="nextstepaction"] 
> [Asignar un nombre DNS personalizado a Azure Web Apps](app-service-web-tutorial-custom-domain.md)

