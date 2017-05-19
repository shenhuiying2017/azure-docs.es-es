---
title: "Creación de su primera aplicación web de Python en Azure en cinco minutos | Microsoft Docs"
description: "Implementación de su primera aplicación Hola mundo de Python en la aplicación web de App Service en cuestión de minutos."
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 2916ee6ba4753efdb8823f93c951a4f678b08ae4
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017


---
# <a name="create-a-python-application-on-web-app"></a>Crear una aplicación de Python en una aplicación web

Este tutorial de inicio rápido le guía a través del desarrollo e implementación de una aplicación de Python en Azure. La aplicación se ejecutará con Azure App Service, y se creará y configurará una nueva aplicación web en ella mediante la CLI de Azure. A continuación, se usará el repositorio Git para implementar nuestra aplicación de Python en Azure.

![hello-world-in-browser](media/app-service-web-get-started-python/hello-world-in-browser.png)

Estos pasos se pueden realizar con un equipo Mac, Windows o Linux. Completar todos los pasos siguientes le llevará 5 minutos aproximadamente.

## <a name="prerequisites"></a>Requisitos previos

Antes de ejecutar este ejemplo, instale los siguientes requisitos previos localmente:

1. [Descarga e instalación de Git](https://git-scm.com/)
1. [Descarga e instalación de Python](https://www.python.org/downloads/)
1. Descarga e instalación de la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="download-the-sample"></a>Descarga del ejemplo

Clone el repositorio de la aplicación de ejemplo de Hola mundo en la máquina local.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

> [!TIP]
> También puede [descargar el ejemplo](https://github.com/Azure-Samples/Python-docs-hello-world/archive/master.zip) como un archivo zip y extraerlo.

Cambie al directorio que contiene el código de ejemplo.

```bash
cd Python-docs-hello-world
```

## <a name="run-the-app-locally"></a>Ejecución de la aplicación de forma local

Ejecute la aplicación localmente abriendo una ventana de terminal y utilizando la línea de comandos `Python` para que el ejemplo inicie el servidor web de Python integrado.

```bash
python main.py
```

Abra el explorador y navegue al ejemplo.

```bash
http://localhost:5000
```

Puede ver el mensaje **Hola mundo** de la aplicación de ejemplo que aparece en la página.

![localhost-hello-world-in-browser](media/app-service-web-get-started-python/localhost-hello-world-in-browser.png)

En la ventana de terminal, presione **Ctrl + C** para salir del servidor web.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Ahora vamos a usar la CLI de Azure 2.0 en una ventana de terminal para crear los recursos necesarios para hospedar nuestra aplicación de Python en Azure. Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla.

```azurecli
az login
```

## <a name="configure-a-deployment-user"></a>Configuración de un usuario de implementación

Para FTP y Git local es necesario que haya un usuario de implementación configurado en el servidor para autenticar la implementación. Crear un usuario de implementación es una configuración que solo se realiza una vez. Tome nota del nombre de usuario y la contraseña que se van a utilizar en un paso que aparecerá a continuación.

> [!NOTE]
> Se requiere un usuario de implementación para la implementación de FTP y Local Git en una aplicación web.
> Los valores de `username` y `password` son lo de la cuenta; por lo tanto, son diferentes de las credenciales de la suscripción de Azure. **Es necesario crear estas credenciales solo una vez**.
>

Use el comando [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) para crear las credenciales de nivel de cuenta.

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran recursos de Azure como aplicaciones web, bases de datos y cuentas de almacenamiento.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service"></a>Creación de una instancia de Azure App Service

Cree un plan de App Service con el comando [az appservice plan create](/cli/azure/appservice/plan#create).

> [!NOTE]
> Un plan de App Service representa la colección de recursos físicos que se utiliza para hospedar las aplicaciones. Todas las aplicaciones asignadas a un plan de App Service comparten los recursos definidos por él, lo que permite ahorrar costos al hospedar varias aplicaciones.
>
> Los planes de App Service definen lo siguiente:
> * Región (Europa del Norte, este de EE. UU., Sudeste Asiático)
> * Tamaño de la instancia (pequeño, mediano, grande)
> * Recuento de escala (una, dos, tres instancias, etc.)
> * SKU (Gratis, Compartido, Básico, Estándar y Premium)
>

En el siguiente ejemplo se crea un plan de App Service denominado `quickStartPlan` con el plan de tarifa **Gratis**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

Cuando se ha creado el plan de App Service, la CLI de Azure muestra información similar al ejemplo siguiente.

```json
{
"appServicePlanName": "quickStartPlan",
"geoRegion": "North Europe",
"id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
"kind": "app",
"location": "North Europe",
"maximumNumberOfWorkers": 1,
"name": "quickStartPlan",
"provisioningState": "Succeeded",
"resourceGroup": "myResourceGroup",
"sku": {
  "capacity": 0,
  "family": "F",
  "name": "F1",
  "size": "F1",
  "tier": "Free"
},
"status": "Ready",
"type": "Microsoft.Web/serverfarms",
}
```

## <a name="create-a-web-app"></a>Creación de una aplicación web

Ahora que ha creado un plan de App Service, cree una aplicación web dentro del plan de App Service `quickStartPlan`. La aplicación web nos proporciona un espacio de hospedaje para implementar el código, así como una dirección URL para que podamos ver la aplicación implementada. Use el comando [az appservice web create](/cli/azure/appservice/web#create) para crear la aplicación web.

En el comando siguiente, sustituya su nombre de aplicación único donde vea el marcador de posición `<app_name>`. El marcador de posición `<app_name>` se usará como el sitio DNS predeterminado para la aplicación web y, por ello, el nombre debe ser único entre todas las aplicaciones de Azure. Más adelante puede asignar cualquier entrada de DNS personalizada a la aplicación web antes de exponerla a los usuarios.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

Cuando se ha creado la aplicación web, la CLI de Azure muestra información similar al siguiente ejemplo.

```json
{
  "clientAffinityEnabled": true,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "hostNames": [
    "<app_name>.azurewebsites.net"
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>",
  "kind": "app",
  "location": "North Europe",
  "outboundIpAddresses": "13.69.190.80,13.69.191.239,13.69.186.193,13.69.187.34",
  "resourceGroup": "myResourceGroup",
  "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "state": "Running",
  "type": "Microsoft.Web/sites",
}
```

Vaya al sitio para ver la aplicación web recién creada.

```bash
http://<app_name>.azurewebsites.net
```

![app-service-web-service-created](media/app-service-web-get-started-python/app-service-web-service-created.png)

Ya hemos creado una nueva aplicación web vacía en Azure. Vamos a configurar ahora nuestra aplicación web para usar Python e implementar la aplicación en ella.

## <a name="configure-to-use-python"></a>Configuración para usar Python

Use el comando [az appservice web config update](/cli/azure/app-service/web/config#update) para configurar la aplicación web para que use la versión `3.4` de Python.

> [!TIP]
> La configuración de la versión de Python de esta manera usa un contenedor predeterminado proporcionado por la plataforma. Si desea utilizar su propio contenedor, consulte la referencia de la CLI del comando [az appservice web config container update](https://docs.microsoft.com/cli/azure/appservice/web/config/container#update).

```azurecli
az appservice web config update --python-version 3.4 --name <app-name> --resource-group myResourceGroup
```

## <a name="configure-local-git-deployment"></a>Configuración de la implementación de Git local

Puede implementar la aplicación web de varias formas incluyendo FTP, Local Git, así como GitHub, Visual Studio Team Services y Bitbucket.

Use el comando [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) para configurar el acceso de Local Git a la aplicación web.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Copie la salida del terminal ya que se usará en el paso siguiente.

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

## <a name="push-to-azure-from-git"></a>Inserción en Azure desde Git

Agregue una instancia remota de Azure en el repositorio de Git local.

```bash
git remote add azure <paste-previous-command-output-here>
```

Inserte en la instancia remota de Azure para implementar la aplicación. Le pedirá la contraseña que proporcionó anteriormente como parte de la creación del usuario de implementación.

```azurecli
git push azure master
```

Durante la implementación, Azure App Service comunicará su progreso a Git.

```bash
Counting objects: 18, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (16/16), done.
Writing objects: 100% (18/18), 4.31 KiB | 0 bytes/s, done.
Total 18 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '44e74fe7dd'.
remote: Generating deployment script.
remote: Generating deployment script for python Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling python deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'main.py'
remote: Copying file: 'README.md'
remote: Copying file: 'requirements.txt'
remote: Copying file: 'virtualenv_proxy.py'
remote: Copying file: 'web.2.7.config'
remote: Copying file: 'web.3.4.config'
remote: Detected requirements.txt.  You can skip Python specific steps with a .skipPythonDeployment file.
remote: Detecting Python runtime from site configuration
remote: Detected python-3.4
remote: Creating python-3.4 virtual environment.
remote: .................................
remote: Pip install requirements.
remote: Successfully installed Flask click itsdangerous Jinja2 Werkzeug MarkupSafe
remote: Cleaning up...
remote: .
remote: Overwriting web.config with web.3.4.config
remote:         1 file(s) copied.
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Navegación hasta la aplicación

Vaya a la aplicación implementada mediante el explorador web.

```bash
http://<app_name>.azurewebsites.net
```

Esta vez, la página que muestra el mensaje de Hola mundo se ejecuta con nuestro código de Python que se ejecuta como una aplicación web de Azure App Service.

![hello-world-in-browser](media/app-service-web-get-started-python/hello-world-in-browser.png)

## <a name="updating-and-deploying-the-code"></a>Actualización e implementación del código

Con un editor de texto local, abra el archivo `main.py` dentro de la aplicación de Python y realice un pequeño cambio en el texto dentro de la cadena situada junto a la instrucción `return`:

```python
return 'Hello, Azure!'
```

Confirme los cambios en Git y, a continuación, inserte los cambios de código en Azure.

```bash
git commit -am "updated output"
git push azure master
```

Una vez que la implementación haya finalizado, vuelva a cambiar la ventana del explorador que se abrió y actualice la vista.

![hello-azure-in-browser](media/app-service-web-get-started-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Administración de la nueva aplicación web de Azure

Vaya a Azure Portal para echar un vistazo a la aplicación web que acaba de crear.

Para ello, inicie sesión en [https://portal.azure.com/](https://portal.azure.com).

En el menú izquierdo, haga clic en **App Services**, a continuación, haga clic en el nombre de la aplicación web de Azure.

![Navegación desde el portal a la aplicación web de Azure](./media/app-service-web-get-started-python/app-service-list.png)

Ha llegado a la _hoja_ de su aplicación web (una página del portal que se abre horizontalmente).

De forma predeterminada, la hoja de la aplicación web muestra la página de **introducción**. Esta página proporciona una visión del funcionamiento de la aplicación. En este caso, también puede realizar tareas de administración básicas como examinar, detener, iniciar, reiniciar y eliminar. Las pestañas del lado izquierdo de la hoja muestran las diferentes páginas de configuración que puede abrir.

![Hoja de App Service en Azure Portal](media/app-service-web-get-started-python/app-service-detail.png)

Estas pestañas de la hoja muestran las muchas y excepcionales características que puede agregar a la aplicación web. La lista siguiente proporciona solo algunas de las posibilidades:

* Asignación de un nombre DNS personalizado
* Enlace de un certificado SSL personalizado
* Configuración de la implementación continua
* Escalado vertical y horizontal
* Adición de la autenticación de usuarios

**¡Enhorabuena!** Ha implementado la primera aplicación de Python en App Service.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Scripts de ejemplo de la CLI para Web Apps](app-service-cli-samples.md)

