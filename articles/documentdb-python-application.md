<properties 
    pageTitle="Generación de una aplicación web con Python y Flask mediante la Base de datos de documentos | Azure" 
    description="Obtenga información acerca de cómo usar la Base de datos de documentos para almacenar y acceder a los datos de una aplicación web de Python y Flask (MVC) hospedada en Azure." 
    services="documentdb" 
    documentationCenter="" 
    authors="crwilcox" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/12/2015" 
    ms.author="crwilcox"/>

# Generación de una aplicación web con Python y Flask (MVC) mediante la Base de datos de documentos
------------------------------------------------------------------------

Para destacar la forma en que los clientes pueden servirse de Base de datos de documentos de Azure para almacenar y consultar documentos JSON, este documento proporciona un tutorial integral sobre la compilación de una aplicación web para votaciones mediante Base de datos de documentos de Azure.

En este tutorial aprenderá a utilizar el servicio de Base de datos de documentos proporcionado por Azure para almacenar datos y acceder a ellos desde cualquier aplicación web Phyton hospedada en Azure. En él se presupone que tiene experiencia previa en el uso de los sitios web de Phyton y Azure.

En este tutorial, se describen los siguientes procedimientos:

1. Crear y aprovisionar una cuenta de la Base de datos de documentos

2. Crear una aplicación Phyton MVC

3. Conectarse y utilizar la Base de datos de documentos desde su aplicación web

4. Implementar la aplicación web en Sitios web de Azure

Siguiendo este tutorial, podrá compilar una aplicación de
votación simple que le permita votar en un sondeo.

![Alt text](./media/documentdb-python-application/image1.png)


## Requisitos previos

Antes de seguir las instrucciones del presente artículo, debe asegurarse de tener instalados los siguientes elementos:

- Visual Studio 2013 (o [Visual Studio Express][], que es la versión gratuita)

- Python Tools para Visual Studio desde [aquí][]

- Azure SDK para Visual Studio 2013, versión 2.4 o superior disponible desde [aquí][1]

- Python 2.7 desde [aquí][2]

- Compilador de Microsoft Visual C++ para Python 2.7 desde [aquí][3]

## Paso 1: Creación de una cuenta de base de datos de la Base de datos de documentos

Para aprovisionar una cuenta de base de datos de Base de datos de documentos en Azure, abra el [Portal de administración de Azure][] y haga clic en el mosaico de la Galería de Azure de la página de inicio. También puede hacer clic en "+" en la esquina inferior izquierda de la pantalla.

![Alt text](./media/documentdb-python-application/image2.png)


Esto abrirá Azure Marketplace, donde podrá elegir entre muchos servicios de Azure disponibles. En Marketplace, elija "Datos y análisis" en la lista de categorías.

![Alt text](./media/documentdb-python-application/image3.png)

Desde aquí, busque Base de datos de documentos y elija la opción correspondiente.

![Alt text](./media/documentdb-python-application/image4.png)

A continuación, seleccione "Crear" en la parte inferior de la pantalla

![Alt text](./media/documentdb-python-application/image5.png)

De esta manera, se abrirá la hoja "Nueva base de datos de documentos", donde se puede especificar el nombre, la región, la escala, el grupo de recursos y otros ajustes para la cuenta nueva.

![Alt text](./media/documentdb-python-application/image6.png)

Una vez que haya terminado de suministrar los valores para la cuenta, haga clic en "Crear" y el proceso de aprovisionamiento comenzará a crear la cuenta de la base de datos.
Cuando se haya completado el proceso de aprovisionamiento, debe aparecer una notificación en el área notificaciones del portal. El icono de la pantalla de inicio (si ha elegido crear uno) cambiará para mostrar la acción completada.

![Alt text](./media/documentdb-python-application/image7.png)

Cuando se haya completado el aprovisionamiento, al hacer clic en el mosaico de Base de datos de documentos de la pantalla de inicio, se abrirá la hoja principal para la cuenta de Base de datos de documentos recién creada.

![Alt text](./media/documentdb-python-application/image8.png)

Mediante el botón "Claves", acceda a su URL de extremo y a la clave principal. Cópielas en el portapapeles y téngalas a mano, ya que utilizaremos estos valores en la aplicación web que crearemos a continuación.


## Paso 2: Creación de una nueva aplicación web de Phyton Flask

Abra Visual Studio, Archivo -\> Nuevo proyecto -\> Python -\>, Proyecto web Flask llamado **tutorial**. 

Para aquellos que desconozcan Flask: se trata de un marco web que nos ayuda a crear aplicaciones web en Python más rápido. [Haga clic aquí para tener acceso a tutoriales de Flask][].

![Alt text](./media/documentdb-python-application/image9.png)

Se le preguntará si desea instalar paquetes externos. Haga clic en **Instalar en un entorno virtual**. Use Python 2.7 como el entorno de base, ya que PyDocumentDB no admite actualmente Python 3.x.  Esto configurará el entorno virtual de Python requerido para su proyecto.

![Alt text](./media/documentdb-python-application/image10.png)


## Paso 3: Modificación de la aplicación web de Python Flask


### Adición de paquetes flask a su proyecto

Una vez que el proyecto esté configurado, deberá agregar ciertos paquetes Flask necesarios para nuestro proyecto, incluido pydocumentdb, el paquete de python para Base de datos de documentos. Abra el archivo denominado **requirements.txt** y reemplace el contenido por lo siguiente:

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

Haga clic con el botón derecho en **env** y, a continuación, en **install from requirements.txt**.

![Alt text](./media/documentdb-python-application/image11.png)

