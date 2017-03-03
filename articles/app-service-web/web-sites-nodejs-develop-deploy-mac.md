---
title: "Creación de una aplicación web de Node.js en Azure App Service | Microsoft Docs"
description: "Obtenga información sobre cómo implementar una aplicación de Node.js en una aplicación web en el Servicio de aplicaciones de Azure."
services: app-service\web
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 85af23df-54af-4430-8d77-a1f97e2f5b10
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 4b8eb863a1fcb65c307870619ea9d8942c57a906
ms.lasthandoff: 01/20/2017


---
# <a name="create-a-nodejs-web-app-in-azure-app-service"></a>Creación de una aplicación web de Node.js en el Servicio de aplicaciones de Azure
> [!div class="op_single_selector"]
> * [.Net](web-sites-dotnet-get-started.md)
> * [Node.js](web-sites-nodejs-develop-deploy-mac.md)
> * [Java](web-sites-java-get-started.md)
> * [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
> * [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
> * [Python](web-sites-python-ptvs-django-mysql.md)
> 
> 

En este tutorial se muestra cómo crear una aplicación de [Node.js](http://nodejs.org) sencilla e implementarla en una [aplicación web](app-service-web-overview.md) de [Azure App Service](../app-service/app-service-value-prop-what-is.md) mediante [Git](http://git-scm.com). Las instrucciones de este tutorial se pueden seguir en cualquier sistema operativo que sea capaz de ejecutar Node.js.

Aprenderá a realizar los siguientes procedimientos:

* Creación de una aplicación web en el Servicio de aplicaciones de Azure con el Portal de Azure.
* Cómo implementar una aplicación de Node.js en la aplicación web insertándola en el repositorio Git de la aplicación web.

La aplicación completa escribe una cadena corta "hello world" en el explorador.

![Un explorador muestra el mensaje "Hello World".][helloworld-completed]

Para ver tutoriales y código de ejemplo con aplicaciones más complejas de Node.js, o para ver otros temas sobre cómo usar Node.js en Azure, consulte el [Centro para desarrolladores de Node.js](/develop/nodejs/).

> [!NOTE]
> Necesita una cuenta de Microsoft Azure para completar este tutorial. Si aún no la tiene, puede [activar los beneficios de suscripción a Visual Studio](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) o bien [registrarse para obtener una evaluación gratuita](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).
> 
> Si desea empezar a usar el Servicio de aplicaciones de Azure antes de suscribirse para obtener una cuenta de Azure, vaya a la [prueba gratuita del Servicio de aplicaciones](https://azure.microsoft.com/try/app-service/). Ahí puede crear de forma inmediata una aplicación web de corta duración para iniciarse en Servicio de aplicaciones, no se requiere tarjeta de crédito y no se establece ningún compromiso.
> 
> 

## <a name="create-a-web-app-and-enable-git-publishing"></a>Creación de una aplicación web y habilitación de la publicación Git
Siga estos pasos para crear una aplicación web en el Servicio de aplicaciones de Azure y habilitar la publicación en Git. 

[Git](http://git-scm.com/) es un sistema de control de versión distribuida que puede utilizar para implementar su sitio web de Azure. Tendrá que almacenar el código que escriba para su aplicación web en un repositorio Git local y lo implementará en Azure insertando en un repositorio remoto. Este método de implementación es una característica de las aplicaciones web del Servicio de aplicaciones.  

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en el icono **+NUEVO** situado en la parte superior izquierda del Portal de Azure.
3. Haga clic en **Web y móvil** y, después, en **Aplicación web**.
   
    ![][portal-quick-create]
4. Escriba un nombre para la aplicación web en el cuadro **Aplicación web** .
   
    Este nombre debe ser único en el dominio azurewebsites.net porque la dirección URL de la aplicación web será {nombre}.azurewebsites.net. Si el nombre especificado no es único, se muestra un signo de exclamación rojo en el cuadro de texto.
5. Seleccione una opción en **Suscripción**.
6. Seleccione un **Grupo de recursos** o cree uno nuevo.
   
    Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
7. Seleccione un **Plan de servicio de aplicaciones/Ubicación** o cree uno nuevo.
   
    Para obtener más información sobre los planes del Servicio de aplicaciones, consulte [Información general sobre los planes del Servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)
8. Haga clic en **Crear**.
   
    ![][portal-quick-create2]
   
    En poco tiempo, normalmente menos de un minuto, Azure termina de crear la nueva aplicación web.
9. Haga clic en **Aplicaciones web > {su nueva aplicación web}**.
   
    ![](./media/web-sites-nodejs-develop-deploy-mac/gotowebapp.png)
10. En la hoja **Aplicación web**, haga clic en la parte **Implementación**.
    
    ![][deployment-part]
11. En la hoja **Implementación continua**, haga clic en **Elegir origen**.
12. Haga clic en **Repositorio de Git local** y, después, en **Aceptar**.
    
    ![][setup-git-publishing]
13. Configure las credenciales de implementación si aún no lo hizo.
    
    a. En la hoja Aplicación web, haga clic en **Configuración > Credenciales de implementación**.
    
    ![][deployment-credentials]
    
    b. Cree un nombre de usuario y una contraseña. 
    
    ![](./media/web-sites-nodejs-develop-deploy-mac/setdeploycreds.png)
14. En la hoja Aplicación web, haga clic en **Configuración** y, después, en **Propiedades**.
    
    Para publicar, la insertará en un repositorio de Git remoto. La dirección URL del repositorio se muestra en **Dirección URL de Git**. Esta dirección URL se usará más adelante en el tutorial.
    
    ![][git-url]

## <a name="build-and-test-your-application-locally"></a>Compilación y comprobación de la aplicación localmente
En esta sección, creará un archivo **server.js** que contiene una versión ligeramente modificada del ejemplo 'Hello World' de [nodejs.org]. El código agrega process.env.PORT como el puerto que se escuchará al ejecutarlo en una aplicación web de Azure.

1. Cree un directorio llamado *helloworld*.
2. Use un editor de texto para crear un archivo nuevo denominado **server.js** en el directorio *helloworld* .
3. Copie el código siguiente en el archivo **server.js** y, después, guárdelo:
   
        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);
4. Abra la línea de comandos y use el siguiente comando para iniciar la aplicación web localmente:
   
        node server.js
5. Abra el explorador web y navegue a http://localhost:1337. 
   
    Aparecerá una página web con el mensaje "Hello World", tal y como se muestra en la siguiente captura de pantalla:
   
    ![Un explorador muestra el mensaje "Hello World".][helloworld-localhost]

## <a name="publish-your-application"></a>Publicación de la aplicación
1. Si aún no hizo, instale Git.
   
    Para obtener instrucciones de instalación para su plataforma, consulte la [página de descargas de Git](http://git-scm.com/download).
2. Desde la línea de comandos, cambie los directorios por el directorio **helloworld** y escriba el siguiente comando para inicializar el repositorio de Git local.
   
        git init
3. Utilice los siguientes comandos para agregar archivos al repositorio:
   
        git add .
        git commit -m "initial commit"
4. Agregue un Git remoto para insertar actualizaciones en la aplicación web de Azure que creó anteriormente con el siguiente comando:
   
        git remote add azure [URL for remote repository]
5. Inserte los cambios en Azure con el siguiente comando:
   
        git push azure master
   
    Se le solicitará la contraseña que creó anteriormente. La salida será similar al del ejemplo siguiente:
   
        Counting objects: 3, done.
        Delta compression using up to 8 threads.
        Compressing objects: 100% (2/2), done.
        Writing objects: 100% (3/3), 374 bytes, done.
        Total 3 (delta 0), reused 0 (delta 0)
        remote: New deployment received.
        remote: Updating branch 'master'.
        remote: Preparing deployment for commit id '5ebbe250c9'.
        remote: Preparing files for deployment.
        remote: Deploying Web.config to enable Node.js activation.
        remote: Deployment successful.
        To https://user@testsite.scm.azurewebsites.net/testsite.git
         * [new branch]      master -> master
6. Para ver la aplicación, haga clic en el botón **Examinar** de la parte **Aplicación web** de Azure Portal.
   
    ![Botón Examinar](./media/web-sites-nodejs-develop-deploy-mac/browsebutton.png)
   
    ![Hello world en Azure](./media/web-sites-nodejs-develop-deploy-mac/helloworldazure.png)

## <a name="publish-changes-to-your-application"></a>Publicación de cambios de la aplicación
1. Abra el archivo **server.js** en un editor de texto y cambie "Hello World\n" por "Hello Azure\n". 
2. Guarde el archivo .
3. En la línea de comandos, cambie los directorios por el directorio **helloworld** y ejecute los siguientes comandos:
   
        git add .
        git commit -m "changing to hello azure"
        git push azure master
   
    Se le pedirá de nuevo su contraseña.
4. Actualice la ventana del explorador en la que fue a la dirección URL de la aplicación web.
   
    ![Página web que muestra "Hello Azure"][helloworld-completed]

## <a name="roll-back-a-deployment"></a>Reversión de una implementación
En la hoja **Aplicación web**, puede hacer clic en **Configuración > Implementación continua** para ver el historial de implementaciones en la hoja **Implementaciones**. Si necesita revertir a una implementación anterior, selecciónela y haga clic en **Implementar de nuevo** en la hoja **Detalles de la implementación**.

## <a name="next-steps"></a>Pasos siguientes
Implementó una aplicación de Node.js en una aplicación web en el Servicio de aplicaciones de Azure. Para aprender la forma en que App Service Web Apps ejecuta aplicaciones de Node.js, consulte [Azure App Service Web Apps: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx) y [Especificación de una versión de Node.js en una aplicación Azure](../nodejs-specify-node-version-azure-apps.md).

Node.js proporciona un abundante ecosistema de módulos que puede utilizar con sus aplicaciones. Para obtener información sobre el funcionamiento de Aplicaciones web con módulos, consulte [Uso de módulos Node.js con aplicaciones de Azure](../nodejs-use-node-modules-azure-apps.md).

Si tiene problemas con la aplicación una vez implementada en Azure, consulte [Cómo depurar una aplicación web de Node.js en el Servicio de aplicaciones de Azure](web-sites-nodejs-debug.md) para obtener información acerca de cómo diagnosticar el problema.

Este artículo usa el Portal de Azure para crear una aplicación web. También puede usar la [interfaz de la línea de comandos de Azure](../xplat-cli-install.md) o [Azure PowerShell](/powershell/azureps-cmdlets-docs) para realizar las mismas operaciones.

Para más información sobre cómo desarrollar aplicaciones de Node.js en Azure, consulte el [Centro para desarrolladores de Node.js](/develop/nodejs/).

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png
[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png
[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png
[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png
[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png

