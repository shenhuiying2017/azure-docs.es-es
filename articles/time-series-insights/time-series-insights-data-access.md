---
title: Directivas de acceso a datos de Azure Time Series Insights | Microsoft Docs
description: "En este tutorial, aprenderá a administrar las directivas de acceso de datos de Time Series Insights"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: omravi
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: e975c6d8f217bc73948c0c046204b16b1a742bc7
ms.contentlocale: es-es
ms.lasthandoff: 05/20/2017

---

# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Concesión de acceso a los datos de un entorno de Time Series Insights mediante Azure Portal

Los entornos de Time Series Insights tienen dos tipos independientes de directivas de acceso:

* Directivas de acceso de administración
* Directivas de acceso a datos

Ambas directivas conceden a las entidades de Azure Active Directory (usuarios y aplicaciones) distintos permisos en un entorno concreto. Las entidades (usuarios y aplicaciones) deben pertenecer al directorio activo (o "inquilino de Azure") asociado a la suscripción que contiene el entorno.

Las directivas de acceso de administración conceden permisos relacionados con la configuración del entorno, tales como:
*   La creación y eliminación del entorno, orígenes de eventos, conjuntos de datos de referencia.
*   Administración de las directivas de acceso a datos.

Las directivas de acceso a datos conceden permisos para emitir consultas de datos, manipular datos de referencia en el entorno y compartir consultas guardadas y perspectivas asociadas con el entorno.

Los dos tipos de directivas permiten una separación clara entre el acceso a la administración del entorno y el acceso a los datos dentro del entorno. Por ejemplo, es posible configurar un entorno de modo que el propietario o creador del entorno no disponga de acceso a los datos. Del mismo modo que se permite a los usuarios y servicios leer los datos del entorno, podrían no tener acceso a la configuración del entorno.

## <a name="grant-data-access"></a>Concesión de acceso a datos
Los pasos siguientes muestran cómo conceder acceso a datos a una entidad de usuario:

1.  Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2.  Haga clic en "Todos los recursos" en el menú izquierdo de Azure Portal.
3.  Seleccione el entorno de Time Series Insights.

  ![Administración del origen de Time Series Insights: entorno](media/data-access/getstarted-grant-data-access1.png)

4.  Seleccione “Acceso a plano de datos” y haga clic en “Agregar”.

  ![Administración del origen de Time Series Insights: agregar](media/data-access/getstarted-grant-data-access2.png)

5.  Haga clic en "Seleccionar usuario".
6.  Busque y seleccione el usuario por correo electrónico.
7.  Haga clic en "Seleccionar" en la hoja "Seleccionar usuarios".

  ![Administración del origen de Time Series Insights: seleccionar usuario](media/data-access/getstarted-grant-data-access3.png)

8.  Haga clic en "Seleccionar rol".
9.  Seleccione "Colaborador" si desea permitir al usuario cambiar los datos de referencia y compartir las consultas almacenadas y las perspectivas con otros usuarios del entorno. En caso contrario, seleccione "Lector" para permitir al usuario consultar los datos del entorno y guardar consultas personales (no compartidas) en el entorno.
10. Haga clic en "Aceptar" en la hoja "Seleccionar rol".

  ![Administración del origen de Time Series Insights: seleccionar rol](media/data-access/getstarted-grant-data-access4.png)

11. Haga clic en "Aceptar" en la hoja "Seleccionar rol de usuario".
12. Debería ver lo siguiente:

  ![Administración del origen de Time Series Insights: resultados](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Pasos siguientes

* [Creación de un origen de eventos](time-series-insights-add-event-source.md)
* [Envío de eventos](time-series-insights-send-events.md) al origen de eventos
* Vea el entorno en el [portal de Time Series Insights](https://insights.timeseries.azure.com)

