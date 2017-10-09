---
title: "Transformaciones admitidas disponibles con la preparación de datos de Azure Machine Learning  | Microsoft Docs"
description: "Este documento proporciona una lista completa de las transformaciones disponibles para la preparación de datos de Azure Machine Learning."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fd3f8157e27847b99e59465063111a6d6c8c713d
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---
# <a name="supported-transforms-for-this-release"></a>Transformaciones admitidas para esta versión
En la siguiente lista se resumen las transformaciones disponibles para esta versión. 

Las transformaciones admitidas para esta versión se describen a continuación.

## <a name="column-selection"></a>Selección de columnas 
Muchas de las transformaciones que se describen a continuación funcionan tanto en una sola columna como en varias. Para seleccionar varias columnas, pulse la tecla Control y haga clic en cada columna o bien pulse la tecla Mayús y haga clic en un rango de columnas.

## <a name="transforms-from-main-menu-andor-grid-header"></a>Transformaciones desde el menú principal o el encabezado de la cuadrícula 
Es posible tener acceso a las transformaciones desde la opción Transformaciones del menú principal, pero también se pueden seleccionar haciendo clic con el botón derecho en el nombre de la columna en la cuadrícula de datos. Si se seleccionan varias columnas, al hacer doble clic en cualquiera se obtiene menú de transformaciones.

Solo se ofrecen transformaciones válidas para el tipo de datos seleccionado en el menú que aparece al hacer clic con el botón derecho; por su parte, el menú principal ofrece todas las transformaciones pero marca en gris aquellas que no son pertinentes para las columnas seleccionadas.

Al hacer clic con el botón derecho en una celda, aparece un pequeño subconjunto de transformaciones contextuales. Estas transformaciones son Copiar, Reemplazar y Filtrar y dependen del tipo de datos; por tanto, las opciones para una columna de número son diferentes de aquellas para una columna de cadena.

## <a name="derive-column-by-example"></a>Derivación de columnas por ejemplos
Esta transformación permite la creación de una nueva columna como derivada de una o varias columnas existentes. Para ello,se fija en las columnas de entrada (seleccionadas) y en el ejemplo presentado para determinar el resultado deseado en la nueva columna. 

Para usarla, seleccione una o varias columnas. Agregue una nueva columna derivada (en blanco) por ejemplo. Escriba un ejemplo de lo que desea ver en la columna derivada (suponiendo que se derive de otras columnas) y la tecnología de "Por ejemplo" intentará rellenar todas las demás celdas de la columna. 

En el caso de los ejemplos complicados, puede que sea necesario proporcionar más de un ejemplo; para ello, seleccione otra celda y escriba otro ejemplo.

La tecnología de "Por ejemplo" usa las columnas seleccionadas para intentar determinar el significado de un ejemplo. Si no hay columnas seleccionadas cuando se invoca esta transformación, se utilizan todas las celdas de la fila actual. La selección de únicamente las columnas necesarias genera resultados más precisos.

Puede seleccionar columnas antes de invocar la transformación. Una vez que se ha iniciado el editor de la transformación, es posible saber qué columnas están seleccionadas como entradas comprobando las casillas de verificación de la parte superior de cada columna. Para agregar o quitar columnas a la selección, utilice estas casillas de verificación de los encabezados de las columnas.

Para obtener una explicación más detallada de la transformación **Derivación de columnas por ejemplos**, junto con más ejemplos, consulte la referencia [Derive column by example transformation](data-prep-derive-column-by-example.md) (Transformación de derivación de columnas por ejemplo).  

## <a name="split-column-by-example"></a>División de columnas por ejemplos
Toma una columna existente y, con el motor de "Por ejemplo", intenta dividir esa columna en otras columnas. Es posible ejecutar la división automática en las posteriores columnas generadas.

Para obtener una explicación más detallada de la transformación **División de columnas por ejemplos**, junto con más ejemplos, consulte la referencia [Split column by example transformation](data-prep-split-column-by-example.md) (Transformación de división de columnas por ejemplo).

## <a name="expand-json"></a>Expansión de JSON

La transformación **Expansión de JSON** permite a los usuarios agregar varias columnas mediante la expansión de una columna con texto JSON válido.

