---
title: "Adición de un conjunto de datos de referencia al entorno de Azure Time Series Insights | Microsoft Docs"
description: "En este artículo se describe cómo agregar un conjunto de datos de referencia al entorno de Azure Time Series Insights. Los datos de referencia son útiles para unirse a los datos de origen y aumentar los valores."
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 7cdcefbd0daec3b7bab59680afa1470624583c74
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Creación de un conjunto de datos de referencia para el entorno de Time Series Insights mediante Azure Portal

En este artículo se describe cómo agregar un conjunto de datos de referencia al entorno de Azure Time Series Insights. Los datos de referencia son útiles para unirse a los datos de origen y aumentar los valores.

Un conjunto de datos de referencia es una colección de elementos que aumentan con los eventos de un origen de eventos. El motor de entrada de Time Series Insights une a un evento del origen de eventos con un elemento en el conjunto de datos de referencia. A partir de ese momento, este evento aumentado está disponible para consultas. Esta combinación se basa en las claves definidas en el conjunto de datos de referencia.

## <a name="add-a-reference-data-set"></a>Adición de un conjunto de datos de referencia

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Busque su entorno de Time Series Insights existente. Haga clic en **Todos los recursos** en el menú izquierdo de Azure Portal. Seleccione el entorno de Time Series Insights.

3. En el encabezado **Topología del entorno**, seleccione **Conjuntos de datos de referencia**.

    ![Crear el conjunto de datos de referencia de Time Series Insights](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. Seleccione **+ Agregar** para agregar un nuevo conjunto de datos de referencia.

5. Especifique un **Nombre del conjunto de datos de referencia** único.

    ![Crear el conjunto de datos de referencia de Time Series Insights (detalles)](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

6. Especifique el **Nombre de clave** en el campo vacío y seleccione el **Tipo**. El nombre y el tipo se utilizan para elegir la propiedad correcta de los eventos para el evento de origen de unión a los datos de referencia. 

   Por ejemplo, si especifica el nombre de clave **DeviceId** y el tipo **Cadena**, el motor de ingesta de Time Series Insights busca una propiedad con el nombre **DeviceId** del tipo **Cadena** en los eventos de entrada para unirse a ese evento. Puede proporcionar más de una clave para realizar la combinación con el evento. La coincidencia del nombre de la clave distingue mayúsculas de minúsculas.

7. Seleccione **Crear**.

## <a name="next-steps"></a>Pasos siguientes
* [Administración de datos de referencia](time-series-insights-manage-reference-data-csharp.md) mediante programación.
* Para obtener una referencia completa a la API, consulte el documento [API de datos de referencia](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api).
