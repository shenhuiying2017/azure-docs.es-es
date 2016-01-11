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
	ms.date="12/22/2015" 
	ms.author="jgao"/>

# Problemas conocidos de Apache Spark en HDInsight de Azure (Linux)

En este documento se hace un seguimiento de todos los problemas conocidos de la vista previa pública de Spark.

##Sesión interactiva con pérdidas de Livy
 
**Síntoma:**

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

**Síntoma:**
 
El servidor de historial de Spark no se inicia automáticamente después de crear un clúster.

**Mitigación:**

Inicie el servidor de historial manualmente desde Ambari.

##El primer inicio del cuaderno tarda más de lo esperado 

**Síntoma:**

La primera instrucción del cuaderno de Jupyter con la instrucción mágica de Spark podría tardar más de un minuto.

**Mitigación:**
 
Ninguna solución alternativa. A veces tarda un minuto.

##No se pueden personalizar las configuraciones de núcleos y memoria

**Síntoma:**
 
No se pueden especificar configuraciones de núcleos y memoria diferentes a las predeterminadas de los kernels Spark y Pyspark.

**Mitigación:**
 
Esta característica estará disponible próximamente.

##Tiempo de espera del cuaderno de Jupyter en la creación de la sesión

**Síntoma:**

Cuando el clúster Spark se está quedando sin recursos, el tiempo de espera de los kernels Spark y Pyspark del cuaderno de Jupyter se agotará al tratar de crear la sesión. Mitigaciones:

1. Libere algunos recursos del clúster Spark de la siguiente forma:

    - Detenga otros cuadernos de Spark en el menú Cerrar y detener o haga clic en Apagar en el explorador del cuaderno.
    - Detenga otras aplicaciones de Spark desde YARN.

2. Reinicie el cuaderno que trataba de iniciar. Debe haber suficientes recursos para crear una sesión ahora.

##Problema de formato en los resultados generados en el cuaderno

**Síntoma:**
 
Los resultados generados en el cuaderno presentan un formato incorrecto después de ejecutar una celda desde los kernels Spark y Pyspark de Jupyter. Esto incluye los resultados correctos de las ejecuciones de la celda como rastreos de pila de Spark u otros errores.

**Mitigación:**
 
Este problema se abordará en una versión posterior.

##Errores tipográficos en cuadernos de ejemplo
 
- **Cuaderno de Python 4 (Análisis de registros con Spark mediante una biblioteca personalizada)**

    "Supongamos que lo copia en wasb:///example/data/iislogparser.py" debe ser "Supongamos que lo copia en wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py"”.

- **Cuaderno de Python 5 (Aprendizaje automático de Spark: análisis predictivo de datos de inspección de alimentos mediante MLLib)**

    “Una visualización rápida puede ayudarnos a razonar sobre la distribución de estos resultados” contiene algún código incorrecto que no se ejecutará. Debe editarse como sigue:

        countResults = df.groupBy('results').count().withColumnRenamed('count', 'cnt').collect() 
        labels = [row.results for row in countResults] 
        sizes = [row.cnt for row in countResults] 
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral'] 
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors) plt.axis('equal') 
        
- **Cuaderno de Python 5 (Aprendizaje automático de Spark: análisis predictivo de datos de inspección de alimentos mediante MLLib)**

    El comentario final revela que los porcentajes de falsos negativos y falsos positivos son 12,6% y 16,0%, respectivamente. Estas cifras no son exactas; ejecute el código para visualizar el gráfico circular con los porcentajes verdaderos.

- **Cuadernos de Python 6 y 7**

    Se produce un error en la primera celda para registrar el método sc.stop() al que hay que llamar cuando el cuaderno existe. En determinadas circunstancias, esto podría ocasionar pérdidas de recursos en Spark. Para evitar esta situación, asegúrese de ejecutar import atexit; atexit.register(lambda: sc.stop()) en dichos cuadernos antes de detenerlos. Si accidentalmente ha experimentado una pérdida de recursos, siga las instrucciones anteriores para terminar las aplicaciones de YARN con pérdidas.
     
## Problemas con permisos en el directorio de registros de Spark 

**Síntoma:**
 
Cuando hdiuser envía un trabajo con spark-submit, hay un error java.io.FileNotFoundException: /var/log/spark/sparkdriver\_hdiuser.log (permiso denegado) y el registro del controlador no se ha escrito.

**Mitigación:**
 
1. Agregue hdiuser al grupo de Hadoop. 
2. Proporcione 777 permisos en /var/log/spark después de crear el clúster. 
3. Actualice la ubicación del registro spark con Ambari para que sea un directorio con 777 permisos.  
4. Ejecute spark-submit como sudo. 

##Consulte también

- [Introducción a Apache Spark en HDInsight de Azure (Linux)](hdinsight-apache-spark-overview.md)
- [Introducción: aprovisionamiento de Apache Spark en Azure HDInsight (Linux) y ejecución de consultas interactivas mediante Spark SQL](hdinsight-apache-spark-jupyter-spark-sql.md)

<!---HONumber=AcomDC_1223_2015-->