<properties
   	pageTitle="Creación de clústeres de Hadoop, HBase o Storm basados en Linux en HDInsight mediante la CLI de Azure multiplataforma | Microsoft Azure"
   	description="Aprenda a crear clústeres de HDInsight basados en Linux con la CLI de Azure multiplataforma, las plantillas del Administrador de recursos de Azure y la API de REST de Azure. Puede especificar el tipo de clúster (Hadoop, HBase o Storm) o usar scripts para instalar componentes personalizados..."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="06/13/2016"
   	ms.author="larryfr"/>

#Crear clústeres basados en Linux en HDInsight con la CLI de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

La CLI de Azure es una utilidad de línea de comandos multiplataforma que permite administrar los servicios de Azure. Se puede usar, junto con las plantillas de administración de recursos de Azure para crear un clúster de HDInsight, junto con las cuentas de almacenamiento asociadas y otros servicios.

Las plantillas de Administración de recursos de Azure son documentos JSON que describen un __grupo de recursos__ y todos los recursos contenidos en él (por ejemplo, HDInsight). Este enfoque basado en plantillas permite definir todos los recursos que se necesitan para HDInsight en una sola plantilla y administrar los cambios realizados en el grupo en conjunto a través de __implementaciones__ que aplican los cambios al grupo.

Los pasos de este documento recorren el proceso de creación de un nuevo clúster de HDInsight mediante la CLI de Azure y una plantilla:

> [AZURE.IMPORTANT] Los pasos de este documento usan el número predeterminado de nodos de trabajo (4) para un clúster de HDInsight. Si planea crear más de 32 nodos de trabajo, en la creación de clústeres o cambiando el tamaño del clúster después de la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
>
> Para obtener más información acerca de los tamaños de nodo y los costos asociados, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Requisitos previos

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __CLI de Azure__ Los pasos descritos en este documento se probaron por última vez en la versión 0.10.1 de la CLI de Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##Iniciar sesión en su suscripción de Azure

Siga los pasos que se documentan en [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](../xplat-cli-connect.md) y conéctese a su suscripción con el método __login__.

##Crear un clúster

Los siguientes pasos se deben realizar desde un símbolo del sistema, el shell o la sesión de terminal después de instalar y configurar la CLI de Azure.

1. Use el siguiente comando para autenticarse en su suscripción de Azure:

        azure login

    Se le solicitará que escriba su nombre y contraseña. Si tiene varias suscripciones de Azure, puede usar `azure account set <subscriptionname>` para establecer la suscripción que usarán los comandos de la CLI de Azure.

3. Cambie al modo de Administrador de recursos de Azure con el siguiente comando:

        azure config mode arm

4. Cree un nuevo grupo de recursos. Este contendrá el clúster de HDInsight y la cuenta de almacenamiento asociada.

        azure group create groupname location
        
    * Reemplace __groupname__ con un nombre único para el grupo. 
    * Reemplace __location__ por la región geográfica en la que desee crear los recursos. 
    
        Para obtener una lista de ubicaciones válidas, use el comando `azure locations list` y, luego, una de las ubicaciones de la columna __Nombre__.

5. Cree una cuenta de almacenamiento nueva. Se usará como el almacenamiento predeterminado del clúster de HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
        
     * Reemplace __groupname__ por el nombre del grupo que creó en el paso anterior.
     * Sustituya __location__ con la misma ubicación usada en el paso anterior. 
     * Reemplace __storagename__ por un nombre único para la cuenta de almacenamiento.
     
     > [AZURE.NOTE] Si desea obtener más información sobre los parámetros utilizados en este comando, use `azure storage account create -h` para ver la ayuda correspondiente.

5. Recupere la clave utilizada para tener acceso a la cuenta de almacenamiento.

        azure storage account keys list -g groupname storagename
        
    * Reemplace __groupname__ por el nombre del grupo de recursos.
    * Sustituya __storagename__ con el nombre de la cuenta de almacenamiento.
    
    En los datos que se devuelven, guarde el valor de __key__ para __key1__.

6. Cree un clúster de HDInsight nuevo.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Reemplace __groupname__ por el nombre del grupo de recursos.
    * Sustituya __location__ con la misma ubicación usada en los pasos anteriores.
    * Reemplace __storagename__ con el nombre de la cuenta de almacenamiento.
    * Sustituya __storagekey__ con la clave que obtuvo en el paso anterior. 
    * Para el parámetro `--defaultStorageContainer`, use el mismo nombre que utiliza para el clúster.
    * Reemplace __admin__ y __httppassword__ con el nombre y la contraseña que desea utilizar al acceder al clúster mediante HTTPS.
    * Sustituya __sshuser__ y __sshuserpassword__ con el nombre de usuario y la contraseña que quiere usar al acceder al clúster mediante SSH.

    Pueden pasar varios minutos (por lo general, unos quince) hasta que finalice el proceso de creación del clúster.

##Pasos siguientes

Una vez creado correctamente un clúster de HDInsight correctamente mediante la CLI de Azure, use los siguientes vínculos para aprender a trabajar con él:

###Clústeres Hadoop

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###Clústeres HBase

* [Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desarrollo de aplicaciones de Java para HBase en HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###Clústeres Storm

* [Desarrollo de topologías de Java para Storm en HDInsight](hdinsight-storm-develop-java-topology.md)
* [Uso de componentes de Python en Storm en HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementación y supervisión de topologías con Storm en HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_0615_2016-->