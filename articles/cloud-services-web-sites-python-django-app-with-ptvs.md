<properties urlDisplayName="Django with Python Tools for Visual Studio 2.0" pageTitle="Creación de aplicaciones Django con Python Tools para Visual Studio 2,0" metaKeywords="" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a SQL Database or MySQL database instance and can be deployed to a website or cloud service." metaCanonical="" services="web-sites,cloud-services" documentationCenter="Python" title="Creating Django applications with Python Tools 2.0 for Visual Studio" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />





# Creación de aplicaciones Django con Python Tools 2.0 para Visual Studio

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>En este tutorial crearemos una aplicación Django sencilla haciendo uso de Python Tools 2.0 para Visual Studio. Esta aplicación permitirá a los usuarios participar en sondeos. Primero, utilizaremos una base de datos sqlite3 local y, a continuación, pasaremos a una base de datos MySQL o de SQL Server en Azure. Le mostraremos cómo habilitar la interfaz de administración de Django y utilizarla para agregar sondeos a nuestra base de datos. También utilizaremos el shell de Django integrado en Visual Studio.  Finalmente, implementaremos la aplicación en un sitio web de Azure y en un servicio en la nube de Azure.</p>
<p>Si prefiere ver un vídeo, el clip que aparece a la derecha muestra los mismos pasos que este tutorial.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="label">Ver el tutorial (en inglés)</a> <a style="background-image: url('/media/devcenter/python/videos/django-tutorial-180x120.png') !important;" href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir vídeo (en inglés)</span></a> <span class="time">33:08</span></div>
</div>

