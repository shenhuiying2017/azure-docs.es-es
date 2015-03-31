<properties 
	pageTitle="Bloc de notas de IPython - Tutorial de Azure" 
	description="Un tutorial que muestra cómo implementar el bloc de notas de IPython en Azure, utilizando máquinas virtuales de Linux o Windows." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="huvalo"/>





# Bloc de notas de IPython en Azure

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>El <a href="http://ipython.org">proyecto IPython</a> proporciona una colección de herramientas para la informática científica que incluye potentes shells interactivos, bibliotecas paralelas de elevado rendimiento y facilidad de uso, así como un entorno basado en web conocido como bloc de notas de IPython. El bloc de notas ofrece un entorno de trabajo para la informática interactiva que combina la ejecución del código con la creación de documentos informáticos activos. Estos archivos del bloc de notas pueden contener texto arbitrario, fórmulas matemáticas, código de entrada, resultados, gráficos, vídeos y cualquier otra clase de contenido multimedia que pueda mostrar cualquier explorador web moderno.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="label">Ver el tutorial</a> <a style="background-image: url('/media/devcenter/python/ipy-youtube2.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir vídeo</span></a> <span class="time">5:22</span></div>
</div>

Tanto si no tiene ninguna experiencia con Python y desea aprender en un entorno entretenido e interactivo, como si desea hacer procesamientos técnicos o paralelos de importancia, el Bloc de notas de IPython es una excelente opción. Para ilustrar sus capacidades, en la captura de pantalla siguiente se muestra el uso del Bloc de notas de IPython en combinación con los paquetes SciPy y matplotlib, para analizar la estructura de una grabación de sonido:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png)

En este documento se muestra cómo implementar el Bloc de notas de IPython en Microsoft Azure, con máquinas virtuales (VM) de Linux o Windows.  Mediante el uso del Bloc de notas de IPython, puede proporcionar fácilmente una interfaz de acceso web a recursos informáticos escalables con toda la potencia de Python y sus muchas bibliotecas.  Puesto que la instalación se realiza en la nube, los usuarios pueden tener acceso a estos recursos sin necesidad de ninguna configuración local más allá de un explorador web moderno.

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Creación y configuración de una máquina virtual en Azure

El primer paso es crear una máquina virtual que se ejecute en Azure. Esta máquina virtual es un sistema operativo completo en la nube y se utilizará para ejecutar el Bloc de notas de IPython. Azure es capaz de ejecutar máquinas virtuales Windows y Linux, y se tratará la instalación de IPython en ambos tipos de máquinas virtuales.

### Máquina virtual de Linux

Siga las instrucciones que se proporcionan [aquí][portal-vm-linux] para crear una máquina virtual de la distribución  *OpenSUSE* o *Ubuntu*. En este tutorial se utiliza OpenSUSE 13.2 y Ubuntu Server 14.04 LTS. Se asumirá que el nombre de usuario predeterminado es  *azureuser*.

### Máquina virtual de Windows

Siga las instrucciones que se proporcionan [aquí][portal-vm-windows] para crear una máquina virtual de la distribución *Windows Server 2012 R2 Datacenter*.  En este tutorial, se asumirá que el nombre de usuario es *azureuser*.

## Creación de un extremo para el bloc de notas de IPython

Este paso se aplica tanto a las máquinas virtuales de Linux como a las de Windows. Más adelante, configuraremos IPython para ejecutar su servidor de bloc de notas en el puerto 9999. Para hacer que este puerto esté disponible de forma pública, se debe crear un extremo en el Portal de administración de Azure. Este extremo abre un puerto en el firewall de Azure y asigna el puerto público (HTTPS, 443) al puerto privado de la máquina virtual (9999).

Para crear un extremo, vaya al panel de la máquina virtual, haga clic en "Extremos" y en "Agregar extremo", y cree un nuevo extremo (llamado `ipython_nb` en este ejemplo). Seleccione TCP como el protocolo, 443 como el puerto público y 9999 como el puerto privado:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png)

