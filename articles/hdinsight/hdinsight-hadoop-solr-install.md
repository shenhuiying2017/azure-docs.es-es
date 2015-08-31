<properties 
	pageTitle="Uso de la acción de script para instalar Solr en un clúster de Hadoop | Microsoft Azure" 
	description="Obtenga información para personalizar el clúster de HDInsight con Solr. Va a usar una opción de configuración de la acción de script para emplear un script para instalar Solr." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/07/2015" 
	ms.author="nitinme"/>

# Instalación y uso de Solr en clústeres de Hadoop de HDInsight

Puede instalar Solr en cualquier tipo de clúster de Hadoop en HDInsight de Azure mediante la personalización de clústeres de **acción de script**. La acción de script le permite ejecutar scripts para personalizar un clúster, conforme se crea el clúster. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-cluster-customize].

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Instalación de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install.md)

En este tema aprenderá a instalar Solr con la acción de script. Solr es una eficaz plataforma de búsqueda eficaz y proporciona capacidades de búsqueda de nivel empresarial en datos administrados por Hadoop. Una vez que haya instalado Solr en un clúster de HDInsight, también aprenderá a buscar datos mediante Solr.

> [AZURE.NOTE]El script de ejemplo que se utiliza en este tema crea un clúster de Solr con una configuración concreta. Si desea configurar el clúster de Solr con distintas colecciones, particiones, esquemas o réplicas, entre otras, debe modificar el script y los archivos binarios de Solr en consecuencia.


## <a name="whatis"></a>¿Qué es Solr?

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> es una plataforma de búsqueda empresarial que permite una eficaz búsqueda de texto completo en los datos. Si bien Hadoop permite almacenar y administrar grandes cantidades de datos, Apache Solr proporciona las capacidades de búsqueda para recuperar rápidamente los datos. Este tema proporciona instrucciones sobre cómo personalizar un clúster de HDInsight para instalar Solr.

## <a name="install"></a>¿Cómo instalo Solr?

Hay un script de ejemplo para instalar Solr en un clúster de HDInsight disponible desde un blob de almacenamiento de Azure de solo lectura en [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1). Esta sección proporciona instrucciones sobre cómo utilizar el script de ejemplo durante el aprovisionamiento del clúster mediante el Portal de Azure.


> [AZURE.NOTE]El script de ejemplo solo funciona con el clúster de HDInsight versión 3.1. Para obtener más información acerca de las versiones de clústeres de HDInsight, consulte las [versiones de clústeres de HDInsight](hdinsight-component-versioning.md).


