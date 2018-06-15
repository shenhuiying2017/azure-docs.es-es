---
title: 'Tutorial: Funciones definidas por el usuario en JavaScript para Azure Stream Analytics | Microsoft Docs '
description: En este tutorial se realizan mecánicas de consultas avanzadas con funciones definidas por el usuario en JavaScript
keywords: javascript, funciones definidas por el usuario, udf
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: jasonh
ms.custom: mvc
ms.date: 04/01/2018
ms.workload: data-services
ms.author: sngun
ms.openlocfilehash: f3a94017b95eb614669fa42594fe3a3499c74be7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31415303"
---
# <a name="tutorial-azure-stream-analytics-javascript-user-defined-functions"></a>Tutorial: Funciones definidas por el usuario en JavaScript para Azure Stream Analytics

Azure Stream Analytics admite funciones definidas por el usuario escritas en JavaScript. Con el conjunto completo de métodos **String**, **RegExp**, **Math**, **Array** y **Date** que proporciona JavaScript, las transformaciones de datos complejas con trabajos de Stream Analytics son más fáciles de crear.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Definir funciones definidas por el usuario en JavaScript
> * Agregar la función al portal
> * Definir una consulta que ejecuta la función

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="javascript-user-defined-functions"></a>Funciones definidas por el usuario en JavaScript
Las funciones definidas por el usuario en JavaScript admiten funciones escalares de solo cálculo y sin estado, que no requieren conectividad externa. El valor devuelto de una función solo puede ser un valor escalar (único). Después de agregar una función definida por el usuario en JavaScript a un trabajo, puede usar la función en cualquier parte de la consulta, como una función escalar integrada.

Estos son algunos escenarios en los que le podrían resultar útiles las funciones definidas por el usuario en JavaScript:
* Análisis y manipulación de cadenas que incluyen funciones de expresiones regulares, por ejemplo, **Regexp_Replace()** y **Regexp_Extract()**
* Descodificación y codificación de datos, por ejemplo, conversión de binario a hexadecimal
* Realización de cálculos matemáticos con funciones **matemáticas** de JavaScript
* Realización de operaciones de matriz como ordenar, combinar, buscar y rellenar

Aquí se indican algunas operaciones que no se pueden realizar con una función definida por el usuario de JavaScript en Stream Analytics:
* Llamar a puntos de conexión de REST externos, por ejemplo, mediante la realización de una búsqueda inversa de direcciones IP o una extracción de datos de referencia de un origen externo
* Realizar la serialización o deserialización de formatos de eventos personalizados en entradas y salidas
* Crear agregados personalizados

Aunque haya funciones como **Date.GetDate()** o **Math.random()** que no están bloqueadas en la definición de funciones, debe evitar su uso. Estas funciones **no** devuelven el mismo resultado cada vez que las llama y el servicio Azure Stream Analytics no mantiene un diario de las llamadas a funciones y los resultados devueltos. Si una función devuelve resultados diferentes en los mismos eventos, la repetibilidad no se garantizará cuando el usuario o el servicio Stream Analytics reinicie un trabajo.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Incorporación de una función definida por el usuario de JavaScript en Azure Portal
Para crear una función definida por el usuario de JavaScript en un trabajo de Stream Analytics existente, realice estos pasos:

1.  En Azure Portal, busque el trabajo de Stream Analytics.
2.  En **TOPOLOGÍA DE TRABAJO**, seleccione la función. Aparece una lista vacía de funciones.
3.  Para crear una nueva función definida por el usuario, seleccione **Agregar**.
4.  En la hoja **Nueva función**, en **Tipo de función**, seleccione **JavaScript**. Aparecerá una plantilla de función predeterminada en el editor.
5.  Como **alias de la función definida por el usuario** escriba **hex2Int** y cambie la implementación de la función como se indica a continuación:

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Seleccione **Guardar**. La función aparece en la lista de funciones.
7.  Seleccione la nueva función **hex2Int** y compruebe la definición de función. Todas las funciones tienen un prefijo **UDF** agregado al alias de la función. Debe *incluir el prefijo* cuando llama a la función en la consulta de Stream Analytics. En este caso, debe llamar a **UDF.hex2Int**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Llamada a una función definida por el usuario de JavaScript en una consulta

