<properties urlDisplayName="Django with Visual Studio" pageTitle="Django con Visual Studio (Python): tutorial de Azure" metaKeywords="Azure Django web app, Azure Django virtual machine" description="Un tutorial que le ense&ntilde;a a crear una aplicaci&oacute;n web de Django hospedada en una m&aacute;quina virtual de Azure." metaCanonical="" services="cloud-services" documentationCenter="Python" title="Creaci&oacute;n de aplicaciones Django con Python Tools para Visual Studio 1.5" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Creación de aplicaciones Django con Python Tools para Visual Studio 1.5

**Nota:** este tutorial también está encapsulado en un [vídeo de Youtube][vídeo de Youtube].

**Nota:** hay disponible un [tutorial más reciente y más extenso][tutorial más reciente y más extenso] sobre PTVS 2.0 Beta.

El desarrollo en Azure es sencillo cuando se usan las herramientas disponibles.
En este tutorial se asume que no tiene ninguna experiencia previa con Azure.
Al término de esta guía, tendrá una aplicación Django funcionando en la nube.

Aprenderá a:

-   Creación de una aplicación Django básica
-   Ejecución y depuración de la aplicación Django localmente con el servidor de prueba Django
-   Ejecución de la aplicación Django localmente en el emulador de proceso
-   Publicación y republicación de la aplicación en Azure

Mediante este tutorial, se compilará una aplicación web Hola mundo
sencilla. La aplicación se hospedará en una instancia de un rol web
que, cuando se ejecuta en Azure, se hospeda en una
máquina virtual dedicada (VM).

A continuación se muestra una captura de pantalla de la aplicación completada:

![][0]

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <span id="setup"></span> </a>Configuración del entorno de desarrollo

Antes de comenzar a desarrollar la aplicación de Azure, debe obtener las herramientas y configurar el entorno de desarrollo. Para obtener información sobre la obtención e instalación del SDK de Azure para Python, consulte [Instalación de Python][Instalación de Python].

**Nota:** este tutorial requiere Python 2.7 y Django 1.4. Estas versiones se encuentran incluidas en el SDK de Azure actual para Python.

**Nota:** el desarrollo en el emulador de proceso o Azure requiere la versión completa de Visual Studio (el shell integrado no es compatible).

## Creación de una nueva aplicación Django

Para crear una nueva aplicación Django, en primer lugar, inicie Visual Studio y, a continuación, cree un nuevo proyecto con **File-\>New Project**. Busque en la pestaña Python (disponible en la parte superior o en el área Other Languages) y seleccione la plantilla de aplicación Django.

![Nuevas plantilla de proyectos de Python][Nuevas plantilla de proyectos de Python]

Haga clic en **OK** y habrá creado la primera aplicación de Django.

![Visual Studio abrió el primer proyecto de Django][Visual Studio abrió el primer proyecto de Django]

A continuación, querrá iniciar el desarrollo de la primera aplicación de Django. Puede hacer clic con el botón secundario en el nodo del módulo y seleccionar **Add New Django app**... para configurar una nueva aplicación en su proyecto:

![Incorporación de un elemento de menú de nueva aplicación][Incorporación de un elemento de menú de nueva aplicación]

Después de esto, puede especificar un nuevo nombre para la aplicación:

![Incorporación de un aviso de nombre de nueva aplicación][Incorporación de un aviso de nombre de nueva aplicación]

Después de especificar un nombre para la aplicación y hacer clic en **OK**, se agrega una nueva aplicación al proyecto:

![Explorador de soluciones con una nueva aplicación agregada][Explorador de soluciones con una nueva aplicación agregada]

Ahora, actualice **settings.py** de manera que se registre la aplicación. Esto provocará que Django detecte automáticamente los archivos de plantilla que se agregan al directorio Templates de las aplicaciones. Agregue el nombre de aplicación a la sección INSTALLED\_APPS:

    'DjangoApplication.MyFirstApp',

![Incorporación de la aplicación a settings.py en INSTALLED\_APPS][Incorporación de la aplicación a settings.py en INSTALLED\_APPS]

