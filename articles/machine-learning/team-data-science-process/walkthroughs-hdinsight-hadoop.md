---
title: Tutoriales de ciencia de datos de HDInsight Hadoop con Hive en Azure | Microsoft Docs
description: "Ejemplos del proceso de ciencia de datos en equipo donde se examina el uso de Hive en Azure HDInsight Hadoop para la realización de análisis predictivo."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev
ms.openlocfilehash: cef2dac8651933765c83410579eafb23460960f9
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Tutoriales de ciencia de datos de HDInsight Hadoop con Hive en Azure 

En estos tutoriales se usa Hive con un clúster de HDInsight Hadoop para realizar análisis predictivo. En ellos se siguen los pasos descritos en el proceso de ciencia de datos en equipo. Para obtener información general sobre el proceso de ciencia de datos en equipo, consulte [Proceso de ciencia de datos](overview.md). Para ver una introducción a Azure HDInsight, consulte [Introducción a Azure HDInsight, la pila de tecnología de Hadoop y los clústeres de Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md).

Los otros tutoriales de ciencia de datos donde se ejecuta el Proceso de ciencia de datos en equipo se agrupan por la **plataforma** que usan. Consulte [Walkthroughs executing the Team Data Science Process](walkthroughs.md) (Tutoriales para ejecutar el Proceso de ciencia de datos en equipo) para ver ejemplos.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Predicción de propinas para taxis mediante Hive con HDInsight Hadoop

En el tutorial [Uso de clústeres de HDInsight Hadoop](hive-walkthrough.md) se usan datos de taxis de New York para predecir: 

- Si se da una propina 
- La distribución de los importes de propina

El escenario se implementa mediante Hive con un [clúster de Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/). Aprenderá a almacenar, explorar y realizar la ingeniería de características con un conjunto de datos de carreras y tarifas de taxis de Nueva York. También usará Azure Machine Learning para crear e implementar los modelos.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Predicción de clics de anuncio mediante Hive con HDInsight Hadoop

En el tutorial [Uso de un clúster de Hadoop de HDInsight de Azure en un conjunto de datos de 1 TB](hive-criteo-walkthrough.md) se usa un conjunto de datos de clics de [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/), que está disponible públicamente, para predecir si se da una propina y el intervalo de importes esperado. El escenario se implementa mediante Hive con un [clúster de Azure HDInsight Hadoop ](https://azure.microsoft.com/services/hdinsight/) para almacenar, explorar, realizar la ingeniería de características y reducir el tamaño de los datos. Se emplea Azure Machine Learning para crear, entrenar y puntuar un modelo de clasificación binario que predice si un usuario hace clic en un anuncio. Para concluir el tutorial, se muestra cómo publicar uno de estos modelos como un servicio web.


## <a name="next-steps"></a>Pasos siguientes

Para obtener una explicación de los componentes clave que conforman el proceso de ciencia de datos en equipo, consulte [Información general del proceso de ciencia de datos en equipo](overview.md).

Para obtener una explicación del ciclo de vida del proceso de ciencia de datos en equipo que puede usar para estructurar los proyectos de ciencia de datos, consulte [Team Data Science Process lifecycle](lifecycle.md) (Ciclo de vida del proceso de ciencia de datos en equipo). El ciclo de vida describe el proceso, de principio a fin, que suelen seguir los proyectos al ejecutarlos. 

