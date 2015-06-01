<properties 
    pageTitle="Generación de una aplicación web con Python y Flask mediante la DocumentDB | Azure" 
    description="Obtenga información acerca de cómo usar la DocumentDB para almacenar y acceder a los datos de una aplicación web de Python y Flask (MVC) hospedada en Azure." 
    services="documentdb" 
    documentationCenter="python" 
    authors="crwilcox" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="03/23/2015" 
    ms.author="crwilcox"/>

# Generación de una aplicación web con Python y Flask (MVC) mediante la DocumentDB
------------------------------------------------------------------------

Para destacar la forma en que los clientes pueden aprovechar eficazmente Microsoft Azure DocumentDB para almacenar y consultar documentos JSON, este documento proporciona un tutorial integral sobre la compilación de una aplicación web para votaciones mediante Microsoft Azure DocumentDB.

En este tutorial aprenderá a utilizar el servicio de DocumentDB proporcionado por Azure para almacenar datos y obtener acceso a ellos desde cualquier aplicación web Phyton hospedada en Azure. En él se presupone que tiene experiencia previa en el uso de los sitios web de Phyton y Azure.

Esta tutorial abarca lo siguiente:

1. Crear y aprovisionar una cuenta de DocumentDB.
2. Crear una aplicación Phyton MVC.
3. Conectarse y utilizar DocumentDB desde su aplicación web.
4. Implementar la aplicación web en Sitios web Azure.

Siguiendo este tutorial, podrá compilar una aplicación de votación simple que le permita votar en un sondeo.

![Screen shot of the todo list web application created by this tutorial](./media/documentdb-python-application/image1.png)


## Requisitos previos

Antes de seguir las instrucciones del presente artículo, debe asegurarse de tener instalados los siguientes elementos:

- [Visual Studio 2013](http://www.visualstudio.com/) o superior, o [Visual Studio Express], que es la versión gratuita.
- Herramientas de Python para Visual Studio desde [aquí][].
- Azure SDK para Visual Studio 2013, versión 2.4 o superior disponible desde [aquí][1].
- Python 2.7 desde [aquí][2].
- Compilador de Microsoft Visual C++ para Python 2.7 desde [aquí][3].

## Paso 1: Creación de una cuenta de base de datos de la DocumentDB

Comenzaremos por crear una cuenta de DocumentDB. Si ya tiene una cuenta, puede ir al [paso 2: Creación de una nueva aplicación web de Phyton Flask](#Step-2:-Create-a-new-Python-Flask-Web-Application).

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../includes/documentdb-keys.md)]

<br/>
Ahora veremos cómo crear una nueva aplicación web de Phyton Flask desde el principio.

## Paso 2: Creación de una nueva aplicación web de Phyton Flask

1. Abra Visual Studio y, a continuación, haga clic en **Archivo** -> **Nuevo proyecto** -> **Python** -> **Proyecto web Flask** y cree un nuevo proyecto llamado **tutorial**. 

	Para aquellos que desconozcan Flask: se trata de un marco web que nos ayuda a crear aplicaciones web en Python más rápido. [Haga clic aquí para tener acceso a tutoriales de Flask][].

	![Screen shot of the New Project window in Vidual Studio with Python highlighted on the left, Flask Web Project selected in the middle, and the name tutorial in the Name box](./media/documentdb-python-application/image9.png)

2. Se le preguntará si desea instalar paquetes externos. Haga clic en **Instalar en un entorno virtual**. Use Python 2.7 como el entorno de base, ya que PyDocumentDB no admite actualmente Python 3.x.  Esto configurará el entorno virtual de Python requerido para su proyecto.

	![Screen shot of the tutorial - Python Tools for Visual Studio window](./media/documentdb-python-application/image10.png)


## Paso 3: Modificación de la aplicación web de Python Flask

### Adición de paquetes flask a su proyecto

Una vez que el proyecto esté configurado, deberá agregar ciertos paquetes Flask necesarios para nuestro proyecto, incluido pydocumentdb, el paquete de python para DocumentDB. 

1. Abra el archivo denominado **requirements.txt** y reemplace el contenido por lo siguiente:

    	flask==0.9
    	flask-mail==0.7.6
    	sqlalchemy==0.7.9
    	flask-sqlalchemy==0.16
    	sqlalchemy-migrate==0.7.2
    	flask-whooshalchemy==0.55a
    	flask-wtf==0.8.4
    	pytz==2013b
    	flask-babel==0.8
    	flup
    	pydocumentdb>=0.9.4-preview

2. Haga clic con el botón derecho en **env** y haga clic en **Instalar desde requirements.txt**.

	![Screen shot showing env (Python 2.7) selected with Install from requirements.txt highlighted in the list](./media/documentdb-python-application/image11.png)

