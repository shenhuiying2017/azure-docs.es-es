<properties 
	pageTitle="Sitios web de Python con Flask: tutorial de Azure" 
	description="Un tutorial que le introduce a la ejecución de un sitio web de Python en Azure." 
	services="app-service\web" 
	documentationCenter="python" 
	tags="python"
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Creación de sitios web con Flask

En este tutorial se describe cómo empezar a ejecutar Python en Sitios web Azure.  Estos sitios ofrecen hospedaje gratuito limitado y una implementación rápida. Además, permiten usar Python.  A medida que su aplicación crece, puede cambiar a un tipo de hospedaje de pago e integrar el resto de los servicios de Azure.

Creará una aplicación con el marco web de Flask (consulte las versiones alternativas de este tutorial para [Django](web-sites-python-create-deploy-django-app.md) y [Bottle](web-sites-python-create-deploy-bottle-app.md)).  Creará el sitio web en la galería de Azure, configurará la implementación Git y clonará el repositorio en modo local.  A continuación, ejecutará la aplicación localmente, realizará cambios, los confirmará y los insertará en Azure.  El tutorial muestra cómo llevarlo a cabo en Windows o Mac/Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]


## Requisitos previos

- Windows, Mac o Linux
- Python 2.7 o 3.4
- setuptools, pip, virtualenv (solo en Python 2.7)
- Git
- [Python Tools 2.1 para Visual Studio][] (opcional)

**Nota**: la publicación TFS no se admite actualmente para los proyectos de Python.

### Windows

Si aún no tiene Python 2.7 o 3.4 instalado (32 bits), se recomienda instalar [Azure SDK para Python 2.7][] o [Azure SDK para Python 3.4][] mediante el instalador de plataforma web.  Se instala la versión de 32 bits de Python, setuptools, pip, virtualenv, etc. (Python de 32 bits es lo que se instala en los equipos host de Azure).  También puede obtener Python en [python.org][].

Para Git recomendamos [Git para Windows][] o [GitHub para Windows][].  Si utiliza Visual Studio, puede utilizar la compatibilidad integrada con Git.

También se recomienda instalar [Python Tools 2.1 para Visual Studio][].  Esto es opcional, pero si tiene [Visual Studio][], incluidas las versiones gratuitas Visual Studio Community 2013 o Visual Studio Express 2013 para web, obtendrá un excelente IDE de Python.

### Mac o Linux:

Debe tener Python y Git instalados, pero asegúrese de que tiene Python 2.7 o 3.4.


## Creación de sitios web en el portal

El primer paso para crear la aplicación consiste en crear el sitio web a través del Portal de administración de Azure.  Para ello, deberá iniciar sesión en el portal y hacer clic en el botón **NUEVO** de la esquina inferior izquierda. Aparecerá una ventana. Haga clic en **PROCESO**, **SITIO WEB** y, a continuación, **DE LA GALERÍA**.

![](./media/web-sites-python-create-deploy-flask-app/portal-create-site.png)

Aparecerá una ventana con las aplicaciones disponibles de la galería. Haga clic en la categoría **MARCOS DE LA APLICACIÓN** de la izquierda y seleccione **Flask**.

![](./media/web-sites-python-create-deploy-flask-app/portal-gallery-flask.png)

En la página siguiente, escriba un nombre y una región para el sitio y haga clic en el botón Completar.

El sitio se configurará rápidamente. Puede hacer clic en el botón **EXAMINAR** en la barra de herramientas de la parte inferior y verá la nueva aplicación Flask que se ejecuta en Azure.

![](./media/web-sites-python-create-deploy-flask-app/portal-website-flask.png)
 
A continuación, se agregará compatibilidad para publicar a través de Git.  Para ello, elija **Configurar implementación desde control de código fuente**.

![](./media/web-sites-python-create-deploy-flask-app/portal-site-created.png)

En el cuadro de diálogo **Configurar implementación**, desplácese hacia abajo y seleccione la opción **Repositorio de Git local**. Haga clic en la flecha derecha para continuar.

![](./media/web-sites-python-create-deploy-flask-app/portal-setup-deployment.png)

Una vez configurada la publicación Git, aparecerá brevemente una página donde se le informa de que el repositorio se está creando. Cuando esté listo, verá instrucciones sobre cómo conectarse.  

![](./media/web-sites-python-create-deploy-flask-app/portal-repo-created.png)

Seguiremos estas instrucciones en las secciones siguientes.


## Información general de la aplicación

### Contenido del repositorio de Git

A continuación se muestra información general de los archivos que encontrará en el repositorio de Git inicial, que se va a clonar en la sección siguiente.

    \FlaskWebProject__init__.py
    \FlaskWebProject\views.py
    \FlaskWebProject\static\content\
    \FlaskWebProject\static\fonts\
    \FlaskWebProject\static\scripts\
    \FlaskWebProject\templates\about.html
    \FlaskWebProject\templates\contact.html
    \FlaskWebProject\templates\index.html
    \FlaskWebProject\templates\layout.html

