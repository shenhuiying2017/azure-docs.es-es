<properties title="Build a web application with Python and Flask (MVC) using DocumentDB" pageTitle="Build a web app with Python and Flask using DocumentDB | Azure" description="Learn how to use DocumentDB to store and access data from an Python and Flask (MVC) web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="hawong" />

# <a name="_Toc395888515"></a><a name="_Toc395809324">Generación de una aplicación web con Python y Flask (MVC) mediante la Base de datos de documentos</a>

<a name="_Toc395809324">

# <a name="_Toc395888516"></a><a name="_Toc395809325"></a><a name="_Toc389865467"></a><a name="_Toc389828008">Información general</a>

## <a name="_Toc395888517"></a><a name="_Toc395809326">Escenario</a>

Para resaltar cómo los clientes pueden aprovechar la Base de datos de documentos de Azure para
almacenar y consultar documentos JSON, este documento proporciona un tutorial
integral sobre la generación de una aplicación web para votaciones mediante la Base de datos de documentos de
Azure.

En este tutorial aprenderá a utilizar el servicio de Base de datos de documentos proporcionado por
Azure para almacenar y acceder a datos desde cualquier aplicación web Phyton hospedada en
Azure y presupone que tiene experiencia previa en el uso de los sitios web de Phyton y
Azure.

Aprenderá a:

1. Crear y aprovisionar una cuenta de la Base de datos de documentos

2. Crear una aplicación Phyton MVC

3. Conectarse y utilizar la Base de datos de documentos desde su aplicación web

4. Implementar la aplicación web en Sitios web de Azure

Siguiendo este tutorial, podrá compilar una aplicación
de votación sencilla que le permita votar en un sondeo.

![Texto alternativo][Texto alternativo]

<a name="_Toc395888520"></a><a name="_Toc395809329">Requisitos previos</a>

Antes de seguir las instrucciones del presente artículo, debe asegurarse
de tener instalados los siguientes elementos:

Visual Studio 2013 (o [Visual Studio Express][Visual Studio Express] que es la versión
gratuita)

Python Tools para Visual Studio desde [aquí][aquí]

Azure SDK para Visual Studio 2013, versión 2.4 o superior disponible desde
[aquí][1]

Herramientas de línea de comandos multiplataforma de Azure, disponible a través del [instalador de
plataforma web de Microsoft][instalador de
plataforma web de Microsoft]

# <a name="_Toc395888519"></a><a name="_Toc395809328">Creación de una cuenta de base de datos de la Base de datos de documentos</a>

Para aprovisionar una cuenta de base de datos de la Base de datos de documentos en Azure, abra el Portal
de administración de Azure y haga clic en el mosaico de la Galería de Azure de la
página de inicio o haga clic en "+" en la esquina inferior izquierda de la pantalla.

![Texto alternativo][2]

Esto abrirá la Galería de Azure, donde podrá seleccionar entre muchos
servicios de Azure disponibles. En la Galería, seleccione "Datos, almacenamiento y
copia de seguridad" de la lista de categorías.

![Texto alternativo][3]

Desde aquí, seleccione la opción de la Base de datos de documentos de Azure.

![Texto alternativo][4]

A continuación, seleccione "Crear" en la parte inferior de la pantalla.

![Texto alternativo][5]

Esto abrirá el cuadro "Nueva Base de datos de documentos" donde puede especificar el
nombre, región, escala, grupo de recursos y otras especificaciones de su nueva
cuenta.

![Texto alternativo][6]

Una vez que haya terminado de proporcionar los valores de la cuenta, haga clic en "Crear"
y el proceso de aprovisionamiento comenzará a crear su cuenta de base de datos.
Una vez que se haya completado el proceso de aprovisionamiento debería ver una notificación
en el área de notificaciones del portal y el icono de su
pantalla de inicio (si ha seleccionado crear uno) cambiará para mostrar la
acción completada.

![Texto alternativo][7]

Una vez que se haya completado el aprovisionamiento, al hacer clic en el icono de la Base de datos de documentos de la
pantalla inicio, se abrirá el cuadro principal para la cuenta de la
Base de datos de documentos recién creada.

![Texto alternativo][8]
![Texto alternativo][9]

Mediante el botón "Claves", obtiene acceso a su URL de extremo y a la clave principal,
cópielas en el portapapeles y consérvelas a mano ya que utilizaremos estos
valores en la aplicación web que crearemos a continuación.

</a>

# <a name="_Toc395888520"></a><a name="_Toc395809329">Creación de una nueva aplicación web de Phyton Flask</a>