Este tutorial se centra en Python Tools para Visual Studio y Azure. Para obtener más información acerca de Django y la aplicación de sondeos que vamos a crear en este tutorial, consulte [https://www.djangoproject.com/](https://www.djangoproject.com/).

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Requisitos
Para realizar este tutorial, necesitará:

- [Python Tools 2.0 para Visual Studio (en inglés)](http://pytools.codeplex.com)
- [Python 2.7 (32 bits)](http://www.python.org/download/)
- Visual Studio y SDK de Azure:
  - VS 2010 Pro o superior con el SDK de Azure 2.1.
  - VS 2012 Pro o superior con el SDK de Azure 2.1, 2.2 o posterior.
  - VS 2013 Pro o superior con el SDK de Azure 2.2 o posterior.
  - Shell gratuito integrado en VS 2013. **El SDK de Azure no admite el shell integrado**. Puede desarrollar, depurar y ejecutar aplicaciones Django localmente mediante el shell gratuito integrado, pero no puede publicarlas en sitios web ni en servicios en la nube de Azure utilizando Visual Studio.

Utilice el instalador de plataforma web para instalar el SDK de Azure. De este modo se instalará el SDK, el emulador y Azure Tools para Visual Studio. En el instalador de plataforma web, busque **SDK de Azure para .NET** y seleccione una de las versiones del SDK compatibles con la versión de Visual Studio.

**Nota:** Para ejecutar esta aplicación correctamente utilizando un sitio web o servicio en la nube de Azure, debe utilizar la distribución oficial CPython 2.7 disponible en [python.org](http://www.python.org/download/). Otras distribuciones podrían funcionar, pero no son oficialmente compatibles.

## Descarga de un proyecto existente

Si desea omitir pasos de este tutorial, puede [descargar el código fuente de este proyecto](http://download-codeplex.sec.s-msft.com/Download?ProjectName=pytools&DownloadId=783376) (información en inglés).

La base de datos sqlite3 ya se creó, con las siguientes credenciales para el superusuario:

```
Username: tutorial
Password: azure
```

La descarga NO incluye un entorno virtual. Debe crear uno siguiendo los pasos de la sección [Creación de un entorno virtual](#creating-a-virtual-environment). Una vez hecho esto, el proyecto estará listo para la sección [Depuración](#debugging).

## Creación del proyecto

Python Tools para Visual Studio es compatible con entornos virtuales de Python.  Crearemos un proyecto django y usaremos un entorno virtual para instalar nuestras dependencias.  Este es el procedimiento recomendado para configurar proyectos que se publican en sitios web o servicios en la nube de Azure.

1. Abra Visual Studio, Archivo/Nuevo proyecto, Aplicación Django, con el nombre **tutorial**.

	![New Project](./media/cloud-services-python-create-deploy-django-app/django-tutorial-001-new-project.png)


**Nota:** en el Explorador de soluciones, en la sección Referencias, verá un nodo para Django 1.4. Este nodo se utiliza en la implementación de servicios en la nube de Azure, para instalar Python y Django en la máquina de destino. No elimine la referencia a Django 1.4 de la nota de referencias. Dado que vamos a utilizar un entorno virtual e instalar nuestro propio paquete de Django en él, se utilizará dicho paquete.

##<a name="creating-a-virtual-environment"></a>Creación de un entorno virtual

Utilizaremos un entorno virtual para instalar las dependencias, lo cual no solo es recomendable para cualquier aplicación Python, sino que además es obligatorio cuando se publica contenido en Azure.

1. Cree un nuevo entorno virtual.  En el Explorador de soluciones, haga clic con el botón secundario en **Entornos de Python** y seleccione **Agregar entorno virtual**.

	![Add Virtual Environment](./media/cloud-services-python-create-deploy-django-app/django-tutorial-002-add-virtual-env.png)

1. Seleccione Python 2.7 como intérprete base de Python y acepte el nombre predeterminado **env**. PTVS instalará pip o virtualenv si todavía no están instalados.

1. Haga clic con el botón secundario en **env** y en **Instalar paquete de Python**: **django**

	![Install Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-003-install-django.png)

1. Django incluye una gran cantidad de archivos, por lo que tardará un poco en instalarse. Puede ver el progreso en la ventana de resultados.

	![Install Django Output](./media/cloud-services-python-create-deploy-django-app/django-tutorial-004-install-django-output.png)

	**Nota:** en casos excepcionales, es posible que aparezca un error en la ventana de resultados. De ser así, compruebe si el error está relacionado con la limpieza. En ocasiones la limpieza da error, pero la instalación se realiza correctamente (desplácese hacia arriba en la ventana de resultados para comprobarlo). Esto se debe a que PTVS bloquea los archivos/carpetas temporales creados recientemente, lo que evita su eliminación durante la limpieza de pip.

1. Haga clic con el botón secundario en **env** y en **Instalar paquete de Python**: **pytz** (opcional pero recomendado, utilizado por Django para la compatibilidad de zonas horarias).

## Comprobación del entorno virtual

1. Asegurémonos de que todo esté instalado correctamente. Inicie el sitio web pulsando **F5** o **CTRL+F5**. De este modo se ejecutará el servidor de desarrollo de Django y el explorador web. Verá la siguiente página:

	![Django Web Browser](./media/cloud-services-python-create-deploy-django-app/django-tutorial-004b-itworked.png)

## Creación de la aplicación de sondeos

En esta sección, agregaremos una aplicación para administrar la votación en sondeos.

Los proyectos Django pueden incluir varias aplicaciones. En este tutorial, nuestro proyecto se llama "tutorial" y se corresponde con el proyecto de Visual Studio. La aplicación que vamos a agregar se llama **polls** y aparecerá como una carpeta del nodo de proyecto.

1. Seleccione el **nodo del proyecto**, **Agregar**->**Aplicación Django**, con el nombre **polls**. De este modo se creará una carpeta para la aplicación, con código reutilizable para los archivos de aplicación de uso habitual.

	![Add Django App](./media/cloud-services-python-create-deploy-django-app/django-tutorial-005-add-django-app.png)

1. En **tutorial/settings.py**, agregue lo siguiente a **INSTALLED\_APPS**:

		'polls',

1. y quite el comentario de **INSTALLED\_APPS**:

		'django.contrib.admin',

1. Reemplace **tutorial/urls.py** por el código siguiente:

        from django.conf.urls import patterns, include, url

        from django.contrib import admin
        admin.autodiscover()

        urlpatterns = patterns('',
            url(r'^', include('polls.urls', namespace="polls")),
            url(r'^admin/', include(admin.site.urls)),
        )

1. Reemplace **polls/models.py** por el código siguiente:

        from django.db import models
		
        class Poll(models.Model):
            question = models.CharField(max_length=200)
            pub_date = models.DateTimeField('date published')
			
            def __unicode__(self):
                return self.question
		
        class Choice(models.Model):
            poll = models.ForeignKey(Poll)
            choice_text = models.CharField(max_length=200)
            votes = models.IntegerField(default=0)
			
            def __unicode__(self):
                return self.choice_text

1. Reemplace **polls/views.py** por el código siguiente:

		from django.shortcuts import get_object_or_404, render
		from django.http import HttpResponseRedirect
		from django.core.urlresolvers import reverse
		from polls.models import Choice, Poll
		
		def vote(request, poll_id):
		    p = get_object_or_404(Poll, pk=poll_id)
		    try:
		        selected_choice = p.choice_set.get(pk=request.POST['choice'])
		    except (KeyError, Choice.DoesNotExist):
		        # Redisplay the poll voting form.
		        return render(request, 'polls/detail.html', {
		            'poll': p,
		            'error_message': "You didn't select a choice.",
		        })
		    else:
		        selected_choice.votes += 1
		        selected_choice.save()
		        # Always return an HttpResponseRedirect after successfully dealing
		        # with POST data. This prevents data from being posted twice if a
		        # user hits the Back button.
		        return HttpResponseRedirect(reverse('polls:results', args=(p.id,)))
		
1. Agregue un nuevo archivo de Python **polls/urls.py** con el código siguiente:

	    from django.conf.urls import patterns, url
	    from django.views.generic import DetailView, ListView
	    from polls.models import Poll
	
	    urlpatterns = patterns('',
	        url(r'^$',
	            ListView.as_view(
	                queryset=Poll.objects.order_by('-pub_date')[:5],
	                context_object_name='latest_poll_list',
	                template_name='polls/index.html'),
	            name='index'),
	        url(r'^(?P<pk>\d+)/$',
	            DetailView.as_view(
	                model=Poll,
	                template_name='polls/detail.html'),
	            name='detail'),
	        url(r'^(?P<pk>\d+)/results/$',
	            DetailView.as_view(
	                model=Poll,
	                template_name='polls/results.html'),
	            name='results'),
	        url(r'^(?P<poll_id>\d+)/vote/$', 'polls.views.vote', name='vote'),
	    )
	
1. Cree un nuevo elemento **polls/admin.py** con el código siguiente:

		from django.contrib import admin
		from polls.models import Choice, Poll
		
		class ChoiceInline(admin.TabularInline):
		    model = Choice
		    extra = 3
		
		class PollAdmin(admin.ModelAdmin):
		    fieldsets = [
		        (None,               {'fields': ['question']}),
		        ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
		    ]
		    inlines = [ChoiceInline]
		    list_display = ('question', 'pub_date')
		    list_filter = ['pub_date']
		    search_fields = ['question']
		    date_hierarchy = 'pub_date'
		
		admin.site.register(Poll, PollAdmin)

1. En la carpeta **polls/templates**, cree una nueva carpeta llamada **polls**.

1. Mueva el archivo **polls/templates/index.html** a la carpeta **polls/templates/polls** utilizando las funciones de arrastrar y colocar o copiar y pegar.

1. Reemplace **polls/templates/polls/index.html** por la marca siguiente:

        <html>
        <head></head>
        <body>
        {% if latest_poll_list %}
            <ul>
            {% for poll in latest_poll_list %}
                <li><a href="{% url 'polls:detail' poll.id %}">{{ poll.question }}</a></li>
            {% endfor %}
            </ul>
        {% else %}
            <p>No polls are available.</p>
        {% endif %}
        </body>
        </html>

1. Cree una nueva plantilla HTML de Django **polls/templates/polls/detail.html** con el código siguiente:

        <html>
        <head></head>
        <body>
        <h1>{{ poll.question }}</h1>
        {% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}
        <form action="{% url 'polls:vote' poll.id %}" method="post">
        {% csrf_token %}
        {% for choice in poll.choice_set.all %}
            <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
            <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br />
        {% endfor %}
        <input type="submit" value="Vote" />
        </form>
        </body>
        </html>

1. Cree una nueva plantilla HTML de Django **polls/templates/polls/results.html** con el código siguiente:

        <html>
        <head></head>
        <body>
        <h1>{{ poll.question }}</h1>
        <ul>
        {% for choice in poll.choice_set.all %}
            <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
        {% endfor %}
        </ul>
        <a href="{% url 'polls:detail' poll.id %}">Vote again?</a>
        </body>
        </html>

1. Ahora el proyecto debería incluir los siguientes archivos:

	![Solution Explorer](./media/cloud-services-python-create-deploy-django-app/django-tutorial-006-solution-explorer.png)

## Creación de una base de datos sqlite3 localmente

La aplicación web está casi lista para usarse, pero primero tenemos que configurar una base de datos.  Para probar el sitio web localmente, crearemos una base de datos sqlite3.  Se trata de una base de datos ligera que no requiere ninguna instalación adicional.  El archivo de base de datos se creará en la carpeta del proyecto.

1. En **tutorial/settings.py**, agregue la siguiente instrucción de importación en la parte superior del archivo:

        from os import path

1. Agregue la siguiente definición cerca de la parte superior del archivo, tras la instrucción de importación:

		PROJECT_ROOT = path.dirname(path.abspath(path.dirname(__file__)))

1. Cambie la sección DATABASES por el código siguiente:

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.sqlite3',
                'NAME': path.join(PROJECT_ROOT, 'db.sqlite3'),
                'USER': '',
                'PASSWORD': '',
                'HOST': '',
                'PORT': '',
            }
        }

1. Haga clic con el botón secundario en el nodo de proyecto y seleccione **Django**->**BD de sincronización de Django**.  Aparecerá una ventana interactiva de administración de Django.  Dado que la base de datos todavía no existe, se le pedirá que cree unas credenciales de administrador.  Escriba un nombre de usuario y una contraseña. El correo electrónico es opcional.

	![Django Sync DB](./media/cloud-services-python-create-deploy-django-app/django-tutorial-007-sqlite3.png)

1. Inicie el sitio web pulsando F5 o CTRL+F5. De este modo se ejecutará el servidor de desarrollo de Django y el explorador web. En la URL raíz del sitio web aparece el índice de sondeos, pero todavía no hay ninguno en la base de datos.

	![Web Browser](./media/cloud-services-python-create-deploy-django-app/django-tutorial-008-dev-server.png)

1. Vaya a **http://localhost:{puerto}/admin**. Puede obtener el número de puerto de la ventana de la consola del servidor de desarrollo. Inicie sesión con las credenciales que creó en el paso anterior.

	![Add Poll](./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-login.png)

1. Utilice la interfaz de administración para agregar uno o dos sondeos.  No dedique demasiado tiempo a agregar sondeos a la base de datos local, ya que más tarde cambiaremos a una base de datos en la nube y será entonces cuando los agreguemos.

	![Poll Index](./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-add-poll.png)

1. Vaya a **http://localhost:{puerto}/**.  Verá un índice de los sondeos agregados.

	![](./media/cloud-services-python-create-deploy-django-app/django-tutorial-010-index.png)

1. Haga clic en uno de los sondeos para ir a la página de participación.

	![Poll Detail](./media/cloud-services-python-create-deploy-django-app/django-tutorial-011-detail.png)

1. Envíe sus respuestas y se le redirigirá a la página de resultados, donde verá cómo aumenta el recuento de participantes.

	![Poll Results](./media/cloud-services-python-create-deploy-django-app/django-tutorial-012-results.png)

## Uso de hojas de estilo y otros archivos estáticos

En esta sección, actualizaremos la apariencia de nuestro sitio utilizando una hoja de estilo. Los archivos estáticos como las hojas de estilo se tratan de manera diferente, por lo que es importante almacenarlos en la ubicación correcta.

1. En **tutorial/settings.py**, busque la asignación de **STATIC_ROOT** y cámbiela a:

        STATIC_ROOT = path.join(PROJECT_ROOT, 'static').replace('\\','/')

1. En la carpeta **polls**, cree una nueva carpeta llamada **static**.

1. En la carpeta **polls/static**, cree una nueva carpeta llamada **polls**.

1. En la carpeta **polls/static/polls**, cree una nueva carpeta llamada **images**.

1. Agregue un nuevo archivo de hoja de estilo **polls/static/polls/style.css** con el código siguiente:

        body {
            color: darkblue;
            background: white url("images/background.jpg");
        }
		
1. Agregue una imagen existente a la carpeta **polls/static/polls/images** y asígnele el nombre **background.jpg**.

1. Ahora el proyecto debería incluir los siguientes archivos:

	![Solution Explorer](./media/cloud-services-python-create-deploy-django-app/django-tutorial-013-solution-explorer.png)

1. Modifique el encabezado de todas las plantillas para que haga referencia a la hoja de estilo con la marca siguiente:

        <head>
        {% load staticfiles %}
        <link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}" />
        </head>

1. Ejecute el sitio web de nuevo.  Las páginas de índice, sondeo y resultados utilizarán la hoja de estilo creada: el texto en azul oscuro y una imagen de fondo.

	![Poll Detail](./media/cloud-services-python-create-deploy-django-app/django-tutorial-014-detail-styled.png)

##<a name="debugging"></a>Depuración

Python Tools para Visual Studio ofrece una compatibilidad especial con la depuración de plantillas de Django.

1. Abra **polls/templates/polls/index.html** y coloque un punto de interrupción mediante **F9** en esta línea:

        {% if latest_poll_list %}

1. Inicie la depuración presionando **F5**. Visual Studio se detendrá en el archivo de plantilla.

1. Abra la ventana **Variables locales** desde **Depurar->Ventanas->Variables locales** y verá la variable **latest\_poll\_list** y su valor.

1. Puede presionar **F10** para ir paso a paso al igual que en el código Python normal.  Dentro del bucle for, puede inspeccionar el valor de **poll**:

	![Debugging](./media/cloud-services-python-create-deploy-django-app/django-tutorial-015-debugging.png)

## Creación de una base de datos en Azure

Ahora que hemos comprobado que nuestra aplicación de sondeos funciona localmente, vamos a migrarla a una base de datos hospedada en Azure.

En las dos secciones siguientes le mostraremos cómo utilizar una base de datos SQL y una base de datos MySQL.  En ambos casos se trata de servicios hospedados.

Otra opción sería crear una máquina virtual e instalar un servidor de bases de datos.  Consulte [aquí](http://www.windowsazure.com/es-es/manage/linux/common-tasks/mysql-on-a-linux-vm/) las instrucciones para configurar MySQL en una máquina virtual Linux de Azure.

**Nota:** es posible utilizar una base de datos sqlite3 en Azure (únicamente para fines de desarrollo, no es recomendable usarla para la producción). Deberá agregar el archivo **db.sqlite3** al proyecto para implementar la base de datos con la aplicación Django.

### Base de datos SQL de Azure

En esta sección, crearemos una base de datos SQL en Azure, agregaremos los paquetes necesarios al entorno virtual y cambiaremos la configuración para utilizar la nueva base de datos.

1. En el Portal de administración de Azure, seleccione **BASES DE DATOS SQL**.

1. Primero cree un servidor para hospedar la base de datos.  Seleccione **SERVIDORES** y **AGREGAR**.

1. En la pestaña **CONFIGURAR** del servidor recién creado, aparecerá la dirección IP del cliente actual. Haga clic en **AGREGAR A DIRECCIONES IP PERMITIDAS** junto a ella.

	**Nota:** en ocasiones Azure no detecta la dirección IP del cliente correctamente. Si obtiene un error al sincronizar la base de datos, debe copiar y pegar la dirección IP del mensaje de error y agregarla a las direcciones IP permitidas.

1. A continuación, crearemos la base de datos. En la pestaña **BASES DE DATOS**, haga clic en **AGREGAR** en la barra inferior.

1. En Visual Studio, instalaremos los paquetes necesarios para obtener acceso a bases de datos de SQL Server desde Django en nuestro entorno virtual.

1. Haga clic con el botón secundario en **env** y en **Instalar paquete de Python**: **pyodbc** utilizando **easy\_install**.

1. Haga clic con el botón secundario en **env** y en **Instalar paquete de Python**: **django-pyodbc-azure** utilizando **pip**.

1. Edite **tutorial/settings.py** y cambie la definición **DATABASES** al siguiente código, reemplazando **NAME**, **USER**, **PASSWORD** y **HOST** por los valores indicados en el panel de control de ClearDB:

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<database name>',
                'USER': '<user name>@<server name>',
                'PASSWORD': '<user password>',
                'HOST': '<server name>.database.windows.net',
                'PORT': '',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': True,
                },
            }
        }

	Asegúrese de utilizar un controlador instalado en su equipo.  En el menú/pantalla de inicio, abra **Herramientas administrativas**, **Orígenes de datos ODBC (32 bits)**. Los controladores aparecen en la pestaña **Controladores**.
	
	Si va a ejecutar la aplicación en un sitio web de Azure, podrá utilizar tanto **SQL Server Native Client 10.0** como **SQL Server Native Client 11.0**.

	Si va a ejecutarla en un servicio en la nube de Azure, solo podrá utilizar **SQL Server Native Client 11.0**.
	
1. Sincronice la base de datos y cree unas credenciales de administrador, como hicimos para la base de datos sqlite3 local.

### Base de datos MySQL

En esta sección, crearemos una base de datos MySQL en Azure, agregaremos los paquetes necesarios al entorno virtual y cambiaremos la configuración para utilizar la nueva base de datos.

En la Tienda de Azure, puede agregar varios servicios a su cuenta, incluida una base de datos MySQL.  Es posible crear una cuenta de evaluación sin coste alguno, y las bases de datos pequeñas son gratuitas hasta un determinado tamaño.

1. En el Portal de administración de Azure, seleccione **NUEVO**->**TIENDA**->**SERVICIOS DE APLICACIONES**->**Base de datos MySQL de ClearDB**.  Cree una base de datos con el plan gratuito.

1. A continuación, instalaremos el paquete necesario para obtener acceso a bases de datos MySQL desde Django en nuestro entorno virtual.  Haga clic con el botón secundario en **env** y en **Instalar paquete de Python**: **mysql-python** utilizando **easy\_install**.

1. Edite **tutorial/settings.py** y cambie la definición **DATABASES** al siguiente código, reemplazando **NAME**, **USER**, **PASSWORD** y **HOST** por los valores indicados en el panel de control de ClearDB:

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<database name>',
                'USER': '<user name>',
                'PASSWORD': '<user password>',
                'HOST': '<host url>',
                'PORT': '',
            }
        }

