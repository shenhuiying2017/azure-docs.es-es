---
title: "Configuración de la replicación de clúster de HBase en redes virtuales de Azure | Microsoft Docs"
description: "Aprenda a configurar la replicación de HBase de una versión de HDInsight a otra para conseguir equilibrio de carga, alta disponibilidad, migración y actualizaciones sin tiempo de inactividad y recuperación ante desastres."
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 87f4d219a678cab78980af2ff8b98ae55739f04c
ms.contentlocale: es-es
ms.lasthandoff: 09/09/2017

---
# <a name="set-up-hbase-cluster-replication-in-azure-virtual-networks"></a>Configuración de la replicación de clúster de HBase en redes virtuales de Azure

Aprenda a configurar la replicación de HBase en una red virtual o entre dos redes virtuales en Azure.

La replicación de clúster usa una metodología de inserción de origen. Un clúster de HBase puede ser un origen, un destino o cumplir ambos roles a la vez. La replicación es asincrónica. El objetivo de la replicación es la coherencia final. Cuando el origen recibe una modificación en una familia de columnas cuando la replicación está habilitada, la modificación se propaga a todos los clústeres de destino. Cuando se replican datos de un clúster a otro, se realiza un seguimiento del clúster de origen y de todos los clústeres que ya han consumido los datos para evitar bucles de replicación.

