<properties linkid="develop-python-web-app-with-django-mac" urlDisplayName="Web with Django" pageTitle="Python web app with Django on Mac - Azure tutorial" metaKeywords="" description="A tutorial that shows how to host a Django-based website on Azure using a Linux virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World Web Application (mac-linux)" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-linux" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Aplicación web Django Hello World (mac-linux)

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/develop/python/tutorials/web-app-with-django/" title="Windows">Windows</a><a href="/es-es/develop/python/tutorials/django-hello-world-(maclinux)/" title="Mac/Linux" class="current">Mac/Linux</a></div>

En este tutorial se describe cómo hospedar un sitio web basado en Django en Windows
Azure con una máquina virtual de Linux. En este tutorial se asume que no tiene ninguna experiencia previa con Azure. Al término de esta guía, tendrá una aplicación basada en Django funcionando en la nube.

Aprenderá a:

-   Configurar una máquina virtual de Azure para hospedar Django. Aunque en este tutorial se explica cómo llevar a cabo esta acción en **Linux**, también se puede hacer lo mismo con una máquina virtual de Windows Server hospedada en Azure.
-   Crear una aplicación Django desde Linux.

Mediante este tutorial, se compilará una aplicación
web Hello World sencilla que se hospedará en una máquina virtual de Azure.

A continuación se muestra una captura de pantalla de la aplicación completada:

![Ventana del explorador que muestra la página Hello World en Azure][Ventana del explorador que muestra la página Hello World en Azure]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Creación y configuración de una máquina virtual de Azure para hospedar Django

1.  Siga las instrucciones que se proporcionan [aquí][aquí] para crear una máquina virtual de Azure de la distribución *Ubuntu Server 14,04*.

**Nota:** *solo* necesita crear la máquina virtual. Deténgase en la sección titulada *Inicio de sesión en una máquina virtual después de su creación*.

1.  Indique a Azure que dirija el tráfico del puerto **80** de la Web al puerto **80** de la máquina virtual:

    -   Diríjase hasta la máquina virtual recién creada en el Portal de Azure y haga clic en la pestaña *ENDPOINTS*.
    -   Haga clic en *ADD ENDPOINT* en la parte inferior de la pantalla.
        ![add endpoint][add endpoint]
    -   Abra el puerto *PUBLIC PORT 80* del protocolo *TCP* como *PRIVATE PORT 80*.
        ![port80][port80]

## <span id="setup"></span> </a>Configuración del entorno de desarrollo

**Nota:** si es necesario instalar Python o desea usar las bibliotecas de clientes, consulte la [guía de instalación de Python][guía de instalación de Python] (en inglés).

La máquina virtual de Linux (Ubuntu) ya tiene preinstalado Python 2.7, pero no incluye Apache o Django. Siga estos pasos para conectarse a la máquina virtual e instalar Apache y Django.

1.  Abra una nueva ventana de **terminal**.

2.  Escriba el siguiente comando para conectarse a la máquina virtual de Azure:

        $ ssh yourusername@yourVmUrl

3.  Escriba los siguientes comandos para instalar Django:

        $ sudo apt-get install python-setuptools
        $ sudo easy_install django

4.  Escriba el siguiente comando para instalar Apache con mod-wsgi:

        $ sudo apt-get install apache2 libapache2-mod-wsgi

## Creación de una nueva aplicación Django

1.  Abra la ventana de **terminal** utilizada en la sección anterior para conectarse a la máquina virtual mediante el comando ssh.

2.  Escriba los siguientes comandos para crear un proyecto Django:

        $ cd /var/www
        $ sudo django-admin.py startproject helloworld

    El script **django-admin.py** genera una estructura básica para los sitios web basados en Django:

    -   **helloworld/manage.py** le ayuda a iniciar y detener el hospedaje del sitio web basado en Django.
    -   **helloworld/helloworld/settings.py** contiene ajustes de Django para la aplicación.
    -   **helloworld/helloworld/urls.py** contiene el código de asignación entre cada dirección URL y su vista.

3.  Cree un nuevo archivo denominado **views.py** en el directorio **/var/www/helloworld/helloworld** (en el mismo nivel que **urls.py**). Este contendrá la vista que representa la página "hello world". Inicie el editor y escriba lo siguiente:

        from django.http import HttpResponse
        def hello(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

4.  Ahora, reemplace el contenido del archivo **urls.py** por el código siguiente.

        from django.conf.urls.defaults import patterns, include, url
        from helloworld.views import hello
        urlpatterns = patterns('',
            (r'^$',hello),
        )

## Instalación de Apache

1.  Cree un archivo de configuración del host virtual Apache **/etc/apache2/sites-available/helloworld.conf**. Configure los contenidos de la siguiente manera y asegúrese de sustituir *yourVmUrl* por la URL real de la máquina que está utilizando (por ejemplo, *pyubuntu.cloudapp.net*).

        <VirtualHost *:80>
        ServerName yourVmUrl
        </VirtualHost>
        WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
        WSGIPythonPath /var/www/helloworld

2.  Habilite el sitio con el comando siguiente:

        $ sudo a2ensite helloworld

3.  Reinicie Apache con el comando siguiente:

        $ sudo service apache2 reload

4.  Por último, cargue la página web en el explorador:

    ![Ventana del explorador que muestra la página Hello World en Azure][Ventana del explorador que muestra la página Hello World en Azure]

## Apagado de la máquina virtual de Azure

Cuando finalice este tutorial, apague o quite la máquina virtual de Azure recién creada para liberar recursos para otros tutoriales y así evitar incurrir en cargos por uso de Azure.

  [Ventana del explorador que muestra la página Hello World en Azure]: ./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png
  [create-account-and-vms-note]: ../includes/create-account-and-vms-note.md
  [aquí]: /es-es/manage/linux/tutorials/virtual-machine-from-gallery/
  [add endpoint]: ./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-add-endpoint.png
  [port80]: ./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-port80.png
  [guía de instalación de Python]: ../python-how-to-install/
