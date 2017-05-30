---
title: "Azure Cosmos DB: análisis de gráficos mediante Spark y Apache TinkerPop Gremlin | Microsoft Docs"
description: "Presenta un conjunto de instrucciones para configurar y ejecutar análisis de gráficos y Azure Cosmos DB de proceso en paralelo con Spark GraphX"
services: cosmosdb
documentationcenter: 
author: khdang
manager: shireest
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: gremlin
ms.topic: hero-article
ms.date: 05/21/2017
ms.author: khdang
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 7a95953fadb3089f60fd55973fdb3410012655af
ms.contentlocale: es-es
ms.lasthandoff: 05/25/2017


---
# <a name="azure-cosmos-db-perform-graph-analytics-using-spark-and-apache-tinkerpop-gremlin"></a>Azure Cosmos DB: análisis de gráficos mediante Spark y Apache TinkerPop Gremlin

[Azure Cosmos DB](introduction.md) es un servicio de base de datos con varios modelos y de distribución global de Microsoft. Puede crear bases de datos de documentos, de pares clave-valor y de gráficos, y realizar consultas en ellas. Todas las bases de datos se beneficiarán de las funcionalidades de distribución global y escalado horizontal en Azure Cosmos DB. Azure Cosmos DB admite cargas de trabajo de gráficos OLTP mediante [el lenguaje Gremlin de Apache TinkerPop](graph-introduction.md).

