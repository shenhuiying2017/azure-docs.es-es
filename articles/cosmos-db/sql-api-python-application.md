---
title: Tutorial sobre aplicaciones web de Python Flask para Azure Cosmos DB | Microsoft Docs
description: "Vea un tutorial de base de datos sobre el uso de Azure cosmos DB para almacenar datos y acceder a ellos desde una aplicación web de Python Flask hospedada en Azure. Encuentre soluciones de desarrollo de aplicaciones."
keywords: "Desarrollo de aplicaciones, python flask, aplicación web de python, desarrollo web de python"
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 20ebec18-67c2-4988-a760-be7c30cfb745
ms.service: cosmos-db
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 10/17/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b83e096cbb677653db8a13b6b7c04e6c705fd2f3
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="build-a-python-flask-web-application-using-azure-cosmos-db"></a>Compilación de una aplicación web Node.js de Python Flask mediante Azure cosmos DB
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

En este tutorial aprenderá a usar Azure Cosmos DB para almacenar datos de una aplicación web de Python Flask hospedada en Azure App Service y acceder a ellos. En este tutorial se da por supuesto que tiene cierta experiencia previa con Python y Azure Websites.

En este tutorial de base de datos se trata lo siguiente:

1. Creación y aprovisionamiento de una cuenta de Azure Cosmos DB.
2. Crear una aplicación Python Flask.
3. Conexión y uso de Azure Cosmos DB desde la aplicación web.
4. Implementación de la aplicación web en Azure App Service.

Siguiendo este tutorial, podrá compilar una aplicación de votación simple que le permita votar en un sondeo.

![Captura de pantalla de la aplicación de votación creada con este tutorial](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)

## <a name="database-tutorial-prerequisites"></a>Requisitos previos del tutorial de base de datos
Antes de seguir las instrucciones del presente artículo, debe asegurarse de tener instalados los siguientes elementos:

