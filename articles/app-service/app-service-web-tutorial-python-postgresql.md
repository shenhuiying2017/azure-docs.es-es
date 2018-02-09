---
title: "Compilación de una aplicación web Python y PostgreSQL en Azure | Microsoft Docs"
description: "Aprenda a trabajar con una aplicación Python en Azure, con conexión a una base de datos PostgreSQL."
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/25/2018
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: 87ed4015e06e0a05e628e8e356b835b9b886eb5c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="build-a-python-and-postgresql-web-app-in-azure"></a>Creación de una aplicación web Python y PostgreSQL en Azure

> [!NOTE]
> En este artículo se implementa una aplicación en App Service en Windows. Para realizar implementaciones de App Service en _Linux_, consulte [Compilación de una aplicación web Docker Python con PostgreSQL en Azure](./containers/tutorial-docker-python-postgresql-app.md).
>

[Azure App Service](app-service-web-overview.md) proporciona un servicio de hospedaje web muy escalable y con aplicación de revisiones de un modo automático. Este tutorial muestra cómo crear una aplicación web Python básica en Azure. Dicha aplicación se conecta a una base de datos PostgreSQL. Cuando haya terminado, tendrá una aplicación Python Flask que se ejecuta en un App Service.

![Aplicación Python Flask en App Service en Linux](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una base de datos PostgreSQL en Azure
> * Conexión de una aplicación Python a MySQL
> * Implementación de la aplicación en Azure
> * Actualizar el modelo de datos y volver a implementar la aplicación
> * Administrar la aplicación en Azure Portal

También puede seguir los pasos de este tutorial en macOS. Las instrucciones de Linux y Windows son las mismas en la mayoría de los casos, pero en este tutorial no se detallan las diferencias.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial:

1. [Instalación de Git](https://git-scm.com/)
1. [Instalación de Python](https://www.python.org/downloads/)
1. [Instalación y ejecución de PostgreSQL](https://www.postgresql.org/download/)

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

Ejecute los siguientes comandos para clonar el repositorio de ejemplo y, a continuación, volver a la confirmación de la aplicación inicial (antes de `modelChange`).

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app
cd flask-postgresql-app
git revert modelChange --no-edit
```

Este repositorio de ejemplo contiene una aplicación [Flask](http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Ejecución de la aplicación

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

![Aplicación Python Flask que se ejecuta de forma local](./media/app-service-web-tutorial-python-postgresql/local-app.png)

La aplicación Flask de ejemplo almacena datos de usuario en la base de datos. Si el registro del usuario se realiza correctamente, la aplicación escribe datos en la base de datos de PostgreSQL local.

Para detener el servidor de Flask en cualquier momento, escriba Ctrl+C en el terminal. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgresql-in-azure"></a>Creación de PostgreSQL en Azure

En este paso, creará una base de datos PostgreSQL en Azure. Cuando la aplicación se implementa en Azure, utiliza esta base de datos en la nube.

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-postgresql-server"></a>Creación de un servidor PostgreSQL

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

### <a name="configure-server-firewall"></a>Configuración del firewall del servidor

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

### <a name="create-a-production-database-and-user"></a>Creación de una base de datos de producción y un usuario

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

### <a name="test-app-locally-with-azure-postgresql"></a>Prueba de la aplicación localmente con Azure PostgreSQL

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

![Aplicación Python Flask que se ejecuta de forma local](./media/app-service-web-tutorial-python-postgresql/local-app.png)

## <a name="deploy-to-azure"></a>Implementar en Azure

En este paso, se implementará la aplicación Python conectada a PostgreSQL en Azure App Service.

El repositorio Git ya contiene los archivos siguientes que se necesitan para ejecutar la aplicación web Flask en App Service:

- `.deployment`: especifica el script de implementación personalizado que se ejecutará.
- `deploy.cmd`: script de implementación. Es donde `pip install` se ejecuta.
- `web.config`: especifica el script del punto de entrada para que se ejecute en un `httpPlatformHandler` en IIS.
- `run_waitress_server.py`: script del punto de entrada. Inicia la aplicación web Flask en un servidor [`waitress`](https://docs.pylonsproject.org/projects/waitress).

### <a name="configure-a-deployment-user"></a>Configuración de un usuario de implementación

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creación de un plan de App Service

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Creación de una aplicación web

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-no-h.md)] 

### <a name="install-python"></a>Instalación de Python

En este paso, se instala Python 3.6.2 con [extensiones de sitio](https://www.siteextensions.net/packages?q=Tags%3A%22python%22) en App Service. Utilizará las credenciales configuradas en [Configuración de un usuario de implementación](#configure-a-deployment-user) para autenticarse con el punto de conexión REST.

En Cloud Shell, ejecute el comando siguiente para obtener la información de paquete de Python 3.6.2. Reemplace  *\<deployment_user>* por el nombre de usuario de implementación que ha configurado y  *\<app_name>* por el nombre de la aplicación. Cuando se le pida, use la contraseña de implementación que configuró.

```bash
packageinfo=$(curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/extensionfeed/python362x86)
```

En Cloud Shell, ejecute el comando siguiente para instalar el paquete Python. Reemplace  *\<deployment_user>* por el nombre de usuario de implementación que ha configurado y  *\<app_name>* por el nombre de la aplicación. Cuando se le pida, use la contraseña de implementación que configuró.

```bash
curl -X PUT -u <deployment_user> -H "Content-Type: application/json" -d '$packageinfo' https://<app_name>.scm.azurewebsites.net/api/siteextensions/python362x86
```

En la salida del comando, puede ver que Python está instalado en la ruta de acceso `D:\home\python362x86\python.exe`.

### <a name="configure-database-settings"></a>Configuración de la base de datos

En este mismo tutorial, ya ha definido variables de entorno para conectarse a su base de datos de PostgreSQL.

En App Service, las variables de entorno se establecen como _valores de aplicación_ mediante el comando [az appservice web config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

En el siguiente ejemplo se especifican los datos de conexión de la base de datos en forma de valores de configuración de aplicación. Reemplace  *\<app_name>* por el nombre de la aplicación y  *\<postgresql_name>* por el nombre del servidor PostgreSQL.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Inserción en Azure desde Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

### <a name="browse-to-the-azure-web-app"></a>Navegación hasta la aplicación web de Azure 

Vaya a la aplicación implementada mediante el explorador web. 

```bash 
http://<app_name>.azurewebsites.net 
```

Ve a los invitados que registró anteriormente y que se guardaron en la base de datos de producción de Azure en el paso anterior.

![Aplicación Python Flask que se ejecuta de forma local](./media/app-service-web-tutorial-python-postgresql/docker-app-deployed.png)

**¡Enhorabuena!** Va a ejecutar una aplicación Python Flask en Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Actualización del modelo de datos y nueva implementación

En este paso, se agrega el número de asistentes al registro de cada evento mediante la actualización del modelo `Guest`.

Extraiga del repositorio los archivos etiquetados por la confirmación de `modelChange`:

```bash
git checkout modelChange -- *
```

En esta versión ya se han realizado los cambios necesarios en las vistas, los controladores y el modelo. También incluye una migración de base de datos generada mediante *alembic* (`flask db migrate`). Puede ver los cambios en todos los archivos en la [vista de confirmación de GitHub](https://github.com/Azure-Samples/flask-postgresql-app/commit/139a53023688631c3cc2caefd70086f4722ecd7e).

### <a name="test-your-changes-locally"></a>Prueba de los cambios localmente

Ejecute los siguientes comandos para hacer pruebas locales de los cambios ejecutando el servidor de Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Vaya a http://localhost:5000 en el explorador para ver los cambios. Cree un registro de prueba.

![Aplicación Python Flask que se ejecuta de forma local](./media/app-service-web-tutorial-python-postgresql/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publicación de los cambios en Azure

En la ventana del terminal local, confirme todos los cambios en Git e inserte los cambios de código en Azure.

```bash
git add .
git commit -m "updated data model"
git push azure master
```

Vaya a la aplicación web de Azure y vuelva a probar la nueva funcionalidad. Cree otro registro de eventos.

```bash 
http://<app_name>.azurewebsites.net 
```

![Aplicación Python Flask en Azure App Service](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Administración de la aplicación web de Azure

Vaya a [Azure Portal](https://portal.azure.com) para ver la aplicación web que ha creado.

En el menú izquierdo, haga clic en **App Services**, a continuación, haga clic en el nombre de la aplicación web de Azure.

![Navegación desde el portal a la aplicación web de Azure](./media/app-service-web-tutorial-python-postgresql/app-resource.png)

De forma predeterminada, el portal muestra la página **Información general**. Esta página proporciona una visión del funcionamiento de la aplicación. En este caso, también puede realizar tareas de administración básicas como examinar, detener, iniciar, reiniciar y eliminar. Las pestañas del lado izquierdo de la página muestran las diferentes páginas de configuración que puede abrir.

![Página de App Service en Azure Portal](./media/app-service-web-tutorial-python-postgresql/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>pasos siguientes

Vaya al siguiente tutorial para aprender a asignar un nombre DNS personalizado a una aplicación web.

> [!div class="nextstepaction"]
> [Asignar un nombre DNS personalizado a Azure Web Apps](app-service-web-tutorial-custom-domain.md)
