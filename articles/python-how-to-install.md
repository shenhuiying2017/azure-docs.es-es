<properties
	pageTitle="Instalación de Python y el SDK - Azure"
	description="Obtenga información para instalar Python y el SDK para usarlos con Azure."
	services=""
	documentationCenter="python"
	authors="lmazuel"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="lmazuel"/>

# Instalación de Python y el SDK

Python es fácil de configurar en Windows y viene preinstalado en Mac, Linux y [Bash para Windows](https://msdn.microsoft.com/commandline/wsl/about). En esta guía se realizará un recorrido por la instalación y preparación de la máquina para su uso con Azure.

## ¿Qué es el SDK de Azure para Python?

El SDK de Azure para Python incluye componentes que le permiten desarrollar, implementar y administrar aplicaciones de Python para Azure. Específicamente, el SDK de Azure para Python incluye lo siguiente:

* **Bibliotecas de administración**. Estas bibliotecas de clases proporcionan una interfaz de administración de recursos de Azure, como cuentas de almacenamiento y máquinas virtuales.

* **Bibliotecas tiempo de ejecución**. Estas bibliotecas de clases proporcionan una interfaz para tener acceso a funciones de Azure, como el bus de servicio y almacenamiento.

## Qué es Python y qué versión usar

Existen varios modelos de intérpretes Python disponibles, entre los ejemplos se incluyen los siguientes:

* CPython: El intérprete Python estándar y que se usa con más frecuencia.
* PyPy: implementación alternativa rápida y compatible con CPython
* IronPython: El intérprete Python que se ejecuta en .Net/CLR.
* Jython: el intérprete Python que se ejecuta en Java Virtual Machine

**CPython** v2.7 o v3.3+ y PyPy 5.4.0 se han probado y son compatibles con el SDK de Azure de Python.

## ¿Dónde obtener Python?

Existen diferentes formas de obtener CPython:

* Directamente desde [www.python.org][]
* Desde una distribución con reputación como [www.continuum.io][], [www.enthought.com][] o [www.activestate.com][]
* Creación a partir del origen.

A menos que tenga una necesidad específica, recomendamos las dos primeras opciones.

## Instalación del SDK en Windows, Linux y MacOS (solo bibliotecas de cliente)

Si ya tiene instalado Python, puede usar pip para instalar una agrupación de todas las bibliotecas de cliente en su entorno de Python 2.7 o Python 3.3+ existente. Esto descargará los paquetes del [Índice de paquetes de Python][] (PyPI).

Puede que necesite derechos de administrador:

- Linux y MacOS, utilice el comando de `sudo`: `sudo pip install azure-mgmt-compute`.
- Windows: abra un símbolo del sistema/PowerShell como administrador

Puede instalar individualmente cada biblioteca para cada servicio de Azure:

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

Los paquetes de versión preliminar pueden instalarse con el indicador `--pre`:

```console
   $ pip install --pre azure-mgmt-compute # will install only the latest Compute Management library
```

También puede instalar un conjunto de bibliotecas de Azure en una sola línea con el metapaquete `azure`. Puesto que no todos los paquetes de este metapaquete se publican como estables todavía, el metapaquete `azure` sigue estando en versión preliminar. Sin embargo, los paquetes principales, desde las perspectivas de calidad e integridad de código se pueden considerar "estables" en este momento
- se etiquetarán oficialmente como tal en sincronización con otros lenguajes tan pronto como sea posible. No estamos planeando cambios más importantes realizados hasta ese momento.

Puesto que es una versión preliminar, tiene que usar el indicador `--pre`:

```console
   $ pip install --pre azure
```
   
o directamente

```console
   $ pip install azure==2.0.0rc6
```

## Instalación de otros paquetes

[Python Package Index][] (Índice de paquetes de Python, PyPI) dispone de una abundante selección de bibliotecas de Python. Si ha optado por instalar una distribución, ya dispondrá de los bits más interesantes para varios escenarios que van desde el desarrollo web a la informática técnica.


## Python Tools para Visual Studio

[Python Tools para Visual Studio][] (PTVS) es un complemento gratuito de OSS de Microsoft que convierte VS en un IDE de Python completo:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

El uso de PTVS es opcional, pero es recomendable, ya que le proporciona compatibilidad con soluciones o proyectos de Web y Python, depuración, creación de perfiles, ventana interactiva, edición de plantillas e IntelliSense.

PTVS también simplifica la implementación en Microsoft Azure, con soporte para la implementación en [Servicios en la nube][] y [Sitios web][].

PTVS funciona con su instalación de Visual Studio 2013 o 2015 existente. Para obtener documentación, descargas y discusiones, consulte [Python Tools para Visual Studio].

## Escenarios de Python Azure para Linux y MacOS

Para Linux o Mac OS, los escenarios principales de Azure que se admiten:

1. Consumo de servicios de Azure mediante bibliotecas de clientes para Python

2. Ejecución de la aplicación en la VM de Linux

3. Desarrollar y publicar en sitios web de Azure mediante Git

El primer escenario le permite crear aplicaciones web enriquecidas que aprovechan las funcionalidades de PaaS de Azure como [Blob Storage][], [Queue Storage][], [Table Storage][] etc. a través de contenedores de Python para la API de REST de Azure. Estos funcionan de forma idéntica en Windows, Mac y Linux. También puede usar estas bibliotecas de cliente desde su equipo de desarrollo local o en una máquina virtual de Linux que se ejecute en Azure.

En el escenario de VM, simplemente inicie la VM de Linux que elija (Ubuntu, CentOS y Suse) y ejecute o administre lo que desee. Por ejemplo, puede ejecutar el bloc de notas o el REPL de [IPython][] en la máquina de Windows/Mac/Linux y configurar el explorador para que apunte a una máquina virtual multiproceso de Linux o Windows que ejecute el motor de IPython en Azure. Consulte el tutorial [Bloc de notas de IPython en Azure][] para obtener más información.

Para obtener información sobre cómo configurar una máquina virtual de Linux, consulte el tutorial [Creación de una máquina virtual que ejecuta Linux][].

Con la implementación de Git puede desarrollar una aplicación web de Python y publicarla en un sitio web de Azure desde cualquier sistema operativo. Cuando inserte el repositorio en Azure, creará automáticamente un entorno virtual y pip instala los paquetes necesarios.

Para obtener más información acerca del desarrollo y la publicación de Azure Websites, consulte los tutoriales para [Creación de sitios web con Django en Azure][], [Creación de sitios web con Bottle][] y [Creación de sitios web con Flask][]. Para obtener información general sobre el uso de cualquier marco de trabajo compatible con WSGI, consulte [Configuración de Python con Azure Websites][].


## Recursos y software adicionales:

* [SDK de Azure para Python ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [SDK de Azure para Python Github](https://github.com/Azure/azure-sdk-for-python)
* [Ejemplos oficiales de Azure para Python](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Distribución de Python de Continuum Analytics][]
* [Distribución de Python de Enthought][]
* [Distribución de Python de ActiveState][]
* [SciPy: un conjunto de bibliotecas de Scientific Python][]
* [NumPy: biblioteca de tipos numéricos para Python][]
* [Django Project: un CMS/marco de trabajo para web maduro][]
* [IPython: un bloc de notas/REPL avanzado para Python][]
* [Bloc de notas de IPython en Azure][]
* [Python Tools para Visual Studio en GitHub][]
* [Centro para desarrolladores de Python](/develop/python/)

[Distribución de Python de Continuum Analytics]: http://continuum.io
[Distribución de Python de Enthought]: http://www.enthought.com
[Distribución de Python de ActiveState]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy: un conjunto de bibliotecas de Scientific Python]: http://www.scipy.org
[NumPy: biblioteca de tipos numéricos para Python]: http://www.numpy.org
[Django Project: un CMS/marco de trabajo para web maduro]: http://www.djangoproject.com
[IPython: un bloc de notas/REPL avanzado para Python]: http://ipython.org
[IPython]: http://ipython.org
[Bloc de notas de IPython en Azure]: virtual-machines-linux-jupyter-notebook.md
[Servicios en la nube]: cloud-services-python-ptvs.md
[Sitios web]: web-sites-python-ptvs-django-mysql.md
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Python Tools para Visual Studio en GitHub]: https://github.com/microsoft/ptvs
[Python Package Index]: http://pypi.python.org/pypi
[Índice de paquetes de Python]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Creación de una máquina virtual que ejecuta Linux]: virtual-machines-linux-quick-create-cli.md
[Creación de sitios web con Django en Azure]: web-sites-python-create-deploy-django-app.md
[Creación de sitios web con Bottle]: web-sites-python-create-deploy-bottle-app.md
[Creación de sitios web con Flask]: web-sites-python-create-deploy-flask-app.md
[Configuración de Python con Azure Websites]: web-sites-python-configure.md
[table storage]: storage-python-how-to-use-table-storage.md
[queue storage]: storage-python-how-to-use-queue-storage.md
[blob storage]: storage-python-how-to-use-blob-storage.md

<!---HONumber=AcomDC_0914_2016-->