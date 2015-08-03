<properties
	pageTitle="Creación de una aplicación web de Node.js en el Servicio de aplicaciones de Azure"
	description="Obtenga información acerca de cómo crear e implementar una aplicación web de Node.js en Azure."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.date="03/24/2015"
	ms.author="mwasson"/>

# Compilación e implementación de una aplicación web de Node.js en el Servicio de aplicaciones de Azure

Este tutorial muestra la forma de crear una aplicación de [Node][nodejs.org] e implementarla en [Aplicaciones web del Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) con [Git]. Las instrucciones de este tutorial se pueden seguir en cualquier sistema operativo que sea capaz de ejecutar Node.

A continuación se muestra una captura de pantalla de la aplicación completada:

![Un explorador muestra el mensaje "Hello World".][helloworld-completed]

##Creación de una aplicación web y habilitación de la publicación Git

Siga estos pasos para crear una aplicación web y habilitar la publicación Git.

> [AZURE.NOTE]Necesita una cuenta de Microsoft Azure para completar este tutorial. Si aún no la tiene, puede [activar los beneficios de suscripción a MSDN](/es-es/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) o bien [registrarse para obtener una evaluación gratuita](/es-es/pricing/free-trial/?WT.mc_id=A261C142F).

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Haga clic en el icono **+Nuevo** situado en la parte superior izquierda del portal.

3. Haga clic en **Web + móvil** y, a continuación, en **Aplicación web**.

    ![][portal-quick-create]

4. Escriba un valor para **URL**.

5. Seleccione un plan de Servicio de aplicaciones o cree uno nuevo. Si crea un nuevo plan, seleccione el nivel de precios, la ubicación y otras opciones.

    ![][portal-quick-create2]

6. Haga clic en **Crear**.

7. Una vez que el estado cambia a **Ejecutándose**, el portal abrirá automáticamente la hoja de la aplicación web. También puede llegar a la hoja haciendo clic en **Examinar**.

	![][go-to-dashboard]

8. Haga clic en **Implementación**. Puede que necesite desplazarse para ver esta parte de la hoja.

	![][deployment-part]

9. Haga clic en **Elegir origen**, a continuación, en **Repositorio de Git local**. Haga clic en **Aceptar**.

	![][setup-git-publishing]


10. Haga clic en la parte **credenciales de implementación** (subrayado en rojo abajo). Cree un nombre de usuario y una contraseña. Haga clic en **Guardar**. Si se ha habilitado la publicación para una aplicación web, no es necesario este paso.

	![][deployment-credentials]


11. Para publicar, insertará un repositorio de Git remoto. Busque la dirección URL del repositorio, haga clic en **Toda la configuración**, a continuación, haga clic en **Propiedades**. La dirección URL aparece en "GIT URL".

	![][git-url]

##Compilación y comprobación de la aplicación localmente

En esta sección, creará un archivo **server.js** que contiene el ejemplo 'hello world' de [nodejs.org]. Este ejemplo se ha modificado a partir del ejemplo original agregando process.env.PORT como el puerto en el que se escuchará al ejecutarlo en una aplicación web de Azure.

1. Mediante un editor de texto, cree un nuevo archivo con nombre **server.js** en el directorio **helloworld**. Si el directorio **helloworld** no existe, créelo.

2. Agregue lo siguiente como contenido del archivo **server.js** y, a continuación, guárdelo:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Abra la línea de comandos y utilice el siguiente comando para iniciar la aplicación web localmente:

        node server.js

4. Abra el explorador web y navegue a http://localhost:1337. Aparecerá una página web con el mensaje "Hello World", tal y como se muestra en la siguiente captura de pantalla:

    ![Un explorador muestra el mensaje "Hello World".][helloworld-localhost]

##Publicación de la aplicación

1. Desde la línea de comandos, cambie los directorios al directorio **helloworld** y escriba los siguientes comandos para inicializar el repositorio Git local.

		git init

	> [AZURE.NOTE]**¿No está disponible el comando Git?** [Git](http://git-scm.com/%20target="_blank) es un sistema de control de versión distribuida que puede utilizar para implementar su sitio web de Azure. Para obtener instrucciones de instalación en su plataforma, consulte la [página de descargas de Git](http://git-scm.com/download%20target="_blank").

2. Utilice los siguientes comandos para agregar archivos al repositorio:

		git add .
		git commit -m "initial commit"

3. Agregue un Git remoto para enviar actualizaciones a la aplicación web de Azure que ha creado anteriormente con el siguiente comando:

		git remote add azure [URL for remote repository]


4. Envíe los cambios a Azure con el siguiente comando:

		git push azure master

	Se le solicitará la contraseña que ha creado anteriormente. La salida debe ser similar a la siguiente:

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


5. Para ver la aplicación, haga clic en el botón **Examinar** situado en la parte **Aplicación web** dentro del portal de administración.

##Publicación de cambios de la aplicación

1. Abra el archivo **server.js** en un editor de texto y cambie "Hello World\n" a "Hello Azure\n". Guarde el archivo .
2. Desde la línea de comandos, cambie los directorios al directorio **helloworld** y ejecute los siguientes comandos:

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	Se le solicitará la contraseña que ha creado anteriormente.

3. Vaya a su aplicación haciendo clic en **Examinar** y observe que las actualizaciones se han aplicado.

	![Página web que muestra "Hello Azure"][helloworld-completed]

4. Puede revertir a la implementación anterior seleccionándola en **Implementaciones**.

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

##Pasos siguientes

Si bien los pasos de este artículo utilizan el Portal de Azure para crear una aplicación web, también es posible utilizar la [interfaz de la línea de comandos de Azure](../xplat-cli.md) para realizar las mismas operaciones.

Node.js proporciona un abundante ecosistema de módulos que puede utilizar con sus aplicaciones. Para obtener información acerca del funcionamiento de aplicaciones web con los módulos, consulte [Uso de módulos Node.js con aplicaciones de Azure](../nodejs-use-node-modules-azure-apps.md).

Para obtener más información acerca de las versiones de Node.js que se proporcionan con Azure y la forma de especificar la versión que se debe utilizar con su aplicación, consulte [Especificación de una versión de Node.js en una aplicación Azure](../nodejs-specify-node-version-azure-apps.md).

Si tiene problemas con su aplicación después de la implementación en Azure, consulte [Cómo depurar una aplicación Node.js en Sitios web Azure](web-sites-nodejs-debug.md) para obtener información acerca del diagnóstico del problema.


##Recursos adicionales

* [Azure PowerShell](../install-configure-powershell.md)
* [Interfaz de la línea de comandos de Azure](../xplat-cli.md)

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)


[nodejs.org]: http://nodejs.org
[Git]: http://git-scm.com


[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png

[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png


[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png

[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png

[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png

[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png


[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png
 

<!---HONumber=July15_HO4-->