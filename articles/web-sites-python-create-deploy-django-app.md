<properties linkid="develop-python-web-site-with-django" urlDisplayName="Websites with Django" pageTitle="Python Websites with Django - Azure tutorial" metaKeywords="Azure django, django website" description="A tutorial that introduces you to running a Python website on Azure." metaCanonical="" services="web-sites" documentationCenter="Python" title="Creating Websites with Django" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Creación de sitios web con Django

En este tutorial se describe cómo empezar a ejecutar Python en los sitios web Azure. Estos sitios ofrecen hospedaje gratuito limitado y una implementación rápida. Además, ahora también permiten usar Python. A medida que su aplicación crece, puede cambiar a un tipo de hospedaje de pago e integrar el resto de los servicios de Azure.

En este tutorial se muestra cómo implementar una aplicación compilada con el marco de trabajo web Django y se analizan los distintos pasos para implementar la aplicación y las bibliotecas necesarias, incluidas las de Django. Todos estos elementos se incluirán en un repositorio Git, lo cual facilitará y agilizará la inserción de actualizaciones en el sitio web. Por último, el sitio recién creado se configurará mediante Azure para poder ejecutar la aplicación Python.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

En este tutorial se usan Python 2.7 y Django 1.4. Puede obtener estas aplicaciones por su cuenta o bien instalarlas de forma rápida y sencilla a través del vínculo del instalador de Windows en [][]<http://www.windowsazure.com/es-es/develop/python/></a>.

**Nota**: Ahora los sitios web de Azure tienen preinstalados Python (2.7.3 o 3.4.0, según desee) y el controlador wfastcgi. Sin embargo, los marcos de trabajo web, como Django, no se incluyen. Aún así, si lo prefiere, puede usar otro intérprete de Python. Solo tendrá que incluirlo en el repositorio Git y configurar el sitio web para usar dicho intérprete en lugar del de Python 2.7, ya instalado.

> [WACOM.NOTE] Ahora puede seleccionar la versión de Python que desee usar en el portal Sitios web de Azure. Para ello, abra la pestaña Configurar de su sitio web y cambie la opción **Versión de Python**.

También deberá instalar una opción de implementación para insertar el sitio en Azure. Hay varias herramientas de implementación disponibles, si bien en este tutorial se usa Git. Se recomienda [msysgit][msysgit].

**Nota**: la publicación TFS no se admite actualmente para los proyectos de Python.

Una vez instalados Python, Django y Git, tendrá todo lo necesario para comenzar.

## Creación de sitios web en el portal

El primer paso para crear la aplicación consiste en crear el sitio web a través del Portal de administración de Azure. Para ello, deberá iniciar sesión en el portal y hacer clic en el botón NEW de la esquina inferior izquierda. Aparecerá una ventana. Haga clic en **Quick Create**, especifique una dirección URL y seleccione **Create Website**.

![][0]

El sitio se configurará rápidamente. A continuación, se agregará compatibilidad para publicar a través de Git. Para ello, elija **Set up deployment from source control**.

![][1]

Desplácese hacia abajo en el cuadro de diálogo **Set up deployment** y seleccione la opción **Local Git**. Haga clic en la flecha derecha para continuar.

![][2]

Una vez configurada la publicación Git, aparecerá brevemente una página donde se le informa de que el repositorio se está creando. Una vez listo, se le llevará a la pestaña Implementaciones, que incluye instrucciones sobre cómo conectarse.

![][3]

## Desarrollo de sitios web

Ahora que ya se ha creado el repositorio Git en Azure, deberá empezar a rellenarlo con los datos del sitio web desde la máquina local. El primer paso consiste en clonar el sitio vacío existente con la dirección URL proporcionada:

![][4]

A partir de aquí, estará listo para configurar la incorporación del sitio web. Deberá realizar algunas acciones:

1.  Incluir la biblioteca de Django y otras bibliotecas que se usarán para ejecutar el sitio web.
2.  Incluir el código de aplicación de Django.

