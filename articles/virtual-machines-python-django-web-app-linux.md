<properties linkid="develop-python-web-app-with-django-mac" urlDisplayName="Web with Django" pageTitle="Python web app with Django on Mac - Azure tutorial" metaKeywords="" description="A tutorial that shows how to host a Django-based web site on Azure using a Linux virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World Web Application (mac-linux)" authors="" solutions="" manager="" editor="" />

Aplicación web Django Hello World (mac-linux)
=============================================

[Windows](/es-es/develop/python/tutorials/web-app-with-django/ "Windows")[Mac/Linux](/es-es/develop/python/tutorials/django-hello-world-(maclinux)/ "Mac/Linux")

En este tutorial se describe cómo hospedar un sitio web basado en Django en Microsoft Azure usando una máquina virtual con Linux. En este tutorial se asume que no tiene ninguna experiencia previa con Azure. Al término de esta guía, tendrá una aplicación basada en Django funcionando en la nube.

Aprenderá a:

-   Configurar una máquina virtual de Azure para hospedar Django. Aunque en este tutorial se explica cómo llevar a cabo esta acción en **Linux**, también se puede hacer lo mismo con una máquina virtual de Windows Server hospedada en Azure.
-   Crear una aplicación Django desde Linux.

Mediante este tutorial, se compilará una aplicación web Hello World sencilla que se hospedará en una máquina virtual de Azure.

A continuación se muestra una captura de pantalla de la aplicación completada:

![Ventana del explorador que muestra la página Hello World en Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

Creación y configuración de una máquina virtual de Azure para hospedar Django
-----------------------------------------------------------------------------

1.  Siga las instrucciones que se proporcionan [aquí](/es-es/manage/linux/tutorials/virtual-machine-from-gallery/) para crear una máquina virtual de Azure de la distribución *Ubuntu Server 12.04*.

	**Nota:** *solo* necesita crear la máquina virtual. Deténgase en la sección titulada *Inicio de sesión en una máquina virtual después de su creación*.

1.  Indique a Azure que dirija el tráfico del puerto **80** de la Web al puerto **80** de la máquina virtual:
    -   Diríjase hasta la máquina virtual recién creada en el Portal de Azure y haga clic en la pestaña *ENDPOINTS*.
    -   Haga clic en *ADD ENDPOINT* en la parte inferior de la pantalla. ![add endpoint](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-add-endpoint.png)
    -   Abra el puerto *PUBLIC PORT 80* del protocolo *TCP* como *PRIVATE PORT 80*. ![port80](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-port80.png)

Configuración del entorno de desarrollo
---------------------------------------

**Nota:** si es necesario instalar Python o desea usar las bibliotecas de clientes, consulte la [guía de instalación de Python](../python-how-to-install/) (en inglés).

La máquina virtual de Linux (Ubuntu) ya tiene preinstalado Python 2.7, pero no incluye Apache o Django. Siga estos pasos para conectarse a la máquina virtual e instalar Apache y Django.

1.  Abra una nueva ventana de **terminal**.

2.  Escriba el siguiente comando para conectarse a la máquina virtual de Azure:

        $ ssh yourusername@yourVmUrl

3.  Escriba los siguientes comandos para instalar Django:

        $ sudo apt-get install python-setuptools
        $ sudo easy_install django

4.  Escriba el siguiente comando para instalar Apache con mod-wsgi:

        $ sudo apt-get install apache2 libapache2-mod-wsgi

Creación de una nueva aplicación Django
---------------------------------------

1.  Abra la ventana de **terminal** utilizada en la sección anterior para conectarse a la máquina virtual mediante el comando ssh.

2.  Escriba los siguientes comandos para crear un proyecto Django:

    ![Resultado del comando django-admin](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-dir.png)

    El script **django-admin.py** genera una estructura básica para los sitios web basados en Django: 
	- **manage.py** le ayuda a empezar a hospedar el sitio web basado en Django y a dejar de hospedarlo.
	- **helloworld\\settings.py** contiene valores de Django para su aplicación. 
	- **helloworld\\urls.py** contiene el código de asignación entre cada dirección URL y su vista.

3.  Cree un archivo denominado **views.py** en el subdirectorio *helloworld* de *django\\helloworld*, como elemento del mismo nivel de **urls.py**. Este contendrá la vista que representa la página "hello world". Inicie el editor y escriba lo siguiente:

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

Implementación y ejecución de su sitio web Django
-------------------------------------------------

1.  Edite el archivo de configuración de Apache **/etc/apache2/httpd.conf** y agregue el siguiente código, donde deberá reemplazar *username* por el nombre de usuario que especificó durante la creación de la máquina virtual:

        WSGIScriptAlias / /home/*username*/django/helloworld/helloworld/wsgi.py
        WSGIPythonPath /home/*username*/django/helloworld

        <Directory /home/*username*/django/helloworld/helloworld>
        <Files wsgi.py>
        Order deny,allow
        Allow from all
        </Files>
        </Directory>

2.  Reinicie Apache con el comando siguiente:

        $ sudo apachectl restart

3.  Por último, cargue la página web en el explorador:

    ![Ventana del explorador que muestra la página Hello World en Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

Apagado de la máquina virtual de Azure
--------------------------------------

Cuando finalice este tutorial, apague o quite la máquina virtual de Azure recién creada para liberar recursos para otros tutoriales y así evitar incurrir en cargos por uso de Azure.

