---
title: "Azure Cosmos DB: compilación de una aplicación web de Flask con Python y MongoDB API de Azure Cosmos DB | Microsoft Docs"
description: "En este tema se presenta un ejemplo de código de Python Flask que se puede usar para conectarse a MongoDB API de Azure Cosmos DB y realizar consultas."
services: cosmos-db
documentationcenter: 
author: hshapiro
manager: scicoria
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/2/2017
ms.author: hshapiro
ms.openlocfilehash: f86c6cce82812e02f373d7307c76ace26ea3e99b
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="azure-cosmos-db-build-a-flask-app-with-the-mongodb-api"></a>Azure Cosmos DB: compilación de una aplicación de Flask con MongoDB API

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y grafos, y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escala horizontal en Azure Cosmos DB.

En esta guía de inicio rápido se usa el siguiente [ejemplo de Flask](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) y se muestra cómo compilar una aplicación sencilla de tareas de Flask con el [emulador Azure Cosmos DB](/local-emulator.md) en lugar de con MongoDB.

## <a name="prerequisites"></a>Requisitos previos

- Descargue el [emulador de Azure Cosmos DB](/local-emulator.md). Actualmente, el emulador solo se admite en Windows. Con el ejemplo se muestra cómo utilizarlo con una clave de producción de Azure en cualquier plataforma.

- Si aún no tiene [VS Code](https://code.visualstudio.com/Download) instalado, puede instalarlo rápidamente para su plataforma (Windows, Mac, Linux).

- No olvide instalar una de las extensiones de Python populares para agregar compatibilidad con el lenguaje Python.
    1. Seleccione una extensión.
    2. Instálela; para ello, escriba `ext install` en la paleta de comandos `Ctrl+Shift+P`.

    En los ejemplos de este documento se usa la popular y completa [extensión de Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) de Don Jayamanne.

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a clonar una aplicación de Flask con MongoDB API desde GitHub, establecer la cadena de conexión y ejecutarla. Verá lo fácil que es trabajar con datos mediante programación.

1. Abra una ventana de terminal de Git, como Git Bash, y `cd` en un directorio de trabajo.
2. Ejecute el comando siguiente para clonar el repositorio de ejemplo.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Ejecute el siguiente comando para instalar los módulos de Python.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Abra la carpeta en Visual Studio Code.

## <a name="review-the-code"></a>Revisar el código

Vamos a revisar rápidamente lo que sucede en la aplicación. Abra el archivo **app.py** del directorio raíz y observe que estas líneas de código crean la conexión de Azure Cosmos DB. El código siguiente utiliza la cadena de conexión para el emulador de Azure Cosmos DB local. La contraseña debe dividirse tal como se muestra a continuación para dar cabida a la barra diagonal, que no se puede analizar de otra manera.

* Inicie el cliente de MongoDB, recupere la base de datos y realice la autenticación.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Recupere la colección o créela si aún no existe.

    ```python
    todos = db.todo #Select the collection
    ```

* Creación de la aplicación

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Ejecución de la aplicación web

1. Asegúrese de que el emulador de Azure Cosmos DB se está ejecutando.

2. Abra una ventana de terminal y `cd` en el directorio donde se guarda la aplicación.

3. A continuación, establezca la variable de entorno para la aplicación de Flask con `set FLASK_APP=app.py` o `export FLASK_APP=app.py` si usa un equipo Mac.

4. Ejecute la aplicación con `flask run` y vaya a [http://127.0.0.1:5000/](http://127.0.0.1:5000/).

5. Agregue y quite tareas para ver la incorporación o el cambio en la colección.

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Actualizar la cadena de conexión

Si desea probar el código comparándolo con una cuenta de Azure Cosmos DB en vivo, vaya a Azure Portal para crear una cuenta y obtener la información de la cadena de conexión. A continuación, cópiela en la aplicación.

1. En [Azure Portal](http://portal.azure.com/), en la cuenta de Azure Cosmos DB, en el panel de navegación izquierdo, haga clic en **Cadena de conexión** y en **Claves de lectura y escritura**. Deberá usar los botones de copia del lado derecho de la pantalla para copiar el nombre de usuario, la contraseña y el host en el archivo Dal.cs en el paso siguiente.

2. Abra el archivo **app.py** en el directorio raíz.

3. Copie el valor del **nombre de usuario** del portal (con el botón de copia) y conviértalo en el valor del **nombre** en el archivo **app.py**.

4. A continuación, copie el valor de la **cadena de conexión** del portal y conviértalo en el valor de MongoClient en el archivo **app.py**.

5. Por último, copie el valor de la **contraseña** del portal y conviértalo en el valor de la **contraseña** en el archivo **app.py**.

Ya ha actualizado la aplicación con toda la información que necesita para comunicarse con Azure Cosmos DB. Puede ejecutarla como antes.

## <a name="deploy-to-azure"></a>Implementación en Azure

Para implementar esta aplicación, puede crear una nueva aplicación web en Azure y habilitar la implementación continua con una bifurcación de este repositorio de GitHub. Siga este [tutorial](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment) para configurar la implementación continua con GitHub en Azure.

Para la implementación en Azure, debe quitar las claves de la aplicación y asegurarse de que la sección siguiente no tiene comentarios:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Después debe agregar los valores de MONGOURL, MONGO_PASSWORD y MONGO_USERNAME a la configuración de la aplicación. Puede seguir este [tutorial](https://docs.microsoft.com/azure/app-service-web/web-sites-configure#application-settings) para aprender acerca de la configuración de la aplicación en Azure Web Apps.

Si no desea crear una bifurcación de este repositorio, también puede hacer clic en el botón de implementación en Azure siguiente. A continuación, debe ir a Azure y ajustar la configuración de la aplicación con la información de cuenta de Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="http://azuredeploy.net/deploybutton.png"/>
</a>

> [!NOTE]
> Si planea almacenar el código en GitHub u otras opciones de control de código fuente, asegúrese de quitar las cadenas de conexión del código. En su lugar, se pueden establecer con la configuración de la aplicación web.

## <a name="review-slas-in-the-azure-portal"></a>Revisar los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en Azure Portal:

1. En el menú de la izquierda de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del recurso que creó.
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba en el cuadro de texto el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

Con esta guía de inicio rápido ha aprendido a crear una cuenta de Azure Cosmos DB y a ejecutar una aplicación de Flask con MongoDB API. Ahora puede importar otros datos a la cuenta de Cosmos DB.

> [!div class="nextstepaction"]
> [Importar datos en Azure Cosmos DB para la API MongoDB](mongodb-migrate.md)
