---
title: "Instalación o actualización de Mono en HDInsight | Microsoft Docs"
description: "Obtenga información sobre cómo usar una versión específica de Mono con un clúster de HDInsight. Mono se usa para ejecutar aplicaciones .NET en clústeres de HDInsight basados en Linux."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: larryfr
ms.custom: hdinsightactive
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: dc303b5386d48cd7daad1630a35c8b8366a9c19f
ms.contentlocale: es-es
ms.lasthandoff: 05/02/2017


---

# <a name="install-or-update-mono-on-hdinsight"></a>Instalación o actualización de Mono en HDInsight

Obtenga información sobre cómo instalar una versión específica de [Mono](https://www.mono-project.com) en HDInsight 3.4 o superior.

Mono se instala en HDInsight 3.4 y versiones posteriores y se utiliza para ejecutar aplicaciones. NET. Para más información sobre la versión de Mono incluida con cada HDInsight, consulte el artículo relativo al [control de versiones de componentes de HDInsight](hdinsight-component-versioning.md). Para instalar una versión diferente en el clúster, use la acción de script en este documento. 

## <a name="how-it-works"></a>Cómo funciona

Este script acepta los siguientes parámetros:

* __Número de versión de Mono__: la versión de Mono para instalar. La versión debe estar disponible en [https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/).

El script instala los paquetes de Mono siguientes:

* __mono-complete__

* __ca-certificates-mono__

## <a name="the-script"></a>La secuencia de comandos

__Ubicación de script__: [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__Requisitos__:

* El script se debe aplicar en los __nodos principales__ y en los __nodos de trabajo__.

## <a name="to-use-the-script"></a>Para usar el script

Para información sobre cómo utilizar este script con HDInsight, vea el documento [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster). Puede ejecutar el script a través de Azure Portal, Azure PowerShell o la CLI de Azure.

Mientras sigue el documento de la acción de script, use el siguiente identificador URI:

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

> [!NOTE]
> Al configurar HDInsight con este script, marque el script como __Persistente__. Esta configuración permite a HDInsight aplicar el script a los nodos de trabajo agregados a través de operaciones de escala.


## <a name="next-steps"></a>Pasos siguientes

Ha aprendido a actualizar o instalar una versión específica de Mono en HDInsight. Para más información sobre el uso de aplicaciones .NET con Mono en HDInsight, vea los documentos siguientes:

* [Uso de .NET con el streaming de MapReduce en Hadoop en HDInsight](hdinsight-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Uso de .NET con Hive y Pig en HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)
* [Desarrollo de topologías de C# para Apache Storm en HDInsight con herramientas de Hadoop para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Migración de soluciones .NET a HDInsight basado en Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md)

Para más información sobre el uso de las acciones de script, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md).
