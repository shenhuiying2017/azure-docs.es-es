---
title: "Uso de transformaciones de datos para la preparación de datos en Azure Machine Learning | Microsoft Docs"
description: "En este artículo se ofrece una lista completa de las transformaciones disponibles para la preparación de datos en Azure Machine Learning."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 10/09/2017
ms.openlocfilehash: 062143ae34450484cd66cd7364fb8839d697e483
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Uso de transformaciones de datos para la preparación de datos en Azure Machine Learning

En Azure Machine Learning, una *transformación* consume datos con un formato dado, realiza una operación en los datos (por ejemplo, cambiar el tipo de datos) y, luego, genera datos con el nuevo formato. Cada transformación tiene su propia interfaz y su propio comportamiento. Al encadenar varias transformaciones entre sí a través de los pasos del flujo de datos, puede realizar transformaciones complejas y repetibles de los datos. Este es el núcleo de la funcionalidad de preparación de datos.

A continuación, se detallan las transformaciones disponibles en Azure Machine Learning. 

## <a name="column-selection"></a>Selección de columnas 
Muchas de las transformaciones de esta lista funcionan en una única columna o en varias. Para seleccionar varias columnas, use la tecla Ctrl. Para seleccionar un intervalo de columnas, utilice la tecla Mayús.

## <a name="transforms-from-the-main-menu-or-the-grid-header"></a>Transformaciones desde el menú principal o el encabezado de la cuadrícula 
Acceda a las transformaciones desde la opción Transformaciones del menú principal. También puede seleccionarlas haciendo clic con el botón derecho en el nombre de la columna en la cuadrícula de datos. Si se seleccionan varias columnas, al hacer clic con el botón derecho en cualquiera de ellas, aparece el menú de transformaciones.

En el menú contextual solo se muestran transformaciones válidas para el tipo de datos seleccionado. El menú principal ofrece todas las transformaciones, pero deshabilita las que no son relevantes para las columnas seleccionadas.

Al hacer clic con el botón derecho en una celda, aparece un pequeño subconjunto de transformaciones contextuales. Estas transformaciones son Copiar, Reemplazar y Filtrar. Las transformaciones dependen del tipo de datos, por lo que las opciones de una columna de número son distintas de las de una columna de cadena.

## <a name="derive-column-by-example"></a>Derivación de columnas por ejemplos
Use esta transformación para crear una columna como derivada de una o varias columnas existentes. Las transformaciones examinan las columnas de entrada (seleccionadas) y el ejemplo presentado, para determinar el resultado deseado en la nueva columna. 

Para usar esta transformación, seleccione una o varias columnas. Agregue una nueva columna derivada (en blanco) por ejemplo. Escriba un ejemplo de lo que desea ver en la columna derivada (suponiendo que se derive de otras columnas) y la tecnología de "Por ejemplo" intenta rellenar las restantes celdas de la columna. 

Para ejemplos complicados, puede que sea necesario proporcionar más de un ejemplo. Para ello, seleccione otra celda y escriba otro ejemplo.

La tecnología de "Por ejemplo" usa las columnas seleccionadas para intentar determinar el significado de un ejemplo. Si no hay columnas seleccionadas cuando se invoca esta transformación, se utilizan todas las celdas de la fila actual. La selección de únicamente las columnas necesarias genera resultados más precisos.

Puede seleccionar columnas antes de invocar la transformación. Cuando se abre el editor de transformaciones, las casillas de la parte superior de cada columna indican qué columnas se seleccionan como entradas. Para agregar columnas a la selección, o quitarlas de ella, utilice las casillas de los encabezados de las columnas.

Para una explicación más detallada de la transformación **Derivación de columnas por ejemplos**, junto con más ejemplos, consulte la referencia [Transformación Derivar columna por ejemplos](data-prep-derive-column-by-example.md).  

## <a name="split-column-by-example"></a>División de columnas por ejemplos
Esta transformación toma una columna existente y, con el motor de "Por ejemplo", intenta dividir esa columna en *n* otras columnas. Puede ejecutar la división automática en las posteriores columnas generadas.

Para obtener una explicación más detallada de la transformación **Dividir columna por ejemplo**, junto con más ejemplos, consulte la referencia [Transformación Dividir columna por ejemplo](data-prep-split-column-by-example.md).

