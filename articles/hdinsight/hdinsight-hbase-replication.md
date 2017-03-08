---
title: "Configuración de la replicación de HBase | Microsoft Docs"
description: "Aprenda a configurar la replicación de HBase para equilibrio de carga, alta disponibilidad, migración o actualización sin tiempo de inactividad de una versión de HDInsight a otra y recuperación ante desastres."
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: a62cd616f0abd59c83c834bf13b4ba8549a9c73e
ms.openlocfilehash: 83aabe0e5161cd3f94caa996dec29bc020e5308b
ms.lasthandoff: 02/23/2017


---
# <a name="configure-hbase-replication"></a>Configuración de la replicación de HBase

Aprenda a configurar la replicación de HBase en una red virtual (VNet) o entre dos redes virtuales.

La replicación de clúster usa una metodología de inserción de origen. Un clúster de HBase puede ser un origen, un destino o cumplir ambos roles a la vez. La replicación es asincrónica y el objetivo de la replicación es la coherencia eventual. Cuando el origen recibe una edición en una familia de columna con la replicación habilitada, esa edición se propaga a todos los clústeres de destino. Cuando se replican datos de un clúster a otro, se realiza un seguimiento del clúster de origen y de todos los clústeres que ya han consumido los datos para evitar bucles de replicación.

