---
title: "Tutorial de preparación de los datos para la clasificación de Iris en Azure Machine Learning Services (versión preliminar) | Microsoft Docs"
description: "A lo largo de este tutorial se muestra cómo puede aprovechar al máximo Azure Machine Learning Services (versión preliminar). Esta es la primera parte y describe la preparación de los datos."
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
ms.openlocfilehash: cabba8ce04d62d35ca40b3ae35d9d40a6ec7b2b9
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2017
---
# <a name="classify-iris-part-1-prepare-the-data"></a>Parte 1 de la clasificación de Iris: preparación de los datos
Azure Machine Learning Services (versión preliminar) es una solución de análisis avanzado y ciencia de datos completa e integrada que permite a los científicos de datos profesionales preparar datos, desarrollar experimentos e implementar modelos a escala de la nube.

Este tutorial es la primera parte de una serie de tres. En este tutorial, le guiamos por los aspectos básicos de Azure Machine Learning Services (versión preliminar). Aprenderá a:
> [!div class="checklist"]
> * Crear un proyecto en Azure Machine Learning Workbench.
> * Generar un paquete de preparación de datos.
> * Generar código de Python/PySpark para invocar el paquete de preparación de datos.

Este tutorial usa el [conjunto de datos Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) atemporal. Las capturas de pantalla son específicas de Windows, pero la experiencia con MacOS es casi idéntica.

## <a name="prerequisites"></a>Requisitos previos
- Cree una cuenta de Experimentación de Azure Machine Learning.
- Instale Azure Machine Learning Workbench.

Puede seguir las instrucciones del artículo [Guía de inicio rápido de instalación y creación](quickstart-installation.md) para instalar la aplicación Azure Machine Learning Workbench. Esta instalación incluye también la herramienta de línea de comandos multiplataforma de Azure, o la CLI de Azure.

## <a name="create-a-new-project-in-azure-machine-learning-workbench"></a>Creación de un proyecto en Azure Machine Learning Workbench
1. Abra la aplicación Azure Machine Learning Workbench e inicie sesión si es necesario. En el panel **Proyectos**, seleccione el signo más (**+**) para crear un **Nuevo proyecto**.

   ![Nueva área de trabajo](media/tutorial-classifying-iris/new_ws.png)

