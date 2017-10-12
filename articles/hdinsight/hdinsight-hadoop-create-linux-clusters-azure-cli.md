---
title: "Creación de clústeres de Hadoop mediante la línea de comandos - Azure HDInsight | Microsoft Docs"
description: "Aprenda a crear clústeres de HDInsight con la CLI multiplataforma de Azure 1.0."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 50b01483-455c-4d87-b754-2229005a8ab9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/26/2017
ms.author: larryfr
ms.openlocfilehash: 791edd4e56c7957458d49f6f3bd87b67e96db7a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Creación de clústeres de HDInsight mediante la CLI de Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Los pasos de este tutorial describen la creación de un clúster de HDInsight 3.5 mediante la CLI de Azure 1.0.

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).


## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **CLI de Azure** Los pasos de este documento se probaron por última vez en la versión 0.10.14 de la CLI de Azure.

    > [!IMPORTANT]
    > Los pasos descritos en este documento no funcionan con la CLI de Azure 2.0. La CLI de Azure 2.0 no admite la creación de un clúster de HDInsight.

## <a name="log-in-to-your-azure-subscription"></a>Inicio de sesión en la suscripción de Azure

Siga los pasos que se documentan en [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](../xplat-cli-connect.md) y conéctese a su suscripción con el método **login** .

## <a name="create-a-cluster"></a>Crear un clúster

Los siguientes pasos deben realizarse desde una línea de comandos como PowerShell o Bash.

1. Use el siguiente comando para autenticarse en su suscripción de Azure:

        azure login

    Se le solicitará que escriba su nombre y contraseña. Si tiene varias suscripciones de Azure, puede usar `azure account set <subscriptionname>` para establecer la suscripción que usarán los comandos de la CLI de Azure.

2. Cambie al modo de Administrador de recursos de Azure con el siguiente comando:

        azure config mode arm

3. Cree un grupo de recursos. Este grupo de recursos contiene el clúster de HDInsight y la cuenta de almacenamiento asociada.

        azure group create groupname location

    * Sustituya `groupname` por un nombre único para el grupo.

    * Sustituya `location` por la región geográfica en la que quiere crear el grupo.

       Para obtener una lista de ubicaciones válidas, use el comando `azure location list` y luego una de las ubicaciones de la columna `Name`.

4. Cree una cuenta de almacenamiento. Esta cuenta de almacenamiento se usa como almacenamiento predeterminado del clúster de HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Sustituya `groupname` por el nombre del grupo creado en el paso anterior.

    * Sustituya `location` por la misma ubicación usada en el paso anterior.

    * Sustituya `storagename` por un nombre único para la cuenta de almacenamiento.

        > [!NOTE]
        > Si desea obtener más información sobre los parámetros utilizados en este comando, use `azure storage account create -h` para ver la ayuda correspondiente.

5. Recupere la clave utilizada para tener acceso a la cuenta de almacenamiento.

        azure storage account keys list -g groupname storagename

    * Sustituya `groupname` por el nombre del grupo de recursos.
    * Sustituya `storagename` por el nombre de la cuenta de almacenamiento.

     En los datos que se devuelven, guarde el valor `key` de `key1`.

6. Cree un clúster de HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Sustituya `groupname` por el nombre del grupo de recursos.

    * Sustituya `Hadoop` por el tipo de clúster que quiere crear. Por ejemplo, `Hadoop`, `HBase`, `Kafka`, `Spark` o `Storm`.

     > [!IMPORTANT]
     > Los clústeres de HDInsight incluyen diversos tipos, que corresponden a la carga de trabajo o la tecnología para los que el clúster está optimizado. No hay ningún método admitido para crear un solo clúster que combine varios tipos, como Storm y HBase.

    * Sustituya `location` por la misma ubicación usada en pasos anteriores.

    * Sustituya `storagename` por el nombre de la cuenta de almacenamiento.

    * Sustituya `storagekey` por la clave obtenida en el paso anterior.

    * Para el parámetro `--defaultStorageContainer` , use el mismo nombre que utiliza para el clúster.

    * Sustituya `admin` y `httppassword` por el nombre y la contraseña que quiere usar en el acceso al clúster mediante HTTPS.

    * Sustituya `sshuser` y `sshuserpassword` por el nombre de usuario y la contraseña que quiere usar en el acceso al clúster mediante SSH

    > [!IMPORTANT]
    > En este ejemplo se crea un clúster con dos nodos de trabajo. Si quiere cambiar el número de nodos de trabajo después de la creación del clúster, realice operaciones de escalado. Si planea usar más de 32 nodos de trabajo, tiene que seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM. Durante la creación del clúster, puede establecer el tamaño del nodo principal mediante el parámetro `--headNodeSize`.
    >
    > Para obtener más información acerca de los tamaños de nodo y los costos asociados, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Pueden pasar varios minutos (por lo general, unos quince) hasta que finalice el proceso de creación del clúster.

## <a name="troubleshoot"></a>Solución de problemas

Si experimenta problemas con la creación de clústeres de HDInsight, consulte los [requisitos de control de acceso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Pasos siguientes

Una vez creado correctamente un clúster de HDInsight correctamente mediante la CLI de Azure, use los siguientes vínculos para aprender a trabajar con él:

### <a name="hadoop-clusters"></a>Clústeres Hadoop

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clústeres HBase

* [Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desarrollo de aplicaciones de Java para HBase en HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clústeres Storm

* [Desarrollo de topologías de Java para Storm en HDInsight](hdinsight-storm-develop-java-topology.md)
* [Uso de componentes de Python en Storm en HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementación y supervisión de topologías con Storm en HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)
