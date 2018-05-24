---
title: Uso de Kafka MirrorMaker con Azure Event Hubs para ecosistemas de Kafka | Microsoft Docs
description: Uso de Kafka MirrorMaker para crear el reflejo de un clúster de Kafka en Event Hubs.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: mirror-maker
ms.custom: mvc
ms.date: 05/07/2018
ms.author: bahariri
ms.openlocfilehash: 819071321d5609728e7c62abb5b25bf354107850
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="using-kafka-mirrormaker-with-event-hubs-for-kafka-ecosystems"></a>Uso de Kafka MirrorMaker con Event Hubs para ecosistemas de Kafka

> [!NOTE]
> Este ejemplo está disponible en [GitHub](https://github.com/Azure/azure-event-hubs).

Un aspecto importante para las aplicaciones de escala de nube modernas es la capacidad de actualizar, mejorar y cambiar la infraestructura sin interrumpir el servicio. En este tutorial se muestra cómo una instancia de Event Hubs habilitada para Kafka y Kafka MirrorMaker pueden integrarse en una canalización de Kafka existente en Azure al "crear un reflejo" del streaming de entrada de Kafka en el servicio Event Hubs. 

Un punto de conexión de Kafka en Azure Event Hubs le permite conectarse a Azure Event Hubs mediante el protocolo Kafka (es decir, clientes de Kafka). Al realizar cambios mínimos en una aplicación de Kafka, puede conectarse a Azure Event Hubs y disfrutar de las ventajas del ecosistema de Azure. Las instancias de Event Hubs habilitadas para Kafka actualmente son compatibles con Kafka versión 1.0 y posteriores.

En este ejemplo, se muestra cómo reflejar un agente de Kafka en una instancia de Event Hubs habilitada para Kafka mediante Kafka MirrorMaker.

   ![Kafka MirrorMaker con Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, asegúrese de disponer de los siguientes elementos:

* Una suscripción de Azure. Si no tiene una, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.
* [Kit de desarrollo de Java (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * En Ubuntu, ejecute `apt-get install default-jdk` para instalar el JDK.
    * Asegúrese de establecer la variable de entorno JAVA_HOME para que apunte a la carpeta donde está instalado el JDK.
* [Descargue](http://maven.apache.org/download.cgi) e [instale](http://maven.apache.org/install.html) un archivo binario de Maven
    * En Ubuntu, puede ejecutar `apt-get install maven` para instalar Maven.
* [Git](https://www.git-scm.com/downloads)
    * En Ubuntu, puede ejecutar `sudo apt-get install git` para instalar Git.

## <a name="create-an-event-hubs-namespace"></a>Creación de un espacio de nombres de Event Hubs

Se requiere un espacio de nombres de Event Hubs para enviar y recibir de cualquier servicio de Event Hubs. Consulte [Creación de un espacio de nombres de Event Hubs y un centro de eventos con Azure Portal](event-hubs-create.md) para obtener instrucciones sobre cómo obtener un punto de conexión de Kafka para Event Hubs. Asegúrese de copiar la cadena de conexión de Event Hubs para su uso posterior.

## <a name="clone-the-example-project"></a>Clonación del proyecto de ejemplo

Ahora que tiene una cadena de conexión de Event Hubs habilitada para Kafka, clone el repositorio de Azure Event Hubs y navegue hasta la subcarpeta `mirror-maker`:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Configuración de un clúster de Kafka

Utilice la [guía de inicio rápido de Kafka](https://kafka.apache.org/quickstart) para configurar un clúster con la configuración deseada (o use un clúster de Kafka existente).

## <a name="kafka-mirrormaker"></a>Kafka MirrorMaker

Kafka MirrorMaker permite la "creación de reflejos" de un flujo de datos. Dados clústeres de Kafka de origen y destino, MirrorMaker garantiza que los mensajes enviados al clúster de origen se reciban tanto en el clúster de origen como en el clúster de destino. En este ejemplo se muestra cómo crear el reflejo de un clúster de Kafka de origen con una instancia de Event Hubs habilitada para Kafka de destino. Este escenario puede utilizarse para enviar datos de una canalización de Kafka existente a Event Hubs sin interrumpir el flujo de datos. 

Para obtener información más detallada sobre Kafka MirrorMaker, consulte la [guía de creación de reflejo de Kafka para MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

### <a name="configuration"></a>Configuración

Para configurar Kafka MirrorMaker, asígnele un clúster de Kafka como su consumidor u origen, y una instancia de Event Hubs habilitada para Kafka como su productor o destino.

#### <a name="consumer-configuration"></a>Configuración del consumidor

Actualice el archivo de configuración del consumidor `source-kafka.config`, que indica a MirrorMaker las propiedades del clúster de origen de Kafka.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Configuración del productor

Ahora, actualice el archivo de configuración del productor `mirror-eventhub.config`, que indica a MirrorMaker que envíe los datos duplicados (o "reflejados") al servicio de Event Hubs. En concreto, cambie `bootstrap.servers` y `sasl.jaas.config` para que apunten a su punto de conexión de Kafka para Event Hubs. El servicio de Event Hubs requiere una comunicación segura (SASL), que se consigue estableciendo las tres últimas propiedades en la configuración siguiente: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-mirrormaker"></a>Ejecución de MirrorMaker

Ejecute el script de Kafka MirrorMaker desde el directorio raíz de Kafka con los archivos de configuración recientemente actualizados. Asegúrese de copiar los archivos de configuración al directorio raíz de Kafka, o de actualizar sus rutas de acceso en el siguiente comando.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Para comprobar que los eventos llegan a la instancia de Event Hubs habilitada para Kafka, consulte las estadísticas de entrada en [Azure Portal](https://azure.microsoft.com/features/azure-portal/) o ejecute un consumidor en el centro de eventos.

Con MirrorMaker en ejecución, los eventos que se envían al clúster de Kafka de origen son recibidos por el clúster de Kafka y el servicio de Event Hubs habilitado para Kafka reflejado. Al usar MirrorMaker y un punto de conexión de Kafka para Event Hubs, puede migrar una canalización de Kafka existente al servicio de Azure Event Hubs administrado sin cambiar el clúster existente ni interrumpir ningún flujo de datos en curso.

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre Events Hubs](event-hubs-what-is-event-hubs.md)
* [Información sobre Event Hubs para el ecosistema de Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Obtenga más información sobre [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para transmitir eventos desde Kafka local a instancias de Event Hubs habilitadas para Kafka en la nube.
