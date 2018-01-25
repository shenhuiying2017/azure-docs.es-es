---
title: "Optimizar las configuraciones de clúster con Ambari - Azure HDInsight | Microsoft Docs"
description: "Use la interfaz de usuario web de Ambari para configurar y optimizar clústeres de HDInsight."
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: ashish
ms.openlocfilehash: 74c1b3298cd7b6ffd5b4a60e2fa78ed733232f92
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="use-ambari-to-optimize-hdinsight-cluster-configurations"></a>Usar Ambari para optimizar configuraciones de clúster de HDInsight

HDInsight proporciona clústeres de Apache Hadoop para aplicaciones de procesamiento de datos a gran escala. La administración, la supervisión y la optimización de estos clústeres complejos de varios nodos pueden ser un reto. [Apache Ambari](http://ambari.apache.org/) es una interfaz web para administrar y supervisar clústeres de HDInsight Linux.  Para los clústeres de Windows, use la [API de REST](hdinsight-hadoop-manage-ambari-rest-api.md) de Ambari.

Para obtener una introducción al uso de la interfaz de usuario web de Ambari, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md).

Inicie sesión en Ambari en `https://CLUSTERNAME.azurehdidnsight.net` con las credenciales del clúster. La pantalla inicial muestra un panel de información general.

