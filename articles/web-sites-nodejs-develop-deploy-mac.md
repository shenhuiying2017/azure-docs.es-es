<properties urlDisplayName="Website" pageTitle="Crear un sitio web Node.js en Mac: tutoriales de Azure"metaKeywords ="crear nodo sitio web de Azure, nodo de sitio web de implementación de Azure, Node.js de sitio web, sitio web de nodo" description="Aprenda a crear e implementar un sitio web de Node.js en Azure. El código de ejemplo está escrito en Java." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />






# Compilación e implementación de un sitio web Node.js en Azure

Este tutorial muestra la forma de crear una aplicación [nodejs.org] de [Node] e implementarla en un sitio web de Azure con [Git]. Las instrucciones de este tutorial se pueden seguir en cualquier sistema operativo que sea capaz de ejecutar Node.

Si prefiere ver este tutorial como un vídeo, el clip siguiente muestra pasos similares:
[AZURE.VIDEO create-a-nodejs-site-deploy-from-github]
 
A continuación se muestra una captura de pantalla de la aplicación completada:

![A browser displaying the 'Hello World' message.][helloworld-completed]

##Creación de un sitio web de Azure y habilitación de la publicación Git

Siga estos pasos para crear un sitio web de Azure y, a continuación, activar la publicación Git para el sitio web.

> [WACOM.NOTE]
> para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener información, consulte: <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Prueba gratuita de Azure</a>.
> 
> Si desea empezar a trabajar con sitios web de Azure antes de contratar una cuenta, vaya a <a href="https://trywebsites.azurewebsites.net/?language=nodejs">https://trywebsites.azurewebsites.net,</a>donde puede crear inmediatamente un sitio de inicio ASP.NET en sitios web de Azure de forma gratuita. No se requiere ninguna tarjeta de crédito, sin compromisos.

1. Inicie sesión en el [Portal de administración de Azure].

2. Haga clic en el icono **+ NEW**, situado en la parte inferior izquierda del portal.

    ![The Azure Portal with the +NEW link highlighted.][portal-new-website]

3. Haga clic en **SITIO WEB** y luego en **CREACIÓN RÁPIDA**. Escriba un valor de **URL** y seleccione el centro de datos de su sitio web en la lista desplegable **REGIÓN**. Haga clic en la marca de verificación de la parte inferior del cuadro de diálogo.

    ![The Quick Create dialog][portal-quick-create]

4. Cuando el estado del sitio web cambie a **Running**, haga clic en el nombre del sitio web para obtener acceso al **Panel**.

	![Open web site dashboard][go-to-dashboard]

6. En la parte inferior derecha de la página de inicio rápido, seleccione **Set up a deployment from source control**.

	![Set up Git publishing][setup-git-publishing]

6. Cuando se le pregunte "Where is your source code?" (¿Dónde está su código fuente?), seleccione **Local Git repository** y, a continuación, haga clic en la flecha.

	![where is your source code][where-is-code]

7. Para habilitar la publicación Git, debe proporcionar un nombre de usuario y una contraseña. Si ya ha activado la publicación para un sitio web de Azure, no se le solicitará el nombre de usuario o la contraseña. En su lugar, se creará un repositorio Git con el nombre de usuario y la contraseña que ha especificado anteriormente. Anote el nombre de usuario y la contraseña, puesto que se usarán para la publicación Git en todos los sitios web de Azure que cree.

	![The dialog prompting for user name and password.][portal-git-username-password]

8. Una vez esté listo el repositorio Git, se le mostrarán las instrucciones sobre los comandos Git que debe utilizar para configurar un repositorio local y, a continuación, se insertarán los archivos en Azure.

	![Git deployment instructions returned after creating a repository for the website.][git-instructions]

##Compilación y comprobación de la aplicación localmente

En esta sección, creará un archivo **server.js** que contiene el ejemplo 'hello world' de [nodejs.org]. Este ejemplo se ha modificado a partir del ejemplo original agregando process.env.PORT como el puerto en el que se debe escuchar al ejecutarlo en un sitio web de Azure.

1. Mediante un editor de texto, cree un nuevo archivo con nombre **server.js** en el directorio **helloworld**. Si el directorio **helloworld** no existe, créelo.
2. Agregue lo siguiente como contenido del archivo **server.js** y, a continuación, guárdelo:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Abra la línea de comandos y utilice el siguiente comando para iniciar la página web localmente:

        node server.js

4. Abra el explorador web y navegue a http://localhost:1337. Aparecerá una página web con el mensaje "Hello World", tal y como se muestra en la siguiente captura de pantalla:

    ![A browser displaying the 'Hello World' message.][helloworld-localhost]