En este tutorial, configurará una replicación de origen y de destino. Para otras topologías de clúster, consulte [Guía de referencia de HBase Apache](http://hbase.apache.org/book.html#_cluster_replication).

Casos de uso de replicación de HBase para una única red virtual:

* Equilibrio de carga, por ejemplo: ejecutar exámenes o trabajos MapReduce en el clúster de destino e ingerir datos en el clúster de origen
* Alta disponibilidad
* Migración de datos de un clúster de HBase a otro
* Actualización de un clúster de Azure HDInsight de una versión a otra

Casos de uso de replicación de HBase para dos redes virtuales:

* Recuperación ante desastres
* Equilibrio de carga y creación de particiones de la aplicación
* Alta disponibilidad

Puede replicar clústeres mediante scripts de [acción de script](hdinsight-hadoop-customize-cluster-linux.md) que se encuentran en [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar este tutorial, debe tener una suscripción a Azure. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="configure-the-environments"></a>Configuración de los entornos

Hay tres opciones de configuración posibles:

- Dos clústeres de HBase en una única red virtual de Azure
- Dos clústeres de HBase en dos redes virtuales diferentes de la misma región
- Dos clústeres de HBase en dos redes virtuales diferentes de dos regiones distintas (replicación geográfica)

Para que sea más fácil configurar los entornos, hemos creado algunas [plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Si prefiere configurar los entornos mediante otros métodos, consulte:

- [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Create HBase clusters in Azure Virtual Network](hdinsight-hbase-provision-vnet.md) (Creación de clústeres de HBase en Azure Virtual Network)

### <a name="configure-one-virtual-network"></a>Configuración de una única red virtual

Haga clic en la imagen siguiente para crear dos clústeres de HBase en la misma red virtual. La plantilla se encuentra en [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="configure-two-virtual-networks-in-the-same-region"></a>Configuración de dos redes virtuales en la misma región

Haga clic en la imagen siguiente para crear dos redes virtuales con emparejamiento de VNet y dos clústeres de HBase en la misma región. La plantilla se encuentra en [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



Este escenario requiere [emparejamiento de VNet](../virtual-network/virtual-network-peering-overview.md). La plantilla permite el emparejamiento de VNet.   

La replicación de HBase utiliza direcciones IP de las máquinas virtuales ZooKeeper. Debe configurar las direcciones IP estáticas para los nodos ZooKeeper de HBase de destino.

**Para configurar las direcciones IP estáticas**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En el menú de la izquierda, haga clic en **Resource groups** (Grupos de recursos).
3. Haga clic en el grupo de recursos que contiene el clúster de HBase de destino. Este es el grupo de recursos que especificó al usar la plantilla de Resource Manager para crear el entorno. Puede utilizar el filtro para restringir la lista. Puede ver una lista de recursos que incluye las dos redes virtuales.
4. Haga clic en la red virtual que contiene el clúster de HBase de destino. Por ejemplo, haga clic en **xxxx-vnet2**. Puede ver tres dispositivos con nombres que empiezan por **nic-zookeepermode-**. Estos dispositivos son las tres máquinas virtuales ZooKeeper.
5. Haga clic en una de las máquinas virtuales ZooKeeper.
6. Haga clic en **IP configurations** (Configuraciones IP).
7. En la lista, haga clic en **ipConfig1**.
8. Haga clic en **Static** (Estático) y anote la dirección IP real. Necesitará la dirección IP al ejecutar la acción de script para habilitar la replicación.

  ![Dirección IP estática ZooKeeper de replicación de HBase para HDInsight](./media/hdinsight-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. Repita el paso 6 para establecer la dirección IP estática para los otros dos nodos ZooKeeper.

En el escenario entre redes virtuales, debe usar el modificador **-ip** al llamar a la acción de script **hdi_enable_replication.sh**.

### <a name="configure-two-virtual-networks-in-two-different-regions"></a>Configuración de dos redes virtuales en dos regiones distintas

Haga clic en la imagen siguiente para crear dos redes virtuales en dos regiones distintas. La plantilla se encuentra almacenada en un contenedor de blobs de Azure público.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fdeploy-hbase-geo-replication.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Cree una puerta de enlace de VPN entre las dos redes virtuales. Para obtener instrucciones, consulte [Create a VNet with a site-to-site connection](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) (Creación de una red virtual con una conexión de sitio a sitio).

La replicación de HBase utiliza direcciones IP de las máquinas virtuales ZooKeeper. Debe configurar las direcciones IP estáticas para los nodos ZooKeeper de HBase de destino. Para configurar la dirección IP estática, consulte la sección "Configuración de dos redes virtuales en la misma región" en este artículo.

En el escenario entre redes virtuales, debe usar el modificador **-ip** al llamar a la acción de script **hdi_enable_replication.sh**.

## <a name="load-test-data"></a>Carga de datos de prueba

Cuando replica un clúster, debe especificar las tablas que se van a replicar. En esta sección, cargará algunos datos en el clúster de origen. En la siguiente sección, habilitará la replicación entre los dos clústeres.

Siga las instrucciones de [HBase tutorial: Get started using Apache HBase with Linux-based Hadoop in HDInsight](hdinsight-hbase-tutorial-get-started-linux.md) (Tutorial de HBase: Introducción al uso de Apache HBase con Hadoop basado en Linux en HDInsight) para crear una tabla **Contacts** e insertar algunos datos en la tabla.

## <a name="enable-replication"></a>Habilitar replicación

Los pasos siguientes muestran cómo llamar al script de acción de script desde Azure Portal. Para ejecutar una acción de script mediante Azure PowerShell y la interfaz de línea de comandos (CLI) de Azure, consulte [Customize Linux-based HDInsight clusters using script action](hdinsight-hadoop-customize-cluster-linux.md) (Personalización de clústeres de HDInsight basado en Linux mediante acciones de script).

**Para habilitar la replicación de HBase desde Azure Portal**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Abra el clúster de HBase de origen.
3. En el menú del clúster, haga clic en **Script Actions** (Acciones de script).
4. Haga clic en **Submit New** (Enviar nueva) en la parte superior de la hoja.
5. Seleccione o escriba la siguiente información:

  - **Nombre** especifique **Enable replication** (Habilitar replicación).
  - **URL de script de Bash**: escriba **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  - **Principal**: seleccionado. Borre los demás tipos de nodo.
  - **Parámetros**: los siguientes parámetros de ejemplo habilitan la replicación de todas las tablas existentes y copian todos los datos del clúster de origen en el clúster de destino:

            -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata

6. Haga clic en **Crear**. El script puede tardar algún tiempo, especialmente cuando se usa el argumento -copydata.

Argumentos necesarios:

|Nombre|Descripción|
|----|-----------|
|-s, --src-cluster | Especifica el nombre DNS del clúster de HBase de origen. Por ejemplo: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | Especifica el nombre DNS del clúster de HBase de destino (réplica). Por ejemplo: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | Especifica la contraseña de administrador para Ambari en el clúster de HBase de origen. |
|-dp, --dst-ambari-password | Especifica la contraseña de administrador para Ambari en el clúster de HBase de destino.|

Argumentos opcionales:

|Nombre|Descripción|
|----|-----------|
|-su, --src-ambari-user | Especifica el nombre de usuario de administrador para Ambari en el clúster de HBase de origen. El valor predeterminado es **admin**. |
|-du, --dst-ambari-user | Especifica el nombre de usuario de administrador para Ambari en el clúster de HBase de destino. El valor predeterminado es **admin**. |
|-t, --table-list | Especifica las tablas que se van a replicar. Por ejemplo: --table-list="table1;table2;table3". Si no se especifica unas tablas determinadas, se replican todas las tablas de HBase existentes.|
|-m, --machine | Especifica el nodo principal en el que se ejecutará la acción de script. El valor es hn1 o hn0. Dado que hn0 suele estar más ocupado, se recomienda utilizar hn1. Esta opción se utiliza si se está ejecutando el script $0 como acción de script desde el portal de HDInsight o Azure PowerShell.|
|-ip | Este argumento es necesario cuando se habilita la replicación entre dos redes virtuales. Este argumento actúa como un conmutador para utilizar las direcciones IP estáticas de los nodos ZooKeeper de los clústeres de réplica en lugar de los nombres FQDN. Las direcciones IP estáticas deben configurarse antes de habilitar la replicación. |
|-cp, -copydata | Habilita la migración de datos existentes en las tablas en las que está habilitada la replicación. |
|-rpm, -replicate-phoenix-meta | Habilita la replicación en las tablas del sistema Phoenix. <br><br>*Esta opción se debe utilizar con precaución.* Se recomienda volver a crear tablas de Phoenix en clústeres de réplica antes de utilizar este script. |
|-h, --help | Muestra información de uso. |

La sección print_usage() del [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) proporciona una explicación detallada de los parámetros.

Una vez implementada la acción de script correctamente, puede utilizar SSH para conectarse al clúster de HBase de destino y comprobar que los datos se hayan replicado.

### <a name="replication-scenarios"></a>Escenarios de replicación

En la lista siguiente se muestran algunos casos de uso general y la configuración de parámetros:

- **Habilitar la replicación en todas las tablas entre los dos clústeres**. Este escenario no requiere la copia o migración de datos existentes en las tablas y no utiliza tablas de Phoenix. Utilice los siguientes parámetros:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Habilitar la replicación en tablas específicas**. Use los parámetros siguientes para habilitar la replicación en table1, table2 y table3:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Habilitar la replicación en tablas específicas y copiar los datos existentes**. Use los parámetros siguientes para habilitar la replicación en table1, table2 y table3:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Habilitar la replicación en todas las tablas con replicación de metadatos de Phoenix de origen a destino**. La replicación de metadatos de Phoenix no es perfecta, así que debe habilitarse con precaución.

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Copia y migración de datos

Hay dos scripts de acción de script independientes para copiar o migrar datos después de habilitar la replicación:

- [Script para tablas pequeñas](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (unos pocos gigabytes de tamaño y una copia general deberían finalizar en menos de una hora)

- [Script para tablas grandes](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (se espera que tarden más de una hora en copiarse)

Puede seguir el mismo procedimiento que aparece en [Habilitar replicación](#enable-replication) para llamar a la acción de script con los parámetros siguientes:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

La sección print_usage() del [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) proporciona una descripción detallada de los parámetros.

### <a name="scenarios"></a>Escenarios

- **Copiar tablas específicas (test1, test2 y test3) para todas las filas editadas hasta ahora (marca de tiempo actual)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  o

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Copiar tablas específicas con el intervalo de tiempo especificado**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Deshabilitar replicación

Para deshabilitar la replicación, utilice otro script de acción de script que se encuentra en [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Puede seguir el mismo procedimiento que aparece en [Habilitar replicación](#enable-replication) para llamar a la acción de script con los parámetros siguientes:

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari Password> <-all|-t "table1;table2;...">  

La sección print_usage() del [script](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) proporciona una explicación detallada de los parámetros.

### <a name="scenarios"></a>Escenarios

- **Deshabilitar la replicación en todas las tablas**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  o

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari username> --src-ambari-password=<source cluster Ambari password>

- **Deshabilitar la replicación en las tablas especificadas (table1, table2 y table3)**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo configurar la replicación de HBase entre dos centros de datos. Para obtener más información acerca de HDInsight y HBase, consulte:

* [Get started with Apache HBase in HDInsight][hdinsight-hbase-get-started] (Introducción a HBase Apache en HDInsight)
* [HDInsight HBase overview][hdinsight-hbase-overview] (Información general de HBase de HDInsight)
* [Create HBase clusters in Azure Virtual Network][hdinsight-hbase-provision-vnet] (Creación de clústeres de HBase en Azure Virtual Network)
* [Analyze real-time Twitter sentiment with HBase][hdinsight-hbase-twitter-sentiment] (Análisis de opinión en Twitter en tiempo real con HBase)
* [Analyzing sensor data with Storm and HBase in HDInsight (Hadoop)][hdinsight-sensor-data] [Análisis de los datos de sensor con Storm y HBase en HDInsight (Hadoop)]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started-linux.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