Abra Visual Studio, Archivo -\> Nuevo proyecto -\> Python -\>, Proyecto web
Flask llamado **tutorial**. Se le preguntará si desea
instalar paquetes externos. Haga clic en Instalar en un entorno virtual
y, a continuación, haga clic en Crear. Esto configurará el entorno virtual de Python
requerido para su proyecto.

Para aquellos nuevos en Flask, se trata de un marco web que nos ayuda a crear aplicaciones
web en Python más rápido. [Haga clic aquí para obtener acceso a los tutoriales de Flask][Haga clic aquí para obtener acceso a los tutoriales de Flask].

![Texto alternativo][10]

# <a name="_Toc395888520"></a><a name="_Toc395809329">Adición de paquetes flask a su proyecto</a>

Una vez que haya configurado el proyecto, debe agregar determinados paquetes flask que
serán necesarios para nuestro proyecto; como formularios. Haga clic con el botón derecho en **env**
y en **Instalar los siguientes paquetes Python (seguir esta orden es
importante)**:

    flask==0.9
    flask-login
    flask-openid
    flask-mail==0.7.6
    sqlalchemy==0.7.9
    flask-sqlalchemy==0.16
    sqlalchemy-migrate==0.7.2
    flask-whooshalchemy==0.55a
    flask-wtf==0.8.4
    pytz==2013b
    flask-babel==0.8
    flup

![Texto alternativo][11]

**Nota:** en casos excepcionales, es posible que aparezca un error en la ventana de resultados. Si
esto ocurre, compruebe si el error está relacionado con la limpieza. En ocasiones la
limpieza da error, pero la instalación se realiza correctamente (desplácese hacia arriba
en la ventana de resultados para comprobarlo).
<a name="verify-the-virtual-environment"></a> Si esto ocurre, puede
continuar.

</h1>
# <a name="_Toc395888522"></a><a name="_Toc395809331">Comprobación del entorno virtual</a>

Asegurémonos de que todo esté instalado correctamente. Inicie el sitio
web haciendo clic en **F5**. De este modo se ejecutará el servidor de desarrollo flask
y el explorador web. Verá la siguiente página:

![Texto alternativo][12]

# <a name="_Toc395888523"></a><a name="_Toc395809332">Adición de una Base de datos de documentos a su proyecto</a>

El SDK de Python de la Base de datos de documentos está hospedado en PyPi y se puede instalar con
pip.

Amplíe el nodo de Entornos Python en el Explorador de soluciones, haga clic con el botón derecho en
su entorno y seleccione "Instalar paquete Python…".

![Texto alternativo][13]

Inserte "--pre pydocumentdb" que es el nombre del paquete PyPi. También puede
proporcionar una versión conocida si desea controlar qué versión le
gustaría instalar, dejándolo sin versión se asegura de que se instala
la última versión estable. Tenga en cuenta que es posible que tenga que especificar
todo el nombre "--pre pydocumentdb".

![Texto alternativo][14]

Esto descargará e instalará el paquete de pydocumentdb en su
entorno, una vez completado debería ver pydocumentdb en la lista como un
módulo de su entorno.

</h1>
# <a name="_Toc395888524"></a><a name="_Toc395809333">Creación de definición de base de datos, colección y documento</a>

Agregue lo siguiente a un archivo de python – **forms.py** y agréguelo a la carpeta
llamada tutorial en su Explorador de soluciones. Agregue el código siguiente a
forms.py. Estamos creando nuestra aplicación de sondeo. Esto se hace
creando tres campos booleanos que se alternarán dependiendo de lo que especifique el usuario.

</h1>
    #forms.py
    from flask.ext.wtf import Form
    from wtforms import TextField, BooleanField
    from wtforms.validators import Required
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors
    class LoginForm(Form):
        openid = TextField('openid')
        remember_me = BooleanField('remember_me', default = False)
        remember_me1 = BooleanField('remember_me1', default = False)
        remember_me2 = BooleanField('remember_me2', default = False)

# <a name="_Toc395888520"></a>

### <a name="_Toc395809338">Creación de base de datos, colección y documento</a>

\#Nota: se considera más seguro mantener sus credenciales de autenticación
en un archivo de configuración en lugar de en la aplicación. Para que sea más sencillo, lo estamos
colocando en código fuente. Cree una nueva función en views.py y llámela
crear. Anexe lo siguiente a**views.py**. No elimine ningún código
existente.

