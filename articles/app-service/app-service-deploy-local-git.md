---
title: Implementación de Git local a Azure App Service
description: Aprenda a habilitar la implementación de Git local en Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: 4cbe26055bdbf906223a327ab8cf94bebe9e7998
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="local-git-deployment-to-azure-app-service"></a>Implementación de Git local a Azure App Service

Esta guía muestra cómo implementar el código en [Azure App Service](app-service-web-overview.md) desde un repositorio Git en la máquina local.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

Para seguir los pasos de esta guía:

* [Instale Git](http://www.git-scm.com/downloads).
* Mantenga un repositorio Git local con el código que desea implementar.

Para usar un repositorio de ejemplo para continuar, ejecute el comando siguiente en la ventana del terminal local:

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

## <a name="prepare-your-repository"></a>Preparación del repositorio

Asegúrese de que la raíz del repositorio tiene los archivos correctos en el proyecto.

| Tiempo de ejecución | Archivos del directorio raíz |
|-|-|
| ASP.NET (solo Windows) | _*.sln_, _*.csproj_ o _default.aspx_ |
| ASP.NET Core | _*.sln_ o _*.csproj_ |
| PHP | _index.php_ |
| Ruby (solo Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ o _package.json_ con un script de inicio |
| Python (solo Windows) | _\*.py_, _requirements.txt_ o _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ o _iisstart.htm_ |
| Trabajos web | _\<nombre_de_trabajo>/run.\<extensión>_ en _App\_Data/jobs/continuous_ (para WebJobs continuos) o _App\_Data/jobs/triggered_ (para WebJobs desencadenados). Para más información, consulte la [documentación de WebJobs de Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Functions | Consulte [Implementación continua para Azure Functions](../azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Para personalizar la implementación puede incluir un archivo _.deployment_ en la raíz del repositorio. Para más información, consulte el artículo sobre la [personalización de las implementaciones](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) y el artículo sobre el [script de implementación personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Asegúrese de ejecutar `git commit` en todos los cambios que desea implementar.
>
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user.md)]

## <a name="enable-git-for-your-app"></a>Habilitación de Git para la aplicación

Para habilitar la implementación de Git para una aplicación de App Service existente, ejecute [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_local_git) en Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Para crear en su lugar una aplicación habilitada para Git, ejecute [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) en Cloud Shell con el parámetro `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

El comando `az webapp create` debe generar algo similar a la salida siguiente:

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

## <a name="deploy-your-project"></a>Implementación del proyecto

En la _ventana del terminal local_, agregue una instancia remota de Azure al repositorio de Git local. Reemplace _\<url>_ con la dirección URL del Git remoto que obtuvo en [Habilitación de Git para la aplicación](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

Inserte en la instancia remota de Azure para implementar la aplicación con el comando siguiente. Cuando se le pida una contraseña, asegúrese de escribir la que creó en [Configuración de un usuario de implementación](#configure-a-deployment-user), no la que usa para iniciar sesión en Azure Portal.

```bash
git push azure master
```

Es posible que vea la automatización específica para el entorno de tiempo de ejecución en la salida, como MSBuild para ASP.NET, `npm install` para Node.js y `pip install` para Python. 

Una vez que finalice la implementación, la aplicación en Azure Portal ahora debe tener un registro de `git push` en la página de **opciones de implementación**.

![](./media/app-service-deploy-local-git/deployment_history.png)

Vaya a la aplicación para comprobar que se implementó el contenido.

## <a name="troubleshooting"></a>solución de problemas

Estos son los errores o problemas comunes al usar Git para publicar en una aplicación de App Service en Azure:

---
**Síntoma**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Causa**: este error puede producirse si la aplicación no está en funcionamiento.

**Resolución**: inicie la aplicación en Azure Portal. La implementación de GIT no está disponible cuando la aplicación web está detenida.

---
**Síntoma**: `Couldn't resolve host 'hostname'`

**Causa**: este error puede producirse si la información de dirección introducida al crear el repositorio remoto "azure" no era correcta.

**Resolución**: use el comando `git remote -v` para obtener un listado de todos los remotos, junto con la dirección URL asociada. Compruebe que la URL para el repositorio correcto "azure" es correcta. Si lo necesita, suprima y vuelva a crear este repositorio remoto utilizando la URL correcta.

---
**Síntoma**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Causa**: este error puede producirse si no se especifica una rama durante `git push` o si no ha establecido el valor `push.default` en `.gitconfig`.

**Resolución**: vuelva a ejecutar `git push` y especifique la rama principal. Por ejemplo: 

```bash
git push azure master
```

---
**Síntoma**: `src refspec [branchname] does not match any.`

**Causa**: este error puede producirse si intenta insertar una rama que no es la principal en el repositorio remoto "azure".

**Resolución**: vuelva a ejecutar `git push` y especifique la rama principal. Por ejemplo: 

```bash
git push azure master
```

---
**Síntoma**: `RPC failed; result=22, HTTP code = 5xx.`

**Causa**: este error puede producirse si se trata de insertar un repositorio Git de gran tamaño a través de HTTPS.

**Resolución**: cambie la configuración de git en la máquina local para aumentar el tamaño de postBuffer.

```bash
git config --global http.postBuffer 524288000
```

---
**Síntoma**: `Error - Changes committed to remote repository but your web app not updated.`

**Causa**: este error puede ocurrir si se implementa una aplicación Node.js con un archivo _package.json_ que especifica los módulos requeridos adicionales.

**Resolución**: los mensajes adicionales con "npm ERR!" deben registrarse antes de este error y pueden proporcionar contexto adicional sobre el error. A continuación se indican las causas conocidas de este error y el mensaje 'npm ERR!' correspondiente:

* **Archivo package.json con estructura incorrecta**: npm ERR! No se pudieron leer las dependencias.
* **Módulo nativo que no tiene una distribución binaria para Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      OR
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Recursos adicionales

* [Documentación de Project Kudu](https://github.com/projectkudu/kudu/wiki)
* [Implementación continua en Azure App Service](app-service-continuous-deployment.md)