En primer lugar, incluya la biblioteca de Django. Para ello, cree un nuevo directorio denominado "site-packages" y copie en este la versión de Django instalada mediante los siguientes comandos:

    mkdir site-packages
    cd site-packages
    xcopy /s C:\Python27\lib\site-packages\* .

Estos comandos copian todas las bibliotecas ubicadas en "site-packages", incluida la de Django. Si existen bibliotecas que el sitio web no utiliza, puede eliminarlas sin problema.

![][5]

A continuación, cree la aplicación inicial de Django. Esta puede crearse de la misma forma que se haría con cualquier otra aplicación de Django desde la línea de comandos, o bien puede usar las [herramientas de Python para Visual Studio][herramientas de Python para Visual Studio] (en inglés) para crear el proyecto. Ambas opciones se muestran a continuación.

**Opción 1:**
Para crear el nuevo proyecto a partir de la línea de comandos, ejecute el comando siguiente. que crea la aplicación de Django en la carpeta DjangoApplication:

     C:\Python27\python.exe -m django.bin.django-admin startproject DjangoApplication

![][6]

**Opción 2:**
También puede crear el nuevo sitio mediante las herramientas de Python para Visual Studio. Inicie Visual Studio con las herramientas de Python para Visual Studio instaladas y seleccione **Archivo**-\>**Nuevo proyecto**. En **Otros lenguajes**, desplácese a los proyectos de Python y seleccione **Django Application**. Especifique **DjangoApplication** como nombre del proyecto y asegúrese de que la opción **Crear directorio para la solución** esté desactivada, a fin de obtener la misma estructura de directorios que al crear una aplicación de Django desde la línea de comandos. Mediante esta opción se configurarán una solución de Visual Studio y un archivo de proyecto que ofrecen una fantástica experiencia de desarrollo local donde se incluyen la depuración de plantillas e IntelliSense.

![][7]

Ahora solo tiene que agregar el resto de archivos recién agregados e insertar el sitio en Git. Para ello, ejecute estos comandos:

    git add DjangoApplication site-packages
    git commit -m "Initial site"
    git remote add azure https://dinov@pythonwebsite.scm.azurewebsites.net/PythonWebSite.git
    git push azure master

El primer comando agregará los archivos sin seguimiento para que se les aplique seguimiento. El segundo comando confirmará los archivos que se acaban de agregar al repositorio. El tercer comando agrega un elemento remoto denominado *azure* para el repositorio. El último comando toma los cambios y los inserta en el repositorio remoto. Además, también iniciará la implementación. Una vez realizado este proceso, debe aparecer un resultado similar a este:

![][8]

Tras realizar la inserción, el portal de Azure se actualiza y muestra la implementación activa:

![][9]

## Configuración del sitio web

Ahora debe configurarse el sitio web para informarle sobre el proyecto de Django y usar el controlador wfastcgi. Para ello, puede hacer clic en la pestaña Configure de la parte superior de la pantalla y, después, desplazarse a la mitad inferior de la página, que contiene las secciones de **configuración de aplicación** y **asignaciones de controlador**.

Todos los valores aquí configurados se convertirán en variables de entorno durante la solicitud en sí. Esto significa que se puede usar para configurar la variable de entorno DJANGO\_SETTINGS\_MODULE, además de PYTHONPATH y WSGI\_HANDLER. Si la aplicación tiene otros valores de configuración, podrían asignarse en este punto y seleccionarlos fuera del entorno. En algunos casos, se deseará establecer una ruta de acceso a un archivo del sitio web, por ejemplo para PYTHONPATH. Al ejecutarse como sitio web de Azure, el sitio web residirá en **D:\\home\\site\\wwwroot\**, por lo que podrá usarse en cualquier ubicación donde se requiera una ruta de acceso completa a un archivo del disco.

