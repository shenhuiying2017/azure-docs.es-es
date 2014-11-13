<properties urlDisplayName="Web with Django (Windows)" pageTitle="Aplicaci&oacute;n web Python con Django - Tutorial de Azure" metaKeywords="Azure Django web app, Azure Django virtual machine" description="Un tutorial que le ense&ntilde;a c&oacute;mo hospedar un sitio web basado en Django en Azure con una m&aacute;quina virtual de Windows Server 2012 R2 Datacenter." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Aplicaci&oacute;n web Django Hello World" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-windows" ms.devlang="python" ms.topic="article" ms.date="09/25/2014" ms.author="huvalo" />

# Aplicación web Django Hello World

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/es-es/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

En este tutorial se describe cómo hospedar un sitio web basado en Django en Microsoft
Azure utilizando una máquina virtual de Windows Server. En este tutorial se asume que no tiene ninguna experiencia previa con Azure. Al término de esta guía, tendrá una aplicación basada en Django funcionando en la nube.

Aprenderá a:

-   Configurar una máquina virtual de Azure para hospedar Django. Aunque este tutorial explica cómo llevar a cabo esta acción en **Windows Server**, también se puede hacer lo mismo con una máquina virtual de Linux hospedada en Azure.
-   Crear una nueva aplicación Django desde Windows.

Mediante este tutorial, se compilará una aplicación web Hola mundo
sencilla. que se hospedará en una máquina virtual de Azure.

A continuación se muestra una captura de pantalla de la aplicación completada:

![Ventana del explorador que muestra la página Hello World en Azure][Ventana del explorador que muestra la página Hello World en Azure]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Creación y configuración de una máquina virtual de Azure para hospedar Django

1.  Siga las instrucciones que se proporcionan [aquí][aquí] para crear una máquina virtual de Azure de la distribución *Windows Server 2012 R2 Datacenter*.

2.  Indique a Azure que dirija el tráfico del puerto **80** de la Web al puerto **80** de la máquina virtual:

-   Diríjase hasta la máquina virtual recién creada en el Portal de Azure y haga clic en la pestaña *ENDPOINTS*.
-   Haga clic en el botón *AGREGAR* en la parte inferior de la pantalla.
    ![add endpoint][add endpoint]

-   Abra el puerto *PUBLIC PORT 80* del protocolo *TCP* como *PRIVATE PORT 80*.
    ![][0]

1.  En la pestaña *PANEL*, haga clic en *CONECTAR* para utilizar *Escritorio remoto* a fin de iniciar sesión de forma remota en la máquina virtual de Azure recién creada.

**Nota importante:** en las instrucciones que aparecen a continuación se asume que no tuvo problemas para iniciar sesión en la máquina virtual y que está generando comandos desde allí y no desde la máquina local.

## <span id="setup"></span> </a>Configuración de Python y Django

**Nota:** si desea realizar la descarga utilizando Internet Explorer, es posible que deba ajustar la configuración de ESC de Internet Explorer (Inicio/Herramientas administrativas/Administrador de servidores/Servidor local, a continuación haga clic en **Configuración de seguridad mejorada de IE** y desactive esta opción).

1.  Instale el [Instalador de la plataforma web][Instalador de la plataforma web].
2.  Instale Python y WFastCGI utilizando el instalador de la plataforma web. Esto instalará wfastcgi.py en su carpeta de scripts de Python.

    1.  Inicie el Instalador de la plataforma web.
    2.  Escriba WFastCGI en la barra de búsqueda.
    3.  Seleccione la entrada WFactCGI para la versión de Python que desea utilizar (2.7 o 3.4). Tenga en cuenta que de este modo se instalará Python como una dependencia de WFastCGI.

3.  Si instaló Python 2.7, [siga estas instrucciones para instalar pip manualmente][siga estas instrucciones para instalar pip manualmente] (Python 3.4 ya incluye pip).
4.  Instale Django mediante pip.

    Python 2,7:

        c:\python27\scripts\pip install django

    Python 3.4:

        c:\python34\scripts\pip install django

## Configuración de IIS con FastCGI

1.  Instale ISS habilitado para FastCGI. Esto puede tardar varios minutos en ejecutarse.

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

### Python 2,7

Ejecute estos comandos solo si está utilizando Python 2,7.

1.  Configure el controlador de FastCGI de Python.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"

2.  Registre el controlador para el sitio.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"

3.  Configure el controlador para ejecutar la aplicación Django.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost

4.  Configure PYTHONPATH para que el intérprete de Python pueda encontrar la aplicación Django.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost

5.  Indique a la puerta de enlace entre FastCGI y WSGI qué controlador de WSGI utilizar.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

6.  Verá lo siguiente:

    ![Configuración 1 de IIS][Configuración 1 de IIS]

### Python 3.4

Ejecute estos comandos solo si está utilizando Python 3.4.

1.  Configure el controlador de FastCGI de Python.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py']"

2.  Registre el controlador para el sitio.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python34\python.exe|C:\Python34\Scripts\wfastcgi.py',resourceType='Unspecified']"

3.  Configure el controlador para ejecutar la aplicación Django.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost

4.  Configure PYTHONPATH para que el intérprete de Python pueda encontrar la aplicación Django.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost

5.  Indique a la puerta de enlace entre FastCGI y WSGI qué controlador de WSGI utilizar.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

6.  Verá lo siguiente:

    ![Configuración 1 de IIS][1]

## Creación de una nueva aplicación Django

1.  En *C:\\inetpub\\wwwroot*, escriba el siguiente comando para crear un nuevo proyecto Django:

    Python 2,7:

        C:\Python27\Scripts\django-admin.exe startproject helloworld

    Python 3.4:

        C:\Python34\Scripts\django-admin.exe startproject helloworld

    ![Resultado del comando New-AzureService][Resultado del comando New-AzureService]

2.  El comando **django-admin** genera una estructura básica para los sitios web basados en Django:

-   **helloworld\\manage.py** le ayuda a iniciar y detener el hospedaje del sitio web basado en Django.
-   **helloworld\\helloworld\\settings.py** contiene ajustes de Django para la aplicación.
-   **helloworld\\helloworld\\urls.py** contiene el código de asignación entre cada dirección URL y su vista.

1.  Cree un nuevo archivo denominado **views.py** en el directorio *C:\\inetpub\\wwwroot\\helloworld\\helloworld*. Este contendrá la vista que representa la página "hello world". Inicie el editor y escriba lo siguiente:

        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

2.  Ahora, reemplace el contenido del archivo **urls.py** por lo siguiente:

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )

3.  Por último, cargue la página web en el explorador.

![Ventana del explorador que muestra la página Hello World en Azure][Ventana del explorador que muestra la página Hello World en Azure]

## Apagado de la máquina virtual de Azure

Cuando finalice este tutorial, apague o quite la máquina virtual de Azure recién creada para liberar recursos para otros tutoriales y así evitar incurrir en cargos por uso de Azure.

  [Ventana del explorador que muestra la página Hello World en Azure]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png
  [aquí]: /es-es/manage/windows/tutorials/virtual-machine-from-gallery/
  [add endpoint]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png
  [0]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png
  [Instalador de la plataforma web]: http://www.microsoft.com/web/downloads/platform.aspx
  [siga estas instrucciones para instalar pip manualmente]: https://pip.pypa.io/en/latest/installing.html
  [Configuración 1 de IIS]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-27.png
  [1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-34.png
  [Resultado del comando New-AzureService]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png
