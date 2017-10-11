---
title: "Adición de cuentas de almacenamiento de Azure a HDInsight | Microsoft Docs"
description: "Aprenda a agregar cuentas de Azure Storage adicionales a un clúster de HDInsight existente."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/04/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 0853e8605e07c28867676e9c13b89263ade67c88
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Adición de más cuentas de almacenamiento a HDInsight

Aprenda a usar acciones de script para agregar cuentas de almacenamiento de Azure adicionales a HDInsight. Los pasos descritos en este documento agregan una cuenta de almacenamiento a un clúster de HDInsight existente basado en Linux.

> [!IMPORTANT]
> La información de este documento trata sobre cómo agregar almacenamiento adicional a un clúster después de que se ha creado. Para más información sobre cómo agregar cuentas de almacenamiento durante la creación del clúster, consulte [Configuración de clústeres de HDInsight con Hadoop, Spark, Kafka y mucho más](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="how-it-works"></a>Cómo funciona

Este script toma los parámetros siguientes:

* __Nombre de la cuenta de almacenamiento de Azure__: el nombre de la cuenta de almacenamiento que se agregará al clúster de HDInsight. Después de ejecutar el script, HDInsight podrá leer y escribir los datos almacenados en esta cuenta de almacenamiento.

* __Clave de la cuenta de almacenamiento de Azure__: una clave que concede acceso a la cuenta de almacenamiento.

* __-p__ (opcional): si se especifica, la clave no se cifra y se almacena en el archivo core-site.xml como texto sin formato.

Durante el procesamiento, el script realiza las siguientes acciones:

* Si la cuenta de almacenamiento ya existe en la configuración de core-site.xml para el clúster, el script se cierra y no se lleva a cabo ninguna otra acción.

* Comprueba que la cuenta de almacenamiento existe y que se puede acceder a ella mediante la clave.

* Cifra la clave con la credencial del clúster.

* Agrega la cuenta de almacenamiento al archivo core-site.xml.

* Detiene y reinicia los servicios de Oozie, YARN, MapReduce2 y HDFS. Detener e iniciar estos servicios permite que usen la nueva cuenta de almacenamiento.

> [!WARNING]
> No se admite el uso de una cuenta de almacenamiento en una ubicación diferente a la del clúster de HDInsight.

## <a name="the-script"></a>La secuencia de comandos

__Ubicación del script__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Requisitos__:

* El script se debe aplicar en los __nodos principales__.

## <a name="to-use-the-script"></a>Para usar el script

Este script se puede utilizar a través de Azure Portal, Azure PowerShell o la CLI de Azure 1.0. Para más información, consulte el documento [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

> [!IMPORTANT]
> Al utilizar los pasos indicados en el documento de personalización, utilice la siguiente información para aplicar este script:
>
> * Sustituya el identificador URI de la acción de script necesaria por el identificador URI de este script (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh).
> * Reemplace los parámetros de ejemplo por el nombre de la cuenta de almacenamiento de Azure y la clave de la cuenta de almacenamiento que va a agregar al clúster. Si usa Azure Portal, estos parámetros deben estar separados por un espacio.
> * No es necesario marcar este script como __Guardado__, porque actualiza directamente la configuración de Ambari para el clúster.

## <a name="known-issues"></a>Problemas conocidos

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Cuentas de almacenamiento que no se muestran en Azure Portal o las herramientas

Cuando vea el clúster de HDInsight en Azure Portal, si selecciona la entrada __Cuentas de almacenamiento__ en __Propiedades__, no se mostrarán las cuentas de almacenamiento agregadas mediante esta acción de script. Azure PowerShell y la CLI de Azure tampoco mostrarán la cuenta de almacenamiento adicional.

Esto se debe a que el script solo modifica la configuración de core-site.xml del clúster. Esta información no se usa al recuperar la información del clúster mediante las API de administración de Azure.

Para ver información de la cuenta de almacenamiento agregada al clúster mediante este script, use la API de REST de Ambari. Use los siguientes comandos para recuperar esta información para su clúster:

```PowerShell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]
> Establezca `$clusterName` en el nombre del clúster de HDInsight. Establezca `$storageAccountName` en el nombre de la cuenta de almacenamiento. Cuando se le solicite, escriba el nombre de usuario y la contraseña de administrador del clúster.

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]
> Establezca `$PASSWORD` en la contraseña de cuenta de inicio de sesión del clúster. Establezca `$CLUSTERNAME` en el nombre del clúster de HDInsight. Establezca `$STORAGEACCOUNTNAME` en el nombre de la cuenta de almacenamiento.
>
> El comando siguiente muestra cómo usar [curl (http://curl.haxx.se/)](http://curl.haxx.se/) y [jq (https://stedolan.github.io/jq/)](https://stedolan.github.io/jq/) para recuperar y analizar datos JSON.

Cuando use este comando, reemplace __CLUSTERNAME__ por el nombre del clúster de HDInsight. Reemplace __PASSWORD__ por la contraseña de inicio de sesión HTTP del clúster. Reemplace __STORAGEACCOUNT__ por el nombre de la cuenta de almacenamiento agregada mediante la acción de script. La información que devuelve este comando es similar al siguiente texto:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Este texto es un ejemplo de una clave cifrada que se usa para acceder a la cuenta de almacenamiento.

### <a name="unable-to-access-storage-after-changing-key"></a>No se puede acceder a almacenamiento después de cambiar la clave

Si cambia la clave de una cuenta de almacenamiento, HDInsight ya no podrá acceder a dicha cuenta. HDInsight usa una copia en caché de clave del archivo core-site.xml para el clúster. Esta copia en caché debe actualizarse para que coincida con la nueva.

La ejecución nuevamente de la acción de script __no__ actualizará la clave, ya que el script comprueba si ya existe una entrada para la cuenta de almacenamiento. Si ya existe una entrada, no realice ningún cambio.

Para solucionar este problema, debe quitar la entrada existente para la cuenta de almacenamiento. Realice los siguientes pasos para quitar la entrada:

1. Abra un explorador web y abra la interfaz de usuario web de Ambari de su clúster de HDInsight. El identificador URI es https://CLUSTERNAME.azurehdinsight.net. Reemplace __CLUSTERNAME__ por el nombre del clúster.

    Cuando se le solicite, escriba el usuario de inicio de sesión HTTP y la contraseña para el clúster.

2. En la lista de servicios situada a la izquierda de la página, seleccione __HDFS__. A continuación, seleccione la pestaña __Configs__ (Configuraciones) en el centro de la página.

3. En el campo __Filter...__ (Filtro), escriba un valor de __fs.azure.account__. Esta acción devolverá entradas para las cuentas de almacenamiento adicionales que se hayan agregado al clúster. Hay dos tipos de entradas; __keyprovider__ y __key__. Ambas contendrán el nombre de la cuenta de almacenamiento como parte del nombre de clave.

    Los siguientes son entradas de ejemplo para una cuenta de almacenamiento denominada __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Después de haber identificado las claves de la cuenta de almacenamiento que quiere quitar, use el icono rojo '-' a la derecha de la entrada para eliminarlas. Haga clic en el botón __Guardar__ para guardar los cambios.

5. Después de guardar los cambios, use la acción de secuencia de comandos para agregar la cuenta de almacenamiento y el nuevo valor de clave a clúster.

### <a name="poor-performance"></a>Rendimiento deficiente

Si la cuenta de almacenamiento está en una región distinta a la del clúster de HDInsight, puede que experimente un rendimiento deficiente. Al acceder a los datos en una región diferente, el tráfico de red se envía fuera del centro de datos regional de Azure y en la red pública de Internet, lo que puede producir latencia.

> [!WARNING]
> No se admite el uso de una cuenta de almacenamiento en una región diferente a la del clúster de HDInsight.

### <a name="additional-charges"></a>Cargos adicionales

Si la cuenta de almacenamiento se encuentra en una región distinta a la del clúster de HDInsight, puede que observe cargos de salida adicionales en su facturación de Azure. Se aplica un cargo de salida cuando los datos salen de un centro de datos regional. Este cargo se aplica incluso si el tráfico va destinado a otro centro de datos de Azure en una región distinta.

> [!WARNING]
> No se admite el uso de una cuenta de almacenamiento en una región diferente a la del clúster de HDInsight.

## <a name="next-steps"></a>Pasos siguientes

En este documento ha aprendido a agregar más cuentas de almacenamiento a un clúster de HDInsight. Para más información sobre las acciones de script, consulte [Personalización de clústeres de HDInsight basados en Linux mediante la acción de script](hdinsight-hadoop-customize-cluster-linux.md).
