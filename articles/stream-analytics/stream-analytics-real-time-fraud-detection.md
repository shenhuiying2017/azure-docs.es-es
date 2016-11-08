---
title: 'Análisis de transmisiones: detección de fraudes en tiempo real | Microsoft Docs'
description: Aprenda a crear una solución para la detección de fraudes en tiempo real con Análisis de transmisiones. Use un centro de eventos para el procesamiento de eventos en tiempo real.
keywords: detección de anomalías, detección de fraudes, detección de anomalías en tiempo real
services: stream-analytics
documentationcenter: ''
author: jeffstokes72
manager: jhubbard
editor: cgronlun

ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 09/26/2016
ms.author: jeffstok

---
# <a name="get-started-using-azure-stream-analytics:-real-time-fraud-detection"></a>Introducción al uso de Análisis de transmisiones de Azure: detección de fraudes en tiempo real
Aprenda a crear una solución de extremo a extremo para la detección de fraudes en tiempo real con Análisis de transmisiones de Azure. Lleve eventos a Centros de eventos de Azure, escriba consultas de Stream Analytics para agregación o alertas y envíe los resultados a un receptor de salida para obtener información detallada sobre los datos con procesamiento en tiempo real. Se explica la detección de anomalías en tiempo real para telecomunicaciones, pero la técnica de ejemplo es igualmente adecuada para otros tipos de detección de fraudes, tales como los escenarios de robo de la tarjeta de crédito o de identidad.

Stream Analytics es un servicio totalmente administrado que proporciona un procesamiento complejo de eventos de baja latencia, alta disponibilidad y escalable a través de la transmisión de datos en la nube. Para obtener más información, consulte [Introducción a Análisis de transmisiones de Azure](stream-analytics-introduction.md).

## <a name="scenario:-telecommunications-and-sim-fraud-detection-in-real-time"></a>Escenario: telecomunicaciones y detección de fraudes de SIM en tiempo real
Una empresa de telecomunicaciones tiene un gran volumen de datos en llamadas entrantes. La compañía necesita lo siguiente para sus datos:

* Reducir los datos hasta un volumen manejable y obtener información detallada sobre el uso del cliente por tiempo y a través de regiones geográficas.
* Detectar el fraude de SIM (varias llamadas de la misma identidad aproximadamente al mismo tiempo, pero en ubicaciones geográficamente diferentes) en tiempo real para que la empresa pueda responder fácilmente notificando a los clientes o cerrando el servicio.

Los escenarios de Internet de las cosas (IoT) de Canonical tienen una gran cantidad de telemetría o datos de sensores. Los clientes desean agregar los datos o recibir alertas sobre anomalías en tiempo real.

## <a name="prerequisites"></a>Requisitos previos
* Descargue [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) del Centro de descarga de Microsoft.
* Opcional: código fuente del generador de eventos desde [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator)

## <a name="create-azure-event-hubs-input-and-consumer-group"></a>Creación de un grupo de consumidores y una entrada de Azure Event Hubs
La aplicación de ejemplo generará eventos y los insertará en una instancia de Centros de eventos para el procesamiento en tiempo real. Los centros de eventos de Bus de servicio son el método preferido de recopilación de eventos para Análisis de transmisiones. Puede obtener más información sobre Centros de eventos en [documentación de Azure Service Bus](/documentation/services/service-bus/).

Para crear un Centro de eventos:

