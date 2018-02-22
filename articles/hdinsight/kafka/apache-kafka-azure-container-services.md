---
title: Uso de Azure Container Service con Kafka en HDInsight | Microsoft Docs
description: "Aprenda a usar Kafka en HDInsight desde imágenes de contenedor hospedadas en Azure Container Service (AKS)."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2018
ms.author: larryfr
ms.openlocfilehash: 53342e11476a307bb6af356eb40fe51928041822
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="use-azure-container-services-with-kafka-on-hdinsight"></a>Uso de Azure Container Service con Kafka en HDInsight

Aprenda a usar Azure Container Service (AKS) con Kafka en un clúster de HDInsight. Los pasos descritos en este documento usan una aplicación de Node.js hospedada en AKS para comprobar la conectividad con Kafka. Esta aplicación usa el paquete [kafka-node](https://www.npmjs.com/package/kafka-node) para comunicarse con Kafka. Usa [Socket.io](https://socket.io/) para la mensajería basada en eventos entre el cliente del explorador y el servidor back-end hospedado en AKS.

[Apache Kafka](https://kafka.apache.org) es una plataforma de streaming distribuido de código abierto que se puede utilizar para compilar canalizaciones de datos de streaming en tiempo real y aplicaciones. Azure Container Service administra el entorno de Kubernetes hospedado y permite que sea rápido y fácil implementar en él aplicaciones en contenedor. Mediante una red virtual de Azure, puede conectar los dos servicios.

> [!NOTE]
> Este documento se centra en los pasos necesarios para permitir que Azure Container Service se comunique con Kafka en HDInsight. El ejemplo en sí es simplemente un cliente de Kafka básico para demostrar que la configuración funciona.

## <a name="prerequisites"></a>requisitos previos

* [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Una suscripción de Azure

En este documento se supone que está familiarizado con la creación y el uso de los siguientes servicios de Azure:

* Kafka en HDInsight
* Azure Container Service
* Instancias de Azure Virtual Network

También se supone que ha examinado el [tutorial de Azure Container Service](../../aks/tutorial-kubernetes-prepare-app.md). En este tutorial se crea un servicio de contenedor, un clúster de Kubernetes y un registro de contenedor, y se configura la utilidad `kubectl`.

## <a name="architecture"></a>Architecture

### <a name="network-topology"></a>Topología de red

Tanto HDInsight como AKS usan una red virtual de Azure como contenedor para los recursos de proceso. Para permitir la comunicación entre HDInsight y AKS, debe habilitar la comunicación entre sus redes. En los pasos descritos en este documento se usa emparejamiento de red virtual a las redes. También deberían funcionar otras conexiones, por ejemplo, VPN. Para más información sobre el emparejamiento, consulte el documento [Emparejamiento de redes virtuales](../../virtual-network/virtual-network-peering-overview.md).


El diagrama siguiente ilustra la topología de red usada en este documento:

![HDInsight en una red virtual, AKS en otra y las redes conectadas mediante emparejamiento](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]
> No está habilitada la resolución de nombres entre las redes emparejadas, por lo que se usa el direccionamiento IP. De forma predeterminada, Kafka en HDInsight está configurado para devolver nombres de host en lugar de direcciones IP cuando se conectan los clientes. Los pasos descritos en este documento modifican Kafka para usar en su lugar el anuncio de direcciones IP.

## <a name="create-an-azure-container-service-aks"></a>Creación de una instancia de Azure Container Service (AKS)

Si aún no dispone de un clúster AKS, use uno de los siguientes documentos para saber cómo crear uno:

* [Implementación de un clúster de Azure Container Service (AKS) - Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Implementación de un clúster de Azure Container Service (AKS) - CLI](../../aks/kubernetes-walkthrough.md)

> [!NOTE]
> AKS crea una red virtual durante la instalación. Esta red se empareja con la creada para HDInsight en la sección siguiente.

## <a name="configure-virtual-network-peering"></a>Configuración del emparejamiento de red virtual

1. En [Azure Portal](https://portal.azure.com), seleccione __Grupos de recursos__ y luego busque el grupo de recursos que contiene la red virtual del clúster de AKS. El nombre del grupo de recursos es `MC_<resourcegroup>_<akscluster>_<location>`. Las entradas `resourcegroup` y `akscluster` son el nombre del grupo de recursos en el que creó el clúster y el nombre del clúster. `location` es la ubicación en la que se ha creado el clúster.

2. En el grupo de recursos, seleccione el recurso __Red virtual__.

3. Seleccione __Espacio de direcciones__. Observe el espacio de direcciones que se muestra.

4. Para crear una red virtual para HDInsight, seleccione __+ Crear un recurso__, __Redes__ y, luego, __Red virtual__.

    > [!IMPORTANT]
    > Al especificar los valores de la nueva red virtual, debe usar un espacio de direcciones que no se superponga con el usado por la red del clúster de AKS.

    Use la misma __ubicación__ para la red virtual que la que usó para el clúster de AKS.

    Espere a que se cree la red virtual antes de pasar al siguiente paso.

5. Para configurar el emparejamiento entre la red de HDInsight y la red del clúster de AKS, seleccione la red virtual y, a continuación, seleccione __Emparejamientos__. Seleccione __+ Agregar__ y use los siguientes valores para rellenar el formulario:

    * __Nombre__: escriba un nombre único para esta configuración de emparejamiento.
    * __Red virtual__: use este campo para seleccionar la red virtual para el **clúster de AKS**.

    Deje todos los demás campos en el valor predeterminado y, luego, seleccione __Aceptar__ para configurar el emparejamiento.

6. Para configurar el emparejamiento entre la red del clúster de AKS y la red de HDInsight, seleccione la __red virtual del clúster de AKS__ y, luego, seleccione __Emparejamientos__. Seleccione __+ Agregar__ y use los siguientes valores para rellenar el formulario:

    * __Nombre__: escriba un nombre único para esta configuración de emparejamiento.
    * __Red virtual__: use este campo para seleccionar la red virtual para el __clúster de HDInsight__.

    Deje todos los demás campos en el valor predeterminado y, luego, seleccione __Aceptar__ para configurar el emparejamiento.

## <a name="install-kafka-on-hdinsight"></a>Instalación de Kafka en HDInsight

Al crear el clúster de Kafka en HDInsight, debe unir la red virtual que creó anteriormente para HDInsight. Para más información sobre cómo crear un clúster de Kafka, consulte el documento [Creación de un clúster de Kafka](apache-kafka-get-started.md).

> [!IMPORTANT]
> Al crear el clúster, debe usar la opción __Configuración avanzada__ para unirse a la red virtual que creó para HDInsight.

## <a name="configure-kafka-ip-advertising"></a>Configuración del anuncio de direcciones IP de Kafka

Use los pasos siguientes para configurar Kafka para anunciar direcciones IP en lugar de nombres de dominio:

1. En el explorador web, vaya a https://CLUSTERNAME.azurehdinsight.net. Reemplace __CLUSTERNAME__ por el nombre del clúster de Kafka en HDInsight.

    Cuando se le solicite, use el nombre de usuario y la contraseña HTTPS para el clúster. Aparece la interfaz de usuario web de Ambari para el clúster.

2. Para ver información sobre Kafka, seleccione __Kafka__ en la lista de la izquierda.

    ![Lista de servicios donde Kafka aparece resaltado](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Para ver la configuración de Kafka, seleccione __Configs__ (Configuraciones) en la parte superior central.

    ![Vínculos de configuraciones de Kafka](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. Para encontrar la configuración __kafka-env__, escriba `kafka-env` en el campo __Filtrar__ que se encuentra en la esquina superior derecha.

    ![Configuración de Kafka para kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

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

    ![Botón Guardar configuración](./media/apache-kafka-azure-container-services/save-button.png)

9. Para evitar errores al reiniciar Kafka, use el botón __Acciones de servicio__ y seleccione __Activar el modo de mantenimiento__. Seleccione Aceptar para completar esta operación.

    ![Acciones de servicio, en que Activar mantenimiento aparece resaltado](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Para reiniciar Kafka, use el botón __Reiniciar__ y seleccione __Restart All Affected__ (Reiniciar todos los elementos afectados). Confirme el reinicio y use el botón __Aceptar__ una vez que se complete la operación.

    ![Botón Reiniciar con la opción Restart All Affected resaltada](./media/apache-kafka-azure-container-services/restart-button.png)

11. Para deshabilitar el modo de mantenimiento, use el botón __Acciones de servicio__ y seleccione __Desactivar el modo de mantenimiento__. Seleccione **Aceptar** para completar esta operación.

## <a name="test-the-configuration"></a>Pruebe la configuración.

En este momento, Kafka y Azure Container Service se están comunicando a través de las redes virtuales emparejadas. Para probar esta conexión, siga estos pasos:

1. Cree un tema de Kafka que lo use la aplicación de prueba. Para más información sobre cómo crear temas de Kafka, consulte el documento [Creación de un clúster de Kafka](apache-kafka-get-started.md).

2. Descargue la aplicación de ejemplo de [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test). 

3. Edite el archivo `index.js` y cambie las líneas siguientes:

    * `var topic = 'mytopic'`: reemplace `mytopic` por el nombre del tema de Kafka usado por esta aplicación.
    * `var brokerHost = '176.16.0.13:9092`: reemplace `176.16.0.13` por la dirección IP interna de uno de los hosts agentes del clúster.

        Para encontrar la dirección IP interna de los host agentes (nodos de trabajo) en el clúster, consulte el documento sobre la [API REST de Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes). Seleccione la dirección IP de una de las entradas donde el nombre de dominio comience con `wn`.

4. Desde una línea de comandos del directorio `src`, instale dependencias y use Docker para crear una imagen para la implementación:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]
    > Los paquetes que necesita esta aplicación se insertan en el repositorio, así que no tiene que usar la utilidad `npm` para instalarlos.

5. Inicie sesión en su instancia de Azure Container Registry (ACR) y busque el nombre de loginServer:

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]
    > Si no conoce el nombre de su instancia de Azure Container Registry, o no sabe cómo usar la CLI de Azure para que funcione con Azure Container Service, consulte los [tutoriales de AKS](../../aks/tutorial-kubernetes-prepare-app.md).

6. Etiquete la imagen `kafka-aks-test` local con el nombre de loginServer de su ACR. Agregue `:v1` al final para indicar la versión de la imagen:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Inserte la imagen en el registro:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```
    La operación tarda varios minutos en completarse.

8. Edite el archivo de manifiesto de Kubernetes (`kafka-aks-test.yaml`) y reemplace `microsoft` por el nombre de loginServer de ACR recuperado en el paso 4.

9. Use el comando siguiente para implementar la configuración de la aplicación del manifiesto:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Use el siguiente comando para inspeccionar el valor de `EXTERNAL-IP` de la aplicación:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Cuando haya asignado una dirección IP externa, use __CTRL + C__ para salir de la inspección.

11. Abra un explorador web y escriba la dirección IP externa del servicio. Llegará a una página similar a la siguiente imagen:

    ![Imagen de la página web](./media/apache-kafka-azure-container-services/test-web-page.png)

12. Escriba texto en el campo y, a continuación, seleccione el botón __Enviar__. Los datos se envían a Kafka. A continuación, el consumidor de Kafka en la aplicación lee el mensaje y lo agrega a la sección __Messages from Kafka__ (Mensajes de Kafka).

    > [!WARNING]
    > Puede que reciba varias copias de un mensaje. Normalmente, este problema se produce al actualizar el explorador después de conectarse o al abrir varias conexiones de explorador a la aplicación.

## <a name="next-steps"></a>pasos siguientes

Use los vínculos siguientes para aprender a usar a Apache Kafka en HDInsight:

* [Introducción a Kafka en HDInsight](apache-kafka-get-started.md)

* [Uso de MirrorMaker para crear una réplica de Kafka en HDInsight](apache-kafka-mirroring.md)

* [Uso de Apache Kafka con Storm en HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Uso de Apache Spark con Kafka en HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Conexión a Kafka a través de una instancia de Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)