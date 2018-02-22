---
title: Uso de Azure Functions para enviar datos a Kafka en HDInsight | Microsoft Docs
description: "Aprenda a usar una función de Azure para escribir datos en Kafka en HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2018
ms.author: larryfr
ms.openlocfilehash: c1c03cfcbcb7e0bfdb4a631b9e2ae568f0684069
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="use-kafka-on-hdinsight-from-an-azure-function-app"></a>Uso de Kafka en HDInsight desde una aplicación de función de Azure

Aprenda a crear una aplicación de función de Azure que envía datos a Kafka en HDInsight.

[Azure Functions](https://docs.microsoft.com/azure/azure-functions/) es un servicio de proceso sin servidor. Permite ejecutar código a petición sin tener que aprovisionar o administrar explícitamente la infraestructura.

[Apache Kafka](https://kafka.apache.org) es una plataforma de streaming distribuido de código abierto que se puede utilizar para compilar canalizaciones de datos de streaming en tiempo real y aplicaciones. Kafka también proporciona funcionalidad de agente de mensajería similar a una cola de mensajes, donde puede publicar y suscribirse a las transmisiones de datos con nombre. Kafka en HDInsight proporciona un servicio administrado, altamente escalable y de alta disponibilidad en la nube de Microsoft Azure.

Kafka en HDInsight no proporciona una API en la red pública de Internet. Para publicar o consumir datos de Kafka, debe conectarse al clúster de Kafka mediante una red virtual de Azure. Azure Functions proporciona una manera práctica de crear un punto de conexión público que inserta datos en Kafka en HDInsight mediante una puerta de enlace de Virtual Network.

> [!NOTE]
> El enfoque de este documento está en los pasos necesarios para habilitar Azure Functions para comunicarse con Kafka en HDInsight. El ejemplo en sí es simplemente un productor de Kafka básico para demostrar que la configuración funciona.

## <a name="prerequisites"></a>requisitos previos

* Una aplicación de función de Azure. Para más información, consulte la documentación [Creación de su primera función](../../azure-functions/functions-create-first-azure-function.md).

* Una instancia de Azure Virtual Network. Para trabajar con Azure Functions, la red virtual debe usar uno de los siguientes intervalos IP:

    * 10.0.0.0-10.255.255.255
    * 172.16.0.0-172.31.255.255
    * 192.168.0.0-192.168.255.255

    Para más información, consulte el documento [Integración de su aplicación con una instancia de Azure Virtual Network](../../app-service/web-sites-integrate-with-vnet.md).

* Un clúster de Kafka en HDInsight. Para más información sobre cómo crear un clúster de Kafka en HDInsight, consulte el documento [Creación de un clúster de Kafka](apache-kafka-get-started.md).

    > [!IMPORTANT]
    > Durante la configuración del clúster, debe usar el paso __Configuración avanzada__ para seleccionar la red virtual de Azure y la subred que usa HDInsight. Seleccione la red virtual y la subred creadas en el paso anterior.

    Para más información sobre Kafka y las redes virtuales, consulte el documento [Conexión a Kafka mediante una red virtual](apache-kafka-connect-vpn-gateway.md).

## <a name="architecture"></a>Architecture

Kafka en HDInsight se encuentra en una red virtual de Azure. Azure Functions puede comunicarse con la red virtual mediante una puerta de enlace de punto a sitio. La siguiente imagen es un diagrama de esta topología de red:

![Arquitectura de la conexión de Azure Functions a HDInsight](./media/apache-kafka-azure-functions/kafka-azure-functions.png)

## <a name="configure-kafka"></a>Configuración de Kafka

La información de esta sección prepara el clúster de Kafka para aceptar datos de la función de Azure. Se tratan las siguientes acciones de configuración:

* Anuncio de direcciones IP
* Recopilación de direcciones IP del agente de Kafka
* Creación de un tema de Kafka

### <a name="configure-kafka-for-ip-advertising"></a>Configuración de Kafka para anunciar direcciones IP

De manera predeterminada, Zookeeper devuelve el nombre de dominio de los agentes de Kafka a los clientes. Esta configuración no funciona sin un servidor DNS, dado que el cliente (Azure Functions) no puede resolver nombres de la red virtual. Para esta configuración, use los pasos siguientes para configurar Kafka con el fin de anunciar direcciones IP en lugar de nombres de dominio:

1. En el explorador web, vaya a https://CLUSTERNAME.azurehdinsight.net. Reemplace __CLUSTERNAME__ por el nombre del clúster de Kafka en HDInsight.

    Cuando se le solicite, use el nombre de usuario y la contraseña HTTPS para el clúster. Aparece la interfaz de usuario web de Ambari para el clúster.

2. Para ver información sobre Kafka, seleccione __Kafka__ en la lista de la izquierda.

    ![Lista de servicios donde Kafka aparece resaltado](./media/apache-kafka-azure-functions/select-kafka-service.png)

3. Para ver la configuración de Kafka, seleccione __Configs__ (Configuraciones) en la parte superior central.

    ![Vínculos de configuraciones de Kafka](./media/apache-kafka-azure-functions/select-kafka-config.png)

4. Para encontrar la configuración __kafka-env__, escriba `kafka-env` en el campo __Filtrar__ que se encuentra en la esquina superior derecha.

    ![Configuración de Kafka para kafka-env](./media/apache-kafka-azure-functions/search-for-kafka-env.png)

5. Para configurar Kafka y anunciar direcciones IP, agregue el texto siguiente en la parte inferior del campo __kafka-env-template__:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Para configurar la interfaz en que escucha Kafka, escriba `listeners` en el campo __Filtrar__ que se encuentra en la esquina superior derecha.

7. Para configurar Kafka para que escuche en todas las interfaces de red, cambie el valor del campo __listeners__ (agentes de escucha) a `PLAINTEXT://0.0.0.0:9092`.

8. Use el botón __Guardar__ para guardar los cambios en la configuración. Escriba un mensaje de texto para describir los cambios. Seleccione __Aceptar__ una vez que se guarden los cambios.

    ![Botón Guardar configuración](./media/apache-kafka-azure-functions/save-button.png)

9. Para evitar errores al reiniciar Kafka, use el botón __Acciones de servicio__ y seleccione __Activar el modo de mantenimiento__. Seleccione Aceptar para completar esta operación.

    ![Acciones de servicio, en que Activar mantenimiento aparece resaltado](./media/apache-kafka-azure-functions/turn-on-maintenance-mode.png)

10. Para reiniciar Kafka, use el botón __Reiniciar__ y seleccione __Restart All Affected__ (Reiniciar todos los elementos afectados). Confirme el reinicio y use el botón __Aceptar__ una vez que se complete la operación.

    ![Botón Reiniciar con la opción Restart All Affected resaltada](./media/apache-kafka-azure-functions/restart-button.png)

11. Para deshabilitar el modo de mantenimiento, use el botón __Acciones de servicio__ y seleccione __Desactivar el modo de mantenimiento__. Seleccione **Aceptar** para completar esta operación.

### <a name="get-the-kafka-broker-ip-address"></a>Obtención de la dirección IP del agente de Kafka

Use uno de los métodos siguientes para recuperar el nombre de dominio completo (FQDN) y las direcciones IP de los nodos del clúster de Kafka:

```powershell
$resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

En este comando se supone que `<resourcegroupname>` es el nombre del grupo de recursos de Azure que contiene la red virtual.

En la lista de nombres devueltos, seleccione la dirección IP de un nodo de trabajo. El nombre de dominio completo interno del nodo comienza con las letras `wn`. La función usa esta dirección IP para comunicarse con Kafka.

### <a name="create-a-kafka-topic"></a>Creación de un tema de Kafka

Kafka almacena los datos en __temas__. Antes de enviar datos a Kafka desde una función de Azure, debe crear la función.

Para crear un tema, siga los pasos del documento [Creación de un clúster de Kafka](apache-kafka-get-started.md).

## <a name="create-a-function-that-sends-to-kafka"></a>Creación de una función que se envía a Kafka

> [!NOTE]
> En los pasos de esta sección se crea una función de JavaScript que usa el paquete [kafka-node](https://www.npmjs.com/package/kafka-node) para publicar datos en Kafka:

1. Cree una nueva función __WebHook + API__ y seleccione __JavaScript__ como lenguaje. Para más información sobre la creación de nuevas funciones, consulte el documento [Creación de su primera función](../../azure-functions/functions-create-first-azure-function.md#create-function).

2. Use el siguiente código como cuerpo de la función:

    ```javascript
    var kafka = require('kafka-node');

    // The topic and a Kafka broker host for your HDInsight cluster
    var topic = 'test';
    var brokerHost = '10.1.0.7:9092';
    // Create the client
    var client= new kafka.KafkaClient({kafkaHost: brokerHost});

    module.exports = function (context, req) {
        // Create the producer
        var producer = new kafka.Producer(client, {requireAcks: 1});

        context.log('JavaScript HTTP trigger function processed a request.');

        if (req.query.name || (req.body && req.body.name)) {
            var name = req.query.name || req.body.name
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "Hello " + name
            };
            // Create the payload, using the name as the body
            var payloads = [
                    { topic: topic, messages: [name]}
            ];
            context.log("calling producer.send");
            // Send the data to Kafka
            producer.send(payloads, function(err, data) {
                if(err) {
                    context.log(err);
                } else {
                    context.log(data);
                }
            });
        }
        else {
            context.res = {
                status: 400,
                body: "Please pass a name on the query string or in the request body"
            };
        }
        context.done();
    };
    ```

    Reemplace `'test'` por el nombre del tema de Kafka que creó en el clúster de HDInsight.

    Reemplace `10.1.0.7` por la dirección IP que recuperó anteriormente. Deje el valor `:9092`. 9092 es el puerto que escucha Kafka.

    Presione el botón __Guardar__ para guardar los cambios.

    ![Editor con el botón para guardar](./media/apache-kafka-azure-functions/function-editor.png)

3. En la parte derecha del editor de funciones, seleccione __View files__ (Ver archivos). Seleccione __+ Add__ (+Agregar) y agregue un nuevo archivo llamado `package.json`. Este archivo se usa para especificar la dependencia del paquete `kafka-node`.

    ![Agregar un archivo](./media/apache-kafka-azure-functions/add-files.png)

4. Para editar el nuevo archivo, seleccione `package.json` en la lista __View files__ (Ver archivos). Use el texto siguiente como contenido del archivo:

    ```json
    {
    "name": "kafkatest",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "author": "",
    "license": "ISC",
    "dependencies": {
            "kafka-node": "^2.4.1"
        }
    }
    ```

    Presione el botón __Guardar__ para guardar los cambios.

5. Para instalar el paquete `kafka-node`, use la sección _Node version and package management_ (Administración de la versión y el paquete del nodo) de la [guía del desarrollador de JavaScript para Azure Functions](../../azure-functions/functions-reference-node.md#node-version-and-package-management).

    > [!NOTE]
    > Puede que reciba varios errores cuando se instala el paquete kafka-node. Puede omitir estos errores con seguridad.

## <a name="run-the-function"></a>Ejecución de la función

En la parte derecha del editor de funciones, seleccione __Test__ (Probar). Deje la configuración predeterminada para la prueba y seleccione __Run__ (Ejecutar). Cuando se ejecuta la prueba, se pasa un parámetro `name` a la función. Este parámetro contiene un valor de `Azure`, que la función inserta en Kafka.

![Captura de pantalla del cuadro de diálogo de prueba](./media/apache-kafka-azure-functions/function-test-dialog.png)

Para ver la información registrada por la función mientras se ejecuta la prueba, seleccione __Logs__ (Registros) en la parte inferior de la página. Ejecute de nuevo la prueba para generar información de registro.

![Ejemplo de la salida de registro de función](./media/apache-kafka-azure-functions/function-log.png)

## <a name="verify-the-data-is-in-kafka"></a>Comprobación de que los datos están en Kafka

Para comprobar que los datos llegaron en el tema de Kafka, use la información de la sección _Produce and consume records_ (Producción y consumo de registros) del documento [Creación de un clúster de Kafka](apache-kafka-get-started.md#produce-and-consume-records). `kafka-console-consumer` lee los datos del tema y muestra una lista de los mensajes almacenados en el tema.

## <a name="next-steps"></a>pasos siguientes

Use los vínculos siguientes para aprender a usar a Apache Kafka en HDInsight:

* [Introducción a Kafka en HDInsight](apache-kafka-get-started.md)

* [Uso de MirrorMaker para crear una réplica de Kafka en HDInsight](apache-kafka-mirroring.md)

* [Uso de Apache Kafka con Storm en HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Uso de Apache Spark con Kafka en HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Conexión a Kafka a través de una instancia de Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)