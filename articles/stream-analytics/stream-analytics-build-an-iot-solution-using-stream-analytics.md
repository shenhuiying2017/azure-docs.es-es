---
title: Compilación de una solución de IoT con Azure Stream Analytics
description: Tutorial de introducción a la solución de IoT de Stream Analytics de un escenario de cabinas de peaje.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh, sngun
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: 80e287d09fdc5ab7157b9ee46bc830fd2db4d501
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
ms.locfileid: "30912279"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Compilación de una solución de IoT con Stream Analytics

## <a name="introduction"></a>Introducción
En esta solución, obtendrá información sobre cómo usar Azure Stream Analytics para obtener información de sus datos en tiempo real. Los desarrolladores pueden combinar fácilmente secuencias de datos, como secuencias de clic, registros y eventos generados por el dispositivo, con registros históricos o datos de referencia para obtener información empresarial. Azure Stream Analytics es un servicio de cálculo de transmisiones en tiempo real totalmente administrado y hospedado en Microsoft Azure que ofrece gran resistencia, baja latencia y escalabilidad, para permitirle ponerse a trabajar en cuestión de minutos.

Después de completar esta solución, podrá:

* Familiarizarse con el portal de Azure Stream Analytics.
* Configurar e implementar un trabajo de streaming.
* Señalar los problemas reales y solucionarlos usando el lenguaje de consulta de Stream Analytics.
* Desarrollar soluciones de streaming para los clientes usando el lenguaje de consulta de Stream Analytics.
* Usar la experiencia de supervisión y registro para solucionar problemas.

