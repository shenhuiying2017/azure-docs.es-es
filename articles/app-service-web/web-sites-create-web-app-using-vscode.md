---
title: "Creación de una aplicación web ASP.NET Core en Visual Studio Code"
description: "En este tutorial muestra cómo crear una aplicación web ASP.NET Core con Visual Studio Code."
services: app-service\web
documentationcenter: .net
author: erikre
manager: erikre
editor: jimbe
ms.assetid: 877bff08-9ef7-405a-a1ca-1194f33c55f2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: e8f78602b293863b2e58160a5eb2cf8a5855576b
ms.contentlocale: es-es
ms.lasthandoff: 05/02/2017


---
# <a name="create-an-aspnet-core-web-app-in-visual-studio-code"></a>Creación de una aplicación web ASP.NET Core en Visual Studio Code
## <a name="overview"></a>Información general
En este tutorial se muestra cómo se crea una aplicación web de ASP.NET Core con [Visual Studio Code (VS Code)](http://code.visualstudio.com//Docs/whyvscode) y cómo se implementa en [Azure App Service](../app-service/app-service-value-prop-what-is.md). 

> [!NOTE]
> Aunque este artículo se refiere a las aplicaciones web, también se aplica a las aplicaciones de API y las aplicaciones móviles. 
> 
> 

ASP.NET Core es un rediseño significativo de ASP.NET. ASP.NET Core es un nuevo marco de código abierto multiplataforma diseñado para crear modernas aplicaciones web basadas en la nube con .NET. Para obtener más información, consulte [Introducción a ASP.NET Core](http://docs.asp.net/latest/conceptual-overview/aspnet.html). Para obtener información sobre Aplicaciones web del Servicio de aplicaciones de Azure, consulte [Introducción a aplicaciones web](app-service-web-overview.md).

[!INCLUDE [app-service-web-try-app-service.md](../../includes/app-service-web-try-app-service.md)]

## <a name="prerequisites"></a>Requisitos previos
* Instalación de [VS Code](http://code.visualstudio.com/Docs/setup).
* Instalación de Git. Puede instalarlo desde cualquiera de estas ubicaciones: [Chocolatey](https://chocolatey.org/packages/git) o [git-scm.com](http://git-scm.com/downloads). Si no está familiarizado con Git, elija [git-scm.com](http://git-scm.com/downloads) y seleccione la opción para **usar Git desde el símbolo del sistema de Windows**. Una vez que instale Git, también tendrá que establecer el nombre de usuario de Git y el correo electrónico, ya que es necesario más adelante en el tutorial (al realizar una confirmación desde VS Code).  

## <a name="install-aspnet-core"></a>Instalación de ASP.NET Core
ASP.NET Core es una pila de .NET eficiente que sirve para crear aplicaciones web y de nube modernas que se ejecutan en OS X, Linux y Windows. Se ha desarrollado desde el principio para proporcionar un marco de desarrollo optimizado para las aplicaciones que se implementan en la nube o se ejecutan de forma local. Consta de componentes modulares con una sobrecarga mínima, para continuar disfrutando de flexibilidad al crear soluciones.

Este tutorial está diseñado para ayudarle a comenzar a crear aplicaciones con las últimas versiones de desarrollo de ASP.NET Core. Las instrucciones siguientes son específicas de Windows. Para obtener instrucciones de instalación en OS X, Linux y Windows, vea [Introducción a ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started). 


> [!NOTE]
> Para obtener instrucciones de instalación más detalladas para Windows, Linux y OS X, consulte [Instalación de ASP.NET Core](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx). 
> 
> 

## <a name="create-the-web-app"></a>Creación de la aplicación web
En esta sección se explica cómo aplicar la técnica scaffolding a una nueva aplicación web ASP.NET mediante la herramienta de la CLI de donet. 

1. En el símbolo del sistema para crear la carpeta del proyecto y aplicar la técnica scaffolding a la aplicación, escriba lo siguiente.
   
```terminal
mkdir SampleWebApp
cd SampleWebApp
dotnet new mvc
```
![CLI de donet: Generador de ASP.NET Core](./media/web-sites-create-web-app-using-vscode/dotnetcore-mvc-01.png)

2. Para instalar los paquetes de NuGet necesarios para ejecutar el comando siguiente:
   
    ```terminal
    dotnet restore
    ```

## <a name="run-the-web-app-locally"></a>Ejecute la aplicación web localmente.
Ahora que ha creado la aplicación web y ha recuperado todos los paquetes de NuGet para la aplicación, puede ejecutar la aplicación web localmente.

1. Ejecute la aplicación (el comando `dotnet run` creará la aplicación cuando está anticuada):
    ```terminal
    dotnet run
    ```
2. Abra un explorador y vaya a la dirección URL siguiente.
   
    **http://localhost:5000**
   
    La página predeterminada de la aplicación web se mostrará como sigue.
   
    ![Aplicación web local en un explorador](./media/web-sites-create-web-app-using-vscode/08-web-app.png)
3. Cierre el explorador. En la **ventana Comandos**, presione **Ctrl+C** para cerrar la aplicación o cerrar dicha **ventana**. 

## <a name="create-a-web-app-in-the-azure-portal"></a>Creación de una aplicación web en el Portal de Azure
Los pasos siguientes le guiarán a través de la creación de una aplicación web en el Portal de Azure.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **NUEVO** en la parte superior izquierda del portal.
3. Haga clic en **Web Apps > Aplicación web**.
   
    ![Nueva aplicación web de Azure](./media/web-sites-create-web-app-using-vscode/09-azure-newwebapp.png)
4. Escriba un valor para **Nombre**, como **SampleWebAppDemo**. Tenga en cuenta que este nombre debe ser exclusivo y que el portal le obligará a ello al escribir el nombre. Por lo tanto, si selecciona un valor diferente, tendrá que sustituir ese valor en cada aparición de **SampleWebAppDemo** que aparezca en este tutorial. 
5. Seleccione un **plan de Servicio de aplicaciones** ya existente o cree uno nuevo. Si crea un nuevo plan, seleccione el nivel de precios, la ubicación y otras opciones. Para obtener más información sobre los planes del Servicio de aplicaciones, consulte el artículo [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
   
    ![Hoja de la nueva aplicación web de Azure](./media/web-sites-create-web-app-using-vscode/10-azure-newappblade.png)
6. Haga clic en **Crear**.
   
    ![hoja de aplicación web](./media/web-sites-create-web-app-using-vscode/11-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Habilitación de la publicación Git para la nueva aplicación web
Git es un sistema de control de versión distribuida que puede utilizar para implementar su aplicación web del Servicio de aplicaciones de Azure. Tendrá que almacenar el código que escriba para su aplicación web en un repositorio Git local y lo implementará en Azure insertando en un repositorio remoto.   

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Examinar**.
3. Haga clic en **Aplicaciones web** para ver una lista de las aplicaciones web asociadas con su suscripción de Azure.
4. Seleccione la aplicación web que creó en este tutorial.
5. En la hoja de aplicaciones web, haga clic en **Configuración** > **Implementación continua**. 
   
    ![Host de la aplicación web de Azure](./media/web-sites-create-web-app-using-vscode/14-azure-deployment.png)
6. Haga clic en **Elegir origen > Repositorio de Git local**.
7. Haga clic en **OK**.
   
    ![Repositorio de Git local de Azure](./media/web-sites-create-web-app-using-vscode/15-azure-localrepository.png)
8. Si no ha configurado previamente las credenciales de implementación para publicar una aplicación web u otra aplicación del Servicio de aplicaciones, configúrelas ahora:
   
   * Haga clic en **Configuración** > **Credenciales de implementación**. Se mostrará la hoja **Configurar credenciales de implementación** .
   * Cree un nombre de usuario y una contraseña.  Necesitará esta contraseña más tarde al configurar Git.
   * Haga clic en **Guardar**.
9. En la hoja de la aplicación web, haga clic en **Configuración > Propiedades**. La dirección URL del repositorio Git remoto en el que implementará se muestra en **DIRECCIÓN URL DE GIT**.
10. Copie el valor **dirección URL de GIT** para su uso posterior en el tutorial.
    
    ![Dirección URL de Git de Azure](./media/web-sites-create-web-app-using-vscode/17-azure-giturl.png)

## <a name="publish-your-web-app-to-azure-app-service"></a>Publicación de la aplicación web en el Servicio de aplicaciones de Azure
En esta sección, creará un repositorio Git local e insertará desde ese repositorio a Azure para implementar la aplicación web en Azure.

1. En VS Code seleccione la opción **Git** en la barra de navegación de la izquierda.
   
    ![Icono de Git en VS Code](./media/web-sites-create-web-app-using-vscode/git-icon.png)
2. Seleccione **Inicializar repositorio Git** para asegurarse de que el área de trabajo está bajo control del código fuente de git. 
   
    ![Inicializar Git](./media/web-sites-create-web-app-using-vscode/19-initgit.png)
3. Abra la ventana Comandos y cambie los directorios por el directorio de la aplicación web. Después, escriba el comando siguiente:
   
        git config core.autocrlf false
   
    Este comando evita problemas relacionados con el texto donde existen las finalizaciones CRLF y LF.
4. En VS Code, agregue un mensaje de confirmación y haga clic en el icono de marca **Confirmar todo** .
   
    ![Confirmar todo de Git](./media/web-sites-create-web-app-using-vscode/20-git-commit.png)
5. Una vez que Git haya completado el procesamiento, verá que no hay ningún archivo que aparezca en la ventana de Git en **Cambios**. 
   
    ![Sin cambios de Git](./media/web-sites-create-web-app-using-vscode/no-changes.png)
6. Vuelva a la ventana Comandos donde el símbolo del sistema señala al directorio donde se encuentra la aplicación web.
7. Cree una referencia remota para insertar actualizaciones en la aplicación web usando la dirección URL de Git (terminada en “.git”) que copió anteriormente.
   
        git remote add azure [URL for remote repository]
8. Configure Git para guardar las credenciales localmente de manera que se anexe automáticamente a los comandos de inserción generados a partir de código de VS.
   
        git config credential.helper store
9. Envíe los cambios a Azure escribiendo el siguiente comando. Después de esta inserción inicial en Azure, podrá realizar todos los comandos de inserción desde el código de VS. 
   
        git push -u azure master
   
    Se le solicitará la contraseña que ha creado anteriormente en Azure. **Nota: La contraseña no será visible.**
   
    La salida de este comando finaliza con un mensaje que indica que la implementación se ha realizado correctamente.
   
        remote: Deployment successful.
        To https://user@testsite.scm.azurewebsites.net/testsite.git
        [new branch]      master -> master

> [!NOTE]
> Si realiza cambios en la aplicación, puede volver a publicar directamente en el código de VS con la funcionalidad integrada de Git seleccionando la opción **Confirmar todo**, seguida de la opción **Insertar**. Encontrará la opción **Insertar** en el menú desplegable junto a los botones **Confirmar todo** y **Actualizar**.
> 
> 

Si necesita para colaborar en un proyecto, considere la posibilidad de insertar en GitHub entre la inserción en Azure.

## <a name="run-the-app-in-azure"></a>Ejecución de la aplicación en Azure
Ahora que ha implementado la aplicación web, vamos a ejecutar la aplicación mientras está hospedada en Azure. 

Esto puede hacerse de dos maneras:

* Abra un explorador y escriba el nombre de la aplicación web de la manera siguiente.   
  
        http://SampleWebAppDemo.azurewebsites.net
* En el Portal de Azure, busque la hoja de aplicación web para la aplicación web y haga clic en **Examinar** para ver la aplicación. 
* en el explorador predeterminado.

![Aplicación web de Azure](./media/web-sites-create-web-app-using-vscode/21-azurewebapp.png)

## <a name="summary"></a>Resumen
En este tutorial, ha aprendido a crear una aplicación web en VS Code y a implementarla en Azure. Para más información sobre VS Code, consulte el artículo [¿Por qué Visual Studio Code?](https://code.visualstudio.com/Docs/) Para más información sobre App Service Web Apps, consulte [Información general de Web Apps](app-service-web-overview.md). 