Para obtener una explicación más detallada de la transformación **Expansión de JSON**, junto con más ejemplos, consulte la referencia [Expand JSON transformation](data-prep-expand-json.md) (Transformación de expansión de JSON).


## <a name="combine-columns-by-example"></a>Combinación de columnas por ejemplos

Permite al usuario agregar una nueva columna mediante la combinación de valores de varias columnas. 

Para obtener una explicación más detallada de la transformación **Combinación de columnas por ejemplos**, junto con más ejemplos, consulte la referencia [Combine columns by example transformation](data-prep-combine-columns-by-example.md) (Transformación de combinación de columnas por ejemplo).


## <a name="duplicate-column"></a>Duplicación de columna
Esta transformación crea una copia exacta de otras columnas y le pone un nuevo nombre derivado de los nombres de las columnas originales. Esta transformación admite una o varias columnas.

## <a name="text-clustering"></a>Agrupación en clústeres de texto 
Esta transformación está diseñada para tomar los valores incoherentes que deberían ser iguales y agruparlos.  

Cuando se ejecuta esta transformación, se analizan los valores en una sola columna para detectar su similitud. A continuación, se agrupan en clústeres. Para cada clúster, hay un valor canónico, que es el valor que reemplaza todas las instancias del clúster y los valores de la instancia. Los clústeres completados se pueden quitar, y el valor canónico se puede editar. Las instancias se pueden quitar de un clúster determinado. Además, se puede cambiar el filtro de umbral de puntuación de similitud que se usa para agrupar las instancias en un clúster.

De forma predeterminada, esta transformación reemplaza todos los valores de la instancia de clúster por el valor canónico de dicho clúster, creando una nueva columna que contiene los nuevos valores. También es posible agregar la puntuación de similitud de cada instancia a una nueva columna (a la que se puede poner un nombre) para permitir su uso más adelante en el flujo de datos.

## <a name="replace-values"></a>Reemplazo de valores
Esta transformación permite la sustitución de una cadena por otra; la cadena de origen puede ser una cadena parcial o una celda completa. La sustitución puede aplicarse a una sola columna o a varias. La cadena de búsqueda admite la búsqueda de caracteres especiales, así como caracteres normales. 

## <a name="replace-na-values"></a>Sustitución de valores NA
Permite la sustitución de las diferentes formas de NA (N/A, NA, null, NaN, etc.) o cadenas vacías por un valor único para que sean coherentes. Esta transformación admite una o varias columnas. Esta transformación se muestra únicamente cuando una columna está seleccionada y no figura en el menú de transformación principal en el momento en que no hay ninguna columna seleccionada.

## <a name="replace-missing-values"></a>Sustitución de valores ausentes
Realiza la sustitución de los datos ausentes por un solo valor. Esta transformación admite una o varias columnas. Esta transformación se muestra únicamente cuando una columna está seleccionada y no figura en el menú de transformación principal en el momento en que no hay ninguna columna seleccionada.

## <a name="replace-error-values"></a>Sustitución de valores de error
Realiza la sustitución de los errores por un solo valor. Esta transformación admite una o varias columnas. Esta transformación se muestra únicamente cuando una columna está seleccionada y no figura en el menú de transformación principal en el momento en que no hay ninguna columna seleccionada.

## <a name="trim-string"></a>Recorte de cadena
Quita los caracteres de "espacio en blanco" iniciales y finales (lo que incluye espacios, tabulaciones, etc.), de una sola columna o de varias.

## <a name="adjust-precision"></a>Ajuste de la precisión
Permite el establecimiento del número de posiciones decimales para una columna numérica.

## <a name="rename-column"></a>Cambio del nombre de columna
Cambia el nombre de la columna; esta transformación se puede invocar también desde el propio encabezado de la columna haciendo clic en el nombre de esta.

## <a name="remove-column"></a>Retirada de columna
Quita las columnas seleccionadas; esta transformación funciona en una sola columna o en varias. 

## <a name="keep-column"></a>Mantenimiento de columna
Mantiene solo las columnas seleccionadas; esta transformación funciona en una sola columna o en varias.

## <a name="handle-path-column"></a>Manipulación de la columna de ruta de acceso
Durante la importación de un archivo, se agrega automáticamente una columna de la ruta de acceso al conjunto de datos mediante el asistente. Esta contiene el nombre de archivo completo, que constituye la ruta de acceso al conjunto de datos. Esta transformación agrega o quita esa columna adicional del conjunto de datos.

