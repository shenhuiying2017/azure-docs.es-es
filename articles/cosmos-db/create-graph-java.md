---
title: "Creación de una base de datos de grafos de Azure Cosmos DB con Java | Microsoft Docs"
description: "Se presenta un ejemplo de código de Java que puede usar para conectarse y consultar datos de grafos de Azure Cosmos DB con Gremlin."
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
ms.date: 08/07/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: afa4fe6cdef298e4504ddcf3e344ee6a5c181653
ms.contentlocale: es-es
ms.lasthandoff: 08/08/2017

---
# <a name="azure-cosmos-db-create-a-graph-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB: creación una base de datos de grafos mediante Java y Azure Portal

Azure Cosmos DB es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear rápidamente bases de datos de documentos, clave-valor y grafos, y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escala horizontal en Azure Cosmos DB. 

Este tutorial rápido crea una base de datos de grafos mediante las herramientas de Azure Portal para Azure Cosmos DB. Este tutorial rápido también muestra cómo crear rápidamente una aplicación de consola Java usando una base de datos de grafos mediante el controlador [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) de OSS. Las instrucciones que se indican en este tutorial rápido se pueden seguir en cualquier sistema operativo que sea capaz de ejecutar Java. Al completar este tutorial rápido aprenderá a crear y modificar los recursos de base de datos de grafos en la interfaz de usuario o mediante programación, lo que prefiera. 

## <a name="prerequisites"></a>Requisitos previos

