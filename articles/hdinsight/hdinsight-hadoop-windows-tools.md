---
title: 'Uso de un equipo Windows con Hadoop en HDInsight: Azure | Microsoft Docs'
description: "Trabaje desde un equipo Windows en Hadoop en HDInsight. Administre y consulte clústeres con PowerShell, Visual Studio y herramientas de Linux. Desarrolle soluciones de macrodatos con .NET."
services: hdinsight
keywords: hadoop en windows,hadoop para windows
author: cjgronlund
manager: jhubbard
ms.author: cgronlun
ms.date: 05/17/2017
ms.topic: article
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.openlocfilehash: e4f231c1f9b903d6cc7f2b062b30d2a072be8493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="work-in-the-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Trabajo en el ecosistema de Hadoop en HDInsight desde un equipo Windows

Aprenda sobre opciones de desarrollo y administración en el equipo Windows para trabajar en el ecosistema de Hadoop en HDInsight. 

HDInsight se basa en Apache Hadoop y componentes de Hadoop, tecnologías de código abierto desarrolladas en Linux. HDInsight versión 3.4 y posteriores usan la distribución Ubuntu de Linux como sistema operativo subyacente para el clúster. Sin embargo, puede trabajar con HDInsight desde un cliente Windows o un entorno de desarrollo de Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Uso de PowerShell para tareas de implementación y administración
Azure PowerShell es un entorno de scripting que se puede usar para controlar y automatizar tareas de implementación y administración en HDInsight desde Windows.

Ejemplos de tareas que puede realizar con PowerShell:

* [Creación de clústeres con PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Ejecución de consultas de Hive con PowerShell](hdinsight-hadoop-use-hive-powershell.md)
* [Administración de clústeres con PowerShell](hdinsight-administer-use-powershell.md)

Siga los pasos para [instalar y configurar Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) para obtener la versión más reciente. Si tiene scripts que se deben modificar para usar los nuevos cmdlets para Azure Resource Manager, consulte [Migración a las herramientas de desarrollo basadas en Azure Resource Manager para clústeres de HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="utilities-you-can-run-in-a-browser"></a>Utilidades que puede ejecutar en un explorador
Las utilidades siguientes tienen una interfaz de usuario web que se ejecuta en un explorador:
* **[Azure Cloud Shell (versión preliminar)](https://docs.microsoft.com/azure/cloud-shell/quickstart)** es un shell de línea de comandos interactivo que se ejecuta en el explorador y desde Azure Portal.
* **[Interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md)** es una utilidad de administración y supervisión que está disponible en Azure Portal y puede usarse para administrar diferentes tipos de trabajos, como:
    * [Utilice Ambari con la API de REST](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Vista de Hive en Ambari](hdinsight-hadoop-use-hive-ambari-view.md)
    * [Vista de Tez en Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Herramientas de Data Lake (Hadoop) para Visual Studio
Use Herramientas de Data Lake para Visual Studio para implementar y administrar topologías de Storm. Herramientas de Data Lake también instala el SDK de SCP.NET, que le permite desarrollar topologías de Storm C# con Visual Studio.

Antes de ir a los siguientes ejemplos, [instale y pruebe Herramientas de Data Lake para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md). 

Ejemplos de tareas que puede hacer con Visual Studio y Herramientas de Data Lake para Visual Studio:
* [Implementación y administración de topologías de Storm desde Visual Studio](hdinsight-storm-deploy-monitor-topology-linux.md)
* [Desarrollo de las topologías de C# para Storm con Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md) Los bits incluyen plantillas de ejemplo para topologías de Storm que puede conectar a bases de datos, como Azure Cosmos DB y SQL Database.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio y el SDK de .NET 

Puede usar Visual Studio con el SDK de .NET para administrar clústeres y desarrollar aplicaciones de macrodatos. Puede usar otros IDE para las siguientes tareas, pero los ejemplos se muestran en Visual Studio.

Ejemplos de tareas que puede realizar con el SDK de .NET en Visual Studio:
* [Creación de clústeres y trabajo en HDInsight desde una aplicación de .NET Framework](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [Ejecución de consultas de Hive mediante el SDK de .NET](hdinsight-hadoop-use-hive-dotnet-sdk.md)
* [Uso de funciones definidas por el usuario de C# con el streaming de Hive y Pig en Hadoop](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

> Sugerencia Si ejecuta soluciones de .NET con clústeres de HDInsight basados en Windows, es un buen momento para planear la migración a clústeres basados en Linux. Para más información, consulte [Migración de soluciones .NET para HDInsight basado en Windows a HDInsight basado en Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA y Eclipse IDE para clústeres de Spark
Tanto [Intellij IDEA](https://www.jetbrains.com/idea/download) como [Eclipse IDE](https://www.eclipse.org/downloads/) sirven para:
* Desarrollar y enviar una aplicación Spark en Scala en un clúster de Spark en HDInsight.
* Acceder a recursos de clúster de Spark.
* Desarrollar y ejecutar localmente una aplicación Spark en Scala.

En estos artículos se muestra cómo hacerlo: 
* Intellij IDEA: [Creación de aplicaciones Spark mediante el complemento de kit de herramientas de Azure para Intellij y el SDK de Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* Eclipse IDE o Scala IDE para Eclipse: [Creación de aplicaciones Spark y el kit de herramientas de Azure para Eclipse](hdinsight-apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>Notebooks en Spark para científicos de datos 
Los clústeres de Apache Spark en HDInsight incluyen notebooks y kernels de Zeppelin que se pueden usar con notebooks de Jupyter. 

* [Aprenda a usar kernels en clústeres de Spark con notebooks de Jupyter para probar aplicaciones Spark](hdinsight-apache-spark-zeppelin-notebook.md)
* [Aprenda a usar notebooks de Zeppelin en clústeres de Spark para ejecutar trabajos de Spark](hdinsight-apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Ejecución en Windows de tecnologías y herramientas basadas en Linux

Si se da una situación en que debe usar una herramienta o tecnología que solo está disponible en Linux, tenga en cuenta las siguientes opciones:

* **Bash (beta) en Windows 10** proporciona un subsistema de Linux en Windows. Bash permite ejecutar utilidades de Linux directamente sin tener que mantener una instalación de Linux dedicada. [Instalación y ejecución de la versión beta de Bash en Windows 10](https://msdn.microsoft.com/commandline/wsl/install_guide)
* **Docker para Windows** proporciona acceso a muchas herramientas basadas en Linux y se puede ejecutar directamente desde Windows. Por ejemplo, puede usar Docker para ejecutar al cliente Beeline para Hive directamente desde Windows. También puede usar Docker para ejecutar un notebook de Jupyter local y conectarse de forma remota con Spark en HDInsight. [Introducción a Docker para Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](http://mobaxterm.mobatek.net/)** permite examinar gráficamente el sistema de archivos del clúster a través de una conexión SSH.

## <a name="next-steps"></a>Pasos siguientes
Si no ha trabajado antes en clústeres basados en Linux, consulte los artículos siguientes:
* [Configuración de Hadoop, Kafka, Spark u otros clústeres](hdinsight-hadoop-provision-linux-clusters.md)
* [Sugerencias para clústeres de HDInsight en Linux](hdinsight-hadoop-linux-information.md)