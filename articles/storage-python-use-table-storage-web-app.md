<properties linkid="develop-python-web-app-with-blob-storage" urlDisplayName="Web App with Blob Storage" pageTitle="Python web app with table storage | Microsoft Azure" metaKeywords="Azure table storage Python, Azure Python application, Azure Python tutorial, Azure Python example" description="A tutorial that teaches you how to create a Python web application using the Azure Client Libraries. Django is used as the web framework." metaCanonical="" services="storage" documentationCenter="Python" title="Python Web Application using Table Storage" authors="huvalo" solutions="" videoId="" scriptId="" manager="" editor="mollybos" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"/>

# Aplicación web de Python con almacenamiento de tablas

En este tutorial, aprenderá a crear una aplicación que utiliza el almacenamiento de tablas con las bibliotecas de cliente de Azure para Python. Si esta es su primera aplicación de Azure para Python, es posible que primero desee echar un vistazo a [Aplicación web Django Hello World][].

Para esta guía, creará una aplicación de lista de tareas basada en web que puede implementar en Azure. La lista de tareas permite al usuario recuperar tareas, agregar tareas nuevas y marcar tareas como completadas. Utilizaremos Django como el marco web.

Los elementos de tarea se almacenan en el almacenamiento de Azure. El almacenamiento de Azure ofrece almacenamiento de datos no estructurados que es tolerante a errores y tiene una alta disponibilidad. El almacenamiento de Azure incluye varias estructuras de datos donde puede almacenar datos y tener acceso a ellos, además de aprovechar los servicios de almacenamiento de las API que se incluyen en el SDK de Azure para Python o
mediante las API de REST. Para obtener más información, consulte [Almacenamiento de datos y acceso a los mismos en Azure][].

Aprenderá a:

-   Trabajar con el servicio de almacenamiento de tablas de Azure

A continuación se muestra una captura de pantalla con el aspecto que tendrá la aplicación completa (los elementos de tarea agregados serán diferentes):

![](./media/storage-python-use-table-storage-web-app/web-app-with-storage-Finaloutput-mac.png)

[WACOM.INCLUDE [create-account-note][]]

## <span id="setup"></span> </a>Configuración del entorno de desarrollo

**Nota:** Si necesita instalar Python o las bibliotecas de clientes, consulte la [guía de instalación de Python][] (en inglés).

*Nota para Windows*: si se ha utilizado el programa de instalación de Windows WebPI, ya tiene instalados Django y las librerías de cliente.

## Creación de una cuenta de almacenamiento en Azure

[WACOM.INCLUDE [create-storage-account][]]

## Creación de un proyecto de Django

Estos son los pasos para la creación de la aplicación:

-   Cree un proyecto de Django predeterminado llamado "TableserviceSample".
-   Desde la línea de comandos, haga cd al directorio en el que desea almacenar el código y, a continuación, ejecute el siguiente comando:

        django-admin.py startproject TableserviceSample

-   Agregue un nuevo archivo de Python **views.py** al proyecto.
-   Agregue el siguiente código a **views.py** para importar el soporte de Django requerido:

        from django.http import HttpResponse
        from django.template.loader import render_to_string
        from django.template import Context

-   Cree una carpeta nueva llamada **templates** bajo la carpeta **TableserviceSample/TableserviceSample**.
-   Edite la configuración de la aplicación para que sus plantillas se puedan ubicar. Abra **settings.py** y agregue la siguiente entrada a INSTALLED\_APPS:

        'TableserviceSample',

-   Agregue un nuevo archivo de plantilla de Django **mytasks.html** a la carpeta **templates** y agréguele el siguiente código:

