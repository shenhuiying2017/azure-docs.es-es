---
title: 'Flujo estructurado de Apache Spark de Kafka a Azure Cosmos DB: Azure HDInsight | Microsoft Docs'
description: Obtenga información acerca de cómo usar el flujo estructurado de Apache Spark para leer datos de Apache Kafka y, después, almacenarlos en Azure Cosmos DB. En este ejemplo, se transmiten datos con Jupyter Notebook de Spark en HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: larryfr
ms.openlocfilehash: 63c536f1a8bdcfbbbd97b904f15ccf83043659e0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31402964"
---
# <a name="use-spark-structured-streaming-with-kafka-and-azure-cosmos-db"></a>Uso del flujo estructurado de Spark con Kafka y Azure Cosmos DB

Obtenga información acerca de cómo usar el flujo estructurado de Spark para leer datos de Apache Kafka en Azure HDInsight y, después, almacenarlos en Azure Cosmos DB.

Azure Cosmos DB es una base de datos de varios modelos distribuidos globalmente. Este ejemplo utiliza un modelo de base de datos de la API de SQL. Para obtener más información, consulte el documento [Bienvenido a Azure Cosmos DB](../cosmos-db/introduction.md).

El flujo estructurado de Spark es un motor de procesamiento de flujo basado en Spark SQL. Permite expresar los cálculos de streaming de la misma forma que el cálculo por lotes de los datos estáticos. Para obtener más información sobre el flujo estructurado, consulte el artículo [Structured Streaming Programming Guide [Alpha]](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html) (Guía de programación de flujo estructurado [alfa]) en Apache.org.

> [!IMPORTANT]
> En este ejemplo, se utiliza Spark 2.2 en HDInsight 3.6.
>
> Los pasos que se describen en este documento crean un grupo de recursos de Azure que contiene un clúster Spark de HDInsight y un clúster Kafka de HDInsight. Estos dos clústeres se encuentran en una instancia de Azure Virtual Network, lo que permite al clúster Spark comunicarse directamente con el clúster Kafka.
>
> Cuando haya terminado los pasos indicados en este documento, no olvide eliminar los clústeres para evitar gastos innecesarios.

## <a name="create-the-clusters"></a>Creación de los clústeres

Apache Kafka en HDInsight no proporciona acceso a los agentes de Kafka a través de Internet. Cualquier comunicación con Kafka debe realizarse en la misma red virtual de Azure que utilizan los nodos del clúster Kafka. En este ejemplo, los clústeres Kafka y Spark se encuentran en una red virtual de Azure. En el diagrama siguiente, se muestra cómo fluye la comunicación entre los clústeres:

