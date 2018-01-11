---
title: "Directrices para la optimización del rendimiento de Hive en Azure Data Lake Store | Microsoft Docs"
description: "Directrices para la optimización del rendimiento de Hive en Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 57bd8758c2ae24922a959c9ce3893aad90dfe7e1
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-store"></a>Guía para la optimización del rendimiento de Hive en HDInsight y Azure Data Lake Store

La configuración predeterminada se ha establecido para proporcionar un buen rendimiento en muchos casos de uso diferentes.  Para las consultas de uso intensivo de E/S, Hive se puede optimizar para obtener un mejor rendimiento con ADLS.  

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Una cuenta de Almacén de Azure Data Lake**. Para obtener instrucciones sobre cómo crear una, consulte la [introducción al Almacén de Azure Data Lake](data-lake-store-get-started-portal.md)
* **Clúster de HDInsight de Azure** con acceso a una cuenta de Almacén de Data Lake. Consulte [Creación de un clúster de HDInsight con Data Lake Store mediante el Portal de Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Asegúrese de habilitar el Escritorio remoto para el clúster.
* **Ejecución de Hive en HDInsight**.  Para más información sobre cómo ejecutar trabajos de Hive en HDInsight, consulte [Uso de Hive en HDInsight] (https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Directrices para la optimización del rendimiento en ADLS**.  Para conocer los conceptos generales de rendimiento, consulte [Guía para la optimización del rendimiento de Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance).

## <a name="parameters"></a>parameters

Estos son los parámetros de configuración más importantes que se deben ajustar para mejorar el rendimiento de ADLS:

* **hive.tez.container.size**: la cantidad de memoria usada en cada tarea.

* **tez.grouping.min-size**: tamaño mínimo de cada asignador.

* **tez.grouping.min-size**: tamaño mínimo de cada asignador.

* **hive.exec.reducer.bytes.per.reducer**: tamaño de cada reductor.

**hive.tez.container.size**: el tamaño del contenedor determina cuánta memoria está disponible para cada tarea.  Esta es la entrada principal para controlar la simultaneidad en Hive.  

**tez.grouping.min-size**: este parámetro permite establecer el tamaño mínimo de cada asignador.  Si el número de asignadores que Tez elige es inferior al valor de este parámetro, Tez usará el valor establecido aquí.  

**tez.grouping.max-size**: el parámetro le permite establecer el tamaño máximo de cada asignador.  Si el número de asignadores que elige Tez es superior al valor de este parámetro, Tez usará entonces el valor establecido aquí.  

**hive.exec.reducer.bytes.per.reducer**: este parámetro establece el tamaño de cada reductor.  De forma predeterminada, cada reductor tiene 256 MB.  

## <a name="guidance"></a>Guía

**Set hive.exec.reducer.bytes.per.reducer**: el valor predeterminado funciona bien cuando los datos están descomprimidos.  En el caso de los datos que están comprimidos, debe reducir el tamaño del reductor.  

**Set hive.tez.container.size**: en cada nodo, la memoria se especifica mediante yarn.nodemanager.resource.memory-mb y se debe establecer correctamente en el clúster de HDI de forma predeterminada.  Para más información sobre la configuración de la memoria adecuada en YARN, consulte esta [entrada](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

Las cargas de trabajo que hacen un uso intensivo de E/S pueden beneficiarse de un mayor paralelismo disminuyendo el tamaño del contenedor de Tez. Con ello el usuario tiene más contenedores, lo que aumenta la simultaneidad.  Sin embargo, algunas consultas de Hive requieren una cantidad significativa de memoria (p. ej. MapJoin).  Si la tarea no tiene suficiente memoria, obtendrá una excepción de memoria insuficiente durante el tiempo de ejecución.  Si recibe excepciones de memoria insuficiente, debe aumentar la memoria.   

El número de tareas simultáneas en ejecución o con paralelismo estará enlazado por la cantidad total de memoria YARN.  El número de contenedores YARN determina cuántas tareas simultáneas se pueden ejecutar.  Para buscar la memoria YARN por nodo, puede ir a Ambari.  Vaya a YARN y examine la pestaña de configuración.  En esta ventana se muestra el tamaño de la memoria de YARN.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
La clave para mejorar el rendimiento mediante ADLS es aumentar la simultaneidad tanto como sea posible.  Tez calcula automáticamente el número de tareas que se deben crear por lo que no es necesario establecerlo.   

## <a name="example-calculation"></a>Cálculo de ejemplo

Supongamos que tiene un clúster D14 de 8 nodos.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Limitaciones
**Limitación de ADLS** 

Si alcanza los límites de ancho de banda impuestos por ADLS, empezará a ver errores de tareas. Esto puede identificarse observando los errores de limitación en los registros de tarea.  Puede reducir el paralelismo mediante un aumento del tamaño del contenedor de Tez.  Si necesita una mayor simultaneidad para su trabajo, póngase en contacto con nosotros.   

Para comprobar si le están aplicando limitaciones, debe habilitar el registro de depuración en el lado del cliente. Así es cómo debe hacerlo:

1. Coloque la siguiente propiedad en las propiedades de log4j en la configuración de Hive. Puede hacerlo desde la vista de Ambari: log4j.logger.com.microsoft.azure.datalake.store=DEBUG. Reinicie todos los nodos/servicios para que la configuración surta efecto.

2. Si se le están aplicando límites, verá el código de error HTTP 429 en el archivo de registro de Hive. El archivo de registro de Hive se encuentra en /tmp/&lt;user&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>Información adicional sobre la optimización de Hive

Estos son algunos blogs que le ayudará a optimizar las consultas de Hive:
* [Optimizar consultas de Hive para Hadoop en HDInsight](https://azure.microsoft.com/en-us/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Troubleshooting Hive query performance](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) (Solución de problemas de rendimiento de consultas de Hive)
* [Ignite talk on optimize Hive on HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25) (Acalorada charla sobre la optimización de Hive en HDInsight)
