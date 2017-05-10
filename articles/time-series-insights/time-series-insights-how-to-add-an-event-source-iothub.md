---
title: "Adición de un origen de eventos de IoT Hub al entorno de Azure Time Series Insights | Microsoft Docs"
description: "Este tutorial describe cómo agregar un origen de eventos que está conectado a un IoT Hub para su entorno de Time Series Insights."
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 72037677fac528ff8174d25b474ca7e70826a7b0
ms.contentlocale: es-es
ms.lasthandoff: 05/01/2017

---
# <a name="how-to-add-an-iot-hub-event-source"></a>Adición de un origen de eventos de IoT Hub

Este tutorial describe cómo usar Azure Portal para agregar un origen de eventos que se lea desde un IoT Hub a su entorno de Time Series Insights.

## <a name="prerequisites"></a>Requisitos previos

Ha creado un IoT Hub y escribe eventos en él. Para más información sobre IoT Hubs, vea <https://azure.microsoft.com/services/iot-hub/>

> [Grupos de consumidores] Cada origen de eventos de Time Series Insights tiene que tener su propio grupo de consumidores dedicado que no se comparte con otros consumidores. Si varios lectores consumen eventos desde el mismo grupo de consumidores, es probable que todos los lectores vean errores. Para más información, vea la [Guía para desarrolladores de IoT Hub](../iot-hub/iot-hub-devguide.md).

## <a name="choose-an-import-option"></a>Selección de un opción de importación

La configuración del origen de eventos puede especificarse manualmente o se puede seleccionar un IoT Hub desde los IoT Hub disponibles.
En el selector **Opción de importación**, elija una de las siguientes opciones:

* Proporcionar configuración de IoT Hub de forma manual
* Usar IoT Hub desde suscripciones disponibles

### <a name="select-an-available-iot-hub"></a>Seleccionar un IoT Hub disponible

La siguiente tabla explica cada opción en la pestaña Nuevo origen de eventos con su descripción al seleccionar un IoT Hub disponible como un origen de eventos:

| Nombre de propiedad | Description |
| --- | --- |
| Nombre de origen de eventos | El nombre del origen de eventos. Este nombre debe ser único dentro del entorno de Time Series Insights.
| Origen | Seleccione **IoT Hub** para crear un origen de eventos de IoT Hub.
| Id. de suscripción | Seleccione la suscripción en la que se creó IoT Hub.
| Nombre de IoT Hub | Seleccione el nombre del IoT Hub.
| Nombre de la directiva de IoT Hub | Seleccione la directiva de acceso compartido, que puede encontrarse en la pestaña Configuración de IoT Hub. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. La directiva de acceso compartido para el origen de eventos *debe* tener permisos de **conexión de servicios**.
| Grupo de consumidores de IoT Hub | El grupo de consumidores para leer eventos desde IoT Hub. Se recomienda fehacientemente usar un grupo de consumidores dedicado para el origen del evento.

### <a name="provide-iot-hub-settings-manually"></a>Proporcionar configuración de IoT Hub de forma manual

La siguiente tabla explica cada propiedad en la pestaña Nuevo origen de eventos con su descripción al escribir los ajustes manualmente:

| Nombre de propiedad | Description |
| --- | --- |
| Nombre de origen de eventos | El nombre del origen de eventos. Este nombre debe ser único dentro del entorno de Time Series Insights.
| Origen | Seleccione **IoT Hub** para crear un origen de eventos de IoT Hub.
| Id. de suscripción | La suscripción en la que se creó este IoT Hub.
| Grupos de recursos | La suscripción en la que se creó este IoT Hub.
| Nombre de IoT Hub | El nombre de IoT Hub. Cuando creó IoT Hub, también le asignó un nombre específico.
| Nombre de la directiva de IoT Hub | La directiva de acceso compartido, que puede crearse en la pestaña Configuración de IoT Hub. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. La directiva de acceso compartido para el origen de eventos *debe* tener permisos de **conexión de servicios**.
| Clave de la directiva de IoT Hub | La clave de acceso compartido usada para autenticar el acceso al espacio de nombres del Bus de servicio. Escriba la clave principal o secundaria aquí.
| Grupo de consumidores de IoT Hub | El grupo de consumidores para leer eventos desde IoT Hub. Se recomienda fehacientemente usar un grupo de consumidores dedicado para el origen del evento.

## <a name="next-steps"></a>Pasos siguientes

1. Adición de un directiva de acceso de datos al entorno [Definir directivas de acceso de datos](time-series-insights-data-access.md)
1. Acceso al entorno en el [Portal de Time Series Insights](https://insights.timeseries.azure.com)