![Diagrama de clústeres Spark y Kafka en una red virtual de Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> El servicio Kafka se limita a la comunicación dentro de la red virtual. Se puede acceder a otros servicios del clúster, como SSH y Ambari, a través de Internet. Para más información sobre los puertos públicos disponibles en HDInsight, consulte [Puertos e identificadores URI usados en HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Aunque puede crear manualmente la red virtual de Azure y los clústeres Kafka y Spark, resulta más sencillo utilizar una plantilla de Azure Resource Manager. Siga los pasos que se indican a continuación para implementar una red virtual de Azure y los clústeres Kafka y Spark en la suscripción de Azure.

1. Utilice el siguiente botón para iniciar sesión en Azure y abrir la plantilla en Azure Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
    </a>

    La plantilla de Azure Resource Manager se encuentra en el repositorio de GitHub para este proyecto ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Esta plantilla crea los siguientes recursos:

    * Un clúster de Kafka en HDInsight 3.6.

    * Un clúster de Spark en HDInsight 3.6

    * Una instancia de Azure Virtual Network, que contiene los clústeres de HDInsight.

        > [!NOTE]
        > La red virtual creada por la plantilla utiliza el espacio de direcciones 10.0.0.0/16.

    * Una base de datos de la API de SQL de Azure Cosmos DB.

    > [!IMPORTANT]
    > El cuaderno de flujo estructurado que se utiliza en este ejemplo requiere Spark en HDInsight 3.6. Si usa una versión anterior de Spark en HDInsight, recibirá errores al usar dicho cuaderno.

2. Utilice los datos siguientes para rellenar las entradas de la sección **Implementación personalizada**:
   
    ![Implementación personalizada de HDInsight](./media/apache-kafka-spark-structured-streaming-cosmosdb/parameters.png)

    * **Suscripción**: seleccione su suscripción de Azure.
   
    * **Grupo de recursos**: cree un nuevo grupo de recursos o seleccione uno existente. Este grupo contiene el clúster de HDInsight.

    * **Ubicación**: seleccione una ubicación geográfica próxima a usted.

    * **Nombre de cuenta de Cosmos DB**: este valor se utiliza como nombre para la cuenta de Cosmos DB.

    * **Base Cluster Name** (Nombre base del clúster): este valor se utiliza como nombre base en los clústeres Spark y Kafka. Por ejemplo, si especifica **myhdi**, creará un clúster de Spark denominado __spark-myhdi__ y un clúster de Kafka denominado **kafka-myhdi**.

    * **Versión de clúster**: la versión del clúster de HDInsight.

        > [!IMPORTANT]
        > Este ejemplo se ha probado con HDInsight 3.6 y podría no funcionar con otros tipos de clúster.

    * **Nombre de usuario de inicio de sesión del clúster**: nombre de usuario del administrador de los clústeres Spark y Kafka.

    * **Contraseña de inicio de sesión de clúster**: contraseña del usuario administrador para los clústeres Spark y Kafka.

    * **Nombre de usuario SSH**: usuario de SSH que se va a crear para los clústeres Spark y Kafka.

    * **Contraseña SSH**: contraseña del usuario de SSH para los clústeres Spark y Kafka.

3. Consulte los **Términos y condiciones** y seleccione **Acepto los términos y condiciones indicados anteriormente**.

4. Por último, active **Anclar al panel** y seleccione **Adquirir**. Se tarda aproximadamente 20 minutos en crear los clústeres.

> [!IMPORTANT]
> Puede tardar hasta 45 minutos en crear los clústeres, la red virtual y la cuenta de Cosmos DB.

## <a name="create-the-cosmos-db-database-and-collection"></a>Creación de la base de datos y la colección de Cosmos DB

El proyecto utilizado en este documento almacena los datos en Cosmos DB. Antes de ejecutar el código, primero debe crear una _base de datos_ y una _colección_ en la instancia de Cosmos DB. También se debe recuperar el punto de conexión del documento y la _clave_ usada para autenticar solicitudes en Cosmos DB. 

Una manera de hacerlo es mediante la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). El script siguiente creará una base de datos denominada `kafkadata` y una colección denominada `kafkacollection`. A continuación, devuelve la clave principal.

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter notebook
databaseName='kafkadata'
collectionName='kafkacollection'

# Create the database
az cosmosdb database create --name $name --db-name $databaseName --resource-group $resourceGroupName
# Create the collection
az cosmosdb collection create --collection-name $collectionName --name $name --db-name $databaseName --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb list-keys --name $name --resource-group $resourceGroupName --query primaryMasterKey
```

La información del punto de conexión del documento y de la clave principal es similar al siguiente texto:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]
> Guarde los valores de punto de conexión y de clave, ya que son necesarios en las instancias de Jupyter Notebook.

## <a name="get-the-kafka-brokers"></a>Obtención de los agentes de Kafka

En este ejemplo, el código se conecta a los hosts de los agentes de Kafka del clúster de Kafka. Para buscar las direcciones de los dos hosts de los agentes de Kafka, use el siguiente ejemplo de PowerShell o Bash:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds `
    -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

> [!NOTE]
> En el ejemplo de Bash, se espera que `$CLUSTERNAME` contenga el nombre del clúster de Kafka.
>
> En este ejemplo se utiliza la utilidad [jq](https://stedolan.github.io/jq/) para analizar datos a partir del documento JSON.

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

Cuando se lo pidan, escriba la contraseña de la cuenta de inicio sesión del clúster (admin).

La salida será similar al siguiente texto:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Guarde esta información, ya que se utilizará en las siguientes secciones de este documento.

## <a name="get-the-notebooks"></a>Obtención de los cuadernos

El código para el ejemplo descrito en este documento está disponible en [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb).

## <a name="upload-the-notebooks"></a>Carga de los cuadernos

Siga estos pasos para cargar los cuadernos del proyecto en el clúster de Spark en HDInsight:

1. En el explorador web, conéctese al cuaderno de Jupyter Notebook en el clúster de Spark. En la siguiente URL, reemplace `CLUSTERNAME` por el nombre del clúster de __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Cuando se lo soliciten, escriba el nombre de inicio de sesión del clúster (administrador) y la contraseña que utilizó cuando creó el clúster.

2. En la parte superior derecha de la página, utilice el botón __Cargar__ para cargar el archivo __Stream-taxi-data-to-kafka.ipynb__ en el clúster. Seleccione __Open__ (Abrir) para iniciar la carga.

3. Busque la entrada __Stream-taxi-data-to-kafka.ipynb__ en la lista de cuadernos y seleccione el botón __Cargar__ que está junto a ella.

4. Repita los pasos del 1 al 3 para cargar el cuaderno __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__.

## <a name="load-taxi-data-into-kafka"></a>Cargar los datos del taxi en Kafka

Cuando se hayan cargado los archivos, seleccione la entrada __Stream-taxi-data-to-kafka.ipynb__ para abrir el cuaderno. Siga los pasos descritos en el cuaderno para cargar datos en Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Procesamiento de datos de taxi mediante el flujo estructurado de Spark

En la página principal de Jupyter Notebook, seleccione la entrada __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__. Siga los pasos descritos en el cuaderno para transmitir datos en secuencia de Kafka y en Azure Cosmos DB mediante el flujo estructurado de Spark.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar el flujo estructurado de Spark, consulte los siguientes documentos para obtener más información sobre cómo trabajar con Spark, Kafka y Azure Cosmos DB:

* [Uso del flujo estructurado de Spark (DStream) con Kafka](hdinsight-apache-spark-with-kafka.md)
* [Introducción a Jupyter Notebook y Spark en HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Bienvenido a Azure Cosmos DB](../cosmos-db/introduction.md)