1. Sincronice la base de datos y cree unas credenciales de administrador, como hicimos para la base de datos sqlite3 local.

## Uso del shell de Django

1. Haga clic con el botón secundario en el nodo de proyecto y seleccione **Django** -> **Abrir shell de Django**.

1. En esta ventana interactiva, podemos obtener acceso a nuestra base de datos utilizando nuestros modelos.  Escriba el código siguiente para agregar un sondeo a la base de datos:

		from polls.models import Poll, Choice
		from django.utils import timezone

		p = Poll(question="Favorite way to host Django on Azure?", pub_date=timezone.now())
		p.save()

		p.choice_set.create(choice_text='Web Site', votes=0)
		p.choice_set.create(choice_text='Cloud Service', votes=0)
		p.choice_set.create(choice_text='Virtual Machine', votes=0)

	![Django Shell Add Poll](./media/cloud-services-python-create-deploy-django-app/django-tutorial-018-shell-add-poll.png)

1. El análisis estático de los modelos ofrece una vista limitada de la API completa.  En la ventana interactiva, obtendrá IntelliSense respecto a los objetos en directo, por lo que resulta una gran manera de explorar la API.  Estas son algunas cosas que puede probar en la ventana interactiva:

		# all poll objects
		Poll.objects.all()

		# primary key for poll
		p.id

		# all choice objects for the poll
		p.choice_set.all()

		# get object by primary key
		Poll.objects.get(pk=1)

	![Django Shell Query Poll](./media/cloud-services-python-create-deploy-django-app/django-tutorial-019-shell-query.png)

