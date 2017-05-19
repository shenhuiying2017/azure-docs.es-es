---
title: "Azure Cosmos DB: Compilar una aplicación con Python y la API DocumentDB | Microsoft Docs"
description: "En este tema se presenta un ejemplo de código de Python que se puede usar para conectarse a la API DocumentDB de Azure Cosmos DB y realizar consultas."
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 51c11be2-af6d-425f-a86a-39cbfe61da29
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 05/13/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6e64614c6ca746d91be535b64e520033c9e7d053
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-build-a-documentdb-api-app-with-python-and-the-azure-portal"></a>Azure Cosmos DB: Compilar una aplicación de API DocumentDB con Python y Azure Portal

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y gráficos y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escala horizontal en Azure Cosmos DB. 

En esta guía de inicio rápido se muestra cómo crear una cuenta, una base de datos de documentos y una colección de Azure Cosmos DB mediante Azure Portal. Después, compilará y ejecutará una aplicación de consola compilada en la [API DocumentDB de Python](../documentdb/documentdb-sdk-python.md).

## <a name="prerequisites"></a>Requisitos previos

* Antes de ejecutar este ejemplo, debe cumplir los siguientes requisitos previos:
    * [Visual Studio 2015](http://www.visualstudio.com/) o posterior.
    * Herramientas de Python para Visual Studio desde [GitHub](http://microsoft.github.io/PTVS/). En este tutorial se usan las herramientas de Python para VS 2015.
    * Python 2.7 en [python.org](https://www.python.org/downloads/release/python-2712/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="add-a-collection"></a>Agregar una colección

[!INCLUDE [cosmosdb-create-collection](../../includes/cosmosdb-create-collection.md)]

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a clonar una aplicación de API DocumentDB desde GitHub, establecer la cadena de conexión y ejecutarla. Verá lo fácil que es trabajar con datos mediante programación. 

1. Abra una ventana de terminal de Git, como Git Bash, y `cd` en un directorio de trabajo.  

2. Ejecute el comando siguiente para clonar el repositorio de ejemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-python-getting-started.git
    ```  
## <a name="review-the-code"></a>Revisar el código

Vamos a revisar rápidamente lo que sucede en la aplicación. Abra el archivo DocumentDBRepository.cs y observe que estas líneas de código crean los recursos de Azure Cosmos DB. 


* Se inicializa DocumentClient.

    ```python
    # Initialize the Python DocumentDB client
    client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})
    ```

* Se crea una base de datos.

    ```python
    # Create a database
    db = client.CreateDatabase({ 'id': config['DOCUMENTDB_DATABASE'] })
    ```

* Se crea una colección.

    ```python
    # Create collection options
    options = {
        'offerEnableRUPerMinuteThroughput': True,
        'offerVersion': "V2",
        'offerThroughput': 400
    }

    # Create a collection
    collection = client.CreateCollection(db['_self'], { 'id': config['DOCUMENTDB_COLLECTION'] }, options)
    ```

* Se crean algunos documentos.

    ```python
    # Create some documents
    document1 = client.CreateDocument(collection['_self'],
        { 
            'id': 'server1',
            'Web Site': 0,
            'Cloud Service': 0,
            'Virtual Machine': 0,
            'name': 'some' 
        })
    ```

* Se realiza una consulta con SQL.

    ```python
    # Query them in SQL
    query = { 'query': 'SELECT * FROM server s' }    
            
    options = {} 
    options['enableCrossPartitionQuery'] = True
    options['maxItemCount'] = 2

    result_iterable = client.QueryDocuments(collection['_self'], query, options)
    results = list(result_iterable);

    print(results)
    ```

## <a name="update-your-connection-string"></a>Actualizar la cadena de conexión

Ahora vuelva a Azure Portal para obtener la información de la cadena de conexión y cópiela en la aplicación.

1. En [Azure Portal](http://portal.azure.com/), en la cuenta de Azure Cosmos DB, en el panel de navegación izquierdo, haga clic en **Claves** y en **Claves de lectura y escritura**. Deberá usar los botones de copia del lado derecho de la pantalla para copiar el URI y la clave principal en el archivo `DocumentDBGetStarted.py` en el paso siguiente.

    ![Visualización y copia de una clave de acceso en Azure Portal, hoja Claves](./media/create-documentdb-dotnet/keys.png)

2. Abra el archivo `DocumentDBGetStarted.py`. 

3. Copie el valor del URI del portal (con el botón de copia) y conviértalo en el valor de la clave de punto de conexión en `DocumentDBGetStarted.py`. 

    `config.ENDPOINT : "https://FILLME.documents.azure.com"`

4. Después, copie el valor de la clave principal del portal y conviértalo en el valor de `config.MASTERKEY` en `DocumentDBGetStarted.py`. Ya ha actualizado la aplicación con toda la información que necesita para comunicarse con Azure Cosmos DB. 

    `config.MASTERKEY : "FILLME"`
    
## <a name="run-the-app"></a>Ejecución de la aplicación
1. En Visual Studio, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones**, seleccione el entorno de Python actual y haga clic con el botón derecho.

2. Seleccione Instalar paquete de Python y, después, escriba **pydocumentdb**.

3. Presione F5 para ejecutar la aplicación. La aplicación se muestra en el explorador. 

Ahora puede volver al Explorador de datos y ver, consultar, modificar y trabajar con estos nuevos datos. 

## <a name="review-slas-in-the-azure-portal"></a>Revisar los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en Azure Portal:

1. En el menú izquierdo de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del recurso que ha creado. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba en el cuadro de texto el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha aprendido a crear una cuenta de Azure Cosmos DB, crear una colección mediante el Explorador de datos y ejecutar una aplicación. Ahora puede importar datos adicionales en la cuenta de Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar datos en Azure Cosmos DB para la API DocumentDB](../documentdb/documentdb-import-data.md)



