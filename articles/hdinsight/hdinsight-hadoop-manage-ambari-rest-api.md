<properties
   pageTitle="Supervisión y administración de clústeres de HDInsight con la API de REST de Apache Ambari | Microsoft Azure"
   description="Aprenda a usar Ambari para supervisar y administrar clústeres de HDInsight basado en Linux. En este documento, aprenderá a usar la API de REST de Ambari incluida con clústeres de HDInsight."
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
   ms.date="03/29/2016"
   ms.author="larryfr"/>

#Administración de clústeres de HDInsight con la API de REST de Ambari

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifica la administración y la supervisión de un clúster de Hadoop al brindar una API de REST y una interfaz de usuario web fácil de usar. Ambari se incluye en los clústeres de HDInsight basado en Linux y, además, se usa para supervisar el clúster y realizar cambios en la configuración. En este documento, aprenderá los conceptos básicos de trabajar con la API de REST de Ambari al realizar tareas comunes, como encontrar el nombre de dominio completo de los nodos de clúster o encontrar la cuenta de almacenamiento predeterminada que usa el clúster.

> [AZURE.NOTE] La información que aparece en este artículo solo se aplica a clústeres de HDInsight basado en Linux. En el caso de los clústeres de HDInsight basado en Windows, solo hay disponible un subconjunto de funcionalidad de supervisión a través de la API de REST de Ambari. Consulte [Supervisión de Hadoop en HDInsight basado en Windows con la API de Ambari](hdinsight-monitor-use-ambari-api.md).

##Requisitos previos