**Nota:** en pocas ocasiones se mostrará un error en la ventana de salida. Si esto ocurre, compruebe si el error está relacionado con la limpieza. En ocasiones, se puede producir un error de limpieza, pero la instalación será correcta (desplácese hacia arriba en la ventana de resultados para comprobarlo).
<a name="verify-the-virtual-environment"></a> Si es el caso, puede continuar.


### Comprobación del entorno virtual

Asegurémonos de que todo esté correctamente instalado. Inicie el sitio web haciendo clic en **F5**. De este modo se ejecutará el servidor de desarrollo flask y el explorador web. Verá la siguiente página:

![Alt text](./media/documentdb-python-application/image12.png)

### Creación de definición de base de datos, colección y documento

Haga clic con el botón derecho para agregar un archivo Python en la carpeta denominada **tutorial** en el Explorador de soluciones.  Asigne al archivo el nombre **forms.py**.  Estamos creando nuestra aplicación de sondeo.

    from flask.ext.wtf import Form
    from wtforms import RadioField

    class VoteForm(Form):
        deploy_preference  = RadioField('Deployment Preference', choices=[
            ('Web Site', 'Web Site'),
            ('Cloud Service', 'Cloud Service'),
            ('Virtual Machine', 'Virtual Machine')], default='Web Site')

### Agregue las importaciones necesarias a views.py.

Agregue las siguientes instrucciones import a la parte superior de **views.py**. Esto importará los paquetes de PythonSDK y Flask de Base de datos de documentos.


    from forms import VoteForm
    import config
    import pydocumentdb.document_client as document_client
    

### Creación de base de datos, colección y documento

Agregue el siguiente código a **views.py**. Esta acción se encarga de crear la base de datos que usa el formulario. No elimine el código existente en **views.py**. Solo tiene que incluir esto al final.

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

Agregue el siguiente código a **views.py**. Esta acción se encarga de configurar el formulario, leyendo la base de datos, la colección y el documento. No elimine el código existente en **views.py**. Solo tiene que incluir esto al final.
    
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

En la carpeta de plantillas, agregue los siguientes archivos html: create.html, results.html y vote.html

Agregue el siguiente código a **create.html**. Esta acción se encarga de mostrar el mensaje de que hemos creado una nueva base de datos, una colección y un documento.

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}

Agregue el siguiente código a **results.html**. Se encarga de mostrar los resultados del sondeo.

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

Agregue el siguiente código a **vote.html**. Se encarga de mostrar los resultados y aceptar los votos. Al registrar los votos, el control se pasa sobre views.py donde reconoceremos el voto emitido y se anexará al documento de forma correspondiente.

    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
        {{form.deploy_preference}}
        <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}

Reemplace el contenido de **index.html** por lo siguiente. Esto sirve como la página de aterrizaje de la aplicación.

    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + DocumentDB Voting Application.</h2>
    <h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}


### Adición de un archivo de configuración y modificación de \_\_init\_\_.py

Haga clic con el botón derecho en el tutorial del nombre del proyecto y agregue el archivo **config.py**.
Esta configuración es necesaria para los formularios en flask. También puede utilizarlo para proporcionar una clave secreta. Sin embargo, esto no es necesario para este tutorial. Agregue el siguiente código a config.py.   Modifique los valores de **DOCUMENTDB\_HOST** y **DOCUMENTDB\_KEY**.

    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    
    DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
    
    DOCUMENTDB_DATABASE = 'voting database'
    DOCUMENTDB_COLLECTION = 'voting collection'
    DOCUMENTDB_DOCUMENT = 'voting document'


Del mismo modo, sustituya el contenido de **\_\_init\_\_.py** por lo siguiente.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

Tras seguir los pasos mencionados anteriormente, este es el aspecto que debería tener
su explorador de soluciones.

![Alt text](./media/documentdb-python-application/image15.png)


## Paso 4: Ejecución de la aplicación de forma local

Presione F5 o el botón de ejecución en Visual Studio. Debería ver lo siguiente en la pantalla.

![Alt text](./media/documentdb-python-application/image16.png)

Haga clic en "Crear/borrar la base de datos de votos" para generar la base de datos.

![Alt text](./media/documentdb-python-application/image17.png)

A continuación, haga clic en "Votar" y elija su opción.

![Alt text](./media/documentdb-python-application/image18.png)

Para cada voto que emita, se incrementará el contador correspondiente.

![Alt text](./media/documentdb-python-application/image19.png)


## Paso 5: Implementación de la aplicación en Sitios web de Azure

Ahora que tiene la aplicación completa funcionando correctamente con Base de datos de documentos, vamos a implementar esto en sitios web Azure. Haga clic con el botón derecho en el Proyecto del Explorador de soluciones (asegúrese de que no se esté ejecutando de forma local) y elija Publicar.  A continuación, elija Sitios web Microsoft Azure.

![Alt text](./media/documentdb-python-application/image20.png)


Configure su Sitio web Azure proporcionando sus credenciales. Haga clic en Publicar.

![Alt text](./media/documentdb-python-application/image21.png)

En pocos segundos, Visual Studio terminará de publicar su aplicación web y ejecutará un explorador donde podrá ver su útil trabajo ejecutándose en Azure.


## Pasos siguientes

¡Enhorabuena! Acaba de generar su primera aplicación Phyton mediante Base de datos de documentos de Azure y la ha publicado en los sitios web de Azure.


  [Haga clic aquí para tener acceso a tutoriales de Flask]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

  [Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
  [aquí]: http://aka.ms/ptvs
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [2]: https://www.python.org/downloads/windows/
  [3]: http://aka.ms/vcpython27 
  [Instalador de plataforma web de Microsoft]: http://www.microsoft.com/web/downloads/platform.aspx
  [Portal de administración de Azure]: http://portal.azure.com
<!--HONumber=47-->
