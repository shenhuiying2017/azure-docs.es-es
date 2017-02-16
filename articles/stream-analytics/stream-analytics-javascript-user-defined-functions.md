---
title: Funciones definidas por el usuario en JavaScript para Azure Stream Analytics | Microsoft Docs
description: "Flujos de datos y SensorTags de IoT con análisis de transmisiones y procesamiento de datos en tiempo real"
keywords: "solución de iot, introducción a iot, herramientas"
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 822f0870396e1fa5fe2dfee3bae410aad3da1122
ms.openlocfilehash: 959fd48cb7d7f21385f6eea21f79ad26e8164bc0

---

# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Funciones definidas por el usuario en JavaScript para Azure Stream Analytics
Azure Stream Analytics admite funciones definidas por el usuario (UDF) escritas en JavaScript. Con el conjunto completo de métodos String, RegExp, Math, Array y Date que proporciona JavaScript, las transformaciones de datos complejas con trabajos de Stream Analytics son más fáciles de crear.

## <a name="overview"></a>Información general
Las funciones definidas por el usuario de JavaScript admiten funciones escalares de solo cálculo y sin estado, que no requieren conectividad externa. El valor devuelto de una función solo puede ser un valor escalar (único). Una vez agregada a un trabajo, la función se puede utilizar en cualquier lugar en la consulta como una función escalar integrada.

Estos son algunos escenarios de ejemplo donde se puede ver la utilidad de las funciones definidas por el usuario de JavaScript:
* Análisis y manipulación de cadenas con funciones de expresiones regulares, por ejemplo, Regexp_Replace() and Regexp_Extract()
* Descodificación de datos de codificación, por ejemplo, conversión de binario a hexadecimal
* Cálculos matemáticos con funciones matemáticas de JavaScript
* Operaciones de matriz como ordenar, combinar, buscar y rellenar

A continuación se indican algunas operaciones que no se pueden realizar con una función definida por el usuario de JavaScript en Stream Analytics:
* Llamar a puntos de conexión de REST externos, por ejemplo, búsqueda inversa de direcciones IP o extracción de datos de referencia de un origen externo
* Serialización o deserialización de formatos de eventos personalizados en entradas y salidas
* Agregados personalizados

Aunque no estén bloqueadas en la definición de funciones, debe evitar el uso de funciones como Date.GetDate() o Math.random(). Estas funciones **no** devuelven el mismo resultado cada vez que las llama y el servicio Azure Stream Analytics no mantiene un diario de las llamadas a funciones y los resultados devueltos. Por lo tanto, si una función devuelve resultados diferentes en los mismos eventos, la repetibilidad no se garantizará cuando el usuario o el servicio Stream Analytics reinicie un trabajo.

## <a name="adding-a-javascript-udf-from-azure-portal"></a>Incorporación de una función definida por el usuario de JavaScript desde Azure Portal
Para crear una función definida por el usuario de JavaScript en un trabajo de Stream Analytics existente, siga estos pasos.

1.  Abra Azure Portal.

2.  Busque el trabajo de Stream Analytics y luego haga clic en el vínculo de funciones en **TOPOLOGÍA DE TRABAJO**.
 
3.  Verá una lista vacía de las funciones existentes; haga clic en el icono **Agregar** para agregar una nueva función definida por el usuario.

4.  En la hoja **Nueva función**, seleccione JavaScript como el tipo de función y verá que aparece una plantilla de función predeterminada en el editor.
 
5.  Ponga _hex2Int_ como el alias de función definida por el usuario y cambie la implementación de la función como se indica a continuación:

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Haga clic en el botón **Guardar**; la función aparecerá en la lista de funciones. 

7.  Haga clic en la nueva función **hex2Int** y podrá comprobar la definición de función. Tenga en cuenta que a todas las funciones se agregará un prefijo "UDF" delante del alias de la función. Debe llamar a la función **con el prefijo** en la consulta de Stream Analytics, en este caso **UDF.hex2Int**.
 
## <a name="calling-javascript-udf-in-a-query"></a>Llamada a una función definida por el usuario de JavaScript en una consulta

1. Abra el editor de consultas haciendo clic en **Consulta** en **TOPOLOGÍA DE TRABAJO**. 

2.  Edite la consulta y llame a la función definida por el usuario que acaba de agregar tal y como se indica a continuación:

    ```
    SELECT 
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Haga clic con el botón derecho en la entrada del trabajo para cargar el archivo de datos de ejemplo 
 
4.  Haga clic en **Probar** para probar la consulta.


## <a name="supported-javascript-objects"></a>Objetos de JavaScript compatibles
Una función definida por el usuario de JavaScript para Azure Stream Analytics admite objetos integrados estándar del lenguaje JavaScript. Para obtener la lista de objetos, consulte el documento sobre [objetos globales](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Conversión de tipos de Stream Analytics y JavaScript

Hay diferencias entre los tipos admitidos en el lenguaje de consultas Stream Analytics y JavaScript. En la tabla siguiente se muestran las asignaciones de conversión entre los dos.


---
Stream Analytics | JavaScript
--- | ---
bigint | Number (tenga en cuenta que JavaScript solamente puede representar enteros hasta 2^53 con precisión)
DateTime | Date (tenga en cuenta que JavaScript solo admite milisegundos) 
double | Number
nvarchar(MAX) | String
Registro | Objeto
Matriz | Matriz
NULL | Null


También se muestran las conversiones de JavaScript a ASA.

---
JavaScript | Stream Analytics
--- | ---
Number | Bigint si el número es round y entre long.MinValue y long.MaxValue, double en caso contrario
Date | DateTime
String | nvarchar(MAX)
Objeto | Registro
Matriz | Matriz
Null, sin definir | NULL
cualquier otro tipo, por ejemplo, Function, Error, etc. | No admitido (error en tiempo de ejecución)

## <a name="troubleshooting"></a>Solución de problemas
Los errores en tiempo de ejecución de JavaScript se considerarán graves y se presentarán en el registro de actividad. Para recuperar el registro de Azure Portal, vaya a su trabajo y haga clic en **Registro de actividad**.
 

## <a name="other-javascript-udf-usage-patterns"></a>Otros patrones de uso de funciones definidas por el usuario de JavaScript

### <a name="writing-nested-json-to-output"></a>Escritura de JSON anidado en la salida
La escritura de una cadena JSON en la salida es una tarea común cuando tiene un paso de procesamiento de seguimiento que toma la salida del trabajo de Stream Analytics como entrada y requiere un formato JSON. El ejemplo siguiente llama a la función JSON.stringify() para empaquetar todos los pares nombre-valor de la entrada y escribirlos como un único valor de cadena en la salida. 

### <a name="javascript-udf-definition"></a>Definición de función definida por el usuario de JavaScript:

```
function main(x) {
return JSON.stringify(x);
}
```

**Consulta de ejemplo:**
```
SELECT 
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--HONumber=Feb17_HO1-->


