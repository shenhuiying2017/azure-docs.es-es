---
title: "Implementar la aplicación en Azure App Service con un archivo ZIP | Microsoft Docs"
description: "Aprenda a implementar una aplicación en Azure App Service con un archivo ZIP."
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: cephalin;sisirap
ms.openlocfilehash: a0e4df0ef0a1c873f1efcac1d8dbfe3cada18218
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-file"></a>Implementar una aplicación en Azure App Service con un archivo ZIP

En este artículo se muestra cómo usar un archivo ZIP para implementar una aplicación web en [Azure App Service](app-service-web-overview.md). 

Esta implementación del archivo ZIP usa el mismo servicio de Kudu que permite realizar implementaciones basadas en integraciones continuas. Kudu admite la siguientes funcionalidades para implementar el archivo ZIP: 

- La eliminación de archivos restantes de una implementación anterior.
- La opción de activar el proceso de compilación predeterminado, en el que se incluye la restauración del paquete.
- La [personalización de la implementación](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), incluida la ejecución de scripts de implementación.  
- Registros de implementación. 

Para obtener más información, consulte la [documentación de Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

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

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="upload-zip-file-to-cloud-shell"></a>Carga del archivo ZIP en Cloud Shell

Si decide ejecutar la CLI de Azure desde el terminal local, omita este paso.

Siga estos pasos para cargar el archivo ZIP en Cloud Shell. 

[!INCLUDE [app-service-web-upload-zip.md](../../includes/app-service-web-upload-zip-no-h.md)]

Para obtener más información, consulte [Persistencia de archivos en Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

## <a name="deploy-zip-file"></a>Implementación de un archivo ZIP

Implemente el archivo ZIP cargado en la aplicación web mediante el comando [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip). Si decide no usar Cloud Shell, asegúrese de que su versión de CLI de Azure sea 2.0.21 o superior. Para ver qué versión tiene, ejecute el comando `az --version` en la ventana del terminal local. 

En el ejemplo siguiente se implementa el archivo ZIP que cargó. Cuando use una instalación local de la CLI de Azure, debe especificar la ruta de acceso al archivo ZIP local de `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Este comando implementa los archivos y directorios del archivo ZIP en la carpeta predeterminada de la aplicación App Service (`\home\site\wwwroot`) y reinicia la aplicación. Si se configura algún proceso de compilación personalizado adicional, se ejecuta también. Para obtener más información, consulte la [documentación de Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Para ver la lista de implementaciones de esta aplicación, debe usar las API de REST (consulte la siguiente sección). 

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="next-steps"></a>pasos siguientes

Para ver escenarios de implementación más avanzados, pruebe [Implementación en Azure con Git](app-service-deploy-local-git.md). La implementación basada en Git en Azure permite el control de versiones, la restauración de paquetes, MSBuild y mucho más.

## <a name="more-resources"></a>Más recursos

* [Kudu: realizar una implementación desde un archivo .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Credenciales de implementación de Azure App Service](app-service-deploy-ftp.md)
