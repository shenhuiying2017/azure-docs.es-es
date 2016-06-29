<properties 
	pageTitle="Problemas conocidos de Apache Spark en HDInsight | Microsoft Azure" 
	description="Problemas conocidos de Apache Spark en HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/06/2016" 
	ms.author="nitinme"/>

# Problemas conocidos para el clúster de Apache Spark en HDInsight Linux

En este documento se hace un seguimiento de todos los problemas conocidos de la versión preliminar pública de HDInsight Spark.

##Sesión interactiva con pérdidas de Livy
 
Cuando se reinicia Livy con una sesión interactiva (desde Ambari o debido al reinicio de la máquina virtual del nodo principal 0) activa, se perderá una sesión de trabajo interactiva. Por este motivo, los nuevos trabajos pueden bloquearse en el estado Aceptado y no se pueden iniciar.

**Mitigación:**

Utilice el procedimiento siguiente para encontrar una solución alternativa para el problema:

1. SSH en el nodo principal. 
2. Ejecute el siguiente comando para buscar los identificadores de aplicación de los trabajos interactivos iniciados mediante Livy. 

        yarn application –list

    Los nombres predeterminados de los trabajos serán Livy si se han iniciado con una sesión interactiva de Livy sin nombres explícitos especificados; en caso de que la sesión de Livy se haya iniciado mediante el cuaderno de Jupyter, el nombre del trabajo empezará por remotesparkmagics\_*.

3. Ejecute el comando siguiente para terminar esos trabajos.

        yarn application –kill <Application ID>

Los nuevos trabajos empezarán a ejecutarse.

##No se ha iniciado el servidor de historial de Spark 

El servidor de historial de Spark no se inicia automáticamente después de crear un clúster.

**Mitigación:**

Inicie el servidor de historial manualmente desde Ambari.

## Problemas con permisos en el directorio de registros de Spark 

Cuando hdiuser envía un trabajo con spark-submit, hay un error java.io.FileNotFoundException: /var/log/spark/sparkdriver\_hdiuser.log (permiso denegado) y el registro del controlador no se ha escrito.

**Mitigación:**
 
1. Agregue hdiuser al grupo de Hadoop. 
2. Proporcione 777 permisos en /var/log/spark después de crear el clúster. 
3. Actualice la ubicación del registro spark con Ambari para que sea un directorio con 777 permisos.  
4. Ejecute spark-submit como sudo.  

## Problemas relacionados con los cuadernos de Jupyter Notebook

A continuación, se muestran algunos problemas conocidos relacionados con los cuadernos de Jupyter Notebook.


### Cuadernos que tienen caracteres no ASCII en los nombres de archivo

Los cuadernos de Jupyter Notebook que pueden utilizarse en clústeres Spark de HDInsight no deben tener caracteres que no sean ASCII en los nombres de archivo. Si trata de cargar un archivo a través de la interfaz de usuario de Jupyter con un nombre de archivo que incluye caracteres que no son ASCII, se producirá un error silenciosamente (es decir, Jupyter no permitirá cargar el archivo y tampoco se mostrará un error).

### Error al cargar cuadernos con tamaños mayores

Es posible que aparezca un error **`Error loading notebook`** al cargar cuadernos de mayor tamaño.

**Mitigación:**

El hecho de recibir este error no implica que los datos estén dañados o perdidos. Los cuadernos siguen aún en el disco en `/var/lib/jupyter` y se puede conectar mediante SSH al clúster para acceder ellos. Puede copiar los blocs de notas del clúster en el equipo local (mediante SCP o WinSCP) como copia de seguridad para evitar la pérdida de datos importantes del bloc de notas. A continuación, puede aplicar túneles SSH al nodo principal del puerto 8001 para tener acceso a Jupyter sin pasar por la puerta de enlace. Desde ahí, puede borrar la salida del bloc de notas y volver a guardarla para minimizar el tamaño del bloc de notas.

Para evitar que este error ocurra en el futuro, debe seguir algunos procedimientos recomendados:

* Es importante mantener reducido el tamaño del bloc de notas. Las salidas de los trabajos de Spark que se envían a Jupyter de vuelta se conservan en el bloc de notas. En general, con Jupyter se recomienda evitar ejecutar `.collect()` en RDD o tramas de datos de gran tamaño; en su lugar, si desea inspeccionar el contenido de un RDD, considere la posibilidad de ejecutar `.take()` o `.sample()` para que la salida no sea demasiado grande.
* Además, cuando guarde un bloc de notas, desactive todas las celdas de la salida para reducir el tamaño.

### El primer inicio del cuaderno tarda más de lo esperado 

La primera instrucción del cuaderno de Jupyter con la instrucción mágica de Spark podría tardar más de un minuto.

**Explicación:**
 
Esto sucede cuando se ejecuta la primera celda de código. En segundo plano se inicia la configuración de la sesión y se establecen los contextos de Spark, SQL y Hive. Una vez establecidos estos contextos, se ejecuta la primera instrucción y esto da la impresión de que la instrucción tardó mucho tiempo en completarse.

### Tiempo de espera del cuaderno de Jupyter en la creación de la sesión

Cuando el clúster Spark se está quedando sin recursos, el tiempo de espera de los kernels Spark y Pyspark del cuaderno de Jupyter se agotará al tratar de crear la sesión.

**Mitigaciones:**

1. Libere algunos recursos del clúster Spark de la siguiente forma:

    - Detenga otros cuadernos de Spark en el menú Cerrar y detener o haga clic en Apagar en el explorador del cuaderno.
    - Detenga otras aplicaciones de Spark desde YARN.

2. Reinicie el cuaderno que trataba de iniciar. Debe haber suficientes recursos para crear una sesión ahora.

##Consulte también

* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### Escenarios

* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Creación y ejecución de aplicaciones

* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (Linux)](hdinsight-apache-spark-livy-rest-interface.md)

### Herramientas y extensiones

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applications (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones Scala Spark)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels available for Jupyter notebook in Spark cluster for HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Administración de recursos

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0615_2016-->