A continuación, vamos a agregar código a **views.py** de la aplicación de manera que pueda devolverse un archivo de plantilla simple:

    from django.http import HttpResponse
    from django.template.loader import render_to_string
    def home(request):
        return HttpResponse(render_to_string(
                                            'index.html',
                                            {'content': 'Hello World'}
                                            ))

![Incorporación de la aplicación a settings.py en INSTALLED\_APPS][1]

A continuación, se va a agregar un archivo de plantilla simple que se representará cuando consulte esta vista. Para ello, podemos hacer clic con el botón secundario en la carpeta Templates y seleccionar **"Add new Item"**:

![Incorporación de un nuevo elemento a la carpeta de plantillas][Incorporación de un nuevo elemento a la carpeta de plantillas]

Ahora puede seleccionar "Django HTML Template" desde la lista de plantillas y especificar **index.html** como nombre de archivo:

![Incorporación de un nuevo elemento a la carpeta de plantillas][2]

Después, la plantilla se agrega al proyecto y se abre. Puede observar aquí el inicio del resaltado de la sintaxis para las etiquetas de plantillas:

![Plantilla agregada al Explorador de soluciones][Plantilla agregada al Explorador de soluciones]

Podría continuar y comenzar a actualizar la plantilla en este momento para cambiar el HTML representado y obtendrá un IntelliSense correcto si realiza los siguientes procedimientos:

![Intellisense de plantilla para filtros de Django][Intellisense de plantilla para filtros de Django]

Puede dejar activadas o desactivadas las mayúsculas, los resultados de este tutorial no cambiarán de una forma u otra. Finalmente, basta con registrar la vista con los patrones de la dirección URL en **urls.py**. Agregue esto a **urlpatterns**:

    url(r'^$', 'DjangoApplication.MyFirstApp.views.home', name='home'),

![Registro de la dirección URL][Registro de la dirección URL]

## Ejecución de la aplicación localmente en el servidor de prueba

Llegados a este punto, ya tiene la primera aplicación de Django. Ahora puede ejecutarla localmente **presionando F5**.

![Django Hello World en un explorador y el servidor de prueba][Django Hello World en un explorador y el servidor de prueba]

De esta forma, se iniciará el intérprete de Python que ejecuta **manage.py** de Django para ejecutar el servidor de prueba. Una vez que se inicie correctamente el servidor de prueba, también se iniciará un explorador web para ver el sitio web. Ya que inició con F5, se iniciará bajo el depurador para permitirnos establecer los puntos de interrupción en cualquier código de Python, como el código de visualización en los propios archivos de plantillas:

![El depurador se detuvo en el punto de interrupción de la plantilla][El depurador se detuvo en el punto de interrupción de la plantilla]

Ahora puede **hacer clic en el botón de detención** y pasar a la ejecución en el emulador de proceso de Azure.

## Ejecución de su aplicación localmente en el emulador

Para realizar la ejecución dentro del emulador de proceso, simplemente tiene que agregar el proyecto de implementación de Azure en la solución para el proyecto de Django.

**Nota:** el desarrollo en el emulador de proceso o Azure requiere la versión completa de Visual Studio (el shell integrado no es compatible).

Esto puede hacerse haciendo clic con el botón secundario en el nodo del proyecto de Django en el Explorador de soluciones y seleccionando **"Add Azure Cloud Service Project"**:

![Incorporación del proyecto de desarrollo][Incorporación del proyecto de desarrollo]

Una vez que se ejecute este comando, verá un proyecto recién agregado en el explorador de soluciones:

![Después de la incorporación del proyecto de desarrollo][Después de la incorporación del proyecto de desarrollo]

Este nuevo proyecto está ahora marcado también como proyecto de inicio de la solución. En este momento, tendrá que **reiniciar Visual Studio como administrador** para poder ejecutar el emulador de proceso, pero después de hacerlo, podemos simplemente **presionar F5** y la aplicación se ejecutará e implementará en el emulador de proceso:

![Después de la incorporación del proyecto de desarrollo][3]