<!-- -->

        <html>
        <head><title></title></head>
        <body>
        <h2>My Tasks</h2> <br>
        <table border="1"> 
        <tr>
        <td>Name</td><td>Category</td><td>Date</td><td>Complete</td><td>Action</td></tr>
        {% for entity in entities %}
        <form action="update_task" method="GET">
        <tr><td>{{entity.name}} <input type="hidden" name='name' value="{{entity.name}}"></td>
        <td>{{entity.category}} <input type="hidden" name='category' value="{{entity.category}}"></td>
        <td>{{entity.date}} <input type="hidden" name='date' value="{{entity.date}}"></td>
        <td>{{entity.complete}} <input type="hidden" name='complete' value="{{entity.complete}}"></td>

        <td><input type="submit" value="Complete"></td>
        </tr>
        </form>
        {% endfor %}
        </table>
        <br>
        <hr>
        <table border="1">
        <form action="add_task" method="GET">
        <tr><td>Name:</td><td><input type="text" name="name"></input></td></tr>
        <tr><td>Category:</td><td><input type="text" name="category"></input></td></tr>
        <tr><td>Item Date:</td><td><input type="text" name="date"></input></td></tr>
        <tr><td><input type="submit" value="add task"></input></td></tr>
        </form>
        </table>
        </body>
        </html>    

## Importación del módulo de almacenamiento de Azure

Agregue el siguiente código en la parte superior de **views.py** justo después de las importaciones de Django.

    from azure.storage import TableService

## Obtención del nombre y la clave de la cuenta de almacenamiento

Agregue el siguiente código a **views.py** justo después de la importación de azure y reemplace "youraccount" y "yourkey" por su nombre de cuenta y clave reales. Puede obtener un nombre y una clave de cuenta en el portal de administración de azure.

    account_name = 'youraccount'
    account_key = 'yourkey'

## Creación de TableService

Agregue el siguiente código después de **account\_name**:

    table_service = TableService(account_name=account_name, account_key=account_key)
    table_service.create_table('mytasks')

## Enumeración de tareas

Agregue la función list\_tasks a **views.py**:

    def list_tasks(request): 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

## Incorporación de una tarea

Agregue la función add\_task a **views.py**:

    def add_task(request):
        name = request.GET['name']
        category = request.GET['category']
        date = request.GET['date']
        table_service.insert_entity('mytasks', {'PartitionKey':name+category, 'RowKey':date, 'name':name, 'category':category, 'date':date, 'complete':'No'}) 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

## Actualización del estado de una tarea

Agregue la función update\_task a **views.py**:

    def update_task(request):
        name = request.GET['name']
        category = request.GET['category']
        date = request.GET['date']
        partition_key = name + category
        row_key = date
        table_service.update_entity('mytasks', partition_key, row_key, {'PartitionKey':partition_key, 'RowKey':row_key, 'name': name, 'category':category, 'date':date, 'complete':'Yes'})
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

## Asignación de direcciones URL

Ahora necesita asignar las direcciones URL en la aplicación de Django. Abra **urls.py** y agregue las siguientes asignaciones a urlpatterns:

    url(r'^$', 'TableserviceSample.views.list_tasks'),
    url(r'^list_tasks$', 'TableserviceSample.views.list_tasks'),
    url(r'^add_task$', 'TableserviceSample.views.add_task'),
    url(r'^update_task$', 'TableserviceSample.views.update_task'),

## Ejecución de la aplicación

-   Cambie al directorio **TableserviceSample** si todavía no lo ha hecho y ejecute el comando:

    python manage.py runserver

-   Dirija el explorador a: `http://127.0.0.1:8000/`. Reemplace 8000 por el número de puerto real.

Ahora puede hacer clic en **Add Task** para crear una tarea y, a continuación, en el botón **Complete** para actualizar la tarea y establecer su estado en Yes.

## Ejecución de la aplicación en el emulador de proceso, publicación y detención/eliminación de la aplicación

Ahora que ha ejecutado correctamente la aplicación en el servidor integrado de Django, puede probarla con más detalle mediante su implementación en el emulador de Azure (solo Windows) y luego publicarla en Azure. Para obtener instrucciones generales sobre cómo hacerlo, consulte el artículo [Aplicación web Django Hello World][], que analiza estos pasos con mayor profundidad.

## <span id="NextSteps"></span></a>Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del servicio de almacenamiento de tablas de Azure, utilice estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure][]
-   Obtenga acceso al blog del equipo de almacenamiento de Azure: <http://blogs.msdn.com/b/windowsazurestorage/>

  [Aplicación web Django Hello World]: http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-django-web-app-windows-server
  [Almacenamiento de datos y acceso a los mismos en Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  
  [create-account-note]: ../includes/create-account-note.md
  [guía de instalación de Python]: http://windowsazure.com/en-us/documentation/articles/python-how-to-install
  [create-storage-account]: ../includes/create-storage-account.md
