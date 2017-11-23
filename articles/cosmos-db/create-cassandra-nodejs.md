---
title: "Guía de inicio rápido: API de Cassandra con Node.js y Azure Cosmos DB | Microsoft Docs"
description: "Esta guía de inicio rápido muestra cómo usar la API de Cassandra de Azure Cosmos DB para crear una aplicación de perfil con Node.js"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 4732e57d-32ed-40e2-b148-a8df4ff2630d
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/15/2017
ms.author: govindk
ms.openlocfilehash: abf88cf96b32bc4168cb8c09a6e70ad0e395e88c
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2017
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-and-azure-cosmos-db"></a>Guía de inicio rápido: compilación de una aplicación Cassandra con Node.js y Azure Cosmos DB

Esta guía de inicio rápido muestra cómo se usan Node.js y la [API de Cassandra](cassandra-introduction.md) de Azure Cosmos DB para compilar una aplicación de perfil mediante la clonación de un ejemplo de GitHub. Esta guía de inicio rápido también le guía a través de la creación de una cuenta de Azure Cosmos DB a través de Azure Portal en la web.

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, tablas, clave-valor y grafos, y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escala horizontal en Azure Cosmos DB. 

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] También puede [probar gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sin suscripción de Azure, sin cargos y sin compromiso.

Acceda a la versión preliminar del programa de la API de Cassandra de Azure Cosmos DB. Si no ha solicitado el acceso aún, [regístrese ahora](cassandra-introduction.md#sign-up-now).

Además:
* [Node.js](https://nodejs.org/en/) versión v0.10.29 o superior
* [Git](http://git-scm.com/)

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

Para poder crear una base de datos de documentos, debe crear una cuenta de Cassandra con Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a clonar una aplicación de la API de Cassandra desde GitHub, establecer la cadena de conexión y ejecutarla. Verá lo fácil que es trabajar con datos mediante programación. 

1. Abra una ventana de terminal de Git, como git bash y utilice el comando `cd` para cambiar a una carpeta para instalar la aplicación de ejemplo. 

    ```bash
    cd "C:\git-samples"
    ```

2. Ejecute el comando siguiente para clonar el repositorio de ejemplo. Este comando crea una copia de la aplicación de ejemplo en el equipo.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Revisar el código

Este paso es opcional. Si está interesado en aprender cómo se crean los recursos de base de datos en el código, puede revisar los siguientes fragmentos de código. Todos los fragmentos de código se toman del archivo `uprofile.js` en la carpeta C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started. En caso contrario, puede ir directamente a [Actualización de la cadena de conexión](#update-your-connection-string). 

* El nombre de usuario y la contraseña se establecen en la página de la cadena de conexión de Azure Portal. "path\to\cert" proporciona una ruta de acceso a un certificado X509. 

   ```nodejs
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* El objeto `client` se inicializa con la información de contactPoint. contactPoint se recupera de Azure Portal.

    ```nodejs
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* El objeto `client` se conecta a la API de Cassandra de Azure Cosmos DB.

    ```nodejs
    client.connect(next);
    ```

* Se crea un espacio de claves.

    ```nodejs
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* Se crea una tabla.

   ```nodejs
   function createTable(next) {
    var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* Se insertan entidades de clave/valor.

    ```nodejs
    ...
       {
          query: 'INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)',
          params: [5, 'IvanaV', 'Belgaum', '2017-10-3136']
        }
    ];
    client.batch(queries, { prepare: true}, next);
    ```

* Realice una consulta para obtener todos los valores de clave.

    ```nodejs
   var query = 'SELECT * FROM uprofile.user';
    client.execute(query, { prepare: true}, function (err, result) {
      if (err) return next(err);
      result.rows.forEach(function(row) {
        console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
      }, this);
      next();
    });
    ```  
    
* Realice una consulta para obtener un valor de clave.

    ```nodejs
    function selectById(next) {
        console.log("\Getting by id");
        var query = 'SELECT * FROM uprofile.user where user_id=1';
        client.execute(query, { prepare: true}, function (err, result) {
        if (err) return next(err);
            result.rows.forEach(function(row) {
            console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
        }, this);
        next();
        });
    }
    ```  

## <a name="update-your-connection-string"></a>Actualizar la cadena de conexión

Ahora vuelva a Azure Portal para obtener la información de la cadena de conexión y cópiela en la aplicación. Esto permite que la aplicación se comunique con la base de datos hospedada.

1. En [Azure Portal](http://portal.azure.com/), haga clic en **Cadena de conexión**. 

    Use el botón ![Botón Copiar](./media/create-cassandra-nodejs/copy.png) del lado derecho de la pantalla para copiar el valor superior, CONTACT POINT (Punto de contacto).

    ![Visualización y copia del valor de CONTACT POINT (Punto de contacto), NOMBRE DE USUARIO y CONTRASEÑA de la página de la cadena de conexión de Azure Portal](./media/create-cassandra-nodejs/keys.png)

2. Abra el archivo `config.js` . 

3. Pegue el valor de CONTACT POINT (Punto de contacto) del portal en `<FillMEIN>` en la línea 4.

    La línea 4 ahora debe ser similar a 

    `config.contactPoint = "cosmos-db-quickstarts.documents.azure.com:10350"`

4. Copie el valor de NOMBRE DE USUARIO del portal y péguelo en `<FillMEIN>` en la línea 2.

    La línea 2 ahora debe ser similar a 

    `config.username = 'cosmos-db-quickstart';`
    
5. Copie el valor de CONTRASEÑA del portal y péguelo en `<FillMEIN>` en la línea 3.

    La línea 3 ahora debe ser similar a

    `config.password = '2Ggkr662ifxz2Mg==';`

6. Guarde el archivo config.js.
    
## <a name="use-the-x509-certificate"></a>Uso del certificado X509 

1. Si necesita agregar Baltimore CyberTrust Root, tiene el número de serie 02:00:00:b9 y la huella digital SHA1 d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74. Se descarga desde https://cacert.omniroot.com/bc2025.crt y se guarda en un archivo local con la extensión .cer. 

2. Abra uprofile.js y cambie "path\to\cert" para que apunte al certificado nuevo. 

3. Guarde uprofile.js. 

## <a name="run-the-app"></a>Ejecución de la aplicación

1. En la ventana del terminal de git, ejecute `npm install` para instalar los módulos de npm necesarios.

2. Ejecute `node uprofile.js` para iniciar la aplicación de nodo.

3. Compruebe los resultados previstos desde la línea de comandos.

    ![Visualización y comprobación del resultado](./media/create-cassandra-nodejs/output.png)

    Presione CTRL + C para detener la ejecución del programa y cerrar la ventana de consola. 

    Ahora puede volver al Explorador de datos de Azure Portal para ver, consultar, modificar estos nuevos datos y trabajar con ellos. 

    ![Visualización de los datos en el Explorador de datos](./media/create-cassandra-nodejs/data-explorer.png) 

## <a name="review-slas-in-the-azure-portal"></a>Revisar los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha aprendido a crear una cuenta de Azure Cosmos DB, crear una colección mediante el Explorador de datos y ejecutar una aplicación. Ahora puede importar datos adicionales en la cuenta de Cosmos DB. 

> [!div class="nextstepaction"]
> [Importación de datos de Cassandra en Azure Cosmos DB](cassandra-import-data.md)


