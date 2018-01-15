---
title: "Creación de un entorno de Azure Time Series Insights | Microsoft Docs"
description: "Este artículo se describe cómo usar Azure Portal para crear un nuevo entorno de Time Series Insights."
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 20156432e17d5eca90779271bd18dc49fa988d7c
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Creación de un nuevo entorno de Time Series Insights en Azure Portal
En este artículo se describe cómo crear un nuevo entorno de Time Series Insights mediante Azure Portal.

Time Series Insights permite empezar a visualizar y consultar en cuestión de minutos los datos que fluyen en centros de Azure IoT Hub y Event Hubs, de modo que se pueden consultar grandes volúmenes de datos de series temporales de consulta en segundos.  Se diseñó para la escala de Internet de las cosas (IoT) y puede controlar terabytes de datos.

## <a name="steps-to-create-the-environment"></a>Pasos para crear el entorno
Siga estos pasos para crear un entorno:

1.  Inicie sesión en el [Azure Portal](https://portal.azure.com).

2.  Seleccione el botón **+ Nuevo**.

3.  Seleccione la categoría **Internet de las cosas** y elija **Time Series Insights**.

   ![Creación del entorno de Time Series Insights](media/time-series-insights-get-started/1-new-tsi.png)

4.  En la página **Time Series Insights**, seleccione **Crear**.

5. Rellene todos los campos obligatorios. La siguiente tabla explica cada parámetro:
   
   ![Creación del grupo de recursos de Time Series Insights](media/time-series-insights-get-started/2-create-tsi.png)
   
   Configuración|Valor sugerido|DESCRIPCIÓN
   ---|---|---
   Nombre del entorno | Un nombre único | Este nombre representará al entorno en el [explorador de Time Series Insights](https://insights.timeseries.azure.com).
   La suscripción | Su suscripción | Si tiene varias suscripciones, elija la suscripción que contiene el origen de eventos si es posible. Time Series Insights puede detectar automáticamente los recursos de Azure IoT Hub y Event Hubs existentes en la misma suscripción.
   Grupos de recursos | Cree uno o use uno existente | Un grupo de recursos es una colección de recursos de Azure que se usan juntos. Puede elegir un grupo de recursos existente, por ejemplo, la carpeta que contenga el centro de eventos o IoT Hub. También puede crear un disco si este recurso no está relacionado con los otros recursos.
   La ubicación | La más cercana al origen de su evento | Si es posible, elija la misma ubicación del centro de datos que contiene los datos de origen de eventos para evitar costos adicionales de ancho de banda entre regiones y zonas, y un aumento de la latencia cuando se mueven datos fuera de la región.
   Nivel de precios | S1 | Elija el rendimiento requerido. Para costos más bajos y una capacidad de inicio menor, seleccione S1.
   Capacity | 1 | La capacidad es el multiplicador que se aplica a la tasa de entrada, la capacidad de almacenamiento y el costo asociado con la SKU seleccionada.  Puede cambiar la capacidad de un entorno después de su creación. Para costos más bajo, seleccione una capacidad de 1. 
  
6. Marque **Anclar al panel** para acceder más fácilmente a su entorno de Time Series Insights en el futuro.

   ![Anclaje de Time Series Insights al panel](media/time-series-insights-get-started/3-pin-create.png)

7. Haga clic en **Crear** para comenzar el proceso de aprovisionamiento. Este proceso tardará unos minutos.

8. Seleccione el símbolo de **Notificaciones** (icono de campana) para supervisar el proceso de implementación.

   ![Visualización de notificaciones](media/time-series-insights-get-started/4-notifications.png)

Cuando la implementación se realiza correctamente, puede seleccionar **Ir al recurso** para configurar otras propiedades, establecer la seguridad con directivas de acceso de datos, agregar orígenes de eventos y otras acciones.

## <a name="next-steps"></a>pasos siguientes
* [Defina directivas de acceso de datos](time-series-insights-data-access.md) para proteger su entorno.
* [Agregue un origen de eventos de Event Hubs](time-series-insights-how-to-add-an-event-source-eventhub.md) al entorno de Azure Time Series Insights. 
* Realice el [envío de eventos](time-series-insights-send-events.md) al origen del evento.
* Vea el entorno en el [explorador de Time Series Insights](https://insights.timeseries.azure.com).
