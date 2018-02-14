---
title: "Migración de un clúster de HBase a una versión nueva: Azure HDInsight | Microsoft Docs"
description: "Migración de clústeres de HBase a una versión nueva."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 15d23d0ccf816ca355103ad7fd0d6124f1c5c226
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="migrate-an-hbase-cluster-to-a-new-version"></a>Migración de un clúster de HBase a una versión nueva

Actualizar los clústeres basados en trabajos, como Spark y Hadoop, es sencillo. Consulte [Actualización del clúster de HDInsight a una versión más reciente](../hdinsight-upgrade-cluster.md):

1. Cree copias de seguridad de los datos transitorios (almacenados localmente).
2. Elimine el clúster existente.
3. Cree un clúster en la misma subred de VNET.
4. Importe los datos transitorios.
5. Inicie los trabajos y continúe el procesamiento en el clúster nuevo.

Si desea actualizar un clúster de HBase, necesita algunos pasos adicionales que se describen este artículo.

> [!NOTE]
> El tiempo de inactividad durante la actualización debe ser mínimo, no más de unos minutos. Este tiempo de inactividad se debe a los pasos que se necesitan para variar todos los datos en memoria y luego el tiempo para configurar y reiniciar los servicios en el clúster nuevo. Los resultados variarán en función del número de nodos, la cantidad de datos y otras variables.

## <a name="review-hbase-compatibility"></a>Revisión de la compatibilidad de HBase

Antes de actualizar HBase, asegúrese de que las versiones de HBase en los clústeres de origen y de destino sean compatibles. Para más información, consulte cuáles son [los componentes y las versiones de Hadoop disponibles en HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]
> Es muy recomendable revisar la matriz de compatibilidad de versiones en el [libro de HBase](https://hbase.apache.org/book.html#upgrading).

A continuación, aparece una matriz de compatibilidad de versiones de ejemplo, donde Y indica la compatibilidad y N indica una posible incompatibilidad:

| Tipo de compatibilidad | Versión principal| Versión secundaria | Revisión |
| --- | --- | --- | --- |
| Compatibilidad de conexión cliente-servidor | N | Y | Y |
| Compatibilidad servidor-servidor | N | Y | Y |
| Compatibilidad de formato de archivo | N | Y | Y |
| Compatibilidad de API de cliente | N | Y | Y |
| Compatibilidad binaria de cliente | N | N | Y |
| **Compatibilidad de API limitada del servidor** |  |  |  |
| Stable | N | Y | Y |
| En evolución | N | N | Y |
| Inestable | N | N | N |
| Compatibilidad de dependencia | N | Y | Y |
| Compatibilidad de las operaciones | N | N | Y |

> [!NOTE]
> Todas las incompatibilidades importantes se deben describir en las notas de la versión de HBase.

## <a name="upgrade-with-same-hbase-major-version"></a>Actualización con la misma versión principal de HBase

El siguiente escenario es para actualizar de HDInsight 3.4 a 3.6 (ambas versiones con Apache HBase 1.1.2) con la misma versión principal de HBase. Las actualizaciones de otras versiones son similares, siempre que no existan problemas de compatibilidad entre la versión de origen y la de destino.

1. Asegúrese de que la aplicación sea compatible con la versión nueva, como se muestra en las notas de la versión y la matriz de compatibilidad de HBase. Pruebe la aplicación en un clúster que ejecute la versión de destino de HDInsight y HBase.

2. [Configure un clúster de HDInsight de destino nuevo](../hdinsight-hadoop-provision-linux-clusters.md) con la misma cuenta de almacenamiento, pero con un nombre de contenedor distinto:

    ![Use la misma cuenta de almacenamiento, pero cree un contenedor distinto](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Vacíe el clúster de HBase de origen. Este es el clúster desde el que se realiza la actualización. HBase escribe los datos entrantes a un almacén en memoria denominado _memstore_. Una vez que el almacén memstore alcanza cierto tamaño, se vacía al disco para un almacenamiento a largo plazo en la cuenta de almacenamiento del clúster. Al eliminar el clúster anterior, los almacenes memstore se reciclan, con lo que es posible que se pierdan datos. Para vaciar manualmente el almacén memstore de cada tabla al disco, ejecute el script siguiente. La versión más reciente de este script está en [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh) de Azure.

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1 or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```
    
4. Detenga la ingesta al clúster de HBase anterior.
5. Para asegurarse de que se vacían todos los datos recientes del almacén memstore, vuelva a ejecutar el script anterior.
6. Inicie sesión en Ambari en el clúster anterior (https://NombreClústerAnterior.azurehdidnsight.net) y detenga los servicios de HBase. Cuando se le pida confirmar que desea detener los servicios, active la casilla para habilitar el modo de mantenimiento para HBase. Para más información sobre la conexión y el uso de Ambari, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![En Ambari, haga clic en la pestaña Servicios, luego en HBase en el menú de la izquierda y, a continuación, en Detener en Acciones de servicio](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Active la casilla de verificación para habilitar el modo de mantenimiento para HBase y confirme](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Inicie sesión en Ambari en el clúster de HDInsight nuevo. Cambie la configuración HDFS de `fs.defaultFS` para que apunte al nombre de contenedor que el clúster original usa. Esta configuración se encuentra en **HDFS > Configs > Advanced > Advanced core-site** (HDFS > Configuraciones > Avanzadas > Sitio principal avanzado).

    ![En Ambari, haga clic en la pestaña Servicios, luego en HDFS en el menú de la izquierda y, a continuación, en la pestaña Configs (Configuraciones) y en la pestaña Advanced (Avanzadas) que se encuentra abajo.](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![En Ambari, cambie el nombre del contenedor.](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. Guarde los cambios.
9. Reinicie todos los servicios necesarios según se indica en Ambari.
10. Dirija la aplicación al clúster nuevo.

    > [!NOTE]
    > El DNS estático de la aplicación cambia cuando se realiza la actualización. En lugar de codificar de forma rígida este DNS, puede configurar un CNAME en los valores de DNS del nombre de dominio que apunta al nombre del clúster. Otra opción es usar un archivo de configuración para la aplicación que se puede actualizar sin volver a implementar.

11. Inicie la ingesta para ver si todo funciona según lo previsto.
12. Si el clúster nuevo funciona de manera satisfactoria, elimine el clúster original.

## <a name="next-steps"></a>pasos siguientes

Para más información sobre HBase y sobre la actualización de los clústeres de HDInsight, consulte los artículos siguientes:

* [Actualización de un clúster de HDInsight a una versión más reciente](../hdinsight-upgrade-cluster.md)
* [Supervisión y administración de clústeres de Azure HDInsight mediante la interfaz de usuario web de Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Componentes y versiones de Hadoop](../hdinsight-component-versioning.md)
* [Optimización de la configuración mediante Ambari](../hdinsight-changing-configs-via-ambari.md#hbase-optimization-with-the-ambari-web-ui)
