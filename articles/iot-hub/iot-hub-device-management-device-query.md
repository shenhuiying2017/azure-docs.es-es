<properties
	pageTitle="Consultas gemelas de administración de dispositivos del Centro de IoT | Microsoft Azure"
	description="Tutorial del Centro de IoT de Azure para la administración de dispositivos que describe cómo usar consultas para encontrar dispositivos gemelos."
	services="iot-hub"
	documentationCenter=".net"
	authors="ellenfosborne"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# Tutorial: Búsqueda de dispositivos gemelos mediante consultas (versión preliminar)

La administración de dispositivos IoT de Azure permite buscar dispositivos gemelos, la representación del servicio de un dispositivo físico, con consultas. Puede realizar consultas en función de las propiedades del dispositivo, las propiedades del servicio o las etiquetas del dispositivo gemelo. Puede realizar consultas mediante etiquetas y propiedades:

-   Para consultar dispositivos gemelos mediante etiquetas, se pasa una matriz de cadenas y la consulta devuelve el conjunto de dispositivos que están etiquetados con todas esas cadenas.

-   Para consultar dispositivos gemelos mediante propiedades de servicio o de dispositivo, se usa una expresión de consulta JSON.

Para más información sobre dispositivos gemelos y consultas, consulte [Overview of Azure IoT Hub device management][lnk-dm-overview] (Información general sobre la administración de dispositivos del Centro de IoT).

## Ejecución del ejemplo de consulta de dispositivo

En el ejemplo siguiente se amplía la funcionalidad del tutorial [Get started with Azure IoT Hub device management][lnk-get-started] (Introducción a la administración de dispositivos del Centro de IoT de Azure). Partiendo de que los distintos dispositivos simulados están en ejecución, se utilizará una consulta para encontrar dispositivos específicos.

### Requisitos previos 

Antes de ejecutar este ejemplo, debe haber completado los pasos de [Get started with Azure IoT Hub device management][lnk-get-started] (Introducción a la administración de dispositivos del Centro de IoT de Azure). Esto significa que los dispositivos simulados deben estar en ejecución. Si ya realizó el proceso anteriormente, reinicie ahora los dispositivos simulados.

### Inicio del ejemplo

Para iniciar el ejemplo, debe ejecutar el proceso **Query.exe**. Este proceso ejecutará algunas consultas diferentes. Siga estos pasos para iniciar el ejemplo:

1.  Permita que los dispositivos simulados se ejecuten durante al menos un minuto. Así se garantiza que las propiedades del dispositivo en el gemelo se sincronizan con el dispositivo físico. Consulte [Tutorial: How to use the device twin][lnk-twin-tutorial] (Tutorial: Uso del dispositivo gemelo) para más información sobre la sincronización.

2.  En la carpeta raíz donde clonó el repositorio **azure-iot-sdks**, vaya a la carpeta **azure-iot-sdks\\csharp\\service\\samples\\bin**.

3.  Ejecute `Query.exe <IoT Hub Connection String>`

Verá la salida en la ventana de la línea de comandos, con el resultado de las consultas de objetos de dispositivo mediante etiquetas, propiedades de servicio y propiedades de dispositivo.

## Estructura de la consulta (JSON)

Las consultas en las propiedades de dispositivo y las propiedades de servicio se ejecutan con una cadena JSON para representar la propia consulta. La cadena JSON está compuesto de cuatro partes. A continuación se muestra una explicación de cada parte y el objeto de C# que se puede serializar en la cadena JSON correcta.

- **Proyecto**: la expresión que designa los campos del objeto de dispositivo que se incluirán en el conjunto de resultados de consulta (equivalente a SELECT en SQL):

  ```
  var query = JsonConvert.SerializeObject(
      project = new
      {
        all = false,
        properties = new []
        {
          new
          {
            name = "CustomerId",
            type = "service"
          },
          new
          {
            name = "Weight",
            type = "service"
          }
        }
      }
  );
```

- **Filtro**: la expresión que limita los objetos de dispositivo incluidos en el conjunto de resultados de consulta (equivalente a WHERE en SQL):

  ```
  var query = JsonConvert.SerializeObject(
      new
      {
        filter = new
        {
          property = new
          {
            name = "CustomerId",
            type = "service"
          },
          value = "123456",
          comparisonOperator = "eq",
          type = "comparison"
        }
      }
  );
  ```

