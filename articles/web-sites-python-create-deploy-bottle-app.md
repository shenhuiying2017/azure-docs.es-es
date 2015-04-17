<properties 
	pageTitle="Sitios Web de Python con Bottle: tutorial de Azure" 
	description="Un tutorial que le introduce a la ejecución de un sitio web de Python en Azure." 
	services="web-sites" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="12/17/2014" 
	ms.author="huvalo"/>




# Creación de sitios web con Bottle

En este tutorial se describe cómo empezar a ejecutar Python en Sitios web Azure.  Sitios web Azure ofrece un hospedaje gratuito limitado y una implementación rápida. Además, también es posible usar Python.  A medida que su aplicación crece, puede cambiar a un tipo de hospedaje de pago e integrar el resto de los servicios de Azure.

Creará una aplicación con el marco de trabajo web de Bottle (consulte las versiones alternativas de este tutorial para [Django](../web-sites-python-create-deploy-django-app) y [Flask](../web-sites-python-create-deploy-flask-app)).  Creará el sitio web de la galería de Azure, configurará la implementación Git y clonará el repositorio de forma local.  A continuación, ejecutará la aplicación localmente, realizará cambios, los confirmará y los transmitirá a Azure.  En el tutorial se muestra cómo hacer esto desde Windows, Mac o Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]


