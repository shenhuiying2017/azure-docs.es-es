<properties 
	pageTitle="Instalación de Python y el SDK - Azure" 
	description="Obtenga información para instalar Python y el SDK para usarlos con Azure." 
	services="" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="huvalo"/>




# Instalación de Python y el SDK
Python es muy fácil de configurar en Windows y viene preinstalado en Mac y Linux.  En esta guía se realizará un recorrido por la instalación y preparación de la máquina para su uso con Azure.  Esta guía le ayudará con lo siguiente:

* ¿Qué es el SDK de Azure para Python?
* Qué es Python y qué versión usar
* Instalación en Windows
* Instalación en Mac y Linux

## ¿Qué es el SDK de Azure para Python?

El SDK de Azure para Python incluye componentes que le permiten desarrollar, implementar y administrar aplicaciones de Python para Azure. Específicamente, el SDK de Azure para Python incluye lo siguiente:

* **Las bibliotecas de clientes Python para Azure**. Estas bibliotecas de clases proporcionan una interfaz para tener acceso a características de Azure, como los servicios de administración de datos y los servicios en la nube.  
* **Los emuladores de Azure (solo Windows)**. Los emuladores de proceso y almacenamiento son emuladores locales de los servicios en la nube y los servicios de administración de datos que le permiten probar localmente una aplicación. Los emuladores de Azure solo se ejecutan en Windows.


Los escenarios principales para esta versión son los siguientes:

* **Windows**: Servicio en la nube, por ejemplo, un sitio de Django con roles web.
* **Mac/Linux**: IaaS; ejecución de lo que desee en una VM; consumo de servicios de Azure a través de Python.

## Qué es Python y qué versión usar

Existen varios modelos de intérpretes Python disponibles, entre los ejemplos se incluyen los siguientes:

* CPython: El intérprete Python estándar y que se usa con más frecuencia.
* IronPython: El intérprete Python que se ejecuta en .Net/CLR.
* Jython: El intérprete Python que se ejecuta en JVM.

Para los propósitos de esta versión, solo se prueba y admite **CPython**.  Recomendamos las versiones 2.7 o 3.4.

## ¿Dónde obtener Python?

Existen diferentes formas de obtener CPython:

* Directamente desde www.python.org.
* Desde una distro de confianza como www.enthought.com, www.ActiveState.com o www.continuum.io
* Creación a partir del origen.

A menos que tenga una necesidad específica, recomendamos las dos primeras opciones tal y como se describe a continuación.

## Instalación en Windows

Para Windows, puede utilizar el instalador de la plataforma web para simplificar la instalación. Se incluye CPython de www.python.org.

* [SDK de Microsoft Azure para Python 2.7][]
* [SDK de Microsoft Azure para Python 3.4][]

**Nota:** En Windows Server, para descargar el instalador de WebPI es posible que tenga que configurar los ajustes ESC de IE (Inicio/Herramientas administrativas/Administrador del servidor/Servidor local), luego hacer clic en **Configuración de seguridad mejorada de IE** y establecerlo como Desactivado).


### Python 2,7

El instalador de WebPI proporciona todo lo que necesita para desarrollar aplicaciones de Azure para Python.

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

Una vez finalizado el proceso, debe ver esta pantalla de configuración de las opciones de instalación:

![how-to-install-python-webpi-27-2](./media/python-how-to-install/how-to-install-python-webpi-27-2.png)

Una vez que se haya completado la instalación, escriba "python" cuando se le solicite para asegurarse de que el proceso se ha realizado sin interrupciones.  Según la forma en la que haya realizado la instalación, es posible que tenga que establecer la variable "path" para buscar la versión correcta de Python:

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

Una vez completada la instalación, debe tener las bibliotecas de clientes y de Python disponibles en la ubicación predeterminada:

		C:\Python27\Lib\site-packages\azure


### Python 3.4

El instalador de WebPI proporciona todo lo que necesita para desarrollar aplicaciones de Azure para Python.

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)
 
Una vez finalizado el proceso, debe ver esta pantalla de configuración de las opciones de instalación:

![how-to-install-python-webpi-34-2](./media/python-how-to-install/how-to-install-python-webpi-34-2.png)

Una vez que se haya completado la instalación, escriba "python" cuando se le solicite para asegurarse de que el proceso se ha realizado sin interrupciones.  Según la forma en la que haya realizado la instalación, es posible que tenga que establecer la variable "path" para buscar la versión correcta de Python:

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

Una vez completada la instalación, debe tener las bibliotecas de clientes y de Python disponibles en la ubicación predeterminada:

		C:\Python34\Lib\site-packages\azure


### Instalación de otros paquetes