## <a name="expand-json"></a>Expansión de JSON

Esta transformación permite agregar varias columnas mediante la expansión de una columna con texto JSON válido.

Para obtener una explicación más detallada de la transformación **Expandir JSON**, junto con más ejemplos, consulte la referencia [Transformación Expandir JSON](data-prep-expand-json.md).


## <a name="combine-columns-by-example"></a>Combinación de columnas por ejemplos

Esta transformación agrega una nueva columna mediante la combinación de valores de varias columnas. 

Para obtener una explicación más detallada de la transformación **Combinar columnas por ejemplos**, junto con más ejemplos, consulte la referencia [Transformación Combinar columnas por ejemplos](data-prep-combine-columns-by-example.md).


## <a name="duplicate-column"></a>Duplicación de columna
Esta transformación crea una copia exacta de una o varias columnas seleccionadas y asigna a cada una de ellas un nuevo nombre derivado del nombre de columna original.

## <a name="text-clustering"></a>Agrupación en clústeres de texto 
Esta transformación está diseñada para tomar los valores incoherentes que deberían ser iguales y agruparlos.  

Con esta transformación, se analizan los valores en una sola columna para detectar su similitud y, posteriormente, se agrupan en clústeres. Para cada clúster, hay un valor canónico, que es el valor que reemplaza todas las instancias del clúster y los valores de todas las instancias. Los clústeres completos se pueden quitar y el valor canónico se puede editar. Las instancias se pueden quitar de un clúster determinado. Se puede cambiar el filtro de umbral de puntuación de similitud que se usa para agrupar las instancias en un clúster.

De forma predeterminada, esta transformación reemplaza todos los valores de la instancia de clúster por el valor canónico de dicho clúster, creando una columna que contiene los nuevos valores. También puede agregar la puntuación de similitud de cada instancia a una nueva columna (a la que se le puede poner un nombre) para usarla más adelante en el flujo de datos.

## <a name="replace-values"></a>Reemplazo de valores
Utilice esta transformación para reemplazar una cadena con otra. La cadena de origen puede ser una cadena parcial o una celda completa, y la sustitución se puede aplicar a una sola columna o a muchas. La cadena de búsqueda admite la búsqueda de caracteres especiales, así como de caracteres normales. 

## <a name="replace-na-values"></a>Sustitución de valores NA
Use esta transformación para reemplazar las diferentes formas de NA (N/A, NA, null, NaN, etc.) o las cadenas vacías por un valor único para que sean coherentes. Esta transformación es compatible con una o varias columnas, y se muestra únicamente cuando se selecciona una columna. No aparece en el menú de la transformación principal cuando no se selecciona ninguna columna.

## <a name="replace-missing-values"></a>Sustitución de valores ausentes
Esta transformación sustituye los datos que faltan por un valor individual y admite una o varias columnas. Esta transformación se muestra únicamente cuando se selecciona una columna. No aparece en el menú de la transformación principal cuando no se selecciona ninguna columna.

## <a name="replace-error-values"></a>Sustitución de valores de error
Esta transformación sustituye los errores por un valor individual y admite una o varias columnas. Esta transformación se muestra únicamente cuando se selecciona una columna. No aparece en el menú de la transformación principal cuando no se selecciona ninguna columna.

## <a name="trim-string"></a>Recorte de cadena
Esta transformación quita los caracteres de "espacio en blanco" iniciales y finales (lo que incluye espacios, tabulaciones, etc.), de una o varias columnas.

## <a name="adjust-precision"></a>Ajuste de la precisión
Esta transformación establece el número de posiciones decimales para una columna numérica.

## <a name="rename-column"></a>Cambio del nombre de columna
Esta transformación cambia el nombre de la columna seleccionada. También puede invocarla alineada en el encabezado de la columna haciendo clic en el nombre de esta.

## <a name="remove-column"></a>Retirada de columna
Esta transformación quita las columnas seleccionadas y funciona en una sola columna o en varias. 

## <a name="keep-column"></a>Mantenimiento de columna
Esta transformación mantiene solo las columnas seleccionadas y funciona en una sola columna o en varias.

