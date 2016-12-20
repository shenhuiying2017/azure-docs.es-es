---
title: "Creación de clústeres de Hadoop, HBase o Storm basados en Linux en HDInsight mediante la CLI de Azure multiplataforma | Microsoft Docs"
description: "Aprenda a crear clústeres de HDInsight basados en Linux con la CLI de Azure multiplataforma, las plantillas del Administrador de recursos de Azure y la API de REST de Azure. Puede especificar el tipo de clúster (Hadoop, HBase o Storm) o usar scripts para instalar componentes personalizados..."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 50b01483-455c-4d87-b754-2229005a8ab9
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f322b95e3ade4318ccd64f5e2222194bd2fb9361


---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-cli"></a>Crear clústeres basados en Linux en HDInsight con la CLI de Azure
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

La CLI de Azure es una utilidad de línea de comandos multiplataforma que permite administrar los servicios de Azure. Se puede usar, junto con las plantillas de administración de recursos de Azure para crear un clúster de HDInsight, junto con las cuentas de almacenamiento asociadas y otros servicios.

Las plantillas del Administrador de recursos de Azure son documentos JSON que describen un **grupo de recursos** y todos los recursos incluidos (por ejemplo, HDInsight). Este enfoque basado en la plantilla, le permite definir todos los recursos que necesita para HDInsight en una plantilla. También le permite administrar los cambios en el grupo como un todo a través de **implementaciones**, que aplican cambios a todo el grupo.

Los pasos de este documento recorren el proceso de creación de un nuevo clúster de HDInsight mediante la CLI de Azure y una plantilla:

> [!IMPORTANT]
> Los pasos de este documento usan el número predeterminado de nodos de trabajo (4) para un clúster de HDInsight. Si planea crear más de 32 nodos de trabajo (en el momento de la creación de clústeres o escalando el clúster), tiene que seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
> 
> Para obtener más información acerca de los tamaños de nodo y los costos asociados, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
> 
> 

## <a name="prerequisites"></a>Requisitos previos
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **CLI de Azure** Los pasos descritos en este documento se probaron por última vez en la versión 0.10.1 de la CLI de Azure.
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

### <a name="access-control-requirements"></a>Requisitos de control de acceso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="log-in-to-your-azure-subscription"></a>Inicio de sesión en la suscripción de Azure
Siga los pasos que se documentan en [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](../xplat-cli-connect.md) y conéctese a su suscripción con el método **login** .

## <a name="create-a-cluster"></a>Crear un clúster
Los siguientes pasos se deben realizar desde un símbolo del sistema, el shell o la sesión de terminal después de instalar y configurar la CLI de Azure.

1. Use el siguiente comando para autenticarse en su suscripción de Azure:
   
        azure login
   
    Se le solicitará que escriba su nombre y contraseña. Si tiene varias suscripciones de Azure, puede usar `azure account set <subscriptionname>` para establecer la suscripción que usarán los comandos de la CLI de Azure.
2. Cambie al modo de Administrador de recursos de Azure con el siguiente comando:
   
        azure config mode arm
3. Cree un grupo de recursos. Este grupo de recursos contendrá el clúster de HDInsight y la cuenta de almacenamiento asociada.
   
        azure group create groupname location
   
   * Reemplace **groupname** con un nombre único para el grupo. 
   * Reemplace **location** por la región geográfica en la que desee crear los recursos. 
     
       Para obtener una lista de ubicaciones válidas, use el comando `azure location list` y, luego, una de las ubicaciones de la columna **Nombre** .
4. Cree una cuenta de almacenamiento. Esta cuenta de almacenamiento se usará como almacenamiento predeterminado del clúster de HDInsight.
   
        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
   
   * Reemplace **groupname** por el nombre del grupo que creó en el paso anterior.
   * Sustituya **location** con la misma ubicación usada en el paso anterior. 
   * Reemplace **storagename** por un nombre único para la cuenta de almacenamiento.
     
     > [!NOTE]
     > Si desea obtener más información sobre los parámetros utilizados en este comando, use `azure storage account create -h` para ver la ayuda correspondiente.
     > 
     > 
5. Recupere la clave utilizada para tener acceso a la cuenta de almacenamiento.
   
        azure storage account keys list -g groupname storagename
   
   * Reemplace **groupname** por el nombre del grupo de recursos.
   * Sustituya **storagename** con el nombre de la cuenta de almacenamiento.
     
     En los datos que se devuelven, guarde el valor de **key** para **key1**.
6. Cree un clúster de HDInsight.
   
        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername
   
   * Reemplace **groupname** por el nombre del grupo de recursos.
   * Reemplace **Hadoop** por el tipo de clúster que va a crear. Por ejemplo, `Hadoop`, `HBase`, `Storm` o `Spark`.
     
     > [!IMPORTANT]
     > Los clústeres de HDInsight incluyen diversos tipos, que corresponden a la carga de trabajo o la tecnología para los que el clúster está optimizado. No hay ningún método compatible para crear un clúster que combina varios tipos, como Storm y HBase en un clúster. 
     > 
     > 
   * Sustituya **location** con la misma ubicación usada en los pasos anteriores.
   * Reemplace **storagename** con el nombre de la cuenta de almacenamiento.
   * Sustituya **storagekey** con la clave que obtuvo en el paso anterior. 
   * Para el parámetro `--defaultStorageContainer` , use el mismo nombre que utiliza para el clúster.
   * Reemplace **admin** y **httppassword** con el nombre y la contraseña que desea utilizar al acceder al clúster mediante HTTPS.
   * Sustituya **sshuser** y **sshuserpassword** con el nombre de usuario y la contraseña que quiere usar al acceder al clúster mediante SSH.
     
     Pueden pasar varios minutos (por lo general, unos quince) hasta que finalice el proceso de creación del clúster.

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




<!--HONumber=Nov16_HO3-->


