---
title: "Compilación de una aplicación web Node.js y MongoDB en Azure App Service en Linux | Microsoft Docs"
description: "Aprenda a empezar a trabajar con una aplicación Node.js en Azure App Service en Linux, con conexión a una base de datos Cosmos DB con una cadena de conexión de MongoDB."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: syntaxc4
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/10/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: c2087af14ad456c679479334c9391055f6b2e45e
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure-app-service-on-linux"></a>Compilación de una aplicación web Node.js y MongoDB en Azure App Service en Linux

> [!NOTE]
> En este artículo se implementa una aplicación en App Service en Linux. Para una implementación en App Service en _Windows_, consulte [Creación de una aplicación web con Node.js y MongoDB en Azure](../app-service-web-tutorial-nodejs-mongodb-app.md).
>

[App Service en Linux](app-service-linux-intro.md) proporciona un servicio de hospedaje web muy escalable y con aplicación automática de revisiones utilizando el sistema operativo Linux. Este tutorial muestra cómo crear una aplicación web de Node.js, conectarla localmente a una base de datos de MongoDB y luego implementar en Azure conectado a una base de datos de CosmosDB mediante la API de MongoDB. Cuando haya terminado, tendrá una aplicación MEAN (MongoDB, Express, AngularJS y Node.js) que se ejecuta en App Service en Linux. Por sencillez, la aplicación de ejemplo usa el [marco web MEAN.js ](http://meanjs.org/).

![Aplicación MEAN.js que se ejecuta en Azure App Service](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Aprenderá a:

> [!div class="checklist"]
> * Crear una base de datos de CosmosDB mediante la API de MongoDB en Azure
> * Conectar una aplicación Node.js a MongoDB
> * Implementación de la aplicación en Azure
> * Actualizar el modelo de datos y volver a implementar la aplicación
> * Transmitir registros de diagnóstico desde Azure
> * Administrar la aplicación en Azure Portal

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial:

1. [Instalación de Git](https://git-scm.com/)
1. [Instalación de Node.js v6.0 o superior y NPM](https://nodejs.org/)
1. [Instale Gulp.js](http://gulpjs.com/) (necesario para [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started))
1. [Descarga y ejecución de MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-mongodb"></a>Prueba de la base de datos MongoDB local

Abra la ventana de terminal y use `cd` para cambiar al directorio `bin` de la instalación de MongoDB. Esta ventana de terminal se puede usar para ejecutar todos los comandos de este tutorial.

Ejecute `mongo` en el terminal para conectarse a su servidor local de MongoDB.

```bash
mongo
```

Si la conexión se realiza correctamente, la base de datos de MongoDB estará ya funcionando. En caso contrario, asegúrese de que la base de datos de MongoDB local se inicia con los pasos descritos en [Install MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) (Instalación de MongoDB Community Edition). Con frecuencia, MongoDB ya estará instalado, pero aun así deberá iniciarlo ejecutando `mongod`.

Cuando haya terminado de probar la base de datos de MongoDB, escriba `Ctrl+C` en el terminal.

## <a name="create-local-nodejs-app"></a>Creación de una aplicación local Node.js

En este paso, configurará el proyecto local de Node.js.

### <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

En la ventana del terminal, use `cd` para cambiar a un directorio de trabajo.

Ejecute el comando siguiente para clonar el repositorio de ejemplo.

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Este repositorio de ejemplo contiene una copia del [repositorio MEAN.js](https://github.com/meanjs/mean). Se ha modificado para ejecutarse App Service (para más información, consulte el [archivo LÉAME](https://github.com/Azure-Samples/meanjs/blob/master/README.md) del repositorio MEAN.js).

### <a name="run-the-application"></a>Ejecución de la aplicación

Instale los comandos siguientes para instalar los paquetes necesarios e inicie la aplicación.

```bash
cd meanjs
npm install
npm start
```

Ignore la advertencia de config.domain. Cuando la aplicación se haya cargado completamente, verá algo similar al siguiente mensaje:

```txt
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

Vaya a `http://localhost:3000` en un explorador. Haga clic en **Registrarse** en el menú superior y cree un usuario de prueba. 

La aplicación de ejemplo MEAN.js almacena datos de usuario en la base de datos. Si crea un usuario e inicia sesión con él correctamente, la aplicación escribe datos en la base de datos de MongoDB local.

![MEAN.js se conecta correctamente a MongoDB](./media/tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Seleccione **Admin (Administrador) > Manage Articles (Administrar artículos)** para agregar algunos artículos.

Para detener Node.js en cualquier momento, presione `Ctrl+C` en el terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>Creación de una base de datos de MongoDB de producción

En este paso, creará una base de datos MongoDB en Azure. Cuando la aplicación se implementa en Azure, utiliza esta base de datos en la nube.

Para MongoDB, en este tutorial se usa [Azure Cosmos DB](/azure/documentdb/). Cosmos DB admite las conexiones del cliente de MongoDB.

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-cosmos-db-account"></a>Creación de una cuenta de Cosmos DB

En Cloud Shell, cree una cuenta de Cosmos DB con el comando [az cosmosdb create](/cli/azure/cosmosdb?view=azure-cli-latest#az_cosmosdb_create).

En el siguiente comando, sustituya un nombre único de Cosmos DB por el marcador de posición *\<cosmosdb_name>*. Este nombre se usa como parte del punto de conexión de Cosmos DB, `https://<cosmosdb_name>.documents.azure.com/`, por lo que el nombre debe ser único en todas las cuentas de Cosmos DB de Azure. El nombre debe contener solo letras minúsculas, números y el carácter de guión (-), y debe tener una longitud de entre 3 y 50 caracteres.

```azurecli-interactive
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

El parámetro *--kind MongoDB* habilita las conexiones de cliente de MongoDB.

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
  < Output truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>Conexión de la aplicación a la base de datos MongoDB de producción

En este paso, conectará la aplicación de ejemplo MEAN.js a la base de datos Cosmos DB que acaba de crear mediante una cadena de conexión de MongoDB.

### <a name="retrieve-the-database-key"></a>Recuperación de la clave de base de datos

Para conectarse a la base de datos Cosmos DB, necesita la clave de base de datos. En Cloud Shell, use el comando [az cosmosdb list-keys](/cli/azure/cosmosdb?view=azure-cli-latest#az_cosmosdb_list_keys) para recuperar la clave principal.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

La CLI de Azure muestra información similar a la del ejemplo siguiente:

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

Copie el valor de `primaryMasterKey`. Esta información la necesitará en el siguiente paso.

<a name="devconfig"></a>

### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configuración de la cadena de conexión en la aplicación Node.js

En la carpeta _config/env/_ del repositorio MEAN.js local, cree un archivo llamado _local-production.js_. _.gitignore_ está configurado para mantener este archivo fuera del repositorio.

Copie en él el código siguiente. Asegúrese de reemplazar los dos marcadores de posición *\<cosmosdb_name>* por el nombre de la base de datos Cosmos DB y de reemplazar el marcador de posición *\<primary_master_key>* por la clave que copió en el paso anterior.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

La opción `ssl=true` se requiere porque [Cosmos DB requiere SSL](../../cosmos-db/connect-mongodb-account.md#connection-string-requirements).

Guarde los cambios.

### <a name="test-the-application-in-production-mode"></a>Prueba de la aplicación en modo de producción

En una ventana de terminal local, ejecute el siguiente comando para minimizar y agrupar scripts para el entorno de producción. Este proceso genera los archivos que necesita dicho entorno.

```bash
gulp prod
```

En la ventana de terminal local, ejecute el siguiente comando para usar la cadena de conexión que configuró en _config/env/local-production.js_. Omita el error de certificado y la advertencia de config.domain.

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production` establece la variable de entorno que indica a Node.js que se ejecute en el entorno de producción.  `node server.js` inicia el servidor de Node.js con `server.js` en la raíz del repositorio. Así es como se carga la aplicación de Node.js en Azure.

Cuando se cargue la aplicación, compruebe que se ejecuta en el entorno de producción:

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Vaya a `http://localhost:8443` en un explorador. Haga clic en **Registrarse** en el menú superior y cree un usuario de prueba. Si crea un usuario e inicia sesión con él correctamente, la aplicación escribe datos en la base de datos de Cosmos DB en Azure.

En el terminal, detenga Node.js escribiendo `Ctrl+C`.

## <a name="deploy-app-to-azure"></a>Implementación de aplicación en Azure

En este paso, implementará la aplicación Node.js conectada a MongoDB en Azure App Service.

### <a name="configure-local-git-deployment"></a>Configuración de la implementación de Git local

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creación de un plan de App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-linux-based-web-app"></a>Creaación de una aplicación de web basada en Linux

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Configuración de una variable de entorno

De forma predeterminada, el proyecto MEAN.js mantiene _config/env/local-production.js_ fuera del repositorio de Git. Por lo tanto, para su aplicación web de Azure, use la configuración de aplicación para definir la cadena de conexión de MongoDB.

Para establecer la configuración de la aplicación, use el comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) en Cloud Shell.

En el ejemplo siguiente se configura una opción de configuración de aplicación `MONGODB_URI` en la aplicación web de Azure. Reemplace los marcadores de posición  *\<app_name >*,  *\<cosmosdb_name >* y  *\<primary_master_key >*.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

En el código de Node.js, accederá a este valor de aplicación con `process.env.MONGODB_URI`, al igual que accedería a cualquier variable de entorno. 

En el repositorio MEAN.js local, abra _config/env/production.js_ (no _config/env/local-production.js_), que tiene la configuración específica para el entorno de producción. Tenga en cuenta que la aplicación MEAN.js predeterminada ya está configurada para usar la variable de entorno `MONGODB_URI` que ha creado.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Inserción en Azure desde Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

Puede observar que el proceso de implementación ejecuta [Gulp](http://gulpjs.com/) después de `npm install`. App Service no ejecuta tareas Gulp o Grunt durante la implementación, así que este repositorio de ejemplo tiene dos archivos adicionales en su directorio raíz para permitirlo:

- _.deployment_: este archivo indica a App Service que ejecute `bash deploy.sh` como script de implementación personalizado.
- _deploy.sh_: se trata del script de implementación personalizado. Si revisa el archivo, verá que se ejecuta `gulp prod` después de `npm install` y `bower install`.

Puede usar este enfoque para agregar cualquier paso a la implementación basada en Git. Si reinicia la aplicación web de Azure en cualquier momento, App Service no vuelve a ejecutar estas tareas de automatización.

### <a name="browse-to-the-azure-web-app"></a>Navegación hasta la aplicación web de Azure

Vaya a la aplicación implementada mediante el explorador web.

```bash
http://<app_name>.azurewebsites.net
```

Haga clic en **Registrarse** en el menú superior y cree un usuario ficticio.

Si se realiza correctamente y la aplicación inicia automáticamente la sesión del usuario creado, la aplicación MEAN.js de Azure tiene conectividad con la API de MongoDB de la base de datos Cosmos DB.

![Aplicación MEAN.js que se ejecuta en Azure App Service](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Seleccione **Admin (Administrador) > Manage Articles (Administrar artículos)** para agregar algunos artículos.

**¡Enhorabuena!** Está ejecutando una aplicación Node.js orientada a datos en Azure App Service en Linux.

## <a name="update-data-model-and-redeploy"></a>Actualización del modelo de datos y nueva implementación

En este paso, se cambia el modelo de datos de `article` y se publica el cambio en Azure.

### <a name="update-the-data-model"></a>Actualización del modelo de datos

En el repositorio MEAN.js local, abra _modules/articles/server/models/article.server.model.js_.

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

### <a name="update-the-articles-code"></a>Actualización del código de artículos

Actualice el resto del código de `articles` para usar `comment`.

Hay cinco archivos que es necesario modificar: el controlador del servidor y las cuatro vistas del cliente. 

Abra _modules/articles/server/controllers/articles.server.controller.js_.

En la función `update`, agregue una asignación para `article.comment`. El código siguiente muestra la función `update` completada:

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Abra _modules/articles/client/views/view-article.client.view.html_.

Justo antes de la etiqueta `</section>` de cierre, agregue la línea siguiente para mostrar `comment` junto con el resto de los datos de artículo:

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Abra _modules/articles/client/views/list-articles.client.view.html_.

Justo antes de la etiqueta `</a>` de cierre, agregue la línea siguiente para mostrar `comment` junto con el resto de los datos de artículo:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Abra _modules/articles/client/views/admin/list-articles.client.view.html_.

Dentro del elemento `<div class="list-group">` e inmediatamente encima de la etiqueta `</a>` de cierre, agregue la siguiente línea para mostrar `comment` junto con el resto de los datos de artículo:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Abra _modules/articles/client/views/admin/form-article.client.view.html_.

Busque el elemento `<div class="form-group">` que contiene el botón de envío, que es similar a este:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Inmediatamente encima de esta etiqueta, agregue otro elemento `<div class="form-group">` que permita a los usuarios editar el campo `comment`. El nuevo elemento debe ser como este:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Prueba de los cambios localmente

Guarde todos los cambios.

En la ventana de terminal local, pruebe de nuevo los cambios en el modo de producción.

```bash
gulp prod
NODE_ENV=production node server.js
```

> [!NOTE]
> Recuerde que _config/env/production.js_ se ha revertido y que la variable de entorno `MONGODB_URI` solo está establecida en la aplicación web de Azure y no en la máquina local. Si examina al archivo de configuración, verá que la configuración de producción usa de forma predeterminada una base de datos de MongoDB local. De esta forma se garantiza que los datos de producción no se tocan al probar los cambios en el código localmente.

Vaya a `http://localhost:8443` en un explorador y asegúrese de que ha iniciado sesión.

Seleccione **Admin (Administrador) > Manage Articles (Administrar artículos)** y luego haga clic en el botón **+** para agregar un artículo.

Ahora verá el nuevo cuadro de texto `Comment`.

![Campo de comentario agregado a los artículos](./media/tutorial-nodejs-mongodb-app/added-comment-field.png)

En el terminal, detenga Node.js escribiendo `Ctrl+C`.

### <a name="publish-changes-to-azure"></a>Publicación de los cambios en Azure

Confirme los cambios en Git y, después, inserte los cambios de código en Azure.

```bash
git commit -am "added article comment"
git push azure master
```

Una vez que `git push` esté completo, navegue hasta su aplicación web de Azure y pruebe la nueva funcionalidad.

![Modelo y cambios en la base de datos publicados en Azure](media/tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Si agregó anteriormente artículos, aún puede verlos. Los datos existentes en Cosmos DB no se pierden. Además, se actualiza el esquema de datos y los datos existentes permanecen sin cambios.

## <a name="manage-your-azure-web-app"></a>Administración de la aplicación web de Azure

Vaya a [Azure Portal](https://portal.azure.com) para ver la aplicación web que ha creado.

En el menú izquierdo, haga clic en **App Services**, a continuación, haga clic en el nombre de la aplicación web de Azure.

![Navegación desde el portal a la aplicación web de Azure](./media/tutorial-nodejs-mongodb-app/access-portal.png)

De forma predeterminada, el portal muestra la página **Información general**. Esta página proporciona una visión del funcionamiento de la aplicación. En este caso, también puede realizar tareas de administración básicas como examinar, detener, iniciar, reiniciar y eliminar. Las pestañas del lado izquierdo de la página muestran las diferentes páginas de configuración que puede abrir.

![Página de App Service en Azure Portal](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>pasos siguientes

¿Qué ha aprendido?

> [!div class="checklist"]
> * Crear una base de datos de CosmosDB mediante la API de MongoDB en Azure
> * Conectar una aplicación Node.js a MongoDB
> * Implementación de la aplicación en Azure
> * Actualizar el modelo de datos y volver a implementar la aplicación
> * Transmitir registros desde Azure a un terminal
> * Administrar la aplicación en Azure Portal

Vaya al siguiente tutorial para aprender a asignar un nombre DNS personalizado a una aplicación web.

> [!div class="nextstepaction"]
> [Asignar un nombre DNS personalizado a Azure Web Apps](../app-service-web-tutorial-custom-domain.md)
