---
title: "Creación de aplicaciones web con Flask en Azure"
description: "Un tutorial que indica cómo ejecutar una aplicación web de Python en Azure."
services: app-service\web
documentationcenter: python
tags: python
author: huguesv
manager: wpickett
editor: 
ms.assetid: b7f4ca3a-0b52-4108-90a7-f7e07ac73da0
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/20/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6a0aa48be45b5d0cbac7953e0106c465161e7228


---
# <a name="creating-web-apps-with-flask-in-azure"></a>Creación de aplicaciones web con Flask en Azure
En este tutorial, se describe cómo empezar a ejecutar Python en [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).  Aplicaciones web ofrece hospedaje gratuito limitado y una implementación rápida. Además, ahora también se puede usar Python.  A medida que su aplicación crece, puede cambiar a un tipo de hospedaje de pago e integrar el resto de los servicios de Azure.

Creará una aplicación con el marco web de Flask (consulte las versiones alternativas de este tutorial para [Django](web-sites-python-create-deploy-django-app.md) y [Bottle](web-sites-python-create-deploy-bottle-app.md)).  Creará el sitio web en la galería de Azure, configurará la implementación Git y clonará el repositorio en modo local.  A continuación, ejecutará la aplicación localmente, realizará cambios, los confirmará y los insertará en Azure.  El tutorial muestra cómo llevarlo a cabo en Windows o Mac/Linux.

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
> 
> 

## <a name="prerequisites"></a>Requisitos previos
* Windows, Mac o Linux
* Python 2.7 o 3.4
* setuptools, pip, virtualenv (solo en Python 2.7)
* Git
* [Python Tools para Visual Studio][Python Tools para Visual Studio] (PTVS)- Nota: Esto es opcional

**Nota:**la publicación TFS no se admite actualmente para los proyectos de Python.

### <a name="windows"></a>Windows
Si aún no tiene Python 2.7 o 3.4 instalado (32 bits), se recomienda instalar [Azure SDK para Python 2.7] o [Azure SDK para Python 3.4] mediante el instalador de plataforma web.  Se instala la versión de 32 bits de Python, setuptools, pip, virtualenv, etc. (Python de 32 bits es lo que se instala en los equipos host de Azure).  También puede obtener Python en [python.org].

Para Git, recomendamos [Git para Windows] o [GitHub para Windows].  Si utiliza Visual Studio, puede utilizar la compatibilidad integrada con Git.

También se recomienda instalar [Python Tools 2.2 para Visual Studio].  Esto es opcional, pero si tiene [Visual Studio], incluidas las versiones gratuitas Visual Studio Community 2013 o Visual Studio Express 2013 para web, obtendrá un excelente IDE de Python.

### <a name="maclinux"></a>Mac o Linux:
Debe tener Python y Git instalados, pero asegúrese de que tiene Python 2.7 o 3.4.

