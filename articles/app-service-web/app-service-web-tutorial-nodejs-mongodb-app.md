---
title: "Compilación de una aplicación web Node.js y MongoDB en Azure | Microsoft Docs"
description: "Aprenda cómo empezar a trabajar con una aplicación Node.js en Azure, con conexión a una base de datos de DocumentDB con una cadena de conexión de MongoDB."
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
ms.date: 03/30/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 8dcb006a8cf167cdbfb67de5a11dabf0edbbe41c
ms.lasthandoff: 04/22/2017


---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure"></a>Compilación de una aplicación web Node.js y MongoDB en Azure
En este tutorial se muestra cómo crear una aplicación web Node.js en Azure y conectarla a una base de datos MongoDB. Cuando haya terminado, tendrá una aplicación MEAN (MongoDB, Express, AngularJS y Node.js) que se ejecuta en [App Service Web Apps](app-service-web-overview.md).

![Aplicación MEAN.js que se ejecuta en Azure App Service](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

## <a name="before-you-begin"></a>Antes de empezar

Antes de ejecutar este ejemplo, instale los siguientes requisitos previos localmente:

1. [Descarga e instalación de Git](https://git-scm.com/)
1. [Descarga e instalación de Node.js y NPM](https://nodejs.org/)
1. [Descarga, instalación y ejecución de MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/). 
1. [Descarga e instalación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-mongodb-database"></a>Prueba de la base de datos MongoDB local
En este paso, asegúrese de que su base de datos local MongoDB está en funcionamiento.

Abra la ventana de terminal y use `CD` para cambiar al directorio `bin` de la instalación de MongoDB. 

Ejecute `mongo` en el terminal para conectarse a su servidor local de MongoDB.

```bash
mongo
```

Si la conexión se realiza correctamente, la base de datos MongoDB estará ya funcionando. En caso contrario, asegúrese de que se ha iniciado la base de datos local MongoDB siguiendo los pasos descritos en [Descarga, instalación y ejecución de MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/).

Cuando haya terminado de probar la base de datos MongoDB, escriba `Ctrl`+`C` en el terminal. 

<a name="step2"></a>

## <a name="create-local-nodejs-application"></a>Creación de una aplicación local Node.js
En este paso, configurará el proyecto local de Node.js.

### <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Abra la ventana de terminal y use `CD` para cambiar a un directorio de trabajo.  

Ejecute los comandos siguientes para clonar el repositorio de ejemplo. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Este repositorio de ejemplo contiene una aplicación [MEAN.js](http://meanjs.org/). 

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

## <a name="create-a-production-mongodb-database"></a>Creación de una base de datos de producción MongoDB

En este paso, creará una base de datos MongoDB en Azure. Cuando la aplicación se implementa en Azure, se utiliza esta base de datos para la carga de trabajo de producción.

Para MongoDB, este tutorial usa [Azure DocumentDB](/azure/documentdb/), que puede admitir conexiones de cliente de MongoDB. En otras palabras, la aplicación Node.js solo sabe que se está conectando a una base de datos MongoDB. El hecho de que la conexión esté respaldada por una base de datos de DocumentDB es transparente para la aplicación.

### <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Ahora vamos a usar la CLI de Azure 2.0 en una ventana de terminal para crear los recursos necesarios para hospedar su aplicación Node.js en Azure App Service.  Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla. 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un [grupo de recursos](../azure-resource-manager/resource-group-overview.md) con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y administran recursos de Azure como aplicaciones web, bases de datos y cuentas de almacenamiento. 

En el ejemplo siguiente se crea un grupo de recursos en la región de Europa Occidental:

```azurecli
az group create --name myResourceGroup --location "West Europe"
```

Para ver los posibles valores que se pueden usar para `--location`, utilice el comando `az appservice list-locations` de la CLI de Azure.

### <a name="create-a-documentdb-account"></a>de una cuenta de Base de datos de documentos

Cree una cuenta de DocumentDB con el comando [az documentdb create](/cli/azure/documentdb#create).

En el siguiente comando, sustituya su propio nombre único de DocumentDB donde vea el marcador de posición `<documentdb_name>`. Este nombre único se usará como parte de su punto de conexión de DocumentDB (`https://<documentdb_name>.documents.azure.com/`), así que debe mantener este carácter unívoco en todas las cuentas de DocumentDB de Azure. 

```azurecli
az documentdb create --name <documentdb_name> --resource-group myResourceGroup --kind MongoDB
```

El parámetro `--kind MongoDB` habilita las conexiones de cliente de MongoDB.

Cuando se crea la cuenta de DocumentDB, la CLI de Azure muestra información similar a la del ejemplo siguiente:

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<documentdb_name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<documentdb_name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<documentdb_name>",
  "readLocations": [
    ...
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    ...
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Conexión de la aplicación Node.js a la base de datos

En este paso, conectará su aplicación de ejemplo MEAN.js a la base de datos de DocumentDB que acaba de crear mediante una cadena de conexión de MongoDB. 

### <a name="retrieve-the-database-key"></a>Recuperación de la clave de base de datos

Para conectarse a la base de datos de DocumentDB, necesita la clave de base de datos. Use el comando [az documentdb list-keys](/cli/azure/documentdb#list-keys) para recuperar la clave principal.

```azurecli
az documentdb list-keys --name <documentdb_name> --resource-group myResourceGroup
```

La CLI de Azure genera información similar al ejemplo siguiente:

```json
{
  "primaryMasterKey": "RUayjYjixJDWG5xTqIiXjC...",
  "primaryReadonlyMasterKey": "...",
  "secondaryMasterKey": "...",
  "secondaryReadonlyMasterKey": "..."
}
```

Copie el valor de `primaryMasterKey` en un editor de texto. Esta información la necesitará en el siguiente paso.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configuración de la cadena de conexión en la aplicación Node.js

En el repositorio de MEAN.js, abra `config/env/production.js`.

En el objeto `db`, reemplace el valor de `uri` como se muestra en el ejemplo siguiente. Asegúrese de reemplazar los dos marcadores de posición `<documentdb_name>` por el nombre de la base de datos de DocumentDB y el marcador de posición `<primary_maste_key>` por la clave que copió en el paso anterior.

```javascript
db: {
  uri: 'mongodb://<documentdb_name>:<primary_maste_key>@<documentdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
  ...
},
```

> [!NOTE] 
> La opción `ssl=true` es importante porque [Azure DocumentDB requiere SSL](../documentdb/documentdb-connect-mongodb-account.md#connection-string-requirements). 
>
>

Guarde los cambios.

### <a name="test-the-application-in-production-mode"></a>Prueba de la aplicación en modo de producción 

Al igual que otras aplicaciones Node.js, MEAN.js usa `gulp prod` para extraer y empaquetar scripts para el entorno de producción. De esta forma se generan los archivos necesarios en dicho entorno. 

Ejecute ahora `gulp prod`.

```bash
gulp prod
```

A continuación, ejecute el siguiente comando para usar la cadena de conexión que configuró en `config/env/production.js`.

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
Database:        mongodb://<documentdb_name>:<primary_maste_key>@<documentdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Vaya a `http://localhost:8443` en un explorador. Haga clic en **Registrarse** en el menú superior e intente crear un usuario ficticio igual que antes. Si lo logra, su aplicación está escribiendo datos en la base de datos de DocumentDB en Azure. 

## <a name="deploy-the-nodejs-application-to-azure"></a>Implementación de la aplicación Node.js en Azure
En este paso, implementará la aplicación Node.js conectada a MongoDB en Azure App Service.

### <a name="create-an-app-service-plan"></a>Creación de un plan del Servicio de aplicaciones

Cree un plan de App Service con el comando [az appservice plan create](/cli/azure/appservice/plan#create). 

> [!NOTE] 
> Un plan de App Service representa la colección de recursos físicos que se utiliza para hospedar las aplicaciones. Todas las aplicaciones asignadas a un plan de App Service comparten los recursos definidos por él, lo que permite ahorrar costos al hospedar varias aplicaciones. 
> 
> Los planes de App Service definen lo siguiente: 
> 
> * Región (Europa del Norte, este de EE. UU., Sudeste Asiático) 
> * Tamaño de la instancia (pequeño, mediano, grande) 
> * Recuento de escala (una, dos, tres instancias, etc.) 
> * SKU (Gratis, Compartido, Básico, Estándar y Premium) 
> 

En el siguiente ejemplo se crea un plan de App Service denominado `myAppServicePlan` con el plan de tarifa **Gratis**.

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Cuando se ha creado el plan de App Service, la CLI de Azure muestra información similar al ejemplo siguiente.

```json 
{ 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "kind": "app", 
    "location": "West Europe", 
    "sku": { 
    "capacity": 0, 
    "family": "F", 
    "name": "F1", 
    "tier": "Free" 
    }, 
    "status": "Ready", 
    "type": "Microsoft.Web/serverfarms" 
} 
``` 

### <a name="create-a-web-app"></a>Creación de una aplicación web

Ahora que ha creado un plan de App Service, cree una aplicación web dentro del plan de App Service `myAppServicePlan`. La aplicación web ofrece un espacio de hospedaje para implementar el código y proporciona una dirección URL para que pueda ver la aplicación implementada. Use el comando [az appservice web create](/cli/azure/appservice/web#create) para crear la aplicación web. 

En el siguiente comando, sustituya el marcador de posición `<app_name>` por su propio nombre de aplicación único. Este nombre único se usará como parte del nombre de dominio predeterminado para la aplicación web, por lo que el nombre debe ser único en todas las aplicaciones de Azure. Más adelante puede asignar cualquier entrada de DNS personalizada a la aplicación web antes de exponerla a los usuarios. 

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Cuando se ha creado la aplicación web, la CLI de Azure muestra información similar a la del ejemplo siguiente: 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

### <a name="configure-an-environment-variable"></a>Configuración de una variable de entorno

Anteriormente en este tutorial, codificó la cadena de conexión de base de datos en `config/env/production.js`. De acuerdo con el procedimiento recomendado de seguridad, querrá mantener estos datos confidenciales fuera de su repositorio de Git. Para la aplicación que se ejecuta en Azure, usará en su lugar una variable de entorno.

En App Service, establecerá variables de entorno como _valores de aplicación_ mediante el comando [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). 

El ejemplo siguiente le permite configurar un valor de aplicación `MONGODB_URI` en su aplicación web de Azure. No olvide reemplazar los marcadores de posición como hizo antes.

```azurecli
az appservice web config appsettings update --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<documentdb_name>:<primary_maste_key>@<documentdb_name>.documents.azure.com:10250/mean?ssl=true"
```

En el código de Node.js, accederá a este valor de aplicación con `process.env.MONGODB_URI`, al igual que accedería a cualquier variable de entorno. 

Ahora, deshaga los cambios realizados en `config/env/production.js` con el siguiente comando:

```bash
git checkout -- .
```

Abra de nuevo `config/env/production.js`. Tenga en cuenta que la aplicación MEAN.js predeterminada ya está configurada para usar la variable de entorno `MONGODB_URI` que acaba de crear.

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
az appservice web deployment user set --user-name <specify-a-username> --password <mininum-8-char-captital-lowercase-number>
```

Use el comando [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) para configurar el acceso de Git local a la aplicación web de Azure. 

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup
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
> - `.deployment`: este archivo indica a App Service que ejecute `bash deploy.sh` como script de implementación personalizado.
> - `deploy.sh`: el script de implementación personalizado. Si revisa el archivo, verá que se ejecuta `gulp prod` después de `npm install` y `bower install`. 
>
> Puede usar este enfoque para agregar cualquier paso a la implementación basada en Git.
>
> Tenga en cuenta que si reinicia la aplicación web de Azure en cualquier momento, App Service no volverá a ejecutar estas tareas de automatización.
>
>

### <a name="browse-to-the-azure-web-app"></a>Navegación hasta la aplicación web de Azure 
Vaya a la aplicación implementada mediante el explorador web. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

Haga clic en **Registrarse** en el menú superior e intente crear un usuario ficticio. 

Si lo logra y la aplicación inicia la sesión automáticamente del usuario creado, la aplicación MEAN.js en Azure tiene conectividad a la base de datos de MongoDB (DocumentDB). 

![Aplicación MEAN.js que se ejecuta en Azure App Service](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Pruebe a hacer clic en **Administrador** > **Manage Articles** (Administrar artículos) para agregar algunos artículos. 

**¡Enhorabuena!** Está ejecutando una aplicación Node.js orientada a datos en Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Actualización del modelo de datos y nueva implementación

En este paso, realizará algunos cambios en los datos de `article` y publicará los cambios en Azure.

### <a name="update-the-data-model"></a>Actualización del modelo de datos

Abra `modules/articles/server/models/articles.server.controller.js`.

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

En primer lugar, abra `modules/articles/server/controllers/articles.server.controller.js`.

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

A continuación, abra `modules/client/views/view-article.client.view.js`.

Justo antes de la etiqueta `</section>` de cierre, agregue la línea siguiente para mostrar `comment` junto con el resto de los datos de artículo:

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

A continuación, abra `modules/client/views/list-articles.client.view.js`.

Justo antes de la etiqueta `</a>` de cierre, agregue la línea siguiente para mostrar `comment` junto con el resto de los datos de artículo:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

A continuación, abra `modules/client/views/admin/list-articles.client.view.js`.

Dentro de la etiqueta `<div class="list-group">` y justo encima de la etiqueta `</a>`, agregue la siguiente línea para mostrar `comment` junto con el resto de los datos de artículo:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Por último, abra `modules/client/views/admin/list-articles.client.view.js`.

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
> Recuerde que `config/env/production.js` se ha revertido y que la variable de entorno `MONGODB_URI` solo está establecida en su aplicación web de Azure y no en su máquina local. Si echa un vistazo al archivo de configuración, verá que la configuración de producción usa de forma predeterminada una base de datos local MongoDB. De esta forma se garantiza que los datos de producción no se tocan al probar los cambios en el código localmente.
>
>

Vaya a `http://localhost:8443` en un explorador y asegúrese de que ha iniciado sesión.

Haga clic en **Administrador** > **Manage Articles** (Administrar artículos) y luego haga clic en el botón **+** para agregar un artículo.

Ahora verá el nuevo cuadro de texto `Comment`.

![Campo de comentario agregado a los artículos](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

### <a name="publish-changes-to-azure"></a>Publicación de los cambios en Azure

Confirme los cambios en Git y, a continuación, inserte los cambios de código en Azure.

```bash
git commit -am "added article comment"
git push azure master
```

Una vez que `git push` está completo, vaya a su aplicación web de Azure y vuelva a probar la nueva funcionalidad.

![Modelo y cambios en la base de datos publicados en Azure](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

> [!NOTE]
> Si agregó anteriormente artículos, aún puede verlos. Los datos existentes en DocumentDB no se pierden. Además, se actualiza el esquema de datos y los datos existentes permanecen sin cambios.
>
>

## <a name="stream-diagnostic-logs"></a>Transmisión de registros de diagnóstico 

Mientras se ejecuta la aplicación de Node.js en Azure App Service, puede tener insertados los registros de consola directamente en su terminal. De este modo, puede obtener los mismos mensajes de diagnóstico para ayudarle a depurar errores de la aplicación.

Para iniciar la secuencia de registro, use el comando [az appservice web log tail](/cli/azure/appservice/web/log#tail).

```azurecli 
az appservice web log tail --name <app_name> --resource-group myResourceGroup 
``` 

Cuando se inicie la secuencia de registro, actualice la aplicación web de Azure en el explorador para obtener algún tráfico web. Ahora debería ver los registros de la consola insertados en su terminal.

Para detener la secuencia de registro en cualquier momento, escriba `Ctrl`+`C`. 

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

<!--

## Step 4 - Download server logs
In this step, you turn on monitoring of your web app with web server logs, and then download these logs. 

### Enable logging
Enable all logging options for your web app.

```azurecli
az appservice web log config --name <app_name> --resource-group myResourceGroup --application-logging true --detailed-error-messages true --failed-request-tracing true --web-server-logging filesystem
```

### Generate errors

To generate some error entries, navigate to a nonexistent page in your web app. For example: `http://<app_name>.azurewebsites.net/404`. 

### Download log files
Download the log files for review.

```azurecli
az appservice web log download --name <app_name> --resource-group myResourceGroup
```

## Step 5 - Scale to another region
In this step, you scale your Node.js app to serve your customers in a new region. That way, you can tailor your web app to customers in different regions, and also put your web app closer to them to improve performance. When you're done with this step, you will have a [Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/) profile with two endpoints, which route traffic to two web apps which reside in different geographical regions.

1. Create a Traffic Manager profile with a unique name and add it to your resource group.

    ```azurecli
    az network traffic-manager profile create --name myTrafficManagerProfile --resource-group myResourceGroup --routing-method Performance --unique-dns-name <unique-dns-name>
    ```

    > [!NOTE]
    > `--routing-method Performance` specifies that this profile [routes user traffic to the closest endpoint](../traffic-manager/traffic-manager-routing-methods.md).

2. Get the resource ID of your existing Node.js web app.

    ```azurecli
    az appservice web show --name <app_name> --resource-group myResourceGroup --query id --output tsv
    ```

3. Add an endpoint to the Traffic Manager profile and put the output of the last command in `<web-app-1-resource-id>`:

    ```azurecli
    az network traffic-manager endpoint create --name <app_name>-westeurope --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id <web-app-1-resource-id>
    ```

4. Your Traffic Manager profile now has an endpoint that points to your web app. Query for its URL to try it out.

    ```azurecli
    az network traffic-manager profile show --name cephalin-express --resource-group myResourceGroup --query dnsConfig.fqdn --output tsv
    ```

    Copy the output into your browser. You should get the default Express page again, with data from your database.

5. Let's add some identifying characteristic to your West Europe app. Add an environment variable.

    ```azurecli
    az appservice web config appsettings update --settings region="Europe" --name <app_name> --resource-group myResourceGroup    
    ```

6. Open `routes/index.js` and change the `router.get()` to use the environment variable.

    ```javascript
    router.get('/', function(req, res, next) {
      res.render('index', { title: 'Express ' + process.env.region, data: output });
    });
    ```

7. Save your changes and push them to Azure.

    ```
    git add .
    git commit -m "added region string."
    git push azure master
    ```

8. Refresh your browser on your Traffic Manager profile's URL. You should now see `Express Europe` in the homepage. 

    Since your Traffic Manager profile only has one endpoint which points to your West Europe web app, this is the only page you'll see. Next, you create a new web app in Southeast Asia and add a new endpoint to the profile.

4. Create an App Service plan and web app in the Southeast Asia region, and deploy the same code to it just like you did in [Step 2]<#step2>.

    ```azurecli
    az appservice plan create --name my-expressjs-appservice-plan-asia --resource-group myResourceGroup --location "Southeast Asia" --sku FREE
    az appservice web create --name <app_name>-asia --plan my-expressjs-appservice-plan-asia --resource-group myResourceGroup
    url=$(az appservice web source-control config-local-git --name <app_name>-asia --resource-group myResourceGroup --query url --output tsv)
    git remote add azureasia $url
    git push azureasia master
    ```

5. Add the same application settings to the new web app. Set the region to `"Asia"`.

    ```azurecli
    az appservice web config appsettings update --settings dbconnstring="mongodb://$accountname:$password@$accountname.documents.azure.com:10250/tutorial?ssl=true&sslverifycertificate=false" --name <app_name>-asia --resource-group myResourceGroup    
    az appservice web config appsettings update --settings region="Asia" --name <app_name>-asia --resource-group myResourceGroup    
    ```

    Since DocumentDB is a [geographically distributed](../documentdb/documentdb-distribute-data-globally.md) NoSQL service, you can use the same MongoDB connection string in the Southeast Asia web app. When the MongoDB client driver connects to your DocumentDB account, Azure automatically figures out where is the closest place to route the connection. No code change is necessary. You only need to add the regions you want to support to your DocumentDB account, which you will do next.

6. Add `Southeast Asia` as a region to your DocumentDB account.

    ```azurecli
    az documentdb update --locations "West Europe"=0 "Southeast Asia"=1 --name $accountname --resource-group myResourceGroup
    ```

3. To finish, add a second endpoint to the Traffic Manager profile and put the output of the last command in `<web-app-2-resource-id>`:

    ```azurecli
    resourceid=$(az appservice web show --name <app_name>-asia --resource-group myResourceGroup --query id --output tsv)
    az network traffic-manager endpoint create -n <app_name>-southeastasia --profile-name myTrafficManagerProfile -g myResourceGroup --type azureEndpoints --target-resource-id resourceid
    ```
  
Now, try to access the URL of your Traffic Manager profile. If you access the URL from the Europe region, you should see "Express Europe", but from the Asia region, you should see "Express Asia".

-->
## <a name="more-resources"></a>Más recursos

