---
title: Implementación de una aplicación en Azure App Service con un archivo ZIP o WAR | Microsoft Docs
description: Aprenda a implementar una aplicación en Azure App Service con un archivo ZIP (o un archivo WAR en el caso de desarrolladores de Java).
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: cephalin;sisirap
ms.openlocfilehash: 41fb529f6b4ae923f2920919306324c86a2baa45
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Implementación de una aplicación en Azure App Service con un archivo ZIP o WAR

En este artículo se muestra cómo usar un archivo ZIP o WAR para implementar una aplicación web en [Azure App Service](app-service-web-overview.md). 

Esta implementación del archivo ZIP usa el mismo servicio de Kudu que permite realizar implementaciones basadas en integraciones continuas. Kudu admite la siguientes funcionalidades para implementar el archivo ZIP: 

- La eliminación de archivos restantes de una implementación anterior.
- La opción de activar el proceso de compilación predeterminado, en el que se incluye la restauración del paquete.
- La [personalización de la implementación](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), incluida la ejecución de scripts de implementación.  
- Registros de implementación. 

Para obtener más información, consulte la [documentación de Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

La implementación del archivo WAR implementa su archivo [WAR](https://wikipedia.org/wiki/WAR_(file_format)) en Azure App Service para ejecutar la aplicación web de Java. Consulte [Implementación de un archivo WAR](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>requisitos previos

Para completar los pasos de este artículo:

* [Cree una aplicación de App Service](/azure/app-service/) o use alguna aplicación que haya creado para otro tutorial.

## <a name="create-a-project-zip-file"></a>Creación de un archivo ZIP de proyecto

>[!NOTE]
> Si descargó los archivos en un archivo ZIP, extráigalos primero. Por ejemplo, si descargó un archivo ZIP de GitHub, no puede implementar ese archivo tal cual. GitHub agrega directorios anidados adicionales que no funcionan con App Service. 
>

En la ventana de un terminal local, navegue hasta el directorio raíz del proyecto de la aplicación. 

Este directorio debería contener el archivo de entrada para la aplicación web como, por ejemplo, _index.html_, _index.php_ y _app.js_. También puede contener archivos de administración de paquetes como _project.json_, _composer.json_, _package.json_, _bower.json_ y _requirements.txt_.

Cree un archivo ZIP con todo el contenido del proyecto. El siguiente comando usa la herramienta predeterminada de su terminal:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="deploy-zip-file-with-azure-cli"></a>Implementación del archivo ZIP con la CLI de Azure

Asegúrese de que su versión de la CLI de Azure sea la 2.0.21 o posterior. Para ver qué versión tiene, ejecute el comando `az --version` en la ventana del terminal.

Implemente el archivo ZIP cargado en la aplicación web mediante el comando [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip).  

En el ejemplo siguiente se implementa el archivo ZIP que cargó. Cuando use una instalación local de la CLI de Azure, debe especificar la ruta de acceso al archivo ZIP local de `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Este comando implementa los archivos y directorios del archivo ZIP en la carpeta predeterminada de la aplicación App Service (`\home\site\wwwroot`) y reinicia la aplicación. Si se configura algún proceso de compilación personalizado adicional, se ejecuta también. Para obtener más información, consulte la [documentación de Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Implementación de un archivo WAR

Para implementar un archivo WAR en App Service, envíe una solicitud POST a https://<app_name>.scm.azurewebsites.net/api/wardeploy. La solicitud POST debe contener el archivo .war en el cuerpo del mensaje. Las credenciales de implementación de la aplicación se proporcionan en la solicitud mediante la autenticación de HTTP BASIC. 

Para la autenticación básica HTTP, necesita las credenciales de implementación de App Service. Para ver cómo establecer sus credenciales de implementación, consulte [Establecimiento y restablecimiento de credenciales de nivel de usuario](app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>Con cURL

En el ejemplo siguiente se usa la herramienta cURL para implementar un archivo .war. Reemplace los marcadores de posición `<username>`, `<war_file_path>` y `<app_name>`. Cuando cURL se lo solicite, escriba la contraseña.

```bash
curl -X POST -u <username> --data-binary @"<war_file_path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Con PowerShell

En el ejemplo siguiente se usa [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) para enviar una solicitud que contiene el archivo .war. Reemplace los marcadores de posición `<deployment_user>`, `<deployment_password>`, `<zip_file_path>` y `<app_name>`.

```PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<war_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/wardeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

## <a name="next-steps"></a>Pasos siguientes

Para ver escenarios de implementación más avanzados, pruebe [Implementación en Azure con Git](app-service-deploy-local-git.md). La implementación basada en Git en Azure permite el control de versiones, la restauración de paquetes, MSBuild y mucho más.

## <a name="more-resources"></a>Más recursos

* [Kudu: realizar una implementación desde un archivo .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Credenciales de implementación de Azure App Service](app-service-deploy-ftp.md)
