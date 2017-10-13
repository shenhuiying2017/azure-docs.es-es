---
title: "Creación de un entorno de Azure Time Series Insights | Microsoft Docs"
description: "En este tutorial, aprenderá a crear un entorno de Time Series Insights, conectarse a un origen de eventos y estar listo para analizar los datos del evento en minutos."
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: omravi
ms.openlocfilehash: 5a159ed0ba38899b21212518d41ff995895904ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Creación de un nuevo entorno de Time Series Insights en Azure Portal

Un entorno de Time Series Insights es un recurso de Azure con funcionalidades de incorporación y almacenamiento. Los clientes aprovisionan los entornos mediante Azure Portal con la capacidad necesaria.

## <a name="steps-to-create-the-environment"></a>Pasos para crear el entorno

Siga estos pasos para crear el entorno:

1.  Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2.  Haga clic en el signo más ("+") en la esquina superior izquierda.
3.  Busque "Time Series Insights" en el cuadro de búsqueda.

  ![Creación del entorno de Time Series Insights](media/get-started/getstarted-create-environment1.png)

4.  Seleccione "Time Series Insights" y haga clic en "Crear".

  ![Creación del grupo de recursos de Time Series Insights](media/get-started/getstarted-create-environment2.png)

5.  Especifique el nombre del entorno. Este nombre representará al entorno en el [explorador de Time Series Insights](https://insights.timeseries.azure.com).
6.  Seleccione una suscripción. Elija una que contenga el origen de eventos. Time Series Insights puede detectar automáticamente los recursos de Azure IoT Hub y Event Hubs existentes en la misma suscripción.
7.  Seleccione o cree un grupo de recursos. Un grupo de recursos es una colección de recursos de Azure que se usan juntos.
8.  Selección de una ubicación de hospedaje. Para evitar mover datos entre centros de datos, elija la ubicación que contiene el origen de eventos.
9.  Seleccione un plan de tarifa.
10. Seleccione la capacidad. Puede cambiar la capacidad de un entorno después de su creación.
11. Cree el entorno. También puede anclar el entorno en el panel para facilitar el acceso siempre que inicie sesión.

  ![Anclaje de Time Series Insights al panel](media/get-started/getstarted-create-environment3.png)

## <a name="next-steps"></a>Pasos siguientes

* [Definición de las directivas de acceso de datos](time-series-insights-data-access.md) para tener acceso al entorno en el [portal de Time Series Insights](https://insights.timeseries.azure.com)
* [Creación de un origen de eventos](time-series-insights-add-event-source.md)
* [Envío de eventos](time-series-insights-send-events.md) al origen de eventos
