---
title: "Introducción a Preparación de datos para Azure Machine Learning | Microsoft Docs"
description: "Se trata de la guía de introducción de la sección de preparación de datos del área de trabajo de AML"
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 8f90bcb83233b1a2f1a5d342ee444e4b02d4927d
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="getting-started-with-data-preparation"></a>Introducción a Preparación de datos

Le damos la bienvenida a la guía de introducción a Preparación de datos. 

Preparación de datos proporciona un conjunto de herramientas para explorar, entender y solucionar problemas existentes en los datos de forma eficaz. Le permite usar datos de muchas formas y transformarlos en datos limpios, que resultan más adecuados para el uso de bajada.

Preparación de datos se instala como parte de la experiencia de Azure Machine Learning Workbench.  Use Preparación de datos de forma local o impleméntela en un clúster de destino y en la nube como un entorno de ejecución o de tiempo de ejecución.

El tiempo de ejecución del tiempo de diseño usa Python para la extensibilidad y depende de varias bibliotecas de Python, como Pandas. Como con otros componentes de Azure ML Workbench, no es necesario instalar Python, ya que se instala automáticamente. Pero si necesita instalar bibliotecas adicionales, estas se deberán instalar en el directorio de Python de Azure ML Workbench, y no en el directorio de Python habitual. [Aquí](data-prep-python-extensibility-overview.md) encontrará más detalles sobre cómo instalar los paquetes.

Se necesita un proyecto para poder usar Preparación de datos. Cuando haya creado ese proyecto, podrá preparar los datos. 

Vaya a la sección Datos del proyecto seleccionando el icono Datos ![icono de origen de datos](media/data-prep-getting-started/data-source-icon.png), situado a la izquierda de la pantalla.  Vuelva a hacer clic en "+" para **agregar un origen de datos**. El Asistente para orígenes de datos debería iniciarse y agregar un archivo **Origen de datos** (.dsource) al proyecto una vez finalizado el asistente. De forma predeterminada, la vista de los datos es la cuadrícula. Encima de la cuadrícula también se puede seleccionar la vista Métricas. En la vista Métricas se muestran las estadísticas de resumen.  Después de revisar las estadísticas de resumen, haga clic en **Preparar** en la parte superior de la pantalla. [Más información sobre el Asistente para orígenes de datos](data-source-wizard.md) 

## <a name="building-blocks-of-data-preparation"></a>Bloques de creación de preparación de datos ##
### <a name="the-package"></a>El paquete ###

Un paquete es el contenedor principal del trabajo. Un paquete es el artefacto que se guarda y se carga desde el disco. Mientras trabaja dentro del cliente, el paquete se guarda automáticamente de forma constante en segundo plano. 

Los resultados de un paquete se pueden explorar en Python o con un bloc de notas de Jupyter.

Un paquete se puede ejecutar en varios tiempos de ejecución, como Python local, Spark (también en Docker) y HDInsight.

Un paquete contiene uno o varios flujos de datos, que son los pasos y las transformaciones aplicados a los datos.

Un paquete puede usar otro paquete como origen de datos (denominado "flujo de datos de referencia").

### <a name="the-dataflow"></a>El flujo de datos ###
Un flujo de datos tiene una transformación de origen y transformaciones opcionales, que se organizan en una serie de pasos y de destinos opcionales. Al hacer clic en un paso, se vuelven a ejecutar todos los orígenes y todas las transformaciones anteriores al paso seleccionado (este también).  Los datos transformados mediante ese paso se muestran dentro de la cuadrícula. Los pasos se pueden agregar, mover y eliminar dentro de un flujo de datos mediante la lista de pasos.

La lista de pasos situada en el lado derecho del cliente se puede abrir y cerrar para proporcionar más espacio en la pantalla.

Puede haber a la vez varios flujos de datos en la interfaz de usuario. Cada flujo de datos se representa como una pestaña en la interfaz de usuario.

### <a name="the-source"></a>El origen
Un origen es la procedencia de los datos y el formato en el que se encuentran. Los datos de un paquete de preparación de datos siempre tienen como origen otro flujo de datos (origen de datos). Es este flujo de datos de referencia el que contiene la información. Cada origen tiene una experiencia de usuario diferente para que se pueda configurar. El origen genera una vista "rectangular"/tabular de los datos. Si los datos de origen tienen un estilo "derecho irregular", se normaliza la estructura para que sea "rectangular". [El apéndice 2 proporciona la lista actual de orígenes compatibles](data-prep-appendix2-supported-data-sources.md).

