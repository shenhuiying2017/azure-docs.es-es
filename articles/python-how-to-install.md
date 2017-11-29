---
title: "Instalación de Python y el SDK - Azure"
description: "Obtenga información para instalar Python y el SDK para usarlos con Azure."
services: 
documentationcenter: python
author: lmazuel
manager: wpickett
editor: 
ms.assetid: f36294be-daeb-4caf-9129-fce18130f552
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/06/2016
ms.author: lmazuel
ms.openlocfilehash: e69fff29be5b12c3c0004b4101eba69c7da87d3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="installing-python-and-the-sdk"></a>Instalación de Python y el SDK
Python es fácil de configurar en Windows y viene preinstalado en Mac, Linux y [Bash para Windows](https://msdn.microsoft.com/commandline/wsl/about). En esta guía se realizará un recorrido por la instalación y preparación de la máquina para su uso con Azure.

## <a name="whats-in-the-python-azure-sdk"></a>¿Qué es el SDK de Azure para Python?
El SDK de Azure para Python incluye componentes que le permiten desarrollar, implementar y administrar aplicaciones de Python para Azure. Específicamente, el SDK de Azure para Python incluye lo siguiente:

* **Bibliotecas de administración**. Estas bibliotecas de clases proporcionan una interfaz de administración de recursos de Azure, como cuentas de almacenamiento y máquinas virtuales.
* **Bibliotecas tiempo de ejecución**. Estas bibliotecas de clases proporcionan una interfaz para tener acceso a funciones de Azure, como el bus de servicio y almacenamiento.

## <a name="which-python-and-which-version-to-use"></a>Qué es Python y qué versión usar
Existen varios modelos de intérpretes Python disponibles, entre los ejemplos se incluyen los siguientes:

* CPython: El intérprete Python estándar y que se usa con más frecuencia.
* PyPy: implementación alternativa rápida y compatible con CPython
* IronPython: El intérprete Python que se ejecuta en .Net/CLR.
* Jython: el intérprete Python que se ejecuta en Java Virtual Machine

**CPython** v2.7 o v3.3+ y PyPy 5.4.0 se han probado y son compatibles con el SDK de Azure de Python.

## <a name="where-to-get-python"></a>¿Dónde obtener Python?
Existen diferentes formas de obtener CPython:

* Directamente desde [www.python.org][www.python.org]
* Desde una distribución de confianza como [www.continuum.io][www.continuum.io], [www.enthought.com][www.enthought.com] o [www.activestate.com][www.activestate.com]
* Creación a partir del origen.

A menos que tenga una necesidad específica, recomendamos las dos primeras opciones.

## <a name="sdk-installation-on-windows-linux-and-macos-client-libraries-only"></a>Instalación del SDK en Windows, Linux y MacOS (solo bibliotecas de cliente)
Si ya tiene instalado Python, puede usar pip para instalar una agrupación de todas las bibliotecas de cliente en su entorno de Python 2.7 o Python 3.3+ existente. De este modo, se descargan los paquetes del repositorio [índice de paquetes de Python, PyPI][Python Package Index].

Puede que necesite derechos de administrador:

* Linux y MacOS, utilice el comando de `sudo`: `sudo pip install azure-mgmt-compute`.
* Windows: abra un símbolo del sistema/PowerShell como administrador

Puede instalar individualmente cada biblioteca para cada servicio de Azure:

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

Los paquetes de versión preliminar pueden instalarse con el indicador `--pre` :

```console
   $ pip install --pre azure-mgmt-compute # installs only the latest Compute Management library
```

También puede instalar un conjunto de bibliotecas de Azure en una sola línea con el metapaquete `azure` . Puesto que no todos los paquetes de este metapaquete se publican como estables todavía, el metapaquete `azure` sigue estando en versión preliminar.
Sin embargo, los paquetes principales, desde las perspectivas de calidad e integridad de código se pueden considerar "estables" en este momento

* se etiquetan oficialmente como tal en sincronización con otros lenguajes tan pronto como sea posible.
  No estamos planeando cambios más importantes realizados hasta ese momento.

Puesto que es una versión preliminar, tiene que usar el indicador `--pre` :

```console
   $ pip install --pre azure
```

o directamente

```console
   $ pip install azure==2.0.0rc6
```

## <a name="getting-more-packages"></a>Instalación de otros paquetes
El repositorio [Python Package Index][Python Package Index] (PyPI) dispone de una abundante selección de bibliotecas de Python.  Si ha optado por instalar una distribución, ya dispondrá de los bits más interesantes para varios escenarios que van desde el desarrollo web a la informática técnica.

## <a name="python-tools-for-visual-studio"></a>Python Tools para Visual Studio
[Python Tools para Visual Studio][Python Tools para Visual Studio] (PTVS) es un complemento gratuito o software de código abierto (OSS) de Microsoft que convierte VS en un IDE de Python completo:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

El uso de PTVS es opcional, pero es recomendable, ya que le proporciona compatibilidad con soluciones o proyectos de Web y Python, depuración, creación de perfiles, ventana interactiva, edición de plantillas e IntelliSense.

PTVS también simplifica la implementación en Microsoft Azure, con compatibilidad para la implementación en [Cloud Services](cloud-services/cloud-services-python-ptvs.md) y [Websites](app-service/app-service-web-overview.md).

PTVS funciona con su instalación de Visual Studio 2013, 2015 o 2017 existente.  Para obtener documentación, descargas y discusiones, consulte [Python Tools para Visual Studio].  

## <a name="python-azure-scenarios-for-linux-and-macos"></a>Escenarios de Python Azure para Linux y MacOS
Para Linux o Mac OS, los escenarios principales de Azure que se admiten:

1. Consumo de servicios de Azure mediante bibliotecas de clientes para Python
2. Ejecución de la aplicación en la VM de Linux
3. Desarrollar y publicar en sitios web de Azure mediante Git

El primer escenario le permite crear aplicaciones web enriquecidas que aprovechan las funcionalidades de PaaS de Azure como [Blob Storage](virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [Queue Storage](storage/queues/storage-python-how-to-use-queue-storage.md), [Table Storage](cosmos-db/table-storage-how-to-use-python.md), etc., a través de contenedores de Python para la API de REST de Azure. Estos funcionan de forma idéntica en Windows, Mac y Linux.  También puede usar estas bibliotecas de cliente desde su equipo de desarrollo local o en una máquina virtual de Linux que se ejecute en Azure.

En el escenario de VM, simplemente inicie la VM de Linux que elija (Ubuntu, CentOS y Suse) y ejecute o administre lo que desee.  Por ejemplo, puede ejecutar el bloc de notas o REPL de [IPython][IPython] en la máquina de Windows/Mac/Linux y configurar el explorador para que apunte a una máquina virtual multiproceso de Linux o Windows que ejecute el motor de IPython en Azure.

Para obtener información sobre cómo configurar una máquina virtual Linux, consulte el tutorial [Creación de una máquina virtual que ejecuta Linux](virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Con la implementación de Git puede desarrollar una aplicación web de Python y publicarla en un sitio web de Azure desde cualquier sistema operativo.  Cuando inserta el repositorio en Azure, crea automáticamente un entorno virtual y pip instala los paquetes necesarios.

Para información sobre cómo usar cualquier marco de trabajo compatible con WSGI, consulte [Configuración de Python con Azure Websites](app-service/web-sites-python-configure.md).

## <a name="additional-software-and-resources"></a>Recursos y software adicionales:
* [SDK de Azure para Python ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [Azure SDK para Python GitHub](https://github.com/Azure/azure-sdk-for-python)
* [Ejemplos oficiales de Azure para Python](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Distribución de Python de Continuum Analytics][Continuum Analytics Python Distribution]
* [Distribución de Python de Enthought][Enthought Python Distribution]
* [Distribución de Python de ActiveState][ActiveState Python Distribution]
* [SciPy: un conjunto de bibliotecas de Scientific Python][SciPy - A suite of Scientific Python libraries]
* [NumPy: biblioteca de tipos numéricos para Python][NumPy - A numerics library for Python]
* [Django Project: un CMS o marco de trabajo para web maduro][Django Project - A mature web framework/CMS]
* [IPython: un bloc de notas o REPL avanzado para Python][IPython - an advanced REPL/Notebook for Python]
* [Herramientas de Python para Visual Studio en GitHub][Python Tools for Visual Studio on GitHub]
* [Centro para desarrolladores de Python](/develop/python/)

[Continuum Analytics Python Distribution]: http://continuum.io
[Enthought Python Distribution]: http://www.enthought.com
[ActiveState Python Distribution]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - A suite of Scientific Python libraries]: http://www.scipy.org
[NumPy - A numerics library for Python]: http://www.numpy.org
[Django Project - A mature web framework/CMS]: http://www.djangoproject.com
[IPython - an advanced REPL/Notebook for Python]: http://ipython.org
[IPython]: http://ipython.org
[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio on GitHub]: https://github.com/microsoft/ptvs
[Python Package Index]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[blob storage]:storage/blobs/storage-python-how-to-use-blob-storage.md
