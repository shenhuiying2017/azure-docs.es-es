---
title: "Aplicación web Python con Django en Linux | Microsoft Docs"
description: "Obtenga información acerca de cómo hospedar una aplicación web basada en Django en Azure con una máquina virtual de Linux."
services: virtual-machines-linux
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-resource-manager
ms.assetid: 00ad4c2c-4316-4f9a-913f-f7f49b158db7
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: python
ms.topic: article
ms.date: 11/17/2015
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 93add0f19bef64b236933a3d1754f1ecb5826545
ms.lasthandoff: 04/03/2017


---
# <a name="django-hello-world-web-application-on-a-linux-vm"></a>Aplicación web Django Hello World en una máquina virtual Linux
> [!div class="op_single_selector"]
> * [Windows](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Mac o Linux](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

<br>

En este tutorial se describe cómo hospedar un sitio web basado en Django en Microsoft Azure con una máquina virtual de Linux. En este tutorial se asume que no tiene ninguna experiencia previa con Azure. Al término de esta guía, tendrá una aplicación basada en Django funcionando en la nube.

Aprenderá a:

* Configurar una máquina virtual de Azure para hospedar Django. Aunque en este tutorial se explica cómo llevar a cabo esta acción en **Linux**, también se puede hacer lo mismo con una máquina virtual de Windows Server hospedada en Azure. 
* Crear una aplicación Django desde Linux.

Mediante este tutorial, se compilará una aplicación web Hello World sencilla que se hospedará en una máquina virtual de Azure.

Captura de pantalla de la aplicación completa:

![Ventana del explorador que muestra la página Hello World en Azure](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

[!INCLUDE [create-account-and-vms-note](../../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Creación y configuración de una máquina virtual de Azure para hospedar Django
1. Siga las instrucciones que se proporcionan [aquí](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para crear una máquina virtual de Azure de la distribución *Ubuntu Server 14.04 LTS*.  Si lo desea, puede elegir una autenticación de contraseña en lugar de usar la clave pública SSH.
2. Edite el grupo de seguridad de red para permitir el tráfico HTTP entrante al puerto 80 mediante las instrucciones que encontrará [aquí](../../virtual-network/virtual-networks-create-nsg-arm-pportal.md).
3. De forma predeterminada, la nueva máquina virtual no tiene un nombre de dominio completo (FQDN).  Puede crear uno siguiendo las instrucciones que encontrará [aquí](../windows/portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  Este paso es opcional para completar el tutorial.

## <a id="setup"> </a>Configuración del entorno de desarrollo
**Nota:** si es necesario instalar Python o desea usar las bibliotecas de clientes, consulte la [Guía de instalación de Python](../../python-how-to-install.md).

La máquina virtual de Linux (Ubuntu) ya tiene preinstalado Python 2.7, pero no incluye Apache o Django.  Siga estos pasos para conectarse a la máquina virtual e instalar Apache y Django.

1. Abra una nueva ventana **Terminal** .
2. Escriba el siguiente comando para conectarse a la máquina virtual de Azure.  Si no creó un FQDN, puede conectarse usando la dirección IP pública que se muestra en la máquina virtual de resumen en el Portal de Azure clásico.
   
       $ ssh yourusername@yourVmUrl
3. Escriba los siguientes comandos para instalar Django:
   
       $ sudo apt-get install python-setuptools python-pip
       $ sudo pip install django
4. Escriba el siguiente comando para instalar Apache con mod-wsgi:
   
       $ sudo apt-get install apache2 libapache2-mod-wsgi

## <a name="creating-a-new-django-application"></a>Creación de una nueva aplicación Django
1. Abra la ventana **Terminal** utilizada en la sección anterior para conectarse a la máquina virtual mediante el comando ssh.
2. Escriba los siguientes comandos para crear un proyecto Django:
   
       $ cd /var/www
       $ sudo django-admin.py startproject helloworld
   
   El script **django-admin.py** genera una estructura básica para los sitios web basados en Django:
   
   * **helloworld/manage.py** le ayuda a iniciar y detener el hospedaje del sitio web basado en Django.
   * **helloworld/helloworld/settings.py** contiene ajustes de Django para la aplicación.
   * **helloworld/helloworld/urls.py** contiene el código de asignación entre cada dirección URL y su vista.
3. Cree un nuevo archivo denominado **views.py** en el directorio **/var/www/helloworld/helloworld**. Este contendrá la vista que representa la página "hello world". Inicie el editor y escriba lo siguiente:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Ahora, reemplace el contenido del archivo **urls.py** por lo siguiente.
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="setting-up-apache"></a>Instalación de Apache
1. Cree un archivo de configuración del host virtual **/etc/apache2/sites-available/helloworld.conf**. Configure los contenidos de la siguiente manera y asegúrese de sustituir el elemento *yourVmName* por el nombre real de la máquina que va a usar (por ejemplo, *pyubuntu*).
   
       <VirtualHost *:80>
       ServerName yourVmName
       </VirtualHost>
       WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
       WSGIPythonPath /var/www/helloworld
2. Habilite el sitio con el comando siguiente:
   
       $ sudo a2ensite helloworld
3. Reinicie Apache con el comando siguiente:
   
       $ sudo service apache2 reload
4. Por último, cargue la página web en el explorador:
   
   ![Ventana del explorador que muestra la página Hello World en Azure](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

## <a name="shutting-down-your-azure-virtual-machine"></a>Apagado de la máquina virtual de Azure
Cuando finalice este tutorial, apague o quite la máquina virtual de Azure recién creada para liberar recursos para otros tutoriales y así evitar incurrir en cargos por uso de Azure.