### <a name="the-transform"></a>La transformación ###
Las transformaciones consumen datos con un formato determinado, llevan a cabo alguna operación en los datos (por ejemplo, cambiar el tipo de datos) y, luego, generan datos con el nuevo formato. Cada transformación tiene su propia interfaz de usuario y sus propios comportamientos. El encadenamiento de varias transformaciones a través de los pasos del flujo de datos es el núcleo de la funcionalidad de Preparación de datos. [El apéndice 3 proporciona la lista actual de transformaciones compatibles](data-prep-appendix3-supported-transforms.md).

### <a name="the-inspector"></a>El inspector ###

Los inspectores son visualizaciones de los datos que están disponibles para mejorar la comprensión de los datos.  El hecho de comprender los datos y los problemas de calidad de los datos le permite decidir qué acciones (transformaciones) se deben llevar a cabo. Algunos inspectores admiten acciones que generan transformaciones. Por ejemplo, el inspector de recuento de valores le permite seleccionar un valor y aplicar un filtro que incluya o excluya ese valor. Los inspectores también pueden proporcionar el contexto para las transformaciones. Por ejemplo, al seleccionar una o varias columnas, se cambian las posibles transformaciones que se pueden aplicar.

Una columna puede tener varios inspectores en cualquier momento (por ejemplo, Estadísticas de columna y un histograma). También puede haber instancias de un inspector en varias columnas. Por ejemplo, todas las columnas numéricas podrían tener histogramas al mismo tiempo.

Los inspectores aparecen en el depósito de generación de perfiles, situado en la parte inferior de la pantalla.  Maximice los inspectores para verlos más grandes dentro del área de contenido principal. Piense en la cuadrícula de datos como el inspector predeterminado. Todos los inspectores se pueden expandir en el área de contenido principal. Los inspectores situados dentro del área de contenido principal se minimizan en el depósito de generación de perfiles. Personalice un inspector haciendo clic en el icono del lápiz del inspector. Reordene los inspectores dentro del depósito mediante una operación de arrastrar y soltar.

Algunos inspectores admiten el modo "Halo". Este modo muestra el valor o el estado antes de que se aplicara la última transformación. El valor anterior se muestra en color gris con el valor actual en primer plano y muestra el impacto de una transformación. [El apéndice 4 proporciona la lista actual de inspectores compatibles](data-prep-appendix4-supported-inspectors.md).

### <a name="the-destination"></a>El destino
 Un destino es donde se escriben/exportan los datos después de prepararlos en un flujo de datos. Un flujo de datos determinado puede tener varios destinos. Cada destino tiene una experiencia de usuario diferente para que se pueda configurar. El destino consume datos en un formato "rectangular"/tabular y los escribe en alguna ubicación con un formato determinado. [El apéndice 5 proporciona la lista actual de destinos compatibles](data-prep-appendix5-supported-destinations.md).

### <a name="using-data-preparation"></a>Usar la preparación de datos ###
Preparación de datos presupone que hay una metodología/enfoque básico de cinco pasos para la preparación de los datos.

#### <a name="step-1-ingestion"></a>Paso 1: Ingesta ####
Importe los datos para la preparación de datos mediante la opción **Agregar origen de datos** de la vista del proyecto.  Toda la ingesta inicial de datos se controla mediante el Asistente para orígenes de datos.

#### <a name="step-2-understandprofile-the-data"></a>Paso 2: Comprender/generar perfiles de los datos ####

En primer lugar, fíjese en la barra de calidad de datos situada en la parte superior de cada columna. El color verde representa las filas que tienen valores. El color gris representa filas con un valor ausente, nulo, etc. El color rojo indica valores erróneos. Mantenga el cursor del ratón sobre la barra para obtener información sobre herramientas con el número exacto de filas que hay en cada uno de los tres depósitos. La barra de calidad de datos usa una escala logarítmica, por lo que siempre debe comprobar las cifras reales para hacerse una idea aproximada del volumen de datos que faltan.

![columnas](media/data-prep-getting-started/columns.png)

Luego, use una combinación de otros inspectores y la cuadrícula para comprender mejor las características de los datos.  Empiece a formular hipótesis sobre la preparación de los datos necesaria para llevar a cabo un análisis más profundo. La mayoría de los inspectores trabajan en una sola columna o en un número pequeño de columnas.  

Es probable que se necesiten varios inspectores en varias columnas para comprender los datos. Puede desplazarse por varios inspectores dentro del depósito de generación de perfiles. Dentro del depósito también puede desplazar los inspectores al principio de la lista para poder verlos en el área inmediatamente visible.

