---
title: "Configuración de la seguridad para acceder a Time Series Insights y administrarlo | Microsoft Docs"
description: "En este artículo se describe cómo configurar la seguridad y los permisos como directivas de acceso de administración y directivas de acceso a datos para proteger Azure Time Series Insights."
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
ms.openlocfilehash: c7d4079c9106226e0d07aa97c4a52c16ddb257c3
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Concesión de acceso a los datos de un entorno de Time Series Insights mediante Azure Portal

Los entornos de Time Series Insights tienen dos tipos independientes de directivas de acceso:

* Directivas de acceso de administración
* Directivas de acceso a datos

Ambas directivas conceden a las entidades de Azure Active Directory (usuarios y aplicaciones) distintos permisos en un entorno concreto. Las entidades de seguridad (usuarios y aplicaciones) deben pertenecer al directorio activo (conocido como inquilino de Azure) asociado a la suscripción que contiene el entorno.

Las directivas de acceso de administración conceden permisos relacionados con la configuración del entorno, tales como:
*   La creación y eliminación del entorno, orígenes de eventos, conjuntos de datos de referencia.
*   Administración de las directivas de acceso a datos.

Las directivas de acceso a datos conceden permisos para emitir consultas de datos, manipular datos de referencia en el entorno y compartir consultas guardadas y perspectivas asociadas con el entorno.

Los dos tipos de directivas permiten una separación clara entre el acceso a la administración del entorno y el acceso a los datos dentro del entorno. Por ejemplo, es posible configurar un entorno de modo que su propietario o creador no disponga de acceso a los datos. Además, los usuarios y servicios que tienen permiso para leer los datos del entorno, podrían no tener acceso a la configuración del entorno.

## <a name="grant-data-access"></a>Concesión de acceso a datos
Siga estos pasos para conceder acceso a datos a una entidad de seguridad de usuario:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Busque el entorno de Time Series Insights. Escriba **Time Series** en el cuadro de **búsqueda**. Seleccione **Entorno de Time Series** en los resultados de búsqueda. 

3. Seleccione el entorno de Time Series Insights de la lista.
   
4. Seleccione **Data Access Policies** (Directivas de acceso a datos) y, luego, **+ Agregar**.
  ![Administración del entorno del origen de Time Series Insights: entorno](media/data-access/getstarted-grant-data-access1.png)

5. Seleccione **Seleccionar usuario**.  Busque el nombre de usuario o la dirección de correo para encontrar al usuario que quiere agregar. Haga clic en **Seleccionar** para confirmar la selección. 

   ![Administración del origen de Time Series Insights: agregar](media/data-access/getstarted-grant-data-access2.png)

6. Seleccione **Seleccionar rol**. Elija el rol de acceso adecuado para el usuario:
   - Seleccione **Colaborador** si desea permitir al usuario cambiar los datos de referencia y compartir las consultas almacenadas y las perspectivas con otros usuarios del entorno. 
   - En caso contrario, seleccione **Lector** para permitir al usuario consultar los datos del entorno y guardar consultas personales (no compartidas) en el entorno.

   Seleccione **Aceptar** para confirmar la elección del rol.

   ![Administración del origen de Time Series Insights: seleccionar usuario](media/data-access/getstarted-grant-data-access3.png)

8. Seleccione **Aceptar** en la página **Select User Role** (Seleccionar rol de usuario).

   ![Administración del origen de Time Series Insights: seleccionar rol](media/data-access/getstarted-grant-data-access4.png)

9. En la página **Data Access Policies** (Directivas de acceso a datos) se muestran los usuarios y los roles de cada uno.

   ![Administración del origen de Time Series Insights: resultados](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>pasos siguientes
* Aprenda a [agregar un origen del evento del centro de eventos al entorno de Azure Time Series Insights](time-series-insights-how-to-add-an-event-source-eventhub.md).
* Realice el [envío de eventos](time-series-insights-send-events.md) al origen del evento.
* Vea el entorno en el [explorador de Time Series Insights](https://insights.timeseries.azure.com).
