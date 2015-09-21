<properties 
	pageTitle="Agregar salidas | Microsoft Azure" 
	description="Agregue el segmento de la ruta de aprendizaje de salidas."
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

# Agregar salidas

Los trabajos de Análisis de transmisiones de Azure pueden estar conectados a una o más salidas, que definen una conexión a un receptor de datos existente. Conforme el trabajo del Análisis de transmisiones se procesa y transforma los datos entrantes, se escribe una secuencia de eventos de salida en la salida de su trabajo.

Las salidas del Análisis de transmisiones se pueden usar para generar alertas o paneles en tiempo real, desencadenar flujos de trabajo de bajada o simplemente archivar datos para el procesamiento por lotes más adelante. El Análisis de transmisiones tiene una integración de primera clase con varios servicios de Azure, que se documentan aquí con detalle.

Para agregar una salida a su trabajo del Análisis de transmisiones:

1. En su trabajo de Análisis de transmisiones, haga clic en **Salidas** y luego en **Agregar salida**.

    ![Agregar salidas](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)

2. Especifique el tipo de la salida:

    ![Elegir tipo de datos](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)

3. Asigne un nombre descriptivo para esta salida en el cuadro **Alias de salida**. Este nombre se puede usar en la consulta de su trabajo más adelante para hacer referencia a la salida.
    
    Rellene el resto de las propiedades de conexión necesarias para conectarse a su salida. Estos campos varían según el tipo de salida y se definen en detalle aquí.

    ![Agregar propiedades](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)

4. Según el tipo de salida, puede que necesite especificar cómo se serializan o se formatean los datos. La configuración de serialización específica para cada tipo de salida se documenta aquí.

    Rellene el resto de las propiedades de conexión necesarias para conectarse a su origen de datos. Estos campos varían según el tipo de entrada y de origen y se definen en detalle [aquí](stream-analytics-create-a-job.md.).

    ![Agregar centro de eventos](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)

## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->