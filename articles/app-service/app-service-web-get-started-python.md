---
title: "Creación de una aplicación web de Python en Azure | Microsoft Docs"
description: "Implementación de su primera aplicación Hola mundo de Python en Azure App Service Web Apps en cuestión de minutos."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/26/2017
ms.author: cephalin;cfowler
ms.custom: mvc, devcenter
ms.openlocfilehash: a17ff3ca73a824f4aa4b45a7cae9428a0fbc96d4
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="create-a-python-web-app-in-azure"></a>Creación de una aplicación web de Python en Azure

[Azure Web Apps](app-service-web-overview.md) proporciona un servicio de hospedaje web muy escalable y con aplicación de revisiones de un modo automático.  Esta guía de inicio rápido le guía a través del desarrollo e implementación de una aplicación de Python en Azure Web Apps. Se crea la aplicación web con la [CLI de Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) y se usa Git para implementar el código Python de ejemplo en la aplicación web.

![Aplicación de ejemplo que se ejecuta en Azure](media/app-service-web-get-started-python/hello-world-in-browser.png)

Estos pasos se pueden realizar con un equipo Mac, Windows o Linux. Una vez instalados los requisitos previos, tardará aproximadamente cinco minutos en completar los pasos.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

* <a href="https://git-scm.com/" target="_blank">Instalación de Git</a>
* <a href="https://www.python.org/downloads/" target="_blank">Instalación de Python</a>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Descarga del ejemplo

En una ventana de terminal, ejecute el siguiente comando para clonar el repositorio de la aplicación de ejemplo en el equipo local.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Cambie al directorio que contiene el código de ejemplo.

```bash
cd python-docs-hello-world
```

## <a name="run-the-app-locally"></a>Ejecución de la aplicación de forma local

Instale los paquetes necesarios mediante `pip`.

```bash
pip install -r requirements.txt
```

Ejecute la aplicación localmente abriendo una ventana de terminal y utilizando el comando `Python` para iniciar el servidor web de Python integrado.

```bash
python main.py
```

Abra un explorador web y vaya a la aplicación de ejemplo en `http://localhost:5000`.

Puede ver el mensaje **Hola mundo** de la aplicación de ejemplo que aparece en la página.

![Aplicación de ejemplo que se ejecuta localmente](media/app-service-web-get-started-python/localhost-hello-world-in-browser.png)

En la ventana de terminal, presione **Ctrl + C** para salir del servidor web.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)]

## <a name="create-a-web-app"></a>Creación de una aplicación web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-python-no-h.md)]

Vaya a la aplicación web recién creada. Reemplace  _&lt;nombre de aplicación>_ por un nombre de aplicación único.

```bash
http://<app name>.azurewebsites.net
```

![Página de la aplicación web vacía](media/app-service-web-get-started-python/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)]

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

El código de ejemplo de Python se está ejecutando en una aplicación web de Azure App Service.

![Aplicación de ejemplo que se ejecuta en Azure](media/app-service-web-get-started-python/hello-world-in-browser.png)

**¡Enhorabuena!** Ha implementado la primera aplicación de Python en App Service.

## <a name="update-and-redeploy-the-code"></a>Actualización del código y nueva implementación

Con un editor de texto local, abra el archivo `main.py` de la aplicación de Python y realice un pequeño cambio en el texto situado junto a la instrucción `return`:

```python
return 'Hello, Azure!'
```

En la ventana del terminal local, confirme los cambios en Git e inserte los cambios de código en Azure.

```bash
git commit -am "updated output"
git push azure master
```

Una vez que la implementación haya finalizado, vuelva a la ventana del explorador que abrió en el paso [Navegación hasta la aplicación](#browse-to-the-app) y actualice la página.

![Aplicación de ejemplo actualizada que se ejecuta en Azure](media/app-service-web-get-started-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Administración de la nueva aplicación web de Azure

Vaya a <a href="https://portal.azure.com" target="_blank">Azure Portal</a> para administrar la aplicación web que ha creado.

En el menú izquierdo, haga clic en **App Services** y, a continuación, haga clic en el nombre de la aplicación web de Azure.

![Navegación desde el portal a la aplicación web de Azure](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Podrá ver la página de información general de la aplicación web. En este caso, puede realizar tareas de administración básicas como examinar, detener, iniciar, reiniciar y eliminar.

![Hoja de App Service en Azure Portal](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

El menú izquierdo proporciona distintas páginas para configurar la aplicación.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Asignar un nombre DNS personalizado a Azure Web Apps](app-service-web-tutorial-custom-domain.md)
