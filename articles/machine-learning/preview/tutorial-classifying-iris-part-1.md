---
title: "Tutorial de preparación de datos de la clasificación de Iris de Machine Learning Services (versión preliminar) | Microsoft Docs"
description: "A lo largo de este tutorial se muestra cómo puede aprovechar al máximo Azure Machine Learning Services (versión preliminar). Esta es la parte 1 de la preparación de datos."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/28/2017
ms.openlocfilehash: 975a86c1f9d9692f6eadd232177f33cbbbeeff2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="classifying-iris-part-1-prepare-data"></a>Parte 1 de la clasificación de Iris: preparación de los datos
Azure Machine Learning Services (versión preliminar) es una solución de análisis avanzado y ciencia de datos completa e integrada que permite a los científicos de datos profesionales preparar datos, desarrollar experimentos e implementar modelos a escala de la nube.

Este tutorial es la primera parte de una serie de tres partes. En este tutorial, le guiaremos por los aspectos básicos de Azure Machine Learning Services (versión preliminar). En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Crear un proyecto en Azure Machine Learning Workbench.
> * Generar un paquete de preparación de datos.
> * Generar código de Python/PySpark para invocar el paquete de preparación de datos.

Este tutorial usa el [conjunto de datos de Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) atemporal para simplificar las cosas. Las capturas de pantalla son específicas de Windows, pero la experiencia con macOS es casi idéntica.

## <a name="prerequisites"></a>Requisitos previos
- Cree una cuenta de Experimentación de Azure Machine Learning
- Instale Azure Machine Learning Workbench

Puede seguir la [guía de inicio rápido de instalación y creación](quickstart-installation.md) para instalar la aplicación Azure Machine Learning Workbench. Dicha instalación también incluye la interfaz de la línea de comandos (CLI).

## <a name="create-a-new-project-in-azure-ml-workbench"></a>Creación de un nuevo proyecto en Azure ML Workbench
1. Inicie la aplicación Azure Machine Learning Workbench e inicie sesión si es necesario. En el panel **Proyectos**, haga clic en el icono **+** para crear un **Nuevo proyecto**.

   ![nueva área de trabajo](media/tutorial-classifying-iris/new_ws.png)