* [Kit de desarrollo de Java (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * En Ubuntu, ejecute `apt-get install default-jdk` para instalar el JDK.
    * Asegúrese de establecer la variable de entorno JAVA_HOME para que apunte a la carpeta donde está instalado el JDK.
* [Descargar](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) un archivo binario de [Maven](http://maven.apache.org/)
    * En Ubuntu, puede ejecutar `apt-get install maven` para instalar Maven.
* [Git](https://www.git-scm.com/)
    * En Ubuntu, puede ejecutar `sudo apt-get install git` para instalar Git.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

Para poder crear una base de datos de grafos, debe crear una cuenta de base de datos de Gremlin (Graph) con Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Agregar un grafo

Ahora puede usar la herramienta Explorador de datos en Azure Portal para crear una base de datos de grafos. 

1. En Azure Portal, en el menú de navegación izquierdo, haga clic en **Explorador de datos (versión preliminar)**. 
2. En la hoja **Explorador de datos (Vista previa)**, haga clic en **New Graph** (Nuevo grafo) y rellene la página con la información siguiente.

    ![Explorador de datos en Azure Portal](./media/create-graph-java/azure-cosmosdb-data-explorer.png)

    Configuración|Valor sugerido|Descripción
    ---|---|---
    Identificador de base de datos|sample-database|Identificador de la nueva base de datos. Los nombres de bases de datos deben tener entre 1 y 255 caracteres y no pueden contener `/ \ # ?` o un espacio al final.
    Identificador de grafo|sample-graph|Identificador del nuevo grafo. Los nombres de grafo tienen los mismos requisitos de caracteres que los identificadores de base de datos.
    Capacidad de almacenamiento| 10 GB|Deje el valor predeterminado. Se trata de la capacidad de almacenamiento de la base de datos.
    Rendimiento|400 RU|Deje el valor predeterminado. Se puede escalar el rendimiento más adelante si quiere reducir la latencia.
    RU/m|Off|Deje el valor predeterminado. Si tiene que controlar grandes cargas de trabajo más adelante, puede activar la característica [RU/m](request-units-per-minute.md) en ese momento.
    Clave de partición|Déjelo en blanco|Para este tutorial rápido, deje en blanco la clave de partición.

3. Una vez que haya rellenado el formulario, haga clic en **Aceptar**.

## <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

Ahora vamos a clonar una aplicación de grafos desde GitHub, establecer la cadena de conexión y ejecutarla. Verá lo fácil que es trabajar con datos mediante programación. 

1. Abra una ventana de terminal de Git, como Git Bash, y `cd` en un directorio de trabajo.  

2. Ejecute el comando siguiente para clonar el repositorio de ejemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Revisar el código

Vamos a revisar rápidamente lo que sucede en la aplicación. Abra el archivo `Program.java` en la carpeta \src\GetStarted y busque estas líneas de código. 

* El elemento `Client` de Gremlin se a partir de la configuración de `src/remote.yaml`.

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

* Se ejecuta una serie de pasos de Gremlin mediante el método `client.submit`.

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-string"></a>Actualización de la cadena de conexión

1. Abra el archivo src/remote.yaml. 

3. Rellene los valores de *hosts*, *nombre de usuario* y *contraseña* en el archivo src/remote.yaml. El resto de los valores no tienen que cambiarse.

    Configuración|Valor sugerido|Descripción
    ---|---|---
    Hosts|[***.graphs.azure.com]|Consulte la captura de pantalla que sigue a esta tabla. Este es el valor del identificador URI de Gremlin en la página Información general de Azure Portal, entre corchetes, sin la terminación :443/.<br><br>Este valor también se puede obtener desde la pestaña Claves; para ello, use el valor del identificador URI, quite https://, sustituya documents por graphs y elimine la terminación :443 /.
    Nombre de usuario|/dbs/sample-database/colls/sample-graph|El recurso con la forma `/dbs/<db>/colls/<coll>` donde `<db>` es el nombre de la base de datos actual y `<coll>` es el nombre de la colección actual.
    Password|*Su clave maestra principal*|Vea la segunda captura de pantalla que sigue a esta tabla. Este valor es la clave principal, que puede obtener en la página Claves de Azure Portal, en el cuadro de texto Clave principal. Copie el valor con el botón Copiar a la derecha del cuadro.

    Para obtener el valor de Hosts, copie el valor de **URI de Gremlin** de la página **Introducción**. Si está vacía, consulte las instrucciones en la fila Hosts en la tabla anterior acerca de cómo crear el URI de Gremlin desde la hoja de claves.
![Copie del valor del URI de Gremlin en la página Introducción en Azure Portal ](./media/create-graph-java/gremlin-uri.png)

    Para obtener el valor de la contraseña, copie la **Clave principal** de la página **Claves**: ![Copie la clave principal en la página Claves de Azure Portal](./media/create-graph-java/keys.png)

## <a name="run-the-console-app"></a>Ejecutar la aplicación de consola

1. En la ventana del terminal de git, use `cd` para cambiar a la carpeta azure-cosmos-db-graph-java-getting-started.

2. En la ventana del terminal de git, escriba `mvn package` para instalar los paquetes Java necesarios.

3. En la ventana del terminal de git, ejecute `mvn exec:java -D exec.mainClass=GetStarted.Program` para iniciar la aplicación de Java.

En la ventana del terminal se muestran los vértices y los bordes que se agregan al grafo. Una vez que el programa finalice, vuelva a cambiar a Azure Portal en el Explorador de Internet. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Revise y agregue datos de ejemplo

Ahora puede volver al Explorador de datos y ver los vértices que se agregan al grafo, y agregar puntos de datos adicionales.

1. En el Explorador de datos, expanda el **grafo de ejemplo** de la **base de datos de ejemplo**/, haga clic en **Grafo** y, a continuación, haga clic en **Aplicar filtro**. 

   ![Creación de documentos en el Explorador de datos en Azure Portal](./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png)

2. En la lista **Resultados**, observe los nuevos usuarios agregados al grafo. Seleccione **ben** y observe que se ha conectado a robin. Puede mover los vértices en el explorador de grafos, acercar y alejar el grafo, y ampliar el tamaño de la superficie del explorador de grafos. 

   ![Nuevos vértices en el grafo del Explorador de datos de Azure Portal](./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png)

3. Vamos a agregar algunos usuarios nuevos al grafo mediante el Explorador de datos. Haga clic en el botón **Nuevo vértice** para agregar datos al grafo.

   ![Creación de documentos en el Explorador de datos en Azure Portal](./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png)

4. Escriba una etiqueta de *persona* y, a continuación, escriba las claves y los valores siguientes para crear el primer vértice en el grafo. Tenga en cuenta que puede crear propiedades únicas para cada persona en el grafo. Solo se requiere la clave de identificador.

    key|value|Notas
    ----|----|----
    id|ashley|Identificador único del vértice. Si no se especifica un identificador, se genera uno automáticamente.
    gender|mujer| 
    técnico | Java | 

    > [!NOTE]
    > En este tutorial rápido se crea una colección sin particiones. Sin embargo, si crea una colección con particiones especificando una clave de partición durante la creación de la colección, debe incluir la clave de partición como una clave en cada nuevo vértice. 

5. Haga clic en **Aceptar**. Puede que tenga que expandir la pantalla para ver **Aceptar** en la parte inferior de la pantalla.

6. Haga clic en **Nuevo vértice** de nuevo y agregue otro usuario. Escriba una etiqueta de *persona* y, a continuación, escriba las claves y los valores siguientes:

    key|value|Notas
    ----|----|----
    id|rakesh|Identificador único del vértice. Si no se especifica un identificador, se genera uno automáticamente.
    gender|hombre| 
    centro educativo|MIT| 

7. Haga clic en **Aceptar**. 

8. Haga clic en **Aplicar filtro** con el filtro `g.V()` predeterminado. Todos los usuarios se muestran ahora en la lista **Resultados**. Si agrega más datos, puede usar filtros para limitar los resultados. De forma predeterminada, el Explorador de datos usa `g.V()` para recuperar todos los vértices de un grafo, pero puede cambiarlo a una [consulta de grafo](tutorial-query-graph.md), como `g.V().count()`, para devolver un recuento de todos los vértices del grafo en formato JSON.

9. Ahora podemos conectar a rakesh y ashley. Asegúrese de que **ashley** está seleccionado en la lista **Resultados** y, a continuación, haga clic en el botón para editar junto a **Destinos** en la parte inferior derecha. Puede que tenga que ampliar la ventana para ver el área **Propiedades**.

   ![Cambio del destino de un vértice en un grafo](./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png)

10. En el cuadro **Destino**, escriba *rakesh*, en el cuadro **Edge label** (Etiqueta de borde), escriba *sabe* y, a continuación, haga clic en la casilla.

   ![Adicion de una conexión entre ashley y rakesh en el Explorador de datos](./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png)

11. Ahora seleccione **rakesh** en la lista de resultados y compruebe que ashley y rakesh están conectados. 

   ![Dos vértices conectados en el Explorador de datos](./media/create-graph-java/azure-cosmosdb-graph-explorer.png)

    También puede usar el Explorador de datos para crear procedimientos almacenados, UDF y desencadenadores para realizar la lógica de negocios del servidor, así como escalar el rendimiento. El Explorador de datos expone todo el acceso a datos mediante programación integrado que está disponible en las API, pero permite un acceso fácil a los datos de Azure Portal.



## <a name="review-slas-in-the-azure-portal"></a>Revisar los SLA en Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en Azure Portal: 

1. En el menú de la izquierda de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del recurso que creó. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba en el cuadro de texto el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha obtenido información sobre cómo crear una cuenta de Azure Cosmos DB, crear un grafo mediante el Explorador de datos y ejecutar una aplicación. Ahora puede crear consultas más complejas e implementar con Gremlin una lógica eficaz de recorrido del grafo. 

> [!div class="nextstepaction"]
> [Consulta mediante Gremlin](tutorial-query-graph.md)


