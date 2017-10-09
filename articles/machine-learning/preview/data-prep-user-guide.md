---
title: "Guía completa sobre cómo usar la preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona información general y detallada sobre cómo solucionar problemas relacionados con los datos durante la preparación de datos con Azure ML."
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
ms.openlocfilehash: 22389ba85edb119acdd21b63f2deae2d71f31373
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---
# <a name="data-preparation-user-guide"></a>Guía de usuario sobre preparación de los datos 
La experiencia de preparación de datos proporciona numerosas funciones enriquecidas. A continuación se documentan las partes más importantes de la experiencia.

### <a name="step-execution-history-and-caching"></a>Ejecución, historial y almacenamiento en caché de pasos 
El historial de pasos de la preparación de datos mantiene una serie de memorias caché por motivos de rendimiento. Si hace clic en un paso y alcanza una memoria caché, no se vuelve a ejecutar. Si tiene un bloque de escritura al final del historial de pasos y va de un paso a otro pero no realiza ningún cambio, la operación de escritura no se desencadenará después de la primera vez. Si usted: 
- Hace cambios en el bloque de escritura
- Agrega un nuevo bloque de transformación y lo mueve encima del bloque de escritura para generar una invalidación de la caché
- Cambia las propiedades de un bloque situado encima del bloque de escritura para generar una invalidación de la caché
- Selecciona la actualización de una muestra (lo que invalida toda la caché)

Se producirá una nueva operación de escritura y se sobrescribirá la anterior.

### <a name="error-values"></a>Valores de error

Es posible que se produzca un error en las transformaciones de datos para un valor de entrada porque ese valor no puede controlarse de forma adecuada. Por ejemplo, en las operaciones de coerción de tipos, se produce un error en la coerción si el valor de la cadena de entrada no se puede convertir al tipo de destino especificado. Una operación de coerción de tipos podría ser la conversión de una columna de tipo de cadena a un tipo numérico o booleano. También podría serlo intentar duplicar una columna que no existe (el resultado de mover la operación Eliminar columna X antes de la operación Duplicar columna X).

En estos casos, la preparación de datos genera un **valor de error** como salida. Los valores de error indican que se ha producido un error en una operación anterior para el valor especificado. Internamente, se tratan como un tipo de valor de primera clase, pero su presencia no altera el tipo subyacente de una columna, ni siquiera si una columna consta únicamente de valores de error.

Los valores de error son fáciles de identificar. Se resaltan en rojo e incluyen el texto "Error". Para determinar la razón del error, mantenga el puntero sobre un valor para ver una descripción del error.

Los valores de error se propagan. Cuando se produce un valor de error, en la mayoría de los casos se propaga como un error en la mayoría de las operaciones. Hay tres maneras de quitarlos o reemplazarlos:

1) Sustituya
    -  Haga clic con el botón derecho en una columna y seleccione *Replace Error Values* (Reemplazar valores de error). Después, puede elegir un valor de reemplazo para cada valor de error que se encuentra en la columna.

2) Remove
    - La preparación de datos incluye filtros interactivos para conservar o quitar valores de error.
    - Haga clic con el botón derecho en una columna y seleccione *Filtrar columna*. Para conservar o quitar valores de error, cree una instrucción condicional con la condición *"is error"* (es error) o *"is not error"* (no es un error).

3) Use una expresión de Python para operar condicionalmente en valores de error. Para obtener más información, visite la [sección sobre extensiones de Python](data-prep-python-extensibility-overview.md).

### <a name="sampling"></a>muestreo
Un archivo de origen de datos toma datos sin procesar de uno o más orígenes, ya sea del sistema de archivos local o de una ubicación remota. El bloque de muestra le permite especificar si quiere trabajar con un subconjunto de los datos mediante la generación de ejemplos. Si trabaja con una muestra de los datos en lugar de con un conjunto de datos grande, normalmente obtendrá un mejor rendimiento al llevar a cabo operaciones en pasos posteriores.

Para cada archivo de origen de datos se pueden generar y almacenar varias muestras, pero solo se puede establecer una muestra como activa. Puede crear, editar o eliminar muestras en el Asistente para orígenes de datos o mediante la edición del bloque de muestra. Todos los archivos de preparación de datos que hacen referencia a un origen de datos usan de forma inherente la muestra especificada en el archivo de origen de datos.

Hay disponibles varias estrategias de muestreo, cada una de ellas con diferentes parámetros configurables.

#### <a name="top"></a>Top (Máximo)
Esta estrategia puede aplicarse a archivos locales o remotos. Toma las primeras N filas (especificadas por el valor de recuento) en el origen de datos.

#### <a name="random-n"></a>N aleatorio 
Esta estrategia solo puede aplicarse a archivos locales. Toma N filas aleatorias (especificadas por el valor de recuento) en el origen de datos. Puede proporcionar un valor de inicialización específico para garantizar que se genera la misma muestra, siempre que el valor de recuento sea el mismo.

