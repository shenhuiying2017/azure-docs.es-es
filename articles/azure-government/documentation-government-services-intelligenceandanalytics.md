---
title: "Inteligencia y análisis de Azure Government | Microsoft Docs"
description: "Esto proporciona una comparación de funciones e instrucciones sobre cómo desarrollar aplicaciones para la administración de Azure"
services: azure-government
cloud: gov
documentationcenter: 
author: MeganYount
manager: zakramer
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/06/2016
ms.author: MeganYount
translationtype: Human Translation
ms.sourcegitcommit: 14fb62669e15873770ef9b8470e34db70f1ca2ca
ms.openlocfilehash: 2b89d78e20cccee81c9581e762e4c61bacd84fd4
ms.lasthandoff: 02/15/2017


---
# <a name="azure-government-intelligence--analytics"></a>Inteligencia y análisis de Azure Government
En este artículo se describen los servicios, las variaciones y las consideraciones de análisis e inteligencia para el entorno de Azure Government.

## <a name="azure-hdinsight"></a>HDInsight de Azure
HDInsight en Linux Standard suele estar disponible en Azure Government. Puede ver una demostración sobre cómo compilar soluciones basadas en datos en Azure Government con HDInsight <a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>aquí</a>.

HDInsight en Linux Premium estará disponible próximamente.

### <a name="variations"></a>Variaciones
Las siguientes características de HDInsight no están actualmente disponibles en Azure Government.

* HDInsight no está disponible en Windows.
* Azure Data Lake Store no está actualmente disponible en Azure Government. Azure Blob Storage es la única opción de almacenamiento disponible en este momento.

Las direcciones URL para Log Analytics son diferentes en Azure Government:

| Tipo de servicio | Azure Public | Azure Government |
| --- | --- | --- |
| Clúster de HDInsight | \*.azurehdinsight.net | \*.azurehdinsight.us |

Para obtener más información, consulte [Azure HDInsight public documentation](../hdinsight/hdinsight-hadoop-introduction.md) (Documentación pública de Azure HDInsight).

## <a name="power-bi"></a>Power BI
Power BI US Government suele estar disponible como parte de las suscripciones a Office 365 US Government Community. Puede obtener información sobre Power BI US Government <a href=https://powerbi.microsoft.com/en-us/documentation/powerbi-service-govus-overview/>aquí</a>. Puede ver una demostración sobre cómo compilar soluciones basadas en datos en Azure Government con Power BI <a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>aquí</a>.

Power BI Embedded estará disponible próximamente.

### <a name="variations"></a>Variaciones

Las direcciones URL para Power BI son diferentes de las de US Government:

| Tipo de servicio | Power BI Commercial | Power BI US Government |
| --- | --- | --- |
| Power BI URL | app.powerbi.com | app.powerbigov.us |

## <a name="next-steps"></a>Pasos siguientes
Para obtener información complementaria y actualizaciones, suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government</a>.

