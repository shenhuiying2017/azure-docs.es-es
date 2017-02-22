---
title: "Compilación de una aplicación Django en una máquina virtual de Azure | Microsoft Docs"
description: "En este tutorial se enseña cómo hospedar un sitio web basado en Django en Azure con una máquina virtual de Windows Server 2012 R2 Datacenter mediante el modelo de implementación clásica."
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-service-management
ms.assetid: e36484d1-afbf-47f5-b755-5e65397dc1c3
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 08/04/2015
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: 52967959e60ac64f5af2ae044803aa7443c5830b
ms.openlocfilehash: 3691a00dd9ce64b30435be98b3429627113128e3


---
# <a name="django-hello-world-web-application-on-a-windows-server-vm"></a>Aplicación web Django Hello World en una máquina virtual de Windows Server
> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-classic-python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Mac o Linux](virtual-machines-linux-python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

<br>

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Para obtener una plantilla de Resource Manager para implementar Django, consulte [aquí](https://azure.microsoft.com/documentation/templates/django-app/).

En este tutorial se describe cómo hospedar un sitio web basado en Django en Microsoft Azure utilizando una máquina virtual de Windows Server. En este tutorial se asume que no tiene ninguna experiencia previa con Azure. Al terminar este tutorial, tendrá una aplicación basada en Django que funcionará en la nube.

Aprenderá a:

* Configure una máquina virtual de Azure para hospedar Django. Aunque este tutorial explica cómo llevar a cabo esta acción en Windows Server, también se puede hacer lo mismo con una máquina virtual de Linux hospedada en Azure.
* Crear una nueva aplicación Django desde Windows.

Mediante este tutorial, se compilará una aplicación web Hello World sencilla que se hospedará en una máquina virtual de Azure.

A continuación se muestra una captura de pantalla de la aplicación completada:

![Ventana del explorador que muestra la página Hello World en Azure][1]

[!INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Creación y configuración de una máquina virtual de Azure para hospedar Django
1. Siga las instrucciones que se indican [aquí](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) para crear una máquina virtual de Azure de la distribución Windows Server 2012 R2 Datacenter.
2. Indique a Azure que dirija el tráfico del puerto 80 de la Web al puerto 80 de la máquina virtual:
   
   * Diríjase hasta la máquina virtual recién creada en el Portal de Azure clásico y haga clic en la pestaña **ENDPOINTS** .
   * Haga clic en el botón **ADD** situado en la parte inferior de la pantalla.
     ![agregar extremo](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-addendpoint.png)
   * Abra el **puerto público 80** del protocolo**TCP** como **puerto privado 80**.
     ![][port80]
3. En la pestaña **PANEL**, haga clic en **CONECTAR** para usar **Escritorio remoto** a fin de iniciar sesión de forma remota en la máquina virtual de Azure recién creada.  

**Nota importante:** en todas las instrucciones que aparecen a continuación se considera que no tuvo problemas para iniciar sesión en la máquina virtual y que está generando comandos desde allí y no desde la máquina local.

## <a id="setup"> </a>Instalación de Python, Django, WFastCGI
**Nota:** Si desea realizar la descarga mediante Internet Explorer, es posible que deba ajustar la configuración de ESC de Internet Explorer (Inicio/Herramientas administrativas/Administrador de servidores/Servidor local; a continuación haga clic en **Configuración de seguridad mejorada de IE** y desactive esta opción).

1. Instale la Python 2.7 o 3.4 más reciente de [python.org][python.org].
2. Instale los paquetes wfastcgi y django con pip.
   
    Para Python 2.7, use el siguiente comando.
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    Para Python 3.4, use el siguiente comando.
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="installing-iis-with-fastcgi"></a>Instalación de IIS con FastCGI
1. Instale ISS habilitado para FastCGI.  Esto puede tardar varios minutos en ejecutarse.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="creating-a-new-django-application"></a>Creación de una nueva aplicación Django
1. En *C:\inetpub\wwwroot*, escriba el siguiente comando para crear un nuevo proyecto Django:
   
   Para Python 2.7, use el siguiente comando.
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   Para Python 3.4, use el siguiente comando.
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![Resultado del comando New-AzureService](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. El comando **django-admin** genera una estructura básica para los sitios web basados en Django:
   
   * **helloworld/manage.py** le ayuda a iniciar y detener el hospedaje del sitio web basado en Django.
   * **helloworld/helloworld/settings.py** contiene ajustes de Django para la aplicación.
   * **helloworld/helloworld/urls.py** contiene el código de asignación entre cada dirección URL y su vista.
3. Cree un archivo denominado **views.py** en el directorio *C:\inetpub\wwwroot\helloworld\helloworld*. Este contendrá la vista que representa la página "hello world". Inicie el editor y escriba lo siguiente:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Reemplace el contenido del archivo urls.py por lo siguiente:
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="configuring-iis"></a>Configuración de IIS
1. Desbloquee la sección de controladores del archivo applicationhost.config global.  Esto le permitirá el uso del controlador de python en el archivo web.config.
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. Habilite WFastCGI.  Así agregará una aplicación al archivo applicationhost.config global que hace referencia al ejecutable del intérprete de Python y al script wfastcgi.py.
   
    Python 2.7:
   
        c:\python27\scripts\wfastcgi-enable
   
    Python 3.4:
   
        c:\python34\scripts\wfastcgi-enable
3. Cree un archivo web.config en *C:\inetpub\wwwroot\helloworld*.  El valor del atributo `scriptProcessor` debe coincidir con la salida del paso anterior.  Vea la página de [wfastcgi][wfastcgi] en pypi para más información sobre la configuración de wfastcgi.
   
    Python 2.7:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
   
    Python 3.4:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
4. Actualice la ubicación del sitio web predeterminado de IIS para que señale a la carpeta de proyecto de django.
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. Por último, cargue la página web en el explorador.

![Ventana del explorador que muestra la página Hello World en Azure][1]

## <a name="shutting-down-your-azure-virtual-machine"></a>Apagado de la máquina virtual de Azure
Cuando finalice este tutorial, apague o quite la máquina virtual de Azure recién creada para liberar recursos para otros tutoriales y así evitar incurrir en cargos por uso de Azure.

[1]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi



<!--HONumber=Jan17_HO4-->