## <a name="handle-path-column"></a>Manipulación de la columna de ruta de acceso
Durante la importación de un archivo, la característica **Agregar origen de datos** agrega automáticamente una columna de la ruta de acceso al conjunto de datos. Esta contiene el nombre de archivo completo, que constituye la ruta de acceso al conjunto de datos. Esta transformación agrega o quita esa columna adicional del conjunto de datos.

## <a name="convert-field-type-to-numeric"></a>Conversión del tipo de campo a numérico
Esta transformación, cambia el tipo de columna a numérico. Puede especificar el separador para datos no enteros. De forma predeterminada, esta transformación no solicita el separador; así pues, invoque el editor mediante el elemento de menú **Editar**. Esta transformación funciona en una sola columna o en varias.

## <a name="convert-field-type-to-date"></a>Conversión del tipo de campo a fecha
Esta transformación cambia el tipo de columna a fecha. De forma predeterminada se utiliza un formato de fecha y hora, pero es posible anularlo mediante directivas `strftime`. También puede anteponer los valores de hora con una fecha fija.

De forma predeterminada, esta transformación no solicita el formato; así pues, invoque el editor mediante el elemento de menú **Editar** en el paso resultante. Esta transformación funciona en una sola columna o en varias.

Únicamente se pueden convertir al tipo de fecha los valores entre 22/09/1677 y 11/04/2262.

## <a name="convert-field-type-to-boolean"></a>Conversión del tipo de campo a booleano
Esta transformación cambia el tipo de columna a true/false. Admite la asignación de varios valores a true o false, y puede editar estas asignaciones. También admite una constante a la que puede asignar aquellos valores que no existan en las tablas de asignación de true o false. Esta transformación funciona en una sola columna o en varias.

## <a name="convert-field-type-to-string"></a>Conversión del tipo de campo a cadena
Esta transformación cambia el tipo de columna a cadena y funciona en una sola columna o en varias.

## <a name="convert-unix-timestamp-to-datetime"></a>Conversión de marca de tiempo UNIX a DateTime
Esta transformación entiende el formato de marca de tiempo UNIX, incluso si se representa como una cadena en los datos. Lo convierte correctamente a un tipo de fecha en la preparación de los datos.

## <a name="filter"></a>Filtrar
Esta transformación admite el filtrado de filas en función de los valores de una o varias columnas. Las condiciones del filtro dependen del tipo de datos de cada columna, para que pueda filtrar las columnas de cadena por "contiene" o "no contiene". Las columnas numéricas se pueden filtrar por las condiciones"mayor que" o "menor que".

Si la transformación de **filtrado** se invoca desde el menú principal o al hacer clic con el botón derecho en el encabezado de una columna, estará disponible la opción de bifurcar las filas de error en otro flujo de datos. El flujo de datos principal continúa con las filas que el filtrado ha **incluido** y se crea un flujo de datos que contiene todas las filas que el filtrado ha **excluido**.

## <a name="use-first-row-as-headers"></a>Uso de la primera fila como encabezado
Esta transformación promueve que la primera fila del conjunto de datos sean los nombres de columna. Si algunas columnas no tienen datos en la primera fila, se genera automáticamente un nombre. El uso de esta transformación implica que la importación de los datos se inicia como muy pronto en la fila 2. Dependiendo de la configuración de la **omisión de filas**, la importación puede iniciarse incluso más abajo en el conjunto de datos. También puede usarla para quitar la promoción y usar solo los nombres generados automáticamente.

## <a name="join"></a>Unión
Esta transformación se usa para unir dos flujos de datos. Permite seleccionar qué salida de la combinación debe ser el resultado: las filas correctas de la combinación, las filas con error a la "izquierda" de la combinación o las filas con error a la "derecha" de la combinación.

La transformación de **combinación** se inicia desde un flujo de datos individual y selecciona dicho flujo de datos como una parte de la combinación. Luego, le solicita que seleccione otro flujo de datos para el otro lado de la combinación. Una vez que haya seleccionado los dos flujos, la transformación requiere la selección de una sola columna de cada lado para llevar a cabo la combinación. En caso de que la combinación necesite más de una columna, cree una columna derivada antes de iniciar la transformación para crear una columna concatenada que se utilizará solo para la combinación. La transformación intenta sugerir las claves de combinación y, si es posible, generar la columna derivada automáticamente.

