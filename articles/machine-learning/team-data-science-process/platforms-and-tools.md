---
title: Plataformas y herramientas para proyectos de equipo de ciencia de datos en Azure | Microsoft Docs
description: "Detalla y describe los recursos de datos y análisis disponibles para las empresas que se estandarizan en el proceso de ciencia de datos en equipo."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev;
ms.openlocfilehash: 6e6e7a199547135bd9695ff0867e8d5a98341b12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="platforms-and-tools-for-data-science-team-projects"></a>Plataformas y herramientas para proyectos de equipo de ciencia de datos

Microsoft proporciona un espectro completo de servicios de datos y análisis, así como los recursos para plataformas locales y en la nube. Se pueden implementar para que la ejecución de los proyectos de ciencia de datos sean eficaces y escalables. En el [proceso de ciencia de datos en equipo](overview.md) (TDSP) se puede encontrar la guía para equipos que implementan proyectos de ciencia de datos de forma controlada por versiones, de colaboración y cuyo seguimiento se puede realizar.  Para obtener un esquema de los roles personales y sus tareas asociadas que se controlan en un equipo de ciencia de datos que se estandariza en este proceso, consulte [Roles y tareas del proceso de ciencia de datos en equipo](roles-tasks.md).

Los servicios de datos y análisis disponibles para los equipos de ciencia de datos mediante el proceso de ciencia de datos en equipo incluyen los siguientes:

- Data Science Virtual Machine (tanto Windows como Linux CentOS)
- Clústeres de HDInsight Spark
- SQL Data Warehouse
- Azure Data Lake
- Clústeres de HDInsight Hive
- Azure File Storage
- SQL Server 2016 R Services

En este documento, se describen brevemente los recursos y se proporcionan vínculos a tutoriales que los equipos del proceso de ciencia de datos en equipo han publicado. Pueden ayudar a aprender cómo usarlos paso a paso y comenzar a usarlos para compilar las aplicaciones inteligentes. Podrá encontrar más información sobre estos recursos en las páginas de producto. 

## <a name="data-science-virtual-machine-dsvm"></a>Data Science Virtual Machine (DSVM)

La máquina virtual de ciencia de datos que ofrece Microsoft para Windows y Linux contiene herramientas conocidas para actividades de desarrollo y modelado de ciencia de datos. Incluye herramientas tales como:

- Microsoft R Server Developer Edition 
- Anaconda Python Distribution
- Jupyter Notebook para Python y R 
- Visual Studio Community Edition con Python y Herramientas de R en Windows/Eclipse en Linux
- Power BI Desktop para Windows
- SQL Server 2016 Developer Edition en Windows/Postgres en Linux

También incluye **herramientas de Machine Learning y AI** como CNTK (un kit de herramientas de aprendizaje profundo de código abierto de Microsoft), xgboost, mxnet y Vowpal Wabbit.

Actualmente la máquina virtual de ciencia de datos está disponible en los sistemas operativos **Windows** y **CentOS Linux**. Elija el tamaño de la máquina virtual de ciencia de datos (número de núcleos de CPU y la cantidad de memoria) en función de las necesidades de los proyectos de ciencia de datos que van a ejecutar en ella. 