</h1>
    @app.route('/create')
    def create():
        """Renders the contact page."""
        host = 'https://meetdemo.documents.azure.com:443/'
        masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
        client = document_client.DocumentClient(host, {'masterKey': masterKey})
        databases = client.ReadDatabases().ToArray()
        #delete any existing databases for simplicity
        for database in databases:
            client.DeleteDatabase(database['_self'])
        #create database
        db = client.CreateDatabase({ 'id': 'sample database' })
        # create collection
        collection = client.CreateCollection(db['_self'],{ 'id': 'sample collection' })
        # create document
        document = client.CreateDocument(collection['_self'],
        { 'id': 'sample document',
        'Web Site': '0 votes',
        'Cloud Service': '0 votes',
        'Virtual Machine': '0 votes',
        'name': 'sample document' })
        return render_template('create.html',title='Create Page',year=datetime.now().year,
        message='You just created a new database - sample database a collection - sample collection and a document - sample document that has your votes. Your old votes have been deleted')

# <a name="_Toc395888529"></a><a name="_Toc395809338">Aceptación de las votaciones de los usuarios y actualización del documento</a>

### <a name="_Toc395809338">Adición de las importaciones requeridas

# <a name="_Toc395888529"></a>

Agregue las siguientes instrucciones import a la parte superior de **views.py**. Esto
importará los paquetes de PythonSDK y Flask de la Base de datos de documentos.

    from wtforms import Form, BooleanField, TextField, PasswordField, validators
    from forms import LoginForm
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors

### <a name="_Toc395809338">Lectura de base de datos y colección y documento</a>

Agregue el siguiente código a **views.py**. Esto se encarga de configurar
el formulario, leyendo la base de datos, colección y documento. No elimine
ningún código existente en views.py. Simplemente anéxelo al final.

    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = LoginForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            host = 'https://meetdemo.documents.azure.com:443/'
            masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
            client = document_client.DocumentClient(host, {'masterKey': masterKey})
            databases = client.ReadDatabases().ToArray()   #Read database
            db =databases[0] #For simplicity we are assuming there is only one database
            collections = client.ReadCollections(db['_self']).ToArray() #Read collection
            coll =collections[0] #For simplicity we are assuming there is only one collection
            documents = client.ReadDocuments(coll['_self']).ToArray() #Read document
            doc = documents[0]   #For simplicity we are assuming there is only document

### <a name="_Toc395809338">Registro del voto y modificación del documento</a>

            replaced_document = doc
            if form.remember_me.data:
                print 'choice 1'             
                setvar = doc['Web Site']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Web Site'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
                print replaced_document
            elif form.remember_me1.data:
                print 'choice 2'             
                setvar = doc['Cloud Service']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Cloud Service'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
            else:
                print 'choice 2'             
                setvar = doc['Virtual Machine']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Virtual Machine'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)

### <a name="_Toc395809338">Visualización de los resultados</a>

    return render_template('results.html', 
            title = 'Website = ' + replaced_document['Web Site'] + '\n' +'Cloud Service = ' + replaced_document['Cloud Service'] + '\n' + 'Virtual Machine = ' + replaced_document['Virtual Machine'])
        else :        
            return render_template('vote.html', 
            title = 'Vote',
            form = form,
            providers = app.config['OPENID_PROVIDERS'])

    @app.route('/results')
    def results():
        """Renders the results page."""
        return render_template(
            'results.html',
            title='Results',
            year=datetime.now().year,
            message='Your application description page.')

### <a name="_Toc395809338">Creación de los archivos html</a>

En la carpeta plantillas, agregue los siguientes archivos html.
create.html, results.html, vote.html

Agregue el siguiente código a **create.html**. Esto se encarga de mostrar
el mensaje de que hemos creado una nueva base de datos, colección y documento.

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Click here to Vote &raquo;</a></p>
    {% endblock %}

Agregue el siguiente código a **results.html**. Se encarga de mostrar
los resultados del sondeo.

    {% extends "layout.html" %}
    {% block content %}
    <h3>Results of the vote</h3>
    <pre>{{ title }}</pre>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote Again &raquo;</a></p>
    {% endblock %}

Agregue el siguiente código a **vote.html**. Se encarga de mostrar los
resultados y aceptar los votos. Al registrar los votos, el control se pasa
sobre views.py donde reconoceremos el voto emitido y
se anexará al documento de forma correspondiente.

    {% extends "layout.html" %}
    {% block content %}
    <h1>What is your favorite way to host an application on Azure?</h1>
    <form action="" method="post" name="login">
    {{form.hidden_tag()}}
    <p> </p>
        {{form.remember_me}} Website
        {{form.remember_me1}} Cloud
        {{form.remember_me2}} Virtual Machine
        <p><input type="submit" value="Cast your vote"></p>
    </form>
    {% endblock %}

