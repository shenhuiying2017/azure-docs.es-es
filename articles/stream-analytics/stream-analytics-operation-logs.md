<properties 
	pageTitle="Registros de operaciones | Microsoft Azure" 
	description="Registros de operaciones del Análisis de transmisiones" 
	keywords="big data analytics,cloud service,internet of things,managed service,stream processing,streaming analytics,streaming data"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/09/2015" 
	ms.author="jeffstok"/>

# Registros de operaciones

Todos los servicios de Azure ofrecen mensajes de registro operativo a los usuarios para registrar los detalles relacionados con las operaciones de administración. En el Análisis de transmisiones de Azure, esta información se puede usar para depuración, como ver el estado del trabajo, el progreso del trabajo y los mensajes de error para realizar el seguimiento del progreso de un trabajo con el tiempo, desde el inicio hasta el procesamiento y la salida.

## Búsqueda de registros de operaciones

A los registros de operaciones se puede acceder de dos maneras:

- Panel del trabajo del Análisis de transmisiones  
- Servicios de administración en el Portal de Azure  

## Panel del trabajo del Análisis de transmisiones

Se muestra un vínculo a los registros correspondientes de un trabajo del Análisis de transmisiones en la pestaña Panel del trabajo. Si hace clic en ese vínculo, establecerá los filtros de una manera que muestre los registros más recientes para ese trabajo específico.

  ![Seleccionar los servicios de administración](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)

## Servicios de administración en el Portal de Azure

Para navegar de forma manual a los registros de operación para el Análisis de transmisiones y otros servicios en el Portal de Azure:

1.	Haga clic en **Servicios de administración** en el [Portal de Azure](https://manage.windowsazure.com).
2.	Seleccione **Análisis de transmisiones** para el **Tipo** y el nombre del trabajo para el **Nombre de servicio**.  

    ![Seleccionar el Análisis de transmisiones](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)

## Usar registros de operaciones

Puede filtrar por intervalo de tiempo y estado para ver los registros para su trabajo.

Haga clic en el botón **Detalles** situado en la parte inferior de la ventana para ver más detalles sobre un evento seleccionado.

  ![Seleccionar detalles](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)

## Depuración de un trabajo con error

Haga clic en el icono de búsqueda y escriba "Con error". Esto genera todos los registros con errores.

  ![Depuración de un trabajo con error](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)

Puede seleccionar cualquiera de los errores y hacer clic en **Detalles** para obtener más información sobre el error. Algunos mensajes de error también ofrecen información acerca de cómo mitigar el problema.

  ![Detalles de la operación](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)

En caso de que necesite ponerse en contacto con [Soporte](http://azure.microsoft.com/support/options/) u ofrecer información al equipo a través del [foro de MSDN](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics), tenga en cuenta los detalles de la operación, específicamente el **Id. de correlación**.

## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->