Para configurar una aplicación de Django, deben crearse tres variables de entorno. La primera es DJANGO\_SETTINGS\_MODULE, que proporciona el nombre del módulo de la aplicación de Django que se usará para la configuración. La segunda es la variable de entorno PYTHONPATH, que especifica el paquete donde reside el módulo de configuración. La tercera es WSGI\_HANDLER. Esta variable es un nombre de módulo/paquete, seguido del atributo del módulo que se va a usar (por ejemplo, mipaquete.mimódulo.handler). Agregue paréntesis para indicar que debe llamarse al atributo. Así, para configurar estas variables, use los siguientes valores:

    DJANGO_SETTINGS_MODULE    DjangoApplication.settings
    PYTHONPATH                D:\home\site\wwwroot\DjangoApplication;D:\home\site\wwwroot\site-packages
    WSGI_HANDLER              django.core.handlers.wsgi.WSGIHandler()

![][10]

Ahora deberá configurar la asignación de controlador. Para ello, registre el controlador para todas las extensiones usando las rutas de acceso al intérprete de Python y al script wfastcgi.py:

    EXTENSION                 *
    SCRIPT PROCESSOR PATH     D:\python27\python.exe
    ADDITIONAL ARGUMENTS      D:\python27\scripts\wfastcgi.py

![][11]

En este punto, puede hacer clic en el botón **Save** de la parte inferior.

Por último, vuelva al panel. Desplácese hasta la opción **SITE URL** que aparece a la izquierda del mismo. Haga clic en el vínculo y abra el nuevo sitio de Django, que tendrá este aspecto:

![][12]

## Pasos siguientes

A partir de aquí, puede continuar el desarrollo de la aplicación de Django con las herramientas que ya utiliza. Si usa las [herramientas de Python para Visual Studio][herramientas de Python para Visual Studio] (en inglés) para desarrollar, probablemente deseará instalar [VisualGit][VisualGit] para integrar el control de código fuente desde Visual Studio.

La aplicación puede tener otras dependencias, aparte de Python y Django. Si para Python se usó el instalador incluido en [][]<http://www.windowsazure.com/es-es/develop/python/></a>, PIP ya estará instalado y podrá utilizarse para agregar nuevas dependencias de forma rápida. Por ejemplo, para instalar el kit de herramientas de lenguaje natural y todas sus dependencias, escriba:

    pip install nltk

A continuación, deberá actualizar el directorio "site-packages" copiando los archivos de C:\\Python27\\Lib\\site-packages en el directorio "site-packages" local.

Una vez copiados los archivos, ejecute el comando **git status** para ver los archivos recién agregados y **git add** seguido de **git commit** para confirmar los cambios en el repositorio. Por último, puede ejecutar **git push** para implementar el sitio web actualizado en Azure.

Ahora puede ir al directorio DjangoApplication y usar manage.py de la forma habitual para empezar a agregar nuevas aplicaciones al proyecto de Django.

  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [0]: http://www.windowsazure.com/es-es/develop/python/
  [msysgit]: http://code.google.com/p/msysgit/
  [0]: ./media/web-sites-python-create-deploy-django-app/django-ws-003.png
  [1]: ./media/web-sites-python-create-deploy-django-app/django-ws-004.png
  [2]: ./media/web-sites-python-create-deploy-django-app/django-ws-005.png
  [3]: ./media/web-sites-python-create-deploy-django-app/django-ws-006.png
  [4]: ./media/web-sites-python-create-deploy-django-app/django-ws-007.png
  [5]: ./media/web-sites-python-create-deploy-django-app/django-ws-008.png
  [herramientas de Python para Visual Studio]: http://pytools.codeplex.com/
  [6]: ./media/web-sites-python-create-deploy-django-app/django-ws-010.png
  [7]: ./media/web-sites-python-create-deploy-django-app/django-ws-011.png
  [8]: ./media/web-sites-python-create-deploy-django-app/django-ws-013.png
  [9]: ./media/web-sites-python-create-deploy-django-app/django-ws-014.png
  [10]: ./media/web-sites-python-create-deploy-django-app/django-ws-015.png
  [11]: ./media/web-sites-python-create-deploy-django-app/django-ws-016.png
  [12]: ./media/web-sites-python-create-deploy-django-app/django-ws-017.png
  [VisualGit]: http://code.google.com/p/visualgit/
