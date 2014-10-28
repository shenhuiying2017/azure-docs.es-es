<properties linkid="develop-python-web-app-with-django" urlDisplayName="Web with Django (Windows)" pageTitle="Python web app with Django - Azure tutorial" metaKeywords="Azure Django web app, Azure Django virtual machine" description="A tutorial that teaches you how to host a Django-based website on Azure using a Windows Server 2008 R2 virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World Web Application" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-windows" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Aplicación web Django Hello World

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/es-es/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

En este tutorial se describe cómo hospedar un sitio web basado en Django en Windows
Azure utilizando una máquina virtual de Windows Server. En este tutorial se asume que no tiene ninguna experiencia previa con Azure. Al término de esta guía, tendrá una aplicación basada en Django funcionando en la nube.

Aprenderá a:

-   Configurar una máquina virtual de Azure para hospedar Django. Aunque este tutorial explica cómo llevar a cabo esta acción en **Windows Server**, también se puede hacer lo mismo con una máquina virtual de Linux hospedada en Azure.
-   Crear una nueva aplicación Django desde Windows.

Mediante este tutorial, se compilará una aplicación
web Hello World sencilla que se hospedará en una máquina virtual de Azure.

A continuación se muestra una captura de pantalla de la aplicación completada:

![Ventana del explorador que muestra la página Hello World en Azure][]

[WACOM.INCLUDE [create-account-and-vms-note][]]

## Creación y configuración de una máquina virtual de Azure para hospedar Django

1.  Siga las instrucciones que se proporcionan [aquí][] para crear una máquina virtual de Azure de la distribución *Windows Server 2012 Datacenter*.

2.  Indique a Azure que dirija el tráfico del puerto **80** de la Web al puerto **80** de la máquina virtual:

-   Diríjase hasta la máquina virtual recién creada en el Portal de Azure y haga clic en la pestaña *ENDPOINTS*.
-   Haga clic en *ADD ENDPOINT* en la parte inferior de la pantalla.
    ![add endpoint][]

-   Abra el puerto *PUBLIC PORT 80* del protocolo *TCP* como *PRIVATE PORT 80*.
    ![][]

1.  Use el *Escritorio remoto* para iniciar sesión en la máquina virtual de Azure recién creada.

**Nota importante:** en las instrucciones que aparecen a continuación se asume que no tuvo problemas para iniciar sesión en la máquina virtual y que está generando comandos desde allí y no desde la máquina local.

## <span id="setup"></span> </a>Configuración del entorno de desarrollo

Para obtener más información acerca de la configuración de los entornos de Python y Django, consulte la [guía de instalación][].

**Nota 1:***solo* necesita instalar el producto **Django** mediante el instalador WebPI de Windows en la máquina virtual de Azure para que funcione *este* ejemplo concreto.

**Nota 2:** para descargar el instalador WebPI es posible que deba ajustar la configuración de ESC de Internet Explorer (Inicio/Herramientas administrativas/Administrador de servidores, a continuación haga clic en **Configurar ESC de Internet Explorer**, y desactive esta opción).

## Configuración de IIS con FastCGI

1.  Instale ISS habilitado para FastCGI.

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

2.  Configure el controlador de FastCGI de Python.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"

3.  Registre el controlador en el sitio.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"

4.  Configure el controlador para ejecutar la aplicación Django.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='DjangoApplication.settings']" /commit:apphost

5.  Configure PYTHONPATH para que el intérprete de Python pueda encontrar la aplicación Django.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\DjangoApplication']" /commit:apphost

    Verá lo siguiente:

    ![Configuración 1 de IIS][]

6.  Indique a la puerta de enlace entre FastCGI y WSGI qué controlador de WSGI utilizar

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

7.  Descargue wfastcgi.py desde [CodePlex][] y guárdelo en C:\\Python27\\Scripts. Esta es la ubicación de los comandos anteriores utilizados para registrar el controlador de FastCGI. También puede instalarlo utilizando el Instalador de plataforma web. Busque “WFastCGI”.

## Creación de una nueva aplicación Django

1.  Inicie cmd.exe.

2.  Haga cd a C:\\inetpub\\wwwroot.

3.  Escriba el siguiente comando para crear un nuevo proyecto Django:

    C:\\Python27\\python.exe -m django.bin.django-admin startproject DjangoApplication

    ![Resultado del comando New-AzureService][]

El script **django-admin.py** genera una estructura básica para los sitios web basados en Django:

-   **manage.py** le ayuda a iniciar y detener el hospedaje del sitio web basado en Django.
-   **DjangoApplication\\settings.py** contiene ajustes de Django para la aplicación.
-   **DjangoApplication\\urls.py** contiene el código de asignación de cada URL con su vista.

1.  Cree un nuevo archivo denominado **views.py** en el subdirectorio *DjangoApplication* de *C:\\inetpub\\wwwroot\\DjangoApplication*, del mismo nivel que **urls.py**. Este contendrá la vista que representa la página "hello world". Inicie el editor y escriba lo siguiente:

        from django.http import HttpResponse
        def hello(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

2.  Ahora, reemplace el contenido del archivo **urls.py** por el código siguiente.

        from django.conf.urls.defaults import patterns, include, url
        from DjangoApplication.views import hello
        urlpatterns = patterns('',
            (r'^$',hello),
        )

3.  Por último, cargue la página web en el explorador.

![Ventana del explorador que muestra la página Hello World en Azure][]

## Apagado de la máquina virtual de Azure

Cuando finalice este tutorial, apague o quite la máquina virtual de Azure recién creada para liberar recursos para otros tutoriales y así evitar incurrir en cargos por uso de Azure.

  [Windows]: /es-es/develop/python/tutorials/web-app-with-django/ "Windows"
  [Mac/Linux]: /es-es/develop/python/tutorials/django-hello-world-(maclinux)/ "MacLinux"
  [Ventana del explorador que muestra la página Hello World en Azure]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png
  [create-account-and-vms-note]: ../includes/create-account-and-vms-note.md
  [aquí]: /es-es/manage/windows/tutorials/virtual-machine-from-gallery/
  [add endpoint]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png
  []: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png
  [guía de instalación]: ../python-how-to-install/
  [Configuración 1 de IIS]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis1.png
  [CodePlex]: http://go.microsoft.com/fwlink/?LinkID=316392&clcid=0x409
  [Resultado del comando New-AzureService]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png