## <a name="web-app-create-on-the-azure-portal"></a>Creación de una aplicación web en el Portal de Azure
El primer paso para crear la aplicación consiste en crear la aplicación web a través del [Portal de Azure](https://portal.azure.com). 

1. Inicie sesión en el Portal de Azure, haga clic en el botón **Nuevo** situado en la esquina inferior izquierda. 
2. Haga clic en **Web y móvil**.
3. En el cuadro de búsqueda, escriba "python".
4. En los resultados de búsqueda, seleccione **Flask** y, a continuación, haga clic en **Crear**.
5. Configure la nueva aplicación Flask, por ejemplo, la creación de un nuevo plan para el Servicio de aplicaciones y un nuevo grupo de recursos para él. A continuación, haga clic en **Crear**.
6. Configure la publicación de Git para la aplicación web recién creada siguiendo las instrucciones que se describen en [Implementación de Git local en el Servicio de aplicaciones de Azure](app-service-deploy-local-git.md).

## <a name="application-overview"></a>Información general de la aplicación
### <a name="git-repository-contents"></a>Contenido del repositorio de Git
A continuación se muestra información general de los archivos que encontrará en el repositorio de Git inicial, que se va a clonar en la sección siguiente.

    \FlaskWebProject\__init__.py
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

Archivos de proyecto para su uso con [Python Tools para Visual Studio].

    \ptvs_virtualenv_proxy.py

Proxy IIS para entornos virtuales y compatibilidad con la depuración remota de PTVS.

    \requirements.txt

Paquetes externos necesarios para esta aplicación. El script de implementación instalará pip en los paquetes incluidos en este archivo.

    \web.2.7.config
    \web.3.4.config

Archivos de configuración de IIS.  El script de implementación utilizará el web.x.y.config adecuado y lo copiará como web.config.

### <a name="optional-files---customizing-deployment"></a>Archivos opcionales - Implementación de personalización
[!INCLUDE [web-sites-python-customizing-deployment](../../includes/web-sites-python-customizing-deployment.md)]

### <a name="optional-files---python-runtime"></a>Archivos opcionales - Tiempo de ejecución de Python
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### <a name="additional-files-on-server"></a>Archivos adicionales en el servidor
Algunos archivos existen en el servidor pero no se agregan al repositorio de Git.  Estos se crean mediante el script de implementación.

    \web.config

Archivo de configuración de IIS.  Se crea desde web.x.y.config en cada implementación.

    \env\

Entorno virtual de Python.  Se crea durante la implementación si aún no existe un entorno virtual compatible en la aplicación.  En los paquetes que aparecen en requirements.txt se instala pip, pero pip omitirá la instalación si los paquetes ya están instalados.

En las tres secciones siguientes se describe cómo continuar con el desarrollo de aplicaciones web en tres entornos diferentes:

* Windows, con Python Tools para Visual Studio
* Windows, con línea de comandos
* Mac/Linux, con línea de comandos

## <a name="web-app-development---windows---python-tools-for-visual-studio"></a>Desarrollo de aplicaciones web: Windows, Python Tools para Visual Studio
### <a name="clone-the-repository"></a>Clonación del repositorio
En primer lugar, clone el repositorio mediante la dirección URL proporcionada en el Portal de Azure. Para más información, consulte [Implementación de Git local en el Servicio de aplicaciones de Azure](app-service-deploy-local-git.md).

Abra el archivo de la solución (.sln) que se incluye en la raíz del repositorio.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-solution-flask.png)

### <a name="create-virtual-environment"></a>Creación de un entorno virtual
Ahora vamos a crear un entorno virtual para el desarrollo local.  Haga clic con el botón secundario en **Entornos de Python** y elija **Agregar entorno virtual...**

* Asegúrese de que el nombre del entorno sea `env`.
* Seleccione el intérprete base.  Asegúrese de utilizar la misma versión de Python que la seleccionada para la aplicación web (en runtime.txt o en la hoja **Configuración de la aplicación** de la aplicación web en el Portal de Azure).
* Asegúrese de que esté activada la opción para descargar e instalar paquetes.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-add-virtual-env-27.png)

Haga clic en **Crear**.  Esto creará el entorno virtual e instalará las dependencias mostradas en requirements.txt.

### <a name="run-using-development-server"></a>Ejecución con el servidor de desarrollo
Presione F5 para iniciar la depuración y el explorador web abrirá automáticamente la página que se ejecuta localmente.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

Puede establecer puntos de interrupción en los orígenes, utilizar las ventanas Inspección, etc.  Consulte la [Documentación sobre Python Tools para Visual Studio] para obtener más información sobre las distintas características.

### <a name="make-changes"></a>Realización de cambios
Ahora puede experimentar mediante la realización de cambios en los orígenes o plantillas de la aplicación.

Una vez probados los cambios, confírmelos en el repositorio de Git:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-commit-flask.png)

### <a name="install-more-packages"></a>Instalación de más paquetes
La aplicación puede tener otras dependencias, aparte de Python y Flask.

Puede instalar paquetes adicionales con pip.  Para instalar un paquete, haga clic con el botón secundario en el entorno virtual y elija **Instalar paquete de Python**.

