---
title: "Supervisión y administración de clústeres de Hadoop mediante la API de REST de Ambari: Azure HDInsight | Microsoft Docs"
description: "Aprenda a usar Ambari para supervisar y administrar clústeres de Hadoop en Azure HDInsight. En este documento, aprenderá a usar la API de REST de Ambari incluida con clústeres de HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2400530f-92b3-47b7-aa48-875f028765ff
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/22/2018
ms.author: larryfr
ms.openlocfilehash: 44066a3b4d5bebc67b089bebc2f3c11d33c77000
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Administración de clústeres de HDInsight con la API de REST de Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Aprenda a usar la API de REST de Ambari para administrar clústeres de Hadoop en Azure HDInsight.

Apache Ambari simplifica la administración y la supervisión de un clúster de Hadoop al brindar una API de REST y una interfaz de usuario web fácil de usar. Ambari se incluye en los clústeres de HDInsight que usan el sistema operativo Linux. Puede usar Ambari para supervisar el clúster y realizar cambios de configuración.

## <a id="whatis"></a>¿Qué es Ambari?

[Apache Ambari](http://ambari.apache.org) proporciona una interfaz de usuario web que puede usarse para administrar y supervisar clústeres de Hadoop. Los desarrolladores pueden integrar estas funcionalidades en sus aplicaciones mediante el uso de las [API de REST de Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

De manera predeterminada, Ambari viene con los clústeres de HDInsight basado en Linux.

## <a name="how-to-use-the-ambari-rest-api"></a>Uso de la API de REST de Ambari

> [!IMPORTANT]
> La información y los ejemplos de este documento necesitan un clúster de HDInsight que usa el sistema operativo Linux. Para más información, vea la [introducción a HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

Los ejemplos de este documento se proporcionan para Bourne Shell (Bash) y PowerShell. Lo ejemplos de Bash se han probado con GNU Bash 4.3.11, pero deben funcionar con otros shells de Unix. Los ejemplos de PowerShell se han probado con PowerShell 5.0, pero deben funcionar con PowerShell 3.0 o posterior.

Si usa __Bourne Shell__ (Bash), debe tener instalado lo siguiente:

* [cURL](http://curl.haxx.se/): es una utilidad que se puede usar para trabajar con las API de REST desde la línea de comandos. En este documento, se usa para comunicarse con la API de REST de Ambari.

Tanto si usa Bash como PowerShell, también debe tener [jq](https://stedolan.github.io/jq/) instalado. Jq es una utilidad para trabajar con documentos JSON. Se utiliza en **todos** los ejemplos de Bash y en **uno** de los ejemplos de PowerShell.

### <a name="base-uri-for-ambari-rest-api"></a>URI base para la API de REST de Ambari

El URI de base para la API de REST en los clústeres de HDInsight es https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, donde **CLUSTERNAME** es el nombre del clúster de Storm en HDInsight.

> [!IMPORTANT]
> Aunque el nombre del clúster en la parte del nombre de dominio completo (FQDN) del identificador URI (CLUSTERNAME.azurehdinsight.net) no distinga entre mayúsculas y minúsculas, otras apariciones en el identificador URI sí lo hacen. Por ejemplo, si su clúster se denomina `MyCluster`, las siguientes opciones son identificadores URI válidos:
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> Los identificadores URI siguientes devuelven un error porque la segunda aparición del nombre no tiene el formato correcto.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

### <a name="authentication"></a>Autenticación

La conexión a Ambari en HDInsight requiere HTTPS. Utilice el nombre de la cuenta de administrador (el valor predeterminado es **admin**) y la contraseña que proporcionó durante la creación del clúster.

## <a name="examples-authentication-and-parsing-json"></a>Ejemplos: Autenticación y análisis JSON

Los ejemplos siguientes muestran cómo realizar una solicitud GET en la API de REST de Ambari de base:

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
```

> [!IMPORTANT]
> En los ejemplos de Bash de este documento se realizan las hipótesis siguientes:
>
> * El nombre de inicio de sesión del clúster es el valor predeterminado de `admin`.
> * `$CLUSTERNAME` contiene el nombre del clúster. Puede establecer este valor mediante `set CLUSTERNAME='clustername'`.
> * Cuando se le solicite, escriba la contraseña de la cuenta de inicio de sesión del clúster (administrador).

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$resp.Content
```

> [!IMPORTANT]
> En los ejemplos de PowerShell de este documento se realizan las hipótesis siguientes:
>
> * `$creds` es un objeto de credencial que contiene el inicio de sesión de administrador y la contraseña para el clúster. Puede establecer este valor mediante `$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"` y proporcionar las credenciales cuando se le soliciten.
> * `$clusterName` es una cadena que contiene el nombre del clúster. Puede establecer este valor mediante `$clusterName="clustername"`.

Ambos ejemplos devuelven un documento JSON que empieza con información similar al siguiente ejemplo:

```json
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
    ...
```

### <a name="parsing-json-data"></a>Análisis de datos JSON

En el ejemplo siguiente se utiliza `jq` para analizar el documento de respuesta JSON y mostrar solo la información `health_report` de los resultados.

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME" \
| jq '.Clusters.health_report'
```

PowerShell 3.0 y las versiones posteriores proporcionan el cmdlet `ConvertFrom-Json`, que convierte el documento JSON en un objeto con el que resulta más fácil trabajar desde PowerShell. En el ejemplo siguiente se utiliza `ConvertFrom-Json` para mostrar solo la información `health_report` de los resultados.

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

> [!NOTE]
> Aunque la mayoría de los ejemplos de este documento usan `ConvertFrom-Json` para mostrar los elementos del documento de respuesta, el ejemplo [Actualización de la configuración de Ambari](#example-update-ambari-configuration) utiliza jq. Jq se utiliza en este ejemplo para crear una plantilla a partir del documento de respuesta JSON.

Para obtener una referencia completa de la API de REST, consulte [Referencia de API de Ambari V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>Ejemplo: Obtener el FQDN de los nodos del clúster

Cuando trabaja con HDInsight, es posible que deba conocer el nombre de dominio completo (FQDN) del nodo de un clúster. Puede recuperar fácilmente el FQDN de varios nodos del clúster mediante los siguientes ejemplos:

* **Todos los nodos**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" \
    | jq '.items[].Hosts.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.Hosts.host_name
    ```

* **Nodos principales**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/NAMENODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Nodos de trabajo**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/DATANODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Nodos de Zookeeper**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

## <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>Ejemplo: Obtener la dirección IP interna de nodos de clúster

> [!IMPORTANT]
> No se puede acceder directamente a través de Internet a las direcciones IP devueltas por los ejemplos de esta sección. Solo se puede acceder a ellas dentro de la instancia de Azure Virtual Network que contiene el clúster de HDInsight.
>
> Para obtener más información sobre el uso de HDInsight y de las redes virtuales, vea [Extensión de las funcionalidades de HDInsight con una instancia de Azure Virtual Network personalizada](hdinsight-extend-hadoop-virtual-network.md).

Para buscar la dirección IP, debe conocer el nombre de dominio completo (FQDN) interno de los nodos del clúster. Una vez que tenga el FQDN, ya puede obtener la dirección IP del host. En los ejemplos siguientes primero se consulta en Ambari el FQDN de todos los nodos del host y después también se consulta en Ambari la dirección IP de cada host.

```bash
for HOSTNAME in $(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```

> [!TIP]
> En los ejemplos anteriores se solicita la contraseña. En este ejemplo se ejecuta el comando `curl` varias veces, por lo que se proporciona la contraseña como `$PASSWORD` para evitar varios mensajes.

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts"
$resp = Invoke-WebRequest -Uri $uri -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

## <a name="example-get-the-default-storage"></a>Ejemplo: Obtener el almacenamiento predeterminado

Cuando crea un clúster de HDInsight, debe usar una cuenta de Azure Storage o Data Lake Store como el almacenamiento predeterminado para el clúster. Puede usar Ambari para recuperar esta información una vez creado el clúster. Por ejemplo, si desea leer y escribir datos en el contenedor fuera de HDInsight.

En los ejemplos siguientes se recupera la configuración de almacenamiento predeterminada del clúster:

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]
> Estos ejemplos devuelven la primera configuración aplicada al servidor (`service_config_version=1`), que contiene esta información. Si recupera un valor que se modificó después de crear el clúster, es posible que deba enumerar las versiones de configuración y recuperar la más reciente.

El valor devuelto es similar a uno de los ejemplos siguientes:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net`: este valor indica que el clúster usa una cuenta de Azure Storage para el almacenamiento predeterminado. El valor `ACCOUNTNAME` es el nombre de la cuenta de almacenamiento. La parte `CONTAINER` es el nombre del contenedor de blobs de la cuenta de almacenamiento. El contenedor es la raíz del almacenamiento HDFS compatible del clúster.

* `adl://home`: este valor indica que el clúster usa Azure Data Lake Store como almacenamiento predeterminado.

    Para buscar el nombre de cuenta de Data Lake Store, utilice los siguientes ejemplos:

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    El valor devuelto es similar a `ACCOUNTNAME.azuredatalakestore.net`, donde `ACCOUNTNAME` es el nombre de la cuenta de Data Lake Store.

    Para encontrar el directorio en Data Lake Store que contiene el almacenamiento para el clúster, use los siguientes ejemplos:

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    El valor devuelto es similar a `/clusters/CLUSTERNAME/`. Este valor es una ruta de acceso dentro de la cuenta de Data Lake Store. Esta ruta de acceso es la raíz del sistema de archivos HDFS compatible para el clúster. 

> [!NOTE]
> El cmdlet `Get-AzureRmHDInsightCluster` proporcionado por [Azure PowerShell](/powershell/azure/overview) también devuelve la información de almacenamiento para el clúster.


## <a name="example-get-configuration"></a>Ejemplo: Obtener configuración

1. Obtenga las configuraciones que están disponibles para el clúster.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    $respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    $respObj.Content
    ```

    Esto devuelve un documento JSON que contiene la configuración actual (identificada por el valor *tag*) de los componentes instalados en el clúster. Por ejemplo, el siguiente es un extracto de los datos devueltos de un tipo de clúster Spark.
   
   ```json
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
   ```

2. Obtenga la configuración del componente en el que está interesado. En el ejemplo siguiente, reemplace `INITIAL` por el valor de etiqueta devuelto por la solicitud anterior.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=core-site&tag=INITIAL"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=core-site&tag=INITIAL" `
        -Credential $creds
    $resp.Content
    ```

    Este ejemplo devuelve un documento JSON que contiene la configuración actual del componente `core-site`.

## <a name="example-update-configuration"></a>Ejemplo: Actualizar la configuración

1. Obtener la configuración actual, que Ambari almacena como la "configuración deseada":

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    Esto devuelve un documento JSON que contiene la configuración actual (identificada por el valor *tag*) de los componentes instalados en el clúster. Por ejemplo, el siguiente es un extracto de los datos devueltos de un tipo de clúster Spark.
   
    ```json
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
    ```
   
    En esta lista, debe copiar el nombre del componente (por ejemplo, **spark\_thrift\_sparkconf** y el valor **tag**).

2. Recupere la configuración del componente y la etiqueta mediante los comandos siguientes:
   
    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" \
    | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    ```powershell
    $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
    $now = Get-Date
    $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=spark-thrift-sparkconf&tag=INITIAL" `
        -Credential $creds
    $resp.Content | jq --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    > [!NOTE]
    > Reemplace **spark-thrift-sparkconf** e **INITIAL** por el componente y la etiqueta cuya configuración desea recuperar.
   
    Jq se utiliza para convertir los datos recuperados de HDInsight en una nueva plantilla de configuración. En concreto, estos ejemplos realizan las siguientes acciones:
   
    * Crea un valor único que contiene la cadena "version" y la fecha, que se almacena en `newtag`.

    * Crea un documento raíz para la nueva configuración deseada.

    * Obtiene el contenido de la matriz `.items[]` y lo agrega al elemento **desired_config**.

    * Elimina los elementos `href`, `version` y `Config`, porque no son necesarios para enviar una nueva configuración.

    * Agrega un nuevo elemento `tag` con un valor de `version#################`. La parte numérica se basa en la fecha actual. Cada configuración debe tener una etiqueta única.
     
    Por último, los datos se guardan en el documento `newconfig.json`. La estructura del documento será similar a la del siguiente ejemplo:
     
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

3. Abra el documento `newconfig.json` y modifique o agregue valores en el objeto `properties`. En el ejemplo siguiente se cambia el valor `"spark.yarn.am.memory"` de `"1g"` a `"3g"`. También agrega `"spark.kryoserializer.buffer.max"` con un valor de `"256m"`.
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    Cuando haya terminado de realizar modificaciones, guarde el archivo.

4. Use los comandos siguientes para enviar la configuración actualizada a Ambari.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```
   
    Estos comandos envían el contenido del archivo **newconfig.json** al clúster como la nueva configuración deseada. La solicitud devuelve un documento JSON. El elemento **versionTag** de este documento debería coincidir con la versión que envió y el objeto **configs** contiene los cambios de configuración que solicitó.

### <a name="example-restart-a-service-component"></a>Ejemplo: Reiniciar un componente de servicio

En este momento, si observa la IU web de Ambari, el servicio Spark indica que debe reiniciarse antes de que la nueva configuración surta efecto. Siga los siguientes pasos para reiniciar el servicio.

1. Use lo siguiente para habilitar el modo de mantenimiento para el servicio Spark:

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```
   
    Estos comandos envían un documento JSON al servidor que activa el modo de mantenimiento. Para comprobar que el servicio ahora está en modo de mantenimiento, puede usar la siguiente solicitud:
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```
   
    El valor devuelto es `ON`.

2. A continuación, use lo siguiente para desactivar el servicio:

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```
    
    La respuesta es similar al siguiente ejemplo:
   
    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```
    
    > [!IMPORTANT]
    > El valor `href` devuelto para este identificador URI está usando la dirección IP interna del nodo de clúster. Para usarlo desde fuera del clúster, reemplace la parte "10.0.0.18:8080" con el FQDN del clúster. 
    
    Los comandos siguientes recuperan el estado de la solicitud:

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Una respuesta de `COMPLETED` indica que la solicitud ha finalizado.

3. Una vez finalizada la solicitud anterior, use lo siguiente para iniciar el servicio.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```
   
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```
    El servicio ya usa la nueva configuración.

4. Por último, use lo siguiente para desactivar el modo de mantenimiento.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>pasos siguientes

Para obtener una referencia completa de la API de REST, consulte [Referencia de API de Ambari V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

