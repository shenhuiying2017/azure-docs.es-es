<properties 
	pageTitle="Aplicación web Python con Django en Mac - Tutorial de Azure" 
	description="Un tutorial que muestra cómo hospedar un sitio web basado en Django en Azure con una máquina virtual de Linux." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="huvalo"/>





# Aplicación web Django Hello World (mac-linux)

<div class="dev-center-tutorial-selector sublanding"><a href="/develop/python/tutorials/web-app-with-django/" title="Windows">Windows</a><a href="/develop/python/tutorials/django-hello-world-(maclinux)/" title="Mac/Linux" class="current">Mac/Linux</a></div>

En este tutorial se describe cómo hospedar un sitio web basado en Django en Microsoft Azure con una máquina virtual de Linux. En este tutorial se asume que no tiene ninguna experiencia previa con Azure. Al término de esta guía, tendrá una aplicación basada en Django funcionando en la nube.

Aprenderá a:

* Configurar una máquina virtual de Azure para hospedar Django. Aunque en este tutorial se explica cómo llevar a cabo esta acción en **Linux**, también se puede hacer lo mismo con una máquina virtual de Windows Server hospedada en Azure. 
* Crear una aplicación Django desde Linux.

Mediante este tutorial, se compilará una aplicación web Hello World sencilla que se hospedará en una máquina virtual de Azure.

A continuación se muestra una captura de pantalla de la aplicación completada:

![Ventana del explorador que muestra la página Hello World en Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Creación y configuración de una máquina virtual de Azure para hospedar Django

1. Siga las instrucciones que se proporcionan [aquí][portal-vm] para crear una máquina virtual de Azure de la distribución *Ubuntu Server 14.04 LTS*.

  **Nota:** *solo* necesita crear la máquina virtual. Deténgase en la sección titulada *Inicio de sesión en una máquina virtual después de su creación*.

1. Indique a Azure que dirija el tráfico del puerto **80** de la Web al puerto **80** de la máquina virtual:
	* Diríjase hasta la máquina virtual recién creada en el Portal de Azure y haga clic en la pestaña *ENDPOINTS*.
	* Haga clic en el botón *ADD* situado en la parte inferior de la pantalla. ![agregar extremo](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-add-endpoint.png)
	* Abra el puerto *PUBLIC PORT 80* del protocolo *TCP* como *PRIVATE PORT 80*. ![port80](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-port80.png)

## <a id="setup"> </a>Configuración del entorno de desarrollo

**Nota:** si es necesario instalar Python o desea usar las bibliotecas de clientes, consulte la [Guía de instalación de Python](../python-how-to-install.md).

La máquina virtual de Linux (Ubuntu) ya tiene preinstalado Python 2.7, pero no incluye Apache o Django. Siga estos pasos para conectarse a la máquina virtual e instalar Apache y Django.

1.  Abra una nueva ventana **Terminal**.
    
1.  Escriba el siguiente comando para conectarse a la máquina virtual de Azure.

		$ ssh yourusername@yourVmUrl

1.  Escriba los siguientes comandos para instalar Django:

		$ sudo apt-get install python-setuptools
		$ sudo easy_install django

1.  Escriba el siguiente comando para instalar Apache con mod-wsgi:

		$ sudo apt-get install apache2 libapache2-mod-wsgi


## Creación de una nueva aplicación Django

1.  Abra la ventana **Terminal** utilizada en la sección anterior para conectarse a la máquina virtual mediante el comando ssh.
    
1.  Escriba los siguientes comandos para crear un proyecto Django:

		$ cd /var/www
		$ sudo django-admin.py startproject helloworld

    El script **django-admin.py** genera una estructura básica para sitios web basados en Django:- **helloworld/manage.py** le ayuda a iniciar y detener el hospedaje de su sitio web basado en Django. - **helloworld/helloworld/settings.py** contiene la configuración de Django para su aplicación. - **helloworld/helloworld/urls.py** contiene el código de asignación entre cada dirección URL y su vista.

1.  Cree un nuevo archivo denominado **views.py** en el directorio**/var/www/helloworld/helloworld**. Este contendrá la vista que representa la página "hello world". Inicie el editor y escriba lo siguiente:
		
		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Ahora, reemplace el contenido del archivo **urls.py** por lo siguiente.

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)


## Instalación de Apache

1.  Cree un archivo de configuración del host virtual **/etc/apache2/sites-available/helloworld.conf**. Configure los contenidos de la siguiente manera y asegúrese de sustituir *yourVmUr* por la URL real de la máquina que está utilizando (por ejemplo, *pyubuntu.cloudapp.net*).

		<VirtualHost *:80>
		ServerName yourVmUrl
		</VirtualHost>
		WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
		WSGIPythonPath /var/www/helloworld

1.  Habilite el sitio con el comando siguiente:

        $ sudo a2ensite helloworld

1.  Reinicie Apache con el comando siguiente:

        $ sudo service apache2 reload

1.  Por último, cargue la página web en el explorador:

	![Ventana del explorador que muestra la página Hello World en Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)


## Apagado de la máquina virtual de Azure

Cuando finalice este tutorial, apague o quite la máquina virtual de Azure recién creada para liberar recursos para otros tutoriales y así evitar incurrir en cargos por uso de Azure.


[portal-vm]: /manage/linux/tutorials/virtual-machine-from-gallery/
 

<!---HONumber=July15_HO1-->