1. Inicie el sitio web.  Debería ver el sondeo que agregamos utilizando el shell de Django.

## Publicación en Azure

Ahora que la base de datos se encuentra en Azure, el paso siguiente es hospedar el propio sitio web en Azure.

Azure ofrece las siguientes opciones para hospedar aplicaciones Django:

- [Sitio web](http://www.windowsazure.com/es-es/services/web-sites/)
- [Servicio en la nube](http://www.windowsazure.com/es-es/services/cloud-services/)
- [Máquina virtual](http://www.windowsazure.com/es-es/services/virtual-machines/)

Python Tools para Visual Studio incluye funciones de publicación en sitios web y servicios en la nube de Azure.  Dichas funciones se describen en las dos secciones siguientes (puede leer cualquiera de ellas o ambas).

En ambos casos, PTVS se encarga de configurar IIS y genera un archivo web.config si todavía no hay ninguno en el proyecto.  Los archivos estáticos se recopilarán automáticamente (manage.py collectstatic) siempre y cuando tenga establecido STATIC_ROOT en settings.py.

El hospedaje de Django en una máquina virtual está fuera del ámbito de este tutorial.  Esto implica crear una máquina virtual con el sistema operativo deseado (Windows o Linux), instalar Python e implementar la aplicación Django manualmente. 

### Sitio web de Azure

1. Primero, debemos crear un sitio web.  En el Portal de administración de Azure, haga clic en **NUEVO**->**PROCESO**->**SITIO WEB**->**CREACIÓN RÁPIDA**.  Seleccione cualquier nombre que esté disponible.

1. Una vez creado el sitio web, descargue su perfil de publicación.

	![Web Site Download Profile](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-download-profile.png)

1. En Visual Studio, haga clic con el botón secundario en el nodo de proyecto y seleccione **Publicar**.

	![Web Site Publish](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-publish.png)

1. Importe el archivo de perfil de publicación del sitio web que descargó anteriormente.

1. Acepte los valores predeterminados y haga clic en **Publicar** para comenzar la publicación.

1. Cuando finalice la publicación, se abrirá el sitio web publicado en un explorador web.

	![Web Site Browser](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website.png)

### Servicio en la nube de Azure

1. Haga clic con el botón secundario en el nodo de proyecto y seleccione **Agregar proyecto de servicio de nube de Azure** o **Convertir -> Convertir en proyecto de servicio de nube de Azure** (la opción que aparece depende de la versión de Visual Studio).  De este modo, se agregará un nuevo proyecto a la solución, con la extensión .Azure. Este nuevo proyecto está marcado como proyecto de inicio de la solución.

	**Nota:** si en el menú contextual del proyecto no aparece el comando para crear un proyecto de servicio en la nube de Azure, deberá instalar Azure Tools para Visual Studio. Estas herramientas se instalan como parte del SDK de Azure para .NET. Consulte la sección de requisitos al principio de este tutorial.

	![Solution Explorer](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-solution-explorer.png)

#### Ejecución en el emulador de Azure

1. Deberá **reiniciar Visual Studio como administrador** para poder ejecutar la aplicación en el emulador de proceso.

1. Inicie la depuración pulsando **F5** y la aplicación se ejecutará e implementará en el emulador de proceso. Compruebe que la interfaz de administración funcione y que pueda participar en sondeos.

	![Compute Emulator](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-emulator.png)

1. Ahora puede reiniciar Visual Studio si no desea continuar con la ejecución como administrador.

#### Publicación en un servicio en la nube de Azure

1. A continuación, publicaremos el servicio en la nube en Azure. Haga clic con el botón secundario en el proyecto de servicio en la nube **tutorial.Azure** y en **Publicar**.

	**Nota:** asegúrese de seleccionar **Publicar** en el proyecto de servicio en la nube. De este modo, se iniciará el diálogo **Publicar aplicación de Azure**, con el que podrá publicar un servicio en la nube de Azure.  Si selecciona **Publicar** en el proyecto Django, se iniciará el diálogo **Publicación web** para publicar la aplicación en un sitio web de Azure.  

	![Cloud Service Publish](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish.png)

1. Deberá importar el archivo de suscripción de Azure.  Haga clic en el vínculo [Iniciar sesión para descargar credenciales](https://manage.windowsazure.com/publishsettings/index?client=vs&schemaversion=2.0) para descargarlo del portal de Azure.

1. En la página de configuración, seleccione **Crear nuevo** en el cuadro combinado de servicios en la nube para crear un nuevo servicio en la nube. Puede utilizar cualquier nombre disponible.

	![Cloud Service Settings](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-settings.png)

1. Acepte los valores predeterminados y haga clic en **Publicar**. Este proceso tardará más que la publicación de la aplicación en un sitio web, ya que es necesario aprovisionar una máquina virtual para el servicio en la nube. Puede ver el progreso en la ventana de registro de actividad de Azure:

	![Cloud Service Publish](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish-progress.png)

1. Cuando finalice la publicación, haga clic en la URL del sitio web en la ventana de registro de actividad de Azure para abrir un explorador web.

	![Cloud Service Browser](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice.png)

## Conclusión

En este tutorial, desarrollamos una aplicación Django utilizando [Python Tools para Visual Studio](http://pytools.codeplex.com).  Utilizamos tres bases de datos diferentes: sqlite3, SQL Server y MySQL.  Finalmente, publicamos la aplicación en sitios web y servicios en la nube de Azure.
