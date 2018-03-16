---
title: "Importación de datos en Machine Learning Studio desde otro experimento | Microsoft Docs"
description: "Cómo guardar los datos de aprendizaje en Azure Machine Learning Studio y usarlo en otro experimento."
keywords: "importar datos, datos, orígenes de datos, datos de entrenamiento"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 7da9dcec-5693-4bb6-8166-15904e7f75c3
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: bradsev
ms.openlocfilehash: 411cf5960381873d5b348bd35f51b8190900d842
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="import-your-data-into-azure-machine-learning-studio-from-another-experiment"></a>Importación de datos en Azure Machine Learning Studio desde otro experimento
[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Habrá ocasiones en las que querrá tomar un resultado intermedio de un experimento y usarlo como parte de otro experimento. Para ello, guarde el módulo como un conjunto de datos:

1. Haga clic en la salida del módulo que desea guardar como conjunto de datos.
2. Haga clic en **Guardar como conjunto de datos**.
3. Cuando se le solicite, escriba un nombre y una descripción que le permitan identificar fácilmente el conjunto de datos.
4. Haga clic en la marca de verificación **Aceptar** .

Cuando termine de guardar, el conjunto de datos estará disponible para usarlo dentro de cualquier experimento de su área de trabajo. Puede encontrarlo en la lista **Conjuntos de datos guardados** de la paleta de módulos.