![inspectores](media/data-prep-getting-started/inspectors.PNG)

Se proporcionan distintos inspectores para las variables y columnas continuas frente a las categóricas. El menú Inspector habilita y deshabilita opciones según el tipo de variables o columnas que tenga.

Al trabajar con amplios conjuntos de datos que tienen muchas columnas, es aconsejable aplicar un enfoque práctico a la hora de trabajar con subconjuntos. Este enfoque se centra en un pequeño número de columnas (por ejemplo, entre 5 y 10), en su preparación y en trabajar con las columnas restantes. El inspector de cuadrícula admite el particionamiento vertical de las columnas, de modo que si tiene más de 300 columnas, deberá ir avanzando por las páginas.
 

#### <a name="step-3-transform-the-data"></a>Paso 3: Transformar los datos ####
Las transformaciones cambian los datos y permiten su ejecución para admitir la hipótesis de trabajo actual. Las transformaciones aparecen en forma de pasos en la lista de pasos situada en el lado derecho. Se puede "viajar en el tiempo" con la lista de pasos haciendo clic en un punto arbitrario de la lista de pasos.

Un icono verde situado a la izquierda de un paso determinado indica que se ha ejecutado y los datos reflejan la ejecución de la transformación. Una barra vertical situada a la izquierda del paso indica el estado actual de los datos de los inspectores.

![steps](media/data-prep-getting-started/steps.PNG)

Intente efectuar pequeños cambios frecuentes en los datos y validarlos (paso 4) después de cada cambio a medida que evolucione la hipótesis.

#### <a name="step-4-verify-the-impact-of-the-transformation"></a>Paso 4: Comprobar el impacto de la transformación. 
Decida si la hipótesis era correcta. Si lo era, desarrolle la siguiente hipótesis y repita los pasos 2 y 3 de la nueva hipótesis. Si era incorrecta, deshaga la última transformación, desarrolle una hipótesis nueva y repita los pasos 2 y 3.

El método principal para determinar si la transformación tuvo el impacto adecuado consiste en usar los inspectores. Use los existentes. Use los inspectores con el efecto Halo habilitado o inicie varios inspectores para ver los datos en determinados puntos en el tiempo.

![Inspector de Halo](media/data-prep-getting-started/halo1.PNG) ![Inspector de Halo](media/data-prep-getting-started/halo2.PNG)

Para deshacer una transformación, vaya a la lista de pasos, situada en el lado derecho de la interfaz de usuario (es posible que deba salir del panel Lista de pasos; para abrirlo, haga clic en el cheurón doble que señala hacia la izquierda). En el panel, seleccione la transformación ejecutada que quiera deshacer. Seleccione la lista desplegable situada en el lado derecho del bloque de la interfaz de usuario. Seleccione **Editar** para realizar cambios o **Eliminar** para quitar la transformación de la lista de pasos y del flujo de datos.

#### <a name="step-5-output"></a>Paso 5: Salida 
Cuando haya finalizado la preparación de datos, podrá escribir el flujo de datos en una salida. Un flujo de datos puede tener muchas salidas. En el menú Transformaciones puede seleccionar la salida que quiere que se escriba como el conjunto de datos. También puede seleccionar el destino de la salida. 

## <a name="list-of-appendices"></a>Lista de apéndices 
[Apéndice 2: Orígenes de datos admitidos](data-prep-appendix2-supported-data-sources.md)  
[Apéndice 3: Transformaciones admitidas](data-prep-appendix3-supported-transforms.md)  
[Apéndice 4: Inspectores admitidos](data-prep-appendix4-supported-inspectors.md)  
[Apéndice 5: Destinos admitidos](data-prep-appendix5-supported-destinations.md)  
[Apéndice 6: Expresiones de filtro de ejemplo en Python](data-prep-appendix6-sample-filter-expressions-python.md)  
[Apéndice 7: Expresiones de flujo de datos de transformación de ejemplo en Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
[Apéndice 8: Orígenes de datos de ejemplo en Python](data-prep-appendix8-sample-source-connections-python.md)  
[Apéndice 9: Conexiones de destino de ejemplo en Python](data-prep-appendix9-sample-destination-connections-python.md)  
[Apéndice 10: Transformaciones de columna de ejemplo en Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  

## <a name="see-also"></a>Otras referencias

[Tutorial de preparación avanzada de datos](tutorial-bikeshare-dataprep.md)