---
title: 'Herramientas de Azure HDInsight: uso de Visual Studio Code para Hive, LLAP o pySpark | Microsoft Docs'
description: "Aprenda a usar Obtenga información sobre cómo usar Herramientas de Azure HDInsight para Visual Studio Code para crear y enviar consultas y scripts."
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: jgao
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 89e83dc02f32f6f2a781cf2e35040b29cc3d3c06
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Uso de las Herramientas de Azure HDInsight para Visual Studio Code

Aprenda a usar las Herramientas de Azure HDInsight para Visual Studio Code (VS Code) para crear y enviar trabajos por lotes de Hive, consultas interactivas de Hive y scripts de pySpark. Las Azure HDInsight Tools se pueden instalar en las plataformas compatibles con VS Code. Entre ellas se incluyen Windows, Linux y macOS. Encontrará los requisitos previos de cada plataforma.


## <a name="prerequisites"></a>requisitos previos

Para completar los pasos de este artículo, se requieren los elementos siguientes:

- Un clúster de HDInsight.  Para crear un clúster, vea la [introducción a HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono solo es obligatorio para Linux y macOS.

## <a name="install-the-hdinsight-tools"></a>Instalar Herramientas de HDInsight
   
Después de instalar los requisitos previos, puede instalar las Herramientas de Azure HDInsight para VS Code. 

**Para instalar Herramientas de Azure HDInsight**

1. Abra Visual Studio Code.

2. En el panel izquierdo, seleccione **Extensiones**. En el cuadro de búsqueda, escriba **HDInsight**.

3. Al lado de **Azure HDInsight Tools** seleccione **Instalar**. Después de unos segundos, el botón **Instalar** cambia a **Recargar**.

4. Haga clic en **Recargar** para activar la extensión **Azure HDInsight tools**.

5. Haga clic en **Recargar ventana** para confirmar. **Azure HDInsight tools** aparece en el panel **Extensiones**.

   ![Instalación de Python de HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Abrir un área de trabajo de HDInsight

Cree un área de trabajo en VS Code para poder conectarse a Azure.

**Para abrir un área de trabajo**

1. En el menú **Archivo**, seleccione **Abrir carpeta**. A continuación, designe una carpeta existente como carpeta de trabajo o cree una carpeta nueva. La carpeta aparece en el panel izquierdo.

2. En el panel izquierdo, haga clic en el icono **Nuevo archivo** situado junto a la carpeta de trabajo.

   ![Nuevo archivo](./media/hdinsight-for-vscode/new-file.png)

3. Asigne al nuevo archivo el nombre de la extensión de archivo .hql (consultas de Hive) o .py (script de Spark). Observe que se agrega automáticamente un archivo de configuración **XXXX_hdi_settings.json** a la carpeta de trabajo.

4. Abra **XXXX_hdi_settings.json** desde el **EXPLORADOR** o haga clic con el botón derecho en el editor de scripts para seleccionar **Set Configuration** (Establecer configuración). Puede configurar los parámetros de entrada de inicio de sesión, clúster predeterminado y envío de trabajos, como se muestra en el ejemplo del archivo. Puede dejar vacíos el resto de parámetros.

## <a name="connect-to-azure"></a>Conexión a Azure

Para poder enviar scripts a los clústeres de HDInsight desde VS Code, debe conectarse a su cuenta de Azure.

**Para conectarse a Azure**

1. Si no tiene una carpeta de trabajo y un archivo de script, créelos.

2. Haga clic con el botón derecho en el editor de scripts y seleccione **HDInsight: Login** (HDInsight: inicio de sesión) en el menú contextual. También puede presionar **Ctrl+Mayús+P** y, después, especificar **HDInsight: Login** (HDInsight: inicio de sesión).

    ![Inicio de sesión de Herramientas de HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Para iniciar sesión, siga las instrucciones del panel **SALIDA**.

    **Azure:** ![información de inicio de sesión en Herramientas de HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    Una vez establecida la conexión, el nombre de la cuenta de Azure se muestra en la barra de estado en la parte inferior izquierda de la ventana de VSCode. 

    > [!NOTE]
    > Debido a un problema conocido de autenticación de Azure es preciso que abra un explorador en modo privado o incógnito. Si la cuenta de Azure tiene dos factores habilitados, se recomienda usar la autenticación de teléfono en lugar de la del PIN.
  

4. Haga clic con el botón derecho en el editor de scripts para abrir el menú contextual. Desde el menú contextual, puede realizar las siguientes tareas:

    - Cerrar la sesión
    - Enumeración de clústeres
    - Establecer los clústeres predeterminados
    - Enviar consultas de Hive interactivas
    - Enviar scripts por lotes de Hive
    - Envíe consultas de PySpark interactivas
    - Enviar scripts por lotes de PySpark
    - Establecer configuraciones

## <a name="list-hdinsight-clusters"></a>Enumerar los clústeres de HDInsight

Para probar la conexión, puede enumerar los clústeres de HDInsight:

**Para enumerar los clústeres de HDInsight en la suscripción de Azure**
1. Abra un área de trabajo y conéctese a Azure. Para más información, consulte [Abrir un área de trabajo de HDInsight](#open-hdinsight-workspace) y [Conexión a Azure](#connect-to-azure).

2. Haga clic con el botón derecho en el editor de scripts y, después, seleccione **HDInsight: List Cluster** (HDInsight: enumerar clústeres) en el menú contextual. 

3. Los clústeres de Hive y Spark aparecen en el panel **Salida**.

    ![Establecer la configuración de un clúster predeterminado](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Establecimiento de un clúster predeterminado
1. Abra un área de trabajo y conéctese a Azure. Vea [Abrir un área de trabajo de HDInsight](#open-hdinsight-workspace) y [Conexión a Azure](#connect-to-azure).

2. Haga clic con el botón derecho en el editor de scripts y, después, seleccione **HDInsight: Set Default Cluster** (HDInsight: establecer clúster predeterminado). 

3. Seleccione un clúster como clúster predeterminado del archivo de script actual. Las herramientas actualizan automáticamente el archivo de configuración, **XXXX_hdi_settings.json**. 

   ![Establecer la configuración de un clúster predeterminado](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Establecimiento del entorno de Azure 
1. Abra la paleta de comandos presionando **CTRL+MAYÚS+P**.

2. Escriba **HDInsight: establecer el entorno Azure**.

3. Seleccione una forma de Azure y AzureChina como entrada de inicio de sesión predeterminada.

4. Mientras tanto, la herramienta ya ha guardado la entrada de inicio de sesión predeterminada en **XXXX_hdi_settings.json**. También puede actualizarlo directamente en este archivo de configuración. 

   ![Establecer la configuración de entrada de inicio de sesión predeterminada](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Enviar consultas de Hive interactivas

Herramientas de HDInsight para VS Code permite enviar consultas de Hive interactivas a clústeres de consultas interactivas de HDInsight.

1. Si no tiene una carpeta de trabajo y un archivo de script Hive, créelos.

2. Conéctese a su cuenta de Azure y configure el clúster predeterminado, en caso de que todavía no lo haya hecho.

3. Copie y pegue el código siguiente en el archivo de Hive y guárdelo.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Haga clic con el botón derecho en el editor de scripts y, después, seleccione **HDInsight: Hive Interactive** (HDInsight: Hive interactivo) para enviar la consulta. Las herramientas también permiten enviar un bloque de código, en lugar del archivo de script completo mediante el menú contextual. Poco después, los resultados de la consulta aparecen en una pestaña nueva.

   ![Resultado de Hive interactivo](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panel **RESULTADOS**: puede guardar todo el resultado en forma de archivo CSV, JSON o de Excel en una ruta de acceso local, o bien seleccionar únicamente varias líneas.

    - Panel **MENSAJES**: al seleccionar el número de **Línea**, salta la primera línea del script en ejecución.

Una consulta interactiva tarda mucho menos en ejecutarse que un [trabajo por lotes de Hive](#submit-hive-batch-scripts).

## <a name="submit-hive-batch-scripts"></a>Enviar scripts por lotes de Hive

1. Si no tiene una carpeta de trabajo y un archivo de script Hive, créelos.

2. Conéctese a su cuenta de Azure y configure el clúster predeterminado, en caso de que todavía no lo haya hecho.

3. Copie y pegue el código siguiente en el archivo de Hive y guárdelo.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Haga clic con en el botón derecho en el editor de scripts y, después, seleccione **HDInsight: Hive Interactive** (HDInsight: Hive interactivo) para enviar un trabajo de Hive. 

4. Seleccione el clúster al que desea realizar el envío.  

    Después de enviar un trabajo de Hive, la información de envío correcto y el identificador del trabajo aparecen en el panel **SALIDA**. El trabajo de Hive también abre el **EXPLORADOR WEB**, que muestra el estado y los registros del trabajo en tiempo real.

   ![enviar el resultado de un trabajo de Hive](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

El [envío de consultas Hive interactivas](#submit-interactive-hive-queries) tarda mucho menos tiempo que el envío de un trabajo por lotes.

## <a name="submit-interactive-pyspark-queries"></a>Envíe consultas de PySpark interactivas
Herramientas de HDInsight para VS Code también le permite enviar consultas de PySpark interactivas a clústeres de Spark.
1. Cree una carpeta de trabajo y un archivo de script con la extensión .py si no los tiene aún.

2. Conéctese a su cuenta de Azure si no lo ha hecho aún.

3. Copie y pegue el código siguiente en el archivo de script:
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```
4. Resalte estos scripts. Luego haga clic con el botón derecho en el editor de scripts y seleccione **HDInsight: PySpark Interactive** (HDInsight: PySpark interactivo).

5. Si aún no ha instalado la extensión **Python** en VS Code, haga clic en el botón **Instalar**, como se muestra en la siguiente ilustración:

    ![Instalación de Python de HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Instale el entorno de Python en el sistema, en caso de que aún no lo haya hecho. 
   - Para Windows, descargue e instale [Python](https://www.python.org/downloads/). Luego, asegúrese de que `Python` y `pip` están en la RUTA DE ACCESO del sistema.

   - Para ver las instrucciones para macOS y Linux, consulte [Configuración del entorno interactivo de PySpark para Visual Studio Code](set-up-pyspark-interactive-environment.md).

7. Seleccione el clúster al que va a enviar la consulta de PySpark. Poco después, el resultado de la consulta se mostrará en la pestaña nueva de la derecha:

   ![Enviar el resultado del trabajo de Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. La herramienta también admite la consulta **Cláusula SQL**.

   ![Enviar resultado de trabajo de Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) El estado del envío aparece en la parte izquierda de la barra de estado inferior cuando se ejecutan consultas. No envíe otras consultas cuando el estado sea **PySpark Kernel (busy)** [Kernel de PySpark (ocupado)]. 

>[!NOTE]
>Los clústeres pueden mantener la información de la sesión. La variable definida, la función y los valores correspondientes se conservan en la sesión, con el fin de se pueda hacer referencia a ellos en varias llamadas de servicio para el mismo clúster. 
 

## <a name="submit-pyspark-batch-job"></a>Envío de un trabajo por lotes de PySpark

1. Cree una carpeta de trabajo y un archivo de script con la extensión .py si no los tiene aún.

2. Conéctese a su cuenta de Azure, en caso de que aún no lo haya hecho.

3. Copie y pegue el código siguiente en el archivo de script:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. Haga clic con el botón derecho en el editor de scripts y, después, seleccione **HDInsight: PySpark Batch** (HDInsight: lote de PySpark). 

5. Seleccione el clúster al que va a enviar el trabajo de PySpark. 

   ![Enviar el resultado del trabajo de Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Después de enviar un trabajo de Python, los registros de envío aparecen en la ventana **SALIDA** de VS Code. También se muestran la **dirección URL de interfaz de usuario de Spark** y la **dirección URL de interfaz de usuario de Yarn**. Puede abrir la dirección URL en un explorador web para realizar el seguimiento de estado del trabajo.


## <a name="additional-features"></a>Características adicionales

HDInsight para VS Code admite las siguientes características:

- **Autocompletar de IntelliSense**. Emergen sugerencias para la palabra clave, los métodos, las variables, etc. Los distintos iconos representan diferentes tipos de objetos.

    ![Tipos de objetos de IntelliSense de Herramientas de HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Marcador de error de IntelliSense**. El servicio de lenguaje subraya los errores de edición del script de Hive.     
- **Aspectos destacados de la sintaxis**. El servicio de lenguaje usa colores diferentes para diferenciar las variables, palabras clave, tipos de datos, funciones, etc. 

    ![Aspectos destacados de la sintaxis de Herramientas de HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>pasos siguientes

### <a name="demo"></a>Demostración
* HDInsight para VS Code: [vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Herramientas y extensiones

* [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark mediante VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark mediante SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Uso de las herramientas de HDInsight para IntelliJ con Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Uso de las herramientas de HDInsight del kit de herramientas de Azure para Eclipse con el fin de crear aplicaciones Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualización de datos de Hive con Microsoft Power BI en Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Visualización de datos de Interactive Query Hive con Power BI en Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Configuración del entorno interactivo de PySpark para Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Uso de Zeppelin para ejecutar consultas de Hive en Azure HDInsight](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](spark/apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Machine Learning con Apache Spark en HDInsight de Azure](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](spark/apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](spark/apache-spark-create-standalone-application.md)
* [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (Linux)](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](spark/apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](spark/apache-spark-job-debugging.md)