2. Rellene los detalles de la ventana **Crear proyecto nuevo**: 

   ![Nuevo proyecto](media/tutorial-classifying-iris/new_project.png)

   - Rellene el cuadro **Nombre del proyecto** con un nombre para el proyecto. Por ejemplo, use el valor **myIris**.
   - Seleccione el **directorio del proyecto** en el que se crea el proyecto. Por ejemplo, use el valor `C:\Temp\`. 
   - Escriba la **descripción del proyecto**, que es opcional. 
   - El campo **Repositorio de GIT** también es opcional y puede dejarse en blanco. Puede proporcionar un repositorio de GIT vacío existente (un repositorio sin ninguna rama maestra) en Visual Studio Team Services. Si utiliza un repositorio Git que ya existe, puede habilitar los escenarios de movilidad y uso compartido más adelante. Para obtener más información, consulte [Uso del repositorio de Git](using-git-ml-project.md). 
   - Seleccione un **área de trabajo**; por ejemplo, este tutorial usa **IrisGarden**. 
   - Seleccione la plantilla **Classifying iris** (Clasificación de Iris) en la lista de plantillas del proyecto. 

3. Seleccione el botón **Crear**. El proyecto se creará y se abrirá automáticamente.

## <a name="create-a-data-preparation-package"></a>Generar un paquete de preparación de datos.
1. Abra el archivo **iris.csv** desde **Vista de archivos**. El archivo es una tabla con 5 columnas y 150 filas. Tiene cuatro columnas numéricas de características y una columna de destino de la cadena. No tiene encabezados de columna.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > No incluya archivos de datos en la carpeta del proyecto, especialmente cuando el tamaño del archivo sea grande. Incluimos el archivo **iris.csv** en esta plantilla con fines de demostración porque es pequeño. Para obtener más información, consulte [How to read and write large data files](how-to-read-write-files.md) (Procedimiento para leer y escribir archivos de datos de gran tamaño).

2. En **Vista de datos**, seleccione el signo más (**+**) para agregar un nuevo origen de datos. Se abre la página **Agregar origen de datos**. 

   ![Vista de datos](media/tutorial-classifying-iris/data_view.png)

3. Deje los valores predeterminados y, luego, seleccione **Siguiente**.  
 
   ![Selección de Iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Asegúrese de seleccionar el archivo **iris.csv** desde el directorio del proyecto actual de este ejercicio. De lo contrario, los pasos posteriores podrían dar error. 

4. Se crea un nuevo archivo denominado **iris-1.dsource**. Se asigna un nombre único al archivo que contiene un guión seguido de un 1, porque el proyecto de ejemplo ya contiene un archivo **iris.dsource** sin enumerar.  

   Se abre el archivo y se muestran los datos. De forma automática, se agrega una serie de encabezados de columna, de **Column1** a **Column5**, a este conjunto de datos. Desplácese hasta el final y observe que la última fila del conjunto de datos está vacía. La fila está vacía porque no hay un salto de línea adicional en el archivo CSV.

   ![Vista de datos de Iris](media/tutorial-classifying-iris/iris_data_view.png)

5. Seleccione el botón **Métricas**. Observe los histogramas. Se ha calculado un conjunto completo de las estadísticas para cada columna. También puede hacer clic en el botón **Datos** para ver los datos de nuevo. 

   ![Vista de datos de Iris](media/tutorial-classifying-iris/iris_metrics_view.png)

6. Seleccione el botón **Preparar**. Se abre el cuadro de diálogo **Preparar**. 

   El proyecto de ejemplo incluye un archivo **iris.dprep**. De forma predeterminada, le pide que cree un nuevo flujo de datos en el paquete de preparación de datos **iris.dprep** que ya existe. 

   Seleccione **+ Nuevo paquete de preparación de datos** en el menú desplegable, escriba un nuevo valor para el nombre del paquete, utilice **iris-1** y, a continuación, seleccione **Aceptar**.

   ![Vista de datos de Iris](media/tutorial-classifying-iris/new_dprep.png)

   Se crea un nuevo paquete de preparación de datos denominado **iris-1.dprep** y se abre en el editor de preparación de datos.

7. Ahora vamos a preparar algunos datos básicos. Cambie el nombre de las columnas. Seleccione cada encabezado de columna para poder modificar su texto. 

   Escriba **Sepal Length** (Longitud del sépalo), **Sepal Width** (Anchura del sépalo), **Petal Length** (Longitud del pétalo), **Petal Width** (Anchura del pétalo) y **Species** (Especie) para las cinco columnas respectivamente.

   ![Cambio del nombre de las columnas](media/tutorial-classifying-iris/rename_column.png)

8. Para contar valores distintos, seleccione la columna **Species** (Especie) y haga clic con el botón derecho en ella para seleccionarla. Seleccione **Value Counts** (Recuentos de valores) en el menú desplegable. 

   ![Selección de Value Counts (Recuentos de valores)](media/tutorial-classifying-iris/value_count.png)

   De este modo se abre el panel **Inspectores** y se muestra un histograma con cuatro barras. La columna de destino tiene tres valores distintos: **Iris_virginica**, **Iris_versicolor**, **Iris-setosa** y un valor **(null)**.

9. Para filtrar los valores NULL, seleccione la barra del gráfico que representa el valor NULL. Hay una fila con un valor **(null)**. Para quitar esta fila, seleccione el signo menos (**-**).

   ![Histograma de recuento de valores](media/tutorial-classifying-iris/filter_out.png)

10. Tenga en cuenta los pasos individuales que se detallan en el panel **PASOS**. Mientras cambiaba el nombre de las columnas y filtraba las filas con el valor NULL, cada acción se registró como un paso de preparación de datos. Puede editar los pasos individuales para ajustar la configuración, volver a ordenar los pasos y eliminarlos.

   ![Pasos](media/tutorial-classifying-iris/steps.png)

11. Cierre el editor de preparación de datos. Seleccione **Cerrar** (x) en la pestaña **iris-1** con el icono de gráfico. El trabajo se guarda automáticamente en el archivo **iris-1.dprep** que aparece bajo el encabezado **Preparaciones de datos**.

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Generación de código de Python/PySpark para invocar el paquete de preparación de datos

1. Haga clic con el botón derecho en el archivo **iris-1.dprep** para mostrar el menú contextual y seleccione **Generate Data Access Code File** (Generar archivo de código de acceso a datos). 

   ![Generación de código](media/tutorial-classifying-iris/generate_code.png)

2. Un archivo nuevo denominado **iris-1.py** se abre con las siguientes líneas de código:

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   Este fragmento de código invoca la lógica que creó como un paquete de preparación de datos. En función del contexto en el que se ejecuta este código, `df` representa varios tipos de tramas de datos. Se usa una [trama de datos de Pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) cuando se ejecuta en un entorno de ejecución de Python, o una [trama de datos de Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html) cuando se ejecuta en un contexto de Spark. 

   Para obtener más información sobre cómo preparar los datos en Azure Machine Learning Workbench, vea la guía [Introducción a Preparación de datos](data-prep-getting-started.md).

## <a name="next-steps"></a>Pasos siguientes
En esta primera parte de la serie de tutoriales de tres partes, ha usado Azure Machine Learning Workbench para:
> [!div class="checklist"]
> * Crear un nuevo proyecto. 
> * Generar un paquete de preparación de datos.
> * Generar código de Python/PySpark para invocar el paquete de preparación de datos.

Está listo para pasar a la siguiente parte de la serie, en la que aprenderá a crear un modelo de Azure Machine Learning:
> [!div class="nextstepaction"]
> [Compilar un modelo](tutorial-classifying-iris-part-2.md)
