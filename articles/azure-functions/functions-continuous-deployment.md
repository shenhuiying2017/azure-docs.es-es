---
title: "Implementación continua para Azure Functions | Microsoft Docs"
description: "Utilice las funciones de implementación continua de Azure App Service para publicar Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: 35a0b0faa61cf4b42ba1d8696c85f5724ff73f23
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="continuous-deployment-for-azure-functions"></a>Implementación continua para Azure Functions
Azure Functions facilita la implementación de Function App mediante la integración continua de App Service. Functions se integra con BitBucket, Dropbox, GitHub y Visual Studio Team Services (VSTS). Esto permite un flujo de trabajo en el que las actualizaciones del código de la función realizadas mediante uno de estos servicios integrados desencadenan la implementación en Azure. Si no está familiarizado con Azure Functions, consulte primero [Información general sobre Azure Functions](functions-overview.md).

La implementación continua representa una buena opción para los proyectos donde se integran contribuciones diversas y frecuentes. También le permite mantener el control de código fuente en el código de las funciones. Actualmente se admiten los siguientes orígenes de implementación:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Repositorio externo (Git o Mercurial)
* [Repositorio local de GIT](../app-service/app-service-deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Visual Studio Team Services](https://www.visualstudio.com/team-services/)

Las implementaciones se configuran por Function App. Después de habilitada la implementación continua, el acceso al código de la función en el portal está establecido en acceso *de solo lectura*.

## <a name="continuous-deployment-requirements"></a>Requisitos de implementación continua

Para configurar la implementación continua debe tener configurados el origen de implementación y el código de las funciones del origen de implementación. En una implementación determinada de una aplicación de función, cada función se encuentra en un subdirectorio con nombre, donde el nombre del directorio es el nombre de la función.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="set-up-continuous-deployment"></a>Configurar la implementación continua
Use este procedimiento para configurar la implementación continua para una Function App existente. Estos pasos muestran la integración con un repositorio de GitHub, aunque se aplican pasos similares para Visual Studio Team Services u otros servicios de implementación.

1. En la Function App de [Azure Portal](https://portal.azure.com), haga clic en **Características de la plataforma** y en **Opciones de implementación**. 
   
    ![Configurar implementación continua](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Luego, en la hoja **Implementación**, haga clic en **Configurar**.
 
    ![Configurar implementación continua](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. En la hoja **Origen de implementación**, haga clic en **Elegir origen**, rellene la información del origen de implementación elegido y haga clic en **Aceptar**.
   
    ![Elegir origen de la implementación](./media/functions-continuous-deployment/choose-deployment-source.png)

Después de configurar la implementación continua, se copian todos los cambios de archivos del origen de implementación en la Function App y se desencadena una implementación completa del sitio. Cuando se actualizan los archivos del origen se vuelve a implementar el sitio.

## <a name="deployment-options"></a>Opciones de implementación

Los siguientes son algunos escenarios típicos de implementación:

- [Creación de una implementación de ensayo](#staging)
- [Movimiento de funciones existentes para la implementación continua](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Creación de una implementación de ensayo

Las aplicaciones de función todavía no admiten espacios de implementación. Sin embargo, todavía puede administrar implementaciones de ensayo y producción independientes mediante la integración continua.

El proceso para configurar y trabajar con una implementación de ensayo tiene normalmente este aspecto:

1. Cree dos aplicaciones de función en su suscripción, una para el código de producción y otra para el ensayo. 

2. Cree un origen de la implementación, si aún no tiene uno. En este ejemplo se usa [GitHub].

3. Para la Function App de producción, complete los pasos anteriores descritos en **Configuración de la implementación continua** y establezca la rama de implementación en la rama principal del repositorio de GitHub.
   
    ![Elegir rama de la implementación](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Repita este paso para la aplicación de función de ensayo, pero esta vez, seleccione la rama de ensayo en el repositorio de GitHub. Si el origen de la implementación no admite la bifurcación, utilice una carpeta diferente.
    
5. Realice actualizaciones en el código en la rama o carpeta de ensayo y, a continuación, compruebe que esos cambios se reflejan en la implementación de ensayo.

6. Después de la prueba, combine los cambios de la rama de ensayo en la rama principal. Esta combinación desencadena la implementación en la Function App de producción. Si el origen de la implementación no admite ramas, sobrescriba los archivos de la carpeta de producción con los archivos de la carpeta de ensayo.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Movimiento de funciones existentes para la implementación continua
Si dispone de funciones existentes que ha creado y mantenido en el portal, es necesario descargar los archivos de código de la función existente mediante FTP o mediante el repositorio de Git local antes de poder configurar la implementación continua como se ha descrito anteriormente. Puede hacerlo en la configuración de App Service de la aplicación de función. Una vez descargados los archivos, puede cargarlos en el origen de la implementación continua que haya seleccionado.

> [!NOTE]
> Después de configurar la integración continua, ya no podrá modificar los archivos de origen en el portal de Funciones.

- [Configuración de las credenciales de implementación](#credentials)
- [Descarga de archivos mediante FTP](#downftp)
- [Descarga de archivos mediante el repositorio local de Git](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>Configuración de las credenciales de implementación
Para poder descargar archivos desde la Function App con FTP o el repositorio local de Git, debe configurar las credenciales para acceder al sitio. Las credenciales se establecen en el nivel de la aplicación de función. Use los pasos siguientes para establecer las credenciales de implementación en Azure Portal:

1. En la Function App de [Azure Portal](https://portal.azure.com), haga clic en **Características de la plataforma** y en **Credenciales de implementación**.
   
    ![Configurar credenciales de implementación locales](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Escriba un nombre de usuario y una contraseña y haga clic en **Guardar**. Ahora puede usar estas credenciales para tener acceso a la aplicación de función mediante FTP o desde el repositorio de Git integrado.

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>Descarga de archivos mediante FTP

1. En la Function App de [Azure Portal](https://portal.azure.com), haga clic en **Características de la plataforma** y en **Propiedades** y luego copie los valores de **FTP/usuario de implementación**, **Nombre del host FTP** y **Nombre del host FTPS**.  

    **FTP/usuario de implementación** debe escribirse tal como aparece en el portal, incluido el nombre de la aplicación, a fin de proporcionar el contexto adecuado para el servidor FTP.
   
    ![Obtener la información de implementación](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. Desde el cliente de FTP, use la información de conexión recopilada para conectarse a la aplicación y descargar los archivos de origen de las funciones.

<a name="downgit"></a>
#### <a name="how-to-download-files-using-a-local-git-repository"></a>Descarga de archivos mediante el repositorio local de Git

1. En la Function App de [Azure Portal](https://portal.azure.com), haga clic en **Características de la plataforma** y en **Opciones de implementación**. 
   
    ![Configurar implementación continua](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Luego, en la hoja **Implementación**, haga clic en **Configurar**.
 
    ![Configurar implementación continua](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. En la hoja **Origen de implementación**, haga clic en **Repositorio de Git local** y luego en **Aceptar**.

3. En **Características de la plataforma**, haga clic en **Propiedades** y anote el valor de la dirección URL de Git. 
   
    ![Configurar implementación continua](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Clone el repositorio en la máquina local mediante un símbolo del sistema compatible con Git o su herramienta de Git favorita. El comando clone de Git tiene este aspecto:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Recupere los archivos de su aplicación de función y póngalos en la copia del equipo local, como en el ejemplo siguiente:
   
        git pull origin master
   
    Si se le piden, proporcione las [credenciales de implementación configuradas](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Procedimientos recomendados de Azure Functions](functions-best-practices.md)
