---
title: "Combinar columnas por transformación de ejemplo con Azure Machine Learning Workbench"
description: "Documento de referencia para la transformación \"Combinar columnas por ejemplos\""
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 3ca1710c969b9bc5a1f56dc53f52c706e1ed07cd
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="combine-columns-by-example-transformation"></a>Transformación Combinar columnas por ejemplos
Esta transformación permite al usuario agregar una nueva columna mediante la combinación de valores de varias columnas. El usuario puede especificar un separador o proporcionar ejemplos de valores combinados para realizar esta transformación. Cuando el usuario proporciona ejemplos de combinación, la transformación se controla por medio del mismo motor **por ejemplos** que se usa en la transformación **Derivar columna por ejemplos**.

## <a name="how-to-perform-this-transformation"></a>Cómo llevar a cabo esta transformación

Para llevar a cabo esta transformación, siga estos pasos:
1. Seleccione dos o más columnas que quiera combinar en una sola. 
2. Seleccione **Combine Columns by Example** (Combinar columnas por ejemplos) en el menú **Transformaciones**. O bien, haga clic con el botón derecho en el encabezado de cualquiera de las columnas seleccionadas y seleccione **Combine Columns by Example** (Combinar columnas por ejemplos) en el menú contextual. Se abrirá el Editor de transformación y se agregará una nueva columna junto a la columna seleccionada situada más a la derecha. La nueva columna contiene los valores combinados separados por un separador predeterminado. Las columnas seleccionadas se pueden identificar por las casillas de los encabezados de columna. Para agregar y eliminar columnas de la selección se pueden usar dichas casillas.
3. Puede actualizar el valor combinado de la columna recién creada. El valor actualizado se usa como ejemplo para aprender la transformación.
4. Haga clic en **Aceptar** para confirmar la transformación.

### <a name="transform-editor-advanced-mode"></a>Editor de transformación: modo avanzado

El modo avanzado ofrece una experiencia más enriquecedora para combinar columnas. 

Al seleccionar **Separador** en **Combine Columns by** (Combinar columnas por), el usuario puede especificar cadenas en el cuadro de texto **Separador**. Presione el tabulador para salir del cuadro de texto **Separador** y obtener una vista previa de los resultados en la cuadrícula de datos. Presione **Aceptar** para confirmar la transformación.

Al seleccionar **Ejemplos** en **Combine Columns by** (Combinar columnas por) el usuario puede proporcionar ejemplos de valores combinados. Para promover una fila como un ejemplo, haga doble clic en las filas de la cuadrícula. Escriba la salida esperada en el cuadro de texto de la fila promovida. Presione el tabulador para salir del cuadro de texto **Separador** y obtener una vista previa de los resultados en la cuadrícula de datos. Presione **Aceptar** para confirmar la transformación. 

El usuario puede alternar entre el **modo básico** y el **modo avanzado**. Para ello, debe hacer clic en los vínculos del Editor de transformación.

### <a name="editing-existing-transformation"></a>Editar la transformación existente

Para editar una transformación **Combinar columna por ejemplos** existente, seleccione la opción **Editar** del paso de transformación. Al hacer clic en **Editar**, se abre el Editor de transformación en el **modo básico**. Para obtener acceso al **modo avanzado**, haga clic en el vínculo del encabezado. Aquí se muestran todos los ejemplos que se han proporcionado durante la creación de la transformación.

## <a name="example-using-separators"></a>Ejemplo con separadores

En este ejemplo se usa una coma seguida de un espacio como separador para combinar las columnas *Calle*, *Ciudad*, *Estado* y *Código postal*.

|Calle|City|Estado|ZIP|Columna|
|:----|:----|:----|:----|:----|
|16011 N.E. 36th Way|REDMOND|WA|98052|16011 N.E. 36th Way, REDMOND, WA, 98052|
|16021 N.E. 36th Way|REDMOND|WA|98052|16021 N.E. 36th Way, REDMOND, WA, 98052|
|16031 N.E. 36th Way|REDMOND|WA|98052|16031 N.E. 36th Way, REDMOND, WA, 98052|
|16041 N.E. 36th Way|REDMOND|WA|98052|16041 N.E. 36th Way, REDMOND, WA, 98052|
|16051 N.E. 36th Way|REDMOND|WA|98052|16051 N.E. 36th Way, REDMOND, WA, 98052|
|16061 N.E. 36th Way|REDMOND|WA|98052|16061 N.E. 36th Way, REDMOND, WA, 98052|
|3460 157th Avenue NE|REDMOND|WA|98052|3460 157th Avenue NE, REDMOND, WA, 98052|
|3350 157th Ave N.E.|REDMOND|WA|98052|3350 157th Ave N.E., REDMOND, WA, 98052|
|3240 157th Avenue N.E.|REDMOND|WA|98052|3240 157th Avenue N.E., REDMOND, WA, 98052|

## <a name="example-using-by-example"></a>Ejemplo de combinación por ejemplos

El valor en **negrita** se proporciona como ejemplo.

|Date|Mes|Year|Hora|Minuto|Segundo|Columna combinada|
|:----|:----|:----|:----|:----|:----|:----|
|13|Oct|2016|15|01|23|**13-Oct-2016 15:01:23 (hora de verano del Pacífico)**|
|16|Oct|2016|16|22|33|16-Oct-2016 15:01:33 (hora de verano del Pacífico)|
|17|Oct|2016|12|43|12|17-Oct-2016 15:01:12 (hora de verano del Pacífico)|
|12|Nov|2016|14|22|44|12-Nov-2016 15:01:44 (hora de verano del Pacífico)|
|23|Nov|2016|01|52|45|23-Nov-2016 15:01:45 (hora de verano del Pacífico)|
|16|Ene|2017|22|34|56|16-Ene-2016 15:01:56 (hora de verano del Pacífico)|
|23|Mar|2017|01|55|25|23-Mar-2016 15:01:25 (hora de verano del Pacífico)|
|16|Abr|2017|11|34|36|16-Abr-2016 15:01:36 (hora de verano del Pacífico)|