Orígenes principales de la aplicación.  Consta de tres páginas (índice, acerca de, contacto) con un diseño principal.  El contenido estático y los scripts incluyen bootstrap, jquery, modernizr y respond.

    \runserver.py

Compatibilidad con servidor de desarrollo local. Úselo para ejecutar la aplicación localmente.

    \FlaskWebProject.pyproj
    \FlaskWebProject.sln

Archivos de proyecto para su uso con [Python Tools para Visual Studio](http://pytools.codeplex.com).

    \ptvs_virtualenv_proxy.py

Proxy IIS para entornos virtuales y compatibilidad con la depuración remota de PTVS.

    \requirements.txt

Paquetes externos necesarios para esta aplicación. El script de implementación instalará pip en los paquetes incluidos en este archivo.
 
    \web.2.7.config
    \web.3.4.config

Archivos de configuración de IIS.  El script de implementación utilizará el web.x.y.config adecuado y lo copiará como web.config.

### Archivos opcionales - Implementación de personalización

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../../includes/web-sites-python-customizing-deployment.md)]

### Archivos opcionales - Tiempo de ejecución de Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### Archivos adicionales en el servidor

Algunos archivos existen en el servidor pero no se agregan al repositorio de Git.  Estos se crean mediante el script de implementación.

    \web.config

Archivo de configuración de IIS.  Se crea desde web.x.y.config en cada implementación.

    \env\

Entorno virtual de Python.  Se crea durante la implementación si aún no existe un entorno virtual compatible en el sitio.  En los paquetes que aparecen en requirements.txt se instala pip, pero pip omitirá la instalación si los paquetes ya están instalados.

En las tres secciones siguientes se describe cómo continuar con el desarrollo de sitios web en tres entornos diferentes:

- Windows, con Python Tools para Visual Studio
- Windows, con línea de comandos
- Mac/Linux, con línea de comandos


## Desarrollo de sitios web - Windows - Python Tools para Visual Studio

### Clonación del repositorio

En primer lugar, clone el repositorio mediante la dirección URL proporcionada en el Portal de Azure.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-clone.png)

Abra el archivo de la solución (.sln) que se incluye en la raíz del repositorio.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-solution-flask.png)

### Creación de un entorno virtual

Ahora vamos a crear un entorno virtual para el desarrollo local.  Haga clic con el botón secundario en **Entornos de Python** y seleccione **Agregar entorno virtual...**.

- Asegúrese de que el nombre del entorno es `env`.

- Seleccione el intérprete base.  Asegúrese de utilizar la misma versión de Python seleccionada para el sitio (en runtime.txt o en la página de configuración del sitio).

- Asegúrese de que esté activada la opción para descargar e instalar paquetes.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-add-virtual-env-27.png)

Haga clic en **Crear**.  Esto creará el entorno virtual e instalará las dependencias mostradas en requirements.txt.

### Ejecución con el servidor de desarrollo

Presione F5 para iniciar la depuración y el explorador web abrirá automáticamente la página que se ejecuta localmente.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

Puede establecer puntos de interrupción en los orígenes, utilizar las ventanas Inspección, entre otros. Consulte la [Documentación de PTVS][] para obtener más información sobre las distintas características.

### Realización de cambios

Ahora puede experimentar mediante la realización de cambios en los orígenes o plantillas de la aplicación.

Una vez probados los cambios, confírmelos en el repositorio de Git:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-commit-flask.png)

### Instalación de más paquetes

La aplicación puede tener otras dependencias, aparte de Python y Flask.

Puede instalar paquetes adicionales con pip.  Para instalar un paquete, haga clic con el botón secundario en el entorno virtual y seleccione **Instalar paquete de Python**.

Por ejemplo, para instalar el SDK de Azure para Python, que proporciona acceso al almacenamiento de Azure, al bus de servicio y a otros servicios de Azure, escriba `azure`:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-install-package-dialog.png)

Haga clic con el botón secundario en el entorno virtual y seleccione **Generar requirements.txt** actualizar requirements.txt.

A continuación, confirme los cambios de requirements.txt en el repositorio de Git.

### Implementación en Azure

Para desencadenar una implementación, haga clic en **Sincronizar** o **Insertar**.  La sincronización realiza una inserción y una extracción.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-push.png)

La primera implementación tardará algún tiempo, ya que crea un entorno virtual, paquetes de instalación, etc.

