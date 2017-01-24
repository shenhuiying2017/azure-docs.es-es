---
title: "Supervisión y administración de clústeres de HDInsight con la API de REST de Apache Ambari | Microsoft Docs"
description: "Aprenda a usar Ambari para supervisar y administrar clústeres de HDInsight basado en Linux. En este documento, aprenderá a usar la API de REST de Ambari incluida con clústeres de HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2400530f-92b3-47b7-aa48-875f028765ff
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/02/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 11523a95a8eac687d391c23cc1e29fa0684a40ae
ms.openlocfilehash: 2723716eb1ec823809d34ce1698d25a0ed9d012b


---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Administración de clústeres de HDInsight con la API de REST de Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifica la administración y la supervisión de un clúster de Hadoop al brindar una API de REST y una interfaz de usuario web fácil de usar. Ambari se incluye en los clústeres de HDInsight basado en Linux y, además, se usa para supervisar el clúster y realizar cambios en la configuración. En este documento, aprenderá los fundamentos del trabajo con la API de REST de Ambari y a realizar tareas comunes con cURL.

> [!IMPORTANT]
> Los ejemplos de este documento se han probado con PowerShell en Windows 10 y Bash. En muchos casos, el mismo comando funciona en ambas plataformas. En casos donde hay diferencia, se proporcionan ejemplos de PowerShell y Bash.

## <a name="prerequisites"></a>Requisitos previos

