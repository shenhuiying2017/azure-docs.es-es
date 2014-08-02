<properties linkid="develop-python-install-python" urlDisplayName="Install Python" pageTitle="Install Python and the SDK - Azure" metaKeywords="Azure Python SDK" description="Learn how to install Python and the SDK to use with Azure." metaCanonical="" services="" documentationCenter="Python" title="Installing Python and the SDK" authors="" solutions="" manager="" editor="" />

Instalación de Python y el SDK
==============================

Python es muy fácil de configurar en Windows y viene preinstalado en Mac y Linux. En esta guía se realizará un recorrido por la instalación y preparación de la máquina para su uso con Azure. Esta guía le ayudará con lo siguiente:

-   ¿Qué es el SDK de Azure para Python?
-   Qué es Python y qué versión usar
-   Instalación en Windows
-   Instalación en Mac y Linux

¿Qué es el SDK de Azure para Python?
------------------------------------

El SDK de Azure para Python incluye componentes que le permiten desarrollar, implementar y administrar aplicaciones de Python para Azure. Específicamente, el SDK de Azure para Python incluye lo siguiente:

-   **Las bibliotecas de clientes Python para Azure**. Estas bibliotecas de clases proporcionan una interfaz para tener acceso a características de Azure, como los servicios de administración de datos y los servicios en la nube.
-   **Las herramientas de línea de comandos de Azure para Mac y Linux**. Este es un conjunto de herramientas de línea de comandos que sirven para implementar y administrar servicios de Azure, como Sitios web Azure y Máquinas virtuales de Azure. Estas herramientas funcionan en cualquier plataforma, incluidas las plataformas Mac, Linux y Windows.
-   **PowerShell para Azure (solo Windows)**. Este es un conjunto de cmdlets de PowerShell para implementar y administrar servicios de Azure, como Servicios en la nube y Máquinas virtuales.
-   **Los emuladores de Azure (solo Windows)**. Los emuladores de proceso y almacenamiento son emuladores locales de los servicios en la nube y los servicios de administración de datos que le permiten probar localmente una aplicación. Los emuladores de Azure solo se ejecutan en Windows.

Los escenarios principales para esta versión son los siguientes:

-   **Windows**: Servicio en la nube, por ejemplo, un sitio de Django con elementos Webroles.
-   **Mac/Linux**: IaaS; ejecución de lo que desee en una VM; consumo de servicios de Azure a través de Python.

Qué es Python y qué versión usar
--------------------------------

Existen varios modelos de intérpretes Python disponibles, entre los ejemplos se incluyen los siguientes:

-   CPython: El intérprete Python estándar y que se usa con más frecuencia.
-   IronPython: El intérprete Python que se ejecuta en .Net/CLR.
-   Jython: El intérprete Python que se ejecuta en JVM.

Para los propósitos de esta versión, solo se prueba y admite **CPython**. Recomendamos como mínimo la versión 2.7. Se agregará compatibilidad con **IronPython** en un futuro próximo.

¿Dónde obtener Python?
----------------------

Existen diferentes formas de obtener CPython:

-   Directamente desde www.python.org.
-   Desde una distribución con reputación como www.enthought.com o www.ActiveState.com.
-   Creación a partir del origen.

A menos que tenga una necesidad específica, recomendamos las dos primeras opciones tal y como se describe a continuación.

Instalación en Windows
----------------------

Para Windows, puede usar el [instalador de WebPI](http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409) proporcionado del Centro para desarrolladores para Python principal para mejorar la instalación (tomará CPython de www.python.org).

**Nota:** en Windows Server, para descargar el instalador de WebPI es posible que tenga que configurar los ajustes ESC de IE (Inicio/Herramientas administrativas/Administrador del servidor, hacer clic en **Configurar ESC de Internet Explorer** y establecerlo en Desactivado).

![how-to-install-python-webpi-1](./media/python-how-to-install/how-to-install-python-webpi-1.png)

El instalador de WebPI proporciona todo lo que necesita a las aplicaciones de Azure para Python, además de soporte específico para las aplicaciones de Django:

![how-to-install-Python-webpi-2](./media/python-how-to-install/how-to-install-python-webpi-2.png)

Una vez finalizado el proceso, debe ver esta pantalla de configuración de las opciones de instalación:

![how-to-install-python-webpi-3](./media/python-how-to-install/how-to-install-python-webpi-3.png)

Una vez que se haya completado la instalación, escriba "python" cuando se le solicite para asegurarse de que el proceso se ha realizado sin interrupciones. Según la forma en la que haya realizado la instalación, es posible que tenga que establecer la variable "path" para buscar la versión correcta de Python:

![how-to-install-python-win-run](./media/python-how-to-install/how-to-install-python-win-run.png)

