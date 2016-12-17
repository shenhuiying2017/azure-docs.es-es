---
title: "Depuración mediante registros de operaciones y servicios de Stream Analytics | Microsoft Docs"
description: "Uso de registros de operaciones de Análisis de transmisiones"
keywords: registros de servicios
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: a2ed9676-f0bd-4398-87c8-a592779ac728
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 09/26/2016
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e694eb8f4d658ff9410ae7be2bd4b69500ecbf20


---
# <a name="debug-stream-analytics-jobs-using-service-and-operation-logs"></a>Depuración de los trabajos de Análisis de transmisiones mediante registros de operaciones y servicios
Todos los servicios de Azure ofrecen mensajes de registro operativo a los usuarios para registrar los detalles relacionados con las operaciones de administración. En el Análisis de transmisiones de Azure, esta información se puede usar para depuración, como ver el estado del trabajo, el progreso del trabajo y los mensajes de error para realizar el seguimiento del progreso de un trabajo con el tiempo, desde el inicio hasta el procesamiento y la salida.

## <a name="find-operation-logs-in-the-azure-management-portal"></a>Búsqueda de registros de operaciones en el Portal de administración de Azure
A los registros de operaciones se puede acceder de dos maneras:  

* Panel del trabajo del Análisis de transmisiones  
* Servicios de administración en el Portal de Azure clásico  

## <a name="dashboard-of-the-stream-analytics-job"></a>Panel del trabajo del Análisis de transmisiones
Se muestra un vínculo a los registros correspondientes de un trabajo del Análisis de transmisiones en la pestaña Panel del trabajo. Si hace clic en ese vínculo, establecerá los filtros de una manera que muestre los registros más recientes para ese trabajo específico.

  ![Selección de registros de servicios de administración](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)  

## <a name="management-services"></a>Servicios de administración
Para navegar de forma manual a los registros de operación para Stream Analytics y otros servicios en el Portal de Azure clásico:

1. Haga clic en **Servicios de administración** en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Seleccione **Stream Analytics** en **Tipo** y el nombre del trabajo en **Nombre de servicio**.  
   
   ![Seleccionar el Análisis de transmisiones](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)  

## <a name="find-audit-logs-in-the-azure-portal"></a>Búsqueda de registros de auditoría en Azure Portal
Para encontrar registros operativos para el trabajo de Stream Analytics en Azure Portal, haga clic en **Examinar** y seleccione **Registros de auditoría**.

  ![Selección de Stream Analytics en Azure Portal](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)  

Se abrirá una hoja que muestra los eventos de los últimos 7 días para todos los recursos de su suscripción.  Puede filtrar para ver eventos de un tipo específico o un intervalo de tiempo haciendo clic en el comando **Filtro** .

  ![Selección de Stream Analytics en Azure Portal](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)  

## <a name="get-log-details"></a>Obtención de detalles de registro
Puede filtrar por intervalo de tiempo y estado para ver los registros para su trabajo.

En el Portal de administración de Azure, haga clic en el botón **Detalles** situado en la parte inferior de la ventana para ver más detalles sobre un evento seleccionado. 

  ![Seleccionar detalles](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)  

En Azure Portal, haga clic en una entrada del registro para ver los eventos detallados dentro.

  ![Selección de detalles en Azure Portal](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)  

Desde allí, puede abrir la hoja **Detalle** haciendo clic en el evento.

  ![Selección de detalles en Azure Portal](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)  

## <a name="debug-a-failed-job"></a>Depuración de un trabajo con error
En el Portal de administración de Azure, haga clic en el icono Buscar y escriba ‘error’. Esto genera todos los registros con errores. 

  ![Depuración de un trabajo con error](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)  

En Azure Portal, puede filtrar por nivel de mensaje para ver eventos de tipo **Crítico** .

  ![Depuración en Azure Portal](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)  

Puede seleccionar cualquiera de los errores y hacer clic en **Detalles** para obtener más información sobre el error.  Algunos mensajes de error también ofrecen información acerca de cómo mitigar el problema. 

  ![Detalles de la operación](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)  

Si necesita ponerse en contacto con [Soporte](https://azure.microsoft.com/support/options/) u ofrecer información al equipo a través del [foro de MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics), anote los detalles de la operación, específicamente el **id. de correlación**. 

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Nov16_HO3-->


