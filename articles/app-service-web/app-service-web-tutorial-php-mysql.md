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
ms.date: 05/05/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3ad716fab4f5084c38c83f4bc90a616856666b38
ms.contentlocale: es-es
ms.lasthandoff: 05/31/2017

---
# <a name="build-a-php-and-mysql-web-app-in-azure"></a>Compilación de una aplicación web PHP y MySQL en Azure
En este tutorial, se muestra cómo crear una aplicación web PHP en Azure y conectarla a una base de datos MySQL. Cuando haya terminado, tendrá una aplicación [Laravel](https://laravel.com/) que se ejecuta en [Azure App Service Web Apps](app-service-web-overview.md).

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

Antes de ejecutar este ejemplo, instale los siguientes requisitos previos localmente:

1. [Descarga e instalación de Git](https://git-scm.com/)
1. [Descarga e instalación de PHP 5.6.4 o posterior](http://php.net/downloads.php)
1. [Descarga e instalación de Composer](https://getcomposer.org/doc/00-intro.md)
1. Habilitación de las siguientes extensiones PHP necesarias para Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer y XML
1. [Descarga, instalación e inicio de MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [Descarga e instalación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-local-mysql"></a>Preparación de MySQL local

En este paso, creará una base de datos en el servidor MySQL local para usarlo en este tutorial.

### <a name="connect-to-mysql-server"></a>Conexión a un servidor MySQL
En una ventana de terminal, conéctese al servidor MySQL local.

```bash
mysql -u root -p
```

Si se le pide una contraseña, escriba la de la cuenta `root`. Si no recuerda la contraseña de la cuenta raíz, consulte [MySQL: How to Reset the Root Password](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) (MySQL: Instrucciones de restablecimiento de la contraseña de la cuenta raíz).

Si el comando se ejecuta correctamente, significa que el servidor MySQL ya se está ejecutando. De lo contrario, siga los [pasos posteriores a la instalación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html) para asegurarse de iniciar el servidor local de MySQL.

### <a name="create-a-database"></a>Creación de una base de datos

En el símbolo del sistema `mysql`, cree una base de datos.

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

Abra la ventana de terminal y use `cd` para cambiar a un directorio de trabajo.  

Ejecute los comandos siguientes para clonar el repositorio de ejemplo. 

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

Use `cd` en el directorio clonado e instale los paquetes necesarios.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Configuración de la conexión a MySQL

En la raíz del repositorio, cree un archivo _.env_ y copie en él las siguientes variables. Reemplace el marcador de posición _&lt;root_password>_ por la contraseña del usuario raíz.

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

> [!NOTE]
> Para obtener información sobre cómo usa Laravel este archivo _.env_, consulte [Configuración del entorno de Laravel](https://laravel.com/docs/5.4/configuration#environment-configuration).
>
>

### <a name="run-the-sample"></a>Ejecución del ejemplo

Ejecute [migraciones de base de datos Laravel](https://laravel.com/docs/5.4/migrations) para crear las tablas necesarias para la aplicación. Para conocer qué tablas se crean en las migraciones, mire en el directorio _database/migrations_ del repositorio de Git.

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

Para detener PHP en cualquier momento, escriba `Ctrl`+`C` en el terminal. 

## <a name="create-production-mysql-in-azure"></a>Creación de MySQL de producción en Azure

En este paso, creará una base de datos MySQL en [Azure Database for MySQL (versión preliminar)](/azure/mysql). Más adelante, configurará la aplicación PHP para que se conecte a esta base de datos.

### <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Ahora, va a usar la CLI de Azure 2.0 en una ventana de terminal para crear los recursos necesarios para hospedar la aplicación PHP en Azure App Service. Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla. 

```azurecli-interactive 
az login 
``` 

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un [grupo de recursos](../azure-resource-manager/resource-group-overview.md) con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y administran recursos de Azure como aplicaciones web, bases de datos y cuentas de almacenamiento. 

En el siguiente ejemplo, se crea un grupo de recursos en la región de Europa del Norte:

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```

Para ver qué valores posibles puede usar para `--location`, use el comando [az appservice list-locations](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Creación de un servidor MySQL

Cree un servidor en Azure Database for MySQL (versión preliminar) con el comando [az mysql server create](/cli/azure/mysql/server#create).

En el siguiente comando, reemplace el nombre único del servidor MySQL en el lugar en el que observe el marcador de posición _&lt;mysql_server_name>_. Este nombre forma parte del nombre de host del servidor MySQL, `<mysql_server_name>.database.windows.net`, por lo que debe ser único. De igual modo, reemplace _&lt;admin_user>_ y _&lt;admin_password>_ por sus propios valores.

```azurecli-interactive
az mysql server create \
    --name <mysql_server_name> \
    --resource-group myResourceGroup \
    --location "North Europe" \
    --user <admin_user> \
    --password <admin_password>
```

Cuando se crea el servidor MySQL, la CLI de Azure muestra información similar a la del siguiente ejemplo:

```json
{
  "administratorLogin": "<admin_user>",
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
> Azure Database for MySQL (versión preliminar) todavía no permite realizar conexiones únicamente desde servicios de Azure. Dado que las direcciones IP se asignan de forma dinámica en Azure, es mejor habilitarlas todas por el momento. El servicio se encuentra en versión preliminar, por lo que pronto habrá disponibles métodos mejores para proteger las bases de datos.
>
>

### <a name="connect-to-production-mysql-server"></a>Conexión al servidor MySQL de producción

En la ventana de terminal, conéctese al servidor MySQL de Azure. Use el valor que especificó anteriormente para _&lt;admin_user>_ y _&lt;mysql_server_name>_.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

### <a name="create-a-production-database"></a>Creación de una base de datos de producción

En el símbolo del sistema `mysql`, cree una base de datos.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Creación de un usuario con permisos

Cree un usuario de base de datos y concédale todos los privilegios de la base de datos `sampledb`. Reemplace los marcadores de posición _&lt;phpapp_user>_ y _&lt;phpapp_password>_ por su propio nombre de aplicación único.

```sql
CREATE USER '<phpapp_user>' IDENTIFIED BY '<phpapp_password>'; 
GRANT ALL PRIVILEGES ON sampledb.* TO '<phpapp_user>';
```

Escriba `quit` para salir de la conexión del servidor.

```sql
quit
```

## <a name="connect-app-to-production-mysql"></a>Conexión de la aplicación al servidor MySQL de producción

En este paso, conectará la aplicación PHP a la base de datos MySQL que acaba de crear en Azure Database for MySQL (versión preliminar). 

<a name="devconfig"></a>
### <a name="configure-the-connection"></a>Configuración de la conexión 

En la raíz del repositorio, cree un archivo _.env.production_ y copie en él las siguientes variables. Reemplace los marcadores de posición _&lt;mysql_server_name>_, _&lt;phpapp_user>_ y _&lt;phpapp_password>_.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.database.windows.net
DB_DATABASE=sampledb
DB_USERNAME=<phpapp_user>@<mysql_server_name>
DB_PASSWORD=<phpapp_password>
```

Guarde los cambios.

### <a name="test-the-application"></a>Prueba de la aplicación

Ejecute migraciones de base de datos Laravel con _.env.production_ como archivo de entorno para crear las tablas de la base de datos MySQL en Azure Database for MySQL (versión preliminar).

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

Vaya a `http://localhost:8000` en un explorador. Si la página se carga sin errores, significa que la aplicación PHP está conectándose a la base de datos MySQL de Azure. 

Agregue algunas tareas a la página.

![Conexión correcta de PHP a Azure Database for MySQL (versión preliminar)](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Para detener PHP en cualquier momento, escriba `Ctrl`+`C` en el terminal. 

### <a name="secure-sensitive-data"></a>Protección de información confidencial

Debe asegurarse de que la información confidencial de _.env.production_ no se confirme en Git.

Para ello, abra _.gitignore_ en la raíz del repositorio y agregue el nombre de archivo en una nueva línea:

```
.env.production
```

Guarde los cambios y, seguidamente, confírmelos en Git.

```bash
git add .gitignore
git commit -m "keep sensitive data out of git"
```

Más adelante, aprenderá a configurar variables de entorno en App Service para conectarse a la base de datos Azure Database for MySQL (versión preliminar) de forma que no sea necesario ningún archivo `.env` en App Service. 

## <a name="deploy-php-app-to-azure"></a>Implementación de una aplicación PHP en Azure
En este paso, implementará la aplicación PHP conectada a MySQL en Azure App Service.

### <a name="create-an-app-service-plan"></a>Creación de un plan del Servicio de aplicaciones

Cree un plan de App Service con el comando [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

En el siguiente ejemplo, se crea un plan de App Service denominado _miPlanDeAppService_ con el plan de tarifa **Gratis**:

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \
    --resource-group myResourceGroup \
    --sku FREE
```

Cuando se ha creado el plan de App Service, la CLI de Azure muestra información similar al ejemplo siguiente.

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-a-web-app"></a>Creación de una aplicación web

Ahora que ha creado un plan de App Service, cree una aplicación web dentro del plan de App Service _miPlanDeAppService_. La aplicación web ofrece un espacio de hospedaje para implementar el código y proporciona una dirección URL para que pueda ver la aplicación implementada. Use el comando [az appservice web create](/cli/azure/appservice/web#create) para crear la aplicación web. 

En el siguiente comando, reemplace el marcador de posición _&lt;appname>_ por su propio nombre de aplicación único. Este nombre único se usa como parte del nombre de dominio predeterminado para la aplicación web, por lo que debe ser único en todas las aplicaciones de Azure. Más adelante puede asignar cualquier entrada de DNS personalizada a la aplicación web antes de exponerla a los usuarios. 

```azurecli-interactive
az appservice web create \
    --name <app_name> \
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Cuando se ha creado la aplicación web, la CLI de Azure muestra información similar a la del ejemplo siguiente: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="set-the-php-version"></a>Establecimiento de la versión de PHP

Establezca la versión de PHP necesaria para la aplicación con el comando [az appservice web config update](/cli/azure/appservice/web/config#update).

El siguiente comando establece la versión de PHP en _7.0_.

```azurecli-interactive
az appservice web config update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --php-version 7.0
```

### <a name="configure-database-settings"></a>Configuración de la base de datos

Como se indicó anteriormente, es posible conectar la base de datos MySQL de Azure mediante variables de entorno en App Service.

En App Service, establecerá variables de entorno como _valores de aplicación_ mediante el comando [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). 

El siguiente comando permite configurar las opciones `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` y `DB_PASSWORD`. Reemplace los marcadores de posición _&lt;appname>_, _&lt;mysql_server_name>_, _&lt;phpapp_user>_ y _&lt;phpapp_password>_.

```azurecli-interactive
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="<phpapp_user>@<mysql_server_name>" DB_PASSWORD="<phpapp_password>"
```

Puede usar el método de PHP [getenv()](http://www.php.net/manual/function.getenv.php) para acceder a la configuración. El código de Laravel usa un contenedor [env()](https://laravel.com/docs/5.4/helpers#method-env) con el elemento `getenv()` de PHP. Por ejemplo, la configuración de MySQL de _config/database.php_ tiene el siguiente aspecto:

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

Al igual que en el equipo local, Laravel necesita una clave de aplicación en App Service. También puede configurarla con opciones de aplicación.

Use `php artisan` para generar una nueva clave de aplicación sin guardarla en _.env_.

```bash
php artisan key:generate --show
```

Establezca la clave de aplicación en la aplicación web de App Service usando el comando [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). Reemplace los marcadores de posición _&lt;appname>_ y _&lt;outputofphpartisankey:generate>_.

```azurecli-interactive
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

> [!NOTE]
> `APP_DEBUG="true"` indica a Laravel que debe devolver información de depuración si la aplicación web implementada encuentra errores. Al ejecutar una aplicación de producción, debería establecerla en `false` en su lugar para mayor seguridad.
>
>

### <a name="set-the-virtual-application-path"></a>Establecimiento de la ruta de acceso de la aplicación virtual

Establezca la ruta de acceso a aplicación virtual para la aplicación web. Solo necesita este paso porque [el ciclo de vida de la aplicación Laravel](https://laravel.com/docs/5.4/lifecycle) comienza en el directorio _public_ en lugar de en el directorio raíz de la aplicación. Otros marcos PHP cuyo ciclo de vida comienza en el directorio raíz pueden funcionar sin necesidad de configurar manualmente la ruta de acceso de la aplicación virtual.

Use el comando [az resource update](/cli/azure/resource#update) para establecer la ruta de acceso de la aplicación virtual. Reemplace el marcador de posición _&lt;appname>_.

```bash
az resource update \
    --name web \
    --resource-group myResourceGroup \
    --namespace Microsoft.Web \
    --resource-type config \
    --parent sites/<app_name> \
    --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" \
    --api-version 2015-06-01
```

> [!NOTE]
> De manera predeterminada, Azure App Service apunta la ruta de acceso de la aplicación virtual (_/_) al directorio raíz de los archivos de la aplicación implementada (_sites\wwwroot_). 
>
>

### <a name="configure-a-deployment-user"></a>Configuración de un usuario de implementación

En el caso de FTP y repositorio de Git local, es necesario que haya un usuario de implementación configurado en el servidor para autenticar la implementación. 

> [!NOTE] 
> Se requiere un usuario de implementación para la implementación de FTP y Git Local en una aplicación web. El nombre de usuario y la contraseña funcionan en el nivel de cuenta y, por tanto, son diferentes de las credenciales de suscripción de Azure.

Si ya creó un nombre de usuario y una contraseña de implementación, puede usar el siguiente comando para mostrar el nombre de usuario:

```azurecli-interactive
az appservice web deployment user show
```

Si aún no tiene un usuario de implementación, ejecute el comando [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) para crear sus credenciales de implementación. 

```azurecli-interactive
az appservice web deployment user set \
    --user-name <username> \
    --password <minimum-8-char-capital-lowercase-number>
```

El nombre de usuario debe ser único y la contraseña debe ser segura. Si se produce un error `'Conflict'. Details: 409`, cambie el nombre de usuario. Si se produce un error `'Bad Request'. Details: 400`, use una contraseña más segura.

Solo es necesario crear este usuario de implementación una vez. Puede usarlo para todas las implementaciones de Azure.

Anote el nombre de usuario y la contraseña, ya que tendrá que usarlos más adelante al implementar la aplicación.

### <a name="configure-local-git-deployment"></a>Configuración de la implementación de Git local 

Puede implementar su aplicación en Azure App Service de diversas formas, como FTP, repositorio de Git local, GitHub, Visual Studio Team Services y BitBucket. 

Use el comando [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) para configurar el acceso de Git local a la aplicación web de Azure. 

```azurecli-interactive
az appservice web source-control config-local-git \
    --name <app_name> \
    --resource-group myResourceGroup
```

Cuando el usuario de implementación está configurado, la CLI de Azure muestra la dirección URL de implementación de su aplicación web de Azure en el siguiente formato:

```bash 
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git 
``` 

Copie la salida del terminal ya que se usará en el paso siguiente. 

### <a name="push-to-azure-from-git"></a>Inserción en Azure desde Git

Agregue una instancia remota de Azure en el repositorio de Git local. 

```bash
git remote add azure <paste_copied_url_here> 
```

Realice una inserción en la instancia remota de Azure para implementar la aplicación PHP. Le pedirá la contraseña que proporcionó anteriormente como parte de la creación del usuario de implementación. 

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

**¡Enhorabuena!** Ya está ejecutando una aplicación PHP orientada a datos en Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Actualización del modelo de datos y nueva implementación

En este paso, realizará algunos cambios en los datos de `task` y publicará los cambios en Azure.

Para el escenario de las tareas, se recomienda modificar la aplicación para poder marcar una tarea como completada. 

### <a name="add-a-column"></a>Adición de una columna

En el terminal, asegúrese de encontrarse en la raíz del repositorio de Git y, a continuación, genere una nueva migración de base de datos para la tabla `tasks`.

```bash
php artisan make:migration add_complete_column --table=tasks
```

Este comando muestra el nombre del archivo de migración generado. Busque este archivo en _database/migrations_ y ábralo en un editor de texto.

Reemplace el método up() por el siguiente código:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Este código agrega una columna booleana a la tabla `tasks` denominada `complete`.

Reemplace el método down() por el siguiente código para la acción de reversión:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

En el terminal, ejecute migraciones de base de datos Laravel de forma local para realizar el cambio en la base de datos local.

```bash
php artisan migrate
```

En función de la [convención de nomenclatura de Laravel](https://laravel.com/docs/5.4/eloquent#defining-models), el modelo `Task` (consulte _app/Task.php_) se asigna a la tabla `tasks` de manera predeterminada, por lo que ya ha finalizado la actualización del modelo de datos.

### <a name="update-application-logic"></a>Actualización de la lógica de aplicación

Abra _routes/web.php_. La aplicación de ejemplo define sus rutas y su lógica de negocios aquí.

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

Este código lleva a cabo una sencilla actualización en el modelo de datos alternando el valor de `complete`.

### <a name="update-the-view"></a>Actualización de la vista

Abra _resources/views/tasks.blade.php_. Busque la etiqueta de apertura `<tr>` y reemplácela por:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

De este modo, se cambia el color de la fila en función de si la tarea se ha completado.

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

Este código agrega el botón de envío que hace referencia a la ruta que definió previamente.

### <a name="test-your-changes-locally"></a>Prueba de los cambios localmente

Desde el directorio raíz del repositorio de Git, vuelva a ejecutar el servidor de desarrollo.

```bash
php artisan serve
```

Vaya a la dirección `http://localhost:8000` en un explorador y haga clic en la casilla para comprobar que el estado de la tarea ha cambiado como corresponda.

![Casilla agregada a tarea](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

### <a name="publish-changes-to-azure"></a>Publicación de los cambios en Azure

En el terminal, ejecute migraciones de bases de datos Laravel con la cadena de conexión de producción para realizar el cambio en la base de datos de producción de Azure.

```bash
php artisan migrate --env=production --force
```

Confirme todos los cambios en Git y, a continuación, inserte los cambios de código en Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Una vez que se haya completado `git push`, vaya a la aplicación web de Azure y vuelva a probar la nueva funcionalidad.

![Modelo y cambios en la base de datos publicados en Azure](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

> [!NOTE]
> Si agregó cualquier tarea previamente, podrá seguir viéndola. Las actualizaciones que efectúe en el esquema de datos dejan los datos existentes intactos.
>
>

## <a name="stream-diagnostic-logs"></a>Transmisión de registros de diagnóstico 

Mientras se ejecuta la aplicación de PHP en Azure App Service, puede tener los registros de consola insertados directamente en el terminal. De este modo, puede obtener los mismos mensajes de diagnóstico para ayudarle a depurar errores de la aplicación.

Para iniciar la secuencia de registro, use el comando [az appservice web log tail](/cli/azure/appservice/web/log#tail).

```azurecli-interactive 
az appservice web log tail \
    --name <app_name> \
    --resource-group myResourceGroup 
``` 

Cuando se inicie la secuencia de registro, actualice la aplicación web de Azure en el explorador para obtener algún tráfico web. Ahora debería ver los registros de la consola insertados en su terminal.

Para detener la secuencia de registro en cualquier momento, escriba `Ctrl`+`C`. 

> [!TIP]
> Las aplicaciones PHP pueden usar el elemento estándar [error_log()](http://php.net/manual/function.error-log.php) para enviar información a la consola. La aplicación de ejemplo usa este enfoque en _app/Http/routes.php_.
>
> Como marco web, [Laravel usa Monolog](https://laravel.com/docs/5.4/errors) como proveedor de registros. Para conocer cómo hace Monolog para enviar mensajes a la consola, consulte [PHP: How to use monolog to log to console (php://out)](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out) (PHP: Uso de Monolog para iniciar sesión en la consola [php://out]).
>
>

## <a name="manage-your-azure-web-app"></a>Administración de la aplicación web de Azure

Vaya al portal de Azure para ver la aplicación web que ha creado.

Para ello, inicie sesión en [https://portal.azure.com/](https://portal.azure.com).

En el menú izquierdo, haga clic en **App Service**, a continuación, haga clic en el nombre de la aplicación web de Azure.

![Navegación desde el portal a la aplicación web de Azure](./media/app-service-web-tutorial-php-mysql/access-portal.png)

Ha llegado a la _hoja_ de su aplicación web (una página del portal que se abre horizontalmente).

De forma predeterminada, la hoja de la aplicación web muestra la página de **introducción**. Esta página proporciona una visión del funcionamiento de la aplicación. En este caso, también puede realizar tareas de administración básicas como examinar, detener, iniciar, reiniciar y eliminar. Las pestañas del lado izquierdo de la hoja muestran las diferentes páginas de configuración que puede abrir.

![Hoja de App Service en Azure Portal](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

Estas pestañas de la hoja muestran las muchas y excepcionales características que puede agregar a la aplicación web. La lista siguiente proporciona solo algunas de las posibilidades:

* Asignación de un nombre DNS personalizado
* Enlace de un certificado SSL personalizado
* Configuración de la implementación continua
* Escalado vertical y horizontal
* Adición de la autenticación de usuarios

## <a name="clean-up-resources"></a>Limpieza de recursos
 
Si no necesita estos recursos para otro tutorial (consulte [Pasos siguientes](#next)), puede ejecutar el siguiente comando para eliminarlos: 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

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

Pase al siguiente tutorial para aprender a asignarle un nombre DNS personalizado.

> [!div class="nextstepaction"] 
> [Asignar un nombre DNS personalizado a Azure Web Apps](app-service-web-tutorial-custom-domain.md)