## <a name="prerequisites"></a>requisitos previos
Necesita cumplir con estos requisitos previos para completar esta solución:
* [Una suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Introducción al escenario: peajes
Una estación de peaje es un fenómeno común. Se encuentra en muchas autopistas, puentes y túneles de todo el mundo. Cada estación de peaje tiene varias cabinas. En cabinas manuales, se detiene y paga el peaje a un operador. En cabinas automatizadas, un sensor situado en la parte superior de cada cabina escanea una tarjeta RFID que está ubicada en el parabrisas del vehículo al pasar la cabina de peaje. Es fácil imaginar el paso de los vehículos a través de estos peajes como si fuera un flujo de eventos sobre los que se pueden realizar operaciones interesantes.

![Imagen de automóviles en cabinas de peaje](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Datos de entrada
Esta solución funciona con dos secuencias de datos. Los sensores instalados a la entrada y la salida de las estaciones de peaje producen la primera secuencia. La segunda secuencia es un conjunto de datos de búsqueda estática que tiene datos de registro del vehículo.

### <a name="entry-data-stream"></a>Flujo de datos de entrada
El flujo de datos de entrada contiene información sobre los vehículos que entran en las estaciones de peaje. Los eventos de datos de salida se transmiten a la cola de un Centro de eventos desde una instancia de Web App incluida en la misma aplicación.

| TollId | EntryTime | LicensePlate | Estado | Asegúrese | Modelo | VehicleType | VehicleWeight | Toll | Etiqueta |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |10-09-2014 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |10-09-2014 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |10-09-2014 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |10-09-2014 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |10-09-2014 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |10-09-2014 12:05:00.000 |CDE 1007 |NJ |Toyota |4 x 4 |1 |0 |6 |321987654 |

Breve descripción de las columnas:

| Columna | DESCRIPCIÓN |
| --- | --- |
| TollId |El identificador de la cabina de peaje que identifica de forma única una cabina de peaje. |
| EntryTime |Fecha y hora de entrada del vehículo en la cabina de peaje en UTC. |
| LicensePlate |Número de matrícula del vehículo. |
| Estado |Estado de los Estados Unidos. |
| Asegúrese |Fabricante del automóvil. |
| Modelo |Número de modelo de vehículo. |
| VehicleType |1 para los vehículos de pasajeros o 2 para vehículos comerciales. |
| WeightType |Peso del vehículo en toneladas; es 0 para vehículos de pasajeros. |
| Toll |Costo del peaje en USD. |
| Etiqueta |Etiqueta electrónica del vehículo que automatiza el pago; en blanco cuando el pago se realiza manualmente. |

### <a name="exit-data-stream"></a>Flujo de datos de salida
El flujo de datos de salida contiene información sobre los vehículos que salen de la estación de peaje. Los eventos de datos de salida se transmiten a la cola de un Centro de eventos desde una instancia de Web App incluida en la misma aplicación.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |10-09-2014 T12:03:00.0000000Z |JNB 7001 |
| 1 |10-09-2014 T12:03:00.0000000Z |YXZ 1001 |
| 3 |10-09-2014 T12:04:00.0000000Z |ABC 1004 |
| 2 |10-09-2014 T12:07:00.0000000Z |XYZ 1003 |
| 1 |10-09-2014 T12:08:00.0000000Z |BNJ 1007 |
| 2 |10-09-2014 T12:07:00.0000000Z |CDE 1007 |

Breve descripción de las columnas:

| Columna | DESCRIPCIÓN |
| --- | --- |
| TollId |El identificador de la cabina de peaje que identifica de forma única una cabina de peaje. |
| ExitTime |La fecha y hora de salida del vehículo de la cabina de peaje en UTC. |
| LicensePlate |Número de matrícula del vehículo. |

### <a name="commercial-vehicle-registration-data"></a>Datos de registro de vehículos comerciales
En la solución se usa una instantánea estática de una base de datos de registro de vehículos comerciales. Estos datos se guardan como archivo JSON en Azure Blob Storage, incluido en el ejemplo.

| LicensePlate | RegistrationId | Expirada |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Breve descripción de las columnas:

| Columna | DESCRIPCIÓN |
| --- | --- |
| LicensePlate |Número de matrícula del vehículo. |
| RegistrationId |Identificador de registro del vehículo. |
| Expirada |El estado de registro del vehículo: 0 si el registro de vehículo está activo, 1 si el registro ha caducado. |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configuración del entorno para Azure Stream Analytics
Para completar esta solución, necesita una suscripción de Microsoft Azure. Si no tiene una cuenta de Azure, puede [solicitar una versión de evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/).

Asegúrese de seguir los pasos de la sección "Limpieza de la cuenta de Azure" al final de este ejercicio para que pueda aprovechar al máximo su crédito de Azure.

## <a name="deploy-the-sample"></a>Implementación del ejemplo 
Existen varios recursos que se pueden implementar fácilmente en un grupo de recursos junto con algunos clics. La definición de la solución se hospeda en el repositorio de GitHub en [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp).

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Implementación de la plantilla TollApp en Azure Portal
1. Para implementar el entorno TollApp en Azure, use este vínculo para [implementar la plantilla de TollApp de Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Si se le solicita hacerlo, inicie sesión en Azure Portal.

3. Elija la suscripción en la que se facturan los distintos recursos.

4. Especifique un grupo de recursos nuevo, con un nombre único, por ejemplo `MyTollBooth`. 

5. Seleccione una ubicación de Azure.

6. Especifique un **intervalo** como un número de segundos. Este valor se usa en la aplicación web de ejemplo, con la frecuencia de envío de datos al Centro de eventos. 

7. **Active la casilla** para aceptar los términos y condiciones.

8. Seleccione **Anclar al panel** para que pueda ubicar fácilmente los recursos más adelante.

9. Seleccione **Comprar** para implementar la plantilla de ejemplo.

10. Después de unos momentos, aparecerá una notificación para confirmar que la **implementación se realizó correctamente**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Revisión de los recursos de TollApp de Azure Stream Analytics
1. Iniciar sesión en Azure Portal

2. Ubique el grupo de recursos que nombró en la sección anterior.

3. Compruebe que los recursos siguientes aparecen en el grupo de recursos:
   - Una cuenta de Cosmos DB
   - Un trabajo de Azure Stream Analytics
   - Una cuenta de Azure Storage
   - Un Centro de eventos de Azure
   - Dos aplicaciones web

## <a name="examine-the-sample-tollapp-job"></a>Inspeccione el trabajo de TollApp de ejemplo 
1. A partir del grupo de recursos de la sección anterior, seleccione el trabajo de streaming de Stream Analytics que empieza por el nombre **tollapp** (el nombre contiene caracteres aleatorios para que sea único).

2. En la página de **información general** del trabajo, observe el cuadro de **consulta** para ver la sintaxis de la consulta.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Parafraseando la intención de la consulta, supongamos que necesita contar el número de vehículos que entran en una cabina de peaje. Como la cabina de peaje de una autopista tiene un flujo continuo de vehículos que ingresan, esos eventos de entrada son análogos a un flujo que nunca se detiene. Para cuantificar el flujo, debe definir un "período de tiempo" durante el cual realizar la medición. Vamos a restringir aún más la pregunta a "¿Cuántos vehículos entran en una cabina de peaje cada tres minutos?" Esto se conoce comúnmente como "tumbling count".

   Como puede ver, Azure Stream Analytics usa un lenguaje de consulta que es similar a SQL y agrega algunas extensiones para especificar aspectos de la consulta relacionados con el tiempo.  Para más información, lea sobre las construcciones de [Administración del tiempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) y [Ventanas](https://msdn.microsoft.com/library/azure/dn835019.aspx) que se usan en la consulta.

3. Examine las entradas del trabajo de TollApp de ejemplo. Solo se usa la entrada EntryStream en la consulta actual.
   - La entrada **EntryStream** es una conexión del Centro de eventos que pone en cola los datos que representan cada vez que un automóvil entra a una cabina de peaje en la autopista. Los eventos los crea una aplicación web que forma parte del ejemplo y esos datos se ponen en cola en este Centro de eventos. Observe que se consulta esta entrada en la cláusula FROM de la consulta de streaming.
   - La entrada **ExitStream** es una conexión del Centro de eventos que pone en cola los datos que representan cada vez que un automóvil sale de una cabina de peaje en la autopista. Esta entrada de streaming se usa en variaciones posteriores de la sintaxis de consulta.
   - La entrada **Registration** es una conexión de Azure Blob Storage que señala a un archivo estático registration.json, el que se usa para las búsquedas según sea necesario. Esta entrada de datos de referencia se usa en variaciones posteriores de la sintaxis de consulta.

4. Examine las salidas del trabajo de TollApp de ejemplo.
   - La salida **Cosmos DB** es una colección de bases de datos de Cosmos que recibe los eventos de receptor de salida. Observe que esta salida se usa en la cláusula INTO de la consulta de streaming.

## <a name="start-the-tollapp-streaming-job"></a>Inicio del trabajo de streaming TollApp
Siga estos pasos para iniciar el trabajo de streaming:

1. En la página de **información general** del trabajo, seleccione **Iniciar**.

2. En el panel **Iniciar trabajo**, seleccione **Ahora**.

3. Después de unos momentos, una vez que el trabajo esté en ejecución, en la página de **información general** del trabajo de streaming, consulte el gráfico de **supervisión**. El gráfico debería mostrar varios miles de eventos de entrada y decenas de eventos de salida.

## <a name="review-the-cosmosdb-output-data"></a>Revisión de los datos de salida de CosmosDB
1. Ubique el grupo de recursos que contiene los recursos de TollApp.

2. Seleccione la cuenta de Azure Cosmos DB con el patrón de nombre **tollapp<random>-cosmos**.

3. Seleccione el encabezado del **Explorador de datos** para abrir la página del Explorador de datos.

4. Expanda **tollAppDatabase** > **tollAppCollection** > **Documents**.

5. En la lista de identificadores, se muestran varios documentos una vez que la salida está disponible.

6. Seleccione cada identificador para revisar el documento JSON. Observe el valor de cada TollId, el tiempo de WindowEnd y el número de automóviles desde esa ventana.

7. Después de otros tres minutos, otro conjunto de cuatro documentos estará disponible, un documento por TollId. 


## <a name="report-total-time-for-each-car"></a>Informe del tiempo total de cada automóvil
El tiempo medio que necesita un vehículo para pasar el peaje ayuda a evaluar la eficacia del proceso y la experiencia del cliente.

Para encontrar el tiempo total, combine la secuencia EntryTime con la secuencia ExitTime. Combine los dos flujos de entrada de las columnas coincidentes iguales TollId y LicencePlate. El operador **JOIN** requiere que especifique un margen temporal que describa la diferencia de tiempo aceptable entre los eventos combinados. Use la función **DATEDIFF** para especificar que los eventos no deben durar más de 15 minutos entre sí. Aplique también la función **DATEDIFF** a las horas de entrada y salida para calcular el tiempo real que pasa un vehículo en la estación de peaje. Tenga en cuenta la diferencia del uso de **DATEDIFF** en una instrucción **SELECT** en comparación con su uso en una condición **JOIN**.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Para actualizar la sintaxis de la consulta del trabajo de streaming de TollApp:

1. En la página de **información general** del trabajo, seleccione **Detener**.

2. Espere unos momentos para ver la notificación que indica que el trabajo se detuvo.

3. En el encabezado TOPOLOGÍA DE TRABAJO, seleccione **< > Consulta**.

4. Pegue la consulta SQL de streaming ajustada.

5. Seleccione **Guardar** para guardar la consulta. Seleccione **Sí** para confirmar y guardar los cambios.

6. En la página de **información general** del trabajo, seleccione **Iniciar**.

7. En el panel **Iniciar trabajo**, seleccione **Ahora**.

### <a name="review-the-total-time-in-the-output"></a>Revisión del tiempo total en la salida
Repita los pasos de la sección anterior para revisar los datos de salida de CosmosDB del trabajo de streaming. Revise los documentos JSON más recientes. 

Por ejemplo, en este documento se muestra un automóvil de ejemplo con determinado número de licencia, la hora de entrada y la hora de salida, además del campo durationinminutes donde se calcula DATEDIFF que muestra la duración de la cabina de peaje con un valor de dos minutos: 
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>Informe de los vehículos con registro expirado
Azure Stream Analytics puede usar instantáneas estáticas de datos de referencia para combinar con flujos de datos temporales. Para demostrar esta funcionalidad se usará la siguiente pregunta de ejemplo. La entrada Registration es un archivo JSON de blob estático que muestra las expiraciones de las licencias. Si se combinan con la licencia, los datos de referencia se comparan con cada vehículo que pasa por la cabina de peaje. 

Si un vehículo comercial está registrado en la empresa de peaje, puede atravesar la cabina sin detenerse para inspección. Use la tabla de búsqueda de registro para identificar todos los vehículos comerciales con registros expirados.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Repita los pasos de la sección anterior para actualizar la sintaxis de consulta del trabajo de streaming de TollApp.

2. Repita los pasos de la sección anterior para revisar los datos de salida de CosmosDB del trabajo de streaming. 

Salida de ejemplo:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>Escalado horizontal del trabajo
Azure Stream Analytics está diseñado para escalarse elásticamente de forma que pueda controlar grandes volúmenes de datos. Las consultas de Stream Analytics puede usar una cláusula **PARTITION BY** para indicar al sistema que este paso se escala horizontalmente. **PartitionId** es una columna especial que agrega el sistema para que coincida con el identificador de partición de la entrada (centro de eventos).

Para escalar horizontalmente la consulta a particiones, edite la sintaxis de la consulta al código siguiente:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream 
TIMESTAMP BY EntryTime 
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Para escalar verticalmente el trabajo de streaming a más unidades de streaming:

1. **Detenga** el trabajo actual. 

2. Actualice la sintaxis de la consulta en la página **< > Consulta** y guarde los cambios.

3. En el encabezado CONFIGURAR del trabajo de streaming, seleccione **Escalar**.
   
4. Deslice el control deslizante **Unidades de streaming** de 1 a 6. Las unidades de streaming definen la cantidad de capacidad de proceso que el trabajo puede recibir. Seleccione **Guardar**.

5. **Inicie** el trabajo de streaming para mostrar la escala adicional. Azure Stream Analytics distribuye el trabajo en más recursos de proceso y alcanza un mejor rendimiento, mediante la partición del trabajo entre los recursos a través de la columna designada en la cláusula PARTITION BY. 

## <a name="monitor-the-job"></a>Supervisión del trabajo
El área **SUPERVISIÓN** contiene estadísticas sobre el trabajo en ejecución. La primera vez, se necesita configuración para usar la cuenta de almacenamiento en la misma región (llamada toll como en el resto de este documento).   

![Captura de pantalla de Supervisión](media/stream-analytics-build-an-iot-solution-using-stream-analytics/monitoring.png)

También, puede acceder a **Registros de actividad** desde el área de **Configuración** del panel del trabajo.

## <a name="clean-up-the-tollapp-resources"></a>Limpieza de los recursos de TollApp
1. Detenga el trabajo de Stream Analytics desde Azure Portal.

2. Ubique el grupo de recursos que contenga ocho recursos relacionados con la plantilla de TollApp.

3. Seleccione **Eliminar grupo de recursos**. Escriba el nombre del grupo de recursos para confirmar la eliminación.

## <a name="conclusion"></a>Conclusión
En esta solución se introdujo el servicio Azure Stream Analytics. Se ha demostrado cómo configurar entradas y salidas de un trabajo de Stream Analytics. Mediante el escenario de datos de peaje, se han explicado los tipos más comunes de problemas que surgen en el espacio de datos en movimiento y cómo pueden resolverse con simples consultas de tipo SQL en Azure Stream Analytics. Asimismo, se han descrito las construcciones de extensión SQL para trabajar con datos temporales. Se ha mostrado cómo combinar secuencias de datos, cómo enriquecer la secuencia de datos con datos de referencia estáticos y cómo escalar una consulta horizontalmente para lograr un mayor rendimiento.

Aunque esta solución proporciona una buena introducción, no está completo de en modo alguno. Puede encontrar más patrones de consulta que usan el lenguaje SAQL en [Ejemplos de consulta para patrones de uso comunes de Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).
