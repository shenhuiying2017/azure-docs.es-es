---
title: "Compilación de una aplicación web Docker Python con PostgreSQL en Azure | Microsoft Docs"
description: "Aprenda a comenzar a trabajar con una aplicación Docker Python en Azure, con conexión a una base de datos PostgreSQL."
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
editor: 
ms.assetid: 2bada123-ef18-44e5-be71-e16323b20466
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/03/2017
ms.author: beverst
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 554843c5f3161a45ff984b000944f546b44dd472
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Compilación de una aplicación web Docker Python con PostgreSQL en Azure
Este tutorial indica cómo crear una aplicación web Docker Python básica en Azure. También conectará esta aplicación a una base de datos PostgreSQL. Cuando haya terminado, tendrá una aplicación Python Flask que se ejecuta dentro de un contenedor Docker en [Azure App Service Web Apps](app-service-web-overview.md).

![Aplicación Docker Python Flask en Azure App Service](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="before-you-begin"></a>Antes de empezar

Antes de ejecutar este ejemplo, instale los siguientes requisitos previos localmente:

1. [Descarga e instalación de Git](https://git-scm.com/)
1. [Descarga e instalación de Python](https://www.python.org/downloads/)
1. [Descarga, instalación y ejecución de PostgreSQL](https://www.postgresql.org/download/)
1. [Descarga e instalación de Docker Community Edition](https://www.docker.com/community-edition)
1. [Descarga e instalación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Prueba de la instalación local de PostgreSQL y creación de una base de datos
En este paso, se asegurará de que la base de datos local de PostgreSQL se esté ejecutando.

Abra la ventana del terminal y ejecute `psql postgres` para conectarse al servidor de PostgreSQL local.

```bash
psql postgres
```

Si la conexión se realiza correctamente, la base de datos PostgreSQL local ya está ejecutándose. En caso contrario, asegúrese de que se ha iniciado la base de datos PostgreSQL local siguiendo los pasos descritos en [Descarga, instalación y ejecución de PostgreSQL](https://www.postgresql.org/download/).

Cree una base de datos denominada `eventregistration` y configure un usuario de base de datos independiente denominado `manager` y con la contraseña `supersecretpass`.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
Escriba `\q` para salir del cliente de PostgreSQL. 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>Creación de una aplicación Python Flask local
En este paso, configurará el proyecto de Python Flask local.

### <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Abra la ventana de terminal y use `CD` para cambiar a un directorio de trabajo.  

Ejecute los siguientes comandos para clonar el repositorio de ejemplo y acceder a la versión `0.1-initialapp`.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.gi
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Este repositorio de ejemplo contiene una aplicación [Flask](http://http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Ejecución de la aplicación

> [!NOTE] 
> En un paso posterior, compilaremos un contenedor de Docker para usarlo con nuestra base de datos de producción a fin de simplificar este proceso.

Instale los paquetes necesarios e inicie la aplicación.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

Cuando la aplicación se ha cargado completamente, debe ver algo similar a esto:

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Vaya a `http://127.0.0.1:5000` en un explorador. Haga clic en **Register!** (Registrarse) e intente crear un registro ficticio.

![Aplicación Python Flask que se ejecuta de forma local](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

La aplicación Flask de ejemplo almacena datos de usuario en la base de datos. Si realiza el proceso correctamente y es capaz de ver el registro en la aplicación, significa que esta está escribiendo datos en la base de datos PostgreSQL local.

Para detener el servidor de Flask en cualquier momento, escriba `Ctrl`+`C` en el terminal. 

## <a name="create-a-production-postgresql-database"></a>Creación de una base de datos PostgreSQL de producción

En este paso, creará una base de datos PostgreSQL en Azure. Al implementar la aplicación en Azure, se especifica esta base de datos para la carga de trabajo de producción correspondiente.

### <a name="log-in-to-azure"></a>Inicie sesión en Azure.

A continuación, usará la CLI de Azure 2.0 en una ventana de terminal para crear los recursos necesarios para hospedar su aplicación de Python en Azure App Service.  Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla. 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un [grupo de recursos](../azure-resource-manager/resource-group-overview.md) con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y administran recursos de Azure como aplicaciones web, bases de datos y cuentas de almacenamiento. 

En el siguiente ejemplo, se crea un grupo de recursos en la región del oeste de EE. UU.:

```azurecli
az group create --name myResourceGroup --location "West US"
```

Para ver los posibles valores que se pueden usar para `--location`, utilice el comando `az appservice list-locations` de la CLI de Azure.

### <a name="create-an-azure-database-for-postgresql-server"></a>Creación de una base de datos de Azure para un servidor de PostgreSQL

Cree un servidor de PostgreSQL con el comando [az postgres server create](/cli/azure/documentdb#create).

En el siguiente comando, reemplace su propio nombre único de servidor de PostgreSQL donde vea el marcador de posición `<postgresql_name>`. Este nombre único se usa como parte del punto de conexión de PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), por lo que debe ser único entre todos los servidores de Azure. 

```azurecli
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <my_admin_username>
```

Se necesita el elemento `--admin-user` para crear la cuenta de usuario administrador de la base de datos inicial. Se le pedirá que seleccione una contraseña para este usuario.

Cuando se crea la base de datos de Azure para el servidor de PostgreSQL, la CLI de Azure muestra información similar a la del siguiente ejemplo:

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="creating-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Creación de una regla de firewall para la base de datos de Azure para el servidor de PostgreSQL

Antes de poder acceder a la base de datos, es necesario permitir acceder a ella desde todas las direcciones IP. Puede hacerse mediante el siguiente comando de CLI de Azure:

```azurecli
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

Una vez creado el firewall, la CLI de Azure confirma la presencia de las reglas de la siguiente forma:

```json
{
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

## <a name="connect-your-python-flask-application-to-the-database"></a>Conexión de la aplicación Python Flask a la base de datos

En este paso, conectará la aplicación Python Flask de ejemplo a la base de datos de Azure del servidor de PostgreSQL que creó.

### <a name="creating-an-empty-database-and-setting-up-a-new-database-application-user"></a>Creación de una base de datos vacía y configuración de un nuevo usuario de aplicación de base de datos

Cree un nuevo usuario de base de datos que tenga acceso solamente a una base de datos única. Mediante este paso, se evita otorgar acceso total al servidor a la aplicación a través de las credenciales de administrador.

Conéctese a la base de datos (se le pedirá la contraseña de administrador).
```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

A continuación, cree la base de datos y el usuario desde la CLI de PostgreSQL.
```
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Escriba `\q` para salir del cliente de PostgreSQL.

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Prueba local de la aplicación con la base de datos PostgreSQL de Azure 

Al volver a la carpeta `app` del repositorio de GitHub clonado, es posible ejecutar la aplicación Python Flask con tan solo actualizar las variables de entorno de la base de datos.

```bash
FLASK_APP=app.py;DBHOST="<postgresql_name>.postgres.database.azure.com";DBUSER="manager@<postgresql_name>";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

Cuando la aplicación se cargue completamente, debe ver algo similar al siguiente mensaje:

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Vaya a `http://127.0.0.1:5000` en un explorador. Haga clic en **Register!** (Registrarse) e intente crear un registro ficticio. Ya está escribiendo datos en la base de datos de producción en Azure.

![Aplicación Python Flask que se ejecuta de forma local](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Ejecución de la aplicación desde un contenedor de Docker

A continuación, compilará la imagen de contenedor de Docker y ejecutará la aplicación de forma local desde un contenedor de Docker mientras continúa conectado a la base de datos de producción PostgreSQL en Azure.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker confirma que creó el contenedor correctamente.

```
Successfully built 7548f983a36b
```

Agregue las variables de entorno de la base de datos a un archivo de variables de entorno `db.env`.

```
DBHOST="<postgresql_name>.postgres.database.azure.com"
DBUSER="manager@<postgresql_name>"
DBNAME="eventregistration"
DBPASS="supersecretpass"
```

Ahora, ejecute la aplicación desde el contenedor de Docker. Especifique el archivo de variables de entorno y asigne el puerto de Flask 5000 predeterminado al puerto local 5000.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

Evidentemente, el resultado es similar al anterior. No obstante, ya no es necesario efectuar la migración de base de datos inicial, por lo que se omite.
```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
 ```

 La base de datos ya contiene el registro que creó anteriormente.

 ![Aplicación Python Flask basada en contenedor Docker que se ejecuta de forma local](./media/app-service-web-tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Carga del contenedor de Docker a un registro de contenedor
En este paso, cargará el contenedor Docker que creó a un registro de contenedor. En este caso, se usará Azure Container Registry, aunque también es posible usar otros registros conocidos, como Docker Hub.

### <a name="create-an-azure-container-registry"></a>Creación de una instancia de Azure Container Registry

Para crear un registro de contenedor en el siguiente comando, reemplace `<registry_name>` por el nombre de Azure Container Registry que elija.

```azurecli
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

Salida
```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>Recuperación de las credenciales de registro para la inserción y la extracción de imágenes de Docker

Debe habilitar el modo de administrador antes de poder acceder a las credenciales.

```azurecli
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Aparecerán dos contraseñas. Anote el nombre de usuario y la primera contraseña.
```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-your-docker-container-to-azure-container-registry"></a>Carga del contenedor de Docker a Azure Container Registry

```bash
docker login <registry_name>.azurecr.io -u <registry_name> -p "<registry_password>"
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Implementación de la aplicación Docker Python Flask en Azure
En este paso, implementará la aplicación Python Flask basada en contenedor Docker en Azure App Service.

### <a name="create-an-app-service-plan"></a>Creación de un plan del Servicio de aplicaciones

Cree un plan de App Service con el comando [az appservice plan create](/cli/azure/appservice/plan#create). 

> [!NOTE] 
> Un plan de App Service representa la colección de recursos físicos que se utiliza para hospedar las aplicaciones. Todas las aplicaciones asignadas a un plan de App Service comparten los recursos definidos por él, lo que permite ahorrar costos al hospedar varias aplicaciones. 
> 
> Los planes de App Service definen lo siguiente: 
> 
> * Región (Europa del Norte, este de EE. UU., Sudeste Asiático, etc.) 
> * Tamaño de la instancia (pequeño, mediano, grande) 
> * Recuento de escala (una, dos, tres instancias, etc.) 
> * SKU (Gratis, Compartido, Básico, Estándar y Premium) 
> 

En el siguiente ejemplo, se crea un plan de App Service basado en Linux y denominado `myAppServicePlan` con el plan de tarifa S1:

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Cuando se ha creado el plan de App Service, la CLI de Azure muestra información similar al ejemplo siguiente.

```json 
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "linux",
  "location": "West US",
  "maximumNumberOfWorkers": 10,
  "name": "myAppServicePlan",
  "numberOfSites": 0,
  "perSiteScaling": false,
  "provisioningState": "Succeeded",
  "reserved": true,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "family": "S",
    "locations": null,
    "name": "S1",
    "size": "S1",
    "skuCapacity": null,
    "tier": "Standard"
  },
  "status": "Ready",
  "subscription": "00000000-0000-0000-0000-000000000000",
  "tags": null,
  "targetWorkerCount": 0,
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
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

### <a name="configure-the-database-environment-variables"></a>Configuración de las variables de entorno de la base de datos

En una fase anterior del tutorial, definió manualmente variables de entorno para conectarse a la base de datos PostgreSQL.

En App Service, establecerá variables de entorno como _valores de aplicación_ mediante el comando [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). 

La siguiente instancia le permiten especificar los datos de conexión de la base de datos en forma de opciones de configuración de aplicación. También se usa la variable `PORT` para especificar que se quiere asignar el puerto 5000 del contenedor de Docker para recibir tráfico de HTTP a través del puerto 80.

```azurecli
az appservice web config appsettings update --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Configuración de la implementación del contenedor de Docker 

App Service puede descargar automáticamente un contenedor de Docker y ejecutarlo.

Use el comando [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) para crear las credenciales de nivel de cuenta. 

```azurecli
az appservice web config container update --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-custom-image-name "<registry_name>.azurecr.io/flask-postgresql-sample" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Siempre que actualice el contenedor Docker o modifique la configuración anterior, reinicie la aplicación para asegurarse de que se apliquen todos los valores y se extraiga el contenedor más reciente del registro.

```azurecli
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Navegación hasta la aplicación web de Azure 
Vaya a la aplicación implementada mediante el explorador web. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> La primera vez que acceda a la aplicación web después de cambiar la configuración del contenedor, espere un poco más a que el contenedor se descargue e inicie.

Verá a los invitados registrados previamente que se guardaron en la base de datos de producción de Azure en el paso anterior.

 ![Aplicación Python Flask basada en contenedor Docker que se ejecuta de forma local](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**¡Enhorabuena!** Ya está ejecutando una aplicación Python Flask basada en contenedor en Azure App Service.


## <a name="update-data-model-and-redeploy"></a>Actualización del modelo de datos y nueva implementación

En este paso, agregará el número de asistentes al registro de cada evento actualizando el modelo Guest.

Consulte la versión de `0.2-migration` con el siguiente comando de Git.
```bash
git checkout tags/0.2-migration
```

En esta versión, ya se realizaron los cambios necesarios en las vistas, los controladores y el modelo. También incluye una migración de base de datos generada mediante *alembic* (`flask db migrate`). Puede ver todos los cambios realizados mediante el siguiente comando de Git.

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Prueba de los cambios localmente

Ejecute los siguientes comandos para hacer una prueba local de los cambios ejecutando el servidor de Flask.

Mac o Linux:
```bash
source venv/bin/activate
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

Vaya a `http://127.0.0.1:5000` en el explorador para ver los cambios. Cree un nuevo registro ficticio.

![Aplicación Python Flask basada en contenedor Docker que se ejecuta de forma local](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publicación de los cambios en Azure

Compile la nueva imagen de Docker, insértela en el registro de contenedor y reinicie la aplicación.

```bash
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Vaya a la aplicación web de Azure y vuelva a probar la nueva funcionalidad. Cree otro registro de eventos.

```bash 
http://<app_name>.azurewebsites.net 
```

![Aplicación Docker Python Flask en Azure App Service](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Administración de la aplicación web de Azure

Vaya al portal de Azure para ver la aplicación web que ha creado.

Para ello, inicie sesión en [https://portal.azure.com/](https://portal.azure.com).

En el menú izquierdo, haga clic en **App Service**, a continuación, haga clic en el nombre de la aplicación web de Azure.

![Navegación desde el portal a la aplicación web de Azure](./media/app-service-web-tutorial-docker-python-postgresql-app/app-resource.png)

Ha llegado a la _hoja_ de su aplicación web (una página del portal que se abre horizontalmente).

De forma predeterminada, la hoja de la aplicación web muestra la página de **introducción**. Esta página proporciona una visión del funcionamiento de la aplicación. En este caso, también puede realizar tareas de administración básicas como examinar, detener, iniciar, reiniciar y eliminar. Las pestañas del lado izquierdo de la hoja muestran las diferentes páginas de configuración que puede abrir.

![Hoja de App Service en Azure Portal](./media/app-service-web-tutorial-docker-python-postgresql-app/app-mgmt.png)

Estas pestañas de la hoja muestran las muchas y excepcionales características que puede agregar a la aplicación web. La lista siguiente proporciona solo algunas de las posibilidades:

* Asignación de un nombre DNS personalizado
* Enlace de un certificado SSL personalizado
* Configuración de la implementación continua
* Escalado vertical y horizontal
* Adición de la autenticación de usuarios