Después de este paso, la pestaña "Endpoints" del panel se mostrará de esta forma:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png)

## Instalación del software necesario en la máquina virtual

Para ejecutar el bloc de notas de IPython en nuestra máquina virtual, primero tenemos que instalar IPython y sus dependencias.

### Linux (OpenSUSE)

Para instalar IPython y sus dependencias, conéctese con SSH a la máquina virtual de Linux y lleve a cabo los pasos siguientes.

Instale [NumPy][numpy], [Matplotlib][matplotlib], [Tornado][tornado] (referencias en inglés) y otras dependencias de IPython realizando lo siguiente:

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install python-jinja2
    sudo zypper install ipython

### Linux (Ubuntu)

Para instalar IPython y sus dependencias, conéctese con SSH a la máquina virtual de Linux y lleve a cabo los pasos siguientes.

En primer lugar, recupere las nuevas listas de paquetes:

    sudo apt-get update

Instale [NumPy][numpy], [Matplotlib][matplotlib], [Tornado][tornado] (referencias en inglés) y otras dependencias de IPython realizando lo siguiente:

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

Para instalar IPython y sus dependencias en la máquina virtual de Windows, abra el Escritorio remoto para conectarse a la máquina virtual. A continuación, realice los pasos siguientes, mediante Windows PowerShell para ejecutar todas las acciones de la línea de comandos.

**Nota**: para realizar descargas con Internet Explorer, tendrá que cambiar alguna configuración de seguridad.  En el **Administrador de servidores**, haga clic en **Servidor local**, a continuación en **Configuración de seguridad mejorada de IE** y desactívelo para administradores.  Puede volver a habilitarlo una vez instalado IPython.

1.  Descargue e instale la última versión de 32 bits de [Python 2.7][].  Necesitará agregar `C:\Python27` y `C:\Python27\Scripts` a la `PATH` variable de entorno.

1.  Instale [Tornado][tornado] y [PyZMQ][pyzmq], así como otras dependencias de IPython realizando lo siguiente:

        easy_install tornado
        easy_install pyzmq
        easy_install jinja2
        easy_install six
        easy_install python-dateutil
        easy_install pyparsing

1.  Descargue e instale [NumPy][numpy] mediante el instalador binario `.exe` disponible en su sitio web.  En el momento en que se redacta esto, la versión más reciente es **numpy-1.9.1-win32-superpack-python2.7.exe**.

1.  Instale [Matplotlib][matplotlib] mediante:

        pip install matplotlib==1.4.2

1.  Descargue e instale [OpenSSL][].

	* Encontrará **Visual C++ 2008 Redistributable** (necesario) en la misma página de descarga.

	* Necesitará agregar `C:\OpenSSL-Win32\bin` a su variable de entorno `PATH`.

	> [AZURE.NOTE] Al instalar OpenSSL, use la versión 1.0.1g o superior, ya que incluye una revisión para la vulnerabilidad de seguridad Heartbleed.

1.  Instale IPython mediante el comando:

        pip install ipython==2.4

1.  Abra un puerto en el Firewall de Windows.  En Windows Server 2012, el firewall bloqueará de manera predeterminada las conexiones entrantes.  Para abrir el puerto 9999, siga estos pasos:

    - Inicie **Firewall de Windows con seguridad avanzada** en la pantalla de inicio.

    - Haga clic en **Reglas de entrada** en el panel izquierdo.

	- Haga clic en **Nueva regla...** en el panel Acciones.

	- En el Asistente para nueva regla de entrada, seleccione **Puerto**.

	- En la pantalla siguiente, seleccione **TCP** e introduzca **9999** en **Puertos locales específicos**.

	- Acepte los valores predeterminados, asigne un nombre a la regla y haga clic en Finalizar.

### Configuración del bloc de notas de IPython

