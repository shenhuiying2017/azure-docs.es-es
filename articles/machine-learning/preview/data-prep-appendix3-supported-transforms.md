---
title: "Uso de transformaciones de datos para la preparación de datos en Azure Machine Learning | Microsoft Docs"
description: "En este artículo encontrará una lista completa de las transformaciones disponibles para la preparación de datos en Azure Machine Learning."
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
ms.date: 10/09/2017
ms.openlocfilehash: 4331bb4a16d56f027dd63a97868822fa6ecc92d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Uso de transformaciones de datos para la preparación de datos en Azure Machine Learning

En Azure Machine Learning, una *transformación* consume datos con un formato dado, realiza una operación en los datos (por ejemplo, cambiar el tipo de datos) y, luego, genera datos con el nuevo formato. Cada transformación tiene su propia interfaz y su propio comportamiento. Se pueden encadenar varias transformaciones entre sí a través de los pasos del flujo de datos, lo que permite realizar transformaciones complejas y repetibles en los datos. Este es el núcleo de la funcionalidad de preparación de datos.

La siguiente es una lista de las transformaciones disponibles en Azure Machine Learning. 

## <a name="column-selection"></a>Selección de columnas 
Muchas de las transformaciones que se describen a continuación funcionan tanto en una sola columna como en varias. Para seleccionar varias columnas, use la tecla **Ctrl**, mientras que para seleccionar un intervalo de columnas, debe usar la tecla **Mayús**.

## <a name="transforms-from-main-menu-andor-grid-header"></a>Transformaciones desde el menú principal o el encabezado de la cuadrícula 
Es posible tener acceso a las transformaciones desde la opción Transformaciones del menú principal, pero también se pueden seleccionar haciendo clic con el botón derecho en el nombre de la columna en la cuadrícula de datos. Si se seleccionan varias columnas, al hacer clic con el botón derecho en cualquiera de ellas aparece el menú de transformaciones.

En el menú contextual solo se ofrecen transformaciones válidas para el tipo de datos seleccionado. El menú principal ofrece todas las transformaciones, pero deshabilita las que no sean relevantes para las columnas seleccionadas.

Al hacer clic con el botón derecho en una celda, aparece un pequeño subconjunto de transformaciones contextuales. Estas transformaciones son Copiar, Reemplazar y Filtrar. Las transformaciones dependen del tipo de datos, por lo que las opciones de una columna de número son distintas de las de una columna de cadena.

## <a name="derive-column-by-example"></a>Derivación de columnas por ejemplos
Esta transformación permite la creación de una nueva columna como derivada de una o varias columnas existentes. Las transformaciones examinan las columnas de entrada (seleccionadas) y el ejemplo presentado, para determinar el resultado deseado en la nueva columna. 

Para usar esta transformación, seleccione una o varias columnas. Agregue una nueva columna derivada (en blanco) por ejemplo. Escriba un ejemplo de lo que desea ver en la columna derivada (suponiendo que se derive de otras columnas) y la tecnología de "Por ejemplo" intentará rellenar las restantes celdas de la columna. 

En caso de ejemplos complicados, puede que sea necesario proporcionar más de un ejemplo. Para ello, seleccione otra celda y escriba otro ejemplo.

La tecnología de "Por ejemplo" usa las columnas seleccionadas para intentar determinar el significado de un ejemplo. Si no hay columnas seleccionadas cuando se invoca esta transformación, se utilizan todas las celdas de la fila actual. La selección de únicamente las columnas necesarias genera resultados más precisos.

Puede seleccionar columnas antes de invocar la transformación. Una vez que se ha iniciado el editor de transformaciones, las casillas de la parte superior de cada columna indican qué columnas se seleccionan como entradas. Para agregar columnas a la selección, o quitarlas de ella, utilice las casillas de los encabezados de las columnas.

Para una explicación más detallada de la transformación **Derivación de columnas por ejemplos**, junto con más ejemplos, consulte la referencia [Transformación Derivar columna por ejemplos](data-prep-derive-column-by-example.md).  

## <a name="split-column-by-example"></a>División de columnas por ejemplos
Esta transformación toma una columna existente y, con el motor de "Por ejemplo", intenta dividir esa columna en *n* otras columnas. La división automática se puede ejecutar en las posteriores columnas generadas.

Para obtener una explicación más detallada de la transformación **Dividir columna por ejemplo**, junto con más ejemplos, consulte la referencia [Transformación Dividir columna por ejemplo](data-prep-split-column-by-example.md).