Una vez que se ha completado la combinación, se presenta una vista del diagrama de Sankey de la combinación. El ancho de las líneas entre ellas representa el número de filas que se mueven a través de esa parte del flujo de la combinación. El panel de la derecha le permite seleccionar las filas sin errores, o bien solamente las de la izquierda con errores o las de la derecha con errores. También puede elegir una sola rama.

## <a name="append-rows"></a>Anexión de filas
Esta transformación anexa los datos de otro flujo de datos al actual. Asigna las columnas por posición para agregar las filas nuevas al final.

## <a name="append-columns"></a>Anexión de columnas
Esta transformación anexa columnas nuevas de otro flujo de datos al actual. Agrega las nuevas columnas a la derecha. No intente alinear los datos en las filas.

## <a name="summarize"></a>Resumen
Esta transformación calcula agregados para la combinación de valores únicos de una o varias columnas seleccionadas. 

Los agregados que se admiten son: COUNT, SUM, MIN, MAX, MEAN, VARIANCE y STANDARD DEVIATION. La lista de agregados de una columna determinada se filtra solo por los agregados correspondientes al tipo de datos. Los que no son correspondientes se deshabilitan. Por ejemplo, no es posible calcular la MEDIA de una columna de cadena, pero se pueden calcular tanto el MÍNIMO como el MÁXIMO.

Una vez que el editor esté disponible, arrastre desde el encabezado de columna hasta el panel de la esquina superior izquierda, donde se muestran las columnas para agregar. Este panel es jerárquico, por lo que puede realizar agregados anidados. El panel del editor de la esquina superior derecha se utiliza para seleccionar qué agregado se aplica a una columna. Una sola columna se puede agregar una o varias veces. Una vez que se ha elegido al menos un agregado, la cuadrícula de la parte inferior derecha muestra una vista previa de los datos en su formato agregado. 

Esta transformación es análoga a `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Retirada de duplicados
Esta transformación quita toda la fila en la que hay valores duplicados en una o varias columnas seleccionadas. Si no se ha elegido ninguna columna, las únicas filas que se quitan son aquellas en las que todos los valores de la columna son los mismos.

## <a name="sort"></a>Sort
Esta transformación ordena los datos. La ordenación se puede realizar por una sola columna o por varias, y cada una de ellas se puede ordenar de manera ascendente (opción predeterminada) o descendente (puede cambiarse desde el editor).

Esta transformación es análoga a `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Transformaciones de salida
Las siguientes transformaciones generan datos. Puede tener varios bloques de escritura en un único flujo para escribir los datos en distintos puntos.

### <a name="write-to-csv"></a>Escritura en CSV
Esta transformación escribe los datos en formato CSV desde el punto actual del flujo de datos. Controla la ubicación (local o remota) y varias opciones de configuración en el archivo.

### <a name="write-to-parquet"></a>Escritura en Parquet
Esta transformación escribe los datos en formato Parquet desde el punto actual del flujo de datos. Controla la ubicación (local o remota) y varias opciones de configuración en el archivo.

## <a name="script-based-transforms"></a>Transformaciones basadas en script
Las siguientes transformaciones usan un script (Python) para realizar la funcionalidad que falta en el producto principal. 

>[!NOTE]
>Antes de utilizar cualquiera de estas transformaciones, lea [Extensiones de Python para la preparación de datos](data-prep-python-extensibility-overview.md).

### <a name="add-column-script"></a>Adición de columna (script)
Esta transformación agrega una columna a los datos mediante una expresión de Python.
Para más información, consulte [Ejemplo de código de Python para derivar nuevas columnas](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Filtro avanzado (script)
Use esta transformación para escribir un filtro de nivel de fila de Python.
Para más información, consulte [Ejemplo de expresiones de filtro](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Transformación de flujo de datos (script)
Esta transformación aplica Python a todo un conjunto de datos.
Para más información, consulte [Ejemplo de transformaciones de flujo de datos personalizados](data-prep-appendix7-sample-transform-data-flow-python.md).

Esta transformación también puede aplicar Python a toda una partición de datos.
Para más información, consulte [Ejemplo de transformaciones de flujo de datos personalizados](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Escritura de flujo de datos (script)
Esta transformación usa Python para escribir todo un conjunto de datos.
Para más información, consulte [Ejemplo de Python para derivar nuevas columnas](data-prep-appendix9-sample-destination-connections-python.md).