#### <a name="random-"></a>% aleatorio 
Esta estrategia puede aplicarse a archivos locales o remotos. En ambos casos, debe proporcionarse un valor de inicialización y de probabilidad, de forma similar a la estrategia de N aleatorio.

Para obtener muestras de archivos remotos, deben proporcionarse parámetros adicionales.

- Generador de muestras 
  - Seleccione un clúster de Spark o un destino de proceso de Docker remoto para usarlo para la generación de muestras. Para que el destino de proceso aparezca en esta lista, debe crearse de antemano para este proyecto. Para crear destinos de proceso, siga los pasos indicados en la sección "Create a new Compute Target" ("Crear un destino de proceso") del artículo [How to use GPU in Azure Machine Learning](how-to-use-gpu.md) (Cómo usar GPU en Azure Machine Learning).
- Almacenamiento de muestras 
  - Proporcione una ubicación de almacenamiento intermedia para almacenar la muestra remota. Esta ruta de acceso debe ser un directorio diferente de la ubicación del archivo de entrada.

#### <a name="full-file"></a>Archivo completo 
Esta estrategia solo puede aplicarse a archivos locales, y se toma el archivo completo en el origen de datos. Si el archivo es demasiado grande, esta opción podría ralentizar futuras operaciones en la aplicación, por lo que quizás considere más adecuado usar otra estrategia de muestreo.


### <a name="forking-merging-and-appending"></a>Bifurcar, combinar y anexar

Cuando se aplica un filtro a un conjunto de datos, la operación divide los datos en dos conjuntos de resultados: uno representa los registros que cumplen el filtro y el otro representa los registros que no lo cumplen. En cualquier caso, el usuario puede elegir qué conjunto de resultados se mostrará. El usuario puede descartar el otro conjunto de datos o colocarlo en un nuevo flujo de datos. Esta última opción se conoce como *bifurcación*.

Para bifurcar, elija una columna, haga clic con el botón derecho y seleccione Filtrar columna.
- En "Quiero", seleccione *Conservar filas* para mostrar el conjunto de resultados que supera el filtro o *Quitar filas* para mostrar el conjunto con error.
- En "Condiciones", seleccione *Create Dataflow Containing the Filtered Out Rows* (Crear un flujo de datos que contenga las filas filtradas) para bifurcar el conjunto de resultados fuera de presentación en un nuevo flujo de datos.


Esta práctica suele llevarse a cabo para separar un conjunto de datos que requiere una preparación adicional. Después de tratar el conjunto de datos bifurcado, es común combinar los datos con el conjunto de resultados del flujo de datos original. Para llevar a cabo una "combinación" (la operación inversa a la "bifurcación"), realice una de las acciones siguientes:
- *Anexe filas*. Combine dos o más flujos de datos verticalmente (con respecto a las filas). 
- *Anexe columnas*. Combine dos o más flujos de datos horizontalmente (con respecto a las columnas).


>[!NOTE]
>Se produce un error en AppendColumns en caso de una colisión de columnas.


Después de una operación de combinación, un flujo de datos de origen hará referencia a uno o varios flujos de datos. La preparación de datos mostrará una notificación en la esquina inferior derecha de la aplicación, debajo de la lista de pasos.


Todas las operaciones realizadas en el flujo de datos al que se hace referencia requerirán que el flujo de datos principal actualice la muestra usada en el flujo de datos al que se hace referencia. En ese caso, un cuadro de diálogo de confirmación reemplazará la notificación de referencia al flujo de datos en la esquina inferior derecha. Ese cuadro de diálogo confirmará que debe actualizar el flujo de datos para sincronizar con los cambios realizados en cualquier flujo de datos de dependencia.

### <a name="list-of-appendices"></a>Lista de apéndices 
[Apéndice 2: Orígenes de datos admitidos](data-prep-appendix2-supported-data-sources.md)  
[Apéndice 3: Transformaciones admitidas](data-prep-appendix3-supported-transforms.md)  
[Apéndice 4: Inspectores admitidos](data-prep-appendix4-supported-inspectors.md)  
[Apéndice 5: Destinos admitidos](data-prep-appendix5-supported-destinations.md)  
[Apéndice 6: Expresiones de filtro de ejemplo en Python](data-prep-appendix6-sample-filter-expressions-python.md)  
[Apéndice 7: Expresiones de flujo de datos de transformación de ejemplo en Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
[Apéndice 8: Orígenes de datos de ejemplo en Python](data-prep-appendix8-sample-source-connections-python.md)  
[Apéndice 9: Conexiones de destino de ejemplo en Python](data-prep-appendix9-sample-destination-connections-python.md)  
[Apéndice 10: Transformaciones de columna de ejemplo en Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  

