---
title: "Compilación de una aplicación de Node.js de Azure Cosmos DB mediante API Graph | Microsoft Docs"
description: "En este tema se presenta un ejemplo de código Node.js que puede usar para conectarse a Azure Cosmos DB y realizar consultas."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/21/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: b9e8c46ba2f029f8dae2b357f05a806d769d0920
ms.contentlocale: es-es
ms.lasthandoff: 06/07/2017


---
# <a name="azure-cosmos-db-build-a-nodejs-application-by-using-graph-api"></a>Azure Cosmos DB: compilación de una aplicación de Node.js mediante API Graph

Azure Cosmos DB es el servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y gráficos y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escala horizontal en Azure Cosmos DB. 

En este artículo de inicio rápido se muestra cómo crear un gráfico, una base de datos y una cuenta de Azure Cosmos DB para API Graph (versión preliminar) mediante Azure Portal. Después, compilará y ejecutará una aplicación de consola con el controlador [Node.js de Gremlin](https://www.npmjs.com/package/gremlin-secure) de código abierto.  

> [!NOTE]
> El módulo npm `gremlin-secure` es una versión modificada del módulo `gremlin`, compatible con los protocolos SSL y SASL que son necesarios para conectar con Azure Cosmos DB. El código fuente está disponible en [GitHub](https://github.com/CosmosDB/gremlin-javascript).
>

## <a name="prerequisites"></a>Requisitos previos

Antes de ejecutar este ejemplo, debe cumplir los siguientes requisitos previos:
* [Node.js](https://nodejs.org/en/) versión v0.10.29 o posterior
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Agregar un gráfico

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora, vamos a clonar una aplicación de API Graph desde GitHub, establecer la cadena de conexión y ejecutarla. Verá lo fácil que es trabajar con datos mediante programación. 

1. Abra una ventana de terminal de Git, como Git Bash, y cambie a un directorio de trabajo (mediante el comando `cd`).  

2. Ejecute el comando siguiente para clonar el repositorio de ejemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Abra el archivo de solución en Visual Studio. 

## <a name="review-the-code"></a>Revisar el código

Vamos a revisar rápidamente lo que sucede en la aplicación. Abra el archivo `app.js` y encontrará las líneas de código siguientes. 

* Se crea el cliente Gremlin.

    ```nodejs
    const client = Gremlin.createClient(
        443, 
        config.endpoint, 
        { 
            "session": false, 
            "ssl": true, 
            "user": `/dbs/${config.database}/colls/${config.collection}`,
            "password": config.primaryKey
        });
    ```

  Las configuraciones están todas en `config.js`, que podemos editar en la sección siguiente.

* Se ejecuta una serie de pasos de Gremlin con el método `client.execute`.

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

## <a name="update-your-connection-string"></a>Actualización de la cadena de conexión

Ahora, vuelva a Azure Portal para obtener la información de la cadena de conexión y cópiela en la aplicación.

1. En [Azure Portal](http://portal.azure.com/), en la cuenta de Azure Cosmos DB, en el menú de navegación de la izquierda, haga clic en **Claves** y en **Claves de lectura y escritura**. En el paso siguiente, debe usar los botones de copia de la derecha para copiar el URI y la clave principal en el archivo `app.js`.

    ![Hoja de claves de Azure Portal](./media/create-graph-nodejs/keys.png)

2. Copie el valor del identificador URI de Gremlin (con el botón de copia) y conviértalo en el valor de la clave `config.endpoint` en config.js. El punto de conexión de Gremlin debe ser solo el nombre de host sin el número de puerto y protocolo, como `mygraphdb.graphs.azure.com` (no como `https://mygraphdb.graphs.azure.com` ni `mygraphdb.graphs.azure.com:433`).

    `config.endpoint = "GRAPHENDPOINT";`

3. Copie el valor de la clave principal del portal y úselo como valor de config.primaryKey en config.js. Ya ha actualizado la aplicación con toda la información que necesita para comunicarse con Azure Cosmos DB. 

    `config.primaryKey = "PRIMARYKEY";`

4. Escriba el nombre de la base de datos y el nombre del gráfico (contenedor) para el valor de config.database y config.collection. 

Este es un ejemplo del aspecto que debería tener el archivo config.js completado:

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "mygraphdb.graphs.azure.com";
config.primaryKey = "OjlhK6tjxfSXyKtrmCiM9O6gQQgu5DmgAoauzD1PdPIq1LZJmILTarHvrolyUYOB0whGQ4j21rdAFwoYep7Kkw==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>Ejecutar la aplicación de consola

1. Abra una ventana del terminal y, con el comando `cd`, cambie al directorio de instalación del archivo package.json incluido en el proyecto.  

2. Ejecute `npm install` para instalar los módulos npm necesarios, incluido `gremlin-secure`.

3. Ejecute `node app.js` en un terminal para iniciar la aplicación de nodo.

## <a name="browse-with-data-explorer"></a>Examinar con el Explorador de datos

Ahora puede volver al Explorador de datos en Azure Portal para examinar, consultar, modificar y trabajar con los datos del nuevo gráfico.

En el Explorador de datos, la nueva base de datos aparece en el panel **Colecciones**. Expanda **graphdb**, **graphcoll** y, después, haga clic en **Gráfico**.

Los datos generados por la aplicación de ejemplo se muestran en el panel **Gráficos**.

## <a name="review-slas-in-the-azure-portal"></a>Revisar los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>Limpiar los recursos

Si no piensa seguir usando la aplicación, haga lo siguiente para eliminar todos los recursos que ha creado en este artículo: 

1. En Azure Portal, en el menú de navegación de la izquierda, haga clic en **Grupos de recursos** y, después, en el nombre del recurso que creó. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha obtenido información sobre cómo crear una cuenta de Azure Cosmos DB, crear un gráfico mediante el Explorador de datos y ejecutar una aplicación. Ahora puede usar Gremlin para implementar una lógica de recorrido del gráfico eficaz y crear consultas más complejas. 

> [!div class="nextstepaction"]
> [Consulta mediante Gremlin](tutorial-query-graph.md)

