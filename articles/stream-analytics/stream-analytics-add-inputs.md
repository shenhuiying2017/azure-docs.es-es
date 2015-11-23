<properties 
	pageTitle="Agregar entradas | Microsoft Azure" 
	description="Agregar entradas a los trabajos de Análisis de transmisiones | segmento de ruta de aprendizaje."
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
	ms.date="11/06/2015" 
	ms.author="jeffstok"/>


# Agregar entradas

Los trabajos de Análisis de transmisiones de Azure pueden estar conectados a una o más entradas, que definen una conexión a un origen de datos existente. A medida que los datos se envían a ese origen de datos, el trabajo de Análisis de transmisiones los consume y los procesa en tiempo real. Análisis de transmisiones cuenta con integración de primera clase con [Centro de eventos de orígenes de Azure](http://azure.microsoft.com/services/event-hubs/) y [Almacenamiento de blobs de Azure](./storage/storage-dotnet-how-to-use-blobs.md) desde dentro y fuera de la suscripción del trabajo. Hay dos tipos de entradas en el análisis de secuencia: flujos de datos y datos de referencia.

- **Secuencias de datos**: los trabajos de Análisis de transmisiones deben incluir, al menos, una entrada de secuencia de datos para que el trabajo la consuma y transforme. Almacenamiento de blobs de Azure y Centros de eventos de Azure se admiten como orígenes de entrada de flujos de datos. Los Centros de eventos de Azure se usan para recopilar transmisiones de eventos desde dispositivos conectados, servicios y aplicaciones. El almacenamiento de blobs de Azure puede usarse como origen de entrada para la ingesta de datos en masa como secuencia.  
- **Datos de referencia**: Análisis de transmisiones admite un segundo tipo de entrada auxiliar denominada datos de referencia. A diferencia de los datos en movimiento, estos datos son estáticos o están desacelerando los cambios. Normalmente se usa para realizar búsquedas y correlaciones con secuencias de datos para crear un conjunto de datos más amplio. Almacenamiento de blobs de Azure es el único origen de entrada admitido actualmente para los datos de referencia.  

Para agregar una entrada a su trabajo de Análisis de transmisiones:

1. En el portal de Azure, haga clic en **Entradas** y luego en **Agregar una entrada** en el trabajo de Análisis de transmisiones.

    ![Agregar entradas](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)

    En el portal de vista previa de Azure, haga clic en el icono **Entradas** en el trabajo de Análisis de transmisiones.

    ![Agregar entradas en el Portal de vista previa de Azure](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)

2. Especifique el tipo de la entrada: **Flujo de datos** o **Datos de referencia**.

    ![Agregar datos](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)

    ![Agregar datos en el Portal de vista previa de Azure](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)

3. Si crea una entrada de Flujo de datos, especifique el tipo de origen para la entrada. Este paso se omite durante la creación de Datos de referencia ya que, en este momento, solo se admite el almacenamiento de blobs.

    ![Agregar secuencia de datos](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)

    ![Agregar flujo de datos en el portal de vista previa](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)

4. Asigne un nombre descriptivo para esta entrada en el cuadro Alias de entrada. Este nombre se usará en la consulta de su trabajo más adelante para hacer referencia a la entrada.

    Rellene el resto de las propiedades de conexión necesarias para conectarse a su origen de datos. Estos campos varían según el tipo de entrada y de origen y se definen en detalle [aquí](stream-analytics-create-a-job.md).

    ![Agregar centro de eventos](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)

5. Especifique la configuración de serialización para los datos de entrada:
	- Para asegurarse de que las consultas funcionan según lo previsto, especifique la información correspondiente en **Formato de serialización de eventos** para los datos entrantes. Los formatos de serialización compatibles son JSON, CSV y Avro.
	- Compruebe el valor de **Codificación** para los datos. Por el momento, UTF-8 es el único formato de codificación compatible.

    ![Configuración de la serialización de datos](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)

    ![Configuración de la serialización de datos - Portal de vista previa de Azure](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)

6. Después de completar la creación de la entrada, el Análisis de transmisiones comprobará que se puede conectar al origen de la entrada. Puede ver el estado de la operación de prueba de conexión en el centro de notificaciones.

    ![Probar conexión](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)

    ![Probar conexión - Portal de vista previa de Azure](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)

## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Nov15_HO3-->