1. En [Azure Portal](https://manage.windowsazure.com/), haga clic en **NUEVO** > **APP SERVICES** > **SERVICE BUS** > **CENTRO DE EVENTOS** > **CREACIÓN RÁPIDA**. Indique un nombre, una región y un espacio de nombres nuevo o existente para crear un Centro de eventos.  
2. Como práctica recomendada, cada trabajo de Stream Analytics debe leer en un solo grupo de consumidores de Centro de eventos. Le guiaremos a través del proceso de creación de un grupo de consumidores más adelante. [Obtenga más información sobre los grupos de consumidores](https://msdn.microsoft.com/library/azure/dn836025.aspx). Para crear un grupo de consumidores, vaya al Centro de eventos recién creado, haga clic en la pestaña **GRUPOS DE CONSUMIDORES** y en **CREAR** en la parte inferior de la página y proporcione un nombre para el grupo de consumidores.
3. Para otorgar acceso al Centro de eventos, necesitamos crear una directiva de acceso compartido. Haga clic en la pestaña **CONFIGURAR** de su Centro de eventos.
4. En **DIRECTIVAS DE ACCESO COMPARTIDO**, cree una nueva directiva con permisos para **ADMINISTRAR**.
   
   ![Directivas de acceso compartido en las que puede crear una nueva directiva con permisos para Administrar.](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-shared-access-policies.png)
5. Haga clic en **GUARDAR** en la parte inferior de la página.
6. Vaya al **Panel**, haga clic en **INFORMACIÓN DE CONEXIÓN** en la parte inferior de la página y, a continuación, copie y guarde la información de conexión.

## <a name="configure-and-start-the-event-generator-application"></a>Configuración e inicio de la aplicación del generador de eventos
Proporcionamos una aplicación cliente que generará los metadatos de llamada entrante de ejemplo y los insertará en Centros de eventos. Siga los pasos siguientes para configurar esta aplicación.  

1. Descargue el [archivo TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) y descomprímalo en un directorio.
   
   > [!NOTE]
   > Windows puede bloquear el archivo zip descargado. Haga clic con el botón derecho en el archivo y seleccione **Propiedades**. Si ve el mensaje "Este archivo proviene de otro equipo y podría bloquearse para ayudar a proteger este equipo", active la casilla **Desbloquear** y, a continuación, haga clic en Aplicar en el archivo .zip.
   > 
   > 
2. Reemplace los valores Microsoft.ServiceBus.ConnectionString y EventHubName en el archivo telcodatagen.exe.config por la cadena de conexión y el nombre del Centro de eventos.
   
   La cadena de conexión copiada de Azure Portal coloca el nombre de la conexión al final. Asegúrese de quitar ";EntityPath=<value>" del campo "add key=".
3. Inicie la aplicación. El uso es como sigue:
   
   telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]

En el ejemplo siguiente, se generarán 1000 eventos con una probabilidad de fraude del 20 % en el transcurso de dos horas.

    telcodatagen.exe 1000 .2 2

Podrá ver los registros que se envían al Centro de eventos. Aquí se definen algunos campos clave que se van a usar en esta aplicación de detección de fraudes en tiempo real:

| Registro | Definición |
| --- | --- |
| CallrecTime |Marca de tiempo para la hora de inicio de la llamada. |
| SwitchNum |Conmutador de teléfono que se usa para conectar la llamada. |
| CallingNum |Número de teléfono del autor de la llamada. |
| CallingIMSI |Identidad del suscriptor móvil internacional (IMSI).  Identificador único del autor de la llamada. |
| CalledNum |Número de teléfono del destinatario de la llamada. |
| CalledIMSI |Identidad del suscriptor móvil internacional (IMSI).  Identificador único del destinatario de la llamada. |

## <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Análisis de transmisiones
Ahora que tenemos un streaming de eventos de telecomunicaciones, podemos configurar un trabajo de Análisis de transmisiones para analizar estos eventos en tiempo real.

### <a name="provision-a-stream-analytics-job"></a>Aprovisionamiento de un trabajo de Stream Analytics
1. En Azure Portal, haga clic en **NUEVO** > **DATA SERVICES** > **STREAM ANALYTICS** > **CREACIÓN RÁPIDA**.
2. Especifique los valores siguientes y, a continuación, haga clic en **CREAR TRABAJO DE ANÁLISIS DE TRANSMISIONES**:
   
   * **NOMBRE DEL TRABAJO**: escriba un nombre del trabajo.
   * **REGIÓN**: seleccione la región donde desea ejecutar el trabajo. Considere la posibilidad de colocar el trabajo y el centro de eventos en la misma región para garantizar un mejor rendimiento y asegurarse de no pagar la transferencia de datos entre regiones.
   * **CUENTA DE ALMACENAMIENTO**: elija la cuenta de almacenamiento de Azure que desea usar para almacenar los datos de supervisión de todos los trabajos de Stream Analytics que se ejecutan en esta región. Tiene la opción de elegir una cuenta de almacenamiento existente o crear una nueva.
3. Haga clic en **ANÁLISIS DE TRANSMISIONES** en el panel izquierdo para ver una lista de los trabajos de Análisis de transmisiones.
   
   ![Icono de servicio de Análisis de transmisiones](./media/stream-analytics-real-time-fraud-detection/stream-analytics-service-icon.png)
   
   Se mostrará el nuevo trabajo con el estado **CREADO**. Tenga en cuenta que el botón **INICIO** en la parte inferior de la página está deshabilitado. Debe configurar la entrada, la salida y la consulta del trabajo antes de iniciar el trabajo.

### <a name="specify-job-input"></a>Especificación de la entrada del trabajo
1. En su trabajo de Stream Analytics, haga clic en **ENTRADAS** en la parte superior de la página y, a continuación, en **AGREGAR ENTRADA**. El cuadro de diálogo que se abre le guiará a través de varios pasos para configurar la entrada.
2. Haga clic en **FLUJO DE DATOS** y, a continuación, haga clic con el botón derecho.
3. Haga clic en **CENTRO DE EVENTOS** y, a continuación, haga clic con el botón derecho.
4. Escriba o seleccione los valores siguientes en la tercera página:
   
   * **ALIAS DE ENTRADA**: escriba un nombre descriptivo, como *CallStream*, para este trabajo. Tenga en cuenta que va a utilizar este nombre en la consulta más adelante.
   * **CENTRO DE EVENTOS**: si el Centro de eventos que creó está en la misma suscripción que el trabajo de Stream Analytics, seleccione el espacio de nombres en el que está el Centro de eventos.
     
       Si el Centro de eventos está en otra suscripción, seleccione **Usar centro de eventos de otra suscripción** y escriba manualmente la información de **ESPACIO DE NOMBRES DE SERVICE BUS**, **NOMBRE DE CENTRO DE EVENTOS**, **NOMBRE DE DIRECTIVA DE CENTRO DE EVENTOS**, **CLAVE DE DIRECTIVA DE CENTRO DE EVENTOS** y **RECUENTO DE PARTICIONES DE CENTRO DE EVENTOS**.
   * **NOMBRE DE CENTRO DE EVENTOS**: seleccione el nombre del Centro de eventos.
   * **NOMBRE DE DIRECTIVA DE CENTRO DE EVENTOS**: seleccione la directiva del Centro de eventos que creó anteriormente en este tutorial.
   * **GRUPO DE CONSUMIDORES DEL CENTRO DE EVENTOS**: escriba el nombre del grupo de consumidores que creó anteriormente en este tutorial.
5. Haga clic con el botón derecho.
6. Especifique los siguientes valores:
   
   * **FORMATO DEL SERIALIZADOR DE EVENTOS**: JSON
   * **CODIFICACIÓN**: UTF8
7. Haga clic en el botón de **COMPROBACIÓN** para agregar este origen y comprobar que Stream Analytics puede conectarse correctamente al Centro de eventos.

### <a name="specify-job-query"></a>Especificación de la consulta de trabajo
Stream Analytics admite un modelo de consulta declarativo sencillo que describe las transformaciones para el procesamiento en tiempo real. Para obtener más información sobre el lenguaje, consulte la [Referencia de lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/dn834998.aspx). Este tutorial le ayudará a crear y probar varias consultas sobre el streaming de datos de llamada en tiempo real.

#### <a name="optional:-sample-input-data"></a>Opcional: Datos de entrada de ejemplo
Para validar la consulta con datos de trabajo reales, puede utilizar la característica **DATOS DE EJEMPLO** para extraer los eventos del flujo y crear un archivo .JSON de los eventos para las pruebas.  Se muestra cómo hacerlo en los pasos siguientes. También hemos proporcionado un archivo de ejemplo [Telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) con fines de prueba.

1. Seleccione la entrada del Centro de eventos y, a continuación, haga clic en **DATOS DE EJEMPLO** en la parte inferior de la página.
2. En el cuadro de diálogo que aparece, especifique una **HORA DE INICIO** para empezar a recopilar datos y una **DURACIÓN** para indicar la cantidad de datos adicionales que se consumirá.
3. Haga clic en el botón de **COMPROBACIÓN** para empezar a muestrear datos de la entrada.  El archivo de datos puede tardar un minuto o dos en crearse.  Cuando finalice el proceso, haga clic en **DETALLES**, descargue el archivo .JSON generado y guárdelo.
   
   ![Descargar y guardar datos procesados en un archivo JSON](./media/stream-analytics-real-time-fraud-detection/stream-analytics-download-save-json-file.png)

#### <a name="pass-through-query"></a>Consulta de paso a través
Si desea archivar todos los eventos, puede usar una consulta de paso a través para leer todos los campos de la carga útil del evento o mensaje. Para empezar, haga una consulta de paso a través simple que proyecte todos los campos de un evento.

1. Haga clic en **CONSULTA** , en la parte superior de la página de trabajo de Análisis de transmisiones.
2. Agregue lo siguiente al editor de código:
   
       SELECT * FROM CallStream
   
   > [!IMPORTANT]
   > Asegúrese de que el nombre del origen de entrada coincida con el nombre de la entrada que especificó anteriormente.
   > 
   > 
3. Haga clic en **Prueba** en el editor de consultas.
4. Proporcione un archivo de prueba. Use uno creado siguiendo los pasos anteriores, o bien [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/SampleDataFiles/Telco.json).
5. Haga clic en el botón de **COMPROBACIÓN** y vea los resultados que aparecen debajo de la definición de consulta.
   
   ![Resultados de la definición de consulta](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sim-fraud-output.png)

### <a name="column-projection"></a>Proyección de columna
Ahora reduciremos los campos devueltos a un conjunto más pequeño.

1. Cambie la consulta en el editor de código a:
   
       SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
       FROM CallStream
2. Haga clic en **Volver a ejecutar** en el editor de consultas para ver los resultados de la consulta.
   
   ![Salida en el editor de consultas.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region:-tumbling-window-with-aggregation"></a>Recuento de llamadas entrantes por región: ventana de saltos de tamaño constante con agregación
Para comparar el número de llamadas entrantes por región, usaremos una [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obtener el recuento de llamadas entrantes agrupado por **SwitchNum** cada cinco segundos.

1. Cambie la consulta en el editor de código a:
   
       SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
       FROM CallStream TIMESTAMP BY CallRecTime
       GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
   
   Esta consulta usa la palabra clave **Timestamp By** para especificar un campo de marca de tiempo en la carga que se usará en el cálculo temporal. Si no se ha especificado este campo, la operación de ventana se realizará usando el tiempo de cada evento llegado al Centro de eventos. Obtenga más información en "Tiempo de llegada frente a tiempo de aplicación" en la [Referencia de consulta de Análisis de transmisiones](https://msdn.microsoft.com/library/azure/dn834998.aspx).
   
   Tenga en cuenta que puede tener acceso a una marca de tiempo para el final de cada ventana con la propiedad **System.Timestamp** .
2. Haga clic en **Volver a ejecutar** en el editor de consultas para ver los resultados de la consulta.
   
   ![Resultados de la consulta para Timestand By](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>Detección de fraudes de SIM con una autocombinación
Para identificar un uso posiblemente fraudulento buscaremos las llamadas que se originan en el mismo usuario pero en diferentes ubicaciones en menos de cinco segundos.  Vamos a [combinar](https://msdn.microsoft.com/library/azure/dn835026.aspx) el streaming de eventos de llamada consigo mismo para buscar estos casos.

1. Cambie la consulta en el editor de código a:
   
       SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
       CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
       FROM CallStream CS1 TIMESTAMP BY CallRecTime
       JOIN CallStream CS2 TIMESTAMP BY CallRecTime
       ON CS1.CallingIMSI = CS2.CallingIMSI
       AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
       WHERE CS1.SwitchNum != CS2.SwitchNum
2. Haga clic en **Volver a ejecutar** en el editor de consultas para ver los resultados de la consulta.
   
   ![Resultados de la consulta de una unión](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Creación de receptores de salida
Ahora que hemos definido una secuencia de eventos, una entrada de Centro de eventos para introducir eventos y una consulta para realizar una transformación en la secuencia, el último paso es definir un receptor de salida para el trabajo. Escribiremos los eventos de comportamiento fraudulento en Azure Blob Storage.

Siga estos pasos para crear un contenedor para Blob Storage si aún no tiene ninguno.

1. Use una cuenta de almacenamiento existente o cree una nueva; para ello, haga clic en **NUEVO > DATA SERVICES > ALMACENAMIENTO > CREACIÓN RÁPIDA** y siga las instrucciones.
2. Seleccione la cuenta de almacenamiento, haga clic en **CONTENEDORES** en la parte superior de la página y luego haga clic en **AGREGAR**.
3. Especifique un **NOMBRE** para el contenedor y establezca su **ACCESO** en **Blob público**.

## <a name="specify-job-output"></a>Especificación de la salida del trabajo
1. En su trabajo de Stream Analytics, haga clic en **SALIDA** en la parte superior de la página y luego haga clic en **AGREGAR SALIDA**. El cuadro de diálogo que se abre le guiará a través de varios pasos para configurar la salida.
2. Haga clic en **BLOB STORAGE**y, a continuación, haga clic con el botón derecho.
3. Escriba o seleccione los valores siguientes en la tercera página:
   
   * **Alias de salida**: escriba un nombre descriptivo para esta salida de trabajo.
   * **SUSCRIPCIÓN**: si el almacenamiento Blob Storage que creó está en la misma suscripción que el trabajo de Stream Analytics, haga clic en **Usar la cuenta de almacenamiento de la suscripción actual**. Si el almacenamiento está en otra suscripción, haga clic en **Usar la cuenta de almacenamiento de otra suscripción** y especifique manualmente la información de **CUENTA DE ALMACENAMIENTO**, **CLAVE DE CUENTA DE ALMACENAMIENTO** y **CONTENEDOR**.
   * **Cuenta de almacenamiento**: seleccione el nombre de la cuenta de almacenamiento
   * **Contenedor**: seleccione el nombre del contenedor
   * **PREFIJO DE NOMBRE DE ARCHIVO**: escriba un prefijo de archivo que se usará al escribir la salida de blob.
4. Haga clic con el botón derecho.
5. Especifique los siguientes valores:
   
   * **FORMATO DEL SERIALIZADOR DE EVENTOS**: JSON
   * **CODIFICACIÓN**: UTF8
6. Haga clic en el botón de **COMPROBACIÓN** para agregar este origen y comprobar que Stream Analytics puede conectarse correctamente a la cuenta de almacenamiento.

## <a name="start-job-for-real-time-processing"></a>Inicio del trabajo para el procesamiento en tiempo real
Puesto que ya se han especificado la entrada, la consulta y la salida del trabajo, estamos preparados para iniciar el trabajo de Stream Analytics para la detección de fraudes en tiempo real.

1. Desde **Panel** del trabajo, haga clic en **INICIAR** en la parte inferior de la página.
2. En el cuadro de diálogo que se abre, haga clic en **HORA DE INICIO DEL TRABAJO** y luego haga clic en el botón de **COMPROBACIÓN** en la parte inferior del cuadro de diálogo. El estado del trabajo cambiará a **Iniciando** y en breve cambiará a **En ejecución**.

## <a name="view-fraud-detection-output"></a>Consulta de la salida de la detección de fraudes
Use una herramienta como [Explorador de Azure Storage](http://storageexplorer.com/) o [Explorador de Azure](http://www.cerebrata.com/products/azure-explorer/introduction) para ver eventos fraudulentos a medida que se escriben en la salida en tiempo real.  

![Detección de fraudes: eventos fraudulentos vistos en tiempo real](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Obtención de soporte técnico
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--HONumber=Oct16_HO2-->


