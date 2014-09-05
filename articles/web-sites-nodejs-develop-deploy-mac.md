<properties  linkid="develop-node-create-a-website-mac" urlDisplayName="Web site" pageTitle="Create a Node.js web site on Mac - Azure tutorials" metaKeywords="Azure create website Node, Azure deploy website Node, website Node.js, Node website" description="Learn how to build and deploy a Node.js web site in Azure. Sample code is written in Java." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Build and deploy a Node.js web site to Azure" authors="" solutions="" manager="" editor="" />

# Compilación e implementación de un sitio web Node.js en Azure

Este tutorial muestra la forma de crear una aplicación [Node] e implementarla en un sitio web de Azure con [Git]. Las instrucciones de este tutorial se pueden seguir en cualquier sistema operativo que sea capaz de ejecutar Node.

Si prefiere ver un vídeo, el clip que aparece a la derecha muestra los mismos pasos que este tutorial.

A continuación se muestra una captura de pantalla de la aplicación completada:

![Un explorador muestra el mensaje "Hello
World".](./media/web-sites-nodejs-develop-deploy-mac/helloazure.png)

## Creación de un Sitio web Azure y activación de la publicación Git

Siga estos pasos para crear un sitio web de Azure y, a continuación, activar la publicación Git para el sitio web.

 
<div  class="dev-callout"><strong>Nota:</strong>
<p>Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a  href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Evaluación gratuita de Azure</a>.</p>
</div>

1.  Inicie sesión en el [Portal de administración de Azure][1].

2.  Haga clic en el icono **+ NEW**, situado en la parte inferior izquierda del portal.
    
    ![Portal Azure con el vínculo + NEW
    resaltado.](./media/web-sites-nodejs-develop-deploy-mac/plus-new.png)

3.  Haga clic en **WEB SITE** y, a continuación, en **QUICK CREATE**. Escriba un valor de **URL** y seleccione el centro de datos de su sitio web en la lista desplegable **REGIÓN**. Haga clic en la marca de verificación de la parte inferior del cuadro de diálogo.
    
    ![Cuadro de diálogo Quick
    Create](./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png)

4.  Cuando el estado del sitio web cambie a **Running**, haga clic en el nombre del sitio web para obtener acceso al **Panel**.
    
    ![Apertura del panel del sitio
    web](./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png)

5.  En la parte inferior derecha de la página de inicio rápido, seleccione **Set up a deployment from source control**.
    
    ![Configuración de la publicación
    Git](./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png)

6.  Cuando se le pregunte "Where is your source code?" (&iquest;Dónde está su código fuente?), seleccione **Local Git repository** y, a continuación, haga clic en la flecha.
    
    ![Dónde está su código
    fuente](./media/web-sites-nodejs-develop-deploy-mac/where_is_code.png)

7.  Para habilitar la publicación Git, debe proporcionar un nombre de usuario y una contraseña. Si ya ha activado la publicación para un sitio web de Azure, no se le solicitará el nombre de usuario o la contraseña. En su lugar, se creará un repositorio Git con el nombre de usuario y la contraseña que ha especificado anteriormente. Anote el nombre de usuario y la contraseña, puesto que se usarán para la publicación Git en todos los sitios web de Azure que cree.
    
    ![Cuadro de diálogo que solicita el nombre de usuario y la
    contraseña.](./media/web-sites-nodejs-develop-deploy-mac/git-deployment-credentials.png)

8.  Una vez esté listo el repositorio Git, se le mostrarán las instrucciones sobre los comandos Git que debe utilizar para configurar un repositorio local y, a continuación, se insertarán los archivos en Azure.
    
    ![Instrucciones de implementación Git devueltas después de crear un repositorio para el sitio
    web.](./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png)

## Compilación y comprobación de la aplicación localmente

En esta sección, creará un archivo **server.js** que contiene el ejemplo 'hello world' de [nodejs.org]. Este ejemplo se ha modificado a partir del ejemplo original agregando process.env.PORT como el puerto en el que se debe escuchar al ejecutarlo en un sitio web de Azure.

1.  Mediante un editor de texto, cree un nuevo archivo con nombre **server.js** en el directorio **helloworld**. Si el directorio **helloworld** no existe, créelo.
2.  Agregue lo siguiente como contenido del archivo **server.js** y, a continuación, guárdelo:
    
         var http = require('http')
         var port = process.env.PORT || 1337;
         http.createServer(function(req, res) {
           res.writeHead(200, { 'Content-Type': 'text/plain' });
           res.end('Hello World\n');
         }).listen(port);

3.  Abra la línea de comandos y utilice el siguiente comando para iniciar la página web localmente:
    
         node server.js
    
    [WACOM.INCLUDE
    [install-dev-tools](../includes/install-dev-tools.md)]

4.  Abra su explorador web y navegue hasta http://localhost:1337. Aparecerá una página web con el mensaje "Hello World", tal y como se muestra en la siguiente captura de pantalla:
    
    ![Un explorador muestra el mensaje "Hello
    World".](./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png)

## Publicación de la aplicación

1.  Desde la línea de comandos, cambie los directorios al directorio **helloworld** y escriba los siguientes comandos para inicializar el repositorio Git local.
    
         git init
    
	<div  class="dev-callout" markdown="1">

	<b>&iquest;No está disponible el comando Git?</b><p>
	<a href="http://git-scm.com/" target="_blank">Git</a> es un sistema de
	control de versión distribuida que puede utilizar para implementar
	su sitio web de Azure. Para obtener instrucciones acerca de la
	instalación en su plataforma, consulte la <a href="http://git-scm.com/download" target="_blank">página de descargas de
	Git (en inglés)</a>.</p>


	</div>


2.  Utilice los siguientes comandos para agregar archivos al repositorio:
    
         git add .
         git commit -m "initial commit"

3.  Agregue un Git remoto para enviar actualizaciones al sitio web de Azure que ha creado anteriormente con el siguiente comando:
    
         git remote add azure [URL del repositorio remoto]
    
    ![Instrucciones de implementación Git devueltas
    después de crear un repositorio para el sitio
    web.](./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png)

4.  Envíe los cambios a Azure con el siguiente comando:
    
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
    
    ![Estado de implementación Git en el
    portal](./media/web-sites-nodejs-develop-deploy-mac/git_deployments_first.png)

5.  Examine su sitio mediante el botón **Browse**, situado en su página del sitio web de Azure, dentro del Portal de administración.

## Publicación de cambios de la aplicación

1.  Abra el archivo **server.js** en un editor de texto y cambie "Hello World\\n" a "Hello Azure\\n". Guarde el archivo.
2.  Desde la línea de comandos, cambie los directorios al directorio **helloworld** y ejecute los siguientes comandos:
    
         git add .
         git commit -m "changing to hello azure"
         git push azure master
    
    Se le solicitará la contraseña que ha creado anteriormente. Si navega hasta la pestaña de implementaciones de su sitio web de Azure dentro del portal de administración, visualizará su historial de implementaciones actualizado:
    
    ![Estado de implementación Git actualizada en el
    Portal](./media/web-sites-nodejs-develop-deploy-mac/git_deployments_second.png)

3.  Examine su sitio mediante el botón **Browse** y asegúrese de que las actualizaciones se hayan aplicado.
    
    ![Página web que muestra "Hello
    Azure"](./media/web-sites-nodejs-develop-deploy-mac/helloazure.png)

4.  Puede deshacer la implementación anterior seleccionándola en la pestaña "Deployments" de su sitio web de Azure, dentro del Portal de administración, y mediante el botón **Redeploy**.

## Pasos siguientes

Si bien los pasos de este artículo utilizan el Portal Azure para crear un sitio web, también es posible utilizar las [herramientas de línea de comandos de Azure para Mac y Linux](/es-es/develop/nodejs/how-to-guides/command-line-tools/) para realizar las mismas operaciones.

Node.js proporciona un abundante ecosistema de módulos que puede utilizar con sus aplicaciones. Para obtener información acerca del funcionamiento de los Sitios web Azure con los módulos, consulte [Uso de módulos Node.js con aplicaciones de Azure][4].

Para obtener más información acerca de las versiones de Node.js que se proporcionan con Azure y la forma de especificar la versión que se debe utilizar con su aplicación, consulte [Especificación de una versión de Node.js en una aplicación Azure][5].

Si tiene problemas con su aplicación después de la implementación en Azure, consulte [Cómo depurar una aplicación Node.js en Sitios web Azure][6] para obtener información acerca del diagnóstico del problema.

## Recursos adicionales

* [Azure
  PowerShell](/es-es/develop/nodejs/how-to-guides/powershell-cmdlets/)
* [Herramientas de línea de comandos de Azure para Mac y
  Linux](/es-es/develop/nodejs/how-to-guides/command-line-tools/)



[1]: http://manage.windowsazure.com
[2]: http://git-scm.com/
[3]: http://git-scm.com/download
[4]: http://www.windowsazure.com/es-es/develop/nodejs/common-tasks/working-with-node-modules/
[5]: http://www.windowsazure.com/es-es/develop/nodejs/common-tasks/specifying-a-node-version/
[6]: http://www.windowsazure.com/es-es/develop/nodejs/how-to-guides/Debug-Website/