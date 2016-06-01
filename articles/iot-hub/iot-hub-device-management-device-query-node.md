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

# Tutorial: Búsqueda de dispositivos gemelos mediante consultas con node.js (versión preliminar)

[AZURE.INCLUDE [iot-hub-device-management-query-selector](../../includes/iot-hub-device-management-query-selector.md)]

La administración de dispositivos IoT de Azure permite buscar dispositivos gemelos, la representación del servicio de un dispositivo físico, con consultas. Puede realizar consultas en función de las propiedades del dispositivo, las propiedades del servicio o las etiquetas del dispositivo gemelo. Puede realizar consultas mediante etiquetas y propiedades:

-   Para consultar dispositivos gemelos mediante etiquetas, pase una matriz de cadenas y la consulta devolverá el conjunto de dispositivos etiquetados con todas esas cadenas.

-   Para consultar dispositivos gemelos mediante propiedades de servicio o de dispositivo, se usa una expresión de consulta JSON.

Para obtener más información sobre los dispositivos gemelos y las consultas, vea [Introducción a la administración de dispositivos de Centro de IoT de Azure][lnk-dm-overview].

## Ejecución del ejemplo de consulta de dispositivo

En el siguiente ejemplo se amplía la función del tutorial [Introducción a la administración de dispositivos de Centro de IoT de Azure][lnk-get-started]. Partiendo de que los distintos dispositivos simulados están en ejecución, se utilizará una consulta para encontrar dispositivos específicos.

### Requisitos previos 

Antes de ejecutar este ejemplo, debe haber completado los pasos de [Introducción a la administración de dispositivos de Centro de IoT de Azure][lnk-get-started]. Esto significa que los dispositivos simulados deben estar en ejecución. Si ya realizó el proceso anteriormente, reinicie ahora los dispositivos simulados.

### Inicio del ejemplo

Para iniciar el ejemplo, debe ejecutar `registry_queryDevices.js`. Este proceso ejecutará algunas consultas diferentes. Siga estos pasos para iniciar el ejemplo:

1.  Permita que los dispositivos simulados se ejecuten durante al menos un minuto. Así se garantiza que las propiedades del dispositivo en el gemelo se sincronizan con el dispositivo físico. Consulte [Tutorial: Uso del dispositivo gemelo][lnk-twin-tutorial] para obtener más información sobre la sincronización.

2.  Desde la carpeta raíz donde clonó el repositorio **azure-iot-sdks**, vaya al directorio **azure-iot-sdks/node/service/samples**.

3.  Abra **registry\_queryDevices.js** y reemplace el marcador de posición por su cadena de conexión del Centro de IoT.

4.  Ejecute `node registry_queryDevices.js`.

Verá la salida en la ventana de la línea de comandos, con el resultado de las consultas de objetos de dispositivo mediante etiquetas, propiedades de servicio y propiedades de dispositivo.

## Estructura de la consulta (JSON)

Las consultas en las propiedades de dispositivo y las propiedades de servicio se ejecutan con una cadena JSON para representar la propia consulta. La cadena JSON está compuesto de cuatro partes. A continuación se muestra una explicación de cada parte y el objeto de C# que se puede serializar en la cadena JSON correcta.

- **Proyecto:** expresión que designa los campos del objeto de dispositivo que se incluirán en el conjunto de resultados de consulta (equivalente a SELECT en SQL):

	```
	var projectQuery = {
	    "project": {
	        "all": false,
	        "properties": [{
	            "name": "CustomerId",
	            "type": "service"
	        }, {
	            "name": "Weight",
	            "type": "service"
	        }]
	    }
	}
	```

- **Filtro:** expresión que limita los objetos de dispositivo incluidos en el conjunto de resultados de consulta (equivalente a WHERE en SQL):

	```
	var filterQuery = {
	  filter: {
	    property: {
	      name: "CustomerId",
	      type: "service"
	    },
	    value: "123456",
	    comparisonOperator: "eq",
	    type: "comparison"
	  },
	  project: null,
	  aggregate: null,
	  sort: null
	};
	```

- **Agregado:** expresión que determina cómo se agrupa el conjunto de resultados de consulta (equivalente a GROUPBY en SQL):

	```
	var aggregateQuery = {
	filter: null,
	project: null,
	aggregate: {
	keys: [
	  {
	    name: "CustomerId",
	    type: "service"
	  }
	],
	properties: [
	  {
	    operator: "sum",
	    property: {
	      name: "Weight",
	      type: "service"
	    },
	    columnName: "TotalWeight"
	  }
	]
	},
	sort: null
	};
	```

- **Ordenación:** definición de expresión cuya propiedad se debe usar para ordenar el conjunto de resultados de consulta (equivalente a ORDER BY en SQL). Si la ordenación es null, se usa **deviceID** de forma predeterminada:

```
  var sortQuery = {
  filter: null,
  project: null,
  aggregate: null,
  sort: [
      {
          order: "asc",
          property: {
              name: "QoS",
              type: "service"
          }
      }
  ]
  };
```

### Limitaciones

Existen algunas limitaciones en la implementación de la versión preliminar pública de las consultas.

-   No hay ninguna validación en la expresión de consulta.

-   Las consultas distinguen mayúsculas de minúsculas.

-   Cuando se usan expresiones de consulta para consultar por las propiedades de servicio o de dispositivo, solo se devuelven 100 dispositivos. Puede ver un ejemplo de cómo implementar la paginación en [nuestra biblioteca de consultas][lnk-query-samples].

[Aquí][lnk-query-expression-guide] encontrará más información sobre la sintaxis y los campos disponibles para JSON. También puede ver consultas de ejemplo en nuestra [biblioteca de expresiones de consulta][lnk-query-samples].

### Consulta por propiedades de dispositivo y de servicio

Una vez que tiene la expresión de consulta JSON, puede consultar los dispositivos gemelos. Llame a **queryDevices** y compruebe el objeto **result** para consultas de agregado y el objeto **devices** para todas las demás consultas.

```
registry.queryDevices(query, done)
```

### Consulta por etiquetas

La consulta por etiquetas permite encontrar objetos de dispositivo sin utilizar una expresión de consulta JSON. Si se pasa más de una etiqueta, solo se devolverán los objetos de dispositivo con todas las etiquetas. El segundo parámetro es **maxCount**, el número máximo de dispositivos que se devuelve. El valor máximo de **maxCount** es 1000.

```
registry.queryDevicesByTags(['bacon'], 100, done)
```

### Implementación de dispositivos

La consulta se habilita mediante la biblioteca de cliente de administración de dispositivos del Centro de IoT de Azure. Siempre y cuando las propiedades del dispositivo estén sincronizadas (como se describe en [Tutorial: Uso del dispositivo gemelo][lnk-twin-tutorial]), puede realizar consultas basadas en ellas. Las propiedades del dispositivo están disponibles solo después de que el dispositivo físico se conecta al Centro de IoT y proporciona valores iniciales.

## Pasos siguientes

Para más información sobre las características de administración de dispositivos del Centro de IoT de Azure, puede consultar los tutoriales:

- [How to use the device twin (Uso de dispositivos gemelos)][lnk-twin-tutorial]
- [How to use device jobs to update device firmware (Uso de trabajos de dispositivos para actualizar el firmware del dispositivo)][lnk-jobs-tutorial]



<!-- images and links -->
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-jobs-tutorial]: iot-hub-device-management-device-jobs.md
[lnk-query-spec]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md
[lnk-query-expression-guide]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md

<!---HONumber=AcomDC_0518_2016-->