---
title: "Implementación de su aplicación en Azure App Service mediante FTP/S | Microsoft Docs"
description: "Aprenda a implementar la aplicación en Azure App Service mediante FTP o FTPS."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2016
ms.author: cephalin;dariac
ms.translationtype: Human Translation
ms.sourcegitcommit: 816113f7635a003e22a5172113e5039dbcc1ceac
ms.openlocfilehash: 5dc546849bd02ccf4d02f3e6363a3e2fc3898259
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017


---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Implementación de la aplicación en Azure App Service mediante FTP/S
En este artículo se muestra cómo usar FTP o FTPS para implementar la aplicación web, el back-end de la aplicación móvil o la aplicación de API en [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

El punto de conexión FTP/S de la aplicación ya está activo. No se necesita ninguna configuración para habilitar la implementación de FTP/S. 

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>Paso1: Configurar credenciales de implementación

Para acceder al servidor FTP de la aplicación, necesita en primer lugar credenciales de implementación. 

Para establecer o restablecer las credenciales de implementación, consulte [Credenciales de implementación de Azure App Service](app-service-deployment-credentials.md). Este tutorial muestra el uso de credenciales de nivel de usuario.

## <a name="step-2-get-ftp-connection-information"></a>Paso 2: Obtener la información de conexión para FTP

1. En [Azure Portal](https://portal.azure.com), abra la [hoja de recursos](../azure-resource-manager/resource-group-portal.md#manage-resources) de la aplicación.
2. Seleccione **Información general** en el menú izquierdo y, a continuación, compruebe los valores de **FTP/usuario de implementación**, **Nombre del host FTP** y **Nombre del host FTPS**. 

    ![Información de conexión FTP](./media/web-sites-deploy/FTP-Connection-Info.PNG)

    > [!NOTE]
    > El valor de usuario **FTP/usuario de implementación** tal como aparece en Azure Portal, incluido el nombre de la aplicación a fin de proporcionar el contexto adecuado para el servidor FTP.
    > Puede encontrar la misma información si selecciona **Propiedades** en el menú izquierdo. 
    >
    > Además, nunca se muestra la contraseña de la implementación. Si olvida la contraseña de la implementación, vuelva al [Paso 1](#step1) y restablézcala.
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>Paso 3: Implementar archivos en Azure

1. Desde el cliente de FTP ([Visual Studio](https://www.visualstudio.com/vs/community/), [FileZilla](https://filezilla-project.org/download.php?type=client), etc), use la información de conexión recopilada para conectarse a la aplicación.
3. Copie los archivos y la estructura de directorio correspondiente al directorio [**/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)en Azure (o el directorio **/site/wwwroot/App_Data/Jobs/** para WebJobs).
4. Vaya a la dirección URL de la aplicación para comprobar que la aplicación se está ejecutando correctamente. 

> [!NOTE] 
> A diferencia de [las implementaciones basadas en Git](app-service-deploy-local-git.md), la implementación de FTP no es compatible con las automatizaciones de implementación siguientes: 
>
> - restauración de dependencias (como, por ejemplo, automatizaciones de NuGet, NPM, PIP y Composer)
> - compilación de archivos binarios de .NET
> - generación del archivo web.config (aquí se muestra un [ejemplo de Node.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Debe restaurar, compilar y generar estos archivos necesarios de forma manual en la máquina local e implementarlos junto con la aplicación.
>
>

## <a name="next-steps"></a>Pasos siguientes

Para ver escenarios de implementación más avanzados, pruebe [Implementación en Azure con Git](app-service-deploy-local-git.md). La implementación basada en Git en Azure permite el control de versiones, la restauración de paquetes, MSBuild y mucho más.

## <a name="more-resources"></a>Más recursos

* <seg>
  [Creación de un sitio web PHP-MySQL e implementación con FTP](web-sites-php-mysql-deploy-use-ftp.md).</seg>
* [Credenciales de implementación de Azure App Service](app-service-deploy-ftp.md)