> [AZURE.NOTE] En casos excepcionales, es posible que aparezca un error en la ventana de resultados. De ser así, compruebe si el error está relacionado con la limpieza. En ocasiones la limpieza da error, pero la instalación se realiza correctamente (desplácese hacia arriba en la ventana de resultados para comprobarlo). Si esto ocurre, puede continuar.

### <a name="verify-the-virtual-environment"></a>Comprobación del entorno virtual

Asegurémonos de que todo esté correctamente instalado. 

- Inicie el sitio web haciendo clic en **F5**. De este modo se ejecutará el servidor de desarrollo flask y el explorador web. Debe ver la página siguiente.

	![The empty flask project displayed in a browser](./media/documentdb-python-application/image12.png)

### Creación de definición de base de datos, colección y documento

Ahora vamos a crear la aplicación de sondeo.

- Haga clic con el botón derecho para agregar un archivo Python en la carpeta denominada **tutorial** en el Explorador de soluciones.  Asigne al archivo el nombre **forms.py**.  

    	from flask.ext.wtf import Form
    	from wtforms import RadioField

    	class VoteForm(Form):
        	deploy_preference  = RadioField('Deployment Preference', choices=[
            	('Web Site', 'Web Site'),
            	('Cloud Service', 'Cloud Service'),
            	('Virtual Machine', 'Virtual Machine')], default='Web Site')

### Agregue las importaciones necesarias a views.py.

- Agregue las siguientes instrucciones import a la parte superior de **views.py**. Esto importará los paquetes de PythonSDK y Flask de DocumentDB.

    	from forms import VoteForm
    	import config
    	import pydocumentdb.document_client as document_client
    

### Creación de base de datos, colección y documento

- Agregue el siguiente código a **views.py**. Esta acción se encarga de crear la  base de datos utilizada por el formulario. No elimine parte alguna del código existente en  **views.py**. Solo tiene que incluir esto al final.

    	@app.route('/create')
    	def create():
        	"""Renders the contact page."""
        	client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
    
        	# Attempt to delete the database.  This allows this to be used to recreate as well as create
        	try:
            	db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            	client.DeleteDatabase(db['_self'])
        	except:
            	pass
    
       		# Create database
        	db = client.CreateDatabase({ 'id': config.DOCUMENTDB_DATABASE })
        	# Create collection
        	collection = client.CreateCollection(db['_self'],{ 'id': config.DOCUMENTDB_COLLECTION })
        	# Create document
        	document = client.CreateDocument(collection['_self'],
            	{ 'id': config.DOCUMENTDB_DOCUMENT,
            	'Web Site': 0,
            	'Cloud Service': 0,
            	'Virtual Machine': 0,
            	'name': config.DOCUMENTDB_DOCUMENT })
    
        	return render_template(
            	'create.html', 
            	title='Create Page', 
            	year=datetime.now().year,
            	message='You just created a new database, collection, and document.  Your old votes have been deleted')

### Lectura de la base de datos, la colección y el documento, y envío del formulario

- Agregue el siguiente código a **views.py**. Esta acción se encarga de configurar el formulario mediante la lectura de la base de datos, la colección y el documento. No elimine el código existente en **views.py**. Solo tiene que incluir esto al final.
    
    	@app.route('/vote', methods=['GET', 'POST'])
    	def vote(): 
        	form = VoteForm()
        	replaced_document ={}
        	if form.validate_on_submit(): # is user submitted vote  
            	client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
            
            	# Read databases and take first since id should not be duplicated.
            	db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            
            	# Read collections and take first since id should not be duplicated.
            	coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.DOCUMENTDB_COLLECTION))
    
            	# Read documents and take first since id should not be duplicated.
            	doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.DOCUMENTDB_DOCUMENT))
    
            	# Take the data from the deploy_preference and increment our database
            	doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
            	replaced_document = client.ReplaceDocument(doc['_self'], doc)
    
            	# Create a model to pass to results.html
            	class VoteObject:
                	choices = dict()
                	total_votes = 0
    
            	vote_object = VoteObject()
            	vote_object.choices = {
                	"Web Site" : doc['Web Site'],
                	"Cloud Service" : doc['Cloud Service'],
                	"Virtual Machine" : doc['Virtual Machine']
            	}
            	vote_object.total_votes = sum(vote_object.choices.values())
    
            	return render_template(
                	'results.html', 
                	year=datetime.now().year, 
                	vote_object = vote_object)
    
        	else :
            	return render_template(
                	'vote.html', 
                	title = 'Vote',
                	year=datetime.now().year,
                	form = form)


### Creación de los archivos html

En la carpeta de plantillas, agregue los siguientes archivos html: create.html, results.html y vote.html.

1. Agregue el siguiente código a **create.html**. Esta acción se encarga de mostrar un mensaje que indica que hemos creado una nueva base de datos, colección y documento.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>{{ title }}.</h2>
    	<h3>{{ message }}</h3>
    	<p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    	{% endblock %}

