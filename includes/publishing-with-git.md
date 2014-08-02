
# Publicación del control de código fuente a Sitios web Azure

Los Sitios web Azure admiten el desarrollo continuo desde el control de código fuente y herramientas de repositorio como BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial y TFS. Puede utilizar estas herramientas para mantener el contenido y el código de su sitio web y, después, insertar los cambios de forma rápida y fácil en el sitio cuando lo desee.

En este artículo, aprenderá a utilizar Git para publicar directamente desde su equipo local a un Sitio web Azure (en Azure, este método de publicación se denomina **Git local**). También conocerá cómo habilitar la implementación continua desde sitios web de repositorio, como BitBucket, CodePlex, DropBox, GitHub o Mercurial. Para obtener información acerca de la utilización de TFS para la implementación continua, consulte [Continuous delivery to Azure using Visual Studio Online][1].

> [WACOM.NOTE] Muchos de los comandos Git descritos en este artículo se realizan automáticamente al crear un sitio web mediante las herramientas de línea de comandos que se describen en [Herramientas de línea de comandos de Azure para Mac y Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/).

Esta tarea incluye los siguientes pasos:

* [Instalación de Git](#Step1)
* [Creación de un repositorio local](#Step2)
* [Incorporación de una página web](#Step3)
* [Habilitación del repositorio del sitio web](#Step4)
* [Implementación del proyecto](#Step5)
	* [Inserción de archivos locales en Azure (Git local)](#Step6)
	* [Implementación de archivos desde un sitio web de repositorio como
    BitBucket, CodePlex, Dropbox, GitHub o Mercurial](#Step7)
* [Solución de problemas](#Step8)

<h2><a id="Step2" ></a>Instalación de Git</h2>


Los pasos requeridos para instalar Git varían según los sistemas operativos. Consulte [Installing Git][2] para obtener una guía sobre la instalación y las distribuciones específicas del sistema operativo.

> [WACOM.NOTE] Algunos sistemas operativos disponen de versiones de Git en línea de comandos y de GUI. Las instrucciones proporcionadas en este artículo utilizan la versión en línea de comandos.

<h2><a id="Step2" ></a>Creación de un repositorio local</h2>


Realice las tareas siguientes al crear un nuevo repositorio Git.

1.  Cree un directorio denominado MyGitRepository que contenga su repositorio Git y los archivos del sitio web.

2.  Abra una línea de comandos, como **GitBash** (Windows) o **Bash** (shell de Unix). En los sistemas OS X puede tener acceso a la línea de comandos mediante la aplicación **Terminal**.

3.  Desde la línea de comandos, cambie al directorio MyGitRepository.
    
         cd MyGitRepository

4.  Utilice el comando siguiente para inicializar un nuevo repositorio Git:
    
         git init
    
    Debería devolver un mensaje como **Initialized empty Git repository in [path]**.

<h2><a id="Step3" ></a>Incorporación de una página web</h2>


Los Sitios web Azure admiten aplicaciones creadas en una variedad de lenguajes de programación. Para este ejemplo, se utilizará un archivo .html estático. Para obtener información acerca de la publicación de sitios web en otros lenguajes de programación en Azure, consulte la página [Azure Developer Center][3].

1.  Con un editor de texto, cree un nuevo archivo denominado **index.html** en el directorio raíz del repositorio Git (el directorio MyGitRepository que ha creado anteriormente).

2.  Agregue el texto siguiente como contenido para el archivo index.htm y guárdelo.
    
         Hello Git!

3.  Desde la línea de comandos, compruebe que está en la raíz de su repositorio Git. A continuación, utilice el comando siguiente para agregar el archivo **index.html** al repositorio:
    
         git add index.html


    
    > [WACOM.NOTE] Puede encontrar la ayuda para cualquier comando git escribiendo -help o --help tras el comando. Por ejemplo, para ver las opciones de parámetro para el comando "add", escriba `git add -help` para obtener ayuda para la línea de comandos o `git add --help` para recibir ayuda más detallada.

4.  A continuación, confirme los cambios en el repositorio mediante el siguiente comando:
    
         git commit -m "Agregando index.html al repositorio"
    
    Debería ver una salida similar a la siguiente:
    
         [master (root-commit) 369a79c] Agregando index.html al repositorio
          1 file changed, 1 insertion(+)
          create mode 100644 index.html

<h2><a id="Step4" ></a>Habilitación del repositorio del sitio web</h2>


Lleve a cabo los pasos siguientes para habilitar un repositorio Git para su sitio web mediante el portal de Azure:

1.  Inicie sesión en el [Portal de Azure][4].

2.  A la izquierda de la página, seleccione **Sitios web** y, a continuación, seleccione el sitio web para el que desea habilitar un repositorio.
    
    ![Imagen mostrando un sitio web seleccionado](./media/publishing-with-git/git-select-website.png)

3.  Haga clic en la pestaña **DASHBOARD**.

4.  En la sección **quick glance**, seleccione **Set up deployment from source control**. Aparece el siguiente cuadro de diálogo **SET UP DEPLOYMENT**.
    
    ![git-WhereIsYourSourceCode](./media/publishing-with-git/git-WhereIsYourSourceCode.png)

5.  Elija **Local Git** y, a continuación, haga clic en la flecha **Siguiente**.

6.  Después de unos momentos, verá el mensaje indicando que el repositorio ya está listo.
    
    ![git-instructions](./media/publishing-with-git/git-instructions.png)

<h2><a id="Step5" ></a>Implementación del proyecto</h2>


<h3><a id="Step6" ></a>Inserción de archivos locales en Azure (Git local)</h3>


En este punto, el portal muestra instrucciones para inicializar un repositorio local y agregar archivos. Ya lo ha hecho anteriormente en los pasos anteriores de este tema. Sin embargo, si no ha configurado las credenciales de implementación, debe seguir el paso 3 de las instrucciones. Esto le llevará a seguir un vínculo con el texto **Reset your deployment credentials**.

Siga los pasos siguientes para publicar su sitio web en Azure utilizando Local Git:

1.  Utilizando la línea de comandos, compruebe que está en la raíz de su repositorio Git que contiene el archivo index.html creado anteriormente.

2.  Copie el comando git remote add del paso 3 de las instrucciones devuelto por el portal. Tendrá un aspecto similar al siguiente:
    
         git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git


   
    > [WACOM.NOTE] El comando **remote** agrega una referencia con nombre a un repositorio remoto. En este ejemplo, crea una referencia denominada "azure" al repositorio del Sitio web Azure.

3.  Utilice los comandos siguientes de la línea de comandos para insertar el contenido actual del repositorio desde el repositorio local al repositorio remoto "azure":
    
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


   
    > [WACOM.NOTE] El repositorio creado por el Sitio web Azure espera solicitudes de inserción para dirigir la rama **principal** de este repositorio, que se utilizará después como contenido del sitio web.

4.  En el portal, haga clic en el vínculo **BROWSE**, en la parte inferior del portal, para comprobar que se ha implementado **index.html**. Se mostrará una página que contiene "Hello Git!".
    
    ![Página web con "Hello Git!"](./media/publishing-with-git/git-hello-git.png)

5.  Con un editor de texto, cambie el archivo **index.html** para que contenga "Yay!" y, a continuación, guarde el archivo.

6.  Utilice los comandos siguientes desde la línea de comandos para **agregar** y **confirmar** los cambios y, a continuación **insertar** los cambios en el repositorio remoto:
    
         git add index.html
         git commit -m "Celebration"
         git push azure master
    
    Una vez completado el comando **push**, actualice el explorador (puede tener que presionar Ctrl+F5 para que el explorador se actualice correctamente) y observe que el contenido de la página ahora refleja el último cambio confirmado.
    
    ![Página web con "Yay!"](./media/publishing-with-git/git-yay.png)

<h3><a id="Step7" ></a>Implementación de archivos desde un sitio web de repositorio como BitBucket, CodePlex, Dropbox, GitHub o Mercurial</h3>


La inserción de archivos locales en Azure mediante Git local le permite introducir manualmente actualizaciones desde un proyecto local a su Sitio web Azure, mientras que la implementación desde BitBucket, CodePlex, Dropbox, GitHub o Mercurial da como resultado un proceso continuo de implementación en el que Azure extraerá las actualizaciones más recientes de su proyecto.

Aunque ambos métodos dan como resultado la implementación de su proyecto en un Sitio web Azure, la implementación continua es útil cuando hay muchas personas trabajando en un proyecto y desea asegurarse de que se publica siempre la última versión, con independencia de quién realizó la actualización más reciente. La implementación continua también es útil si está utilizando una de las herramientas anteriormente mencionadas como repositorio central para su aplicación.

Implementar archivos de GitHub, CodePlex o BitBucket requiere que haya publicado su proyecto local en uno de estos servicios. Para obtener más información acerca de la publicación de su proyecto en estos servicios, consulte [Create a Repo (GitHub)][5], [Using Git with CodePlex][6],
[Create a Repo (BitBucket)][7], [Using Dropbox to Share Git
Repositories][8] o [Quick Start - Mercurial][9].

1.  Primero coloque los archivos del sitio web en el repositorio seleccionado que se utilizará para la implementación continua.

2.  En el Portal de Azure del sitio web, vaya a la pestaña **DASHBOARD**. En la sección **quick glance**, seleccione **Set up deployment from source control**. Se muestra el cuadro de diálogo **Set Up Deployment dialog** que pregunta **Where is your source code?**.

3.  Elija el método de control de código fuente que desea utilizar para la implementación continua.

4.  Cuando se le indique, introduzca sus credenciales para el servicio seleccionado.

5.  Cuando haya autorizado a Azure a obtener acceso a su cuenta, se le ofrecerá una lista de repositorios.
    
    ![git-ChooseARepositoryToDeploy](./media/publishing-with-git/git-ChooseARepositoryToDeploy.png)

6.  Seleccione el repositorio que desea asociar a su Sitio web Azure. Haga clic en la marca de verificación para continuar.


   
    > [WACOM.NOTE] Al habilitar la implementación continua con GitHub o BitBucket, se mostrarán tanto los proyectos públicos como privados.

7.  Azure crea una asociación al repositorio seleccionado y extrae los archivos desde la rama principal. Una vez finalizado este proceso, el **historial de implementación** en la página **Implementaciones** mostrará un mensaje de **Active Deployment** como el siguiente:
    
    ![git-githubdeployed](./media/publishing-with-git/git-GitHubDeployed.png)

8.  En este punto, el proyecto se ha implementado desde el repositorio que haya elegido en su Sitio web Azure. Para comprobar que el sitio está activo, haga clic en el vínculo **Browse** situado en la parte inferior del portal. El explorador debería desplazarse al sitio web.

9.  Para comprobar que la implementación continua está teniendo lugar, modifique algo en el proyecto e inserte la actualización en el repositorio asociado a este sitio web. El sitio web debería actualizarse para reflejar los cambios poco después de finalizada la inserción en el repositorio. Puede comprobar que se ha extraído la actualización en la página **Implementaciones** de su Sitio web.

    ![git-GitHubDeployed-Updated][git-GitHubDeployed-Updated]

<h4>Cómo funciona la implementación continua</h4>

La implementación continua funciona al proporcionar la **DEPLOYMENT TRIGGER URL** que se encuentra en la sección **Implementaciones** de la pestaña **Configure** de su sitio.

![git-DeploymentTrigger](./media/publishing-with-git/git-DeploymentTrigger.png)

Cuando se realizan actualizaciones en el repositorio, se envía una solicitud POST a esta URL, que notifica al Sitio web Azure que el repositorio se ha actualizado. En este momento, recupera la actualización y la implementa en el sitio web.

<h4>Especificación de la rama que se va a utilizar</h4>


Cuando habilite la implementación continua, presentará de forma predeterminada la rama **principal** al repositorio. Si desea utilizar otra rama, realice los pasos siguientes:

1.  En el portal, seleccione su sitio web y, a continuación, seleccione **CONFIGURE**.

2.  En la sección **Implementaciones** de la página, introduzca la rama que desea utilizar en el campo **BRANCH TO DEPLOY** y, a continuación, presione Entrar. Finalmente, haga clic en **SAVE**.
    
    Azure debe comenzar inmediatamente a actualizarse en función de los cambios en la nueva rama.

<h4>Deshabilitación de la implementación continua</h4>


La implementación continua se puede deshabilitar en el **Panel** de Azure. En la sección **quick glance**, elija la opción para desconectarse del repositorio que está utilizando:

![git-DisconnectFromGitHub](./media/publishing-with-git/git-DisconnectFromGitHub.png)

Después de responder **Yes** al mensaje de confirmación, puede volver a **quick glance** y hacer clic en **Set up deployment from source control** si desea configurar la publicación desde otro origen.

<h2><a id="Step8" ></a>Solución de problemas</h2>


A continuación se indican los errores o problemas que se suelen encontrar al utilizar Git para publicar en un Sitio web Azure:

* * *

**Síntoma**: No se pudo resolver el host "nombre de host".

**Causa**: Este error puede ocurrir si la información de dirección introducida al crear el repositorio remoto "azure" era incorrecta.

**Resolución**: Utilice el comando `git remote -v` para enumerar todos los repositorios remotos, junto con la URL asociada. Compruebe que la URL para el repositorio correcto "azure" es correcta. Si lo necesita, suprima y vuelva a crear este repositorio remoto utilizando la URL correcta.

* * *

**Síntoma**: No hay referencias en común y no se ha especificado nada; sin hacer nada. Quizá hay que especificar una rama como "principal".

**Causa**: Este error puede ocurrir si no especifica una rama al realizar una operación de inserción en Git y no hay establecido el valor push.default utilizado por Git.

**Resolución**: Vuelva a realizar la operación de inserción, especificando la rama principal. Por ejemplo:

    git push azure master

* * *

**Síntoma**: src refspec [nombre rama] no coincide con nada.

**Causa**: Este error puede tener lugar si intenta insertar una rama que no es la principal en el repositorio remoto "azure".

**Resolución**: Vuelva a realizar la operación de inserción, especificando la rama principal. Por ejemplo:

    git push azure master

* * *

**Síntoma**: Error - Los cambios se han confirmado en el repositorio remoto pero el sitio web no se ha actualizado.

**Causa**: Este error puede ocurrir si está implementando una aplicación Node.js que contiene un archivo package.json que especifica módulos requeridos adicionales.

**Resolución**: Los mensajes adicionales que contienen 'npm ERR!' deberían registrarse antes de este error y pueden proporcionar contexto adicional sobre el error. A continuación se indican las causas conocidas de este error y el mensaje 'npm ERR!' correspondiente:

* **Archivo package.json formado incorrectamente**: npm ERR! No se pudieron leer las dependencias.

* **Módulo nativo que no tiene una distribución binaria para Windows**:
  
  * npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1
    
    O
  
  * npm ERR! [modulename@version] preinstall: \`make \|\| gmake\`

## Recursos adicionales

* [Uso de PowerShell para Azure][10]
* [Uso de las herramientas de línea de comandos de Azure para Mac y
  Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/)
* [Git Documentation][11]



[1]: http://www.windowsazure.com/en-us/develop/net/common-tasks/publishing-with-tfs/
[2]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[3]: http://www.windowsazure.com/en-us/develop/overview/
[4]: http://manage.windowsazure.com
[5]: https://help.github.com/articles/create-a-repo
[6]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[7]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[8]: https://gist.github.com/trey/2722927
[9]: http://mercurial.selenic.com/wiki/QuickStart
[10]: http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/powershell-cmdlets/
[11]: http://git-scm.com/documentation

[git-ChooseARepositoryToDeploy]: ./media/publishing-with-git/git-ChooseARepositoryToDeploy.png
[hello-git]: ./media/publishing-with-git/git-hello-git.png
[yay]: ./media/publishing-with-git/git-yay.png
[git-githubdeployed]: ./media/publishing-with-git/git-GitHubDeployed.png
[git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png
[git-DisconnectFromGitHub]: ./media/publishing-with-git/git-DisconnectFromGitHub.png
[git-DeploymentTrigger]: ./media/publishing-with-git/git-DeploymentTrigger.png
[Create a Repo (GitHub)]: https://help.github.com/articles/create-a-repo
[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Create a Repo (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
[Using Dropbox to Share Git Repositories]: https://gist.github.com/trey/2722927
[Continuous delivery to Azure using Visual Studio Online]: http://www.windowsazure.com/en-us/develop/net/common-tasks/publishing-with-tfs/