## <a name="convert-field-type-to-numeric"></a>Conversión del tipo de campo a numérico
Cambia el tipo de columna a un valor numérico. Especifique el separador si hay datos no enteros. De forma predeterminada, esta transformación no solicita el separador; así pues, invoque el editor mediante el elemento del menú **Editar**. Esta transformación funciona en una sola columna o en varias.

## <a name="convert-field-type-to-date"></a>Conversión del tipo de campo a fecha
Cambia el tipo de columna a un valor de fecha. De forma predeterminada se utiliza un formato de fecha y hora, pero es posible anularlo mediante directivas `strftime`. También es posible anteponer los valores de hora con una fecha fija.

De forma predeterminada, esta transformación no solicita el formato; así pues, invoque el editor mediante el elemento del menú **Editar** en el paso resultante. Esta transformación funciona en una sola columna o en varias.

Únicamente se pueden convertir al tipo de fecha los valores entre 22/09/1677 y 11/04/2262.

## <a name="convert-field-type-to-boolean"></a>Conversión del tipo de campo a booleano
Cambia el tipo de columna a un valor true/false. Esta transformación admite la asignación de varios valores a true o false, y es posible editar estas asignaciones. También admite una constante a la que asignar valores que no existen en las tablas de asignación de true/false. Esta transformación funciona en una sola columna o en varias.

## <a name="convert-field-type-to-string"></a>Conversión del tipo de campo a cadena
Cambia el tipo de columna a un valor de cadena. Esta transformación funciona en una sola columna o en varias.

## <a name="convert-unix-timestamp-to-datetime"></a>Conversión de marca de tiempo UNIX a DateTime
Esta transformación entiende el formato de marca de tiempo UNIX, incluso si se representa como una cadena en los datos, y lo convierte correctamente a un tipo de fecha en la preparación de los datos.

## <a name="filter"></a>Filtrar
Con la transformación de filtrado es posible filtrar filas en función de los valores de una o varias columnas, las condiciones del filtro dependen del tipo de datos de cada columna; por lo tanto, es posible filtrar columnas de cadena por "contiene" o "no contiene", pero no en valores numéricos. Sin embargo, en las columnas numéricas es posible aplicar las condiciones "mayor que" o "menor que", pero no en las columnas de cadena.

En caso de que la transformación de filtrado se invoque desde el menú principal o mediante clic derecho en el encabezado de una columna, estará disponible la opción de bifurcar las filas de error en otro flujo de datos. Por lo tanto el flujo de datos principal continúa con las filas que el filtrado ha **incluido** y se crea un nuevo flujo de datos que contiene todas las filas que el filtrado ha **excluido**.

## <a name="use-first-row-as-headers"></a>Uso de la primera fila como encabezado
Esta transformación promueve la primera fila del conjunto de datos para convertirla en el nombre de la columna; en caso de que alguna columna no tenga datos en la primera fila, se genera un nombre automáticamente. El uso de esta transformación implica que la importación de los datos se inicia como muy pronto en la fila 2. Dependiendo de la configuración de la omisión de filas, la importación puede iniciarse incluso más abajo en el conjunto de datos. También puede utilizarse para quitar la promoción a fin de contar solo con nombres generados automáticamente.

## <a name="join"></a>Unión
Esta transformación se usa para unir dos flujos de datos. Permite seleccionar qué valor resultante de la combinación debe ser el definitivo; las filas que se unan correctamente, las filas con error a la "izquierda" de la combinación o las filas con error a la "derecha" de la combinación.

El asistente para la combinación se inicia desde un único flujo de datos y selecciona ese flujo de datos como una parte de la combinación; a continuación, pide al usuario otro flujo de datos para el otro lado de la combinación. Una vez que haya seleccionado los dos flujos, el asistente requiere la selección de una sola columna de cada lado para llevar a cabo la combinación. En caso de que la combinación necesite más de una columna, cree una columna derivada antes de iniciar el asistente para crear una nueva columna (concatenada) que se utilizará solo para la combinación. El asistente intenta sugerir las claves de combinación y, si es posible, generar la columna derivada automáticamente.

