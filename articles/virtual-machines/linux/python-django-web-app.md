---
title: "Aplicación web Python con Django en una máquina virtual Linux de Azure | Microsoft Docs"
description: "Aprenda a hospedar una aplicación web basada en Django en Azure con una máquina virtual Linux."
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
ms.date: 05/31/2017
ms.author: huvalo
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 6e2ab8c7da7496d0e2b567a4bdc9341adcf01552
ms.contentlocale: es-es
ms.lasthandoff: 06/22/2017


---
# <a name="django-hello-world-web-app-on-a-linux-vm"></a>Aplicación web Django Hello World en una máquina virtual Linux
> [!div class="op_single_selector"]
> * [Windows](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Mac o Linux](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

<br>

En este tutorial se muestra cómo hospedar un sitio web basado en Django para Linux en Azure Virtual Machines. En el tutorial, se asume que no tiene experiencia con Azure. Cuando lo termine, tendrá una aplicación basada en Django que funcionará en la nube.

Obtenga información sobre cómo:

* Configure una máquina virtual de Azure para hospedar Django. Aunque en este tutorial se explica cómo hacer esto para **Linux**, puede hacer lo mismo para una máquina virtual de Windows Server hospedada en Azure. 
* Cree una nueva aplicación Django en Linux.

En el tutorial se muestra cómo crear una aplicación web básica de Hola mundo. La aplicación se hospeda en una máquina virtual de Azure.

La siguiente captura de pantalla muestra la aplicación completada:

![Ventana del explorador que muestra la página Hola mundo en Azure](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

[!INCLUDE [create-account-and-vms-note](../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Creación y configuración de una máquina virtual de Azure para hospedar Django

1. Para crear una máquina virtual de Azure con la distribución de Ubuntu Server 14.04 LTS, consulte [Creación de una máquina virtual Linux en Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). También puede elegir una autenticación de contraseña en lugar de usar la clave pública SSH.
2. Para editar el grupo de seguridad de red para que permita el tráfico HTTP entrante al puerto 80, consulte [Creación de grupos de seguridad de red en Azure Portal](../../virtual-network/virtual-networks-create-nsg-arm-pportal.md).
3. (Opcional) De forma predeterminada, la nueva máquina virtual no tiene un nombre de dominio completo (FQDN).  Para crear una máquina virtual con un nombre de dominio completo, consulte [Creación de un nombre de dominio completo en Azure Portal para una máquina virtual Windows](../windows/portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Este paso no es necesario para completar este tutorial.

## <a id="setup"> </a>Configuración del entorno de desarrollo
> [!NOTE]
> Si es necesario instalar Python o desea usar las bibliotecas de clientes, consulte la [Guía de instalación de Python](../../python-how-to-install.md).

La máquina virtual de Ubuntu Linux tiene Python 2.7 preinstalado, pero este no viene incluido con Apache o Django. Realice los pasos siguientes para conectarse a la máquina virtual e instalar Apache y Django:

1. Abra una nueva ventana de terminal.
2. Escriba el siguiente comando para conectarse a la máquina virtual de Azure. Si no creó un FQDN, puede conectarse mediante la dirección IP pública que se muestra en el resumen de la máquina virtual en Azure Portal.
   
       $ ssh yourusername@yourVmUrl
3. Escriba los siguientes comandos para instalar Django:
   
       $ sudo apt-get install python-setuptools python-pip
       $ sudo pip install django
4. Escriba el siguiente comando para instalar Apache con mod-wsgi:
   
       $ sudo apt-get install apache2 libapache2-mod-wsgi

## <a name="create-a-new-django-app"></a>Creación de una aplicación Django
1. Para utilizar SSH para acceder a la máquina virtual, abra la ventana de terminal que utilizó en la sección anterior.
2. Escriba los siguientes comandos para crear un proyecto Django:
   
       $ cd /var/www
       $ sudo django-admin.py startproject helloworld
   
   El script `django-admin.py` genera una estructura básica para los sitios web basados en Django:
   
   * `helloworld/manage.py` le ayuda a iniciar y detener el hospedaje del sitio web basado en Django.
   * `helloworld/helloworld/settings.py` tiene la configuración de Django para la aplicación.
   * `helloworld/helloworld/urls.py` tiene el código de asignación entre las direcciones URL y sus vistas respectivas.
3. Cree un nuevo archivo denominado views.py en el directorio /var/www/helloworld/helloworld. Este archivo contiene la vista que representa la página "hola mundo". En el editor de código, escriba los siguientes comandos:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Reemplace el contenido del archivo urls.py por los comandos siguientes:
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-apache"></a>Instalación de Apache
1. Cree un archivo de configuración del host virtual de Apache en la carpeta /etc/apache2/sites-available/helloworld.conf. Establezca el contenido en los valores siguientes. Sustituya el elemento *yourVmName* por el nombre real de la máquina que va a usar (por ejemplo, *pyubuntu*).
   
       <VirtualHost *:80>
       ServerName yourVmName
       </VirtualHost>
       WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
       WSGIPythonPath /var/www/helloworld
2. Para activar el sitio, use el comando siguiente:
   
       $ sudo a2ensite helloworld
3. Para reiniciar Apache, use el comando siguiente:
   
       $ sudo service apache2 reload
4. Cargue la página web en el explorador:
   
   ![Ventana del explorador que muestra la página Hola mundo en Azure](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

## <a name="shut-down-your-azure-virtual-machine"></a>Apagado de la máquina virtual de Azure
Cuando haya terminado este tutorial, es recomendable apagar o quitar la máquina virtual de Azure que se creó. Esto libera recursos para otros tutoriales y evita cargos de uso de Azure.


