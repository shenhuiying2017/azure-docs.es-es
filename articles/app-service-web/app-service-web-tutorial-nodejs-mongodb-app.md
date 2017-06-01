---
title: "Compilación de una aplicación web Node.js y MongoDB en Azure | Microsoft Docs"
description: "Aprenda a empezar a trabajar con una aplicación Node.js en Azure, con conexión a una base de datos Cosmos DB con una cadena de conexión de MongoDB."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 2a3d63b3829e750b62658d720522ae1abf89cd86
ms.contentlocale: es-es
ms.lasthandoff: 05/31/2017

---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure"></a>Compilación de una aplicación web Node.js y MongoDB en Azure
En este tutorial se muestra cómo crear una aplicación web Node.js en Azure y conectarla a una base de datos MongoDB. Cuando haya terminado, tendrá una aplicación MEAN (MongoDB, Express, AngularJS y Node.js) que se ejecuta en [App Service Web Apps](app-service-web-overview.md). Por sencillez, la aplicación de ejemplo usa el [marco web MEAN.js ](http://meanjs.org/).

![Aplicación MEAN.js que se ejecuta en Azure App Service](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una base de datos MongoDB en Azure
> * Conectar una aplicación Node.js a MongoDB
> * Implementación de la aplicación en Azure
> * Actualizar el modelo de datos y volver a implementar la aplicación
> * Transmitir registros de diagnóstico desde Azure
> * Administrar la aplicación en Azure Portal

## <a name="prerequisites"></a>Requisitos previos

Antes de ejecutar este ejemplo, instale los siguientes requisitos previos localmente:

1. [Descarga e instalación de Git](https://git-scm.com/)
1. [Descarga e instalación de Node.js y NPM](https://nodejs.org/)
1. [Instale Gulp.js](http://gulpjs.com/) (necesario para [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started))
1. [Descarga, instalación y ejecución de MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) 
1. [Descarga e instalación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-mongodb"></a>Prueba de la base de datos MongoDB local
En este paso, asegúrese de que su base de datos local MongoDB está en funcionamiento.

Abra la ventana de terminal y use `cd` para cambiar al directorio `bin` de la instalación de MongoDB. 

Ejecute `mongo` en el terminal para conectarse a su servidor local de MongoDB.

```bash
mongo
```

Si la conexión se realiza correctamente, la base de datos MongoDB estará ya funcionando. En caso contrario, asegúrese de que se ha iniciado la base de datos local MongoDB siguiendo los pasos descritos en [Descarga, instalación y ejecución de MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/). Con frecuencia, MongoDB ya estará instalado, pero aun así deberá iniciarlo ejecutando `mongod`. 

Cuando haya terminado de probar la base de datos MongoDB, escriba `Ctrl`+`C` en el terminal. 

<a name="step2"></a>

## <a name="create-local-nodejs-app"></a>Creación de una aplicación local Node.js
En este paso, configurará el proyecto local de Node.js.

### <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Abra la ventana de terminal y use `cd` para cambiar a un directorio de trabajo.  

Ejecute los comandos siguientes para clonar el repositorio de ejemplo. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Este repositorio de ejemplo contiene una copia del [repositorio MEAN.js](https://github.com/meanjs/mean). Se ha modificado mínimamente para ejecutar App Service (para obtener más información, consulte el [archivo Léame](https://github.com/Azure-Samples/meanjs/blob/master/README.md)).

### <a name="run-the-application"></a>Ejecución de la aplicación

Instale los paquetes necesarios e inicie la aplicación.

```bash
cd meanjs
npm install
npm start
```

Cuando la aplicación se ha cargado completamente, debe ver algo similar a esto:

```
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

Vaya a `http://localhost:3000` en un explorador. Haga clic en **Registrarse** en el menú superior e intente crear un usuario ficticio. 

La aplicación de ejemplo MEAN.js almacena datos de usuario en la base de datos. Si lo logra y MEAN.js inicia la sesión automáticamente del usuario creado, su aplicación escribirá datos en la base de datos local MongoDB.

![MEAN.js se conecta correctamente a MongoDB](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Pruebe a hacer clic en **Administrador** > **Manage Articles** (Administrar artículos) para agregar algunos artículos.

Para detener Node.js en cualquier momento, escriba `Ctrl`+`C` en el terminal. 

## <a name="create-production-mongodb"></a>Creación de una base de datos de MongoDB de producción

En este paso, creará una base de datos MongoDB en Azure. Cuando la aplicación se implementa en Azure, se utiliza esta base de datos para la carga de trabajo de producción.

Para MongoDB, en este tutorial se usa [Azure Cosmos DB](/azure/documentdb/). Cosmos DB admite las conexiones del cliente de MongoDB.

### <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Ahora vamos a usar la CLI de Azure 2.0 en una ventana de terminal para crear los recursos necesarios para hospedar su aplicación Node.js en Azure App Service.  Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla. 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un [grupo de recursos](../azure-resource-manager/resource-group-overview.md) con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y administran recursos de Azure como aplicaciones web, bases de datos y cuentas de almacenamiento. 

En el siguiente ejemplo, se crea un grupo de recursos en la región de Europa Occidental.

```azurecli
az group create --name myResourceGroup --location "West Europe"
```

Para ver los posibles valores que se pueden usar para `--location`, utilice el comando `az appservice list-locations` de la CLI de Azure.

### <a name="create-a-cosmos-db-account"></a>Creación de una cuenta de Cosmos DB

Cree una cuenta de Cosmos DB con el comando [az cosmosdb create](/cli/azure/cosmosdb#create).

En el siguiente comando, reemplace su propio nombre único de Cosmos DB donde vea el marcador de posición _&lt;cosmosdb_name>_. Este nombre único se usa como parte del punto de conexión de Cosmos DB, `https://<cosmosdb_name>.documents.azure.com/`, por lo que debe ser único entre todas las cuentas de Cosmos DB de Azure. 

```azurecli
az cosmosdb create \
    --name <cosmosdb_name> \
    --resource-group myResourceGroup \
    --kind MongoDB
```

El parámetro `--kind MongoDB` habilita las conexiones de cliente de MongoDB.

> [!NOTE]
> _&lt;cosmosdb_name>_ solo debe contener letras minúsculas, números y el carácter _-_, y debe tener una extensión de entre 3 y 50 caracteres.
>
>

Cuando se crea la cuenta de Cosmos DB, la CLI de Azure muestra información similar a la del ejemplo siguiente:

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  < Output has been truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>Conexión de la aplicación a la base de datos MongoDB de producción

En este paso, conectará la aplicación de ejemplo MEAN.js a la base de datos Cosmos DB que acaba de crear mediante una cadena de conexión de MongoDB. 

### <a name="retrieve-the-database-key"></a>Recuperación de la clave de base de datos

Para conectarse a la base de datos Cosmos DB, necesita la clave de base de datos. Use el comando [az cosmosdb list-keys](/cli/azure/cosmosdb#list-keys) para recuperar la clave principal.

```azurecli
az cosmosdb list-keys \
    --name <cosmosdb_name> \
    --resource-group myResourceGroup
```

La CLI de Azure genera información similar al ejemplo siguiente:

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

Copie el valor de `primaryMasterKey` en un editor de texto. Esta información la necesitará en el siguiente paso.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configuración de la cadena de conexión en la aplicación Node.js

En el repositorio MEAN.js, abra _config/env/production.js_.

En el objeto `db`, reemplace el valor de `uri` como se muestra en el siguiente ejemplo. Asegúrese de reemplazar los dos marcadores de posición _&lt;cosmosdb_name>_ por el nombre de la base de datos Cosmos DB y el marcador de posición _&lt;primary_master_key>_ por la clave que copió en el paso anterior.

```javascript
db: {
  uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
  ...
},
```

> [!NOTE] 
> La opción `ssl=true` es importante porque [Cosmos DB necesita SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 
>
>

Guarde los cambios.

### <a name="test-the-application-in-production-mode"></a>Prueba de la aplicación en modo de producción 

Al igual que otras plataformas web de Node.js, MEAN.js usa `gulp` para extraer y empaquetar scripts para el entorno de producción. De esta forma se generan los archivos necesarios en dicho entorno. 

Ejecute ahora `gulp prod`.

```bash
gulp prod
```

A continuación, ejecute el siguiente comando para usar la cadena de conexión que configuró en _config/env/production.js_.

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production` establece la variable de entorno que dice a Node.js que se ejecute en el entorno de producción y `node server.js` inicia el servidor de Node.js con `server.js` en el repositorio raíz. Así es como se carga la aplicación de Node.js en Azure. 

Cuando se cargue la aplicación, compruebe que se ejecuta en producción:

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Vaya a `http://localhost:8443` en un explorador. Haga clic en **Registrarse** en el menú superior e intente crear un usuario ficticio igual que antes. Si lo consigue hacerlo correctamente, significa que la aplicación está escribiendo datos en la base de datos Cosmos DB en Azure. 

En el terminal, detenga Node.js escribiendo `Ctrl` +`C`. 

## <a name="deploy-app-to-azure"></a>Implementación de aplicación en Azure
En este paso, implementará la aplicación Node.js conectada a MongoDB en Azure App Service.

### <a name="create-an-app-service-plan"></a>Creación de un plan del Servicio de aplicaciones

Cree un plan de App Service con el comando [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

En el siguiente ejemplo, se crea un plan de App Service denominado _miPlanDeAppService_ con el plan de tarifa **Gratis**:

```azurecli
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
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
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

En el siguiente comando, reemplace el marcador de posición _&lt;app_name>_ por su propio nombre de aplicación único. Este nombre único se usa como parte del nombre de dominio predeterminado para la aplicación web, por lo que debe ser único en todas las aplicaciones de Azure. Más adelante puede asignar cualquier entrada de DNS personalizada a la aplicación web antes de exponerla a los usuarios. 

```azurecli
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

### <a name="configure-an-environment-variable"></a>Configuración de una variable de entorno

Anteriormente en este tutorial, codificó la cadena de conexión de base de datos de _config/env/production.js_. De acuerdo con el procedimiento recomendado de seguridad, querrá mantener estos datos confidenciales fuera de su repositorio de Git. Para la aplicación que se ejecuta en Azure, usará una variable de entorno en su lugar.

En App Service, establecerá variables de entorno como _valores de aplicación_ mediante el comando [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). 

El ejemplo siguiente le permite configurar un valor de aplicación `MONGODB_URI` en su aplicación web de Azure. No olvide reemplazar los marcadores de posición como hizo antes.

```azurecli
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

En el código de Node.js, accederá a este valor de aplicación con `process.env.MONGODB_URI`, al igual que accedería a cualquier variable de entorno. 

Ahora, deshaga los cambios realizados en _config/env/production.js_ con el siguiente comando:

```bash
git checkout -- .
```

Vuelva a abrir _config/env/production.js_. Tenga en cuenta que la aplicación MEAN.js predeterminada ya está configurada para usar la variable de entorno `MONGODB_URI` que acaba de crear.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="configure-local-git-deployment"></a>Configuración de la implementación de Git local 

Puede implementar su aplicación en Azure App Service de diversas formas, como FTP, repositorio de Git local, GitHub, Visual Studio Team Services y BitBucket. En el caso de FTP y repositorio de Git local, es necesario que haya un usuario de implementación configurado en el servidor para autenticar la implementación. 

Use el comando [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) para crear las credenciales de nivel de cuenta. 

> [!NOTE] 
> Se requiere un usuario de implementación para la implementación de FTP y repositorio de Git Local en App Service. Este usuario de implementación es el nivel de cuenta. Por lo tanto, es diferente de la cuenta de suscripción de Azure. Solo debe configurar este usuario de implementación una vez.

```azurecli
az appservice web deployment user set \
    --user-name <specify-a-username> \
    --password <minimum-8-char-capital-lowercase-number>
```

Use el comando [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) para configurar el acceso de Git local a la aplicación web de Azure. 

```azurecli
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

Inserte la instancia remota de Azure para implementar su aplicación de Node.js. Le pedirá la contraseña que proporcionó anteriormente como parte de la creación del usuario de implementación. 

```bash
git push azure master
```

Durante la implementación, Azure App Service comunicará su progreso con Git.

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

> [!NOTE]
> Puede observar que el proceso de implementación ejecuta [Gulp](http://gulpjs.com/) después de `npm install`. App Service no ejecuta tareas Gulp o Grunt durante la implementación, así que este repositorio de ejemplo tiene dos archivos adicionales en su directorio raíz para permitirlo: 
>
> - _.deployment_: este archivo indica a App Service que ejecute `bash deploy.sh` como script de implementación personalizado.
> - _deploy.sh_: se trata del script de implementación personalizado. Si revisa el archivo, verá que se ejecuta `gulp prod` después de `npm install` y `bower install`. 
>
> Puede usar este enfoque para agregar cualquier paso a la implementación basada en Git.
>
> Si reinicia la aplicación web de Azure en cualquier momento, App Service no vuelve a ejecutar estas tareas de automatización.
>
>

### <a name="browse-to-the-azure-web-app"></a>Navegación hasta la aplicación web de Azure 
Vaya a la aplicación implementada mediante el explorador web. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

Haga clic en **Registrarse** en el menú superior e intente crear un usuario ficticio. 

Si se realiza correctamente y la aplicación inicia automáticamente la sesión del usuario creado, significa que la aplicación MEAN.js de Azure tiene conectividad a la base de datos MongoDB (Cosmos DB). 

![Aplicación MEAN.js que se ejecuta en Azure App Service](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Pruebe a hacer clic en **Administrador** > **Manage Articles** (Administrar artículos) para agregar algunos artículos. 

**¡Enhorabuena!** Está ejecutando una aplicación Node.js orientada a datos en Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Actualización del modelo de datos y nueva implementación

En este paso, realizará algunos cambios en los datos de `article` y publicará los cambios en Azure.

### <a name="update-the-data-model"></a>Actualización del modelo de datos

Abra _modules/articles/server/models/article.server.model.js_.

En `ArticleSchema`, agregue un tipo `String` llamado `comment`. Cuando haya finalizado, su código de esquema tendrá este aspecto:

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

Ya ha terminado con los cambios del modelo. 

### <a name="update-the-articles-code"></a>Actualización del código de artículos

A continuación, actualizará el resto del código de `articles` para usar `comment`.

En total, hay cinco (5) archivos que es necesario modificar: el controlador de servidor y las cuatro vistas de cliente. 

En primer lugar, abra _modules/articles/server/controllers/articles.server.controller.js_.

En la función `update`, agregue una asignación para `article.comment`. Cuando haya terminado, su función `update` debe tener este aspecto:

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

A continuación, abra _modules/articles/client/views/view-article.client.view.html_.

Justo antes de la etiqueta `</section>` de cierre, agregue la línea siguiente para mostrar `comment` junto con el resto de los datos de artículo:

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

A continuación, abra _modules/articles/client/views/list-articles.client.view.html_.

Justo antes de la etiqueta `</a>` de cierre, agregue la línea siguiente para mostrar `comment` junto con el resto de los datos de artículo:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

A continuación, abra _modules/articles/client/views/admin/list-articles.client.view.html_.

Dentro de la etiqueta `<div class="list-group">` y justo encima de la etiqueta `</a>`, agregue la siguiente línea para mostrar `comment` junto con el resto de los datos de artículo:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Por último, abra _modules/articles/client/views/admin/form-article.client.view.html_.

Busque la etiqueta `<div class="form-group">` que contiene el botón de envío, que tiene este aspecto:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Justo encima de esta etiqueta, agregue otra etiqueta `<div class="form-group">` que permite a los usuarios editar el campo `comment`. La nueva etiqueta debe tener este aspecto:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Prueba de los cambios localmente

Guarde todos los cambios.

Pruebe de nuevo los cambios en el modo de producción.

```bash
gulp prod
NODE_ENV=production node server.js
```

> [!NOTE]
> Recuerde que _config/env/production.js_ se ha revertido y que la variable de entorno `MONGODB_URI` solo está establecida en la aplicación web de Azure y no en la máquina local. Si echa un vistazo al archivo de configuración, verá que la configuración de producción usa de forma predeterminada una base de datos local MongoDB. De esta forma se garantiza que los datos de producción no se tocan al probar los cambios en el código localmente.
>
>

Vaya a `http://localhost:8443` en un explorador y asegúrese de que ha iniciado sesión.

Haga clic en **Administrador** > **Manage Articles** (Administrar artículos) y luego haga clic en el botón **+** para agregar un artículo.

Ahora verá el nuevo cuadro de texto `Comment`.

![Campo de comentario agregado a los artículos](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

En el terminal, detenga Node.js escribiendo `Ctrl` +`C`. 

### <a name="publish-changes-to-azure"></a>Publicación de los cambios en Azure

Confirme los cambios en Git y, a continuación, inserte los cambios de código en Azure.

```bash
git commit -am "added article comment"
git push azure master
```

Una vez que `git push` está completo, vaya a su aplicación web de Azure y vuelva a probar la nueva funcionalidad.

![Modelo y cambios en la base de datos publicados en Azure](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

> [!NOTE]
> Si agregó anteriormente artículos, aún puede verlos. Los datos existentes en Cosmos DB no se pierden. Además, se actualiza el esquema de datos y los datos existentes permanecen sin cambios.
>
>

## <a name="stream-diagnostic-logs"></a>Transmisión de registros de diagnóstico 

Mientras se ejecuta la aplicación de Node.js en Azure App Service, puede tener insertados los registros de consola directamente en su terminal. De este modo, puede obtener los mismos mensajes de diagnóstico para ayudarle a depurar errores de la aplicación.

Para iniciar la secuencia de registro, use el comando [az appservice web log tail](/cli/azure/appservice/web/log#tail).

```azurecli 
az appservice web log tail \
    --name <app_name> \
    --resource-group myResourceGroup 
``` 

Cuando se inicie la secuencia de registro, actualice la aplicación web de Azure en el explorador para obtener algún tráfico web. Ahora debería ver los registros de la consola insertados en su terminal.

Detenga las secuencias de registro en cualquier momento escribiendo `Ctrl`+`C`. 

## <a name="manage-your-azure-web-app"></a>Administración de la aplicación web de Azure

Vaya al portal de Azure para ver la aplicación web que ha creado.

Para ello, inicie sesión en [https://portal.azure.com/](https://portal.azure.com).

En el menú izquierdo, haga clic en **App Service**, a continuación, haga clic en el nombre de la aplicación web de Azure.

![Navegación desde el portal a la aplicación web de Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

Ha llegado a la _hoja_ de su aplicación web (una página del portal que se abre horizontalmente).

De forma predeterminada, la hoja de la aplicación web muestra la página de **introducción**. Esta página proporciona una visión del funcionamiento de la aplicación. En este caso, también puede realizar tareas de administración básicas como examinar, detener, iniciar, reiniciar y eliminar. Las pestañas del lado izquierdo de la hoja muestran las diferentes páginas de configuración que puede abrir.

![Hoja de App Service en Azure Portal](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

Estas pestañas de la hoja muestran las muchas y excepcionales características que puede agregar a la aplicación web. La lista siguiente proporciona solo algunas de las posibilidades:

* Asignación de un nombre DNS personalizado
* Enlace de un certificado SSL personalizado
* Configuración de la implementación continua
* Escalado vertical y horizontal
* Adición de la autenticación de usuarios

## <a name="clean-up-resources"></a>Limpieza de recursos
 
Si no necesita estos recursos para otro tutorial (consulte [Pasos siguientes](#next)), puede ejecutar el siguiente comando para eliminarlos: 
  
```azurecli 
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a:

> [!div class="checklist"]
> * Crear una base de datos MongoDB en Azure
> * Conectar una aplicación Node.js a MongoDB
> * Implementación de la aplicación en Azure
> * Actualizar el modelo de datos y volver a implementar la aplicación
> * Transmitir registros desde Azure a un terminal
> * Administrar la aplicación en Azure Portal

Pase al siguiente tutorial para aprender a asignarle un nombre DNS personalizado.

> [!div class="nextstepaction"] 
> [Asignar un nombre DNS personalizado a Azure Web Apps](app-service-web-tutorial-custom-domain.md)

