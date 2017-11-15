---
title: "Herramientas de Azure HDInsight: configuración de un entorno interactivo de PySpark para Visual Studio Code | Microsoft Docs"
description: "Obtenga información sobre cómo usar Herramientas de Azure HDInsight para Visual Studio Code para crear y enviar consultas y scripts."
Keywords: VScode,Herramientas de Azure HDInsight,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Hive interactivo,Consulta interactiva
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: 
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 24839aadaee07b98ac5a6e6cfd14e44de54e7e7e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="set-up-pyspark-interactive-environment-for-visual-studio-code"></a>Configuración del entorno interactivo de PySpark para Visual Studio Code

Los pasos siguientes muestran cómo instalar paquetes de python cuando ejecuta **HDInsight: PySpark interactivo**.


## <a name="set-up-pyspark-interactive-environment-on-macos-and-linux"></a>Configuración del entorno interactivo de PySpark en MacOS y Linux
Debe usar el comando **pip3** para los pasos siguientes, si es **python 3.x**.
1. Asegúrese de que **Python** y **pip** estén instalados.
 
    ![versión de pip de Python](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Instale Jupyter.
    ```
    sudo pip install jupyter
    ```
    +  Es posible que vea el siguiente mensaje de error en Linux y MacOS:

        ![error1](./media/set-up-pyspark-interactive-environment/error1.png)
        ```Resolve:
        sudo pip uninstall asyncio
        sudo pip install trollies
        ```

    + Instale libkrb5 dev (solo para Linux). Es posible que se muestre el siguiente mensaje de error:

        ![error2](./media/set-up-pyspark-interactive-environment/error2.png)
        ```Resolve:
        sudo apt-get install libkrb5-dev 
        ```

3. Instale sparkmagic.
   ```
   sudo pip install sparkmagic
   ```

4. Asegúrese de que ipywidgets esté instalado correctamente ejecutando lo siguiente:
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![Instale los kernels de contenedor.](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Instale los kernels de contenedor. Ejecute **pip show sparkmagic** y se mostrará la ruta de acceso en la que está instalada sparkmagic. 

    ![ubicación de sparkmagic](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Desplácese hasta la ubicación y ejecute lo siguiente:

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![jupyter kernelspec install](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. Compruebe el estado de la instalación: 

    ```
    jupyter-kernelspec list
    ```
    ![jupyter kernelspec list](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    Para los kernels disponibles: **python2** y **pysparkkernel** se corresponden con **python 2.x**; **python3** y **pyspark3kernel** se corresponden con **python 3.x**. 

8. Reinicie VScode y vuelva al editor de script que ejecuta **HDInsight: PySpark interactivo**.

## <a name="next-steps"></a>Pasos siguientes

### <a name="demo"></a>Demostración
* HDInsight para VScode: [vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Uso de Herramientas de Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md)
* [Uso del kit de herramientas de Azure para IntelliJ con el fin de crear y enviar aplicaciones Spark en Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark mediante SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark mediante VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de las herramientas de HDInsight del kit de herramientas de Azure para Eclipse con el fin de crear aplicaciones Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Uso de las herramientas de HDInsight para IntelliJ con Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualize Hive data with Microsoft Power BI in Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md) (Visualización de datos de Hive con Microsoft Power BI en Azure HDInsight)
* [Uso de Zeppelin para ejecutar consultas de Hive en Azure HDInsight](hdinsight-connect-hive-zeppelin.md)
