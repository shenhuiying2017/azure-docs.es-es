# Publicación de Sitios web de Azure con Git

Los Sitios web de Azure admiten el desarrollo continuo desde el control de código fuente y herramientas de repositorio como BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial y TFS. Puede utilizar estas herramientas para mantener el contenido y el código de su sitio web y, después, insertar los cambios de forma rápida y fácil en el sitio cuando lo desee.

En este artículo, aprenderá a utilizar Git para publicar directamente desde su equipo local a un Sitio web de Azure (en Azure, este método de publicación se denomina **Local Git**). También conocerá cómo habilitar la implementación continua desde sitios web de repositorio, como BitBucket, CodePlex, DropBox, GitHub o Mercurial. Para obtener información acerca de la utilización de TFS para la implementación continua, consulte [Entrega continua a Azure con Visual Studio Online].

> [AZURE.NOTE] Muchos de los comandos Git descritos en este artículo se realizan automáticamente al crear un sitio web mediante las herramientas de línea de comandos que se describen en <a href="/es-es/develop/nodejs/how-to-guides/command-line-tools/">Herramientas de línea de comandos de Azure para Mac y Linux</a>.

Esta tarea incluye los siguientes pasos:

* [Instalación de Git](#Step1)
* [Creación de un repositorio local](#Step2)
* [Agregar una página web](#Step3)
* [Habilitación del repositorio del sitio web](#Step4)
* [Implementación del proyecto](#Step5)
	* [Inserción de archivos locales en Azure (Git local)](#Step6)
	* [Implementación de archivos desde un sitio web de repositorio como BitBucket, CodePlex, Dropbox, GitHub o Mercurial](#Step7)
	* [Implementación de una solución de Visual Studio desde BitBucket, CodePlex, Dropbox, GitHub o Mercurial](#Step75)
* [Solución de problemas](#Step8)

<h2><a id="Step2"></a>Instalación de Git</h2>

Los pasos requeridos para instalar Git varían según los sistemas operativos. Consulte [Installing Git] para obtener una guía sobre la instalación y las distribuciones específicas del sistema operativo.

> [AZURE.NOTE] Algunos sistemas operativos disponen de versiones de Git en línea de comandos y de GUI. Las instrucciones proporcionadas en este artículo utilizan la versión en línea de comandos.

<h2><a id="Step2"></a>Creación de un repositorio local</h2>

Realice las tareas siguientes al crear un nuevo repositorio Git.

1. Cree un directorio denominado MyGitRepository que contenga su repositorio Git y los archivos del sitio web.

2. Abra una línea de comandos, como **GitBash** (Windows) o **Bash** (shell de Unix). En los sistemas OS X puede tener acceso a la línea de comandos mediante la aplicación **Terminal**.

3. Desde la línea de comandos, cambie al directorio MyGitRepository.

		cd MyGitRepository

4. Utilice el comando siguiente para inicializar un nuevo repositorio Git:

		git init

	Debería devolver un mensaje como **Se inicializó un repositorio Git vacío en [ruta de acceso]**.

<h2><a id="Step3"></a>Agregar una página web</h2>

Los Sitios web de Azure admiten aplicaciones creadas en una variedad de lenguajes de programación. Para este ejemplo, se utilizará un archivo .html estático. Para obtener información acerca de la publicación de sitios web en otros lenguajes de programación en Azure, consulte la página [Centro para desarrolladores de Azure].

1. Con un editor de texto, cree un nuevo archivo denominado **index.html** en el directorio raíz del repositorio Git (el directorio MyGitRepository que ha creado anteriormente).

2. Agregue el texto siguiente como contenido para el archivo index.htm y guárdelo.

		Hello Git!

3. Desde la línea de comandos, compruebe que está en la raíz de su repositorio Git. A continuación, utilice el comando siguiente para agregar el archivo **index.html** al repositorio:

		git add index.html 

	> [AZURE.NOTE] Puede encontrar la ayuda para cualquier comando git escribiendo -help o --help tras el comando. Por ejemplo, para las opciones de parámetro para el comando add, escriba  'git add -help' para obtener ayuda de línea de comandos o  'git add --help' para obtener más ayuda.

4. A continuación, confirme los cambios en el repositorio mediante el siguiente comando:

		git commit -m "Adding index.html to the repository"

	Debería ver una salida similar a la siguiente:

		[master (root-commit) 369a79c] Adding index.html to the repository
		 1 file changed, 1 insertion(+)
		 create mode 100644 index.html

<h2><a id="Step4"></a>Habilitación del repositorio del sitio web</h2>

Lleve a cabo los pasos siguientes para habilitar un repositorio Git para su sitio web mediante el portal de Azure:

1. Inicie sesión en el [portal de Azure].

2. Haga clic en el botón NUEVO para crear un nuevo sitio web para el que activará un repositorio.

2. Espere hasta que el proceso de creación del sitio web haya finalizado en la vista **Sitios web** y seleccione el sitio web.

	![An image displaying a selected web site][portal-select-website]

3. Seleccione la pestaña **PANEL**.

4. En la sección **vista rápida**, seleccione **Configuración de la implementación desde un control de código fuente**.  Aparece el siguiente cuadro de diálogo **CONFIGURAR IMPLEMENTACIÓN**.

	![git-WhereIsYourSourceCode][git-WhereIsYourSourceCode]

4. Elija **Git local** y, a continuación, haga clic en la flecha **Siguiente**.

4. Si esta es la primera vez que configura un repositorio en Azure, tendrá que crear unas credenciales de inicio de sesión para él. Las usará para iniciar sesión en el repositorio de Azure y aplicar cambios desde su repositorio Git local. 

	![](./media/publishing-with-git/git_credentials.png)
	
5. Después de unos momentos, verá el mensaje indicando que el repositorio ya está listo. 

	![git-instructions][git-instructions]

<h2><a id="Step5"></a>Implementación del proyecto</h2>

<h3><a id="Step6"></a>Inserción de archivos locales en Azure (Git local)</h3>

En este punto, el portal muestra instrucciones para inicializar un repositorio local y agregar archivos. Ya lo ha hecho anteriormente en los pasos anteriores de este tema. Sin embargo, si no ha configurado las credenciales de implementación, debe volver a la pestaña **PANEL**** del portal y hacer clic en **Restablezca sus credenciales de implementación**.

Siga los pasos siguientes para publicar su sitio web en Azure utilizando Local Git:

1. Utilizando la línea de comandos, compruebe que está en la raíz de su repositorio Git que contiene el archivo index.html creado anteriormente.

2. Copie el comando git remote add del paso 3 de las instrucciones devuelto por el portal. Tendrá un aspecto similar al siguiente:

		git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [AZURE.NOTE] El comando **remote** agrega una referencia con nombre a un repositorio remoto. En este ejemplo, se crea una referencia denominada  'azure' para su repositorio del Sitio web de Azure.

1. Utilice los comandos siguientes de la línea de comandos para insertar el contenido actual del repositorio desde el repositorio local al repositorio remoto  'azure':

		git push azure master

	Se le solicitará la contraseña que ha creado antes al restablecer las credenciales de implementación en el portal. Escriba la contraseña (tenga en cuenta que Gitbash no envía los asteriscos a la consola mientras escribe la contraseña). Debería ver una salida similar a la siguiente:

		Counting objects: 6, done.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (6/6), 486 bytes, done.
		Total 6 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '369a79c929'.
		remote: Preparing files for deployment.
		remote: Deployment successful.
		To https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git
		* [new branch]		master -> master

	> [AZURE.NOTE] El repositorio creado para el Sitio web de Azure espera solicitudes de inserción para dirigir la rama <strong>principal</strong> de este repositorio, que se utilizará después como contenido del sitio web.

2. En el portal, haga clic en el vínculo **EXAMINAR**, en la parte inferior del portal, para comprobar que se ha implementado **index.html**. Se mostrará una página que contiene "Hello Git!".

	![A webpage containing 'Hello Git!'][hello-git]

3. Con un editor de texto, cambie el archivo **index.html** para que contenga "Yay!" y, a continuación, guarde el archivo.

4. Utilice los comandos siguientes desde la línea de comandos para **agregar** Y **confirmar** los cambios y, a continuación, **insertar** los cambios en el repositorio remoto:

		git add index.html
		git commit -m "Celebration"
		git push azure master

	Una vez completado el comando **push**, actualice el explorador (puede tener que presionar Ctrl+F5 para que el explorador se actualice correctamente) y observe que el contenido de la página ahora refleja el último cambio confirmado.

	![A webpage containing 'Yay!'][yay]

<h3><a id="Step7"></a>Implementación de archivos desde un sitio web de repositorio como BitBucket, CodePlex, Dropbox, GitHub o Mercurial</h3>

La inserción de archivos locales en Azure mediante Local Git le permite realizar manualmente actualizaciones desde un proyecto local a su Sitio web de Azure, mientras que la implementación desde BitBucket, CodePlex, Dropbox, GitHub o Mercurial da como resultado un proceso continuo de implementación en el que Azure extraerá las actualizaciones más recientes de su proyecto.

Aunque ambos métodos dan como resultado la implementación de su proyecto en un Sitio web de Azure, la implementación continua es útil cuando hay muchas personas trabajando en un proyecto y desea asegurarse de que se publica siempre la última versión, con independencia de quién realizó la actualización más reciente. La implementación continua también es útil si está utilizando una de las herramientas anteriormente mencionadas como repositorio central para su aplicación.

Implementar archivos de GitHub, CodePlex o BitBucket requiere que haya publicado su proyecto local en uno de estos servicios. Para obtener más información sobre cómo publicar el proyecto a estos servicios, consulte [Create a Repo (GitHub)], [Using Git with CodePlex], [Create a Repo (BitBucket)], [Using Dropbox to Share Git Repositories] o [Quick Start - Mercurial].

1. Primero coloque los archivos del sitio web en el repositorio seleccionado que se utilizará para la implementación continua.

2. En el Portal de Azure del sitio web, vaya a la pestaña **PANEL**. En la sección **vista rápida**, seleccione **Configuración de la implementación desde un control de código fuente**.  Aparece el cuadro de diálogo **Configurar implementación** que le pregunta **¿Dónde está su código fuente?**. 

2. Elija el método de control de código fuente que desea utilizar para la implementación continua.
	
3. Cuando se le indique, introduzca sus credenciales para el servicio seleccionado.

4. Cuando haya autorizado a Azure a obtener acceso a su cuenta, se le ofrecerá una lista de repositorios. 

	![git-ChooseARepositoryToDeploy][git-ChooseARepositoryToDeploy]
  
5. Seleccione el repositorio que desea asociar a su Sitio web de Azure. Haga clic en la marca de verificación para continuar.

	> [AZURE.NOTE] Al habilitar la implementación continua con GitHub o BitBucket, se mostrarán tanto los proyectos públicos como privados.

6. Azure crea una asociación al repositorio seleccionado y extrae los archivos desde la rama principal. Una vez finalizado este proceso, el **historial de implementación** en la página **Implementaciones** mostrará el mensaje de **Implementación activa** como el siguiente:

	![git-githubdeployed][git-githubdeployed]

7. En este punto, el proyecto se ha implementado desde el repositorio que haya elegido en su Sitio web de Azure. Para comprobar que el sitio está activo, haga clic en el vínculo **Examinar** situado en la parte inferior del portal. El explorador debería desplazarse al sitio web.

8. Para comprobar que la implementación continua está teniendo lugar, modifique algo en el proyecto e inserte la actualización en el repositorio asociado a este sitio web. El sitio web debería actualizarse para reflejar los cambios poco después de que finalice la inserción en el repositorio. Puede comprobar que se ha extraído la actualización en la página **Implementaciones** de su Sitio web.

	![git-GitHubDeployed-Updated][git-GitHubDeployed-Updated]

<h3><a id="Step75"></a>Implementación de una solución de Visual Studio desde BitBucket, CodePlex, Dropbox, GitHub o Mercurial</h3>

Insertar una solución de Visual Studio en un sitio web de Azure es tan fácil como insertar un simple archivo index.html. El proceso de implementación de sitios web de Azure simplifica todas las particularidades, como las dependencias de NuGet de restauración y la construcción de los binarios de la aplicación. Puede seguir las buenas prácticas de control de código fuente para el mantenimiento del código solo en su repositorio Git y permitir que la implementación de sitios web de Azure se ocupe del resto.

Los pasos para la inserción de la solución de Visual Studio en un sitio Web de Azure son los mismos que en la [sección anterior](#Step7), siempre que configure la solución y el repositorio de la manera siguiente:

-	En la raíz del repositorio, agregue un archivo  `.gitignore`, a continuación, especifique todos los archivos y carpetas que desea excluir del repositorio, como las carpetas  `Obj`, `Bin` y  `packages` (consulte la [documentación de gitignore](http://git-scm.com/docs/gitignore) para obtener información de formato). Por ejemplo:

		[Oo]bj/
		[Bb]in/
		*.user
		/TestResults
		*.vspscc
		*.vssscc
		*.suo
		*.cache
		*.csproj.user
		packages/*
		App_Data/
		/apps
		msbuild.log
		_app/
		nuget.exe

	>[AZURE.NOTE] Si usa GitHub, puede generar de forma opcional un archivo .gitignore específico de Visual Studio cuando crea el repositorio, en el cual se incluyen todos los archivos temporales comunes, los resultados de compilación, etc. Puede entonces personalizarlos para cubrir sus necesidades concretas.

-	Agregue el árbol de directorios de la solución entero a su repositorio, con el archivo .sln en la raíz del repositorio.

-	En su solución de Visual Studio, active [NuGet Package Restore](http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages) para que Visual Studio restaure automáticamente los paquetes que falten.

Cuando haya configurado el repositorio como se indica y haya configurado su sitio web de Azure para la publicación continua desde uno de los repositorios Git en línea, puede desarrollar su aplicación de ASP.NET de forma local en Visual Studio e implementar de forma continua su código, simplemente aplicando los cambios en su repositorio Git en línea.

<h4>Cómo funciona la implementación continua</h4>
La implementación continua funciona al proporcionar la **URL DEL DESENCADENADOR DE IMPLEMENTACIÓN** que se encuentra en la sección **Implementaciones** de la pestaña** ConfigurAR** de su sitio.

![git-DeploymentTrigger][git-DeploymentTrigger]

Cuando se realizan actualizaciones en el repositorio, se envía una solicitud POST a esta URL, que notifica al Sitio web de Azure que el repositorio se ha actualizado. En este momento, recupera la actualización y la implementa en el sitio web.

Para obtener más información sobre el motor detrás del proceso de implementación de Git para sitios web de Azure, consulte [Project Kudu](https://github.com/projectkudu/kudu/wiki).

<h4>Especificación de la rama que se va a utilizar</h4>

Cuando habilite la implementación continua, presentará de forma predeterminada la rama **principal** del repositorio. Si desea utilizar otra rama, realice los pasos siguientes:

1. En el portal, seleccione su sitio web y, a continuación, seleccione **CONFIGURAR**.

2. En la sección **Implementaciones** de la página, introduzca la rama que desea utilizar en el campo **RAMA PARA IMPLEMENTAR** y, a continuación, presione Entrar. Finalmente, haga clic en **GUARDAR**.

	Azure debe comenzar inmediatamente a actualizarse en función de los cambios en la nueva rama.

<h4>Deshabilitación de la implementación continua</h4>

La implementación continua se puede deshabilitar en el **Panel** de Azure. En la sección **vista rápida**, elija la opción para desconectarse del repositorio que está utilizando:

![git-DisconnectFromGitHub][git-DisconnectFromGitHub]	

Después de responder **Sí** al mensaje de confirmación, puede volver a **vista rápida** y hacer clic en **Configurar implementación desde control de código fuente** si desea configurar la publicación desde otro origen.

<h2><a id="Step8"></a>Solución de problemas</h2>

A continuación se indican los errores o problemas que se suelen encontrar al utilizar Git para publicar en un Sitio web de Azure:

****

**Síntoma**: No se pudo tener acceso a [siteURL]': Error de conexión a [scmAddress]

**Causa**: Este error puede ocurrir si el sitio web no está en línea y en ejecución.

**Resolución**: Inicie el sitio web en el portal de Azure. La implementación de Git no funcionará a menos que el sitio web esté en ejecución. 


****

**Síntoma**: No se pudo resolver el host  'hostname'

**Causa**: Este error puede ocurrir si la información de dirección introducida al crear el repositorio remoto  'azure' era incorrecta.

**Resolución**: Use  `git remote -v` para obtener un listado de todos los remotos, junto con la URL asociada. Compruebe que la URL para el repositorio correcto  'azure' es correcta. Si lo necesita, suprima y vuelva a crear este repositorio remoto utilizando la URL correcta.

****

**Síntoma**: No hay referencias en común y no se ha especificado nada; sin hacer nada. Quizá hay que especificar una rama como  'master'.

**Causa**: Este error puede ocurrir si no especifica una rama al realizar una operación de inserción en Git y no hay establecido el valor push.default utilizado por Git.

**Resolución**: Vuelva a realizar la operación de inserción, especificando la rama principal. Por ejemplo:

	git push azure master

****

**Síntoma**: src refspec [branchname] no coincide con ninguna.

**Causa**: Este error puede tener lugar si intenta insertar una rama que no es la principal en el repositorio remoto  'azure'.

**Resolución**: Vuelva a realizar la operación de inserción, especificando la rama principal. Por ejemplo:

	git push azure master

****

**Síntoma**: Error: los cambios se han confirmado en el repositorio remoto pero el sitio web no se ha actualizado.

**Causa**: Este error puede ocurrir si está implementando una aplicación Node.js que contiene un archivo package.json que especifica módulos requeridos adicionales.

**Resolución**: Los mensajes adicionales que contienen 'npm ERR!' deberían registrarse antes de este error y pueden proporcionar contexto adicional sobre el error. A continuación se indican las causas conocidas de este error y el mensaje 'npm ERR!' correspondiente:

* **Archivo package.json corrupto**: npm ERR! No se pudieron leer las dependencias.

* **Módulo nativo que no tiene una distribución binaria para Windows**:

	* npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1

		O bien

	* npm ERR! [modulename@version] preinstall: \`make || gmake\`


## Recursos adicionales

* [Uso de PowerShell para Azure]
* [Uso de las herramientas de línea de comandos de Azure para Mac y Linux]
* [Documentación de GIT]
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)

[Centro para desarrolladores de Azure]: http://azure.microsoft.com/develop/overview/
[Portal de Azure]: http://manage.windowsazure.com
[Sitio web de Git]: http://git-scm.com
[Instalación de Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Uso de PowerShell para Azure]: http://azure.microsoft.com/develop/nodejs/how-to-guides/powershell-cmdlets/
[Uso de las herramientas de línea de comandos de Azure para Mac y Linux]: /es-es/develop/nodejs/how-to-guides/command-line-tools/
[Documentación de GIT]: http://git-scm.com/documentation

[portal-select-website]: ./media/publishing-with-git/git-select-website.png
[git-WhereIsYourSourceCode]: ./media/publishing-with-git/git-WhereIsYourSourceCode.png
[git-instructions]: ./media/publishing-with-git/git-instructions.png
[portal-deployment-credentials]: ./media/publishing-with-git/git-deployment-credentials.png

[git-ChooseARepositoryToDeploy]: ./media/publishing-with-git/git-ChooseARepositoryToDeploy.png
[hello-git]: ./media/publishing-with-git/git-hello-git.png
[yay]: ./media/publishing-with-git/git-yay.png
[git-githubdeployed]: ./media/publishing-with-git/git-GitHubDeployed.png
[git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png
[git-DisconnectFromGitHub]: ./media/publishing-with-git/git-DisconnectFromGitHub.png
[git-DeploymentTrigger]: ./media/publishing-with-git/git-DeploymentTrigger.png
[Creación de un repositorio (GitHub)]: https://help.github.com/articles/create-a-repo
[Uso de Git con CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Creación de un repositorio (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Inicio rápido - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
[Uso de Dropbox para compartir respositorios Git]: https://gist.github.com/trey/2722927
[Entrega continua a Azure con Visual Studio Online]: http://azure.microsoft.com/develop/net/common-tasks/publishing-with-tfs/
<!--HONumber=42-->