A continuación, configuraremos el bloc de notas de IPython. El primer paso es crear un perfil de configuración de IPython personalizado para encapsular la información de configuración:

    ipython profile create nbserver

A continuación, se usa `cd` para cambiar al directorio del perfil y crear nuestro certificado SSL y modificar el archivo de configuración de perfiles.

En Linux:

    cd ~/.ipython/profile_nbserver/

En Windows:

    cd \users\azureuser\.ipython\profile_nbserver

Cree el certificado SSL como sigue (Linux y Windows):

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Tenga en cuenta que, dado que estamos creando un certificado SSL autofirmado, al conectarse al bloc de notas, el explorador le mostrará una advertencia de seguridad.  Para un uso de producción a largo plazo, preferirá utilizar un certificado debidamente firmado asociado a su organización.  Puesto que la administración de certificados está fuera del ámbito de esta demostración, por ahora nos limitaremos a un certificado autofirmado.

Además de utilizar un certificado, también debe proporcionar una contraseña para proteger su bloc de notas del uso no autorizado.  Por motivos de seguridad, IPython usa contraseñas cifradas en su archivo de configuración, por lo que primero deberá cifrar su contraseña.  IPython proporciona una utilidad para hacerlo; en el símbolo del sistema ejecute:

    python -c "import IPython;print IPython.lib.passwd()"

Se le solicitará una contraseña y su confirmación y, a continuación, se imprimirá la contraseña como sigue:

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)
    
A continuación, se modificará el archivo de configuración del perfil, que es el archivo `ipython_notebook_config.py` que está en el directorio del perfil en el que se encuentre. Tenga en cuenta que es posible que este archivo no exista, simplemente créelo.  Este archivo tiene varios campos que, de manera predeterminada, se convierten en comentario.  Puede abrir este archivo con el editor de texto que prefiera y debe asegurarse de que tiene al menos el siguiente contenido:

    c = get_config()
    
    # This starts plotting support always with matplotlib
    c.IPKernelApp.pylab = 'inline'
    
    # You must give the path to the certificate file.
    
    # If using a Linux VM:
    c.NotebookApp.certfile = u'/home/azureuser/.ipython/profile_nbserver/mycert.pem'
    
    # And if using a Windows VM:
    c.NotebookApp.certfile = r'C:\Users\azureuser\.ipython\profile_nbserver\mycert.pem'
    
    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '
    
    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### Ejecución del bloc de notas de IPython

En este momento, estamos preparados para iniciar el bloc de notas de IPython. Para ello, navegue hasta el directorio en el que desee almacenar los blocs de notas e inicie el servidor del Bloc de notas de IPython:

    ipython notebook --profile=nbserver

Ahora debería poder obtener acceso al bloc de notas de IPython en la dirección
`https://[Nombre elegido].cloudapp.net`.

Cuando obtenga acceso por primera vez al bloc de notas, la página de inicio de sesión le solicitará la contraseña:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png)

Y cuando inicie sesión, verá el "Panel del Bloc de notas de IPython", que es el centro de control para todas las operaciones del bloc de notas.  Desde esta página puede crear nuevos blocs de notas, abrir los existentes, etc.:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png)

Si hace clic en el botón "Nuevo bloc de notas", verá una página de inicio como la siguiente:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png)

El área marcada con el aviso `In []:` es el área de entrada; ahí podrá escribir cualquier código Python válido, que se ejecutará cuando presione `Shift-Enter` o haga clic en el icono "Reproducir" (el triángulo orientado hacia la derecha de la barra de herramientas).

Como hemos configurado el bloc de notas para iniciarse con compatibilidad con NumPy y matplotlib de forma automática, es posible incluso producir figuras, por ejemplo:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png)

## Los documentos informáticos activos con medios enriquecidos son un potente paradigma.

