---
title: Copia de datos a y desde WASB en Data Lake Store mediante Distcp| Microsoft Docs
description: "Use la herramienta Distcp para copiar datos a y desde los blobs de Almacenamiento de Azure al Almacén de Data Lake."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 12aea210308636677ba2905887ddd24dc5c35238
ms.contentlocale: es-es
ms.lasthandoff: 03/25/2017


---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Utilice Distcp para copiar datos entre los blobs de Almacenamiento de Azure y el Almacén de Data Lake
> [!div class="op_single_selector"]
> * [Uso de DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Uso de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Una vez que haya creado un clúster de HDInsight que tenga acceso a una cuenta Data Lake Store, puede usar herramientas de ecosistema de Hadoop como Distcp para copiar datos **a y desde** un almacenamiento de clúster de HDInsight (WASB) en una cuenta de Data Lake Store. En este artículo se ofrecen instrucciones sobre cómo lograr esto.

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este artículo, debe tener lo siguiente:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Una cuenta de Almacén de Azure Data Lake**. Para obtener instrucciones sobre cómo crear una, consulte la [introducción al Almacén de Azure Data Lake](data-lake-store-get-started-portal.md)
* **Clúster de HDInsight de Azure** con acceso a una cuenta de Almacén de Data Lake. Consulte [Creación de un clúster de HDInsight con Data Lake Store mediante el Portal de Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Asegúrese de habilitar el Escritorio remoto para el clúster.

## <a name="do-you-learn-fast-with-videos"></a>¿Obtener información más rápidamente con vídeos?
[Vea este vídeo](https://mix.office.com/watch/1liuojvdx6sie) para saber cómo copiar datos entre los blobs de Almacenamiento de Azure y Almacén de Data Lake mediante DistCp.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Usar Distcp desde un clúster de HDInsight de Linux

Un clúster de HDInsight incluye la utilidad Distcp, que puede utilizarse para copiar datos de orígenes diferentes en un clúster de HDInsight. Si ha configurado el clúster de HDInsight para utilizar el Almacén de Data Lake como almacenamiento adicional, la utilidad Distcp puede utilizarse también directamente y sin configuraciones adicionales para copiar datos a y desde una cuenta de Almacén de Data Lake. En esta sección veremos cómo usar la utilidad Distcp.

1. Desde el escritorio, use SSH para conectarse al clúster. Consulte [Conexión a un clúster de HDInsight basado en Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Ejecute los comandos desde el símbolo del sistema SSH.

2. Compruebe si puede tener acceso a los blobs de Almacenamiento de Azure (WASB). Ejecute el siguiente comando:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Esto debería proporcionar una lista de contenido en el blob de almacenamiento.
3. De forma similar, compruebe si puede tener acceso a la cuenta del Almacén de Data Lake desde el clúster. Ejecute el siguiente comando:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    Esto debería proporcionar una lista de archivos o carpetas en la cuenta del Almacén de Data Lake.
4. Utilice Distcp para copiar datos desde WASB a una cuenta de Almacén de Data Lake.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    Esto copiará el contenido de la carpeta **/example/data/gutenberg/** de WASB a **/myfolder** en la cuenta de Data Lake Store.
5. Asimismo, utilice Distcp para copiar datos de la cuenta de la cuenta del Almacén de Data Lake a WASB.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Esto copiará el contenido de **/myfolder** de la cuenta de Data Lake Store en la carpeta **/example/data/gutenberg/** de WASB.

## <a name="performance-considerations-while-using-distcp"></a>Consideraciones de rendimiento sobre el uso de DistCp

Dado que la granularidad más baja de DistCp es un único archivo, configurar el número máximo de copias simultáneas es el parámetro más importante para optimizar con respecto a Data Lake Store. Para controlar esto se establece el número de parámetros de asignador ("m ") en la línea de comandos. Este parámetro especifica el número máximo de asignadores que se usará para copiar los datos. El valor predeterminado es 20.

**Ejemplo**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>¿Cómo se puede determinar el número de asignadores que se debe usar?

A continuación hay algunas instrucciones que puede usar.

* **Paso 1: Determinación de la memoria total de YARN**: el primer paso consiste en determinar la memoria YARN disponible para el clúster donde se ejecuta el trabajo DistCp. Esta información está disponible en el portal de Ambari asociado con el clúster. Vaya a YARN y haga clic en la pestaña de configuración para ver la memoria YARN. Para obtener la memoria de YARN total, multiplique la memoria de YARN por cada nodo por el número de nodos que tiene en el clúster.

* **Paso 2: Cálculo del número de asignadores**: el valor de **m** es igual al cociente de la memoria de YARN total dividido por el tamaño del contenedor de YARN. La información del tamaño de contenedor de YARN está también disponible en el portal del Ambari. Vaya a YARN y examine la pestaña de configuración. En esta ventana se muestra el tamaño del contenedor de YARN. La ecuación para llegar al número de asignadores (**m**) es

        m = (number of nodes * YARN memory for each node) / YARN container size

**Ejemplo**

Supongamos que tiene 4 nodos D14v2s en el clúster y que intenta transferir 10 TB de datos desde 10 carpetas diferentes. Cada una de las carpetas contiene diferentes cantidades de datos y los tamaños de archivo dentro de cada carpeta son diferentes.

* Memoria de YARN total: en el portal de Ambari determinará que la memoria de YARN es de 96 GB para un nodo D14. Por lo tanto, la memoria de YARN total para el clúster de 4 nodos es: 

        YARN memory = 4 * 96GB = 384GB

* Número de asignadores: en el portal de Ambari determinará que el tamaño del contenedor de YARN es 3072 para un nodo del clúster D14. Por lo tanto, el número de asignadores es:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Si otras aplicaciones usan memoria, solo puede elegir usar una parte de la memoria de YARN del clúster para DistCp.

### <a name="copying-large-datasets"></a>Copia de grandes conjuntos de datos

Cuando el tamaño del conjunto de datos que se va a mover es muy grande (por ejemplo, >1 TB) o si tiene muchas carpetas diferentes, debe considerar el uso de varios trabajos de DistCp. Es probable que no haya ninguna mejora de rendimiento, pero se reparten los trabajos por lo que si se produce un error en cualquier trabajo, solo tendrá que reiniciar ese trabajo específico en lugar de todo el trabajo.

### <a name="limitations"></a>Limitaciones

* DistCp intenta crear a asignadores que tengan un tamaño similar para optimizar el rendimiento. El aumento del número de asignadores no siempre aumenta el rendimiento.

* DistCp está limitado a solo un asignador por archivo. Por lo tanto, no debería tener más asignadores que archivos. Como DistCp solo puede asignar a un asignador a un archivo, esto limita la cantidad de simultaneidad que puede usarse para copiar archivos de gran tamaño.

* Si tiene un número pequeño de archivos de gran tamaño, debe dividirlos en fragmentos de archivo de 256 MB para ofrecerle una mayor simultaneidad en potencia. 
 
* Si va a copiar desde una cuenta de Azure Blob Storage, el trabajo de copia puede estar limitado por el lado del almacenamiento de blobs. Como consecuencia, disminuirá el rendimiento de su trabajo de copia. Para aprender sobre los límites de Azure Blob Storage, consulte la información al respecto en [Límites de suscripciones y servicios de Azure](../azure-subscription-service-limits.md).

## <a name="see-also"></a>Consulte también
* [Copiar datos de los blobs de almacenamiento de Azure en el Almacén Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
* [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
* [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

