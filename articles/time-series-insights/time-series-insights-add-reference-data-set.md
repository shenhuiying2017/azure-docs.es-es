---
title: "Incorporación de un conjunto de datos de referencia al entorno de Azure Time Series Insights | Microsoft Docs"
description: En este tutorial, se agrega un conjunto de datos de referencia al entorno de Time Series Insights
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: venkatja
ms.openlocfilehash: 574f3c5b3a889733f47d729c795ec39009f2ef4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-ibiza-portal"></a>Creación de un conjunto de datos de referencia para el entorno de Time Series Insights mediante el portal Ibiza

Un conjunto de datos de referencia es una colección de elementos que aumentan con los eventos de un origen de eventos. El motor de entrada de Time Series Insights une a un evento del origen de eventos con un elemento en el conjunto de datos de referencia. A partir de ese momento, este evento aumentado está disponible para consultas. Esta combinación se basa en las claves definidas en el conjunto de datos de referencia.

## <a name="steps-to-add-a-reference-data-set-to-your-environment"></a>Pasos para agregar un conjunto de datos de referencia a un entorno

1. Inicie sesión en el [portal Ibiza](https://portal.azure.com).
2. Haga clic en "Todos los recursos" en el menú izquierdo del portal Ibiza.
3. Seleccione el entorno de Time Series Insights.

    ![Crear el conjunto de datos de referencia de Time Series Insights](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. Seleccione "Conjuntos de datos de referencia" y haga clic en "+ Agregar".

    ![Crear el conjunto de datos de referencia de Time Series Insights (detalles)](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

5. Especifique el nombre del conjunto de datos de referencia.
6. Especifique tanto el nombre de la clave como su tipo. Dichos nombre y el tipo se utilizan para elegir la propiedad correcta en el evento del origen de eventos. Por ejemplo, si especifica el nombre de clave "DeviceId" y el tipo "Cadena", el motor de entrada de Time Series Insights busca una propiedad llamada "DeviceId" del tipo "Cadena" en el evento de entrada. Puede proporcionar más de una clave para realizar la combinación con el evento. La coincidencia del nombre de la propiedad distingue mayúsculas de minúsculas.

     ![Crear el conjunto de datos de referencia de Time Series Insights (detalles)](media/add-reference-data-set/getstarted-create-reference-data-set-3.png)

7. Haga clic en "Crear".

## <a name="next-steps"></a>Pasos siguientes

* [Administración de datos de referencia](time-series-insights-manage-reference-data-csharp.md) mediante programación.
* Para obtener una referencia completa a la API, consulte el documento [API de datos de referencia](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api).