Ahora puede observar que estamos comprobando la misma página web pero con una dirección URL ligeramente distinta. También puede ver que no existe el archivo python.exe en el servidor de prueba de Django. Estamos ejecutando Django a través de IIS mediante una puerta de enlace de FastCGI que se incluye automáticamente y se configura cuando se trabaja desde dentro de Visual Studio.

Cuando se ejecute en el emulador de proceso, puede iterar rápidamente la aplicación: tan solo vuelva a Visual Studio, actualice los archivos y actualice el explorador web. Podrá ver los resultados inmediatamente.

## Implementación de la aplicación en Azure

Ahora puede realizar la implementación del proyecto en Azure. Para realizar este procedimiento, solo tiene que hacer clic con el botón secundario en el proyecto de desarrollo de Azure en el Explorador de soluciones y seleccionar **Publicar**:

![Menú de aplicación de paquetes][Menú de aplicación de paquetes]

Después de seleccionar Publicar, se le solicitará que inicie sesión en Azure. Puede importar las credenciales existentes aquí o configurar credenciales nuevas:

![Suscripción al paquete][Suscripción al paquete]

Después de seleccionar las credenciales, verá la pantalla Configuración de publicación de Azure. Puede seleccionar las distintas opciones sobre cómo se llevará a cabo la implementación o simplemente pulsar **Publicar**:

![Configuración del paquete][Configuración del paquete]

Ahora tendrá que esperar a que la aplicación se configure e implemente.

![Implementación del paquete][Implementación del paquete]

Una vez que todo esté configurado, puede hacer clic en el siguiente enlace del nombre de DNS para ver el sitio web en ejecución en la nube:

![Su aplicación de Django en la nube][Su aplicación de Django en la nube]

  [vídeo de Youtube]: http://www.youtube.com/watch?v=UsLti4KlgAY
  [tutorial más reciente y más extenso]: ../web-sites-python-create-deploy-django-app/
  [0]: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstAppInCloud.png
  [Instalación de Python]: ../python-how-to-install/
  [Nuevas plantilla de proyectos de Python]: ./media/python-django-app-tools-visual-studio/ptvs-dj-NewProject.png
  [Visual Studio abrió el primer proyecto de Django]: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstProject.png
  [Incorporación de un elemento de menú de nueva aplicación]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddNewApp.png
  [Incorporación de un aviso de nombre de nueva aplicación]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddNewAppPrompt.png
  [Explorador de soluciones con una nueva aplicación agregada]: ./media/python-django-app-tools-visual-studio/ptvs-dj-MyFirstApp.png
  [Incorporación de la aplicación a settings.py en INSTALLED\_APPS]: ./media/python-django-app-tools-visual-studio/ptvs-dj-InstallApp.png
  [1]: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstView.png
  [Incorporación de un nuevo elemento a la carpeta de plantillas]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddFirstTemplate.png
  [2]: ./media/python-django-app-tools-visual-studio/ptvs-dj-NewDjangoTemplate.png
  [Plantilla agregada al Explorador de soluciones]: ./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateAdded.png
  [Intellisense de plantilla para filtros de Django]: ./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateIntellisense.png
  [Registro de la dirección URL]: ./media/python-django-app-tools-visual-studio/ptvs-dj-RegisterUrl.png
  [Django Hello World en un explorador y el servidor de prueba]: ./media/python-django-app-tools-visual-studio/ptvs-dj-DjangoHelloWorldTestServer.png
  [El depurador se detuvo en el punto de interrupción de la plantilla]: ./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateBreakpoint.png
  [Incorporación del proyecto de desarrollo]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddDeploymentProject.png
  [Después de la incorporación del proyecto de desarrollo]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AfterDeployProjAdded.png
  [3]: ./media/python-django-app-tools-visual-studio/ptvs-dj-ComputeEmulator.png
  [Menú de aplicación de paquetes]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish1.png
  [Suscripción al paquete]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish2.png
  [Configuración del paquete]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish3.png
  [Implementación del paquete]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish4.png
