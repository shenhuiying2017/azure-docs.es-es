---
title: "Compilación de una aplicación web Docker Python con PostgreSQL en Azure | Microsoft Docs"
description: "Aprenda a comenzar a trabajar con una aplicación Docker Python en Azure, con conexión a una base de datos PostgreSQL."
services: app-service\web
documentationcenter: python
author: berndverst
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: 070f69cab63525c3209380bc5f7121812be4a899
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2018
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Compilación de una aplicación web Docker Python con PostgreSQL en Azure

Web App for Containers proporciona un servicio de hospedaje web muy escalable y con aplicación de revisiones de un modo automático. Este tutorial muestra cómo crear una aplicación web Docker Python básica en Azure. Dicha aplicación se conecta a una base de datos PostgreSQL. Cuando haya terminado, tendrá una aplicación Python Flask que se ejecuta en un contenedor Docker en [App Service en Linux](app-service-linux-intro.md).

![Aplicación Docker Python Flask en App Service en Linux](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una base de datos PostgreSQL en Azure
> * Conexión de una aplicación Python a MySQL
> * Implementación de la aplicación en Azure
> * Actualizar el modelo de datos y volver a implementar la aplicación
> * Administrar la aplicación en Azure Portal

También puede seguir los pasos de este artículo en macOS. Las instrucciones de Linux y Windows son las mismas en la mayoría de los casos, pero en este tutorial no se detallan las diferencias.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial:

1. [Instalación de Git](https://git-scm.com/)
1. [Instalación de Python](https://www.python.org/downloads/)
1. [Instalación y ejecución de PostgreSQL](https://www.postgresql.org/download/)
1. [Instalación de Docker Community Edition](https://www.docker.com/community-edition)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Prueba de la instalación local de PostgreSQL y creación de una base de datos

Abra la ventana del terminal y ejecute `psql` para conectarse al servidor de PostgreSQL local.

```bash
sudo -u postgres psql
```

Si la conexión se realiza correctamente, significa que la base de datos PostgreSQL está en ejecución. De lo contrario, asegúrese de que se ha iniciado la base de datos PostgreSQL local, para lo que debe seguir los pasos que se indican en [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Descargas - Distribución del núcleo de PostgreSQL).

Cree una base de datos denominada *eventregistration* y configure un usuario de base de datos independiente denominado *manager* cuya contraseña será *supersecretpass*.

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

Ejecute los siguientes comandos para clonar el repositorio de ejemplo y vaya a la versión *0.1-initialapp*.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Este repositorio de ejemplo contiene una aplicación [Flask](http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Ejecución de la aplicación

> [!NOTE] 
> En un paso posterior, compilará un contenedor de Docker para usarlo con la base de datos de producción con el fin de simplificar este proceso.

Instale los paquetes necesarios e inicie la aplicación.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Cuando la aplicación se haya cargado completamente, verá algo similar al siguiente mensaje:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Vaya a `http://localhost:5000` en un explorador. Haga clic en **Register!** (Registrarse) y cree un usuario de prueba.

![Aplicación Python Flask que se ejecuta de forma local](./media/tutorial-docker-python-postgresql-app/local-app.png)

La aplicación Flask de ejemplo almacena datos de usuario en la base de datos. Si el registro del usuario se realiza correctamente, la aplicación escribe datos en la base de datos de PostgreSQL local.

Para detener el servidor de Flask en cualquier momento, escriba Ctrl+C en el terminal. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-production-postgresql-database"></a>Creación de una base de datos PostgreSQL de producción

En este paso, creará una base de datos PostgreSQL en Azure. Cuando la aplicación se implementa en Azure, utiliza esta base de datos en la nube.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-an-azure-database-for-postgresql-server"></a>Creación de un servidor de Azure Database for PostgreSQL

Cree un servidor de PostgreSQL con el comando [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create).

En el siguiente comando, sustituya un nombre de servidor único por el marcador de posición *\<postgresql_name >* y un nombre usuario por el marcador de posición *\<admin_username >* . El nombre del servidor se usa como parte del punto de conexión de PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), por lo que debe ser único en todos los servidores de Azure. El nombre de usuario es para la cuenta de usuario del administrador de la base de datos inicial. Se le pedirá que seleccione una contraseña para este usuario.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>  --storage-size 51200
```

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

### <a name="create-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Creación de una regla de firewall para el servidor de Azure Database for PostgreSQL

Ejecute el siguiente comando de la CLI de Azure para que se pueda acceder a la base de datos desde todas las direcciones IP.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

La CLI de Azure confirma la creación de la regla de firewall con una salida que es similar a la del ejemplo siguiente:

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

### <a name="create-an-empty-database-and-set-up-a-new-database-application-user"></a>Creación de una base de datos vacía y configuración de un nuevo usuario de la aplicación de base de datos

Cree un usuario de base de datos que solo tenga acceso a una única base de datos. Estas credenciales se usan para no dar a la aplicación acceso total al servidor.

Conéctese a la base de datos (se le pide la contraseña de administrador).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Cree la base de datos y el usuario desde la CLI de PostgreSQL.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Escriba `\q` para salir del cliente de PostgreSQL.

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Prueba local de la aplicación con la base de datos PostgreSQL de Azure

Al volver a la carpeta *app* del repositorio de GitHub clonado, es posible ejecutar la aplicación Python Flask con tan solo actualizar las variables de entorno de la base de datos.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Cuando la aplicación se haya cargado completamente, verá algo similar al siguiente mensaje:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Navegue a http://localhost:5000 en un explorador. Haga clic en **Register!** (Registrarse) y cree un registro de prueba. Ya escribe datos en la base de datos de Azure.

![Aplicación Python Flask que se ejecuta de forma local](./media/tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Ejecución de la aplicación desde un contenedor de Docker

Compilación de la imagen del contenedor de Docker.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker confirma que creó el contenedor correctamente.

```bash
Successfully built 7548f983a36b
```

En la raíz del repositorio, agregue un archivo de variables de entorno denominado _db.env_ y, a continuación, agréguele las siguientes variables de entorno de base de datos. La aplicación se conecta a la base de datos de producción de Azure Database for PostgreSQL.

```text
DBHOST=<postgresql_name>.postgres.database.azure.com
DBUSER=manager@<postgresql_name>
DBNAME=eventregistration
DBPASS=supersecretpass
```

Ejecute la aplicación desde el contenedor de Docker. El siguiente comando especifica el archivo de variables de entorno y asigna el puerto 5000 predeterminado de Flask al puerto 5000 local.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

El resultado es similar a lo que vio anteriormente. No obstante, ya no es necesario efectuar la migración de base de datos inicial, por lo que se omite.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

La base de datos ya contiene el registro que creó anteriormente.

![Aplicación Python Flask basada en contenedor Docker que se ejecuta de forma local](./media/tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Carga del contenedor de Docker a un registro de contenedor

En este paso se carga del contenedor de Docker a un registro de contenedor. Se usa Azure Container Registry, pero también se pueden usar otros registros conocidos, como Docker Hub.

### <a name="create-an-azure-container-registry"></a>Creación de una instancia de Azure Container Registry

Para crear un registro de contenedor, en el siguiente comando, reemplace *\<registry_name>* por el nombre único de Azure Container Registry que elija.

```azurecli-interactive
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

Para mostrar las credenciales del registro, antes hay que habilitar el modo de administrador.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Verá dos contraseñas. Anote el nombre de usuario y la primera contraseña.

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

Inicie sesión en el registro. Cuando se le solicite, proporcione la contraseña que ha recuperado.

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

Inserte la imagen de docker en el registro.

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Implementación de la aplicación Docker Python Flask en Azure

En este paso, implementará la aplicación Python Flask basada en contenedor Docker en Azure App Service.

### <a name="create-an-app-service-plan"></a>Creación de un plan de App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Creación de una aplicación web

Cree una aplicación web en el plan de App Service *myAppServicePlan* con el comando [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create).

La aplicación web ofrece un espacio de hospedaje para implementar el código y proporciona una dirección URL para que pueda ver la aplicación implementada. Úsela para crear la aplicación web.

En el siguiente comando, reemplace el marcador de posición *\<app_name>* por un nombre de aplicación único. Este nombre forma parte de la dirección URL predeterminada de la aplicación web, por lo que el nombre debe ser único en todas las aplicaciones de Azure App Service.

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-container-image-name "<registry_name>.azurecr.io/flask-postgresql-sample"
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

### <a name="configure-the-database-environment-variables"></a>Configuración de las variables de entorno de la base de datos

En este mismo tutorial, ya ha definido variables de entorno para conectarse a su base de datos de PostgreSQL.

En App Service, las variables de entorno se establecen como _valores de aplicación_ mediante el comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

En el siguiente ejemplo se especifican los datos de conexión de la base de datos en forma de valores de configuración de aplicación. También se usa la variable *PORT* para asignar el PUERTO 5000 del contenedor de Docker para reciba tráfico HTTP en el PUERTO 80.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Configuración de la implementación del contenedor de Docker

App Service puede descargar automáticamente un contenedor de Docker y ejecutarlo.

```azurecli
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Cada vez que actualice el contenedor de Docker o cambie la configuración, reinicie la aplicación. El reinicio garantiza que se aplican todas las opciones de configuración y que el contenedor más reciente se extrae del registro.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Navegación hasta la aplicación web de Azure 

Vaya a la aplicación implementada mediante el explorador web. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> La aplicación web tarda más tiempo en cargar porque el contenedor se tiene que descargar e iniciar después de cambie la configuración del contenedor.

Ve a los invitados que registró anteriormente y que se guardaron en la base de datos de producción de Azure en el paso anterior.

![Aplicación Python Flask basada en contenedor Docker que se ejecuta de forma local](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**¡Enhorabuena!** Ya está ejecutando una aplicación Python Flask basada en contenedor en Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Actualización del modelo de datos y nueva implementación

En este paso, se agrega el número de asistentes al registro de cada evento mediante la actualización del modelo Guest.

Consulte la versión *0.2-migration* con el siguiente comando de Git:

```bash
git checkout tags/0.2-migration
```

En esta versión ya se han realizado los cambios necesarios en las vistas, los controladores y el modelo. También incluye una migración de base de datos generada mediante *alembic* (`flask db migrate`). Puede ver todos los cambios realizados mediante el siguiente comando de Git:

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Prueba de los cambios localmente

Ejecute los siguientes comandos para hacer una prueba local de los cambios ejecutando el servidor de Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Vaya a http://localhost:5000 en el explorador para ver los cambios. Cree un registro de prueba.

![Aplicación Python Flask basada en contenedor Docker que se ejecuta de forma local](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publicación de los cambios en Azure

Compile la nueva imagen de Docker, insértela en el registro de contenedor y reinicie la aplicación.

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Vaya a la aplicación web de Azure y vuelva a probar la nueva funcionalidad. Cree otro registro de eventos.

```bash 
http://<app_name>.azurewebsites.net 
```

![Aplicación Docker Python Flask en Azure App Service](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Administración de la aplicación web de Azure

Vaya a [Azure Portal](https://portal.azure.com) para ver la aplicación web que ha creado.

En el menú izquierdo, haga clic en **App Services**, a continuación, haga clic en el nombre de la aplicación web de Azure.

![Navegación desde el portal a la aplicación web de Azure](./media/tutorial-docker-python-postgresql-app/app-resource.png)

De forma predeterminada, el portal muestra la página **Información general**. Esta página proporciona una visión del funcionamiento de la aplicación. En este caso, también puede realizar tareas de administración básicas como examinar, detener, iniciar, reiniciar y eliminar. Las pestañas del lado izquierdo de la página muestran las diferentes páginas de configuración que puede abrir.

![Página de App Service en Azure Portal](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>pasos siguientes

Vaya al siguiente tutorial para aprender a asignar un nombre DNS personalizado a una aplicación web.

> [!div class="nextstepaction"]
> [Asignar un nombre DNS personalizado a Azure Web Apps](../app-service-web-tutorial-custom-domain.md)