Por ejemplo, para instalar el SDK de Azure para Python, que proporciona acceso al almacenamiento de Azure, al bus de servicio y a otros servicios de Azure, escriba `azure`:

![](./media/web-sites-python-create-deploy-flask-app/ptvs-install-package-dialog.png)

Haga clic con el botón secundario en el entorno virtual y elija **Generar requirements.txt** para actualizar requirements.txt.

A continuación, confirme los cambios de requirements.txt en el repositorio de Git.

### <a name="deploy-to-azure"></a>Implementar en Azure
Para desencadenar una implementación, haga clic en **Sincronizar** o **Insertar**.  La sincronización realiza una inserción y una extracción.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-push.png)

La primera implementación tardará algún tiempo, ya que crea un entorno virtual, paquetes de instalación, etc.

Visual Studio no muestra el progreso de la implementación.  Si desea revisar la salida, consulte la sección [Solución de problemas - Implementación](#troubleshooting-deployment).

Vaya a la dirección URL de Azure para ver los cambios.

## <a name="web-app-development---windows---command-line"></a>Desarrollo de aplicaciones web - Windows - Línea de comandos
### <a name="clone-the-repository"></a>Clonación del repositorio
En primer lugar, clone el repositorio mediante la dirección URL proporcionada en el Portal de Azure y agregue el repositorio de Azure como remoto. Para más información, consulte [Implementación de Git local en el Servicio de aplicaciones de Azure](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### <a name="create-virtual-environment"></a>Creación de un entorno virtual
Vamos a crear un nuevo entorno virtual para fines de desarrollo (no lo agregue al repositorio).  Los entornos virtuales de Python no son reubicables, por lo que cada desarrollador que trabaja en la aplicación creará su propio entorno localmente.

Asegúrese de utilizar la misma versión de Python que la seleccionada para la aplicación web (en runtime.txt o en la hoja **Configuración de la aplicación** de la aplicación web en el Portal de Azure).

Para Python 2.7:

    c:\python27\python.exe -m virtualenv env

Para Python 3.4:

    c:\python34\python.exe -m venv env

Instale los paquetes externos requeridos por la aplicación. Puede utilizar el archivo requirements.txt en la raíz del repositorio para instalar los paquetes en su entorno virtual:

    env\scripts\pip install -r requirements.txt

### <a name="run-using-development-server"></a>Ejecución con el servidor de desarrollo
Puede iniciar la aplicación en un servidor de desarrollo con el siguiente comando:

    env\scripts\python runserver.py

La consola mostrará la dirección URL y el puerto al que escucha el servidor:

![](./media/web-sites-python-create-deploy-flask-app/windows-run-local-flask.png)

A continuación, abra el explorador web para esa dirección URL.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

### <a name="make-changes"></a>Realización de cambios
Ahora puede experimentar mediante la realización de cambios en los orígenes o plantillas de la aplicación.

Una vez probados los cambios, confírmelos en el repositorio de Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Instalación de más paquetes
La aplicación puede tener otras dependencias, aparte de Python y Flask.

Puede instalar paquetes adicionales con pip.  Por ejemplo, para instalar el SDK de Azure para Python, que proporciona acceso al almacenamiento de Azure, al bus de servicio y a otros servicios de Azure, escriba:

    env\scripts\pip install azure

Asegúrese de actualizar requirements.txt:

    env\scripts\pip freeze > requirements.txt

Confirme los cambios:

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Implementación en Azure
Para desencadenar una implementación, inserte los cambios en Azure:

    git push azure master

Verá la salida del script de implementación, incluida la creación del entorno virtual, la instalación de paquetes y la creación de web.config.

Vaya a la dirección URL de Azure para ver los cambios.

## <a name="web-app-development---maclinux---command-line"></a>Desarrollo de aplicaciones web: Mac/Linux - Línea de comandos
### <a name="clone-the-repository"></a>Clonación del repositorio
En primer lugar, clone el repositorio mediante la dirección URL proporcionada en el Portal de Azure y agregue el repositorio de Azure como remoto. Para más información, consulte [Implementación de Git local en el Servicio de aplicaciones de Azure](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### <a name="create-virtual-environment"></a>Creación de un entorno virtual
Vamos a crear un nuevo entorno virtual para fines de desarrollo (no lo agregue al repositorio).  Los entornos virtuales de Python no son reubicables, por lo que cada desarrollador que trabaja en la aplicación creará su propio entorno localmente.

Asegúrese de utilizar la misma versión de Python que la seleccionada para la aplicación web (en runtime.txt o en la hoja **Configuración de la aplicación** de la aplicación web en el Portal de Azure).

Para Python 2.7:

    python -m virtualenv env

Para Python 3.4:

    python -m venv env
o pyvenv env

Instale los paquetes externos requeridos por la aplicación. Puede utilizar el archivo requirements.txt en la raíz del repositorio para instalar los paquetes en su entorno virtual:

    env/bin/pip install -r requirements.txt

### <a name="run-using-development-server"></a>Ejecución con el servidor de desarrollo
Puede iniciar la aplicación en un servidor de desarrollo con el siguiente comando:

    env/bin/python runserver.py

La consola mostrará la dirección URL y el puerto al que escucha el servidor:

![](./media/web-sites-python-create-deploy-flask-app/mac-run-local-flask.png)

A continuación, abra el explorador web para esa dirección URL.

![](./media/web-sites-python-create-deploy-flask-app/mac-browser-flask.png)

### <a name="make-changes"></a>Realización de cambios
Ahora puede experimentar mediante la realización de cambios en los orígenes o plantillas de la aplicación.

Una vez probados los cambios, confírmelos en el repositorio de Git:

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Instalación de más paquetes
La aplicación puede tener otras dependencias, aparte de Python y Flask.

Puede instalar paquetes adicionales con pip.  Por ejemplo, para instalar el SDK de Azure para Python, que proporciona acceso al almacenamiento de Azure, al bus de servicio y a otros servicios de Azure, escriba:

    env/bin/pip install azure

Asegúrese de actualizar requirements.txt:

    env/bin/pip freeze > requirements.txt

Confirme los cambios:

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Implementación en Azure
Para desencadenar una implementación, inserte los cambios en Azure:

    git push azure master

Verá la salida del script de implementación, incluida la creación del entorno virtual, la instalación de paquetes y la creación de web.config.

Vaya a la dirección URL de Azure para ver los cambios.

## <a name="troubleshooting---package-installation"></a>Solución de problemas - Instalación de un paquete
[!INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]

## <a name="troubleshooting---virtual-environment"></a>Solución de problemas - Entorno virtual
[!INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>Pasos siguientes
Siga estos vínculos para obtener más información acerca de Flask y Python Tools para Visual Studio: 

* [Documentación de Flask]
* [Documentación sobre Python Tools para Visual Studio]

Para obtener información sobre el uso de Almacenamiento de tablas de Azure y MongoDB:

* [Flask y MongoDB en Azure con Python Tools para Visual Studio]
* [Flask y Almacenamiento de tablas de Azure en Azure con Python Tools para Visual Studio]

Para obtener más información, consulte también el [Centro para desarrolladores de Python](/develop/python/).

## <a name="whats-changed"></a>Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!--Link references-->
[Flask y MongoDB en Azure con Python Tools para Visual Studio]: https://github.com/microsoft/ptvs/wiki/Flask-and-MongoDB-on-Azure
[Flask y Almacenamiento de tablas de Azure en Azure con Python Tools para Visual Studio]: web-sites-python-ptvs-flask-table-storage.md

<!--External Link references-->
[Azure SDK para Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK para Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git para Windows]: http://msysgit.github.io/
[GitHub para Windows]: https://windows.github.com/
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Visual Studio]: http://www.visualstudio.com/
[Documentación sobre Python Tools para Visual Studio]: http://aka.ms/ptvsdocs
[Documentación de Flask]: http://flask.pocoo.org/ 




<!--HONumber=Nov16_HO3-->