Aunque esta versión se centra principalmente en las aplicaciones web, puede examinar el [índice de paquetes de Python (PyPI)](http://pypi.python.org/pypi) para ver una abundante selección de otras soluciones de software.  Si ha optado por instalar una distribución, ya dispondrá de los bits más interesantes para diversos escenarios que van desde el desarrollo web a la informática técnica.


### Python Tools para Visual Studio

Python Tools para Visual Studio (PTVS) es un complemento gratuito de OSS de Microsoft que convierte VS en un IDE de Python completo:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

El uso de PTVS es opcional, pero es recomendable, ya que le proporciona compatibilidad con soluciones o proyectos de Django y Python, depuración, creación de perfiles, ventana interactiva, edición de plantillas e IntelliSense.

PTVS también simplifica la implementación en Microsoft Azure, con soporte para [la implementación en Sitios web y Servicios en la nube][].

PTVS funciona con su instalación de Visual Studio 2010, 2012 o 2013 existente.  Para obtener más información y acceder a las descargas, consulte [Python Tools for Visual Studio on CodePlex][].  

## Desinstalación en Windows

Los productos WebPI de **SDK de Azure para Python** no son aplicaciones en el sentido habitual, sino una colección de productos distintos como Python 2.7/3.4 de 32 bits, bibliotecas de cliente de Azure para Python, etc. que se empaquetan juntos.  Una consecuencia de esto es que no existe un desinstalador convencional propio, por lo que tendrá que quitar los programas que instale individualmente desde el Panel de control de Windows.  

Si desea volver a instalar el **SDK de Azure para Python**, simplemente abra un símbolo del sistema de PowerShell como administrador y ejecute el siguiente comando:

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

A continuación, vuelva a ejecuta WebPI.

## Instalación en Linux y MacOS

Lo más probable es que Python ya esté instalado en su equipo de desarrollo.  Puede comprobarlo especificando:

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

Aquí podemos ver que este servidor de Ubuntu 14.04 LTS VM que se ejecuta en Azure tiene CPython 2.7.6 instalado. Si necesita actualizar, siga las instrucciones recomendadas de actualización de paquetes del SO.

Para instalar las bibliotecas de clientes de Azure para Python, use **pip** para realizar la selección desde **PyPI**:

	curl https://bootstrap.pypa.io/get-pip.py | sudo python
	
El comando anterior le solicitará de manera silenciosa la contraseña raíz. Escríbala y presione Entrar.  Pasos siguientes:
	
	sudo pip install azure

Ahora debe ver las bibliotecas de clientes instaladas en **site-packages**.  En MacOS:

![how-to-install-python-mac-site](./media/python-how-to-install/how-to-install-python-mac-site.png)

Cuando se realiza el desarrollo desde Mac/Linux, existen dos escenarios principales compatibles:

1. Consumo de servicios de Azure mediante bibliotecas de clientes para Python

2. Ejecución de la aplicación en la VM de Linux

El primer escenario le permite crear aplicaciones web enriquecidas que aprovechan las capacidades de PaaS de Azure como colas, almacenamiento de blobs, etc. a través de contenedores de Python para la API de REST de Azure. El funcionamiento es idéntico en Windows, Mac y Linux.  Consulte los tutoriales y las guías de procedimientos para ver ejemplos.  También puede usar estas bibliotecas de clientes desde dentro de una VM de Linux.

En el escenario de VM, simplemente inicie la VM de Linux que elija (Ubuntu, CentOS y Suse) y ejecute o administre lo que desee.  Por ejemplo, puede ejecutar el bloc de notas o el REPL de [IPython](http://ipython.org) en la máquina de Windows/Mac/Linux y configurar el explorador para que apunte a una VM multiproceso de Linux o Windows que ejecute el motor de IPython en Azure. Para obtener más información sobre la instalación de IPython, consulte el tutorial.

Para obtener información sobre cómo configurar una VM de Linux, consulte la [sección de administración de Linux.](/es-es/manage/linux/)

## Recursos y software adicionales:

* [Enthought Python Distribution][]
* [ActiveState Python Distribution][]
* [SciPy - A suite of Scientific Python libraries][]
* [NumPy - A numerics library for Python][]
* [Django Project - A mature web framework/CMS][]
* [IPython - an advanced REPL/Notebook for Python][]
* [Bloc de notas de IPython en Azure][]
* [Python Tools for Visual Studio on CodePlex][]
* [Virtualenv][]


[Enthought Python Distribution]: http://www.enthought.com 
[ActiveState Python Distribution]: http://www.activestate.com
[SciPy - A suite of Scientific Python libraries]: http://www.scipy.org
[NumPy - A numerics library for Python]: http://www.numpy.org
[Django Project - A mature web framework/CMS]: http://www.djangoproject.com 
[IPython - an advanced REPL/Notebook for Python]: http://ipython.org
[Bloc de notas de IPython en Azure]: /es-es/documentation/articles/virtual-machines-python-ipython-notebook
[la implementación en Sitios web y Servicios en la nube]: /es-es/documentation/articles/cloud-services-web-sites-python-django-app-with-ptvs/
[Python Tools for Visual Studio on CodePlex]: http://pytools.codeplex.com 
[Índice de paquetes de Python (PyPI)]: http://pypi.python.org/pypi
[Virtualenv]: http://pypi.python.org/pypi/virtualenv 
[SDK de Microsoft Azure para Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[SDK de Microsoft Azure para Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409
[Configuración de una VM de Linux mediante el portal de Azure]: ../../../shared/chunks/create-and-configure-opensuse-vm-in-portal
[Uso de las herramientas de línea de comandos de Azure para Mac y Linux]: ../../shared/chunks/crossplat-cmd-tools


<!--HONumber=46--> 
