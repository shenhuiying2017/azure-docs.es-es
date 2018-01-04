---
title: Asistente para el origen de datos de Azure para Azure Machine Learning | Microsoft Docs
description: "Explica el asistente para origen de datos del área de trabajo de AML"
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: ff0159facd693b83230c731eb7e76f0a9495fdf2
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="data-source-wizard"></a>Asistente para origen de datos #

El Asistente para origen de datos es una manera rápida y sencilla para poner un conjunto de datos en el área de trabajo de Azure Machine Learning sin necesidad de código. Es también el lugar donde puede seleccionar un ejemplo de estrategia para el conjunto de datos. 

## <a name="step-1-trigger-the-data-source-wizard"></a>Paso 1: Iniciar el Asistente para origen de datos ## 

Para pasar datos a un proyecto utilizando el Asistente para origen de datos. Seleccione el botón **+** situado junto al cuadro de búsqueda en la vista de datos y elija Agregar origen de datos. 

![agregar origen de datos](media/data-source-wizard/add-data-source.png)

## <a name="step-2-select-where-data-is-stored"></a>Paso 2: Seleccionar la ubicación en la que se almacenan los datos ##
En primer lugar, especifique cómo están actualmente en los datos. Pueden estar almacenados en un archivo plano o un directorio, un archivo Parquet, un archivo de Excel o una base de datos. Para más información, consulte los [orígenes de datos admitidos](data-prep-appendix2-supported-data-sources.md).

![paso 1](media/data-source-wizard/step1.png)

## <a name="step-3-select-data-file"></a>Paso 3: Seleccionar el archivo de datos ##
Para un archivo o un directorio, especifique la ruta de acceso de archivo. Elija en la lista desplegable la ubicación de los datos, puede ser una ruta de acceso local o Azure Blob Storage. 

Especifique la ruta de acceso escribiéndola o haciendo clic en **Examinar...** botón para examinar. Puede buscar un directorio, o uno o varios archivos.

Haga clic en **Finalizar** para aceptar los valores predeterminados para el resto de pasos o en Siguiente para continuar con el paso siguiente.


![paso 4](media/data-source-wizard/step2.png)

## <a name="step-4-choose-file-parameters"></a>Paso 4: Elegir los parámetros de archivo ##

El Asistente para origen de datos puede detectar automáticamente el tipo de archivo, los separadores y la codificación. También le mostrará un ejemplo del aspecto de los datos. Puede cambiar manualmente cualquiera de estos parámetros. 

![paso 5](media/data-source-wizard/step3.png)

## <a name="step-5-set-data-types-for-columns"></a>Paso 5: Establecer tipos de datos para las columnas ##

El Asistente para origen de datos detecta automáticamente los tipos de datos de las columnas del conjunto de datos. Si el asistente se deja alguno, o si desea aplicar un tipo de datos, puede cambiar manualmente el tipo de datos. La columna **SAMPLE OUTPUT DATA** (DATOS DE SALIDA DE EJEMPLO) muestra ejemplos del aspecto que tendrán los datos.

![paso 6](media/data-source-wizard/step4.png)

## <a name="step-6-choose-sampling-strategy-for-data"></a>Paso 6: Elegir la estrategia de muestreo de datos ##

Puede especificar una o varias estrategias de muestreo para el conjunto de datos y elegir una de ellas como la estrategia activa. El valor predeterminado es cargar las primeras 10000 filas. Puede modificar este ejemplo haciendo clic en el botón **Editar** situado en la barra de herramientas, o agregar una nueva estrategia haciendo clic en + Nuevo. Las estrategias que se admiten actualmente son

-     Número máximo de filas
-     Número aleatorio de filas
-     Porcentaje aleatorio de filas
-     Archivo completo

Puede especificar tantas estrategias de muestreo como desee, pero solo hay una que se pueden establecer como activa al preparar los datos. Puede establecer cualquier estrategia como activa seleccionando la estrategia y haciendo clic en Establecer como activa en la barra de herramientas.

Dependiendo de la procedencia de los datos, algunas estrategias de ejemplo pueden no ser compatibles. Para más información acerca del muestreo, consulte la sección de muestreo en [este documento](data-prep-user-guide.md) 

![paso 6](media/data-source-wizard/step5.png)

## <a name="step-7-path-column-handling"></a>Paso 7: Control de la columna de ruta de acceso ##

Si la ruta de acceso de archivo incluye datos importantes, puede elegir incluirla como la primera columna del conjunto de datos. Esto es útil si va a incluir varios archivos. En caso contrario, puede optar por no incluirla.

![paso 7](media/data-source-wizard/step6.png)

Después de hacer clic en Finalizar, se agregará un nuevo origen de datos al proyecto. Puede encontrarlo en el grupo de orígenes de datos en la vista de datos, o como un archivo .dsource en la **Vista de archivos**.