## <a name="expand-json"></a>Expansión de JSON

Esta transformación permite agregar varias columnas mediante la expansión de una columna con texto JSON válido.

Para obtener una explicación más detallada de la transformación **Expandir JSON**, junto con más ejemplos, consulte: [Transformación Expandir JSON](data-prep-expand-json.md).


## <a name="combine-columns-by-example"></a>Combinación de columnas por ejemplos

Esta transformación permite agregar una nueva columna mediante la combinación de valores de varias columnas. 

Para obtener una explicación más detallada de la transformación **Combinar columnas por ejemplos**, junto con más ejemplos, consulte [Transformación Combinar columnas por ejemplos](data-prep-combine-columns-by-example.md).


## <a name="duplicate-column"></a>Duplicación de columna
Esta transformación crea una copia exacta de una o varias columnas seleccionadas y asigna a cada una de ellas un nuevo nombre derivado del nombre de columna original.

## <a name="text-clustering"></a>Agrupación en clústeres de texto 
Esta transformación está diseñada para tomar los valores incoherentes que deberían ser iguales y agruparlos.  

Cuando se usa esta transformación, se analizan los valores en una sola columna para detectar su similitud y, posteriormente, se agrupan en clústeres. Para cada clúster, hay un valor canónico, que es el valor que reemplaza todas las instancias del clúster y los valores de la instancia. Los clústeres completos se pueden quitar y el valor canónico se puede editar. Las instancias se pueden quitar de un clúster determinado. Además, se puede cambiar el filtro de umbral de puntuación de similitud que se usa para agrupar las instancias en un clúster.

De forma predeterminada, esta transformación reemplaza todos los valores de la instancia de clúster por el valor canónico de dicho clúster, creando una nueva columna que contiene los nuevos valores. También es posible agregar la puntuación de similitud de cada instancia a una nueva columna (a la que se le puede poner un nombre) para permitir su uso más adelante en el flujo de datos.

## <a name="replace-values"></a>Reemplazo de valores
Esta transformación permite sustituir una cadena por otra. La cadena de origen puede ser una cadena parcial o una celda completa; la sustitución se puede aplicar a una sola columna o a muchas. La cadena de búsqueda admite la búsqueda de caracteres especiales, así como caracteres normales. 

## <a name="replace-na-values"></a>Sustitución de valores NA
Esta transformación permite la sustitución de las diferentes formas de NA (N/A, NA, null, NaN, etc.) o de las cadenas vacías por un valor único para que sean coherentes. Esta transformación admite una o varias columnas. Esta transformación se muestra únicamente cuando una columna está seleccionada y no figura en el menú de transformación principal en el momento en que no hay ninguna columna seleccionada.

## <a name="replace-missing-values"></a>Sustitución de valores ausentes
Esta transformación sustituye los datos que faltan por un valor individual. Esta transformación admite una o varias columnas. Esta transformación se muestra únicamente cuando una columna está seleccionada y no figura en el menú de transformación principal en el momento en que no hay ninguna columna seleccionada.

## <a name="replace-error-values"></a>Sustitución de valores de error
Esta transformación sustituye los errores por un valor individual. Esta transformación admite una o varias columnas. Esta transformación se muestra únicamente cuando una columna está seleccionada y no figura en el menú de transformación principal en el momento en que no hay ninguna columna seleccionada.

## <a name="trim-string"></a>Recorte de cadena
Esta transformación quita los caracteres de "espacio en blanco" iniciales y finales (lo que incluye espacios, tabulaciones, *etc.*), de una o varias columnas.

## <a name="adjust-precision"></a>Ajuste de la precisión
Esta transformación permite establecer el número de posiciones decimales de una columna numérica.

## <a name="rename-column"></a>Cambio del nombre de columna
Esta transformación cambia el nombre de la columna seleccionada. Esta transformación también se puede invocar desde el propio encabezado de la columna haciendo clic en el nombre de esta.

## <a name="remove-column"></a>Retirada de columna
Esta transformación quita las columnas seleccionadas. Esta transformación funciona en una sola columna o en varias. 

## <a name="keep-column"></a>Mantenimiento de columna
Esta transformación conserva solo las columnas seleccionadas. Esta transformación funciona en una sola columna o en varias.