Para más información sobre las ediciones de Windows de máquina virtual de ciencias de datos, consulte [Microsoft Data Science Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) en Azure Marketplace. Para la edición Linux de la máquina virtual de ciencia de datos, consulte [Linux Data Science Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Para obtener información sobre cómo ejecutar de forma eficaz alguna de las tareas comunes de ciencia de datos en la máquina virtual de ciencia de datos, consulte [Ten things you can do on the Data science Virtual Machine](../data-science-virtual-machine/vm-do-ten-things.md) (Diez cosas que puede hacer en Data Science Virtual Machine).


## <a name="azure-hdinsight-spark-clusters"></a>Clústeres de Azure HDInsight Spark

Apache Spark es una plataforma de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones analíticas de Big Data. El motor de procesamiento Spark se ha creado para ofrecer velocidad, facilidad de uso y análisis sofisticados. Las funcionalidades de cálculo en memoria de Spark lo convierten en una buena opción para algoritmos iterativos en los cálculos de gráficos y aprendizaje automático. Spark también es compatible con Azure Storage Blob (WASB), por lo que se pueden procesar los datos existentes almacenados en Azure fácilmente mediante Spark.

Cuando crea un clúster Spark en HDInsight, aprovisiona recursos de proceso de Azure con Spark instalado y configurado. Se tardan unos diez minutos en crear un clúster Spark en HDInsight. Almacene los datos que se van procesar en Azure Blob Storage. Para más información sobre cómo usar Azure Blob Storage con HDInsight, consulte [Uso de Azure Storage con clústeres de Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

El equipo de proceso de ciencia de datos en equipo de Microsoft ha publicado dos tutoriales completos sobre cómo usar clústeres de Azure HDInsight Spark para crear soluciones de ciencia de datos, una que use Python y otra Scala. Para más información sobre **clústeres de Azure HDInsight Spark**, consulte [Introducción a Apache Spark en HDInsight para Linux](../../hdinsight/hdinsight-apache-spark-overview.md). Para obtener información sobre cómo crear una solución de ciencia de datos mediante **Python** en un clúster de Azure HDInsight Spark, consulte [Información general sobre la ciencia de datos con Azure HDInsight Spark](spark-overview.md). Para obtener información sobre cómo crear una solución de ciencia de datos mediante **Scala** en un clúster de Azure HDInsight Spark, consulte [Ciencia de datos mediante Scala y Spark en Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Almacenamiento de datos SQL de Azure

Azure SQL Data Warehouse le permite escalar los recursos de proceso fácilmente y en segundos, sin tener que realizar un aprovisionamiento excesivo o pagar en exceso. Además, ofrece la opción única de pausar el uso de los recursos de proceso, lo que aporta la libertad para administrar mejor los costos de la nube. La capacidad de implementar recursos de proceso escalables permite poner todos los datos en Azure SQL Data Warehouse. Los costos de almacenamiento son mínimos y puede ejecutar el proceso solo en aquellas partes de los conjuntos de datos que desea analizar. 

Para más información sobre Azure SQL Data Warehouse, consulte el sitio web de [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse). Para obtener información sobre cómo crear soluciones completas de análisis avanzado con SQL Data Warehouse, consulte [Proceso de ciencia de datos en equipo en acción: uso de SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake es un repositorio para toda la empresa de todos los tipos de datos recopilados en una única ubicación antes de cualquier requisito o esquema formal que se imponga. Esta flexibilidad permite a todos los tipos de datos conservarse en un Data Lake, con independencia del tamaño, estructura o velocidad de ingestión. Las organizaciones pueden usar después Hadoop o análisis avanzado para buscar patrones en estos Data Lake. Los Data Lake también pueden actuar como un repositorio para la preparación de datos de bajo costo antes de conservar los datos y pasarlos a un almacenamiento de datos.

Para más información sobre Azure Data Lake, consulte la entrada de blob [Introducing Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/) (Introducción a Azure Data Lake). Para obtener información sobre cómo crear una solución de ciencia de datos completa y escalable con Azure Data Lake, consulte [Ciencia de datos escalables con Azure Data Lake: tutorial completo](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Clústeres de Azure HDInsight Hive (Hadoop)

Apache Hive es un sistema de almacén de datos para Hadoop, que permite realizar resúmenes de datos, consultas y análisis de datos mediante HiveQL (una lenguaje de consultas similar a SQL). Hive se puede usar para explorar los datos de forma interactiva o para crear trabajos de procesamiento por lotes reutilizables.

Hive le permite proyectar la estructura del proyecto en datos que en gran medida no están estructurados. Después de definir la estructura, puede usar Hive para consultar esos datos en un clúster Hadoop sin tener que usar, incluso sin conocer, Java o MapReduce. HiveQL (el lenguaje de consultas de Hive) permite escribir consultas con instrucciones similares a T-SQL.

Para los científicos de datos, Hive puede ejecutar funciones de Python definidas por el usuario en consultas de Hive para procesar registros. Esta capacidad amplía considerablemente la funcionalidad de consultas de Hive en el análisis de datos. Específicamente, permite a los científicos de datos realizar ingeniería de características escalables en los lenguajes con los que están más familiarizados, como Python y HiveQA de tipo SQL. 

Para más información sobre los clústeres de Azure HDInsight Hive, consulte [Uso de Hive y HiveQL con Hadoop en HDInsight](../../hdinsight/hdinsight-use-hive.md). Para aprender cómo crear una solución de ciencia de datos completa y escalable a clústeres de Hive de HDInsight de Azure, consulte [el proceso de ciencia de datos en equipo en acción: uso de clústeres de Hadoop de HDInsight](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Azure File Storage es un servicio que ofrece recursos compartidos de archivos en la nube mediante el protocolo Bloque de mensajes del servidor (SMB) estándar. Se admiten SMB 2.1 y SMB 3.0. Con Almacenamiento de archivos de Azure puede migrar aplicaciones heredadas basadas en recursos compartidos de archivos a Azure con rapidez y sin necesidad de costosas reescrituras. Las aplicaciones que se ejecutan en máquinas virtuales de Azure o en servicios en la nube o desde clientes locales pueden montar un recurso compartido de archivos en la nube, igual que una aplicación de escritorio monta un recurso compartido SMB típico. Cualquier número de componentes de aplicación puede montar y acceder simultáneamente al recurso compartido de Almacenamiento de archivos.

Especialmente útil para los proyectos de ciencia de datos es la capacidad para crear un almacén de archivos de Azure como el lugar para compartir datos del proyecto con los miembros del equipo de proyecto. Cada uno de ellos tiene acceso entonces a la misma copia de los datos en el almacenamiento de archivos de Azure. También puede utilizar este almacenamiento de archivos para compartir conjuntos de características generados durante la ejecución del proyecto. Si el proyecto es una interacción con los clientes, estos pueden crear un almacenamiento de archivos de Azure en su propia suscripción de Azure para compartir los datos de proyecto y las características con el usuario. De esta manera, el cliente tiene control total sobre los recursos de datos del proyecto. Para más información sobre Azure File Storage, consulte [Introducción a Azure File Storage en Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) o [Uso de Azure File Storage con Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2016-r-services"></a>SQL Server 2016 R Services

R Services (en base de datos) proporciona una plataforma para desarrollar e implementar aplicaciones inteligentes que pueden descubrir nuevas perspectivas. Puede usar el completo y eficaz lenguaje R, incluidos los numerosos paquetes proporcionados por la comunidad R, para crear modelos y generar predicciones con sus datos de SQL Server. Como R Services (en base de datos) integra el lenguaje R con SQL Server, los análisis se mantienen cerca de los datos, lo que elimina los costos y riesgos de seguridad asociados con el movimiento de datos.

R Services (en base de datos) admite el lenguaje R de código abierto con un completo conjunto de tecnologías y herramientas de SQL Server. Ofrece un rendimiento superior, seguridad, confiabilidad y facilidad de uso. Puede implementar soluciones en R con herramientas conocidas y convenientes. Las aplicaciones de producción pueden llamar al entorno en tiempo de ejecución de R y recuperar las predicciones y los objetos visuales mediante Transact-SQL. También puede usar las bibliotecas de ScaleR para mejorar la escalabilidad y el rendimiento de las soluciones de R. Para más información, consulte [SQL Server R Services](https://msdn.microsoft.com/library/mt604845.aspx).

El equipo del proceso de ciencia de datos en equipo de Microsoft ha publicado dos tutoriales completos que muestran cómo crear soluciones de ciencia de datos en SQL Server 2016 R Services, uno para los programadores de R y otro para desarrolladores de SQL. Para los **programadores de R**, consulte el [tutorial completo de ciencia de datos](https://msdn.microsoft.com/library/mt612857.aspx). Para los **desarrolladores de SQL**, consulte [Análisis de R en bases de datos para desarrolladores de SQL (tutorial)](https://msdn.microsoft.com/library/mt683480.aspx).


## <a name="appendix"></a>Apéndice: Herramientas para configurar los proyectos de ciencia de datos

### <a name="install-git-credential-manager-on-windows"></a>Instalación del administrador de credenciales de Git en Windows

Si está siguiendo el proceso de ciencia de datos en equipo en **Windows**, debe instalar la **el Administrador de credenciales (GCM), Git** para comunicarse con los repositorios de Git. Para instalar GCM, primero debe instalar **Chocolaty**. Para instalar Chocolaty y GCM, ejecute los siguientes comandos en Windows PowerShell como **administrador**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Instalación de Git en máquinas Linux (CentOS)

Ejecute el siguiente comando de bash para instalar Git en máquinas Linux (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generación de clave SSH pública en máquinas Linux (CentOS)

Si se usan máquinas Linux (CentOS) para ejecutar los comandos de Git, tendrá que agregar la clave SSH pública de su máquina a su servidor de VSTS, para que esta máquina la reconozca el servidor de VSTS. En primer lugar, debe generar una clave SSH pública y agregar la clave a las claves públicas de SSH en la página de configuración de seguridad de VSTS. 

- Para generar la clave SSH, ejecute los dos comandos siguientes: 

        ssh-keygen
        cat .ssh/id_rsa.pub

![](./media/platforms-and-tools/resources-1-generate_ssh.png)

- Copie todo la clave ssh completa, incluida *ssh-rsa*. 
- Inicie sesión en el servidor de VSTS. 
- Haga clic en **<Su nombre\>** en la esquina superior derecha de la página y haga clic en **Seguridad**. 
    
    ![](./media/platforms-and-tools/resources-2-user-setting.png)

- Haga clic en **Claves públicas de SSH** y haga clic en **+Agregar**. 

    ![](./media/platforms-and-tools/resources-3-add-ssh.png)

- Pegue la clave SSH que acaba de copiar en el cuadro de texto y guárdela.


## <a name="next-steps"></a>Pasos siguientes

También se proporcionan tutoriales completos que muestran todos los pasos del proceso en **escenarios concretos** . Se enumeran y enlazan con descripciones en miniatura en los [tutoriales de ejemplos](walkthroughs.md). En ellos se ilustra cómo combinar herramientas y servicios locales y en la nube en un flujo de trabajo o canalización para crear una aplicación inteligente. 

Para ver ejemplos de ejecución de los pasos en el Proceso de ciencia de datos en equipo que usan Azure Machine Learning Studio, consulte la ruta de aprendizaje [Con Azure ML](http://aka.ms/datascienceprocess).