+ [Requisitos previos](#prerequisites)
+ [Creación de sitios web en el portal](#website-creation-on-portal)
+ [Información general sobre aplicaciones](#application-overview)
+ Desarrollo de sitios web
  + [Windows: Python Tools para Visual Studio](#website-development-windows-ptvs)
  + [Windows: línea de comandos](#website-development-windows-command-line)
  + [Mac o Linux: línea de comandos](#website-development-mac-linux-command-line)
+ [Solución de problemas: implementación](#troubleshooting-deployment)
+ [Solución de problemas: instalación de paquetes](#troubleshooting-package-installation)
+ [Solución de problemas: entorno virtual](#troubleshooting-virtual-environment)
+ [Pasos siguientes](#next-steps)


<h2><a name="prerequisites"></a>Requisitos previos</h2>

- Windows, Mac o Linux
- Python 2.7 o 3.4
- setuptools, pip, virtualenv (solo en Python 2.7)
- Git
- Python Tools para Visual Studio (opcional)

**Nota**: la publicación TFS no se admite actualmente para los proyectos de Python.

### Windows

Si aún no tiene Python 2.7 o 3.4 instalado (32 bits), se recomienda instalar el [SDK de Azure para Python 2.7](http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409) o el [SDK de Azure para Python 3.4](http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409) con el instalador de plataforma web.  Así, se instala la versión de 32 bits de Python, setuptools, pip, virtualenv, etc. (Python de 32 bits es el que está instalado en las máquinas host de Azure).  Como alternativa, puede obtener Python en [python.org](http://www.python.org/).

Para Git, se recomienda [Git para Windows](http://msysgit.github.io/) o [GitHub para Windows](https://windows.github.com/).  Si usa Visual Studio, puede utilizar la compatibilidad con Git integrada.

También se recomienda instalar [Python Tools para Visual Studio](http://pytools.codeplex.com).  Esto es opcional, pero si tiene [Visual Studio](http://www.visualstudio.com/), incluido Visual Studio Express 2013 para web gratuito, obtendrá un IDE de Python excepcional.

### Mac o Linux

Debe tener Git y Python instalados, pero asegúrese de que sea Python 2.7 o 3.4.


<h2><a name="website-creation-on-portal"></a>Creación de sitios web en el portal</h2>

El primer paso para crear la aplicación consiste en crear el sitio web a través del Portal de administración de Azure.  Para ello, deberá iniciar sesión en el portal y hacer clic en el botón **NEW** de la esquina inferior izquierda. Aparecerá una ventana. Haga clic en **COMPUTE**, **WEB SITE** **FROM GALLERY**.

![](./media/web-sites-python-create-deploy-bottle-app/portal-create-site.png)

Aparecerá una ventana donde se indicarán las aplicaciones disponibles en la galería. Haga clic en la categoría **APP FRAMEWORKS** de la izquierda y seleccione **Bottle**.

![](./media/web-sites-python-create-deploy-bottle-app/portal-gallery-bottle.png)

En la página siguiente, escriba un nombre y una región para su sitio, y haga clic en el botón Complete.

El sitio se configurará rápidamente. Puede hacer clic en el botón **BROWSE** de la barra de herramientas de la parte inferior y verá la nueva aplicación de Bottle que se ejecuta en Azure.

![](./media/web-sites-python-create-deploy-bottle-app/portal-website-bottle.png)
 
A continuación, se agregará compatibilidad para publicar a través de Git.  Para ello, elija **Configurar implementación desde control de código fuente**.

![](./media/web-sites-python-create-deploy-bottle-app/portal-site-created.png)

Desde el cuadro de diálogo **Set up deployment**, desplácese hacia abajo y seleccione la opción **Local Git repository**. Haga clic en la flecha derecha para continuar.

![](./media/web-sites-python-create-deploy-bottle-app/portal-setup-deployment.png)

Una vez configurada la publicación Git, aparecerá brevemente una página donde se le informa de que el repositorio se está creando. Cuando esté listo, verá instrucciones sobre cómo conectarse.  

![](./media/web-sites-python-create-deploy-bottle-app/portal-repo-created.png)

Se seguirán estas instrucciones en las secciones siguientes.


<h2><a name="application-overview"></a>Información general sobre aplicaciones</h2>

### Contenido del repositorio de GIT

Le presentamos una visión general de los archivos que encontrará en el repositorio de Git inicial, que se clonará en la sección siguiente.

    \routes.py
    \static\content\
    \static\fonts\
    \static\scripts\
    \views\about.tpl
    \views\contact.tpl
    \views\index.tpl
    \views\layout.tpl

Orígenes principales de la aplicación.  Consta de 3 páginas (index, about, contact) con un diseño principal.  Los scripts y el contenido estático incluyen bootstrap, jquery, modernizr y respond.

    \app.py

Compatibilidad con los servidores de desarrollo locales. Su uso permite ejecutar la aplicación de forma local.

    \BottleWebProject.pyproj
    \BottleWebProject.sln

Archivos de proyecto para su uso con [Python Tools para Visual Studio](http://pytools.codeplex.com).

    \ptvs_virtualenv_proxy.py

Compatibilidad con el proxy IIS para entornos virtuales y la depuración remota PTVS.

    \requirements.txt

Paquetes externos necesarios para esta aplicación. El script de implementación instalará con pip los paquetes que se indiquen en este archivo.
 
    \web.2.7.config
    \web.3.4.config

Archivos de configuración de IIS.  El script de implementación usará la web.x.y.config adecuada y la copiará como web.config.

### Archivos opcionales: personalización de la implementación

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../includes/web-sites-python-customizing-deployment.md)]

### Archivos opcionales: tiempo de ejecución de Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

### Archivos adicionales en el servidor

Algunos archivos existen en el servidor pero no se agregan al repositorio git.  Estos se crean mediante el script de implementación.

    \web.config

Archivo de configuración de IIS.  Se crea a partir de web.x.y.config en cada implementación.

    \env\

Entorno virtual de Python.  Se crea durante la implementación si aún no existe un entorno virtual compatible en el sitio.  Los paquetes que aparecen en requirements.txt se instalan con pip, pero pip omitirá la instalación si los paquetes ya están instalados.

En las tres secciones siguientes se describe cómo continuar con el desarrollo del sitio web en tres entornos diferentes:

- Windows, con Python Tools para Visual Studio
- Windows, con la línea de comandos
- Mac o Linux, con la línea de comandos


<h2><a name="website-development-windows-ptvs"></a>Implementación del sitio web - Windows - Python Tools para Visual Studio</h2>

### Clonación del repositorio

En primer lugar, clone el repositorio mediante la dirección URL proporcionada en el portal de Azure.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-git-clone.png)

Abra el archivo de solución (.sln) que se incluye en la raíz del repositorio.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-solution-bottle.png)

### Creación del entorno virtual

Ahora se creará un entorno virtual para el desarrollo local.  Haga clic con el botón derecho en **Entornos de Python** y seleccione **Agregar entorno virtual...**.

- Asegúrese de que es el nombre del entorno es `env`.

- Seleccione el intérprete base.  Asegúrese de usar la misma versión de Python que se haya seleccionado para el sitio (en runtime.txt o en la página de configuración del sitio).

- Asegúrese de que esté activada la opción para descargar e instalar paquetes.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-add-virtual-env-27.png)

Haga clic en **Crear**.  Esto creará el entorno virtual e instalará las dependencias que se indican en requirements.txt.

### Ejecución con el servidor de desarrollo

Presione F5 para iniciar la depuración; el explorador web se abrirá automáticamente en la página que se ejecuta localmente.

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

Puede establecer puntos de interrupción en los orígenes, usar las ventanas de inspección, etc. Consulte la [documentación PTVS](http://pytools.codeplex.com/documentation) para obtener más información sobre las distintas características.

### Realización de cambios

Ahora puede experimentar mediante la realización de cambios en las plantillas o los orígenes de la aplicación.

Cuando haya probado los cambios, confírmelos en el repositorio Git:

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-commit-bottle.png)

### Instalación de más paquetes

La aplicación puede tener dependencias que vayan más allá de Bottle y Python.

Puede instalar paquetes adicionales con pip.  Para instalar un paquete, haga clic con el botón secundario en el entorno virtual y seleccione **Install Python Package**.

Por ejemplo, para instalar el SDK de Azure para Python, que proporciona acceso al almacenamiento de Azure, al bus de servicio y a otros servicios de Azure, escriba `azure`:

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-install-package-dialog.png)

Haga clic con el botón secundario en el entorno virtual y seleccione **Generate requirements.txt** para actualizar requirements.txt.

A continuación, confirme los cambios de requirements.txt en el repositorio de Git.

### Implementación en Azure

Para desencadenar una implementación, haga clic en **Sync** o **Push**.  Sync realiza una inserción y una extracción.

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-git-push.png)

La primera implementación tardará un rato, ya que creará un entorno virtual, paquetes de instalación, etc.

En Visual Studio no se muestra el progreso de la implementación.  Si desea revisar la salida, consulte la sección sobre [Resolución de problemas: implementación](#troubleshooting-deployment).

Vaya a la dirección URL de Azure para ver los cambios.


<h2><a name="website-development-windows-command-line"></a>Desarrollo de sitios web - Windows - Línea de comandos</h2>

### Clonación del repositorio

En primer lugar, clone el repositorio mediante la dirección URL proporcionada en el portal de Azure y agregue el repositorio de Azure como remoto.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Creación del entorno virtual

Se creará un nuevo entorno virtual para fines de desarrollo (no lo agregue al repositorio).  Los entornos virtuales de Python no son reubicables, por lo que cada desarrollador que trabaje en la aplicación creará el suyo propio de forma local.

Asegúrese de usar la misma versión de Python que se haya seleccionado para el sitio (en runtime.txt o en la página de configuración del sitio).

Para Python 2.7:

    c:\python27\python.exe -m virtualenv env

Para Python 3.4:

    c:\python34\python.exe -m venv env

Instale los paquetes externos que requiera la aplicación. Puede usar el archivo requirements.txt en la raíz del repositorio para instalar los paquetes en su entorno virtual:

    env\scripts\pip install -r requirements.txt

### Ejecución con el servidor de desarrollo

Puede iniciar la aplicación en un servidor de desarrollo con el siguiente comando:

    env\scripts\python app.py

La consola mostrará la dirección URL y el puerto en el que escucha el servidor:

![](./media/web-sites-python-create-deploy-bottle-app/windows-run-local-bottle.png)

A continuación, abra el explorador web en esa dirección URL.

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

### Realización de cambios

Ahora puede experimentar mediante la realización de cambios en las plantillas o los orígenes de la aplicación.

Cuando haya probado los cambios, confírmelos en el repositorio Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Instalación de más paquetes

La aplicación puede tener dependencias que vayan más allá de Bottle y Python.

Puede instalar paquetes adicionales con pip.  Por ejemplo, para instalar el SDK de Azure para Python, que proporciona acceso al almacenamiento de Azure, al bus de servicio y a otros servicios de Azure, escriba:

    env\scripts\pip install azure

Asegúrese de actualizar requirements.txt:

    env\scripts\pip freeze > requirements.txt

Confirme los cambios:

    git add requirements.txt
    git commit -m "Added azure package"

### Implementación en Azure

Para desencadenar una implementación, inserte los cambios en Azure:

    git push azure master

Verá la salida del script de implementación, donde se incluye la creación del entorno virtual, instalación de paquetes, creación de web.config.

Vaya a la dirección URL de Azure para ver los cambios.


<h2><a name="website-development-mac-linux-command-line"></a>Desarrollo de sitios web - Mac o Linux - Línea de comandos</h2>

### Clonación del repositorio

En primer lugar, clone el repositorio mediante la dirección URL proporcionada en el portal de Azure y agregue el repositorio de Azure como remoto.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Creación del entorno virtual

Se creará un nuevo entorno virtual para fines de desarrollo (no lo agregue al repositorio).  Los entornos virtuales de Python no son reubicables, por lo que cada desarrollador que trabaje en la aplicación creará el suyo propio de forma local.

Asegúrese de usar la misma versión de Python que se haya seleccionado para el sitio (en runtime.txt o en la página de configuración del sitio).

Para Python 2.7:

    python -m virtualenv env

Para Python 3.4:

    python -m venv env

Instale los paquetes externos que requiera la aplicación. Puede usar el archivo requirements.txt en la raíz del repositorio para instalar los paquetes en su entorno virtual:

    env/bin/pip install -r requirements.txt

### Ejecución con el servidor de desarrollo

Puede iniciar la aplicación en un servidor de desarrollo con el siguiente comando:

    env/bin/python app.py

La consola mostrará la dirección URL y el puerto en el que escucha el servidor:

![](./media/web-sites-python-create-deploy-bottle-app/mac-run-local-bottle.png)

A continuación, abra el explorador web en esa dirección URL.

![](./media/web-sites-python-create-deploy-bottle-app/mac-browser-bottle.png)

### Realización de cambios

Ahora puede experimentar mediante la realización de cambios en las plantillas o los orígenes de la aplicación.

Cuando haya probado los cambios, confírmelos en el repositorio Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Instalación de más paquetes

La aplicación puede tener dependencias que vayan más allá de Bottle y Python.

Puede instalar paquetes adicionales con pip.  Por ejemplo, para instalar el SDK de Azure para Python, que proporciona acceso al almacenamiento de Azure, al bus de servicio y a otros servicios de Azure, escriba:

    env/bin/pip install azure

Asegúrese de actualizar requirements.txt:

    env/bin/pip freeze > requirements.txt

Confirme los cambios:

    git add requirements.txt
    git commit -m "Added azure package"

### Implementación en Azure

Para desencadenar una implementación, inserte los cambios en Azure:

    git push azure master

Verá la salida del script de implementación, donde se incluye la creación del entorno virtual, instalación de paquetes, creación de web.config.

Vaya a la dirección URL de Azure para ver los cambios.


<h2><a name="troubleshooting-deployment"></a>Solución de problemas: implementación</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


<h2><a name="troubleshooting-package-installation"></a>Solución de problemas: instalación de paquetes</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


<h2><a name="troubleshooting-virtual-environment"></a>Solución de problemas: entorno virtual</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]


<h2><a name="next-steps"></a>Pasos siguientes</h2>

Siga estos vínculos para obtener más información acerca de Bottle y Python Tools para Visual Studio: 
 
- [Documentación de Bottle][]
- [Documentación sobre Python Tools para Visual Studio][]

Para obtener información sobre el uso de almacenamiento de tablas de Azure y MongoDB:

- [Bottle y MongoDB en Azure con Python Tools 2.1 para Visual Studio][]
- [Bottle y Almacenamiento de tablas de Azure en Azure con Python Tools 2.1 para Visual Studio][]


<!--Link references-->
[Bottle y MongoDB en Azure con Python Tools 2.1 para Visual Studio]: ../web-sites-python-ptvs-bottle-table-storage
[Bottle y Almacenamiento de tablas de Azure en Azure con Python Tools 2.1 para Visual Studio]: ../web-sites-python-ptvs-bottle-mongodb

<!--External Link references-->
[Documentación sobre Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation 
[Documentación de Bottle]: http://bottlepy.org/docs/dev/index.html




<!--HONumber=42-->
