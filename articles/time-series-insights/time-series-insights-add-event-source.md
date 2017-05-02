---
title: "Incorporación de un origen de eventos al entorno de Azure Time Series Insights | Microsoft Docs"
description: En este tutorial, se conecta un origen de eventos al entorno de Time Series Insights
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 9dc394c0da34a8ee2796c6114e7f2f647c5345a1
ms.lasthandoff: 04/26/2017

---

# <a name="create-an-event-source-for-your-time-series-insights-environment-using-the-azure-portal"></a>Creación de un origen de eventos para el entorno de Time Series Insights mediante Azure Portal

El origen de eventos de Time Series Insights se deriva de un agente de eventos, como Azure Event Hubs. Time Series Insights se conecta directamente a los orígenes de eventos e ingiere el flujo de datos sin necesidad de que los usuarios escriban una sola línea de código. Actualmente, Time Series Insights admite Azure Event Hubs y Azure IoT Hubs y, en el futuro, se agregarán más orígenes de eventos.

## <a name="steps-to-add-an-event-source-to-your-environment"></a>Pasos para agregar un origen de eventos a su entorno

1.    Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2.    Haga clic en "Todos los recursos" en el menú izquierdo de Azure Portal.
3.    Seleccione el entorno de Time Series Insights.

  ![Creación del origen de eventos de Time Series Insights](media/add-event-source/getstarted-create-eventsource1.png)

4.    Seleccione "Orígenes de eventos", haga clic en "+Agregar".

  ![Creación del origen de eventos de Time Series Insights: detalles](media/add-event-source/getstarted-create-eventsource2.png)

5.    Especifique el nombre del origen de eventos. Este nombre se asocia a todos los eventos procedentes de este origen de eventos y está disponible en el momento de la consulta.
6.    Seleccione un centro de eventos de la lista de recursos de Event Hubs de la suscripción actual, o elija la opción de importación "Proporcionar configuración del centro de eventos de forma manual" para especificar un centro de eventos que se encuentra en otra suscripción. Los eventos se deben publicar en formato JSON.
7.    Seleccione una directiva con permiso de lectura en el centro de eventos.
8.    Especifique el grupo de consumidores del centro de eventos.

  > [!IMPORTANT]
  > Asegúrese de que el grupo de consumidores no es utilizado por ningún otro servicio (como un trabajo de Stream Analytics u otro entorno de Time Series Insights). Si otros servicios utilizan el grupo de consumidores, la operación de lectura se ve afectada negativamente en este entorno y en los otros servicios. Utilizar “$Default” como grupo de consumidores podría provocar que otros lectores lo reutilicen.

9.    Haga clic en "Crear".

Tras la creación del origen de eventos, Time Series Insights iniciará automáticamente la transmisión de datos al entorno.

## <a name="next-steps"></a>Pasos siguientes

* [Envío de eventos](time-series-insights-send-events.md) al origen de eventos
* Vea el entorno en el [portal de Time Series Insights](https://insights.timeseries.azure.com)