2. Agregue el siguiente código a **results.html**. Se encarga de mostrar los resultados del sondeo.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>Results of the vote</h2>
   	 	<br />
	
    	{% for choice in vote_object.choices %}
    	<div class="row">
        	<div class="col-sm-5">{{choice}}</div>
        	<div class="col-sm-5">
            	<div class="progress">
                	<div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" 
                     aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                    	{{vote_object.choices[choice]}}
                	</div>
            	</div>
        	</div>
    	</div>
    	{% endfor %}

    	<br />
    	<a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    	{% endblock %}

3. Agregue el siguiente código a **vote.html**. Se encarga de mostrar los resultados y aceptar los votos. Al registrar los votos, el control se pasa sobre views.py donde reconoceremos el voto emitido y se anexará al documento de forma correspondiente.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>What is your favorite way to host an application on Azure?</h2>
    	<form action="" method="post" name="vote">
        	{{form.hidden_tag()}}
        	{{form.deploy_preference}}
        	<button class="btn btn-primary" type="submit">Vote</button>
    	</form>
    	{% endblock %}

4. Reemplace el contenido de **index.html** por lo siguiente. Esto sirve como la página de aterrizaje de la aplicación.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>Python + DocumentDB Voting Application.</h2>
    	<h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
    	<p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    	<p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    	{% endblock %}


### Adición de un archivo de configuración y modificación de __init__.py

1. Haga clic con el botón derecho en el tutorial del nombre del proyecto y agregue el archivo **config.py**.
Esta configuración es necesaria para los formularios en flask. También puede utilizarlo para proporcionar una clave secreta. Sin embargo, esto no es necesario para este tutorial. 

2. Agregue el siguiente código a config.py. Modifique los valores de **DOCUMENTDB_HOST** y **DOCUMENTDB_KEY**.

    	CSRF_ENABLED = True
    	SECRET_KEY = 'you-will-never-guess'
    
    	DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    	DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
    
    	DOCUMENTDB_DATABASE = 'voting database'
    	DOCUMENTDB_COLLECTION = 'voting collection'
    	DOCUMENTDB_DOCUMENT = 'voting document'

3. Del mismo modo, sustituya el contenido de **__init__.py** por lo siguiente.

    	from flask import Flask
    	app = Flask(__name__)
    	app.config.from_object('config')
    	import tutorial.views

4. Tras seguir los pasos mencionados anteriormente, este es el aspecto que debería tener el Explorador de soluciones.

	![Screen shot of the Visual Studio Solution Explorer window](./media/documentdb-python-application/image15.png)


## Paso 4: Ejecución de la aplicación de forma local

1. Presione F5 o el botón de ejecución en Visual Studio. Debería ver lo siguiente en la pantalla.

	![Screen shot of the Python + DocumentDB Voting Application displayed in a web browser](./media/documentdb-python-application/image16.png)

2. Haga clic en **Crear/borrar la base de datos de votos** para generar la base de datos.

	![Screen shot of the Create Page of the web application](./media/documentdb-python-application/image17.png)

3. A continuación, haga clic en **Votar** y seleccione su opción.

	![Screen shot of the web application with a voting question posed](./media/documentdb-python-application/image18.png)

4. Para cada voto que emita, se incrementará el contador correspondiente.

	![Screen shot of the Results of the vote page shown](./media/documentdb-python-application/image19.png)


## Paso 5: Implementación de la aplicación en Sitios web Azure

Ahora que tiene la aplicación completa funcionando correctamente con DocumentDB, vamos a implementar esto en sitios web Azure. 

1. Haga clic con el botón derecho en el Proyecto del Explorador de soluciones (asegúrese de que no se esté ejecutando de forma local) y elija Publicar.  A continuación, elija Sitios web Microsoft Azure.

 	![Screen shot of the tutorial selected in Solution Explorer, with the Publish option highlighted](./media/documentdb-python-application/image20.png)

2. Configure el Sitio web Azure proporcionando sus credenciales y haga clic en **Publicar**.

	![Screen shot of the Publish Web window](./media/documentdb-python-application/image21.png)

3. En pocos segundos, Visual Studio terminará de publicar su aplicación web y ejecutará un explorador donde podrá ver su útil trabajo ejecutándose en Azure.

## Pasos siguientes

¡Enhorabuena! Acaba de compilar su primera aplicación Phyton mediante Microsoft Azure DocumentDB y la ha publicado en Sitios web Azure.

Para agregar funcionalidad adicional a la aplicación, revise las API disponibles en el [SDK de Python de DocumentDB](https://pypi.python.org/pypi/pydocumentdb).

  [Haga clic aquí para tener acceso a tutoriales de Flask]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world
  [Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
  [aquí]: http://aka.ms/ptvs
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [2]: https://www.python.org/downloads/windows/
  [3]: http://aka.ms/vcpython27 
  [Instalador de plataforma web de Microsoft]: http://www.microsoft.com/web/downloads/platform.aspx
  [Portal de administración de Azure]: http://portal.azure.com

<!--HONumber=49-->