El bloc de notas debería resultar muy familiar para quienes hayan utilizado Python y un procesador de textos, porque en cierto modo es una mezcla de ambos: puede ejecutar bloques de código de Python, pero también puede mantener notas y cualquier otro texto si cambia el estilo de una celda de "Code" a "Markdown" mediante el menú desplegable en la barra de herramientas

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png)


Pero es mucho más que un procesador de textos, ya que el Bloc de notas de IPython permite la mezcla de procesamiento y medios enriquecidos (texto, gráficos, vídeo y prácticamente cualquier cosa que un explorador web moderno pueda mostrar). Por ejemplo, puede mezclar vídeos explicativos con informática con fines formativos:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png)

o incrustar sitios web externos que permanezcan activos y utilizables, dentro de un archivo de bloc de notas:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png)

Y con la potencia de la gran cantidad de excelentes bibliotecas de Python para informática científica y técnica, puede realizar un cálculo simple con la misma facilidad que un análisis de red complejo, todo ello en un entorno:

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png)

Este paradigma que combina la potencia de la web moderna con informática activa ofrece muchas posibilidades y es ideal para la nube. el Bloc de notas puede usarse.

* como un bloc de notas informático para registrar el trabajo de exploración sobre un problema,

* para compartir resultados con compañeros, ya sea de forma computacional 'live' o en formatos impresos (HTML, PDF)

* para distribuir y presentar materiales de formación activos sobre informática. De este modo, los alumnos podrán realizar pruebas de con el código real de inmediato, modificarlo y volver a ejecutarlo de forma interactiva

* para proporcionar "documentos ejecutables" que presentan los resultados de la investigación de tal manera que permita la reproducción, la validación y la ampliación por parte de otros usuarios,

* como plataforma de informática de colaboración: varios usuarios pueden iniciar sesión en el mismo servidor de bloc de notas para compartir una sesión activa de cálculo,

* y mucho más...

Si va al [repositorio][] del código fuente de IPython , encontrará todo un directorio de ejemplos de bloc de notas que puede descargar y con el que puede experimentar en su propia máquina virtual de IPython de Azure.  Solo tiene que descargar los archivos  `.ipynb` desde el sitio y cargarlos en el panel de la máquina virtual del bloc de notas de Azure (o descargarlos directamente en la máquina virtual).

## Conclusión

El Bloc de notas de IPython ofrece una potente interfaz para tener acceso de forma interactiva a la potencia del ecosistema Python en Azure.  Abarca una amplia gama de casos de uso, entre los que se incluyen la exploración simple y el aprendizaje de Python, el análisis y la visualización de datos, la simulación y el procesamiento paralelo. Los documentos del Bloc de notas resultantes contienen un registro completo de los procesamientos que se han realizado y pueden compartirse con otros usuarios de IPython.  Puede utilizar el Bloc de notas de IPython como una aplicación local, pero está especialmente pensado para las implementaciones en la nube de Azure

Las características principales de IPython también están disponibles en Visual Studio mediante las [Herramientas de Python para Visual Studio][] (PTVS). Las PTVS son un complemento de código abierto y libre de Microsoft que convierten Visual Studio en un entorno de desarrollo avanzado de Python que incluye un editor avanzado con IntelliSense, depuración generación de perfiles e integración de procesamiento paralelo.



[tornado]:      http://www.tornadoweb.org/          "Tornado"
[PyZMQ]:        https://github.com/zeromq/pyzmq     "PyZMQ"
[NumPy]:        http://www.numpy.org/               "NumPy"
[Matplotlib]:   http://matplotlib.sourceforge.net/  "Matplotlib"

[portal-vm-windows]: /manage/windows/tutorials/virtual-machine-from-gallery/
[portal-vm-linux]: /manage/linux/tutorials/virtual-machine-from-gallery/

[repositorio]: https://github.com/ipython/ipython
[Herramientas de Python para Visual Studio]: http://aka.ms/ptvs

[Python 2.7]: http://www.python.org/download
[OpenSSL]: http://slproweb.com/products/Win32OpenSSL.html

<!--HONumber=47-->
