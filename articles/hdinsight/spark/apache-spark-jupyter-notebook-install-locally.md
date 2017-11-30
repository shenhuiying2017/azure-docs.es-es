---
title: "Instalación de cuadernos de Jupyter Notebook localmente y conexión a un clúster de Azure HDInsight Spark | Microsoft Docs"
description: "Descubra cómo instalar un cuaderno de Jupyter Notebook en el equipo de forma local y cómo conectarse a un clúster de Apache Spark en Azure HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48593bdf-4122-4f2e-a8ec-fdc009e47c16
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 1508faf7a05461de65b7a4c2f68e2ef9bbd7e19d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight Linux

En este artículo obtendrá información sobre cómo instalar un cuaderno de Jupyter Notebook con PySpark personalizado (para Python) y los kernels de Spark (para Scala) con la Sparkmagic, y conecte el cuaderno a un clúster de HDInsight. Puede haber varias razones para instalar Jupyter en el equipo local y también algunos desafíos. Para obtener más información sobre esto, consulte la sección [¿Por qué debo instalar Jupyter en mi equipo?](#why-should-i-install-jupyter-on-my-computer) al final de este artículo.

Existen tres pasos principales en la instalación de Jupyter y Sparkmagic en el equipo.

* Instalación de cuadernos de Jupyter Notebook
* Instalación de los kernels de PySpark y Spark con Sparkmagic
* Configuración de Sparkmagic para acceder al clúster de Spark en HDInsight

Para más información acerca de los kernels personalizados y Sparkmagic disponibles para cuadernos de Jupyter Notebook con el clúster de HDInsight, consulte [Kernels disponibles para cuadernos de Jupyter con clústeres Spark en HDInsight basados en Linux en HDInsight (versión preliminar)](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Requisitos previos
Los requisitos previos descritos aquí no se aplican a la instalación de Jupyter. Se facilitan para conectar el cuaderno de Jupyter Notebook con un clúster de HDInsight, una vez instalado el cuaderno.

* Una suscripción de Azure. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Instalación de cuadernos de Jupyter Notebook en el equipo

Debe instalar Python para poder instalar cuadernos de Jupyter Notebook. Python y Jupyter están disponibles como parte de la [distribución Anaconda](https://www.continuum.io/downloads). Al instalar Anaconda, instalará una distribución de Python. Una vez se haya instalado Anaconda, agregue la instalación de Jupyter ejecutando comandos adecuados.

1. Descargue el [instalador de Anaconda](https://www.continuum.io/downloads) para su plataforma y ejecute el programa de instalación. Durante la ejecución del Asistente para instalación, asegúrese de seleccionar la opción para agregar Anaconda a la variable PATH.
2. Ejecute el siguiente comando para instalar Jupyter.

        conda install jupyter

    Para más información sobre la instalación de Jupyter, consulte [Installing Jupyter using Anaconda](http://jupyter.readthedocs.io/en/latest/install.html)(Instalación de Jupyter con Anaconda).

## <a name="install-the-kernels-and-spark-magic"></a>Instalación de kernels y Sparkmagic

Para instrucciones sobre cómo instalar los kernels de PySpark y Spark, además de Sparkmagic, siga las instrucciones de instalación en la [documentación de sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) en GitHub. El primer paso en la documentación de Sparkmagic es la instalación. Reemplace el primer paso del vínculo por los comandos siguientes, dependiendo de la versión del clúster de HDInsight al que se vaya a conectar. A continuación, siga los pasos restantes en la documentación de Sparkmagic. Si desea instalar kernels diferentes, debe realizar el paso 3 de la sección de instrucciones de instalación de Sparkmagic.

* Para los clústeres 3.4, instale sparkmagic 0.2.3 ejecutando `pip install sparkmagic==0.2.3`.

* Para los clústeres 3.5 y 3.6, instale sparkmagic 0.11.2 ejecutando `pip install sparkmagic==0.11.2`.

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Configuración de Sparkmagic para acceder al clúster de Spark en HDInsight

En esta sección, configurará el conjunto de Sparkmagic que instaló anteriormente para conectarse a un clúster de Apache Spark que debe haber creado ya en HDInsight de Azure.

1. Normalmente, la información de configuración de Jupyter se almacena en el directorio principal de los usuarios. Para localizar el directorio principal en cualquier plataforma de sistema operativo, escriba los siguientes comandos.

    Inicie el shell de Python. En una ventana de línea de comandos, escriba lo siguiente:

        python

    En el shell de Python, escriba el siguiente comando para localizar el directorio principal.

        import os
        print(os.path.expanduser('~'))

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
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          }
        }

4. Sustituya **{USERNAME}**, **{CLUSTERDNSNAME}** y **{BASE64ENCODEDPASSWORD}** por los valores que correspondan. Puede usar una serie de utilidades en su lenguaje de programación preferido o en línea para generar una contraseña codificada en base64 de su contraseña real.

5. Ajuste la configuración de latido adecuada en `config.json`: Debe agregar estos parámetros de configuración en el mismo nivel que los fragmentos de código `kernel_python_credentials` y `kernel_scala_credentials` agregados anteriormente. Para ver un ejemplo sobre cómo y dónde agregar la configuración de latido, vea este [archivo config.json de ejemplo](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

    * Para `sparkmagic 0.2.3` (clústeres 3.4):

            "should_heartbeat": true,
            "heartbeat_refresh_seconds": 5,
            "heartbeat_retry_seconds": 1

    * Para `sparkmagic 0.11.2` (clústeres v3.5 y v3.6), incluyen:

            "heartbeat_refresh_seconds": 5,
            "livy_server_heartbeat_timeout_seconds": 60,
            "heartbeat_retry_seconds": 1

    >[!TIP]
    >Los latidos se envían para garantizar que no se pierdan sesiones. Cuando un equipo entra en modo de suspensión o se apaga, no se enviará el latido, con lo que se la sesión se limpia. Para los clústeres 3.4, si desea deshabilitar este comportamiento, puede establecer la configuración de Livio `livy.server.interactive.heartbeat.timeout` a `0` en la interfaz de usuario de Ambari. Para los clústeres 3.5, si no establece la configuración de 3.5 anterior, no se eliminará la sesión.

6. Reinicie Jupyter. En la ventana del símbolo del sistema, ejecute el comando siguiente.

        jupyter notebook

7. Compruebe que puede conectarse al clúster con el cuaderno de Jupyter Notebook y que puede usar el conjunto Sparkmagic disponible con los kernels. Lleve a cabo los siguiente pasos.

    a. Cree un nuevo notebook. En la esquina de la derecha, haga clic en **New**(Nuevo). Debería ver el kernel **Python2** predeterminado y los dos nuevos kernels que instaló, **PySpark** y **Spark**. Haga clic en **PySpark**.

    ![Kernels de cuadernos de Jupyter Notebook](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Kernels de cuadernos de Jupyter Notebook")

    b. Ejecute el siguiente fragmento de código.

        %%sql
        SELECT * FROM hivesampletable LIMIT 5

    Si puede recuperar correctamente el resultado, se comprobará la conexión al clúster de HDInsight.

    >[!TIP]
    >Si desea actualizar la configuración del cuaderno para conectarse a un clúster distinto, actualice el archivo config.json con el nuevo conjunto de valores como se muestra en el paso 3 anterior.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>¿Por qué debo instalar Jupyter en mi equipo?
Puede haber varios motivos por los que podría querer instalar Jupyter en el equipo y, a continuación, conectarlo a un clúster de Spark en HDInsight.

* Aunque los cuadernos de Jupyter Notebook ya están disponibles en el clúster de Spark en HDInsight de Azure, la instalación de Jupyter en el equipo ofrece la opción de crear cuadernos de forma local, probar la aplicación en un clúster en ejecución y cargar los cuadernos en el clúster. Para cargar los cuadernos en el clúster, puede hacerlo mediante el cuaderno de Jupyter Notebook en ejecución o el clúster, o bien guardarlos en la carpeta /HdiNotebooks de la cuenta de almacenamiento asociada al clúster. Para obtener más información sobre cómo se almacenan los cuadernos en el clúster, consulte la sección [Where are Jupyter notebooks stored](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)? (Almacenamiento de los cuadernos de Jupyter Notebook).
* Con los cuadernos disponibles localmente, puede conectarse a diferentes clústeres de Spark según los requisitos de la aplicación.
* Puede usar GitHub para implementar un sistema de control de código fuente y tener el control de las versiones de los cuadernos. También puede tener un entorno de colaboración donde varios usuarios pueden trabajar con el mismo cuaderno.
* Puede trabajar con cuadernos localmente sin necesidad de un clúster activo. Solo necesita un clúster para probar los cuadernos, no para administrar manualmente los cuadernos o un entorno de desarrollo.
* Puede resultar más fácil configurar su propio entorno de desarrollo local que configurar la instalación de Jupyter en el clúster.  Puede aprovechar todo el software que haya instalado localmente sin configurar uno o más clústeres remotos.

> [!WARNING]
> Con Jupyter instalado en el equipo local, varios usuarios pueden ejecutar el mismo cuaderno en el mismo clúster de Spark al mismo tiempo. En tal situación, se crean varias sesiones de Livy. Si surge un problema y desea depurarlo, es una tarea compleja realizar el seguimiento de a qué sesión de Livy pertenece cada usuario.
>
>

## <a name="seealso"></a>Otras referencias
* [Introducción a Apache Spark en HDInsight de Azure](apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Machine Learning con Apache Spark en HDInsight de Azure](apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones de Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](apache-spark-job-debugging.md)
