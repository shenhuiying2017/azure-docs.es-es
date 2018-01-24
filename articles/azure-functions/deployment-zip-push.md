---
title: "Implementación para insertar un archivo ZIP en Azure Functions | Microsoft Docs"
description: "Use las funciones de implementación de archivos ZIP del servicio de implementación de Kudu para publicar sus instancias de Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/06/2017
ms.author: glenga
ms.openlocfilehash: faddb73522200f60f18294dc43e8d235943f8bbb
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="zip-push-deployment-for-azure-functions"></a>Implementación para insertar archivos ZIP en Azure Functions 
En este artículo se describe la manera de implementar los archivos de proyecto de la aplicación de función en Azure desde un archivo ZIP (comprimido). Aprenda a realizar una implementación de inserción mediante el uso de la CLI de Azure y con las API de REST. 

Azure Functions tiene toda una gama de opciones de implementación e integración continuas que se proporcionan con Azure App Service. Para más información, vea [Implementación continua para Azure Functions](functions-continuous-deployment.md). 

Para realizar iteraciones más rápidas durante el desarrollo, a menudo resulta más sencillo implementar directamente los archivos de proyecto de la aplicación de función desde un archivo .zip comprimido. Esta implementación del archivo .zip usa el servicio Kudu que permite realizar implementaciones basadas en integraciones continuas, entre las que se incluye:

+ La eliminación de archivos que se omitieron en implementaciones anteriores.
+ La personalización de la implementación, incluyendo la ejecución de scripts de implementación.
+ Registros de implementación.
+ Los desencadenadores de la función de sincronización de una aplicación de función en un [plan de consumo](functions-scale.md).

Para obtener más información, consulte la [referencia de implementación para la inserción del archivo .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

## <a name="deployment-zip-file-requirements"></a>Requisitos de los .zip de implementación
El archivo .zip que use para la implementación de inserción debe contener todos los archivos del proyecto en la aplicación de función, incluyendo el código de función. 

>[!IMPORTANT]
> Cuando realice la implementación de inserción del archivo .zip, los archivos de cualquier otra implementación existente que no se encuentren en el archivo .zip se eliminarán de la aplicación de función.  

### <a name="function-app-folder-structure"></a>Estructura de carpetas de la aplicación de función

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

### <a name="download-your-function-app-project"></a>Descarga del proyecto de aplicación de función

Si usa un equipo local para desarrollar elementos, verá que es fácil crear un archivo .zip de la carpeta del proyecto de la aplicación de función en el equipo de desarrollo. 

Sin embargo, es posible crear las funciones mediante el editor en Azure Portal. Para descargar el proyecto de aplicación de función del portal: 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a la aplicación de función.

2. En la pestaña **Introducción**, seleccione **Descargar contenido de la aplicación**. Seleccione las opciones de descarga y, a continuación, seleccione **Descargar**.     

    ![Descargue el proyecto de aplicación de función.](./media/deployment-zip-push/download-project.png)

El archivo .zip descargado tiene el formato correcto para volver a publicarlo en la aplicación de función mediante la implementación de inserción de archivos .zip.

Igualmente, también puede descargar un archivo .zip desde un repositorio de GitHub. Tenga en cuenta que cuando se descarga un repositorio de GitHub como un archivo .zip, GitHub agrega un nivel de carpeta adicional para la rama. Este nivel de carpetas adicional significa que no puede implementar el archivo .zip directamente ya que se descargó desde GitHub. Si usa un repositorio de GitHub para mantener la aplicación de función, debe usar la [integración continua](functions-continuous-deployment.md) para implementar la aplicación.  

## <a name="cli"></a>Implementación con la CLI de Azure

Puede usar la CLI de Azure para desencadenar una implementación de inserción. Para implementar el archivo .zip en la aplicación de función, use el comando [az functionapp deployment source config-zip](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip). Para usar este comando, debe usar la versión 2.0.21 o posterior de la CLI de Azure. Para ver qué versión de CLI de Azure está usando, use el comando `az --version`.

En el siguiente comando, reemplace el marcador de posición `<zip_file_path>` por la ruta de acceso a la ubicación del archivo .zip. Reemplace también `<app_name>` por el nombre exclusivo de la aplicación de función. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```
Este comando implementa los archivos de proyecto del archivo .zip descargado en la aplicación de función en Azure. A continuación, se reinicia la aplicación. Para ver la lista de implementaciones de esta aplicación de función, debe usar las API de REST.

Cuando se usa la CLI de Azure en el equipo local, `<zip_file_path>` es la ruta de acceso al archivo .zip en el equipo. También puede ejecutar la CLI de Azure en [Azure Cloud Shell](../cloud-shell/overview.md). Cuando use Cloud Shell, recuerde que debe cargar primero el archivo .zip de implementación en la cuenta de Azure Files que esté asociada con Cloud Shell. En ese caso, `<zip_file_path>` será la ubicación de almacenamiento que utiliza la cuenta de Cloud Shell. Para obtener más información, consulte [Persistencia de archivos en Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).


[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Implementación continua para Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
