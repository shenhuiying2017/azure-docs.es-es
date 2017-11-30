---
title: "Instalación de Presto en clústeres de Azure HDInsight basados en Linux | Microsoft Docs"
description: "Aprenda a instalar Presto y Airpal en clústeres de Hadoop para HDInsight basados en Linux mediante acciones de script."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: af6bf1d69761520d20d0a0c5d872377793f6650b
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>Instalación y uso de Presto en clústeres de Hadoop para HDInsight

En este tema aprenderá a instalar Presto en clústeres de Hadoop para HDInsight con la acción de script. También aprenderá a instalar Airpal en un clúster de HDInsight Presto existente.

> [!IMPORTANT]
> Los pasos descritos en este documento requieren un **clúster de Hadoop para HDInsight 3.5** que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte las [versiones de HDInsight](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>¿Qué es Presto?
[Presto](https://prestodb.io/overview.html) es un motor de consultas SQL de distribución rápida para macrodatos. Presto es adecuado para las consultas interactivas de petabytes de datos. Para más información sobre los componentes de Presto y cómo funcionan en conjunto, consulte [Presto concepts](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst) (Conceptos de Presto).

> [!WARNING]
> Los componentes ofrecidos con HDInsight son totalmente compatibles. Además, el soporte técnico de Microsoft le ayudará a aislar y resolver problemas relacionados con estos componentes.
> 
> Los componentes personalizados, como Presto, reciben un soporte técnico comercialmente razonable para ayudarle a solucionar el problema. Esto podría resolver el problema o pedirle que forme parte de los canales disponibles para las tecnologías de código abierto donde se encuentra la más amplia experiencia para esa tecnología. Por ejemplo, hay diversos sitios de la comunidad que se pueden usar, como el [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Los proyectos de Apache también tienen sitios de proyecto en [http://apache.org](http://apache.org) (por ejemplo, [Hadoop](http://hadoop.apache.org/)).
> 
> 


## <a name="install-presto-using-script-action"></a>Instalación de Presto con una acción de script

En esta sección se proporcionan instrucciones sobre cómo usar el script de ejemplo al crear un nuevo clúster mediante el Portal de Azure. 

1. Inicie el aprovisionamiento de un clúster siguiendo los pasos que se describen en [Aprovisionamiento de clústeres de HDInsight basados en Linux](hdinsight-hadoop-create-linux-clusters-portal.md). Asegúrese de crear el clúster con el flujo de creación de clúster **personalizado**. Tiene que asegurarse de que el clúster que crea cumple los requisitos siguientes.

    a. Tiene que ser un clúster Hadoop con HDInsight versión 3.5.

    b. Tiene que usar Azure Storage como almacén de datos. El uso de Presto en un clúster que utiliza Azure Data Lake Store como opción de almacenamiento aún no se admite. 

    ![Creación de un clúster de HDInsight usando las opciones personalizadas](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. En la hoja **Configuración opcional**, seleccione **Acciones de script** y proporcione la información siguiente:
   
   * **NOMBRE**: escriba un nombre descriptivo para la acción de script.
   * **URI de script de Bash**: `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **PRINCIPAL**: active esta opción.
   * **TRABAJADOR**: active esta opción.
   * **ZOOKEEPER**: desactive esta casilla
   * **PARÁMETROS**: deje este campo en blanco.


3. En la parte inferior de la hoja **Acciones de script**, haga clic en el botón **Seleccionar** para guardar la configuración. Por último, haga clic en el botón **Seleccionar** situado en la parte inferior de la hoja **Configuración avanzada** para guardar la información de configuración opcional.

4. Siga aprovisionando el clúster como se describe en [Aprovisionamiento de clústeres de HDInsight basados en Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]
    > También se puede utilizar Azure PowerShell, la CLI de Azure, el SDK de .NET de HDInsight o las plantillas de Azure Resource Manager para aplicar las acciones de script. También puede aplicar acciones de script a clústeres que ya se estén ejecutando. Para obtener más información, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Uso de Presto con HDInsight

Realice los pasos siguientes para usar Presto en un clúster de HDInsight después de instalarlo mediante los pasos descritos anteriormente.

1. Conéctese al clúster de HDInsight con SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Inicie el shell de Presto con el comando siguiente.
   
        presto --schema default

3. Ejecute una consulta en una tabla de ejemplo, **hivesampletable**, que está disponible en todos los clústeres de HDInsight de forma predeterminada.
   
        select count (*) from hivesampletable;
   
    De forma predeterminada, los conectores [Hive](https://prestodb.io/docs/current/connector/hive.html) y [TPCH](https://prestodb.io/docs/current/connector/tpch.html) para Presto ya están configurados. El conector de Hive está configurado para usar la instalación de Hive que se instala de forma predeterminada, de modo que todas las tablas de Hive estarán visibles automáticamente en Presto.

    Para obtener una descripción detallada de cómo puede utilizar Presto, consulte la [documentación de Presto](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Uso de Airpal con Presto

[Airpal](https://github.com/airbnb/airpal#airpal) es una interfaz de consulta de código abierto basada en web para Presto. Para más información sobre Airpal, consulte la [documentación de Airpal](https://github.com/airbnb/airpal#airpal).

En esta sección, veremos cómo **instalar Airpal en el nodo perimetral** de un clúster de Hadoop para HDInsight que ya tiene Presto instalado. Esto garantiza que la interfaz de consulta web de Airpal está disponible a través de Internet.

1. Mediante SSH, conéctese al nodo principal del clúster de HDInsight en el que instaló Presto:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Una vez que esté conectado, ejecute el siguiente comando.

        sudo slider registry  --name presto1 --getexp presto 
   
    Debe ver algo parecido a lo siguiente:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. De esta salida, anote el valor de la propiedad **value**. Va a necesitar este valor durante la instalación de Airpal en el nodo perimetral del clúster. En la salida anterior, el valor que necesita es **10.0.0.12:9090**.

4. Utilice la plantilla **[aquí](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)** para crear un modo perimetral del clúster de HDInsight y proporcionar los valores como se muestran en la captura de pantalla siguiente.

    ![Instalación de Airpal en el clúster de HDInsight con Presto](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Haga clic en **Comprar**.

6. Una vez que los cambios se aplican a la configuración del clúster, puede tener acceso a la interfaz de web de Airpal siguiendo estos pasos.

    a. En la hoja del clúster, haga clic en **Aplicaciones**.

    ![Inicio de Airpal en el clúster de HDInsight con Presto](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    b. Desde la hoja **Aplicaciones instaladas**, haga clic en **Portal** en Airpal.

    ![Inicio de Airpal en el clúster de HDInsight con Presto](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    c. Cuando se le solicite, escriba las credenciales de administrador que especificó al crear el clúster de Hadoop en HDInsight.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Personalización de una instalación de Presto en un clúster de HDInsight

Una vez haya instalado Presto en un clúster de Hadoop en HDInsight, puede personalizar la instalación para realizar cambios, como actualizar la configuración de memoria, cambiar los conectores, etc. Para ello, siga los pasos que se indican a continuación.

1. Mediante SSH, conéctese al nodo principal del clúster de HDInsight en el que instaló Presto:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Realice los cambios de configuración en el archivo `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Para más información sobre la configuración de presto, consulte [Presto Configuration Options for YARN-Based Clusters](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html) (Opciones de configuración de Presto para clústeres basados en YARN).

3. Detenga y termine la instancia de ejecución actual de presto.

        sudo slider stop presto1 --force
        sudo slider destroy presto1 --force

4. Inicie una nueva instancia de Presto con la personalización.

       sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json

5. Espere a que la nueva instancia esté lista y anote la dirección del coordinador de Presto.


       sudo slider registry --name presto1 --getexp presto

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Generación de datos de pruebas comparativas para clústeres de HDInsight que ejecutan Presto

TPC-DS es el estándar del sector para medir el rendimiento de muchos sistemas de ayuda a la toma de decisiones, incluidos los sistemas de macrodatos. Puede usar Presto en clústeres de HDInsight para generar datos y evaluar cómo se compara con sus propios datos de prueba comparativa de HDInsight. Para más información, consulte [esta página](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="see-also"></a>Consulte también
* [Instalación y uso de Hue en clústeres de HDInsight](hdinsight-hadoop-hue-linux.md). Hue es una interfaz de usuario web que simplifica la creación, la ejecución y el guardado de trabajos de Pig y Hive, así como el examen del almacenamiento predeterminado de su clúster de HDInsight.

* [Instalación de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install-linux.md). Use la personalización del clúster para instalar Giraph en clústeres de Hadoop para HDInsight. Giraph permite realizar un procesamiento gráfico mediante Hadoop, y se puede usar con HDInsight de Azure.

* [Instalación de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install-linux.md). Use la personalización del clúster para instalar Solr en clústeres de Hadoop para HDInsight. Solr le permite realizar potentes operaciones de búsqueda en los datos almacenados.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