2. Rellene los detalles de la ventana **Crear proyecto nuevo**. 

   ![Nuevo proyecto](media/tutorial-classifying-iris/new_project.png)

   - Rellene el campo **Nombre del proyecto** con un nombre para el proyecto. Por ejemplo, use el valor **myIris**.
   - Elija el **directorio del proyecto** en el que se crea el proyecto. Por ejemplo, use el valor **C:\Temp**. 
   - Escriba la **descripción del proyecto**. 
   - El campo **Repositorio de GIT** es opcional y puede dejarse en blanco. Puede proporcionar un repositorio de GIT vacío existente (un repositorio sin ninguna rama maestra) en VSTS. Si lo hace, más adelante podrá habilitar la itinerancia y compartir escenarios. Para obtener más información, consulte el artículo [Using Git repo](using-git-ml-project.md) (Uso del repositorio GIT. 
   - Elija un **área de trabajo**, por ejemplo, este tutorial usa **IrisGarden**. 
   - Seleccione la plantilla **Classifying iris** (Clasificación de Iris) en la lista de plantillas del proyecto. 

3. Haga clic en el botón **Crear** para crear el proyecto. El proyecto se creará y se abrirá automáticamente.

## <a name="create-a-data-preparation-package"></a>Generar un paquete de preparación de datos.
1. Abra el archivo **iris.csv** desde **Vista de archivos**. El archivo es una tabla sencilla con 5 columnas y 150 filas. Tiene cuatro columnas numéricas de características y una columna de destino de la cadena. No tiene encabezados de columna.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > No se recomienda incluir archivos de datos en la carpeta del proyecto, especialmente cuando el tamaño del archivo es grande. Incluimos el archivo **iris.csv** en esta plantilla con fines de demostración porque es pequeño. Para obtener más información, consulte el artículo [How to read and write large data files](how-to-read-write-files.md) (Cómo leer y escribir archivos de datos de gran tamaño).

2. En **Vista de datos**, haga clic en el icono **+** para agregar un nuevo origen de datos. Se iniciará el asistente para **Agregar origen de datos**. 

   ![vista de datos](media/tutorial-classifying-iris/data_view.png)

3. Complete el asistente para la preparación de datos. 
   - En la primera pantalla, seleccione la opción **Archivo o directorio** y, a continuación, haga clic en **Siguiente**.
   - En la segunda pantalla, elija el archivo local **iris.csv**, por ejemplo "C:\Temp\myIris\iris.csv".
   - En la tercera pantalla, **Detalles del archivo**, acepte los valores predeterminados.
   - En la cuarta pantalla, **Tipos de datos**, cambie el **TIPO DE DATOS** de _Cadena_ a _Numérico_ para las columnas de _Column1_ a _Column4_ ya que estas columnas son valores numéricos. 
   - En la quinta y sexta pantallas, acepte la configuración predeterminada.
   - Haga clic en el botón **Finalizar**.

   ![selección de Iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Asegúrese de seleccionar el archivo **iris.csv** desde el directorio del proyecto actual de este ejercicio, en caso contrario, puede producirse un error en los últimos pasos. 

4. Se crea un nuevo archivo **iris-1.dsource**. Se asigna un nombre único al archivo que contiene un guión seguido de un 1, porque el proyecto de ejemplo ya contiene un archivo **iris.dsource** sin enumerar.  

   Se abre el archivo y se muestran los datos. De forma automática, se agrega una serie de encabezados de columna, de **Column1** a **Column5**, a este conjunto de datos. Desplácese hasta el final y observe que la última fila del conjunto de datos está vacía. Esto se debe a un salto de línea adicional en el archivo csv.

   ![vista de datos de Iris](media/tutorial-classifying-iris/iris_data_view.png)

5. Ahora, haga clic en el botón **Métricas**. Observe los histogramas y un conjunto completo de las estadísticas que se calculan automáticamente para cada columna. También puede hacer clic en el botón **Datos** para ver los datos de nuevo. 

   ![vista de datos de Iris](media/tutorial-classifying-iris/iris_metrics_view.png)

6. Haga clic en el botón **Preparar**. Aparecerá el cuadro de diálogo **Preparar**. 

   El proyecto de ejemplo contiene el archivo **iris.dprep**, por lo que de forma predeterminada se le pide que cree un nuevo flujo de datos en ese paquete de preparación de datos existente **iris.dprep**. 

   Cambie el valor de la lista desplegable a **+New Data Preparation Package** (+Nuevo paquete de preparación de datos), escriba el valor nuevo "iris-1" y, a continuación, haga clic en **Aceptar**.

   ![Vista de datos de Iris](media/tutorial-classifying-iris/new_dprep.png)

   Se crea un nuevo paquete de preparación de datos denominado **iris-1.dprep** y se abre en el editor de preparación de datos.

7. Ahora vamos a preparar algunos datos básicos. Para cambiar los nombres de la columna, haga clic en el encabezado de columna y haga que el texto del encabezado se pueda modificar. 

   Escriba **Sepal Length** (Longitud del sépalo), **Sepal Width** (Anchura del sépalo), **Petal Length** (Longitud del pétalo), **Petal Width** (Anchura del pétalo) y **Species** (Especie) para las cinco columnas respectivamente.

   ![Cambio de nombre de las columnas](media/tutorial-classifying-iris/rename_column.png)

8. Para contar valores distintos, seleccione la columna **Species** (Especie) y haga clic con el botón derecho en ella. Elija **Value Counts** (Recuentos de valores). 

   ![Hacer clic en Value Counts (Recuentos de valores)](media/tutorial-classifying-iris/value_count.png)

   Esta acción abre el panel **Inspectores** y muestra un histograma con cuatro barras. Observe que la columna de destino tiene tres valores distintos: **Iris_virginica**, **Iris_versicolor**, **Iris-setosa** y un valor **(null)**.

9. Filtre los valores NULL mediante la selección de la barra del gráfico que representa el valor NULL. Hay una fila con un valor **(null)**. Para deshacerse de esta fila, haga clic en el botón de filtro **-**.

   ![Histograma de recuento de valores](media/tutorial-classifying-iris/filter_out.png)

10. Tenga en cuenta los pasos individuales que se detallan en el panel **PASOS**. Cuando se ocupó del cambio de nombre de las columnas y del filtrado de las filas con valores NULL, cada acción se registró como un paso de la preparación de datos. Puede editar los pasos individuales para ajustar la configuración, reordenarlos e incluso eliminar pasos.

   ![Pasos](media/tutorial-classifying-iris/steps.png)

11. Ahora cierre el editor de preparación de datos haciendo clic en **X** en la pestaña denominada **iris 1** con el icono de gráfico. El trabajo se guarda automáticamente en el archivo **iris 1.dprep** que aparece en el encabezado **Preparación de datos**.

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>Generar código de Python/PySpark para invocar el paquete de preparación de datos.

1. Haga doble clic en el archivo **iris 1.dprep** para mostrar el menú contextual y elija **Generate Data Access Code File** (Generar archivo de código de acceso a datos). 

   ![generar código](media/tutorial-classifying-iris/generate_code.png)

2. Un nuevo archivo denominado **iris-1.py** se abre con las siguientes líneas de código:

   ```python
   # This code snippet will load the referenced package and return a DataFrame.
   # If the code is run in a PySpark environment, the code will return a
   # Spark DataFrame. If not, the code will return a Pandas DataFrame.

   from azureml.dataprep.package import run
   df = run('iris.dprep', dataflow_idx=0)
   ```

   Este fragmento de código invoca la lógica que creó como un paquete de preparación de datos. Dependiendo del contexto en el que se ejecuta este código, `df` representa los diferentes tipos de tramas de datos. Se usa una [trama de datos de Pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) cuando se ejecuta en un entorno de ejecución de Python, o una [trama de datos de Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html) cuando se ejecuta en un contexto de Spark. 

   Para obtener más información sobre cómo preparar los datos en Azure Machine Learning Workbench, haga referencia a la guía [Getting Started with Data Preparation](data-prep-getting-started.md) (Introducción a la preparación de datos).

## <a name="next-steps"></a>Pasos siguientes
En esta primera parte de la serie de tutoriales de tres partes, ha usado Azure Machine Learning Workbench para:
> [!div class="checklist"]
> * Crear un nuevo proyecto 
> * Generar un paquete de preparación de datos.
> * Generar código de Python/PySpark para invocar el paquete de preparación de datos.

Está listo para pasar a la siguiente parte de la serie, en la que compilará un modelo de aprendizaje automático.
> [!div class="nextstepaction"]
> [Compilar un modelo](tutorial-classifying-iris-part-2.md)