* [cURL](http://curl.haxx.se/): cURL es una utilidad multiplataforma que se puede usar para trabajar con las API de REST desde la línea de comandos. En este documento, se usa para comunicarse con la API de REST de Ambari.
* [jq](https://stedolan.github.io/jq/): jq es una utilidad de línea de comandos multiplataforma para trabajar con documentos JSON. En este documento, se usa para redistribuir los documentos JSON devueltos desde la API de REST de Ambari.

##<a id="whatis"></a> ¿Qué es Ambari?

[Apache Ambari](http://ambari.apache.org) simplifica la administración de Hadoop al proporcionar una interfaz de usuario web fácil de usar que se puede emplear para aprovisionar, administrar y supervisar clústeres de Hadoop. Los desarrolladores pueden integrar estas funcionalidades en sus aplicaciones mediante el uso de las [API de REST de Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

De manera predeterminada, Ambari viene con los clústeres de HDInsight basado en Linux.

##API de REST

El identificador URI base de la API de REST de Ambari en HDInsight es https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, donde __CLUSTERNAME__ es el nombre del clúster.

> [AZURE.IMPORTANT] Aunque el nombre del clúster en la parte del nombre de dominio completamente cualificado (FQDN) del identificador URI (CLUSTERNAME.azurehdinsight.net,) no distinga entre mayúsculas y minúsculas, otras apariciones en el identificador URI sí que lo hacen. Por ejemplo, si su clúster se denomina MyCluster, las siguientes opciones son identificadores URI válidos:
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster` `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> Los identificadores URI siguientes devolverán un error porque la segunda aparición del nombre no tiene el formato correcto.
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster` `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

La conexión a Ambari en HDInsight requiere HTTPS. También debe autenticarse en Ambari mediante el nombre de la cuenta de administrador (el valor predeterminado es __admin__) y la contraseña que proporcionó cuando se creó el clúster.

Lo siguiente es un ejemplo de cómo usar cURL para realizar una solicitud GET en la API de REST:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
Si la ejecuta, reemplazando __PASSWORD__ por la contraseña de administrador del clúster y __CLUSTERNAME__ por el nombre del clúster, recibirá un documento JSON que comienza con información similar a la siguiente:

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

Como es un documento JSON, normalmente resulta más fácil usar un analizador JSON para recuperar datos. Por ejemplo, si desea recuperar un recuento de alertas (contenidas en el elemento __"Host/host\_status/ALERT"__), puede usar lo siguiente para tener acceso directamente al valor:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report."Host/host_status/ALERT"'
    
Con esto se recupera el documento JSON y, luego, se canaliza el resultado a jq. `'.Clusters.health_report."Host/host_status/ALERT"'` indica el elemento dentro del documento JSON que desea recuperar.

> [AZURE.NOTE] El elemento __Host/host\_status/ALERT__ aparece entre comillas para indicar que "/" es parte del nombre del elemento. Para obtener más información sobre cómo usar jq, consulte el [sitio web de jq](https://stedolan.github.io/jq/).

##Ejemplo: Obtener el FQDN de los nodos del clúster

Cuando trabaja con HDInsight, es posible que deba conocer el nombre de dominio completo (FQDN) del nodo de un clúster. Puede recuperar fácilmente el FQDN de varios nodos del clúster con lo siguiente:

* __Nodos principales__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __Nodos de trabajo__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Nodos Zookeeper__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Observe que todos estos nodos siguen el mismo patrón de consultar a un componente que sabemos que se ejecuta en esos nodos y, luego, recuperar los elementos `host_name` que contienen el FQDN de estos nodos.

El elemento `host_components` del documento devuelto contiene varios elementos. Con `.host_components[]` y, luego, al especificar una ruta de acceso dentro del elemento se aplicará un bucle a través de cada elemento y se extraerá el valor de la ruta de acceso específica. Si solo desea un valor, como la primera entrada de FQDN, puede devolver los elementos como una colección y, luego, seleccionar una entrada específica:

    jq '[.host_components[].HostRoles.host_name][0]'

Esto devolverá el primer FQDN de la colección.

##Ejemplo: Obtener el contenedor y la cuenta de almacenamiento predeterminados

Cuando crea un clúster de HDInsight, debe usar una cuenta de almacenamiento de Azure y un contenedor de blobs como el almacenamiento predeterminado para el clúster. Puede usar Ambari para recuperar esta información una vez creado el clúster. Por ejemplo, si desea escribir datos de manera programática directamente en el contenedor.

Lo siguiente recuperará el identificador URI de WASB del almacenamiento predeterminado de los clústeres:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE] Esto devolverá la primera configuración aplicada al servidor (`service_config_version=1`), la que contendrá esta información. Si recupera un valor que se modificó después de la creación del clúster, es posible que deba enumerar las versiones de configuración y recuperar la más reciente.

Esto devolverá un valor similar al siguiente, donde __CONTAINER__ es el contenedor predeterminado y __ACCOUNTNAME__ es el nombre de la cuenta de almacenamiento de Azure:

    wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net

Luego, puede usar esta información con la [CLI de Azure](../xplat-cli-install.md) para cargar y descargar datos del contenedor. Por ejemplo:

1. Obtenga el grupo de recursos para la cuenta de almacenamiento. Reemplace __ACCOUNTNAME__ por el nombre de la cuenta de almacenamiento que se recuperó de Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Esto devolverá el nombre del grupo de recursos de la cuenta.
    
    > [AZURE.NOTE] Si este comando no devuelve nada, debe cambiar la CLI de Azure al modo Azure Resource Manager y ejecutar el comando de nuevo. Para cambiar al modo Azure Resource Manager, use el siguiente comando:
    >
    > `azure config mode arm`
    
2. Obtenga la clave de la cuenta de almacenamiento. Reemplace __GROUPNAME__ por el grupo de recursos del paso anterior. Reemplace __ACCOUNTNAME__ por el nombre de la cuenta de almacenamiento:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Esto devolverá la clave principal de la cuenta.
    
3. Use el comando upload para almacenar un archivo en el contenedor:

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Reemplace __ACCOUNTNAME__ por el nombre de la cuenta de almacenamiento. Reemplace __ACCOUNTKEY__ por la clave que se recuperó anteriormente. __FILEPATH__ es la ruta de acceso al archivo que desea cargar, mientras que __BLOBPATH__ es la ruta de acceso en el contenedor.

    Por ejemplo, si desea que el archivo aparezca en HDInsight en wasb://example/data/filename.txt, __BLOBPATH__ sería `example/data/filename.txt`.

##Ejemplo: Actualizar configuración de Ambari

1. Obtener la configuración actual, que Ambari almacena como la "configuración deseada":

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
        
    Esto devolverá un documento JSON que contiene la configuración actual (identificada por el valor _tag_) para los componentes instalados en el clúster. Por ejemplo, lo siguiente es un extracto de los datos devueltos de un tipo de clúster Spark.
    
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

    En esta lista, debe copiar el nombre del componente (por ejemplo, __spark\_thrift\_sparkconf__ y el valor __tag__.
    
2. Recupere la configuración del componente y la etiqueta mediante el comando siguiente. Reemplace __spark-thrift-sparkconf__ e __INITIAL__ con el componente y la etiqueta para los que desea recuperar la configuración.

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    
    CUrl recupera el documento JSON y, después, jq se usa para realizar algunas modificaciones a la hora de crear una plantilla que podamos usar para agregar o modificar los valores de configuración. Específicamente, hace lo siguiente:
    
    * Crea un valor único que contiene la cadena "versión" y la fecha, que se almacena en __newtag__.
    * Crea un documento raíz para la nueva configuración deseada.
    * Obtiene el contenido de la matriz .items y lo agrega en el elemento __desired\_config__.
    * Elimina los elementos __href__, __version__ y __Config__, ya que estos no son necesarios para enviar una nueva configuración.
    * Agrega un nuevo elemento __tag__ y establece su valor en __version ###__ donde la parte numérica se basa en la fecha actual. Cada configuración debe tener una etiqueta única.
    
    Por último, los datos se guardan en el documento __newconfig.json__. La estructura del documento será similar a la siguiente:
    
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

3. Abra el documento __newconfig.json__ y agregue o modifique los valores del objeto __properties__. Por ejemplo, cambie el valor de __"spark.yarn.am.memory"__ de __"1g"__ a __"3g"__ y agregue un nuevo elemento para __"spark.kryoserializer.buffer.max"__ con un valor de __"256m"__.

        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",

    Cuando haya terminado de realizar modificaciones, guarde el archivo.

4. Use lo siguiente para enviar la configuración actualizada a Ambari.

        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
        
    Este comando canaliza el contenido del archivo __newconfig.json__ a la solicitud CUrl, que lo envía al clúster como la nueva configuración deseada. Esto devolverá un documento JSON. El elemento __versionTag__ de este documento debería coincidir con la versión que envió, y el objeto __configs__ contendrá los cambios de configuración que solicitó.

###Ejemplo: Reiniciar un componente de servicio

En este momento, si observa la IU web de Ambari, el servicio Spark le indicará que debe reiniciarse antes de que la nueva configuración surta efecto. Siga los siguientes pasos para reiniciar el servicio. Analizar más detenidamente indicará

1. Use lo siguiente para habilitar el modo de mantenimiento para el servicio Spark.

        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Esta acción envía un documento JSON al servidor (incluido en la instrucción `echo`), que activa el modo de mantenimiento. Puede comprobar que el servicio se encuentra ahora en el modo de mantenimiento usando la siguiente solicitud.
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
        
    Esto devolverá un valor de `"ON"`.

3. A continuación, use lo siguiente para desactivar el servicio.

        echo '{"RequestInfo": {"context" :"Stopping the Spark service"}, "Body": {"ServiceInfo": {"state": "INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
        
    Debería recibir una respuesta similar a la siguiente.
    
        {
            "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
            "Requests" : {
                "id" : 29,
                "status" : "Accepted"
            }
        }
    
    El valor `href` devuelto para este identificador URI está usando la dirección IP interna del nodo de clúster. Para usarlo desde fuera del clúster, reemplace la parte "10.0.0.18:8080" con el FQDN del clúster. Por ejemplo, lo siguiente recuperará el estado de la solicitud.
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    
    Si este valor devuelve `"COMPLETED"`, entonces la solicitud ha finalizado.

4. Una vez finalizada la solicitud anterior, use lo siguiente para iniciar el servicio.

        echo '{"RequestInfo": {"context" :"Restarting the Spark service"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Una vez reiniciado el servicio, se usará la nueva configuración.

5. Por último, use lo siguiente para desactivar el modo de mantenimiento.

        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

##Pasos siguientes

Para obtener una referencia completa de la API de REST, consulte [Referencia de API de Ambari V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE] Cierta funcionalidad de Ambari está deshabilitada, puesto que está administrada por el servicio en la nube de HDInsight; por ejemplo, agregar o quitar hosts del clúster o agregar nuevos servicios.

<!---HONumber=AcomDC_0330_2016-->