1. En el editor de consultas, en **TOPOLOGÍA DE CONSULTA**, seleccione **Consulta**.
2.  Edite la consulta y, a continuación, llame a la función definida por el usuario, de la siguiente forma:

    ```
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Haga clic con el botón derecho en la entrada del trabajo para cargar el archivo de datos de ejemplo.
4.  Para probar la consulta, seleccione **Probar**.


## <a name="supported-javascript-objects"></a>Objetos de JavaScript compatibles
Las funciones definidas por el usuario en JavaScript para Azure Stream Analytics admiten objetos de JavaScript estándar e integrados. Para obtener una lista de estos objetos, consulte [Objetos globales](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Conversión de tipos de Stream Analytics y JavaScript

Hay diferencias entre los tipos admitidos en el lenguaje de consultas de Stream Analytics y JavaScript. En esta tabla se muestran las asignaciones de conversión entre los dos:

Stream Analytics | JavaScript
--- | ---
bigint | Number (JavaScript solamente puede representar enteros hasta 2^53 con precisión)
Datetime | Date (JavaScript solo admite milisegundos)
double | Number
nvarchar(MAX) | string
Registro | Objeto
Matriz | Matriz
NULL | Null


Estas son las conversiones de JavaScript a Stream Analytics:


JavaScript | Stream Analytics
--- | ---
Number | Bigint (si el número es round y entre long.MinValue y long.MaxValue, double en caso contrario)
Date | Datetime
string | nvarchar(MAX)
Objeto | Registro
Matriz | Matriz
Null, sin definir | NULL
Cualquier otro tipo (por ejemplo, una función o un error) | No admitido (da lugar a un error en tiempo de ejecución)

## <a name="troubleshooting"></a>solución de problemas
Los errores en tiempo de ejecución de JavaScript se consideran graves y se presentan en el registro de actividad. Para recuperar el registro, en Azure Portal, vaya al trabajo y seleccione **Registro de actividad**.


## <a name="other-javascript-user-defined-function-patterns"></a>Otros patrones de función definida por el usuario de JavaScript

### <a name="write-nested-json-to-output"></a>Escritura de JSON anidado en la salida
Si tiene un paso de procesamiento de seguimiento que usa la salida del trabajo de Stream Analytics como entrada y requiere un formato JSON, puede escribir una cadena JSON en la salida. El ejemplo siguiente llama a la función **JSON.stringify()** para empaquetar todos los pares nombre-valor de la entrada y escribirlos como un único valor de cadena en la salida.

**Definición de funciones definidas por el usuario en JavaScript:**

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

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando no los necesite, elimine el grupo de recursos, el trabajo de streaming y todos los recursos relacionados. La eliminación del trabajo evita la facturación de las unidades de streaming utilizadas por el trabajo. Si piensa utilizar el trabajo en el futuro, puede detenerlo y volver a iniciarlo más adelante cuando sea necesario. Si no va a seguir usando este trabajo, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido:

1. En el menú de la izquierda de Azure Portal, haga clic en **Grupos de recursos** y en el nombre del recurso que creó.  
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba en el cuadro de texto el nombre del recurso que quiere eliminar y haga clic en **Eliminar**.

## <a name="get-help"></a>Obtención de ayuda
Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado un trabajo de Stream Analytics que ejecuta una función simple definida por el usuario en JavaScript. Para más información sobre Stream Analytics, continúe con los artículos del escenario en tiempo real:

> [!div class="nextstepaction"]
> [Análisis de opinión de Twitter en tiempo real en Azure Stream Analytics](stream-analytics-twitter-sentiment-analysis-trends.md)