* [cURL](http://curl.haxx.se/): cURL es una utilidad multiplataforma que se puede usar para trabajar con las API de REST desde la línea de comandos. En este documento, se usa para comunicarse con la API de REST de Ambari.

    > [!WARNING]
    > Si usa PowerShell, debe quitar el alias predeterminado del comando `curl` ejecutando el comando `remove-item alias:curl`. Si no quita el alias, recibirá errores como No se puede procesar el parámetro porque el nombre de parámetro 'u' es ambiguo.

* [jq](https://stedolan.github.io/jq/): jq es una utilidad de línea de comandos multiplataforma para trabajar con documentos JSON. En este documento, se usa para redistribuir los documentos JSON devueltos desde la API de REST de Ambari.

* [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) (versión preliminar): una utilidad de línea de comandos multiplataforma para trabajar con servicios de Azure.
  
[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

## <a name="a-idwhatisawhat-is-ambari"></a><a id="whatis"></a> ¿Qué es Ambari?

[Apache Ambari](http://ambari.apache.org) simplifica la administración de Hadoop al proporcionar una interfaz de usuario web fácil de usar que se puede utilizar para aprovisionar, administrar y supervisar clústeres de Hadoop. Los desarrolladores pueden integrar estas funcionalidades en sus aplicaciones mediante el uso de las [API de REST de Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

De manera predeterminada, Ambari viene con los clústeres de HDInsight basado en Linux.

## <a name="rest-api"></a>API de REST

El URI de base para la API de REST en los clústeres de HDInsight es https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, donde **CLUSTERNAME** es el nombre del clúster de Storm en HDInsight.

> [!IMPORTANT]
> Aunque el nombre del clúster en la parte del nombre de dominio completo (FQDN) del identificador URI (CLUSTERNAME.azurehdinsight.net) no distinga entre mayúsculas y minúsculas, otras apariciones en el identificador URI sí lo hacen. Por ejemplo, si su clúster se denomina MyCluster, las siguientes opciones son identificadores URI válidos:
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> Los identificadores URI siguientes devuelven un error porque la segunda aparición del nombre no tiene el formato correcto.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

La conexión a Ambari en HDInsight requiere HTTPS. Al autenticar la conexión, debe usar el nombre de la cuenta de administrador (el valor predeterminado es **admin**) y la contraseña que proporcionó al crear el clúster.

En el ejemplo siguiente se usa cURL para realizar una solicitud GET a la API de REST. Reemplace **PASSWORD** por la contraseña de administrador del clúster. Reemplace **CLUSTERNAME** por el nombre del clúster:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"

La respuesta es un documento JSON que empieza con información similar a la siguiente:

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

Como es un documento JSON, resulta más fácil usar un analizador JSON para trabajar con los datos. Por ejemplo, en el ejemplo siguiente se usa jq para mostrar solamente el elemento `health_report` .

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report'

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>Ejemplo: Obtener el FQDN de los nodos del clúster

Cuando trabaja con HDInsight, es posible que deba conocer el nombre de dominio completo (FQDN) del nodo de un clúster. Puede recuperar fácilmente el FQDN de varios nodos del clúster con lo siguiente:

* **Nodos principales**: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`

* **Nodos de trabajo**: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`

* **Nodos Zookeeper**: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Tenga en cuenta que todos estos ejemplos siguen el mismo patrón:

1. Consultar un componente que sabemos que se ejecuta en esos nodos.

2. Recuperar los elementos `host_name` , que contienen el FQDN de estos nodos.

El elemento `host_components` del documento devuelto contiene varios elementos. Con `.host_components[]`y, luego, al especificar una ruta de acceso dentro del elemento se aplicará un bucle a través de cada elemento y se extraerá el valor de la ruta de acceso específica. Si solo desea un valor, como la primera entrada de FQDN, puede devolver los elementos como una colección y, luego, seleccionar una entrada específica:

    jq '[.host_components[].HostRoles.host_name][0]'

Esto devuelve el primer FQDN de la colección.

## <a name="example-get-the-default-storage-account-and-container"></a>Ejemplo: Obtener el contenedor y la cuenta de almacenamiento predeterminados

Cuando crea un clúster de HDInsight, debe usar una cuenta de almacenamiento de Azure y un contenedor de blobs como el almacenamiento predeterminado para el clúster. Puede usar Ambari para recuperar esta información una vez creado el clúster. Por ejemplo, si desea escribir datos de manera programática directamente en el contenedor.

Lo siguiente recuperará el identificador URI de WASB del almacenamiento predeterminado de los clústeres:

```bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

PowerShell tiene reglas ligeramente diferentes para el uso de comillas simples y dobles. Use el siguiente comando de PowerShell:

```PowerShell
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["""fs.defaultFS"""] | select(. != null)'
```

> [!NOTE]
> Esto devuelve la primera configuración aplicada al servidor (`service_config_version=1`), que contiene esta información. Si recupera un valor que se modificó después de crear el clúster, es posible que deba enumerar las versiones de configuración y recuperar la más reciente.

Esto devuelve un valor similar al ejemplo siguiente, donde **CONTAINER** es el contenedor predeterminado y **ACCOUNTNAME** es el nombre de la cuenta de Azure Storage:

    wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

Luego, puede usar esta información con la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) para cargar y descargar datos del contenedor.

1. Use el comando siguiente para obtener el identificador único de la cuenta de almacenamiento. En el comando siguiente, reemplace **ACCOUNTNAME** por el nombre de la cuenta de Storage que se recuperó de Ambari:

        az storage account list --query "[?name=='ACCOUNTNAME'].id" --out list

2. Para obtener una clave para la cuenta de almacenamiento, utilice lo siguiente. Reemplace **STORAGEID** por el nombre de la cuenta de almacenamiento:

        az storage account keys list --ids STORAGEID --query "keys[?keyName=='key1'].value" --out list

    Esto devolverá la clave principal de la cuenta.

3. Use el comando upload para almacenar un archivo en el contenedor:
   
        az storage blob upload --account-name ACOUNTNAME --account-key ACCOUNTKEY -f FILEPATH -c CONTAINER -n BLOBPATH
   
    Reemplace **ACCOUNTNAME** por el nombre de la cuenta de almacenamiento. Reemplace **ACCOUNTKEY** por la clave que se recuperó anteriormente. Reemplace __CONTAINER__ por el nombre del contenedor. **FILEPATH** es la ruta de acceso al archivo que desea cargar, mientras que **BLOBPATH** es la ruta de acceso en el contenedor.
   
    Por ejemplo, si desea que el archivo aparezca en HDInsight en wasbs://example/data/filename.txt, **BLOBPATH** sería `example/data/filename.txt`.

## <a name="example-update-ambari-configuration"></a>Ejemplo: Actualizar configuración de Ambari

1. Obtener la configuración actual, que Ambari almacena como la "configuración deseada":
   
        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
   
    Esto devuelve un documento JSON que contiene la configuración actual (identificada por el valor *tag*) de los componentes instalados en el clúster. Por ejemplo, el siguiente es un extracto de los datos devueltos de un tipo de clúster Spark.
   
        "spark-metrics-properties" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-fairscheduler" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-sparkconf" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        }
   
    En esta lista, debe copiar el nombre del componente (por ejemplo, **spark\_thrift\_sparkconf** y el valor **tag**).

2. Recupere la configuración del componente y la etiqueta mediante el comando siguiente. Reemplace **spark-thrift-sparkconf** e **INITIAL** por el componente y la etiqueta cuya configuración desea recuperar.
   
    ```bash
    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    ```PowerShell
    $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
    $now = Get-Date
    $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```
   
    cURL recupera el documento JSON y después se usa jq para realizar modificaciones en los datos con el fin de crear una plantilla. Después, la plantilla se usa para agregar o modificar los valores de configuración. Específicamente, hace lo siguiente:
   
    * Crea un valor único que contiene la cadena "version" y la fecha, que se almacena en **newtag**.

    * Crea un documento raíz para la nueva configuración deseada.

    * Obtiene el contenido de la matriz `.items[]` y lo agrega al elemento **desired_config**.

    * Elimina los elementos **href**, **version** y **Config**, porque no son necesarios para enviar una nueva configuración.

    * Agrega un nuevo elemento **tag** y establece su valor en **version#################**. La parte numérica se basa en la fecha actual. Cada configuración debe tener una etiqueta única.
     
        Por último, los datos se guardan en el documento **newconfig.json**. La estructura del documento será similar a la del siguiente ejemplo:
     
     ```json
    {
        "Clusters": {
            "desired_config": {
            "tag": "version1459260185774265400",
            "type": "spark-thrift-sparkconf",
            "properties": {
                ....
            },
            "properties_attributes": {
                ....
            }
        }
    }
    ```

3. Abra el documento **newconfig.json** y agregue o modifique los valores del objeto **properties**. En el ejemplo siguiente se cambia el valor de **”spark.yarn.am.memory”** de **”1g”** por **”3g”** y se agrega un nuevo elemento para **”spark.kryoserializer.buffer.max”** con un valor de **”256m”**.
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    Cuando haya terminado de realizar modificaciones, guarde el archivo.
4. Use el comando siguiente para enviar la configuración actualizada a Ambari.
   
        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
   
    Este comando canaliza el contenido del archivo **newconfig.json** a la solicitud Curl, que lo envía al clúster como la nueva configuración deseada. La solicitud cURL devuelve un documento JSON. El elemento **versionTag** de este documento debería coincidir con la versión que envió y el objeto **configs** contendrá los cambios de configuración que solicitó.

### <a name="example-restart-a-service-component"></a>Ejemplo: Reiniciar un componente de servicio

En este momento, si observa la IU web de Ambari, el servicio Spark le indicará que debe reiniciarse antes de que la nueva configuración surta efecto. Siga los siguientes pasos para reiniciar el servicio.

1. Use lo siguiente para habilitar el modo de mantenimiento para el servicio Spark:
   
        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
   
    Este comando envía un documento JSON al servidor (incluido en la instrucción `echo` ), que activa el modo de mantenimiento.
    Para comprobar que el servicio ahora está en modo de mantenimiento, puede usar la siguiente solicitud:
   
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
   
    Esto devolverá un valor de `"ON"`.

2. A continuación, use lo siguiente para desactivar el servicio:
    
    ```
    echo '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
    ```
    
    Este comando devuelve una respuesta similar a la siguiente.
   
    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```
    
    El valor `href` devuelto para este identificador URI está usando la dirección IP interna del nodo de clúster. Para usarlo desde fuera del clúster, reemplace la parte "10.0.0.18:8080" con el FQDN del clúster. Por ejemplo, el siguiente comando recupera el estado de la solicitud.
    
    ```
    curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    ```
    
    Si devuelve un valor `"COMPLETED"` , significa que la solicitud ha finalizado.

3. Una vez finalizada la solicitud anterior, use lo siguiente para iniciar el servicio.
   
        echo '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
   
    Cuando se reinicia el servicio, incluye las nuevas opciones de configuración.

4. Por último, use lo siguiente para desactivar el modo de mantenimiento.
   
        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

## <a name="next-steps"></a>Pasos siguientes

Para obtener una referencia completa de la API de REST, consulte [Referencia de API de Ambari V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).




<!--HONumber=Dec16_HO1-->