Una vez que se ha completado la combinación, se presenta una vista de diagrama de Sankey de la combinación, el ancho de una línea en relación con otra representa el número de filas que se mueve a través de esa parte del flujo de combinación. El panel de la derecha permite la selección de las filas correctas, con error a la izquierda o con error a la derecha exclusivamente, o es posible elegir una sola rama.

## <a name="append-rows"></a>Anexión de filas
Esta transformación anexa datos de otro flujo de datos al actual; asigna las columnas por posición para agregar las nuevas filas al final.

## <a name="append-columns"></a>Anexión de columnas
Esta transformación anexa nuevas columnas de otro flujo de datos al actual; agrega las nuevas columnas a la derecha y no intenta alinear datos en filas.

## <a name="summarize"></a>Resumen
Seleccione una o varias columnas y calcule los agregados para esa combinación de valores únicos.

Los agregados admiten lo siguiente: COUNT, SUM, MIN, MAX, MEAN, VARIANCE, STANDARD DEVIATION. La lista de agregados para una columna determinada se filtra para dejar solo aquellos correspondientes a ese tipo de datos. Los agregados a los que no se aplica el filtrado se quedan en color gris. Por ejemplo, no es posible calcular la media de una columna de cadena, pero es posible calcular el valor mínimo y máximo.

Funciona de manera similar a GROUP BY de ANSI-SQL.

Una vez que el editor esté disponible, arrastre desde el encabezado de columna hasta el panel de la esquina superior izquierda: aquí se muestran las columnas para agregar. Este panel es jerárquico, por lo que es posible hacer agregados anidados. El panel del editor de la esquina superior derecha se utiliza para seleccionar qué agregado se aplica a una columna. Una sola columna se puede agregar una o varias veces. Una vez que se ha elegido al menos un agregado, la cuadrícula de la parte inferior derecha muestra una vista previa de los datos de su formato agregado. 

## <a name="remove-duplicates"></a>Retirada de duplicados
Esta transformación quita toda la fila en la que hay valores duplicados. Los valores duplicados se definen mediante una o varias columnas que se han seleccionado en la invocación de la transformación o recurriendo al editor. Si no se ha elegido ninguna columna, las únicas filas que se quitan son aquellas en las que todos los valores de la columna son los mismos.

## <a name="sort"></a>Sort
Esta transformación ordena los datos, puede realizarse por una sola columna o varias, y cada una de ellas se puede ordenar de manera ascendente (el valor predeterminado) o descendente (puede cambiarse desde el editor).

Funciona de manera similar a ORDER BY de ANSI-SQL.

## <a name="output-transforms"></a>Transformaciones de salida
Las transformaciones siguientes generan los datos. Es posible tener varios bloques de escritura en un único flujo para que pueda escribir los datos en distintos puntos.

### <a name="write-to-csv"></a>Escritura en CSV
Esta transformación escribe los datos en formato CSV desde el punto actual del flujo de datos.  Permite un control de la ubicación (local o remota) y varias opciones de configuración en el archivo.

### <a name="write-to-parquet"></a>Escritura en Parquet
Esta transformación escribe los datos en formato Parquet desde el punto actual del flujo de datos. Permite un control de la ubicación (local o remota) y varias opciones de configuración en el archivo.

## <a name="script-based-transforms"></a>Transformaciones basadas en script
Todas las transformaciones que siguen usan script (Python) para realizar la funcionalidad que falta en el producto principal. 
[Antes de utilizar cualquiera de estas transformaciones, lea sobre la extensibilidad en este documento](data-prep-python-extensibility-overview.md):

### <a name="add-column-script"></a>Adición de columna (script)
Permite la adición de una columna a los datos mediante una expresión de Python. [Vea el anexo 10 para obtener más información](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Filtro avanzado (script)
Permite que se escriba un filtro a nivel de fila de Python. [Vea el anexo 6 para obtener más información](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Transformación de flujo de datos (script)
Permite la aplicación de Python en todo un conjunto de datos.

[Consulte el anexo 7 para obtener más información](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="transform-dataflow-script"></a>Transformación de flujo de datos (script)
Permite la aplicación de Python en toda una partición de datos.

[Consulte el anexo 7 para obtener más información](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Escritura de flujo de datos (script)
Permite la aplicación de Python para la escritura en todo un conjunto de datos. [Vea el anexo 10 para obtener más información](data-prep-appendix9-sample-destination-connections-python.md).