## <a name="handle-path-column"></a>Manipulación de la columna de ruta de acceso
Durante la importación de un archivo, el Asistente para agregar origen de datos agrega automáticamente una columna de la ruta de acceso se agrega automáticamente al conjunto de datos. Esta contiene el nombre de archivo completo, que constituye la ruta de acceso al conjunto de datos. Esta transformación agrega o quita esa columna adicional del conjunto de datos.

## <a name="convert-field-type-to-numeric"></a>Conversión del tipo de campo a numérico
Esta transformación, cambia el tipo de columna a numérico. Si hay datos no enteros, se puede especificar el separador. De forma predeterminada, esta transformación no solicita el separador; así pues, invoque el editor mediante el elemento de menú **Editar**. Esta transformación funciona en una sola columna o en varias.

## <a name="convert-field-type-to-date"></a>Conversión del tipo de campo a fecha
Esta transformación cambia el tipo de columna a fecha. De forma predeterminada se utiliza un formato de fecha y hora, pero es posible anularlo mediante directivas `strftime`. También es posible anteponer los valores de hora con una fecha fija.

De forma predeterminada, esta transformación no solicita el formato; así pues, invoque el editor mediante el elemento de menú **Editar** en el paso resultante. Esta transformación funciona en una sola columna o en varias.

Únicamente se pueden convertir al tipo de fecha los valores entre 22/09/1677 y 11/04/2262.

## <a name="convert-field-type-to-boolean"></a>Conversión del tipo de campo a booleano
Esta transformación cambia el tipo de columna a true/false. Esta transformación admite la asignación de varios valores a true o false, y es posible editar estas asignaciones. También admite una constante a la que puede asignar aquellos valores que no existan en las tablas de asignación de true o false. Esta transformación funciona en una sola columna o en varias.

## <a name="convert-field-type-to-string"></a>Conversión del tipo de campo a cadena
Esta transformación cambia el tipo de columna a cadena. Esta transformación funciona en una sola columna o en varias.

## <a name="convert-unix-timestamp-to-datetime"></a>Conversión de marca de tiempo UNIX a DateTime
Esta transformación entiende el formato de marca de tiempo UNIX, incluso si se representa como una cadena en los datos, y lo convierte correctamente a un tipo de fecha en la preparación de los datos.

## <a name="filter"></a>Filtrar
Esta transformación admite el filtrado de filas en función de los valores de una o varias columnas. Las condiciones del filtro dependen del tipo de datos de cada columna, lo que permite a las columnas de cadena de filtro "contiene" o "no contiene". Las columnas numéricas se puede filtrar por las condiciones"mayor que" o "menor que".

Si la transformación de filtrado se invoca desde el menú principal o al hacer clic con el botón derecho en el encabezado de una columna, estará disponible la opción de bifurcar las filas de error en otro flujo de datos. Por tanto, el flujo de datos principal continúa con las filas que el filtrado ha **incluido** y se crea un nuevo flujo de datos que contiene todas las filas que el filtrado ha **excluido**.

## <a name="use-first-row-as-headers"></a>Uso de la primera fila como encabezado
Esta transformación promueve que la primera fila del conjunto de datos sean los nombres de columna. Si algunas columnas no tienen datos en la primera fila, se genera automáticamente un nombre. El uso de esta transformación implica que la importación de los datos se inicia como muy pronto en la fila 2. Dependiendo de la configuración de la omisión de filas, la importación puede iniciarse incluso más abajo en el conjunto de datos. También se puede utilizar para quitar la promoción y tener solo nombres generados automáticamente.

## <a name="join"></a>Unión
Esta transformación se usa para unir dos flujos de datos. Permite seleccionar qué salida de la combinación debería ser el resultado: las filas correctas de la combinación, las filas con error a la "izquierda" de la combinación o las filas con error a la "derecha" de la combinación.

El Asistente para combinación se inicia desde un flujo de datos individual y selecciona dicho flujo de datos como una parte de la combinación. Luego, le solicita otro flujo de datos para el otro lado de la combinación. Una vez que haya seleccionado los dos flujos, el asistente requiere la selección de una sola columna de cada lado para llevar a cabo la combinación. En caso de que la combinación necesite más de una columna, cree una columna derivada antes de iniciar el asistente para crear una nueva columna (concatenada) que se utilizará solo para la combinación. El asistente intenta sugerir las claves de combinación y, si es posible, generar la columna derivada automáticamente.