- **Agregado**: la expresión que determina cómo se agrupa el conjunto de resultados de consulta (equivalente a GROUPBY en SQL):

  ```
  var query = JsonConvert.SerializeObject(
      new
      {
      filter = new
        {
          property = new
          {
            name = "CustomerId",
            type = "service"
          },
          value = (string)null,
          comparisonOperator = "ne",
          type = "comparison"
        },
        aggregate = new
        {
          keys = new[]
          {
            new
            {
              name = "CustomerId",
              type = "service"
            }
          },
          properties = new[]
          {
            new
            {
              @operator = "avg",
              property = new
              {
                name = "Weight",
                type = "service"
              },
              columnName = "TotalWeight"
            }
          }
        }
      }
  );
  ```

- **Ordenación**: la definición de expresión cuya propiedad se debe usar para ordenar el conjunto de resultados de consulta (equivalente a ORDER BY en SQL). Si la ordenación es null, se utiliza de forma predeterminada **deviceID**:

  ```
  var query = JsonConvert.SerializeObject(
    new
    {
      sort = new[]
      {
        new
        {
          property = new
          {
            name = "QoS",
            type = "service"
          },
          order = "asc"
        }
      }
    }
  );
  ```

### Limitaciones

Existen algunas limitaciones en la implementación de la versión preliminar pública de las consultas.

-   No hay ninguna validación en la expresión de consulta.

-   Las consultas distinguen mayúsculas de minúsculas.

-   Cuando se usan expresiones de consulta para consultar por las propiedades de servicio o de dispositivo, solo se devuelven 100 dispositivos. Puede encontrar un ejemplo de cómo implementar la paginación en [nuestra biblioteca de consultas][lnk-query-samples].

Puede encontrar más información sobre la sintaxis y los campos disponibles para JSON en [][lnk-query-expression-guide]. También puede ver consultas de ejemplo en nuestra [biblioteca de expresiones de consulta][lnk-query-samples].

### Consulta por propiedades de dispositivo y de servicio

Una vez que tiene la expresión de consulta JSON, puede consultar los dispositivos gemelos. Llame a **QueryDeviceJsonAsync** y marque el campo **AggregateResult** para las consultas agregadas y el campo **Result** para todas las demás consultas. **Result** contiene una lista de objetos de dispositivo, que representan los dispositivos gemelos que coinciden con la consulta. **AggregateResult** contiene una matriz de diccionarios y cada uno contiene la fila resultante.

Estas consultas se usan en **Program.cs** del proyecto **Consulta**.

```
var foundDevices = (await registryManager.QueryDevicesJsonAsync(query)).Result;

var results = (await registryManager.QueryDevicesJsonAsync(query)).AggregateResult;
```

### Consulta por etiquetas

La consulta por etiquetas permite encontrar objetos de dispositivo sin utilizar una expresión de consulta JSON. Si se pasa más de una etiqueta, solo se devolverán los objetos de dispositivo con todas las etiquetas. El segundo parámetro es **maxCount**, el número máximo de dispositivos que se devuelve. El valor máximo de **maxCount** es 1000.

```
var foundDevices = await registryManager.QueryDevicesAsync(new[] { "bacon" }, 100);
```

### Implementación de dispositivos

La consulta se habilita mediante la biblioteca de cliente de administración de dispositivos del Centro de IoT de Azure. Siempre y cuando las propiedades del dispositivo estén sincronizadas (como se describe en [Tutorial: How to use the device twin][lnk-twin-tutorial] (Uso de dispositivos gemelos)), puede realizar consultas basadas en ellas. Las propiedades del dispositivo están disponibles solo después de que el dispositivo físico se conecta al Centro de IoT y proporciona valores iniciales.

## Pasos siguientes

Para más información sobre las características de administración de dispositivos del Centro de IoT de Azure, puede consultar los tutoriales:

- [How to use the device twin][lnk-twin-tutorial] (Uso de dispositivos gemelos)
- [How to use device jobs to update device firmware][lnk-jobs-tutorial] (Uso de trabajos de dispositivos para actualizar el firmware del dispositivo)



<!-- images and links -->
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-jobs-tutorial]: iot-hub-device-management-device-jobs.md
[lnk-query-spec]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md
[lnk-query-expression-guide]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md

<!---HONumber=AcomDC_0504_2016-->