Visual Studio no muestra el progreso de la implementación.  Si desea revisar la salida, consulte la sección [Solución de problemas - Implementación](#troubleshooting-deployment).

Vaya a la dirección URL de Azure para ver los cambios.


## Desarrollo de sitios web - Windows - Línea de comandos

### Clonación del repositorio

En primer lugar, clone el repositorio mediante la dirección URL proporcionada en el Portal de Azure y agregue el repositorio de Azure como remoto.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Creación de un entorno virtual

Vamos a crear un nuevo entorno virtual para fines de desarrollo (no lo agregue al repositorio).  Los entornos virtuales de Python no son reubicables, por lo que cada desarrollador que trabaja en la aplicación creará su propio entorno localmente.

Asegúrese de utilizar la misma versión de Python seleccionada para el sitio (en runtime.txt o en la página de configuración del sitio).

Para Python 2.7:

    c:\python27\python.exe -m virtualenv env

Para Python 3.4:

    c:\python34\python.exe -m venv env

Instale los paquetes externos requeridos por la aplicación. Puede utilizar el archivo requirements.txt en la raíz del repositorio para instalar los paquetes en su entorno virtual:

    env\scripts\pip install -r requirements.txt

### Ejecución con el servidor de desarrollo

Puede iniciar la aplicación en un servidor de desarrollo con el siguiente comando:

    env\scripts\python runserver.py

La consola mostrará la dirección URL y el puerto al que escucha el servidor:

![](./media/web-sites-python-create-deploy-flask-app/windows-run-local-flask.png)

A continuación, abra el explorador web para esa dirección URL.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

### Realización de cambios

Ahora puede experimentar mediante la realización de cambios en los orígenes o plantillas de la aplicación.

Una vez probados los cambios, confírmelos en el repositorio de Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Instalación de más paquetes

La aplicación puede tener otras dependencias, aparte de Python y Flask.

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

Verá la salida del script de implementación, incluida la creación del entorno virtual, la instalación de paquetes y la creación de web.config.

Vaya a la dirección URL de Azure para ver los cambios.


## Desarrollo de sitios web - Mac/Linux - Línea de comandos

### Clonación del repositorio

En primer lugar, clone el repositorio mediante la dirección URL proporcionada en el Portal de Azure y agregue el repositorio de Azure como remoto.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Creación de un entorno virtual

Vamos a crear un nuevo entorno virtual para fines de desarrollo (no lo agregue al repositorio).  Los entornos virtuales de Python no son reubicables, por lo que cada desarrollador que trabaja en la aplicación creará su propio entorno localmente.

Asegúrese de utilizar la misma versión de Python seleccionada para el sitio (en runtime.txt o en la página de configuración del sitio).

Para Python 2.7:

    python -m virtualenv env

Para Python 3.4:

    python -m venv env

Instale los paquetes externos requeridos por la aplicación. Puede utilizar el archivo requirements.txt en la raíz del repositorio para instalar los paquetes en su entorno virtual:

    env/bin/pip install -r requirements.txt

### Ejecución con el servidor de desarrollo

Puede iniciar la aplicación en un servidor de desarrollo con el siguiente comando:

    env/bin/python runserver.py

La consola mostrará la dirección URL y el puerto al que escucha el servidor:

![](./media/web-sites-python-create-deploy-flask-app/mac-run-local-flask.png)

A continuación, abra el explorador web para esa dirección URL.

![](./media/web-sites-python-create-deploy-flask-app/mac-browser-flask.png)

### Realización de cambios

Ahora puede experimentar mediante la realización de cambios en los orígenes o plantillas de la aplicación.

Una vez probados los cambios, confírmelos en el repositorio de Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### Instalación de más paquetes

La aplicación puede tener otras dependencias, aparte de Python y Flask.

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

Verá la salida del script de implementación, incluida la creación del entorno virtual, la instalación de paquetes y la creación de web.config.

Vaya a la dirección URL de Azure para ver los cambios.


## Troubleshooting - Deployment

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../../includes/web-sites-python-troubleshooting-deployment.md)]


## Solución de problemas - Instalación de un paquete

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## Solución de problemas - Entorno virtual

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## Pasos siguientes

Siga estos vínculos para obtener más información acerca de Flask y Python Tools para Visual Studio: 
 
- [Documentación de Flask][]
- [Documentación sobre Python Tools para Visual Studio][]

Para obtener información sobre el uso de Almacenamiento de tablas de Azure y MongoDB:

- [Flask y MongoDB en Azure con Python Tools 2.1 para Visual Studio][]
- [Flask y Almacenamiento de tablas de Azure en Azure con Python Tools 2.1 para Visual Studio][]


<!--Link references-->
[Flask y MongoDB en Azure con Python Tools 2.1 para Visual Studio]: web-sites-python-ptvs-flask-table-storage.md
[Flask y Almacenamiento de tablas de Azure en Azure con Python Tools 2.1 para Visual Studio]: web-sites-python-ptvs-flask-mongodb.md

<!--External Link references-->
[Azure SDK para Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK para Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git para Windows]: http://msysgit.github.io/
[GitHub para Windows]: https://windows.github.com/
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Visual Studio]: http://www.visualstudio.com/
[Documentación de PTVS]: http://pytools.codeplex.com/documentation
[Documentación sobre Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation 
[Documentación de Flask]: http://flask.pocoo.org/ 


<!--HONumber=52--> 