[Spark](http://spark.apache.org/) es un proyecto de Apache Software Foundation centrado en el procesamiento de datos OLAP de uso general. Spark proporciona un modelo híbrido de computación distribuida basado en memoria/disco, similar al modelo de MapReduce de Hadoop. Puede implementar Apache Spark en la nube mediante [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/). Spark incluye la [biblioteca GraphX](http://spark.apache.org/graphx/) para gráficos y computación de gráficos en paralelo, que también utiliza el lenguaje Gremlin de Apache TinkerPop.

Con Azure Cosmos DB y Spark, puede realizar cargas de trabajo OLTP y OLAP mediante Gremlin. Este tutorial muestra cómo ejecutar consultas de Gremlin para Azure Cosmos DB en un clúster de Azure HDInsight Spark.

## <a name="prerequisites"></a>Requisitos previos

* Antes de ejecutar este ejemplo, debe cumplir los siguientes requisitos previos:
   * Clúster de Azure HDInsight Spark 2.0
   * JDK 1.8+ (ejecute `apt-get install default-jdk` si no tiene JDK)
   * Maven (ejecute `apt-get install maven` si no tiene Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Para más información sobre cómo aprovisionar un clúster de Azure HDInsight Spark, consulte [Aprovisionamiento de clústeres de HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-an-azure-cosmos-db-database-account"></a>Creación de una cuenta de base de datos de Azure Cosmos DB

En primer lugar, creamos una cuenta de base de datos con la API Graph.

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="add-a-collection"></a>Incorporación de una colección

[!INCLUDE [cosmosdb-create-collection](../../includes/cosmosdb-create-collection.md)]

## <a name="get-apache-tinkerpop"></a>Obtención de Apache TinkerPop

* Conéctese en remoto al nodo principal del clúster de HDInsight `ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net`

* Clone el código fuente de TinkerPop3, compílelo localmente e instálelo en la memoria caché de Maven.

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

* Instale el complemento Spark-Gremlin 

    1. La instalación del complemento se administra mediante Grape. Primero, se debe rellenar la información de los repositorios de Grape para poder descargar el complemento y sus dependencias. 

        * Cree el archivo de configuración de Grape si no está en `~/.groovy/grapeConfig.xml`. Use la configuración siguiente:

            ```xml
            <ivysettings>
            <settings defaultResolver="downloadGrapes"/>
            <resolvers>
                <chain name="downloadGrapes">
                <filesystem name="cachedGrapes">
                    <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
                    <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
                </filesystem>
                <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
                <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
                <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
                <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
                <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
                <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
                </chain>
            </resolvers>
            </ivysettings>
            ``` 

    2. Inicie la consola de Gremlin `bin/gremlin.sh`
        
    3. Instale el complemento Spark-Gremlin con la versión 3.3.0-SNAPSHOT que hemos compilado en los pasos anteriores.

        ```bash
        $ bin/gremlin.sh

                \,,,/
                (o o)
        -----oOOo-(3)-oOOo-----
        plugin activated: tinkerpop.server
        plugin activated: tinkerpop.utilities
        plugin activated: tinkerpop.tinkergraph
        gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
        ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
        gremlin> :q
        $ bin/gremlin.sh

                \,,,/
                (o o)
        -----oOOo-(3)-oOOo-----
        plugin activated: tinkerpop.server
        plugin activated: tinkerpop.utilities
        plugin activated: tinkerpop.tinkergraph
        gremlin> :plugin use tinkerpop.spark
        ==>tinkerpop.spark activated
        ```

    4. Si `Hadoop-Gremlin` se activa con `:plugin list`, se debe deshabilitar este complemento ya que podría interferir con el complemento Spark-Gremlin `:plugin unuse tinkerpop.hadoop`

## <a name="prepare-tinkerpop3-dependencies"></a>Preparación de las dependencias de TinkerPop3

Cuando compilamos TinkerPop3 en el paso anterior, también se extrajeron todas las dependencias de archivos JAR para Spark y Hadoop en el directorio de destino. Usaremos los archivos JAR preinstalados con HDI y solo extraeremos las dependencias adicionales cuando sea necesario.

    1. Vaya al directorio de destino de la consola de Gremlin en `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone`. 

    2. Desde aquí, mueva todos los archivos JAR de `ext/` a `lib/`: `find ext/ -name '*.jar' -exec mv {} lib/ \;`

    3. Quite todas las bibliotecas JAR de `lib/` que no estén en la siguiente lista.

        ```bash
        # TinkerPop3
        gremlin-core-3.3.0-SNAPSHOT.jar       
        gremlin-groovy-3.3.0-SNAPSHOT.jar     
        gremlin-shaded-3.3.0-SNAPSHOT.jar     
        hadoop-gremlin-3.3.0-SNAPSHOT.jar     
        spark-gremlin-3.3.0-SNAPSHOT.jar      
        tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

        # Gremlin depedencies
        asm-3.2.jar                                
        avro-1.7.4.jar                             
        caffeine-2.3.1.jar                         
        cglib-2.2.1-v20090111.jar                  
        gbench-0.4.3-groovy-2.4.jar                
        gprof-0.3.1-groovy-2.4.jar                 
        groovy-2.4.9-indy.jar                      
        groovy-2.4.9.jar                           
        groovy-console-2.4.9.jar                   
        groovy-groovysh-2.4.9-indy.jar             
        groovy-json-2.4.9-indy.jar                 
        groovy-jsr223-2.4.9-indy.jar               
        groovy-sql-2.4.9-indy.jar                  
        groovy-swing-2.4.9.jar                     
        groovy-templates-2.4.9.jar                 
        groovy-xml-2.4.9.jar                       
        hadoop-yarn-server-nodemanager-2.7.2.jar   
        hppc-0.7.1.jar                             
        javatuples-1.2.jar                         
        jaxb-impl-2.2.3-1.jar                      
        jbcrypt-0.4.jar                            
        jcabi-log-0.14.jar                         
        jcabi-manifests-1.1.jar                    
        jersey-core-1.9.jar                        
        jersey-guice-1.9.jar                       
        jersey-json-1.9.jar                        
        jettison-1.1.jar                           
        scalatest_2.11-2.2.6.jar                   
        servlet-api-2.5.jar                        
        snakeyaml-1.15.jar                         
        unused-1.0.0.jar                           
        xml-apis-1.3.04.jar                        
        ```

## <a name="get-the-cosmos-db-spark-connector"></a>Obtención del conector de Cosmos DB Spark

1. Obtenga el conector de Cosmos DB Spark `azure-documentdb-spark-0.0.3-SNAPSHOT.jar` y el SDK de Cosmos DB Java `azure-documentdb-1.10.0.jar` en la página del [conector de Azure Cosmos DB Spark en Github](https://github.com/Azure/azure-documentdb-spark/tree/master/releases/azure-documentdb-spark-0.0.3_2.0.2_2.11).

2. También puede compilarlo localmente. Dado que la versión más reciente de Spark-Gremlin se generó con Spark 1.6.1 y no es compatible la versión Spark 2.0.2 que actualmente se usa en el conector de Spark Cosmos DB, puede compilar el código de TinkerPop3 más reciente e instalar manualmente los archivos JAR.

    1. Clone el conector de Cosmos DB Spark.

    2. Compile TinkerPop3 (ya se hizo en pasos anteriores). Instale todos los archivos JAR de TinkerPop 3.3.0-SNAPSHOT localmente
    
    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    2. Actualice `tinkerpop.version` `azure-documentdb-spark/pom.xml` a `3.3.0-SNAPSHOT`
    
    3. Compile con Maven. Los archivos JAR necesarios se colocan en `target` y `target/alternateLocation`

    ```bash
    git clone https://github.com/Azure/azure-documentdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. Copie los archivos JAR mencionados anteriormente en un directorio local en ~/azure-documentdb-spark.

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>Distribuya las dependencias en los nodos de trabajo de Spark 

1. Puesto que la transformación de datos de gráficos depende de TinkerPop3, es necesario distribuir las dependencias relacionadas a todos los nodos de trabajo de Spark.

2. Copie las dependencias de Gremlin mencionadas anteriormente, los archivos JAR del conector de CosmosDB Spark y el SDK de CosmosDB Java en los nodos de trabajo, de la siguiente manera:

    1. Copie todos los archivos JAR en `~/azure-documentdb-spark`

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    2. Obtenga la lista de todos los nodos de trabajo de Spark, que se encuentra en el panel de Ambari, en la lista `Spark2 Clients` de la sección `Spark2`.

    3. Copie ese directorio en cada uno de los nodos.

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>Configuración de las variables de entorno

1. Busque la versión HDP del clúster de Spark; es el nombre del directorio en `/usr/hdp/`, por ejemplo "2.5.4.2-7".

2. Establezca hdp.version para todos los nodos: en el panel de Ambari, vaya a `YARN section -> Configs -> Advanced`. En `Custom yarn-site`, agregue una nueva propiedad `hdp.version` con el valor de la versión de HDP en el nodo principal. Guardar las configuraciones; verá advertencias pero se pueden pasar por alto. Después, reinicie los servicios YARN y Oozie tal y como indican los iconos de notificación.

3. Establezca las siguientes variables de entorno en el nodo principal (reemplace los valores según corresponda):

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>Preparación de la configuración de gráficos

1. Cree un archivo de configuración con los parámetros de conexión de Cosmos DB y la configuración de Spark, y colóquelo en `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties`.

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark Connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. Actualice `spark.driver.extraClassPath` y `spark.executor.extraClassPath` para incluir el directorio de los archivos JAR que se han distribuido en el paso anterior; en este caso, `/home/sshuser/azure-documentdb-spark/*`.

3. Rellene los detalles siguientes para Cosmos DB:

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-tinkerpops-graph-and-save-to-cosmos-db"></a>Cargue el gráfico de TinkerPop y guárdelo en Cosmos DB.
Para demostrar cómo se puede conservar un gráfico en Cosmos DB, vamos a usar como ejemplo el gráfico moderno predefinido de TinkerPop. El gráfico se almacenó en formato de Kryo y se proporcionó en el repositorio de tinkerpop.

1. Como Gremlin se ejecuta en modo YARN, es necesario que los datos del gráfico estén disponibles en el sistema de archivos de Hadoop. Los comandos siguientes crean un directorio y copian el archivo de gráfico local en él. 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. Actualice temporalmente el archivo `gremlin-spark.properties` para que use `GryoInputFormat` para leer el gráfico. También indicamos `inputLocation` como el directorio creamos, similar al siguiente:

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

2. Inicie la consola de Gremlin y cree estos pasos de proceso para conservar los datos en la colección de Cosmos DB configurada:  

    1. Cree el gráfico `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`

    2. Use SparkGraphComputer para escribir: `graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

3. En el Explorador de datos, podemos comprobar que los datos se han ingerido en Cosmos DB.

## <a name="load-the-graph-from-cosmos-db-and-run-gremlin-queries"></a>Carga del gráfico de Cosmos DB y ejecución de las consultas de Gremlin

1. Para cargar el gráfico, edite `gremlin-spark.properties` para establecer `graphReader` en `DocumentDBInputRDD`:

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. Siga estos pasos para cargar el gráfico, recorrer los datos y ejecutar consultas de Gremlin con él:

    1. Inicie la consola de Gremlin `bin/gremlin.sh`

    2. Cree el gráfico con las configuraciones `graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')`

    3. Cree un recorrido de gráfico con SparkGraphComputer `g = graph.traversal().withComputer(SparkGraphComputer)`

    4. Ejecute las consultas de gráfico de Gremlin:

        ```bash
        gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
        ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
        gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
        ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
        gremlin> g.V().count()
        ==>6
        gremlin> g.E().count()
        ==>6
        gremlin> g.V(1).out().values('name')
        ==>josh
        ==>vadas
        ==>lop
        gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
        ==>josh
        ==>peter
        ==>vadas
        ==>marko
        gremlin> g.V().hasLabel('person').
               choose(values('name')).
                 option('marko', values('age')).
                 option('josh', values('name')).
                 option('vadas', valueMap()).
                 option('peter', label())
        ==>josh
        ==>person
        ==>[name:[vadas],age:[27]]
        ==>29
        ```

> [!NOTE]
> Para ver un registro más detallado, configure el nivel de registro de `conf/log4j-console.properties` en un nivel más detallado.
>

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo trabajar con gráficos usando Azure Cosmos DB con Spark.

> [!div class="nextstepaction"]

