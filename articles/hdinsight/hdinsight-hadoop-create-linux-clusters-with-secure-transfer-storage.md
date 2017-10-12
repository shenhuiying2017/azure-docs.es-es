---
title: "Creación de un clúster de Hadoop con cuentas de almacenamiento de transferencia segura en Azure HDInsight |Microsoft Docs"
description: "Aprenda a crear clústeres de HDInsight con cuentas de almacenamiento de Azure habilitadas para transferencia segura."
keywords: "introducción a hadoop,hadoop linux,inicio rápido de hadoop,transferencia segura,cuenta de almacenamiento de azure"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/21/2017
ms.author: jgao
ms.openlocfilehash: 370b2f081930fe88527436a1a127309aed6681f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Creación de un clúster de Hadoop con cuentas de almacenamiento de transferencia segura en Azure HDInsight

La característica [Se requiere transferencia segura](../storage/common/storage-require-secure-transfer.md) mejora la seguridad de su cuenta de Azure Storage al aplicar todas las solicitudes a su cuenta mediante una conexión segura. Esta característica y el esquema wasbs solo son compatibles con la versión de clúster de HDInsight 3.6 o posterior. 

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Suscripción de Azure**: para crear una cuenta de evaluación gratuita durante un mes, vaya a [azure.microsoft.com/es-es/free/](https://azure.microsoft.com/free).
* **Una cuenta de Azure Storage habilitada para transferencia segura**. Para obtener instrucciones, consulte [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) y [Requerir transferencia segura](../storage/common/storage-require-secure-transfer.md).
* **Un contenedor de blobs en la cuenta de almacenamiento**. 
## <a name="create-cluster"></a>Crear clúster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


En esta sección, se crea un clúster de Hadoop en HDInsight mediante una [plantilla de Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). La plantilla también se encuentra en [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/). No es necesario tener experiencia en el uso de la plantilla de Resource Manager para seguir este tutorial. Para conocer otros métodos de creación de clústeres y las propiedades que se usan en este tutorial, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Haga clic en la imagen siguiente para iniciar sesión en Azure y abrir la plantilla de Resource Manager en Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Siga las instrucciones para crear el clúster con las especificaciones siguientes: 

    - Especifique la versión 3.6 de HDInsight.  La versión predeterminada es 3.5. Se requiere la versión 3.6 o posterior.
    - Especifique una cuenta de almacenamiento habilitada para transferencia.
    - Escriba el nombre corto para la cuenta de almacenamiento.
    - La cuenta de almacenamiento y el contenedor de blobs deben crearse con antelación. 

    Para obtener las instrucciones, consulte [Crear un clúster](./hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). 

Si se usa la acción de scrpt para proporcionar sus propios archivos de configuración, debe usar wasbs en la siguiente configuración:

- fs.defaultFS (sitio principal)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Adición de cuentas de almacenamiento adicionales

Existen varias opciones para agregar cuentas de almacenamiento adicionales habilitadas para transferencia segura:

- Modificar la plantilla de Azure Resource Manager de la última sección.
- Crear un clúster mediante [Azure Portal](https://portal.azure.com) y especificar la cuenta de almacenamiento vinculada.
- Use la acción de script para agregar cuentas de almacenamiento adicionales habilitadas para transferencia segura a un clúster de HDInsight existente.  Para más información, consulte [Adición de más cuentas de almacenamiento a HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido a crear un clúster de HDInsight y a habilitar la transferencia segura para las cuentas de almacenamiento.

Para más información sobre el análisis de datos con HDInsight, consulte los siguientes artículos:

* Para más información sobre el uso de Hive con HDInsight, incluida la forma de realizar consultas de Hive desde Visual Studio, consulte [Uso de Hive con HDInsight][hdinsight-use-hive].
* Para más información sobre Pig, un lenguaje usado para transformar datos, consulte [Uso de Pig con HDInsight][hdinsight-use-pig].
* Para más información sobre MapReduce, una manera de escribir programas que procesan datos en Hadoop, consulte [Uso de MapReduce con HDInsight][hdinsight-use-mapreduce].
* Para más información sobre el uso de las herramientas de HDInsight para Visual Studio para analizar datos en HDInsight, consulte [Introducción al uso de herramientas de Hadoop en Visual Studio para HDInsight para ejecutar una consulta de Hive](hdinsight-hadoop-visual-studio-tools-get-started.md).

Para aprender más sobre cómo HDInsight almacena datos o cómo insertar datos en HDInsight, consulte los artículos siguientes:

* Para más información sobre cómo HDInsight usa Azure Storage, consulte [Uso de Azure Storage con HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Para más información sobre cómo cargar datos en HDInsight, consulte [Carga de datos en HDInsight][hdinsight-upload-data].

Para aprender más sobre la creación o administración de un clúster de HDInsight, consulte los siguientes artículos:

* Para más información sobre cómo administrar el clúster de HDInsight basado en Linux, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md).
* Para más información acerca de las opciones que se pueden seleccionar al crear un clúster de HDInsight, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Si está familiarizado con Linux y Hadoop, pero quiere conocer información específica sobre Hadoop en HDInsight, consulte [Información sobre el uso de HDInsight en Linux](hdinsight-hadoop-linux-information.md). Este artículo proporciona información como:
  
  * Direcciones URL para los servicios hospedados en el clúster, por ejemplo, Ambari y WebHCat
  * La ubicación de los archivos y ejemplos de Hadoop en el sistema de archivos local
  * El uso de Azure Storage (WASB) en lugar de HDFS como el almacén de datos predeterminado

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


