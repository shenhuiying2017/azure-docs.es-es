---
title: "Adición de un origen de eventos de centro de eventos al entorno de Azure Time Series Insights | Microsoft Docs"
description: "Este tutorial describe cómo agregar un origen de eventos que está conectado a un centro de eventos para su entorno de Time Series Insights."
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
ms.openlocfilehash: 216c2146371e2b88d4a3d7aa4f08ae8186e89443
ms.contentlocale: es-es
ms.lasthandoff: 05/01/2017

---
# <a name="how-to-add-an-event-hub-event-source"></a>Adición de un origen de eventos de centro de eventos

Este tutorial describe cómo usar Azure Portal para agregar un origen de eventos que se lea desde un centro de eventos a su entorno de Time Series Insights.

## <a name="prerequisites"></a>Requisitos previos

Ha creado un centro de eventos y escribe eventos en él. Para más información sobre centros de eventos, vea <https://azure.microsoft.com/services/event-hubs/>.

> [Grupos de consumidores] Cada origen de eventos de Time Series Insights tiene que tener su propio grupo de consumidores dedicado que no se comparte con otros consumidores. Si varios lectores consumen eventos desde el mismo grupo de consumidores, es probable que todos los lectores vean errores. Tenga en cuenta que también hay un límite de 20 grupos de consumidores por Centro de eventos. Para obtener detalles, consulte la [Guía de programación de Centros de eventos](../event-hubs/event-hubs-programming-guide.md).

## <a name="choose-an-import-option"></a>Selección de un opción de importación

La configuración del origen de eventos puede especificarse manualmente o se puede seleccionar un centro de eventos desde los centros de eventos disponibles.
En el selector **Opción de importación**, elija una de las siguientes opciones:

* Proporcionar configuración del centro de eventos de forma manual
* Usar el centro de eventos desde suscripciones disponibles

### <a name="select-an-available-event-hub"></a>Seleccionar un centro de eventos disponible

La siguiente tabla explica cada opción en la pestaña Nuevo origen de eventos con su descripción al seleccionar un centro de eventos disponible como un origen de eventos:

| Nombre de propiedad | Description |
| --- | --- |
| Nombre de origen de eventos | El nombre del origen de eventos. Este nombre debe ser único dentro del entorno de Time Series Insights.
| Origen | Seleccione **Centro de eventos** para crear un origen de eventos del centro de eventos
| Id. de suscripción | Seleccione la suscripción en la que se creó el centro de eventos.
| Espacio de nombres de Service bus | Seleccione el espacio de nombres de Service Bus que contiene el centro de eventos.
| Nombre del centro de eventos | Seleccione el nombre del centro de eventos.
| Nombre de la directiva del centro de eventos | Seleccione directiva de acceso compartido, que se puede crear en la pestaña Configuración de centro de eventos. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. La directiva de acceso compartido para el origen de eventos *debe* tener permisos de **lectura**.
| Grupo de consumidores de centro de eventos | El grupo de consumidores para leer eventos desde el centro de eventos. Se recomienda fehacientemente usar un grupo de consumidores dedicado para el origen del evento.

### <a name="provide-event-hub-settings-manually"></a>Proporcionar configuración del centro de eventos de forma manual

La siguiente tabla explica cada propiedad en la pestaña Nuevo origen de eventos con su descripción al escribir los ajustes manualmente:

| Nombre de propiedad | Description |
| --- | --- |
| Nombre de origen de eventos | El nombre del origen de eventos. Este nombre debe ser único dentro del entorno de Time Series Insights.
| Origen | Seleccione **Centro de eventos** para crear un origen de eventos del centro de eventos
| Id. de suscripción | La suscripción en la que se creó este centro de eventos.
| Grupos de recursos | La suscripción en la que se creó este centro de eventos.
| Espacio de nombres de Service bus | Un espacio de nombres del bus de servicio es un contenedor para un conjunto de entidades de mensajería. Al crear un nuevo centro de eventos, también se crea un espacio de nombres del Bus de servicio.
| Nombre del centro de eventos | El nombre del centro de eventos. Cuando creó el centro de eventos, también le asignó un nombre específico.
| Nombre de la directiva del centro de eventos | La directiva de acceso compartido, que se puede crear en la pestaña Configuración de Centro de eventos. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. La directiva de acceso compartido para el origen de eventos *debe* tener permisos de **lectura**.
| Clave de la directiva de centro de eventos | La clave de acceso compartido usada para autenticar el acceso al espacio de nombres del Bus de servicio. Escriba la clave principal o secundaria aquí.
| Grupo de consumidores de centro de eventos | El grupo de consumidores para leer eventos desde el centro de eventos. Se recomienda fehacientemente usar un grupo de consumidores dedicado para el origen del evento.

## <a name="next-steps"></a>Pasos siguientes

1. Adición de un directiva de acceso de datos al entorno [Definir directivas de acceso de datos](time-series-insights-data-access.md)
1. Acceso al entorno en el [Portal de Time Series Insights](https://insights.timeseries.azure.com)