En este tutorial, va a configurar una replicación de origen y destino. Para otras topologías de clúster, consulte la [Guía de referencia de HBase Apache](http://hbase.apache.org/book.html#_cluster_replication).

Los siguientes son casos de uso de replicación de HBase para una única red virtual:

* Equilibrio de carga. Por ejemplo, puede ejecutar exámenes o trabajos MapReduce en el clúster de destino e ingerir datos en el clúster de origen.
* Agregar alta disponibilidad.
* Migrar datos de un clúster de HBase a otro.
* Actualizar un clúster de Azure HDInsight de una versión a otra.

Los siguientes son casos de uso de replicación de HBase para dos redes virtuales:

* Configuración de la recuperación ante desastres.
* Equilibrio de carga y creación de particiones de la aplicación.
* Agregar alta disponibilidad.

Puede replicar clústeres mediante scripts de [acción de script](hdinsight-hadoop-customize-cluster-linux.md) disponibles en [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar este tutorial, debe tener una suscripción a Azure. Consulte cómo [obtener una evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Configuración de los entornos

Existen tres opciones de configuración:

- Dos clústeres de HBase en una única red virtual de Azure.
- Dos clústeres de HBase en dos redes virtuales diferentes de la misma región.
- Dos clústeres de HBase en dos redes virtuales diferentes de dos regiones diferentes (replicación geográfica).

Para ayudar a configurar los entornos, hemos creado algunas [plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Si prefiere configurar los entornos mediante otros métodos, consulte:

- [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Create HBase clusters in Azure Virtual Network](hdinsight-hbase-provision-vnet.md) (Creación de clústeres de HBase en Azure Virtual Network)

### <a name="set-up-one-virtual-network"></a>Configuración de una red virtual

Para crear dos clústeres de HBase en la misma red virtual, seleccione la imagen siguiente. La plantilla está almacenada en [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="set-up-two-virtual-networks-in-the-same-region"></a>Configuración de dos redes virtuales de la misma región

Para crear dos redes virtuales con emparejamiento de red virtual y dos clústeres de HBase en la misma región, seleccione la imagen siguiente. La plantilla está almacenada en [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



Este escenario requiere [emparejamiento de red virtual](../virtual-network/virtual-network-peering-overview.md). La plantilla permite el emparejamiento de red virtual.   

La replicación de HBase utiliza direcciones IP de las máquinas virtuales ZooKeeper. Debe configurar direcciones IP estáticas para los nodos ZooKeeper de HBase de destino.

**Para configurar las direcciones IP estáticas**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En el menú de la izquierda, seleccione **Grupos de recursos**.
3. Seleccione el grupo de recursos que tiene el clúster de HBase de destino. Este es el grupo de recursos que especificó al usar la plantilla de Resource Manager para crear el entorno. Puede usar el filtro para restringir la lista. Puede ver una lista de recursos que contienen las dos redes virtuales.
4. Seleccione la red virtual que contiene el clúster de HBase de destino. Por ejemplo, seleccione **xxxx-vnet2**. En la lista aparecen tres dispositivos que comienzan con **nic-zookeepermode-**. Estos dispositivos son las tres máquinas virtuales ZooKeeper.
5. Seleccione en una de las máquinas virtuales ZooKeeper.
6. Seleccione **Configuraciones IP**.
7. En la lista, seleccione **ipConfig1**.
8. Seleccione **Estático** y copie o anote la dirección IP real. Necesitará la dirección IP cuando ejecute la acción de script para habilitar la replicación.

  ![Dirección IP estática de ZooKeeper de replicación de HBase para HDInsight](./media/hdinsight-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. Repita el paso 6 para establecer la dirección IP estática para los otros dos nodos ZooKeeper.

En el escenario de varias redes virtuales, debe usar el modificador **-ip** cuando llame a la acción de script `hdi_enable_replication.sh`.

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Configuración de dos redes virtuales en dos regiones distintas

Para crear dos redes virtuales en dos regiones distintas, seleccione la siguiente imagen. La plantilla se encuentra almacenada en un contenedor global de Azure Blob.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fdeploy-hbase-geo-replication.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Cree una puerta de enlace de VPN entre las dos redes virtuales. Para obtener instrucciones, consulte [Creación de una red virtual con una conexión de sitio a sitio](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

La replicación de HBase utiliza las direcciones IP de las máquinas virtuales ZooKeeper. Debe configurar direcciones IP estáticas para los nodos ZooKeeper de HBase de destino. Para configurar la dirección IP estática, consulte [Configuración de dos redes virtuales de la misma región](#set-up-two-virtual-networks-in-the-same-region) en este artículo.

En el escenario de varias redes virtuales, debe usar el modificador **-ip** cuando llame a la acción de script `hdi_enable_replication.sh`.

## <a name="load-test-data"></a>Carga de datos de prueba

Al replicar un clúster, debe especificar las tablas que quere replicar. En esta sección, va a cargar algunos datos en el clúster de origen. En la siguiente sección, habilitará la replicación entre los dos clústeres.

Para crear una tabla **Contacts** e insertar algunos datos en la tabla, siga las instrucciones que se indican en el [tutorial de HBase sobre la introducción al uso de Apache HBase con Hadoop basado en Linux en HDInsight](hdinsight-hbase-tutorial-get-started-linux.md).

## <a name="enable-replication"></a>Habilitar replicación

En los pasos siguientes se describe cómo llamar al script de acción de script desde Azure Portal. Para información sobre la ejecución de una acción de script mediante Azure PowerShell y la herramienta de línea de comandos (CLI de Azure), consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

**Para habilitar la replicación de HBase desde Azure Portal**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Abra el clúster de HBase de origen.
3. En el menú de clúster, seleccione **Acciones de script**.
4. En la parte superior de la página, seleccione **Enviar nuevo**.
5. Seleccione o escriba la siguiente información:

  1. **Nombre** especifique **Enable replication** (Habilitar replicación).
  2. **URL de script de Bash**: escriba **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  3.  **Principal**: asegúrese de que esta opción está seleccionada. Borre los demás tipos de nodo.
  4. **Parámetros**: los siguientes parámetros de ejemplo permiten la replicación en todas las tablas existentes y copian todos los datos del clúster de origen al clúster de destino:

            -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata

6. Seleccione **Crear**. El script puede tardar un poco en ejecutarse, especialmente cuando se usa el argumento **-copydata**.

Argumentos necesarios:

|Nombre|Descripción|
|----|-----------|
|-s, --src-cluster | Especifica el nombre DNS del clúster de HBase de origen. Por ejemplo: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | Especifica el nombre DNS del clúster de HBase de destino (réplica). Por ejemplo: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | Especifica la contraseña de administrador de Ambari en el clúster de HBase de origen. |
|-dp, --dst-ambari-password | Especifica la contraseña de administrador de Ambari en el clúster de HBase de destino.|

Argumentos opcionales:

|Nombre|Descripción|
|----|-----------|
|-su, --src-ambari-user | Especifica el nombre de usuario de administrador de Ambari en el clúster de HBase de origen. El valor predeterminado es **admin**. |
|-du, --dst-ambari-user | Especifica el nombre de usuario de administrador de Ambari en el clúster de HBase de destino. El valor predeterminado es **admin**. |
|-t, --table-list | Especifica las tablas que se van a replicar. Por ejemplo: --table-list="table1;table2;table3". Si no se especifica unas tablas determinadas, se replican todas las tablas de HBase existentes.|
|-m, --machine | Especifica el nodo principal en el que se ejecuta la acción de script. El valor es **hn1** o **hn0**. Dado que el nodo principal **hn0** normalmente está ocupado, se recomienda usar **hn1**. Use esta opción si se está ejecutando el script $0 como acción de script desde el portal de HDInsight o Azure PowerShell.|
|-ip | Es necesario cuando se habilita la replicación entre dos redes virtuales. Este argumento actúa como un modificador para usar las direcciones IP estáticas de los nodos ZooKeeper de los clústeres de réplica en lugar de los nombres FQDN. Antes de habilitar la replicación, es necesario configurar previamente las direcciones IP estáticas. |
|-cp, -copydata | Habilita la migración de datos existentes en las tablas en las que está habilitada la replicación. |
|-rpm, -replicate-phoenix-meta | Habilita la replicación en las tablas del sistema Phoenix. <br><br>*Esta opción se debe utilizar con precaución.* Se recomienda volver a crear tablas de Phoenix en clústeres de réplica antes de utilizar este script. |
|-h, --help | Muestra información de uso. |

La sección `print_usage()` del [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) contiene una explicación detallada de los parámetros.

Una vez implementada correctamente la acción de script, puede usar SSH para conectarse al clúster de HBase de destino y comprobar que los datos se hayan replicado.

### <a name="replication-scenarios"></a>Escenarios de replicación

En la lista siguiente se muestran algunos casos de uso general y la configuración de parámetros:

- **Habilitar la replicación en todas las tablas entre los dos clústeres**. En este escenario no es necesario copiar o migrar datos existentes de las tablas y no se usan tablas de Phoenix. Utilice los siguientes parámetros:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Habilitar la replicación en tablas específicas**. Para habilitar la replicación en table1, table2 y table3, use los parámetros siguientes:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Habilitar la replicación en tablas específicas y copiar los datos existentes**. Para habilitar la replicación en table1, table2 y table3, use los parámetros siguientes:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Habilitar la replicación en todas las tablas y replicar metadatos de Phoenix del origen al destino**. La replicación de metadatos de Phoenix no es perfecta. Úsela con precaución. Utilice los siguientes parámetros:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Copia y migración de datos

Hay dos scripts de acción de script independientes para copiar o migrar datos después de habilitar la replicación:

- [Script para tablas pequeñas](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tablas que tienen unos cuantos gigabytes de tamaño y se espera que una copia general finalice en menos de una hora)

- [Script para tablas grandes](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tablas que se espera que tarden más de una hora en copiarse)

Puede seguir el mismo procedimiento que se describe en [Habilitar replicación](#enable-replication) para llamar a la acción de script. Utilice los siguientes parámetros:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

La sección `print_usage()` del [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) contiene una descripción detallada de los parámetros.

### <a name="scenarios"></a>Escenarios

- **Copiar tablas específicas (test1, test2 y test3) con todas las filas editadas hasta ahora (marca de tiempo actual)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  O:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Copiar tablas específicas con un intervalo de tiempo especificado**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Deshabilitar replicación

Para deshabilitar la replicación, use otro script de acción de script de [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Puede seguir el mismo procedimiento que se describe en [Habilitar replicación](#enable-replication) para llamar a la acción de script. Utilice los siguientes parámetros:

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

La sección `print_usage()` del [script](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) contiene una explicación detallada de los parámetros.

### <a name="scenarios"></a>Escenarios

- **Deshabilitar la replicación en todas las tablas**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  o

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Deshabilitar la replicación en las tablas especificadas (table1, table2 y table3)**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a configurar la replicación de HBase en una red virtual, o entre dos redes virtuales. Para más información sobre HDInsight y HBase, consulte estos artículos:

* [Get started with Apache HBase in HDInsight][hdinsight-hbase-get-started] (Introducción a HBase Apache en HDInsight)
* [HDInsight HBase overview][hdinsight-hbase-overview] (Información general de HBase de HDInsight)
* [Create HBase clusters in Azure Virtual Network][hdinsight-hbase-provision-vnet] (Creación de clústeres de HBase en Azure Virtual Network)
* [Analyze real-time Twitter sentiment with HBase][hdinsight-hbase-twitter-sentiment] (Análisis de opinión en Twitter en tiempo real con HBase)
* [Análisis de datos de sensores con Storm y HBase en HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started-linux.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