* [Una suscripción de Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Visual Studio de 2017](https://www.visualstudio.com/downloads/) con **desarrollo de Azure** y **desarrollo de Python** habilitado. Puede comprobar si estos requisitos previos están instalados y, si es necesario, instalarlos abriendo el **Instalador de Visual Studio** localmente.   
* [SDK de Microsoft Azure para Python 2.7](https://azure.microsoft.com/downloads/) 
* [Python 2.7](https://www.python.org/downloads/windows/). Puede usar la instalación de 32 bits o la de 64 bits.

> [!IMPORTANT]
> Si va a instalar Python 2.7 por primera vez, asegúrese de que en la pantalla Personalizar Python 2.7.13 selecciona **Agregar python.exe a la ruta de acceso**.
> 
> ![Captura de pantalla de la pantalla Personalizar Python 2.7.11, donde debe seleccionar Agregar python.exe a la ruta de acceso](./media/sql-api-python-application/cosmos-db-python-install.png)
> 
> 

* [Compilador de Microsoft Visual C++ 2013 para Python 2.7](https://www.microsoft.com/en-us/download/details.aspx?id=44266)

## <a name="step-1-create-an-azure-cosmos-db-database-account"></a>Paso 1: Creación de una cuenta de base de datos de Azure Cosmos DB
Para comenzar, creemos una cuenta de Azure Cosmos DB. Si ya tiene una cuenta o si usa el Emulador de Azure Cosmos DB en este tutorial, puede ir directamente al [Paso 2: Creación de una nueva aplicación web de Python Flask](#step-2-create-a-new-python-flask-web-application).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<br/>
Ahora veremos cómo crear una nueva aplicación web de Python Flask partiendo de cero.

## <a name="step-2-create-a-new-python-flask-web-application"></a>Paso 2: Creación de una nueva aplicación web de Python Flask
1. En Visual Studio, en el menú **Archivo**, seleccione **Nuevo** y luego haga clic en **Proyecto**.
   
    Aparecerá el cuadro de diálogo **Nuevo proyecto** .
2. En el panel izquierdo, expanda **Plantillas**, **Python** y luego haga clic en **Web**. 
3. Seleccione **Proyecto web Flask** en el panel central; después en el cuadro **Nombre** escriba **tutorial** y, finalmente, haga clic en **Aceptar**. Recuerde que los nombres de paquete de Python deben escribirse en minúsculas, tal como se describe en [Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/#package-and-module-names).
   
    Para aquellos que desconozcan Python Flask, se trata de un marco de desarrollo de aplicaciones web que ayuda a compilar aplicaciones web en Python más rápidamente.
   
    ![Captura de pantalla de la ventana Nuevo proyecto en Visual Studio con Python resaltado a la izquierda, el proyecto web de Python Flask seleccionado en el centro y el nombre tutorial en el cuadro Nombre](./media/sql-api-python-application/image9.png)
4. En la ventana **Herramientas de Python para Visual Studio**, haga clic en **Instalar en un entorno virtual**. 
   
    ![Captura de pantalla del tutorial de base de datos: ventana Herramientas de Python para Visual Studio](./media/sql-api-python-application/python-install-virtual-environment.png)
5. En la ventana **Agregar entorno virtual**, seleccione Python 2.7 o Python 3.5, en el cuadro Select an interpreter (Seleccionar un intérprete), acepte los demás valores predeterminados y, finalmente, haga clic en **Crear**. Esto configura el entorno virtual de Python requerido para el proyecto.
   
    ![Captura de pantalla del tutorial de base de datos: ventana Herramientas de Python para Visual Studio](./media/sql-api-python-application/image10_A.png)
   
    Muestra la ventana de salida `Successfully installed Flask-0.10.1 Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.5 itsdangerous-0.24 'requirements.txt' was installed successfully.` cuando el entorno está instalado correctamente.

## <a name="step-3-modify-the-python-flask-web-application"></a>Paso 3: Modificación de la aplicación web de Python Flask
### <a name="add-the-python-flask-packages-to-your-project"></a>Adición de paquetes de Python Flask al proyecto
Después de que el proyecto esté configurado, es preciso agregar los paquetes Flask necesarios al proyecto, incluido pydocumentdb, el paquete de Python para la API SQL de Azure Cosmos DB.

1. En el Explorador de soluciones, abra el archivo llamado **requirements.txt** y reemplace su contenido por lo siguiente:
   
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
        pydocumentdb>=1.0.0
2. Guarde el archivo **requirements.txt** . 
3. En el Explorador de soluciones, haga clic con el botón derecho en **env** y haga clic en **Install from requirements.txt**.
   
    ![Captura de pantalla que muestra env (Python 2.7) seleccionado con Instalación desde requirements.txt resaltado en la lista](./media/sql-api-python-application/cosmos-db-python-install-from-requirements.png)
   
    Después de una instalación correcta, la ventana de salidas muestra lo siguiente:
   
        Successfully installed Babel-2.3.2 Tempita-0.5.2 WTForms-2.1 Whoosh-2.7.4 blinker-1.4 decorator-4.0.9 flask-0.9 flask-babel-0.8 flask-mail-0.7.6 flask-sqlalchemy-0.16 flask-whooshalchemy-0.55a0 flask-wtf-0.8.4 flup-1.0.2 pydocumentdb-1.6.1 pytz-2013b0 speaklater-1.3 sqlalchemy-0.7.9 sqlalchemy-migrate-0.7.2
   
   > [!NOTE]
   > En casos excepcionales, es posible que aparezca un error en la ventana de salida. De ser así, compruebe si el error está relacionado con la limpieza. En ocasiones, se produce un error en la limpieza, pero la instalación se realiza correctamente (desplácese hacia arriba en la ventana de salida para comprobarlo). Puede comprobar la instalación mediante la [comprobación del entorno virtual](#verify-the-virtual-environment). Si se produjo un error en la instalación, pero la comprobación es correcta, se puede continuar sin problemas.
   > 
   > 

### <a name="verify-the-virtual-environment"></a>Comprobación del entorno virtual
Asegurémonos de que todo esté instalado correctamente.

1. Copile la solución presionando **Ctrl**+**Mayús**+**B**.
2. Una vez realizada correctamente la compilación, inicie el sitio web presionando **F5**. De este modo se iniciará el servidor de desarrollo de Flask y el explorador web. Debe ver la página siguiente.
   
    ![Proyecto de desarrollo web de Python Flask vacío en un explorador](./media/sql-api-python-application/image12.png)
3. Detenga la depuración del sitio web presionando **Mayús**+**F5** en Visual Studio.

### <a name="create-database-collection-and-document-definitions"></a>Creación de definiciones de base de datos, colección y documento
Ahora vamos a crear la aplicación de votación mediante la adición de archivos nuevos y de la actualización de otros.

1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto de **tutorial**, a continuación haga clic en **Agregar** y, por último, en **Nuevo elemento**. Seleccione **Archivo Python vacío** y asigne el nombre **forms.py** al archivo.  
2. Agregue el código siguiente al archivo forms.py y, después, guárdelo.

```python
from flask.ext.wtf import Form
from wtforms import RadioField

class VoteForm(Form):
    deploy_preference  = RadioField('Deployment Preference', choices=[
        ('Web Site', 'Web Site'),
        ('Cloud Service', 'Cloud Service'),
        ('Virtual Machine', 'Virtual Machine')], default='Web Site')
```


### <a name="add-the-required-imports-to-viewspy"></a>Agregue las importaciones necesarias a views.py.
1. En el Explorador de soluciones, expanda la carpeta **tutorial** y abra el archivo **views.py**. 
2. Agregue las siguientes instrucciones de importación a la parte superior del archivo **views.py** y, después, guarde el archivo. Con estas instrucciones se importarán los paquetes de PythonSDK y Flask de Azure Cosmos DB.
   
    ```python
    from forms import VoteForm
    import config
    import pydocumentdb.document_client as document_client
    ```

### <a name="create-database-collection-and-document"></a>Creación de base de datos, colección y documento
* Todavía en **views.py**, agregue el siguiente código al final del archivo. Esta acción se encarga de crear la base de datos que usa el formulario. No elimine el código existente en **views.py**. Solo tiene que incluir esto al final.

```python
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
          'name': config.DOCUMENTDB_DOCUMENT 
        })

    return render_template(
       'create.html',
        title='Create Page',
        year=datetime.now().year,
        message='You just created a new database, collection, and document.  Your old votes have been deleted')
```


### <a name="read-database-collection-document-and-submit-form"></a>Lectura de la base de datos, la colección y el documento, y envío del formulario
* Todavía en **views.py**, agregue el siguiente código al final del archivo. Esta acción se encarga de configurar el formulario mediante la lectura de la base de datos, la colección y el documento. No elimine el código existente en **views.py**. Solo tiene que incluir esto al final.

```python
@app.route('/vote', methods=['GET', 'POST'])
def vote(): 
    form = VoteForm()
    replaced_document ={}
    if form.validate_on_submit(): # is user submitted vote  
        client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})

        # Read databases and take first since id should not be duplicated.
        db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))

        # Read collections and take first since id should not be duplicated.
        coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.COSMOSDB_COLLECTION))

        # Read documents and take first since id should not be duplicated.
        doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.COSMOSDB_DOCUMENT))

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
```


### <a name="create-the-html-files"></a>Creación de los archivos HTML
1. En el Explorador de soluciones, en la carpeta **tutorial**, haga clic con el botón derecho en la carpeta **templates**, haga clic en **Agregar** y, después, haga clic en **Nuevo elemento**. 
2. Seleccione **Página HTML** y luego en el cuadro **Nombre** escriba create.html. 
3. Repita los pasos 1 y 2 para crear dos archivos HTML adicionales: results.html y vote.html.
4. Agregue el siguiente código a **create.html** in the `<body>` . Se muestra un mensaje que indica que hemos creado una nueva base de datos, colección y documento.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```
5. Agregue el siguiente código a **results.html** en el elemento `<body`>. Se muestran los resultados del sondeo.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
        <br />
   
    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
            <div class="col-sm-5">
                <div class="progress">
                    <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                                {{vote_object.choices[choice]}}
                </div>
            </div>
            </div>
    </div>
    {% endfor %}
   
    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}
    ```
6. Agregue el siguiente código a **vote.html** en el elemento `<body`>. Muestra el sondeo y acepta los votos. Al registrar los votos, el control se pasa a views.py, donde Azure Cosmos DB reconocerá el voto emitido y lo anexará al documento en consecuencia.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
            {{form.deploy_preference}}
            <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}
    ```
7. En la carpeta **templates**, reemplace el contenido de **index.html** por lo siguiente. Esto sirve como la página de aterrizaje de la aplicación.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + Azure Cosmos DB Voting Application.</h2>
    <h3>This is a sample Cosmos DB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```

### <a name="add-a-configuration-file-and-change-the-initpy"></a>Adición de un archivo de configuración y modificación de \_\_init\_\_.py
1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **tutorial**, haga clic en **Agregar**, en **Nuevo elemento**, seleccione **Archivo Python vacío** y, a continuación, asigne al archivo el nombre de **config.py**. Los formularios de Flask requieren este archivo de configuración. También se puede usar para proporcionar una clave secreta. Sin embargo, dicha clave no es necesaria para este tutorial.
2. Agregue el siguiente código a config.py. Deberá modificar los valores de **COSMOSDB\_HOST** y **COSMOSDB\_KEY** en el paso siguiente.
   
    ```python
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
   
    COSMOSDB_HOST = 'https://YOUR_COSMOSDB_NAME.documents.azure.com:443/'
    COSMOSDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
   
    COSMOSDB_DATABASE = 'voting database'
    COSMOSDB_COLLECTION = 'voting collection'
    COSMOSDB_DOCUMENT = 'voting document'
    ```
3. En [Azure Portal](https://portal.azure.com/), haga clic en **Examinar** y en **Cuentas de Azure Cosmos DB** para ir a la página **Claves**; después, haga doble clic en el nombre de la cuenta que desea usar y haga clic en el botón **Claves** del área **Información esencial**. En la página **Claves**, copie el valor del identificador **URI** y péguelo en el archivo **config.py**, como valor de la propiedad **COSMOSDB\_HOST**. 
4. De nuevo en Azure Portal, en la página **Claves**, copie el valor de la **clave principal** o de la **clave secundaria** y péguelo en el archivo **config.py**, como valor de la propiedad **COSMOSDB\_KEY**.
5. En el archivo **\_\_init\_\_.py**, agregue la siguiente línea: 
   
        app.config.from_object('config')
   
    Por lo tanto, el contenido del archivo es:
   
    ```python
    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views
    ```
6. Después de agregar todos los archivos, Explorador de soluciones debe ser similar al siguiente:
   
    ![Captura de pantalla de la ventana del explorador de soluciones de Visual Studio](./media/sql-api-python-application/cosmos-db-python-solution-explorer.png)

## <a name="step-4-run-your-web-application-locally"></a>Paso 4: Ejecución local de la aplicación web
1. Copile la solución presionando **Ctrl**+**Mayús**+**B**.
2. Una vez realizada correctamente la compilación, inicie el sitio web presionando **F5**. Debería ver lo siguiente en la pantalla.
   
    ![Captura de pantalla de la aplicación de voto Python + Azure Cosmos DB mostrada en un explorador web](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)
3. Haga clic en **Create/Clear the Voting Database** (Crear/borrar la base de datos de votos) para generar la base de datos.
   
    ![Captura de pantalla de la página de creación de la aplicación web: detalles sobre el desarrollo](./media/sql-api-python-application/cosmos-db-python-run-create-page.png)
4. A continuación, haga clic en **Vote** (Votar) y seleccione su opción.
   
    ![Captura de pantalla de la aplicación web con una pregunta de votación formulada](./media/sql-api-python-application/cosmos-db-vote.png)
5. Por cada voto que emita se incrementará el contador correspondiente.
   
    ![Captura de pantalla de la página Resultados de la votación mostrada](./media/sql-api-python-application/cosmos-db-voting-results.png)
6. Detenga la depuración del proyecto presionando MAYÚS+F5.

## <a name="step-5-deploy-the-web-application-to-azure"></a>Paso 5: Implementación de la aplicación web en Azure
Ahora que ya tiene la aplicación completa funcionando correctamente en Azure Cosmos DB de forma local, vamos a crear un archivo web.config, actualizaremos los archivos en el servidor para que se correspondan con los del entorno local y, finalmente, veremos la aplicación completa en Azure. Este procedimiento es específico para Visual Studio 2017. Si está utilizando una versión diferente de Visual Studio, consulte [Publicación en Azure App Service](/visualstudio/python/publishing-to-azure.md).

1. En el **Explorador de soluciones** de Visual Studio, haga clic con el botón derecho en el proyecto y seleccione **Agregar > Nuevo elemento...**. En el cuadro de diálogo que aparece, seleccione la plantilla **Azure web.config (Fast CGI)** y seleccione **Aceptar**. Esto crea un archivo `web.config` en la raíz del proyecto. 

2. Modifique la sección `<system.webServer>` en `web.config` para que la ruta de acceso coincida con la instalación de Python. Por ejemplo, para Python 2.7 x64 la entrada debe ser como la siguiente:
    
    ```xml
    <system.webServer>
        <handlers>
            <add name="PythonHandler" path="*" verb="*" modules="FastCgiModule" scriptProcessor="D:\home\Python27\python.exe|D:\home\Python27\wfastcgi.py" resourceType="Unspecified" requireAccess="Script"/>
        </handlers>
    </system.webServer>
    ```

3. Establezca la entrada `WSGI_HANDLER` de `web.config` como `tutorial.app` para que coincida con el nombre del proyecto. 

    ```xml
    <!-- Flask apps only: change the project name to match your app -->
    <add key="WSGI_HANDLER" value="tutorial.app"/>
    ```

4. En el **Explorador de soluciones** de Visual Studio, expanda la carpeta **tutorial**, haga clic con el botón derecho en la carpeta `static`, seleccione **Agregar > Nuevo elemento...**, seleccione la plantilla "Azure static files web.config" y seleccione **Aceptar**. Esta acción crea otro archivo `web.config` en la carpeta `static` que deshabilita el procesamiento de Python para esa carpeta. Esta configuración envía solicitudes de archivos estáticos al servidor web predeterminado en lugar de usar la aplicación de Python.

5. Guarde los archivos, haga clic con el botón derecho en el proyecto en el Explorador de soluciones (asegúrese de que no se ejecuta localmente) y seleccione **Publicar**.  
   
     ![Captura de pantalla del tutorial seleccionado en el Explorador de soluciones, con la opción Publicar resaltada](./media/sql-api-python-application/image20.png)
6. En el cuadro de diálogo **Publicar**, seleccione **Microsoft Azure App Service**, seleccione **Crear nuevo** y, después, haga clic en **Publicar**.
   
    ![Captura de pantalla de la ventana de publicación web con Microsoft Azure App Service resaltada](./media/sql-api-python-application/cosmos-db-python-publish.png)
7. En el cuadro de diálogo **Crear App Service**, escriba el nombre de la aplicación web, junto con la **suscripción**, el **grupo de recursos**, el **plan de App Service** y, a continuación, haga clic en **Crear**.
   
    ![Captura de pantalla de la ventana Microsoft Azure Web Apps](./media/sql-api-python-application/cosmos-db-python-create-app-service.png)
8. En unos segundos, Visual Studio terminará de copiar los archivos en el servidor y aparecerá el error "No se puede mostrar la página. Error interno en el servidor" en la página `http://<your app service>.azurewebsites.net/`.

9. En Azure Portal, abra la nueva cuenta de App Service, a continuación, en el menú de navegación, desplácese hacia abajo hasta la sección **Herramientas de desarrollo**, seleccione **Extensiones** y, finalmente, haga clic en **+ Agregar**.

10. En la página **Elegir extensión**, desplácese hacia abajo hasta la instalación de Python 2.7 más reciente y seleccione la opción de x86 o x64 bits y, a continuación, haga clic en **Aceptar** para aceptar los términos legales.  
   
11. Use la consola de Kudu, que puede buscar en `https://<your app service name>.scm.azurewebsites.net/DebugConsole`, para instalar los paquetes incluidos en el archivo `requirements.txt` de la aplicación. Para ello, en la consola de diagnóstico de Kudu, vaya a la carpeta `D:\home\Python27` de Python y, a continuación, ejecute el siguiente comando como se describe en la sección de la [consola de Kudu](/visual-studio/python/managing-python-on-azure-app-service.md#azure-app-service-kudu-console):

    ```
    D:\home\Python27>python -m pip install --upgrade -r /home/site/wwwroot/requirements.txt
    ```          

12. Reinicie App Service en Azure Portal después de instalar los nuevos paquetes presionando el botón **Reiniciar**. 

    > [!Tip] 
    > Si realiza cambios en el archivo `requirements.txt` de la aplicación, no olvide volver a usar la consola de Kudu para instalar los paquetes que se muestran ahora en ese archivo. 

13. Una vez configurado completamente el entorno del servidor, actualice la página en el explorador y aparecerá la aplicación web.

    ![Resultados de la publicación de las aplicaciones Bottle, Flask y Django en App Service](./media/sql-api-python-application/python-published-app-services.png)

    > [!Tip] 
    > Si no aparece la página web o si continúa apareciendo el mensaje "No se puede mostrar la página. Error interno en el servidor", abra el archivo web.config en Kudo y agregue ` <httpErrors errorMode="Detailed"></httpErrors>` a la sección system.webServer y actualice la página. Esto proporcionará una salida de errores detallada al explorador. 

## <a name="troubleshooting"></a>solución de problemas
Si esta es la primera aplicación de Python que se ejecuta en el equipo, asegúrese de que las carpetas siguientes (o las ubicaciones de instalación equivalentes) se incluyen en la variable PATH:

    C:\Python27\site-packages;C:\Python27\;C:\Python27\Scripts;

Si recibe un error en la página de votos y asignó al proyecto otro nombre distinto del de **tutorial**, asegúrese de que **\_\_init\_\_.py** hace referencia al nombre de proyecto correcto en la línea: `import tutorial.view`.

## <a name="next-steps"></a>pasos siguientes
Felicidades. Ha completado su primera aplicación web de Python con Azure Cosmos DB y la ha publicado en Azure.

Para agregar funcionalidad adicional a la aplicación web, revise las API disponibles en el [SDK de Python de Azure Cosmos DB](sql-api-sdk-python.md).

Para más información acerca de Azure, Visual Studio y Python, consulte el [Python Developer Center](https://azure.microsoft.com/develop/python/). 

Para ver tutoriales adicionales sobre Python Flask, consulte el tutorial [The Flask Mega-Tutorial, Part I: Hello, World!](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world). 

[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[2]: https://www.python.org/downloads/windows/
[3]: https://www.microsoft.com/download/details.aspx?id=44266
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Azure portal]: http://portal.azure.com
