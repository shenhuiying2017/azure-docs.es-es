<properties 
	pageTitle="Ejecutar un trabajo | Microsoft Azure" 
	description="Ejecute un segmento de ruta de aprendizaje de trabajo."
	documentationCenter=""
	services="stream-analytics"
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

# Ejecutar un trabajo

Cuando se hayan especificado una entrada, una consulta y una salida de trabajo, podrá iniciar el trabajo del Análisis de transmisiones.

Para iniciar su trabajo:

1.	Desde el panel del trabajo, haga clic en Iniciar en la parte inferior de la página.

    ![Botón Iniciar](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)

2.	En el cuadro de diálogo que aparece, especifique un valor de **Iniciar salida** para determinar cuándo comenzará este trabajo a generar una salida. La configuración predeterminada para los trabajos que no se han iniciado anteriormente es **Hora de inicio del trabajo**, lo que significa que el trabajo iniciará inmediatamente el procesamiento de datos. También puede especificar un tiempo **Personalizado** en el pasado (para consumir datos históricos) o en el futuro (para retrasar el procesamiento hasta una fecha futura). En los casos en que un trabajo se ha iniciado y detenido anteriormente, la opción **Hora de la última detención** está disponible para reanudar el trabajo desde la última hora de salida y evitar la pérdida de datos.

    ![Hora de inicio](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)

3.	Haga clic en el botón de comprobación en la parte inferior del cuadro de diálogo. El estado del trabajo cambiará a Iniciando y en breve pasará a En ejecución cuando se haya iniciado el trabajo. Puede supervisar el progreso de la operación de inicio en el centro de notificaciones:

    ![Progreso](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)

## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->