1. Inicie el aprovisionamiento de un clúster con la opción **CREACIÓN PERSONALIZADA**, tal como se describe en [Aprovisionamiento de clústeres mediante opciones personalizadas](hdinsight-provision-clusters.md#portal). 
2. En la página **Acciones de script** del asistente, haga clic en **Agregar acción de script** para proporcionar detalles acerca de la acción de script, tal como se muestra a continuación:

	![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Uso de la acción de script para personalizar un clúster")
	
	<table border='1'>
	<tr><th>Propiedad</th><th>Valor</th></tr>
	<tr><td>Nombre</td>
		<td>Especifique un nombre para la acción de script. Por ejemplo, <b>Instalar Solr</b>.</td></tr>
	<tr><td>URI de script</td>
		<td>Especifique el Identificador uniforme de recursos (URI) al script que se ha invocado para personalizar el clúster. Por ejemplo, <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
	<tr><td>Tipo de nodo</td>
		<td>Especifique los nodos en los que se ejecuta el script de personalización. Puede elegir <b>Todos los nodos</b>, <b>Solo nodos principales</b> o <b>Solo nodos de trabajo</b>.
	<tr><td>Parámetros</td>
		<td>Especifique los parámetros, si lo requiere el script. El script para instalar Solr no requiere ningún parámetro, por lo que puede dejarlo en blanco.</td></tr>
</table>Puede agregar más de una acción de script para instalar varios componentes en el clúster. Después de haber agregado los scripts, haga clic en la marca de verificación para iniciar el aprovisionamiento del clúster.


## <a name="usesolr"></a>¿Cómo uso Solr en HDInsight?

Debe comenzar con la indización de Solr con algunos archivos de datos. A continuación, puede utilizar Solr para ejecutar consultas de búsqueda en los datos indizados. Realice los pasos siguientes para utilizar Solr en un clúster de HDInsight:

1. **Use el protocolo de Escritorio remoto (RDP) para conectarse de manera remota con el clúster de HDInsight con Solr instalado**. En el portal de Azure, habilite el Escritorio remoto para el clúster que ha creado con Solr instalado y, a continuación, conéctese de manera remota con el clúster. Para obtener instrucciones, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Conexión a los clústeres de HDInsight con RDP</a>.

2. **Indexe Solr mediante la carga de archivos de datos**. Al indizar Solr, colocar en él aquellos documentos que tenga que buscar. Para indexar Solr, use RDP para conectarse de manera remota al clúster, navegue al escritorio, abra la línea de comandos de Hadoop y vaya a **C:\\apps\\dist\\solr-4.7.2\\example\\exampledocs**. Ejecute el siguiente comando:
	
		java -jar post.jar solr.xml monitor.xml

	Debería ver estos resultados en la consola:

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	La utilidad post.jar indexa Solr con dos documentos de muestra, **solr.xml** y **monitor.xml**. La utilidad post.jar y los documentos de muestra están disponibles con la instalación de Solr.

3. **Utilice el panel de Solr para buscan dentro de los documentos indexados**. En la sesión RDP con el clúster de HDInsight, abra Internet Explorer e inicie el panel de Solr en ****http://headnodehost:8983/solr/#/**. En el panel izquierdo, en la lista desplegable **Selector principal**, seleccione **collection1** y, dentro de ella, haga clic en **Consulta**. Por ejemplo, para seleccionar y devolver a todos los documentos de Solr, indique los valores siguientes:
	1. En el cuadro de texto **q**, escriba ***:***. Se devolverán todos los documentos indizados en Solr. Si desea buscar una cadena específica dentro de los documentos, puede especificar esa cadena aquí.
	2. En el cuadro de texto **wt**, seleccione el formato de salida. El valor predeterminado es **json**. Haga clic en **Ejecutar consulta**.

		![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Ejecución de una consulta en el panel de Solr")
	
	La salida devuelve los dos documentos utilizados para indizar el Solr. La salida debe ser similar a la siguiente:

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }
   

4. **Recomendación: cree una copia de seguridad de los datos indexados desde Solr en el almacenamiento de blobs de Azure asociados con el clúster de HDInsight**. Se recomienda que cree una copia de seguridad de los datos indexados desde los nodos del clúster de Solr en el almacenamiento de blobs de Azure. Realice los pasos siguientes para ello:

	1. Desde la sesión RDP, abra Internet Explorer y apunte a la dirección URL siguiente:

			http://localhost:8983/solr/replication?command=backup

		Debería obtener una respuesta similar a la siguiente:

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. En la sesión remota, vaya a {SOLR\_HOME}{Collection}\\data. Para el clúster creado con el script de ejemplo, debiera ser **C:\\apps\\dist\\solr-4.7.2\\example\\solr\\collection1\\data**. En esta ubicación, verá una carpeta de instantáneas creada con un nombre similar a **snapshot.*timestamp***.
	
	3. Comprima la carpeta de instantáneas y cárguela al almacenamiento de blobs de Azure. En la línea de comandos de Hadoop, use el comando siguiente para ir a la ubicación de la carpeta de instantáneas:

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		Este comando copia la instantánea en /example/data/ en el contenedor dentro de la cuenta de almacenamiento predeterminada asociada con el clúster.


## Consulte también##
- [Instalación y uso de Spark en clústeres de HDInsight][hdinsight-install-spark]. Use la personalización del clúster para instalar Spark en clústeres de Hadoop para HDInsight. Spark es un marco de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones analíticas de Big Data.
- [Instalación de R en clústeres de HDInsight][hdinsight-install-r]. Use la personalización del clúster para instalar R en clústeres de Hadoop para HDInsight. R es un entorno y lenguaje de código abierto para computación estadística. Proporciona cientos de de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientada a objetos. También proporciona amplias capacidades gráficas.
- [Instalación de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md). Use la personalización del clúster para instalar Giraph en clústeres de Hadoop para HDInsight. Giraph permite realizar un procesamiento gráfico mediante Hadoop y se puede usar con HDInsight de Azure.



[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
 

<!---HONumber=August15_HO8-->