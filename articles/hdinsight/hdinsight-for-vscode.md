---
title: 'Herramientas de Azure HDInsight: uso de Visual Studio Code para Hive, LLAP o pySpark | Microsoft Docs'
description: "Obtenga información sobre cómo usar Herramientas de Azure HDInsight para Visual Studio Code para crear y enviar consultas y scripts."
Keywords: VScode,Herramientas de Azure HDInsight,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Hive interactivo,Consulta interactiva
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
ms.date: 09/26/2017
ms.author: jejiang
ms.openlocfilehash: fdf1911f4f5b515592b6197ea738d808104cb43e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-hdinsight-tool-for-visual-studio-code"></a>Uso de Herramientas de Azure HDInsight para Visual Studio Code

Obtenga información sobre cómo usar Herramientas de Azure HDInsight para Visual Studio Code (VSCode) para crear y enviar trabajos por lotes de Hive, consultas interactivas de Hive y scripts de pySpark. Puede instalar Herramientas de Azure HDInsight en las plataformas compatibles con VSCode, incluido Windows, Linux y MacOS. Encontrará los requisitos previos de cada plataforma.


## <a name="prerequisites"></a>Requisitos previos

Para completar este artículo, se requieren los elementos siguientes:

- Un clúster de HDInsight.  Para crear un clúster, vea la [introducción a HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono solo es necesario para Linux y MacOS.

## <a name="install-the-hdinsight-tools"></a>Instalar Herramientas de HDInsight
   
Después de instalar los requisitos previos, puede instalar Herramientas de Azure HDInsight para VSCode. 

**Para instalar Herramientas de Azure HDInsight**

1. Abra **Visual Studio Code**.
2. Haga clic en **Extensiones** en el panel izquierdo. Escriba **HDInsight** en el cuadro de búsqueda.
3. Haga clic en **Instalar** junto a **Herramientas de Azure HDInsight**. Después de unos segundos, el botón **Instalar** cambiará a **Recargar**.
4. Haga clic en **Recargar** para activar la extensión **Herramientas de Azure HDInsight**.
5. Haga clic en **Recargar ventana** para confirmar. Verá **Herramientas de Azure HDInsight** en el panel Extensiones.

   ![Instalación de Python de HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

Para los usuarios de Spark, se recomienda la extensión de Python para mejores experiencias en el servicio de lenguaje. La extensión de Python no es necesaria para los usuarios de Hive. 

**Para instalar Python**

1. Abra **Visual Studio Code**.
2. Haga clic en **Extensiones** en el panel izquierdo. En el cuadro de búsqueda, escriba **python**. Verá una lista de extensiones de python. Una de ellas es **Python**.
3. Haga clic en **Instalar** junto a **Python**. Después de unos segundos, el botón **Instalar** cambiará a **Recargar**.
4. Haga clic en **Recargar** para activar la extensión **Python**.
5. Haga clic en **Recargar ventana** para confirmar. Verá **Python** en el panel Extensiones.

     ![Instalación de Python de HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

## <a name="open-hdinsight-workspace"></a>Abrir un área de trabajo de HDInsight

Debe crear un área de trabajo en VSCode para poder conectarse a Azure.

**Para abrir un área de trabajo**

1. En el menú **Archivo**, haga clic en **Abrir carpeta** y especifique una carpeta existente o cree una carpeta de trabajo. La carpeta aparece en el panel izquierdo.
2. En el panel izquierdo, haga clic en el icono **Nuevo archivo** situado junto a la carpeta de trabajo.

   ![nuevo archivo](./media/hdinsight-for-vscode/new-file.png)
3. Asigne al nuevo archivo el nombre de la extensión de archivo .hql (consultas de Hive) o .py (script de Spark). Observe que se agrega automáticamente un archivo de configuración **XXXX_hdi_settings.json** a la carpeta de trabajo.
4. Abra **XXXX_hdi_settings.json** desde el **EXPLORADOR**, o haga clic con el botón derecho en el editor de scripts para seleccionar **Set Configuration** (Establecer configuración). Puede configurar los parámetros de la entrada de inicio de sesión, el clúster predeterminado y el envío de trabajos, como se muestra en el ejemplo del archivo. Puede dejar vacíos el resto de parámetros.

## <a name="connect-to-azure"></a>Conexión a Azure

Para poder enviar scripts a clústeres de HDInsight desde VSCode, debe conectarse a su cuenta de Azure.

**Para conectarse a Azure**

1. Cree una carpeta de trabajo y un archivo de script si no tiene uno.
2. Haga clic con el botón derecho en el editor de scripts y, después, seleccione **HDInsight: Login** (HDInsight: inicio de sesión) en el menú contextual. También puede presionar **Ctrl+Mayús+P** y especificar **HDInsight: Login** (HDInsight: inicio de sesión).

    ![Inicio de sesión de Herramientas de HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)
3. Siga las instrucciones de inicio de sesión en el panel **SALIDA** para iniciar sesión.

    **Azure:** ![información de inicio de sesión en Herramientas de HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    Una vez realizada la conexión, el nombre de la cuenta de Azure se muestra en la barra de estado en la parte inferior izquierda de la ventana de VSCode. 

    > [!NOTE] 
    > Si la cuenta de Azure tiene dos factores habilitados, se recomienda usar la autenticación de teléfono en lugar de PIN.
    > Hay un problema conocido sobre el inicio de sesión de Azure con Google Chrome.

4. Haga clic con el botón derecho en la edición de script para abrir el menú contextual:

    ![Menú contextual del editor de script de Herramientas de HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-context-menu.png)

    Desde el menú contextual, puede realizar las siguientes tareas:

    - logout
    - Enumerar clústeres
    - Establecer el clúster predeterminado
    - Enviar consultas de Hive interactivas
    - Enviar un script por lotes de Hive
    - Establecer la configuración

## <a name="list-hdinsight-clusters"></a>Enumerar los clústeres de HDInsight

Para probar la conexión, puede enumerar los clústeres de HDInsight:

**Para enumerar los clústeres de HDInsight en la suscripción de Azure**
1. Abra un área de trabajo y conéctese a Azure. Vea [Abrir un área de trabajo de HDInsight](#open-hdinsight-workspace) y [Conexión a Azure](#connect-to-azure).
2. Haga clic con el botón derecho en el editor de scripts y, después, seleccione **HDInsight: List Cluster** (HDInsight: enumerar clústeres) en el menú contextual. 
3. Los clústeres de Hive y Spark aparecen en el panel **Salida**.

    ![establecer la configuración del clúster predeterminado](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Establecer el clúster predeterminado
1. Abra un área de trabajo y conéctese a Azure. Vea [Abrir un área de trabajo de HDInsight](#open-hdinsight-workspace) y [Conexión a Azure](#connect-to-azure).
2. Haga clic con el botón derecho en el editor de scripts y, después, haga clic en **HDInsight: Set Default Cluster** (HDInsight: establecer clúster predeterminado). 
3. Seleccione un clúster como clúster predeterminado para el archivo de script actual. Las Herramientas actualizan automáticamente el archivo de configuración, **XXXX_hdi_settings.json**. 

   ![establecer la configuración del clúster predeterminado](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-azure-environment"></a>Establecer el entorno de Azure 
1. Abra la paleta de comandos presionando **CTRL+MAYÚS+P**.
2. Escriba **HDInsight: establecer el entorno Azure**.
3. Seleccione una forma de Azure y AzureChina como entrada de inicio de sesión predeterminada.
4. Mientras tanto, nuestra herramienta ya ha guardado lo que ha seleccionado como entrada de inicio de sesión predeterminada en **XXXX_hdi_settings.json**. También puede actualizarlo directamente en este archivo de configuración. 

   ![establecer la configuración de entrada de inicio de sesión predeterminada](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Enviar consultas de Hive interactivas

Las Herramientas de HDInsight para VSCode permiten enviar consultas de Hive interactivas a clústeres de consultas interactivas de HDInsight.

1. Cree una carpeta de trabajo y un archivo de script de Hive si no tiene uno.
2. Conéctese a su cuenta de Azure y configure el clúster predeterminado si todavía no lo ha hecho.
3. Copie y pegue el código siguiente en el archivo de Hive y guárdelo.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Haga clic con el botón derecho en el editor de scripts y, después, haga clic en **HDInsight: Interactive Hive** (HDInsight: Hive interactivo) para enviar la consulta. Las Herramientas también permiten enviar un bloque de código en lugar del archivo de script completo mediante el menú contextual. Poco después, el resultado de la consulta se mostrará en una nueva pestaña:

   ![resultado de Hive interactivo](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panel **RESULTADOS**: puede guardar todo el resultado como CSV, JSON o EXCEL en la ruta de acceso local, o bien seleccionar únicamente algunas líneas.
    - Panel **MENSAJES**: al hacer clic en el número de **Línea**, retrocede a la primera línea del script en ejecución.

Si se compara con la [ejecución de un trabajo por lotes de Hive](#submit-hive-batch-scripts), la consulta interactiva tarda mucho menos.

## <a name="submit-hive-batch-scripts"></a>Enviar scripts por lotes de Hive

1. Cree una carpeta de trabajo y un archivo de script de Hive si no tiene uno.
2. Conéctese a su cuenta de Azure y configure el clúster predeterminado si todavía no lo ha hecho.
3. Copie y pegue el código siguiente en el archivo de Hive y guárdelo.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Haga clic con el botón derecho en el editor de scripts y, después, haga clic en **HDInsight: Submit Hive Batch Script** (HDInsight: enviar script por lotes de Hive) para enviar un trabajo de Hive. 
4. Seleccione el clúster al que quiera enviarlo.  

    Después de enviar un trabajo de Hive, se muestra la información de envío correcto y el identificador del trabajo en el panel **SALIDA**. Además, se abre el **EXPLORADOR WEB**, donde se muestran el estado y los registros en tiempo real del trabajo.

   ![enviar el resultado de un trabajo de Hive](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

Si se compara con el [envío de consultas de Hive interactivas](#submit-interactive-hive-queries), el trabajo por lotes tarda mucho más.


## <a name="submit-pyspark-job"></a>Enviar un trabajo de PySpark

1. Cree una carpeta de trabajo y un archivo de script con la extensión .py si no tiene uno.
2. Conéctese a su cuenta de Azure y configure el clúster predeterminado si todavía no lo ha hecho.
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
4. Haga clic con el botón derecho en el editor de scripts y, después, haga clic en **HDInsight: Submit PySpark Job** (HDInsight: enviar trabajo de PySpark). 
5. Seleccione el clúster al que va a enviar el trabajo de PySpark. 

   ![enviar el resultado del trabajo de python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Después de enviar un trabajo de python, los registros de envío se muestran en la ventana **SALIDA** de VSCode. También se muestran la **dirección URL de interfaz de usuario de Spark** y la **dirección URL de interfaz de usuario de Yarn**. Puede abrir la dirección URL en un explorador web para realizar el seguimiento de estado del trabajo.


## <a name="additional-features"></a>Características adicionales

HDInsight para VSCode admite las siguientes características:

- **Autocompletar de IntelliSense**. Las sugerencias aparecen en palabra clave, método, variables, etc. Los distintos iconos representan diferentes tipos de los objetos:

    ![Tipos de objetos de IntelliSense de Herramientas de HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Marcador de error de IntelliSense**. El servicio de lenguaje subraya los errores de edición para los scripts de Hive.     
- **Aspectos destacados de la sintaxis**. El servicio de lenguaje usa un color distinto para diferenciar variables, palabras clave, tipos de datos, funciones, etc. 

    ![Aspectos destacados de la sintaxis de Herramientas de HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Pasos siguientes

### <a name="demo"></a>Demostración
* HDInsight para VScode: [vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark mediante VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark mediante SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Uso de las herramientas de HDInsight para IntelliJ con Hortonworks Sandbox](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Uso de las herramientas de HDInsight del kit de herramientas de Azure para Eclipse con el fin de crear aplicaciones Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)
* [Visualize Hive data with Microsoft Power BI in Azure HDInsight](./hdinsight-connect-hive-power-bi.md) (Visualización de datos de Hive con Microsoft Power BI en Azure HDInsight)
* [Use Zeppelin to run Hive queries in Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md) (Uso de Zeppelin para ejecutar consultas de Hive en Azure HDInsight)

### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="managing-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)



