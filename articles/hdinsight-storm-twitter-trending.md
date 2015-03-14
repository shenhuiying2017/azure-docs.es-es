<properties
   pageTitle="Temas más destacados de Twitter con Apache Storm en HDInsight | Azure"
   description="Aprenda a usar Trident para crear una topología de Storm que determine los temas más destacados basados en hashtags."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Los temas más destacados de Twitter con Apache Storm en HDInsight

Aprenda a usar Trident para crear una topología de Storm que determine los temas más destacados (hashtags) en Twitter. 

Trident es una abstracción de alto nivel que proporciona herramientas como uniones, agregaciones, agrupaciones, funciones y filtros. Además, Trident agrega primitivas para realizar el procesamiento incremental, con estado. En este ejemplo se muestra cómo crear una topología con un spout, función y varias funciones integradas personalizadas proporcionadas por Trident.

> [AZURE.NOTE] Esto se basa principalmente en el ejemplo [storm-trident](https://github.com/jalonsoramos/trident-storm) de Juan Alonso.

## Requisitos

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java y el JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Una cuenta de desarrollador de Twitter

## Descarga del proyecto

Utilice lo siguiente para clonar el proyecto de forma local.

	git clone https://github.com/Blackmist/TwitterTrending

## Topología

La topología de este ejemplo es la siguiente:

![topology](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE] Se trata de una vista simplificada de la topología, varias instancias de los componentes se distribuirán entre los nodos del clúster.

El código de Trident que implementa la topología es como sigue:

	topology.newStream("spout", spout)
	    .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
	    .groupBy(new Fields("hashtag"))
	    .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
	    .newValuesStream()
	    .applyAssembly(new FirstN(10, "count"))
		.each(new Fields("hashtag", "count"), new Debug());

Hace lo siguiente:

1. Cree una nueva secuencia desde el spout. El spout recupera los tweets de Twitter, filtrados por palabras clave específicas: love, music y coffee en este ejemplo.

2. Se utiliza HashtagExtractor, una función personalizada, para extraer hashtags de cada tweet. Estos se envían a la secuencia.

3. La secuencia se agrupa por hashtag y se pasa a un agregador. Este agregador crea un recuento de cuántas veces ha ocurrido cada hashtag, que se mantiene en memoria. Por último, se genera una nueva secuencia que contiene el hashtag y el recuento.

4. Puesto que solo estamos interesados en los hashtags más populares para un lote determinado de tweets, se aplica el ensamblado FirstN para devolver solo los 10 valores superiores basados en el campo de recuento.

> [AZURE.NOTE] Además del spout y HashtagExtractor, estamos utilizando la funcionalidad integrada de Trident.
> 
> Para obtener información sobre las operaciones integradas, consulte <a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">storm.trident.operation.builtin</a>.
> 
> Para las implementaciones de Trident-state que no sea MemoryMapState, consulte lo siguiente:
> 
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">https://github.com/fhussonnois/storm-trident-elasticsearch</a>
> 
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">https://github.com/kstyrc/trident-redis</a>

### El spout

El spout **TwitterSpout** utiliza <a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a> para recuperar tweets de Twitter. Se crea un filtro (love, music y coffee,) y los tweets entrantes (estado) que coinciden con el filtro se almacenan en un <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">LinkedBlockingQueue</a>. Por último, los elementos se sacan de la cola y se envían a la topología.

### El HashtagExtractor

Para extraer hashtags, se utiliza <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a> para recuperar todos los hashtags contenidos en el tweet. Después se envían a la secuencia.

## Habilitación de Twitter

Utilice los pasos siguientes para registrar una nueva aplicación de Twitter y obtener la información del token de acceso y de consumidor necesario para leer desde Twitter.

1. Vaya a <a href="" target="_blank">https://apps.twitter.com/</a> y utilice el botón **Crear nueva aplicación**. Al rellenar el formulario, deje el valor de **URL de devolución de llamada** vacío.

2. Una vez creada la aplicación, seleccione la pestaña **Claves y tokens de acceso**.

3. Copie la información de **Clave de consumidor** y **Secreto de consumidor**.

4. En la parte inferior de la página, seleccione **Crear mi token de acceso** si no existen tokens. Una vez creados los tokens, copie la información del **token de acceso** y del **secreto del token de acceso**.

5. En el proyecto **TwitterSpoutTopology** anteriormente clonado, abra el archivo **resources/twitter4j.properties** y agregue la información recopilada en los pasos anteriores y, a continuación, guarde el archivo.

## Generación de la topología

Utilice lo siguiente para generar el proyecto.

		cd [directoryname]
		mvn compile

## Prueba de la topología

Use el comando siguiente para probar localmente la topología.

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

Una vez iniciada la topología, debería ver la información de depuración que contiene los hashtags y recuentos enviados por la topología. La salida debe ser similar a la siguiente:

	DEBUG: [Quicktellervalentine, 7]
	DEBUG: [GRAMMYs, 7]
	DEBUG: [AskSam, 7]
	DEBUG: [poppunk, 1]
	DEBUG: [rock, 1]
	DEBUG: [punkrock, 1]
	DEBUG: [band, 1]
	DEBUG: [punk, 1]
	DEBUG: [indonesiapunkrock, 1]

## Pasos siguientes

Ahora que ha probado localmente la topología, descubra cómo [implementar esta topología en Storm en HDInsight](../hdinsight-storm-deploy-monitor-topology/).

También se puede interesar en los siguientes temas de Storm:

* [Desarrollo de las topologías de Java para Storm en HDInsight con Maven](../hdinsight-storm-develop-java-topology/)

* [Desarrollo de las topologías de C# para Storm en HDInsight con Visual Studio](../hdinsight-storm-develop-csharp-visual-studio-topology/)
<!--HONumber=45--> 
