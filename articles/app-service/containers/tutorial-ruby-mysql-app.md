---
title: "Compilación de una aplicación web Ruby y MySQL en Azure App Service en Linux | Microsoft Docs"
description: "Obtenga información sobre cómo empezar a trabajar con una aplicación Ruby en Azure, con conexión a una base de datos MySQL en Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/21/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 03b2b4e8f8827a08e1414512d848bd5bed48d674
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="build-a-ruby-and-mysql-web-app-in-azure-app-service-on-linux"></a>Compilación de una aplicación web Ruby y MySQL en Azure App Service en Linux

[App Service en Linux](app-service-linux-intro.md) proporciona un servicio de hospedaje web muy escalable y con aplicación automática de revisiones utilizando el sistema operativo Linux. En este tutorial se muestra cómo crear una aplicación web Ruby y conectarla a una base de datos MySQL. Cuando haya terminado, tendrá una aplicación [Ruby on Rails](http://rubyonrails.org/) que se ejecuta en App Service en Linux.

![Aplicación Ruby on Rails que se ejecuta en Azure App Service](./media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una base de datos MySQL en Azure
> * Conectar una aplicación Ruby on Rails a MySQL
> * Implementación de la aplicación en Azure
> * Actualizar el modelo de datos y volver a implementar la aplicación
> * Transmitir registros de diagnóstico desde Azure
> * Administrar la aplicación en Azure Portal

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

* [Instalación de Git](https://git-scm.com/)
* [Instalación de Ruby 2.3](https://www.ruby-lang.org/documentation/installation/)
* [Instalación de Ruby on Rails 5.1](http://guides.rubyonrails.org/v5.1/getting_started.html)
* [Instalación e inicio de MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>Preparación de MySQL local

En este paso, creará una base de datos en el servidor MySQL local para usarlo en este tutorial.

### <a name="connect-to-local-mysql-server"></a>Conexión a un servidor MySQL local

En una ventana de terminal, conéctese al servidor MySQL local. Esta ventana de terminal se puede usar para ejecutar todos los comandos de este tutorial.

```bash
mysql -u root -p
```

Si se le pide una contraseña, escriba la de la cuenta `root`. Si no recuerda la contraseña de la cuenta raíz, consulte [MySQL: How to Reset the Root Password](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) (MySQL: Instrucciones de restablecimiento de la contraseña de la cuenta raíz).

Si el comando se ejecuta correctamente, significa que el servidor MySQL está en ejecución. De lo contrario, siga los [pasos posteriores a la instalación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html) para asegurarse de iniciar el servidor local de MySQL.

Escriba `quit` para salir de la conexión del servidor.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Creación de una aplicación Ruby on Rails local
En este paso, obtendrá una aplicación Ruby on Rails de ejemplo, configurará la conexión a base de datos correspondiente y la ejecutará de forma local. 

### <a name="clone-the-sample"></a>Clonación del ejemplo

En la ventana del terminal, use `cd` para cambiar a un directorio de trabajo.

Ejecute el comando siguiente para clonar el repositorio de ejemplo.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd` en el directorio clonado. Instale los paquetes requeridos.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="configure-mysql-connection"></a>Configuración de la conexión a MySQL

En el repositorio, abra _config/database.yml_ y proporcione el nombre de usuario raíz y la contraseña local de MySQL (línea 13). Si instaló MySQL mediante una herramienta como [Homebrew](https://brew.sh/), las credenciales del archivo ya están establecidas en los valores predeterminados (que son `root` y una contraseña vacía).

```txt
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password:
  socket: /tmp/mysql.sock
```

### <a name="run-the-sample-locally"></a>Ejecución local del código

Ejecute las [migraciones de Rails](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations) para crear las tablas que necesita la aplicación. Para ver qué tablas se crean en las migraciones, mire en el directorio _db/migrate_ del repositorio de Git.

```bash
rake db:create
rake db:migrate
```

Ejecute la aplicación.

```bash
rails server
```

Vaya a `http://localhost:3000` en un explorador. Agregue algunas tareas a la página.

![Conexión correcta de Ruby on Rails a MySQL](./media/tutorial-ruby-mysql-app/mysql-connect-success.png)

Para detener el servidor Rails, escriba `Ctrl + C` en el terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Creación de MySQL en Azure

En este paso, creará una base de datos MySQL en [Azure Database for MySQL (versión preliminar)](/azure/mysql). Posteriormente, configurará la aplicación Ruby on Rails para que se conecte a esta base de datos.

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Creación de un servidor MySQL

Cree un servidor en Azure Database for MySQL (versión preliminar) con el comando [az mysql server create](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

En el siguiente comando, reemplace el nombre del servidor MySQL en el lugar en el que vea el marcador de posición _&lt;mysql_server_name>_ (los caracteres válidos son `a-z`, `0-9` y `-`). Este nombre forma parte del nombre de host del servidor MySQL (`<mysql_server_name>.mysql.database.azure.com`), por lo que es preciso que sea globalmente único.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

Cuando se crea el servidor MySQL, la CLI de Azure muestra información similar a la del siguiente ejemplo:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Configuración del firewall del servidor

Cree una regla de firewall para que el servidor MySQL permita conexiones de clientes usando el comando [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create).

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Actualmente, Azure Database for MySQL (versión preliminar) limita las conexiones únicamente a servicios de Azure. Dado que las direcciones IP se asignan de forma dinámica en Azure, es mejor habilitarlas todas. El servicio está en versión preliminar. Se planean los mejores métodos para proteger la base de datos.
>

### <a name="connect-to-production-mysql-server-locally"></a>Conexión al servidor MySQL de producción local

En la ventana de terminal, conéctese al servidor MySQL de Azure. Use el valor que especificó anteriormente para _&lt;mysql_server_name>_.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

Cuando se le solicite una contraseña, utilice _My5up3r$tr0ngPa$w0rd!_, que es la que especificó al crear el servidor de bases de datos.

### <a name="create-a-production-database"></a>Creación de una base de datos de producción

En el símbolo del sistema de `mysql`, cree una base de datos.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Creación de un usuario con permisos

Cree un usuario de base de datos denominado _railsappuser_ y concédale todos los privilegios de la base de datos `sampledb`.

```sql
CREATE USER 'railsappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'railsappuser';
```

Escriba `quit` para salir de la conexión del servidor.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Conexión de una aplicación a Azure MySQL

En este paso, conectará la aplicación Ruby on Rails a la base de datos MySQL que creó en Azure Database for MySQL (versión preliminar).

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configuración de la conexión de base de datos

En el repositorio, abra _config/database.yml_. En la parte inferior del archivo, reemplace las variables de producción por el código siguiente. En el marcador de posición _&lt;mysql_server_name>_, use el nombre del servidor MySQL que creó.

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  port: 3306
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
  sslca: ssl/BaltimoreCyberTrustRoot.crt.pem
```

Guarde los cambios.

> [!NOTE]
> Se agrega el elemento `sslca` y apunta a un archivo _.pem_ existente en el mismo repositorio. De manera predeterminada, Azure Database for MySQL aplica las conexiones SSL de los clientes. Este certificado `.pem` es cómo establece conexiones SSL a Azure Database for MySQL. Para más información, consulte [Configuración de la conectividad SSL en la aplicación para conectarse de forma segura a Azure Database for MySQL](../../mysql/howto-configure-ssl.md).
>

### <a name="test-the-application-locally"></a>Prueba de la aplicación de forma local

En el terminal local, establezca las variables de entorno siguientes:

```bash
export DB_HOST=<mysql_server_name>.mysql.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<mysql_server_name>
export DB_PASSWORD=MySQLAzure2017
```

Ejecute migraciones de bases de datos Rails con los valores de producción que acaba de configurar para crear las tablas de la base de datos MySQL en Azure Database for MySQL (versión preliminar). 

```bash
rake db:migrate RAILS_ENV=production
```

Cuando se ejecuta en el entorno de producción, la aplicación Rails necesita recursos precompilados. Genere los recursos necesarios con el comando siguiente:

```bash
rake assets:precompile
```

El entorno de producción Rails también usa secretos para administrar la seguridad. Genere una clave secreta.

```bash
rails secret
```

Guarde la clave secreta en las variables respectivas que usa el entorno de producción Rails. Para mayor comodidad, se usa la misma clave para ambas variables.

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

Habilite el entorno de producción Rails para atender archivos JavaScript y CSS.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Ejecute la aplicación de ejemplo en el entorno de producción.

```bash
rails server -e production
```

Vaya a `http://localhost:3000`. Si la página se carga sin errores, significa que la aplicación Ruby on Rails se está conectado a la base de datos MySQL de Azure.

Agregue algunas tareas a la página.

![Conexión correcta de Ruby on Rails a Azure Database for MySQL (versión preliminar)](./media/tutorial-ruby-mysql-app/azure-mysql-connect-success.png)

Para detener el servidor Rails, escriba `Ctrl + C` en el terminal.

### <a name="commit-your-changes"></a>Confirmación de los cambios

Ejecute los siguientes comandos de Git para confirmar los cambios:

```bash
git add .
git commit -m "database.yml updates"
```

La aplicación está lista para implementarse.

## <a name="deploy-to-azure"></a>Implementar en Azure

En este paso se implementará la aplicación Rails conectada a MySQL en Azure App Service.

### <a name="configure-a-deployment-user"></a>Configuración de un usuario de implementación

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creación de un plan de App Service

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Creación de una aplicación web

En Cloud Shell, cree una aplicación web en el plan de App Service `myAppServicePlan` con el comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). 

En el siguiente ejemplo, reemplace `<app_name>` por un nombre único global de aplicación (los caracteres válidos son `a-z`, `0-9` y `-`). El entorno de tiempo de ejecución está establecido en `RUBY|2.3`, lo que implementa la [imagen predeterminada de Ruby](https://hub.docker.com/r/appsvc/ruby/). Para ver todos los entornos de tiempo de ejecución admitidos, ejecute [az webapp list-runtimes](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "RUBY|2.3" --deployment-local-git
```

Cuando se haya creado la aplicación web, la CLI de Azure mostrará información similar a la del ejemplo siguiente:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Ha creado una nueva aplicación web vacía, con la implementación de Git habilitada.

> [!NOTE]
> La dirección URL del repositorio de Git remoto se muestra en la propiedad `deploymentLocalGitUrl`, con el formato `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Guarde esta dirección URL porque la necesitará más adelante.
>

### <a name="configure-database-settings"></a>Configuración de la base de datos

En App Service, las variables de entorno se establecen como _valores de aplicación_ mediante el comando [az appservice web config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) en Cloud Shell.

El siguiente comando Cloud Shell permite configurar los valores de aplicación `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` y `DB_PASSWORD`. Reemplace los marcadores de posición _&lt;appname>_ y _&lt;mysql_server_name>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Configuración de las variables de entorno de Rails

En el terminal local, genere una clave secreta nueva para el entorno de producción de Rails en Azure.

```bash
rails secret
```

Configure las variables que requiere el entorno de producción de Rails.

En el comando Cloud Shell siguiente, reemplace los dos marcadores de posición _&lt;output_of_rails_secret>_ por la clave secreta nueva que generó en el terminal local.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` indica al contenedor Ruby predeterminado que precompile los recursos en cada implementación de Git.

### <a name="push-to-azure-from-git"></a>Inserción en Azure desde Git

En el terminal local, agregue una instancia remota de Azure al repositorio de Git local.

```bash
git remote add azure <paste_copied_url_here>
```

Insértela en la instancia remota de Azure para implementar la aplicación Ruby on Rails. Se le pedirá la contraseña que especificó anteriormente como parte de la creación del usuario de implementación.

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

### <a name="browse-to-the-azure-web-app"></a>Navegación hasta la aplicación web de Azure

Vaya a `http://<app_name>.azurewebsites.net` y agregue algunas tareas a la lista.

![Aplicación Ruby on Rails que se ejecuta en Azure App Service](./media/tutorial-ruby-mysql-app/ruby-mysql-in-azure.png)

Ya está ejecutando una aplicación Ruby on Rails orientada a datos en Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Actualización local del modelo y nueva implementación

En este paso, se realiza un cambio sencillo en el modelo de datos `task` y en la aplicación web y, después, se publica la actualización en Azure.

Para el escenario de las tareas, modifique la aplicación de forma que pueda marcar una tarea como completada.

### <a name="add-a-column"></a>Adición de una columna

En el terminal, navegue a la raíz del repositorio de Git.

Genere una migración nueva que agregue una columna booleana llamada `Done` en la tabla `Tasks`:

```bash
rails generate migration add_Done_to_Tasks Done:boolean
```

Este comando genera un archivo de migración nuevo en el directorio _db/migrate_.


En el terminal, ejecute migraciones de base de datos de Rails para realizar el cambio en la base de datos local.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Actualización de la lógica de aplicación

Abra el archivo *app/controllers/tasks_controller.rb*. Busque esta línea al final del archivo:

```rb
params.require(:task).permit(:Description)
```

Modifique esta línea para incluir el parámetro `Done` nuevo.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Actualización de las vistas

Abra el archivo *app/views/tasks/_form.html.erb*, que es el formulario Edición.

Busque la línea `<%=f.error_span(:Description) %>` e inserte el código siguiente directamente debajo de ella:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Abra el archivo *app/views/tasks/show.html.erb*, que es la página Vista de un solo registro. 

Busque la línea `<dd><%= @task.Description %></dd>` e inserte el código siguiente directamente debajo de ella:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Abra el archivo *app/views/tasks/index.html.erb*, que es la página de índice para todos los registros.

Busque la línea `<th><%= model_class.human_attribute_name(:Description) %></th>` e inserte el código siguiente directamente debajo de ella:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

En el mismo archivo, busque la línea `<td><%= task.Description %></td>` e inserte el código siguiente directamente debajo de ella:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Prueba local de los cambios

En el terminal local, ejecute el servidor Rails.

```bash
rails server
```

Para ver el cambio del estado de la tarea, navegue a `http://localhost:3000` y agregue o edite los elementos.

![Casilla agregada a tarea](./media/tutorial-ruby-mysql-app/complete-checkbox.png)

Para detener el servidor Rails, escriba `Ctrl + C` en el terminal.

### <a name="publish-changes-to-azure"></a>Publicación de los cambios en Azure

En el terminal, ejecute las migraciones de bases de datos Rails para el entorno de producción para hacer el cambio en la base de datos de Azure.

```bash
rake db:migrate RAILS_ENV=production
```

Confirme todos los cambios en Git y, después, inserte los cambios en el código en Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Una vez que `git push` esté completo, navegue hasta la aplicación web de Azure y pruebe la nueva funcionalidad.

![Modelo y cambios en la base de datos publicados en Azure](media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

Si ha agregado tareas, estas se conservarán en la base de datos. Las actualizaciones que efectúe en el esquema de datos dejan los datos existentes intactos.

## <a name="manage-the-azure-web-app"></a>Administración de la aplicación web de Azure

Vaya a [Azure Portal](https://portal.azure.com) para administrar la aplicación web que ha creado.

En el menú izquierdo, haga clic en **App Services** y, a continuación, haga clic en el nombre de la aplicación web de Azure.

![Navegación desde el portal a la aplicación web de Azure](./media/tutorial-php-mysql-app/access-portal.png)

Podrá ver la página de información general de la aplicación web. Aquí se pueden realizar tareas de administración básicas como detener, iniciar, reiniciar, examinar y eliminar.

El menú izquierdo proporciona varias páginas para configurar la aplicación.

![Página de App Service en Azure Portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Crear una base de datos MySQL en Azure
> * Conectar una aplicación Ruby on Rails a MySQL
> * Implementación de la aplicación en Azure
> * Actualizar el modelo de datos y volver a implementar la aplicación
> * Transmitir registros de diagnóstico desde Azure
> * Administrar la aplicación en Azure Portal

Pase al siguiente tutorial para aprender a asignar un nombre DNS personalizado a una aplicación web.

> [!div class="nextstepaction"]
> [Asignar un nombre DNS personalizado a Azure Web Apps](../app-service-web-tutorial-custom-domain.md)
