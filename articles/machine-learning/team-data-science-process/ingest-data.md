---
title: "Carga de datos en entornos de almacenamiento de Azure para el análisis | Microsoft Docs"
description: Mover datos hacia y desde Azure Blob Storage
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: bradsev
ms.openlocfilehash: 7d7da4f6dfed03d470c5b5706aaf412c07096120
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Carga de datos en entornos de almacenamiento para el análisis
El proceso de ciencia de datos en equipos requiere que los datos se introduzcan o se cargue en una variedad de entornos de almacenamiento diferentes para que se procesen o analicen de la manera más adecuada en cada fase del proceso. Entre los destinos de datos más usados para el procesamiento se incluyen el almacenamiento de blobs de Azure, las bases de datos SQL Azure, SQL Server en VM de Azure, HDInsight (Hadoop) y Azure Machine Learning. 

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

Este **menú** vincula a temas en los que se describe cómo introducir datos en estos entornos de destino donde se almacenan y se procesan los datos.

Las necesidades técnicas y empresariales, así como la ubicación inicial, el formato y el tamaño de sus datos determinarán los entornos de destino en los que deben introducirse los datos para lograr los objetivos de su análisis. No es raro que un escenario requiera que los datos se muevan entre varios entornos para lograr la variedad de las tareas necesarias para construir un modelo predictivo. En esta secuencia de tareas se puede incluir, por ejemplo, la exploración de datos, el procesamiento previo, la limpieza, el muestreo inferior y el entrenamiento del modelo.