![Panel de Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

La interfaz de usuario web de Ambari puede usarse para administrar hosts, servicios, alertas, configuraciones y vistas. Ambari no puede usarse para crear un clúster de HDInsight, actualizar servicios, administrar pilas y versiones, retirar o reordenar hosts ni agregar servicios al clúster.

## <a name="manage-your-clusters-configuration"></a>Administrar la configuración del clúster

Las opciones de configuración ayudan a optimizar un servicio determinado. Para modificar la configuración de un servicio, seleccione el servicio de la barra lateral **Servicios** (a la izquierda) y, a continuación, navegue hasta la pestaña **Configs** (Configuraciones) en la página de detalles de servicio.

![Barra lateral Servicios](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Modificar el tamaño del montón de Java NameNode

El tamaño del montón de Java NameNode depende de muchos factores, como la carga en el clúster, el número de archivos y el número de bloques. El tamaño predeterminado de 1 GB funciona bien con la mayoría de los clústeres, aunque algunas cargas de trabajo pueden requerir más o menos memoria. 

Para modificar el tamaño del montón de Java NameNode:

1. Seleccione **HDFS** de la barra lateral Servicios y navegue hasta la pestaña **Configs** (Configuraciones).

    ![Configuración de HDFS](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

2. Busque el valor **NameNode Java heap size** (Tamaño del montón de Java NameNode). También puede usar el cuadro de texto de **filtro** para escribir y buscar un valor determinado. Seleccione el icono de **lápiz** junto al nombre del valor.

    ![Tamaño del montón de Java NameNode](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

3. Escriba el nuevo valor en el cuadro de texto y, a continuación, presione **ENTRAR** para guardar el cambio.

    ![Editar el tamaño del montón de Java NameNode](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

4. El tamaño del montón de Java NameNode ha cambiado de 2 GB a 1 GB.

    ![Tamaño del montón de Java NameNode editado](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

5. Para guardar los cambios, haga clic en el botón **Guardar** verde de la parte superior de la pantalla de configuración.

    ![Guardar cambios](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="hive-optimization"></a>Optimización de Hive

En las siguientes secciones se describen las opciones de configuración para optimizar el rendimiento general de Hive.

1. Para modificar los parámetros de configuración de Hive, seleccione **Hive** de la barra lateral Servicios.
2. Navegue hasta la pestaña **Configs** (Configuraciones).

### <a name="set-the-hive-execution-engine"></a>Establecer el motor de ejecución de Hive

Hive proporciona dos motores de ejecución: MapReduce y Tez. Tez es más rápido que MapReduce. Tez es el motor de ejecución predeterminado de los clústeres de HDInsight Linux. Para cambiar el motor de ejecución:

1. En la pestaña **Configs** (Configuraciones) de Hive, escriba **motor de ejecución** en el cuadro de filtro.

    ![Motor de ejecución de búsqueda](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

2. El valor predeterminado de la propiedad **Optimización** es **Tez**.

    ![Optimización: Tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>Optimizar asignadores

Hadoop intenta dividir (*asignar*) un único archivo en varios archivos y procesar los archivos resultantes en paralelo. El número de asignadores depende del número de divisiones. Los siguientes dos parámetros de configuración controlan el número de divisiones del motor de ejecución Tez:

* `tez.grouping.min-size`: límite inferior en el tamaño de una división agrupada, con un valor predeterminado de 16 MB (16.777.216 bytes).
* `tez.grouping.max-size`: límite inferior en el tamaño de una división agrupada, con un valor predeterminado de 1 GB (1 073 741 824 bytes).

Como regla general de rendimiento, disminuya estos dos parámetros para mejorar la latencia y aumentar el rendimiento.

Por ejemplo, para establecer cuatro tareas de asignador para un tamaño de datos de 128 MB, debe establecer cada uno de estos parámetros en 32 MB (33 554 432 bytes).

1. Para modificar los parámetros de límite, navegue a la pestaña **Configs** (Configuraciones) del servicio Tez. Expanda el panel **General** y busque los parámetros `tez.grouping.max-size` y `tez.grouping.min-size`.

2. Establezca ambos parámetros en **33 554 432** bytes (32 MB).

    ![Tamaños de agrupación de Tez](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
Estos cambios afectan a todos los trabajos de Tez en el servidor. Para obtener un resultado óptimo, elija valores de parámetro adecuados.

### <a name="tune-reducers"></a>Optimizar reductores

ORC y Snappy ofrecen un alto rendimiento. Sin embargo, es posible que Hive tenga muy pocos reductores de forma predeterminada, y provoque cuellos de botella.

Por ejemplo, supongamos que tiene un tamaño de datos de entrada de 50 GB. Esos datos en formato ORC con compresión Snappy tiene un tamaño de 1 GB. Hive estima el número de reductores necesarios en: (número de bytes de entrada para asignadores/`hive.exec.reducers.bytes.per.reducer`).

Con la configuración predeterminada, este ejemplo es de 4 reductores.

El parámetro `hive.exec.reducers.bytes.per.reducer` especifica el número de bytes procesados por reductor. El valor predeterminado es de 64 MB. Ajustar este valor a la baja aumenta el paralelismo y puede mejorar el rendimiento. Ajustarlo demasiado a la baja también podría generar demasiados reductores y afectar negativamente al rendimiento. Este parámetro se basa en sus requisitos de datos específicos, la configuración de compresión y otros factores del entorno.

1. Para modificar el parámetro, navegue hasta la pestaña **Configs** (Configuraciones) de Hive y busque el parámetro **Data per Reducer** (Datos por reductor) en la página Configuración.

    ![Datos por reductor](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
2. Seleccione **Editar** para modificar el valor a 128 MB (134 217 728 bytes) y, a continuación, presione **ENTRAR** para guardar los cambios.

    ![Datos por reductor: editado](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Dado un tamaño de entrada de 1024 MB, con 128 MB de datos por reductor, hay 8 reductores (1024/128).

3. Un valor incorrecto para el parámetro **Data per Reducer** (Datos por reductor) puede dar lugar a un gran número de reductores, lo que afectaría negativamente al rendimiento de las consultas. Para limitar el número máximo de reductores, establezca `hive.exec.reducers.max` en un valor adecuado. El valor predeterminado es 1009.

### <a name="enable-parallel-execution"></a>Habilitar la ejecución en paralelo

Una consulta de Hive se ejecuta en una o varias fases. Si las fases independientes se pueden ejecutar en paralelo, aumentará el rendimiento de las consultas.

1.  Para habilitar la ejecución de consultas en paralelo, navegue hasta la pestaña **Configs** (Configuraciones) de Hive y busque la propiedad `hive.exec.parallel`. El valor predeterminado es false. Cambie el valor a true y, a continuación, presione **ENTRAR** para guardar el valor.
 
2.  Para limitar el número de trabajos que se van a ejecutar en paralelo, modifique la propiedad `hive.exec.parallel.thread.number`. El valor predeterminado es 8.

    ![Ejecución de Hive en paralelo](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>Habilitar la vectorización

Hive procesa los datos fila por fila. La vectorización dirige Hive para que procese los datos en bloques de 1024 filas en lugar de hacerlo fila por fila. La vectorización solo es aplicable al formato de archivo ORC.

1. Para habilitar la ejecución de consultas vectorizadas, navegue hasta la pestaña **Configs** (Configuraciones) de Hive y busque el parámetro `hive.vectorized.execution.enabled`. El valor predeterminado es true para Hive 0.13.0 o posterior.
 
2. Para habilitar la ejecución vectorizada para el lado de reducción de la consulta, establezca el parámetro `hive.vectorized.execution.reduce.enabled` en true. El valor predeterminado es false.

    ![Ejecución vectorizada de Hive](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Habilitar la optimización basada en costos (CBO)

De forma predeterminada, Hive sigue un conjunto de reglas para buscar un plan de ejecución de consultas óptimo. La optimización basada en costos (CBO) evalúa varios planes para ejecutar una consulta y asigna un costo a cada plan; a continuación, determina el plan más económico para ejecutar una consulta.

Para habilitar CBO, navegue hasta la pestaña **Configs** (Configuraciones) de Hive y busque `parameter hive.cbo.enable`; a continuación, cambie el botón de alternancia a **Activado**.

![Configuración de CBO](./media/hdinsight-changing-configs-via-ambari/cbo.png)

Los siguientes parámetros de configuración adicionales aumentan el rendimiento de las consulta de Hive cuando CBO está habilitado:

* `hive.compute.query.using.stats`

    Si se establece en true, Hive usa las estadísticas almacenadas en su Metastore para responder a consultas simples como `count(*)`.

    ![Estadísticas de CBO](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Las estadísticas de columnas se crean cuando se habilita CBO. Hive usa estadísticas de columnas, que se almacenan en Metastore, para optimizar las consultas. Capturar estadísticas de columnas para cada columna tarda más tiempo cuando el número de columnas es elevado. Si se establece en false, este valor deshabilita la captura de estadísticas de columnas de Metastore.

    ![Estadísticas de columnas del conjunto de estadísticas de Hive](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Las estadísticas de partición básicas, como el número de filas, el tamaño de datos y el tamaño de archivo se almacenan en Metastore. Si se establece en true, las estadísticas de partición se capturan de Metastore. Si se establece en false, el tamaño de archivo se captura del sistema de archivos y el número de filas, del esquema de filas.

    ![Estadísticas de partición del conjunto de estadísticas de Hive](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Habilitar la compresión intermedia

Las tareas de asignación crean archivos intermedios que se usan en las tareas de reductor. La compresión intermedia reduce el tamaño de los archivos intermedios.

Los trabajos de Hadoop suelen ser un cuello de botella de E/S. La compresión de datos puede acelerar la E/S y transferencia de red en general.

Los tipos de compresión disponibles son:

| Formato | Herramienta | Algoritmo | Extensión de archivo | ¿Divisible? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | DEFLATE | .gz | Sin  |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Sí |
| LZO | Lzop | LZO | .lzo | Sí, si está indexado |
| Snappy | N/D | Snappy | Snappy | Sin  |

Como norma general, es importante tener un método de compresión divisible, ya que, de lo contrario, se crearán muy pocos asignadores. Si los datos de entrada son texto, `bzip2` es la mejor opción. Para el formato ORC, Snappy es la opción de compresión más rápida.

1. Para habilitar la compresión intermedia, navegue hasta la pestaña **Configs** (Configuraciones) de Hive y, a continuación, establezca el parámetro `hive.exec.compress.intermediate` en true. El valor predeterminado es false.

    ![Compresión de ejecución de Hive intermedia](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]
    > Para comprimir archivos intermedios, elija un códec de compresión con un costo de CPU más bajo, aunque el códec no tenga una salida de compresión alta.

2. Para establecer el códec de compresión intermedio, agregue la propiedad personalizada `mapred.map.output.compression.codec` al archivo `hive-site.xml` o `mapred-site.xml`.

3. Para agregar un valor personalizado:

    a. Navegue hasta la pestaña **Configs** (Configuraciones) de Hive y seleccione la pestaña **Avanzadas**.

    b. En la pestaña **Avanzadas**, busque y expanda el panel **Custom hive-site** (Sitio de Hive personalizado).

    c. Haga clic en el vínculo **Agregar propiedad** en la parte inferior del panel Custom hive-site (Sitio de Hive personalizado).

    d. En la ventana Agregar propiedad, escriba `mapred.map.output.compression.codec` como la clave y `org.apache.hadoop.io.compress.SnappyCodec` como el valor.

    e. Haga clic en **Agregar**.

    ![Propiedad personalizada de Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Esta propiedad comprimirá el archivo intermedio mediante la compresión Snappy. Una vez agregada, la propiedad aparece en el panel Custom hive-site (Sitio de Hive personalizado).

    > [!NOTE]
    > Este procedimiento modifica el archivo `$HADOOP_HOME/conf/hive-site.xml`.

### <a name="compress-final-output"></a>Comprimir la salida final

También es posible comprimir la salida final de Hive.

1. Para habilitar la salida final de Hive, navegue hasta la pestaña **Configs** (Configuraciones) de Hive y, a continuación, establezca el parámetro `hive.exec.compress.output` en true. El valor predeterminado es false.

2. Para elegir el códec de compresión de salida, agregue la propiedad personalizada `mapred.output.compression.codec` al panel Custom hive-site (Sitio de Hive personalizado), como se describe en el paso 3 de la sección anterior.

    ![Propiedad personalizada de Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Habilitar de ejecución especulativa

La ejecución especulativa inicia un número determinado de tareas duplicadas con el fin de detectar y generar una lista negra del seguimiento de las tareas de ejecución lenta, al tiempo que mejora la ejecución del trabajo general con la optimización de los resultados de las tareas individuales.

La ejecución especulativa no debe activarse para las tareas MapReduce de ejecución prolongada con grandes cantidades de entradas.

* Para habilitar la ejecución especulativa, navegue hasta la pestaña **Configs** (Configuraciones) de Hive y, a continuación, establezca el parámetro `hive.mapred.reduce.tasks.speculative.execution` en true. El valor predeterminado es false.

    ![La propiedad mapred de Hive reduce la ejecución especulativa de tareas](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Optimizar las particiones dinámicas

Hive permite crear particiones dinámicas al insertar registros en una tabla, sin predefinir todas y cada una de las particiones. Se trata de una característica muy eficaz, aunque puede dar como resultado la creación de un gran número de particiones y un gran número de archivos para cada partición.

1. Para que Hive realice particiones dinámicas, el valor del parámetro `hive.exec.dynamic.partition` debe ser true (valor predeterminado).

2. Cambie el modo de partición dinámica a *strict*. En modo strict, al menos una partición debe ser estática. Esto impide consultas sin filtro de partición en la cláusula WHERE, es decir, *strict* impide las consultas que examinan todas las particiones. Navegue hasta la pestaña **Configs** (Configuraciones) de Hive y, a continuación, establezca `hive.exec.dynamic.partition.mode` en **strict**. El valor predeterminado es **nonstrict**.
 
3. Para limitar el número de particiones dinámicas que se va a crear, modifique el parámetro "hive.exec.max.dynamic.partitions". El valor predeterminado es 5000.
 
4. Para limitar el número total de particiones dinámicas por nodo, modifique `hive.exec.max.dynamic.partitions.pernode`. El valor predeterminado es 2000.

### <a name="enable-local-mode"></a>Habilitar el modo local

El modo local permite que Hive realice todas las tareas de un trabajo en una única máquina o, a veces, en un único proceso. Esto mejora el rendimiento de las consultas si los datos de entrada son pequeños y la sobrecarga que supone iniciar tareas para las consultas consume un porcentaje significativo de la ejecución de consultas general.

Para habilitar el modo local, agregue el parámetro `hive.exec.mode.local.auto` al panel Custom hive-site (Sitio de Hive personalizado), como se explica en el paso 3 de la sección [Habilitar la compresión intermedia](#enable-intermediate-compression).

![Modo de ejecución de Hive local automático](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Establecer una consulta MultiGROUP BY de MapReduce única

Cuando esta propiedad está establecida en true, una consulta MultiGROUP BY con claves de agrupación comunes genera un único trabajo de MapReduce.  

Para habilitar el comportamiento, agregue el parámetro `hive.multigroupby.singlereducer` al panel Custom hive-site (Sitio de Hive personalizado), como se explica en el paso 3 de la sección [Habilitar la compresión intermedia](#enable-intermediate-compression).

![Establecer una consulta MultiGROUP BY de MapReduce única de Hive](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Optimizaciones de Hive adicionales

En las secciones siguientes se describen las optimizaciones adicionales relacionadas con Hive que puede establecer.

#### <a name="join-optimizations"></a>Unir optimizaciones

El tipo de unión predeterminado en Hive es una *unión aleatoria*. En Hive, asignadores especiales leen la entrada y emiten un par de clave/valor de unión a un archivo intermedio. Hadoop ordena y combina estos pares en una fase aleatoria. Esta fase aleatoria es costosa. Al seleccionar la unión correcta en función de los datos puede mejorar significativamente el rendimiento.

| Tipo de unión | Si | Cómo | Configuración de Hive | Comentarios |
| -- | -- | -- | -- | -- |
| Unión aleatoria | <ul><li>Opción predeterminada</li><li>Siempre funciona</li></ul> | <ul><li>Lee parte de una de las tablas</li><li>Cubos y ordenaciones por clave de unión</li><li>Envía un cubo a cada elemento reduce</li><li>La unión se realiza en el elemento Reduce</li></ul> | No se requiere ninguna configuración de Hive importante | Funciona siempre |
| Unión de asignación | <ul><li>Una tabla puede caber en memoria</li></ul> | <ul><li>Lee una tabla pequeña en la tabla de hash en memoria</li><li>Transmite parte del archivo de gran tamaño</li><li>Une todos los registros de la tabla de hash</li><li>Las uniones las realiza el asignador solamente</li></ul> | `hive.auto.confvert.join=true` | Acción muy rápida, pero limitada |
| Ordenar el cubo de fusión mediante combinación | Si ambas tablas están: <ul><li>Ordenadas igual</li><li>Incluidas en cubos iguales</li><li>Unión de la columna/incluida en cubos</li></ul> | Cada proceso: <ul><li>Lee un cubo de cada tabla.</li><li>Procesa la fila con el valor más bajo.</li></ul> | `hive.auto.convert.sortmerge.join=true` | Muy eficiente |

#### <a name="execution-engine-optimizations"></a>Optimizaciones del motor de ejecución

Recomendaciones adicionales para optimizar el motor de ejecución de Hive:

| Configuración | Recomendado | Valor predeterminado de HDInsight |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | True = más seguro y más lento; false = más rápido | false |
| `tez.am.resource.memory.mb` | Límite superior de 4 GB para la mayor parte | Ajustado automáticamente |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="pig-optimization"></a>Optimización de Pig

Las propiedades de Pig se pueden modificar desde la interfaz de usuario web de Ambari para optimizar las consultas de Pig. La modificación de las propiedades de Pig desde Ambari directamente modifica las propiedades de Pig en el archivo `/etc/pig/2.4.2.0-258.0/pig.properties`.

1. Para modificar las propiedades de Pig, navegue hasta la pestaña **Configs** (Configuraciones) de Pig y, a continuación, expanda el panel **Advanced pig-properties** (Propiedades de Pig avanzadas).

2. Busque, quite la marca de comentario y cambie el valor de la propiedad que quiera modificar.

3. Seleccione **Guardar** en la parte superior derecha de la ventana para guardar el nuevo valor. Algunas propiedades pueden requerir un reinicio del servicio.

    ![Propiedades de Pig avanzadas](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]
> Cualquier configuración de nivel de sesión reemplaza los valores de propiedad en el archivo `pig.properties`.

### <a name="tune-execution-engine"></a>Optimizar el motor de ejecución

Existen dos motores de ejecución disponibles para ejecutar scripts de Pig: MapReduce y Tez. Tez es un motor optimizado y es mucho más rápido que MapReduce.

1. Para modificar el motor de ejecución, en el panel **Advanced pig-properties** (Propiedades de Pig avanzadas), busque la propiedad `exectype`.

2. El valor predeterminado es **MapReduce**. Cámbielo a **Tez**.


### <a name="enable-local-mode"></a>Habilitar el modo local

De manera similar a Hive, el modo local se usa para acelerar los trabajos con cantidades de datos relativamente más pequeñas.

1. Para habilitar el modo local, establezca `pig.auto.local.enabled` en **true**. El valor predeterminado es false.

2. Los trabajos con un tamaño de datos de entrada inferior al valor de propiedad `pig.auto.local.input.maxbytes` se consideran trabajos pequeños. El valor predeterminado es 1 GB.


### <a name="copy-user-jar-cache"></a>Copiar la caché de archivos JAR usuario

Pig copia los archivos JAR que requieren los formatos UDF en una caché distribuida para estar disponibles para los nodos de tarea. Estos archivos JAR no cambian con frecuencia. Si está habilitado, el valor `pig.user.cache.enabled` permite colocar archivos JAR en una caché para volver a usarlos para trabajos ejecutados por el mismo usuario. Esto da como resultado un aumento menor del rendimiento de trabajo.

1. Para habilitarlo, establezca `pig.user.cache.enabled` en true. El valor predeterminado es false.

2. Para establecer la ruta de acceso base de los archivos JAR en caché, establezca `pig.user.cache.location` en la ruta de acceso base. El valor predeterminado es `/tmp`.


### <a name="optimize-performance-with-memory-settings"></a>Optimizar el rendimiento con la configuración de memoria

La configuración de memoria siguiente puede ayudar a optimizar el rendimiento de los scripts de Pig.

* `pig.cachedbag.memusage`: cantidad de memoria asignada a un contenedor. Un contenedor es una colección de tuplas. Una tupla es un conjunto ordenado de campos y un campo es un fragmento de datos. Si los datos de un contenedor están fuera de la memoria asignada, se vuelcan en el disco. El valor predeterminado es 0,2, que representa el 20 por ciento de memoria disponible. Esta memoria se comparte entre todos los contenedores en una aplicación.

* `pig.spill.size.threshold`: los contenedores que superan este umbral de tamaño de volcado (en bytes) se vuelcan en el disco. El valor predeterminado es de 5 MB.


### <a name="compress-temporary-files"></a>Comprimir archivos temporales

Pig genera archivos temporales durante la ejecución del trabajo. Comprimir los archivos temporales da como resultado un aumento del rendimiento al leer o escribir archivos en el disco. La configuración siguiente se puede usar para comprimir archivos temporales.

* `pig.tmpfilecompression`: si es true, habilita la compresión de archivos temporales. El valor predeterminado es false.

* `pig.tmpfilecompression.codec`: códec de compresión que se usará para comprimir archivos temporales. Los códecs de compresión recomendadas son LZO y Snappy para usos menores de la CPU.

### <a name="enable-split-combining"></a>Habilitar la combinación de divisiones

Si se habilita esta opción, los archivos pequeños se combinan para menos tareas de asignación. Esto mejora la eficiencia de los trabajos con muchos archivos pequeños. Para habilitarla, establezca `pig.noSplitCombination` en true. El valor predeterminado es false.


### <a name="tune-mappers"></a>Optimizar asignadores

El número de asignadores se controla mediante la modificación de la propiedad `pig.maxCombinedSplitSize`. Especifica el tamaño de los datos que se van a procesar mediante una tarea de asignación única. El valor predeterminado es el tamaño de bloque predeterminado del sistema de archivos. Aumentar este valor produce una disminución del número de tareas de asignador.


### <a name="tune-reducers"></a>Optimizar reductores

El número de reductores se calcula en función del parámetro `pig.exec.reducers.bytes.per.reducer`. El parámetro especifica el número de bytes procesados por reductor, que de manera predeterminada es 1 GB. Para limitar el número máximo de reductores, establezca la propiedad `pig.exec.reducers.max`, cuyo valor predeterminado es 999.


## <a name="hbase-optimization-with-the-ambari-web-ui"></a>Optimización de HBase con la interfaz de usuario web de Ambari

La configuración de HBase se modifica desde la pestaña **Configs** (Configuraciones) de HBase. En las secciones siguientes se describen algunas de las opciones de configuración importantes que afectan al rendimiento de HBase.

### <a name="set-hbaseheapsize"></a>Establecer HBASE_HEAPSIZE

El tamaño del montón de HBase especifica la cantidad máxima del montón que se usará en megabytes en los servidores de *regiones* y *maestro*. El valor predeterminado es de 1000 MB. Debe optimizarse para la carga de trabajo del clúster.

1. Para modificarlo, navegue hasta el panel **Advanced HBase-env** (HBase-env avanzado) en la pestaña **Configs** (Configuraciones) de HBase y, a continuación, busque el valor `HBASE_HEAPSIZE`.

2. Cambie el valor predeterminado a 5000 MB.

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>Optimizar las cargas de trabajo con mucha actividad de lectura

Las siguientes configuraciones son importantes para mejorar el rendimiento de las cargas de trabajo con mucha actividad de lectura.

#### <a name="block-cache-size"></a>Tamaño de la caché de bloques

La caché de bloques es la caché de lectura. Su tamaño lo controla el parámetro `hfile.block.cache.size`. El valor predeterminado es 0,4, que es un 40 por ciento de la memoria total del servidor de regiones. Cuanto mayor sea el tamaño de la caché de bloques, más rápida serán las lecturas aleatorias.

1. Para modificar este parámetro, navegue hasta la pestaña **Configuración** de la pestaña **Configs** (Configuraciones) de HBase y, a continuación, busque **% of RegionServer Allocated to Read Buffers** (% de RegionServer asignado a los búferes de lectura).

    ![Tamaño de la caché de bloques HBase](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
2. Para cambiar el valor, seleccione el icono **Editar**.


#### <a name="memstore-size"></a>Tamaño de Memstore

Todas las ediciones se almacenan en el búfer de memoria, denominado *Memstore*. Esto aumenta la cantidad total de datos que se pueden escribir en el disco en una sola operación y acelera el acceso posterior a los cambios recientes. El tamaño de Memstore se define mediante los dos parámetros siguientes:

* `hbase.regionserver.global.memstore.UpperLimit`: define el porcentaje máximo del servidor de regiones que puede usar Memstore combinado.

* `hbase.regionserver.global.memstore.LowerLimit`: define el porcentaje mínimo del servidor de regiones que puede usar Memstore combinado.

Para optimizarlo para las lecturas aleatorias, puede reducir los límites superior e inferior de Memstore.


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Número de filas recuperadas del examen del disco

El parámetro `hbase.client.scanner.caching` define el número de filas leídas del disco cuando se llama al método `next` en un escáner.  El valor predeterminado es 100. Cuanto mayor sea el número, menos llamadas remotas se realizan del cliente al servidor de regiones, lo que permite exámenes más rápidos. Sin embargo, esto también aumentará la presión de la memoria en el cliente.

![Número de filas recuperadas de HBase](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]
> No establezca el valor de modo que el tiempo transcurrido entre la invocación del método siguiente en un escáner sea mayor que el tiempo de espera del escáner. La duración del tiempo de espera del escáner se define mediante la propiedad `hbase.regionserver.lease.period`.


### <a name="optimize-write-heavy-workloads"></a>Optimizar las cargas de trabajo con mucha actividad de escritura

Las siguientes configuraciones son importantes para mejorar el rendimiento de las cargas de trabajo con mucha actividad de escritura.


#### <a name="maximum-region-file-size"></a>Tamaño de archivo de región máximo

HBase almacena los datos en un formato de archivo interno denominado *HFile*. La propiedad `hbase.hregion.max.filesize` define el tamaño de un solo archivo HFile para una región.  Una región se divide en dos regiones si la suma de todos los archivos HFile de una región es mayor que este valor.
 
![Tamaño de archivo máximo de HRegion de HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Cuanto mayor sea el tamaño del archivo de región, menor será el número de divisiones. Puede aumentar el tamaño de archivo para determinar un valor que produzca el máximo rendimiento de escritura.


#### <a name="avoid-update-blocking"></a>Evitar el bloqueo de actualizaciones

* La propiedad `hbase.hregion.memstore.flush.size` define el tamaño en el que se va a vaciar Memstore en el disco. El tamaño predeterminado es de 128 MB.

* El multiplicador de bloques de región de Hbase se define mediante `hbase.hregion.memstore.block.multiplier`. El valor predeterminado es 4. El máximo permitido es 8 GB.

* HBase bloquea las actualizaciones si Memstore es (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) bytes.

    Con los valores predeterminados de tamaño de vaciado y multiplicador de bloque, las actualizaciones se bloquean cuando Memstore tiene un tamaño de 128 * 4 = 512 MB. Para reducir la actualización de recuento de bloqueo, aumente el valor de `hbase.hregion.memstore.block.multiplier`.

![Multiplicador de bloques de regiones de HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Definir el tamaño de Memstore

El tamaño de Memstore se define mediante los parámetros `hbase.regionserver.global.memstore.UpperLimit` y `hbase.regionserver.global.memstore.LowerLimit`. Establecer estos valores igual reduce las pausas al escribir (lo que también provoca un vaciado más frecuentes) y da como resultado un rendimiento de escritura mayor.


### <a name="set-memstore-local-allocation-buffer"></a>Establecer el búfer de asignación local de Memstore

El uso del búfer de asignación local de Memstore viene determinado por la propiedad `hbase.hregion.memstore.mslab.enabled`. Cuando está habilita (true), impide la fragmentación del montón durante la operación de escritura intensiva. El valor predeterminado es true.
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="next-steps"></a>pasos siguientes

* [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md)
* [API de REST de Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