# <a name="_Toc395888529"></a>

Elimine el código de **layout.html** y sustitúyalo por el siguiente. Se
encarga de configurar la barra de exploración y las URL respectivas para el enrutamiento

Elimine el código de index.html y sustitúyalo por el siguiente. Esto
sirve como la página de aterrizaje de la aplicación

</h1>
## <a name="_Toc395888532"></a><a name="_Toc395809341">Adición de un archivo de configuración y modificación del \_\_init\_\_.py</a>.

Haga clic con el botón derecho en el tutorial del nombre del proyecto y agregue un archivo – **config.py**.
Esta configuración es necesaria para los formularios en flask. También puede utilizarlo para proporcionar una
clave secreta. Agregue el siguiente código a config.py

    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    OPENID_PROVIDERS = [
       ]

Del mismo modo, anexe el **archivo \_\_init\_\_.py**. Colóquelo en cada
carpeta llamada tutorial. Reemplace el código original por el siguiente. Se
encarga de la conexión entre las vistas y del archivo config.py.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

Tras seguir los pasos antes mencionados, este es el aspecto que debería tener su explorador
de soluciones.

![Texto alternativo][15]

# <a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Ejecución de la aplicación de forma local</a>

Pulse F5 o el botón Run en Visual Studio y debería ver lo
siguiente en la pantalla.

![Texto alternativo][16]

Haga clic en votar y seleccione su opción.

![Texto alternativo][17]

Por cada voto que emita, se incrementará el contador correspondiente. Cuando
vote la próxima vez, podrá ver los resultados.

![Texto alternativo][18]

</h1>
# <a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Implementación de la aplicación en Sitios web de Azure</a>

Ahora que ya tiene la aplicación completa funcionando correctamente con la
Base de datos de documentos, vamos a implementarla en los sitios web de Azure. Haga clic con el botón derecho en
el Proyecto del Explorador de soluciones (asegúrese de que no se está ejecutando de forma
local) y seleccione Publicar.

![Texto alternativo][19]

Configure su sitio web de Azure proporcionando sus credenciales y creando
un nuevo sitio web o reutilizando uno antiguo.

![Texto alternativo][20]

En pocos segundos, Visual Studio terminará de publicar su aplicación
web y ejecutará un explorador donde podrá ver su útil trabajo
ejecutándose en Azure.

</h1>
# <a name="_Toc395809338">Conclusión</a>

¡Enhorabuena! Acaba de generar su primera aplicación Phyton mediante
la Base de datos de documentos de Azure y publicarla en los sitios web de Azure.

Si desea la solución completa, [haga clic aquí][haga clic aquí]. (Nota: aún
tiene que agregar el entorno virtual, instalar las herramientas y paquetes
de python, como se indicaba anteriormente).

</h1>

  [Texto alternativo]: ./media/documentdb-python-application/image1.png
  [Visual Studio Express]: http://www.visualstudio.com/es-es/products/visual-studio-express-vs.aspx
  [aquí]: https://pytools.codeplex.com/releases/view/123624
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [2]: ./media/documentdb-python-application/image2.png
  [3]: ./media/documentdb-python-application/image3.png
  [4]: ./media/documentdb-python-application/image4.png
  [5]: ./media/documentdb-python-application/image5.png
  [6]: ./media/documentdb-python-application/image6.png
  [7]: ./media/documentdb-python-application/image7.png
  [8]: ./media/documentdb-python-application/image8.png
  [9]: ./media/documentdb-python-application/image9.png
  [Haga clic aquí para obtener acceso a los tutoriales de Flask]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world
  [10]: ./media/documentdb-python-application/image10.png
  [11]: ./media/documentdb-python-application/image11.png
  [12]: ./media/documentdb-python-application/image12.png
  [13]: ./media/documentdb-python-application/image13.png
  [14]: ./media/documentdb-python-application/image14.png
  [15]: ./media/documentdb-python-application/image15.png
  [16]: ./media/documentdb-python-application/image16.png
  [17]: ./media/documentdb-python-application/image17.png
  [18]: ./media/documentdb-python-application/image18.png
  [19]: ./media/documentdb-python-application/image19.png
  [20]: ./media/documentdb-python-application/image20.png
  [haga clic aquí]: http://go.microsoft.com/fwlink/?LinkID=509840&clcid=0x409
