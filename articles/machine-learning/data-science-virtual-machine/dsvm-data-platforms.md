---
title: Plataformas de datos para Data Science Virtual Machine en Azure | Microsoft Docs
description: Plataformas de datos para Data Science Virtual Machine.
keywords: "herramientas de ciencia de datos, máquina virtual de ciencia de datos, herramientas para la ciencia de datos, ciencia de datos de linux"
services: machine-learning
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
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 9b8beb51c555c6125fa3b0abbad892d627a180b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="data-platforms"></a>Plataformas de datos

Data Science Virtual Machine (DSVM) permite crear los análisis en una amplia variedad de plataformas de datos. Además de interfaces para plataformas de datos remotos, DSVM proporciona una instancia local para el desarrollo rápido y la creación de prototipos. 

Las siguientes son las herramientas de la plataforma de datos compatibles con DSVM. 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| ¿Qué es?   | Una instancia de base de datos relacional local      |
| Ediciones compatibles de DSVM      | Windows      |
| Usos típicos      | Desarrollo rápido de forma local con un conjunto de datos más pequeño <br/> Ejecución de R en base de datos   |
| Vínculos a ejemplos      |    Una pequeña muestra de conjunto de datos de ciudad de Nueva York se carga en la instancia de SQL Database `nyctaxi`. <br/> Ejemplo de Jupyter que muestra Microsoft R y el análisis en bases de datos se puede encontrar en:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Herramientas relacionadas en DSVM       | SQL Server Management Studio <br/> Controladores ODBC/JDBC<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> La edición para desarrolladores de SQL Server 2016 solo puede utilizarse para fines de desarrollo y prueba. Se necesita una licencia o una de las máquinas virtuales de SQL Server para ejecutarlo en producción. 


### <a name="setup"></a>Configuración

El servidor de bases de datos ya está configurado y los servicios de Windows relacionados con SQL Server (como `SQL Server (MSSQLSERVER)`) están configurados para ejecutarse automáticamente. El único paso manual que se tiene que ejecutar consiste en habilitar el análisis en bases de datos mediante Microsoft R. Puede hacerlo ejecutando el siguiente comando como una acción de una sola vez en SQL Server Management Studio (SSMS) tras iniciar sesión como administrador del equipo, abra una "nueva consulta" en SSMS, asegúrese de que la base de datos seleccionada es `master` y, a continuación, ejecute: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
Para ejecutar SQL Server Management Studio, puede buscar "SQL Server Management Studio" en la lista de programas o usar la búsqueda de Windows para buscarlo y ejecutarlo. Cuando se le piden credenciales, elija "Autenticación de Windows" y use el nombre del equipo o ```localhost``` en nombre de SQL Server. 

### <a name="how-to-use--run-it"></a>¿Cómo se usa/ejecuta?  

El servidor de bases de datos con la instancia de base de datos predeterminada se ejecuta automáticamente de forma predeterminada. Puede usar herramientas como SQL Server Management Studio en la máquina virtual para tener acceso a la base de datos de SQL Server de forma local. La cuenta local de administradores tienen acceso de administrador en la base de datos. 

También DSVM incluye controladores ODBC y JDBC para comunicarse con SQL Server, instancias de Azure SQL Database y Azure SQL Data Warehouse desde aplicaciones escritas en varios idiomas, como Python, R. 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>¿Cómo se configura/instala en DSVM? 

El servidor SQL Server está instalado de manera estándar. Se puede encontrar en `C:\Program Files\Microsoft SQL Server`. La instancia de R en base de datos se encuentra en `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. DSVM también tiene una instancia de R Server independiente instalada en `C:\Program Files\Microsoft\R Server\R_SERVER`. Estas dos instancias de R no comparten las bibliotecas.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (independiente)

| | |
| ------------- | ------------- |
| ¿Qué es?   | Una instancia independiente (nodo único In-Process) de la popular plataforma Apache Spark, un sistema de procesamiento de datos y aprendizaje automático rápido y a gran escala     |
| Ediciones compatibles de DSVM      | Linux <br /> Windows (experimental)      |
| Usos típicos      | * Rápido desarrollo de aplicaciones de Spark/PySpark localmente con un conjunto de datos más pequeño y más adelante implementación en grandes clústeres Spark como Azure HDInsight<br/> * Prueba del contexto de Microsoft R Server Spark <br />* Uso de la biblioteca de código abierto [MMLSpark](https://github.com/Azure/mmlspark) de Microsoft o SparkML para crear aplicaciones de aprendizaje automático  |
| Vínculos a ejemplos      |    Ejemplo de Jupyter: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft R Server (contexto Spark): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Herramientas relacionadas en DSVM       | PySpark, Scala<br/>Jupyter (Kernels Spark/PySpark)<br/>Microsoft R Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Cómo usarlo
Puede ejecutar Spark enviando trabajos de Spark en la línea de comandos con los comandos `spark-submit` o `pyspark`. También puede crear una instancia de Jupyter Notebook creando un nuevo bloc de notas con el kernel de Spark. 

Puede usar Spark de R mediante bibliotecas como SparkR, Sparklyr o Microsoft R Server que están disponibles en DSVM. Vea punteros a los ejemplos en la tabla anterior. 

> [!NOTE]
> La ejecución de Microsoft R Server en el contexto de Spark de DSVM solo se admite en la edición de Ubuntu Linux DSVM. 



### <a name="setup"></a>Configuración
Antes de ejecutar en el contexto de Spark en Microsoft R Server en la edición de Ubuntu Linux DSVM, tiene que llevar a cabo un paso de configuración una sola vez con el fin de habilitar un HDFS de Hadoop de un solo nodo y una instancia de Yarn. De manera predeterminada, los servicios de Hadoop están instalados pero deshabilitados en la DSVM. Para habilitarlos, debe ejecutar los comandos siguientes como raíz la primera vez:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Puede detener los servicios relacionados de Hadoop cuando no los necesite; para hacerlo, ejecute ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` En el directorio `/dsvm/samples/MRS` hay disponible un ejemplo que demuestra cómo desarrollar y probar MRS en un contexto de Spark remoto (que es la instancia independiente de Spark en DSVM). 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>¿Cómo se configura/instala en DSVM? 
|Plataforma|Ubicación de instalación ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Bibliotecas para acceder a datos de Azure Blob o Azure Data Lake Storage (ADLS) y utilizando las bibliotecas de aprendizaje automático de MMLSpark de Microsoft están preinstaladas en $SPARK_HOME/JAR. Estos archivos JAR se carga automáticamente cuando se inicia Spark. De forma predeterminada Spark utiliza datos en el disco local. Para que la instancia de Spark en DSVM acceda a los datos almacenados en Azure Blob o ADLS es necesario crear o configurar el archivo `core-site.xml` basado en la plantilla que se encuentra en $SPARK_HOME/conf/core-site.xml.template (donde hay marcadores de posición para las configuraciones de Blob y ADLS) con las credenciales adecuadas para Azure Blob y Azure Data Lake Storage. Encontrar más pasos para crear las credenciales del servicio ADLS [aquí](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory#create-an-active-directory-application). Una vez que las credenciales para Azure Blob o ADLS se escriben en el archivo core-site.xml, puede hacer referencia a los datos almacenados en esos orígenes con el prefijo URI de wasb: / / o adl: / /. 

