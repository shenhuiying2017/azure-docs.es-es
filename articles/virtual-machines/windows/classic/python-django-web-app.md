---
title: "Aplicación web de Django web en una máquina virtual de Azure Windows Server | Microsoft Docs"
description: "Aprenda a hospedar un sitio web basado en Django en Azure con una máquina virtual de Windows Server 2012 R2 Datacenter mediante el modelo de implementación clásica."
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
ms.date: 05/31/2017
ms.author: huvalo
ms.openlocfilehash: 01fa162d41e03e29f3b6f0ca128e7cc49aa91abb
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2017
---
# <a name="django-hello-world-web-app-on-a-windows-server-vm"></a>Aplicación web Django Hola mundo en una máquina virtual de Windows Server

> [!IMPORTANT] 
> Azure ofrece dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Azure Resource Manager y el modelo de implementación clásica](../../../resource-manager-deployment-model.md). Este artículo describe el modelo de implementación clásica. Se recomienda que las implementaciones más recientes usen el modelo Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

En este tutorial se muestra cómo hospedar un sitio web basado en Django para Windows Server en Azure Virtual Machines. En el tutorial, se asume que no tiene experiencia con Azure. Cuando lo termine, tendrá una aplicación basada en Django que funcionará en la nube.

Obtenga información sobre cómo:

* Configure una máquina virtual de Azure para hospedar Django. Aunque en este tutorial se explica cómo hacer esto para **Windows Server**, puede hacer lo mismo para una máquina virtual Linux hospedada en Azure.
* Crear una aplicación Django en Windows.

En el tutorial se muestra cómo crear una aplicación web básica de Hola mundo. La aplicación se hospeda en una máquina virtual de Azure.

La siguiente captura de pantalla muestra la aplicación completada:

![Ventana del explorador que muestra la página Hola mundo en Azure][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Creación y configuración de una máquina virtual de Azure para hospedar Django

1. Para crear una máquina virtual de Azure con la distribución de Windows Server 2012 R2 Datacenter, vea [Creación de una máquina virtual que ejecuta Windows en Azure Portal](tutorial.md).
2. Indique a Azure que dirija el tráfico del puerto 80 de la web al puerto 80 de la máquina virtual:
   
   1. En Azure Portal, vaya al panel y seleccione la máquina virtual recién creada.
   2. Haga clic en **Puntos de conexión** y luego en **Agregar**.

     ![Agregación de un extremo](./media/python-django-web-app/django-helloworld-add-endpoint-new-portal.png)

   3. En la página **Agregar punto de conexión**, para **Nombre**, escriba **HTTP**. Establezca de puertos TCP público y privado en **80**.

     ![Escritura del nombre y configuración de los puertos público y privado](./media/python-django-web-app/django-helloworld-add-endpoint-set-ports-new-portal.png)

   4. Haga clic en **Aceptar**.
     
3. En el panel, seleccione la máquina virtual. Para usar el protocolo de escritorio remoto (RDP) para iniciar sesión de forma remota en la máquina virtual de Azure recién creada, haga clic en **Conectar**.  

> [!IMPORTANT] 
> En las siguientes instrucciones se presupone que ha iniciado sesión en la máquina virtual correctamente. También se presupone que se realiza los comandos en la máquina virtual y no en la máquina local.

## <a id="setup"></a>Instalación de Python, Django y WFastCGI
> [!NOTE]
> Para realizar la descarga mediante Internet Explorer, es posible que deba definir su **Configuración de seguridad mejorada**. Para ello, haga clic en **Inicio** > **Herramientas administrativas** > **Administrador del servidor** > **Servidor local**. Haga clic en **Configuración de seguridad mejorada de IE** y seleccione **Desactivar**.

1. Instale las últimas versiones de Python 2.7 o 3.4 de Python de [python.org][python.org].
2. Instale los paquetes wfastcgi y django con pip.
   
    Para Python 2.7, use el siguiente comando:
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    Para Python 3.4, use el siguiente comando:
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="install-iis-with-fastcgi"></a>Instalación de ISS con FastCGI
* Instale Internet Information Services (IIS) con compatibilidad para FastCGI. Esto puede tardar varios minutos en ejecutarse.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="create-a-new-django-application"></a>Creación de una aplicación Django
1. En C:\inetpub\wwwroot, para crear un proyecto Django, escriba el siguiente comando:
   
   Para Python 2.7, use el siguiente comando:
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   Para Python 3.4, use el siguiente comando:
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![Resultado del comando New-AzureService](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. El comando `django-admin` genera una estructura básica para los sitios web basados en Django:
   
   * `helloworld\manage.py` le ayuda a iniciar y detener el hospedaje del sitio web basado en Django.
   * `helloworld\helloworld\settings.py` tiene la configuración de Django para la aplicación.
   * `helloworld\helloworld\urls.py` tiene el código de asignación entre las direcciones URL y sus vistas respectivas.
3. En el directorio C:\inetpub\wwwroot\helloworld\helloworld, cree un archivo denominado views.py. Este archivo contiene la vista que representa la página "hola mundo". En el editor de código, escriba los siguientes comandos:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Reemplace el contenido del archivo urls.py por los comandos siguientes:
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-iis"></a>Configuración de IIS
1. Desbloquee la sección de controladores del archivo applicationhost.config global.  Esto permite que el archivo web.config use el controlador de Python. Agregue este comando:
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. Active WFastCGI. Así agregará una aplicación al archivo applicationhost.config global que hace referencia al archivo ejecutable del intérprete de Python y al script wfastcgi.py.
   
    En Python 2.7:
   
        C:\python27\scripts\wfastcgi-enable
   
    En Python 3.4:
   
        C:\python34\scripts\wfastcgi-enable
3. Cree un archivo web.config en C:\inetpub\wwwroot\helloworld. El valor del atributo `scriptProcessor` debe coincidir con la salida del paso anterior. Para más información acerca de la configuración de wfastcgi, consulte [pypi wfastcgi][wfastcgi].
   
   En Python 2.7:
   
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
   
   En Python 3.4:
   
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
4. Actualice la ubicación del sitio web predeterminado de IIS para que señale a la carpeta de proyecto de Django:
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. Cargue la página web en el explorador.

![Ventana del explorador que muestra la página Hola mundo en Azure][1]

## <a name="shut-down-your-azure-virtual-machine"></a>Apagado de la máquina virtual de Azure
Cuando haya terminado este tutorial, es recomendable apagar o quitar la máquina virtual de Azure que se creó. Esto libera recursos para otros tutoriales y evita cargos de uso de Azure.

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
