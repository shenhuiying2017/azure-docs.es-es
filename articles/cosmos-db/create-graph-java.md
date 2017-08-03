---
title: "Compilar una aplicación de Java de Azure Cosmos DB mediante API Graph | Microsoft Docs"
description: "Se presenta un ejemplo de código de Java que puede usar para conectarse y consultar datos de gráfico de Azure Cosmos DB con Gremlin."
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/14/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 8eac406c6ef96d7ae8dd5f4931c7d16edb723be8
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="azure-cosmos-db-build-a-java-application-using-the-graph-api"></a>Azure Cosmos DB: Compilar una aplicación de Java mediante API Graph

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y gráficos y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funciones de distribución global y escala horizontal en Azure Cosmos DB. 

En esta guía de inicio rápido se muestra cómo crear una cuenta para API Graph (versión preliminar), una base de datos y un gráfico de Azure Cosmos DB mediante Azure Portal. Después, compilará y ejecutará una aplicación de consola con el controlador de [Java de Gremlin](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) de OSS.  

## <a name="prerequisites"></a>Requisitos previos

* Antes de ejecutar este ejemplo, debe cumplir los siguientes requisitos previos:
   * JDK 1.7 + (ejecute `apt-get install default-jdk` si no tiene JDK) y establezca variables de entorno como`JAVA_HOME`
   * Maven (ejecute `apt-get install maven` si no tiene Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Agregar un gráfico

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a clonar una aplicación de API Graph (versión preliminar) desde GitHub, establecer la cadena de conexión y ejecutarla. Verá lo fácil que es trabajar con datos mediante programación. 

1. Abra una ventana de terminal de Git, como Git Bash, y `cd` en un directorio de trabajo.  

2. Ejecute el comando siguiente para clonar el repositorio de ejemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Revisar el código

Vamos a revisar rápidamente lo que sucede en la aplicación. Abra el archivo `Program.java` y verá estas líneas de código. 

* El elemento `Client` de Gremlin se a partir de la configuración de `src/remote.yaml`.

    ```java
    Cluster cluster = Cluster.build(new File("src/remote.yaml")).create();
    
    Client client = cluster.connect();
    ```

* Se ejecuta una serie de pasos de Gremlin mediante el método `client.submit`.

    ```java
    ResultSet results = client.submit("g.V()");

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```
## <a name="update-your-connection-string"></a>Actualización de la cadena de conexión

1. Abra el archivo src/remote.yaml. 

3. Rellene los valores de configuración de *host*, *port* (puerto), *username* (nombre de usuario), *password* (contraseña), *connectionPool* (grupo de conexión) y *serializer* (serializador) en el archivo src/remote.yaml:

    Configuración|Valor sugerido|Descripción
    ---|---|---
    Hosts|[***.graphs.azure.com]|Ver la captura de pantalla siguiente. Este es el valor del Identificador URI de Gremlin en la página Información general de Azure Portal, entre corchetes, sin la terminación :443/.<br><br>Este valor también se puede obtener desde la pestaña Claves; para ello, use el valor del identificador URI, quite https://, sustituya documents por graphs y elimine la terminación :443 /.
    Port|443|Se establece en 443.
    Nombre de usuario|*Su nombre de usuario*|El recurso con la forma `/dbs/<db>/colls/<coll>` donde `<db>` es el nombre de la base de datos y `<coll>` es el nombre de la colección.
    Password|*Su clave maestra principal*|Ver la segunda captura de pantalla más adelante. Es la clave principal, que puede obtener en la página Claves de Azure Portal, en el cuadro de texto Clave principal. Use el botón Copiar a la izquierda del cuadro de texto para copiar el valor.
    ConnectionPool|{enableSsl: true}|La configuración del grupo de conexiones para SSL.
    serializer|{ className:org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Establecer en este valor y eliminar los saltos de línea `\n` cuando se pegue el valor.

    Para obtener el valor de hosts, copie el valor del **Identificador URI de Gremlin** de la página **Información general**: ![Visualizar y copiar el valor del identificador URI de Gremlin en la página Información general de Azure Portal](./media/create-graph-java/gremlin-uri.png)

    Para obtener el valor de la contraseña, copie la **Clave principal** en la página **Claves**: ![Visualizar y copiar la clave principal en la página Claves de Azure Portal](./media/create-graph-java/keys.png)

## <a name="run-the-console-app"></a>Ejecutar la aplicación de consola

1. Ejecute `mvn package` en un terminal para instalar los paquetes Java necesarios.

2. Ejecute `mvn exec:java -D exec.mainClass=GetStarted.Program` en un terminal para iniciar la aplicación de Java.

Ahora puede volver al Explorador de datos y ver, consultar, modificar y trabajar con estos nuevos datos. 

## <a name="browse-using-the-data-explorer"></a>Examinar mediante el Explorador de datos

Ahora puede volver al Explorador de datos en Azure Portal para examinar y consultar los datos del nuevo gráfico.

* En el Explorador de datos, la nueva base de datos aparece en el panel Colecciones. Expanda **graphdb**, **graphcoll** y, después, haga clic en **Gráfico**.

    Los datos generados por la aplicación de ejemplo se muestran en el panel Gráficos.

## <a name="review-slas-in-the-azure-portal"></a>Revisar los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en Azure Portal: 

1. En el menú de la izquierda de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del recurso que creó. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba en el cuadro de texto el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha obtenido información sobre cómo crear una cuenta de Azure Cosmos DB, crear un gráfico mediante el Explorador de datos y ejecutar una aplicación. Ahora puede crear consultas más complejas e implementar con Gremlin una lógica de recorrido del gráfico eficaz. 

> [!div class="nextstepaction"]
> [Consulta mediante Gremlin](tutorial-query-graph.md)