Aunque esta versión se centra principalmente en las aplicaciones web con Django, puede examinar el [índice de paquetes de Python (PyPI)](http://pypi.python.org/pypi) (en inglés) para ver una abundante selección de otras soluciones de software. Si ha optado por instalar una distribución, ya dispondrá de los bits más interesantes para diversos escenarios que van desde el desarrollo web a la informática técnica.

Para ver qué paquetes de Python se han instalado en **site-packages**, especifique lo siguiente para buscar la ubicación:

![how-to-install-python-win-site](./media/python-how-to-install/how-to-install-python-win-site.png)

Esto le ofrecerá una lista de lo que se ha instalado en el sistema.

Una vez completada la instalación, debe disponer de las bibliotecas de clientes, Django y Python disponibles en la ubicación predeterminada:

     C:\Python27\Lib\site-packages\windowsazure
        C:\Python27\Lib\site-packages\django

### Python Tools para Visual Studio

Python Tools para Visual Studio es un complemento gratuito de OSS de Microsoft que convierte VS en un IDE de Python completo:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

El uso de Python Tools para Visual Studio es opcional, pero es recomendable, ya que le proporciona compatibilidad con soluciones o proyectos de Django y Python, depuración, creación de perfiles, edición de plantillas e IntelliSense, implementación en la nube, etc. Este complemento funciona con su instalación de VS2010 existente. Si no dispone de VS2010, WebPI instalará el shell gratuito integrado + PTVS que le proporciona fundamentalmente un IDE de "VS Python Express" **completamente gratuito**. Para obtener más información, consulte [Python Tools for Visual Studio on CodePlex](http://pytools.codeplex.com).

Nota: cuando el complemento de PTVS es pequeño, el shell integrado aumentará los tiempos de descarga. La versión del shell integrado tampoco es actualmente compatible con la característica de incorporación del proyecto de implementación de Azure.

Desinstalación en Windows
-------------------------

El producto WebPI del **SDK de Azure para Python de junio de 2012** no es una aplicación típica, sino una recopilación de distintos productos como Python 2.7 de 32 bits, las API de cliente de Azure para Python, Django, etc. que se agrupan en un solo conjunto. Una consecuencia de esto es que no existe un desinstalador convencional propio, por lo que tendrá que quitar los programas que instale individualmente desde el Panel de control de Windows.

Si desea volver a instalar el **SDK de Azure para Python**, simplemente abra un símbolo del sistema de PowerShell como administrador y ejecute el siguiente comando:

    rm -force "HKLM:\SOFTWARE\Microsoft\SDK de Azure para Python de junio de 2012"

A continuación, vuelva a ejecuta WebPI.

Instalación en Linux y MacOS
----------------------------

Lo más probable es que Python ya esté instalado en su equipo de desarrollo. Puede comprobarlo especificando:

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

Aquí puede ver que esta VM de Azure Suse cuenta con CPython 2.7.2 instalado, que resulta útil para ejecutar los tutoriales de Azure y los ejemplos de Django. Si necesita actualizar, siga las instrucciones recomendadas de actualización de paquetes del SO. Sin embargo, tenga en cuenta que en general es mejor dejar el sistema Python solo (otros pueden depender de esa versión) e instalar la versión más reciente mediante [Virtualenv](http://pypi.python.org/pypi/virtualenv).

Para instalar las bibliotecas de clientes de Azure para Python, use **pip** para realizar la selección desde **PyPI**:

    curl https://raw.github.com/pypa/pip/master/contrib/get-pip.py | sudo python

El comando anterior le solicitará de manera silenciosa la contraseña raíz. Escríbala y presione Entrar. A continuación:

    sudo /usr/local/bin/pip-2.7 install azure

Ahora debe ver las bibliotecas de clientes instaladas en **site-packages**. En MacOS:

![MacOS site-packages](./media/python-how-to-install/how-to-install-python-mac-site.png)

Cuando se realiza el desarrollo desde Mac/Linux, existen dos escenarios principales compatibles para esta versión:

1.  Consumo de servicios de Azure mediante bibliotecas de clientes para Python

2.  Ejecución de la aplicación en la VM de Linux

El primer escenario le permite crear aplicaciones web enriquecidas que aprovechan las capacidades de PaaS de Azure como colas, almacenamiento de blobs, etc. a través de contenedores de Python para la API de REST de Azure. El funcionamiento es idéntico en Windows, Mac y Linux. Consulte los tutoriales y las guías de procedimientos para ver ejemplos. También puede usar estas bibliotecas de clientes desde dentro de una VM de Linux.

En el escenario de VM, simplemente inicie la VM de Linux que elija (Ubuntu, CentOS y Suse) y ejecute o administre lo que desee. Por ejemplo, puede ejecutar el bloc de notas o el REPL de [IPython](http://ipython.org) en la máquina de Windows/Mac/Linux y configurar el explorador para que apunte a una VM multiproceso de Linux o Windows que ejecute el motor de IPython en Azure. Para obtener más información sobre la instalación de IPython, consulte el tutorial.

Para obtener información sobre cómo configurar una VM de Linux, consulte la sección de [administración de Linux](/en-us/manage/linux/).

Recursos y software adicionales:
--------------------------------

-   [Enthought Python Distribution](http://www.enthought.com)
-   [ActiveState Python Distribution](http://www.activestate.com)
-   [SciPy - A suite of Scientific Python libraries](http://www.scipy.org)
-   [NumPy - A numerics library for Python](http://www.numpy.org)
-   [Django Project - A mature web framework/CMS](http://www.djangoproject.com)
-   [IPython - an advanced REPL/Notebook for Python](http://ipython.org)
-   [Bloc de notas de IPython en Azure](http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-ipython-notebook)
-   [Python Tools for Visual Studio on CodePlex](http://pytools.codeplex.com)
-   [Virtualenv](http://pypi.python.org/pypi/virtualenv)