Una vez que se ha completado la combinación, se presenta una vista del diagrama de Sankey de la combinación. El ancho de las líneas entre ellas representa el número de filas que se mueven a través de esa parte del flujo de la combinación. El panel de la derecha le permite seleccionar las filas sin errores, o bien solamente las de la izquierda con errores o las de la derecha con errores. También es posible elegir sólo una rama.

## <a name="append-rows"></a>Anexión de filas
Esta transformación anexa los datos de otro flujo de datos al actual. Asigna las columnas por posición para agregar las filas nuevas al final.

## <a name="append-columns"></a>Anexión de columnas
Esta transformación anexa columnas nuevas de otro flujo de datos al actual. Agrega las columnas nuevas a la derecha; no intente alinear los datos en filas.

## <a name="summarize"></a>Resumen
Esta transformación calcula agregados para la combinación de valores únicos de una o varias columnas seleccionadas. 

Los agregados que se admiten son: COUNT, SUM, MIN, MAX, MEAN, VARIANCE y STANDARD DEVIATION. La lista de agregados de una columna determinada se filtra solo a los correspondientes al tipo de datos. Los que no son correspondientes se deshabilitan. Por ejemplo, no es posible calcular la media de una columna de cadena, pero se pueden calcular tanto el mínimo como el máximo.

Una vez que el editor esté disponible, arrastre desde el encabezado de columna hasta el panel de la esquina superior izquierda, donde se muestran las columnas que se van a agregar. Este panel es jerárquico, por lo que es posible realizar agregados anidados. El panel del editor de la esquina superior derecha se utiliza para seleccionar qué agregado se aplica a una columna. Una sola columna se puede agregar una o varias veces. Una vez que se ha elegido al menos un agregado, la cuadrícula de la parte inferior derecha muestra una vista previa de los datos de su formato agregado. 

Esta transformación es análoga a `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Retirada de duplicados
Esta transformación quita toda la fila en la que hay valores duplicados en una o varias columnas seleccionadas. Si no se ha elegido ninguna columna, las únicas filas que se quitan son aquellas en las que todos los valores de la columna son los mismos.

## <a name="sort"></a>Sort
Esta transformación ordena los datos. La ordenación se puede realizar por una sola columna o por varias, y cada una de ellas se puede ordenar de manera ascendente (el valor predeterminado) o descendente (puede cambiarse desde el editor).

Esta transformación es análoga a `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Transformaciones de salida
Las siguientes transformaciones generan datos. Es posible tener varios bloques de escritura en un único flujo para poder escribir los datos en distintos puntos.

### <a name="write-to-csv"></a>Escritura en CSV
Esta transformación escribe los datos en formato CSV desde el punto actual del flujo de datos. Permite el control de la ubicación (local o remota) y varias opciones de configuración en el archivo.

### <a name="write-to-parquet"></a>Escritura en Parquet
Esta transformación escribe los datos en formato Parquet desde el punto actual del flujo de datos. Permite el control de la ubicación (local o remota) y varias opciones de configuración en el archivo.

## <a name="script-based-transforms"></a>Transformaciones basadas en script
Las siguientes transformaciones usan un script (Python) para realizar la funcionalidad que falta en el producto principal. Antes de utilizar cualquiera de estas transformaciones, lea [Extensiones de Python para la preparación de datos](data-prep-python-extensibility-overview.md).

### <a name="add-column-script"></a>Adición de columna (script)
Esta transformación permite la adición de una columna a los datos mediante una expresión de Python.
Para más información, consulte [Ejemplo de código de Python para derivar nuevas columnas](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Filtro avanzado (script)
Esta transformación permite que se escriba un filtro a nivel de fila de Python.
Para más información, consulte [Ejemplo de expresiones de filtro](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Transformación de flujo de datos (script)
Esta transformación permite la aplicación de Python en todo un conjunto de datos.
Para más información, consulte [Ejemplo de transformaciones de flujo de datos personalizados](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="transform-dataflow-script"></a>Transformación de flujo de datos (script)
Esta transformación permite la aplicación de Python en toda una partición de datos.
Para más información, consulte [Ejemplo de transformaciones de flujo de datos personalizados](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Escritura de flujo de datos (script)
Esta transformación permite la aplicación de Python al escribir todo un conjunto de datos.
Para más información, consulte [Ejemplo de Python para derivar nuevas columnas](data-prep-appendix9-sample-destination-connections-python.md).