##Publicación de la aplicación

1. Desde la línea de comandos, cambie los directorios al directorio **helloworld** y escriba los siguientes comandos para inicializar el repositorio Git local. 

		git init

	<div class="dev-callout"><strong>¿No está disponible el comando Git?</strong>
	<p><a href="http://git-scm.com/" target="_blank">Git</a> es un sistema de control de versión distribuida que puede utilizar para implementar su sitio web de Azure. Para obtener instrucciones de instalación para su plataforma, consulte <a href="http://git-scm.com/download" target="_blank">la página de descarga de Git</a>.</p>
	</div>

2. Utilice los siguientes comandos para agregar archivos al repositorio:

		git add .
		git commit -m "initial commit"

3. Agregue un Git remoto para enviar actualizaciones al sitio web de Azure que ha creado anteriormente con el siguiente comando:

		git remote add azure [URL for remote repository]

    ![Git deployment instructions returned after creating a repository for the web site.][git-instructions]
 
4. Envíe los cambios a Azure con el siguiente comando:

		git push azure master

	Se le solicitará la contraseña que ha creado anteriormente y visualizará lo siguiente:

		Password for 'testsite.scm.azurewebsites.net':
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
    
	Si navega hasta la pestaña de implementaciones de su sitio web de Azure dentro del Portal de administración, visualizará su primera implementación en el historial de implementaciones:

	![Git deployment status on the portal][git-deployments-first] 

5. Examine su sitio mediante el botón **Browse**, situado en su página del sitio web de Azure, dentro del Portal de administración.

##Publicación de cambios de la aplicación

1. Abra el archivo **server.js** en un editor de texto y cambie "Hello World\n" a "Hello Azure\n". Guarde el archivo.
2. Desde la línea de comandos, cambie los directorios al directorio **helloworld** y ejecute los siguientes comandos:

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	Se le solicitará la contraseña que ha creado anteriormente. Si navega hasta la pestaña de implementaciones de su sitio web de Azure dentro del portal de administración, visualizará su historial de implementaciones actualizado:
	
	![Git deployment status updated on the portal][git-deployments-second]

3. Examine su sitio mediante el botón **Browse** y asegúrese de que las actualizaciones se hayan aplicado.

	![A web page displaying 'Hello Azure'][helloworld-completed]

4. Puede deshacer la implementación anterior seleccionándola en la pestaña "Deployments" de su sitio web de Azure, dentro del Portal de administración, y mediante el botón **Redeploy**.

##Pasos siguientes

Si bien los pasos de este artículo utilizan el Portal Azure para crear un sitio web, también es posible utilizar las [herramientas de línea de comandos de Azure para Mac y Linux] para realizar las mismas operaciones.

Node.js proporciona un abundante ecosistema de módulos que puede utilizar con sus aplicaciones. Para obtener información acerca del funcionamiento de los Sitios web Azure con los módulos, consulte [Uso de módulos Node.js con aplicaciones de Azure].(/es-es/documentation/articles/nodejs-use-node-modules-azure-apps/).

Para obtener más información acerca de las versiones de Node.js que se proporcionan con Azure y la forma de especificar la versión que se debe utilizar con su aplicación, consulte [Especificación de una versión de Node.js en una aplicación Azure].(/es-es/documentation/articles/nodejs-specify-node-version-azure-apps/).

Si tiene problemas con la aplicación una vez implementada en Azure, consulte [cómo depurar una aplicación Node.js en Sitios web de Azure](/es-es/documentation/articles/web-sites-nodejs-debug/) para obtener información acerca del diagnóstico del problema.


##Recursos adicionales

* [Azure PowerShell]
* [Herramientas de línea de comandos de Azure para Mac y Linux]

[Azure PowerShell]: /es-es/documentation/articles/install-configure-powershell/

[nodejs.org]: http://nodejs.org
[Git]: http://git-scm.com

[Portal de administración de Azure]: http://manage.windowsazure.com
[Herramientas de línea de comandos de Azure para Mac y Linux]: /es-es/documentation/articles/xplat-cli/

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-new-website]: ./media/web-sites-nodejs-develop-deploy-mac/plus-new.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-git-username-password]: ./media/web-sites-nodejs-develop-deploy-mac/git-deployment-credentials.png
[git-instructions]: ./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png

[git-deployments-first]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_first.png
[git-deployments-second]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_second.png

[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[where-is-code]: ./media/web-sites-nodejs-develop-deploy-mac/where_is_code.png

<!--HONumber=35.2-->
