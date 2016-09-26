<properties 
	pageTitle="Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión a un clúster de HDInsight Spark | Microsoft Azure" 
	description="Descubra cómo instalar un cuaderno de Jupyter Notebook en el equipo de forma local y cómo conectarse a un clúster de Apache Spark en HDInsight de Azure." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="jhubbard" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/25/2016" 
	ms.author="nitinme"/>


# Instalación de un cuaderno de Jupyter en el equipo y conexión al clúster de Apache Spark en HDInsight Linux

En este artículo obtendrá información sobre cómo instalar un cuaderno de Jupyter Notebook con PySpark personalizado (para Python) y los kernels de Spark (para Scala) con la Sparkmagic, y conecte el cuaderno a un clúster de HDInsight. Puede haber varias razones para instalar Jupyter en el equipo local y también algunos desafíos. Para conocer una lista de ambos, consulte la sección [¿Por qué debo instalar Jupyter en mi equipo?](#why-should-i-install-jupyter-on-my-computer) al final de este artículo.

Existen tres pasos principales en la instalación de Jupyter y Sparkmagic en el equipo.

* Instalación de cuadernos de Jupyter Notebook
* Instalación de los kernels de PySpark y Spark con Sparkmagic
* Configuración de Sparkmagic para acceder al clúster de Spark en HDInsight

Para más información acerca de los kernels personalizados y Sparkmagic disponibles para cuadernos de Jupyter Notebook con el clúster de HDInsight, consulte [Kernels disponibles para cuadernos de Jupyter con clústeres Spark en HDInsight basados en Linux en HDInsight (versión preliminar)](hdinsight-apache-spark-jupyter-notebook-kernels.md).

##Requisitos previos

Los requisitos previos descritos aquí no se aplican a la instalación de Jupyter. Se facilitan para conectar el cuaderno de Jupyter Notebook con un clúster de HDInsight, una vez instalado el cuaderno.

- Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster Apache Spark en HDInsight Linux. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

## Instalación de cuadernos de Jupyter Notebook en el equipo

Debe instalar Python para poder instalar cuadernos de Jupyter Notebook. Python y Jupyter están disponibles como parte de la [distribución Anaconda](https://www.continuum.io/downloads). Al instalar Anaconda, realmente instalará una distribución de Python. Una vez se haya instalado Anaconda, agregue la instalación de Jupyter ejecutando un comando. Esta sección facilita las instrucciones que debe seguir.

1. Descargue el [instalador de Anaconda](https://www.continuum.io/downloads) para su plataforma y ejecute el programa de instalación. Durante la ejecución del Asistente para instalación, asegúrese de seleccionar la opción para agregar Anaconda a la variable PATH.

2. Ejecute el siguiente comando para instalar Jupyter.

		conda install jupyter

	Para más información sobre la instalación de Jupyter, consulte [Installing Jupyter using Anaconda](http://jupyter.readthedocs.io/en/latest/install.html) (Instalación de Jupyter con Anaconda).

## Instalación de kernels y Sparkmagic

En esta sección, instalará Sparkmagic, los kernels de PySpark y Spark y luego configurará los kernels para conectarse a un clúster Apache Spark que se ejecuta en HDInsight de Azure.

1. Descargue la versión preliminar pública más reciente de Sparkmagic desde [Github](https://github.com/jupyter-incubator/sparkmagic/archive/publicpreview0.5.zip).

2. Descomprima el archivo descargado en una ubicación del disco. En estas instrucciones, nos referiremos a esta ruta de acceso como `$SPARKMAGIC_PATH`.

2. Ejecute el siguiente comando.

		pip install -r $SPARKMAGIC_PATH/requirements.txt  

3. Ejecute el siguiente comando para instalar Sparkmagic.

		pip install -e $SPARKMAGIC_PATH

4. Instale los kernels de PySpark y Spark. Ejecute los comandos siguientes:

		jupyter-kernelspec install $SPARKMAGIC_PATH/remotespark/kernels/sparkkernel
		jupyter-kernelspec install $SPARKMAGIC_PATH/remotespark/kernels/pysparkkernel

## Configuración de Sparkmagic para acceder al clúster de Spark en HDInsight

En esta sección, configurará el conjunto de Sparkmagic que instaló anteriormente para conectarse a un clúster de Apache Spark que debe haber creado ya en HDInsight de Azure.

1. Normalmente, la información de configuración de Jupyter se almacena en el directorio principal de los usuarios. Para localizar el directorio principal en cualquier plataforma de sistema operativo, escriba los siguientes comandos.

	Inicie el shell de Python. En una ventana de línea de comandos, escriba lo siguiente:

		python

	En el shell de Python, escriba el siguiente comando para localizar el directorio principal.

		import os
		print os.path.expanduser('~')

2. Navegue hasta el directorio principal y cree una carpeta denominada **.sparkmagic** si todavía no existe.

3. Dentro de la carpeta, cree un archivo llamado **config.json** y agregue el siguiente fragmento de código JSON en él.

		{
		  "kernel_python_credentials" : {
		    "username": "{USERNAME}",
		    "base64_password": "{BASE64ENCODEDPASSWORD}",
		    "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
		  },
		  "kernel_scala_credentials" : {
		    "username": "{USERNAME}",
		    " base64_password ": "{BASE64ENCODEDPASSWORD}",
		    "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
		  }
		}

4. Sustituya **{USERNAME}**, **{CLUSTERDNSNAME}** y **{BASE64ENCODEDPASSWORD}** por los valores que correspondan. Puede usar una serie de utilidades en su lenguaje de programación preferido o en línea para generar una contraseña codificada en base64 de su contraseña real. Aquí le ofrecemos un ejemplo cómo sería un fragmento de código de Python sencillo para ejecutar desde el símbolo del sistema:

		python -c "import base64; print(base64.b64encode('{YOURPASSWORD}'))"

5. Reinicie Jupyter. En la ventana del símbolo del sistema, ejecute el comando siguiente.

		jupyter notebook

6. Compruebe que puede conectarse al clúster con el cuaderno de Jupyter Notebook y que puede usar el conjunto Sparkmagic disponible con los kernels. Lleve a cabo los siguiente pasos.

	1. Cree un nuevo notebook. En la esquina de la derecha, haga clic en **New** (Nuevo). Debería ver el kernel **Python2** predeterminado y los dos nuevos kernels que instaló, **PySpark** y **Spark**.

		![Crear un nuevo cuaderno de Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Crear un nuevo cuaderno de Jupyter")

	
		Haga clic en **PySpark**.


	2. Ejecute el siguiente fragmento de código.

			%%sql
			SELECT * FROM hivesampletable LIMIT 5

		Si puede recuperar correctamente el resultado, se comprobará la conexión al clúster de HDInsight.

	>[AZURE.TIP] Si desea actualizar la configuración del cuaderno para conectarse a un clúster distinto, actualice el archivo config.json con el nuevo conjunto de valores como se muestra en el paso 3 anterior.

## ¿Por qué debo instalar Jupyter en mi equipo?

Puede haber varios motivos por los que podría querer instalar Jupyter en el equipo y, a continuación, conectarlo a un clúster de Spark en HDInsight.

* Aunque los cuadernos de Jupyter Notebook ya están disponibles en el clúster de Spark en HDInsight de Azure, la instalación de Jupyter en el equipo ofrece la opción de crear cuadernos de forma local, probar la aplicación en un clúster en ejecución y cargar los cuadernos en el clúster. Para cargar los cuadernos en el clúster, puede hacerlo mediante el cuaderno de Jupyter Notebook en ejecución o el clúster, o bien guardarlos en la carpeta /HdiNotebooks de la cuenta de almacenamiento asociada al clúster. Para más información acerca de cómo se almacenan los cuadernos en el clúster, consulte la sección [Where are Jupyter notebooks stored](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)? (Almacenamiento de los cuadernos de Jupyter Notebook).
* Con los cuadernos disponibles localmente, puede conectarse a diferentes clústeres de Spark según los requisitos de la aplicación.
* Puede usar GitHub para implementar un sistema de control de código fuente y tener el control de las versiones de los cuadernos. También puede tener un entorno de colaboración donde varios usuarios pueden trabajar con el mismo cuaderno.
* Puede trabajar con cuadernos localmente sin necesidad de un clúster activo. Solo necesita un clúster para probar los cuadernos, no para administrar manualmente los cuadernos o un entorno de desarrollo.
* Puede resultar más fácil configurar su propio entorno de desarrollo local que configurar la instalación de Jupyter en el clúster. Puede aprovechar todo el software que haya instalado localmente sin configurar uno o más clústeres remotos.

>[AZURE.WARNING] Con Jupyter instalado en el equipo local, varios usuarios pueden ejecutar el mismo cuaderno en el mismo clúster de Spark al mismo tiempo. En tal situación, se crean varias sesiones de Livy. Si surge un problema y desea depurarlo, es una tarea compleja realizar el seguimiento de a qué sesión de Livy pertenece cada usuario.




## <a name="seealso"></a>Otras referencias


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

* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

### Administración de recursos

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0914_2016-->