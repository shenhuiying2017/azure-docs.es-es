---
title: "Tutorial de preparación de los datos para la clasificación de Iris en Azure Machine Learning Services (versión preliminar) | Microsoft Docs"
description: "A lo largo de este tutorial se muestra cómo puede aprovechar al máximo Azure Machine Learning Services (versión preliminar). Esta es la primera parte y describe la preparación de los datos."
services: machine-learning
author: hning86
ms.author: haining, j-martens
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/28/2018
ms.openlocfilehash: 0bef557ee1394e3c786fd2c54e821b5dea28fabf
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-classify-iris-part-1---preparing-the-data"></a>Tutorial: Clasificación de iris, parte 1: preparación de los datos

Azure Machine Learning Services (versión preliminar) es una solución de análisis avanzado y ciencia de datos completa e integrada que permite a los científicos de datos profesionales preparar datos, desarrollar experimentos e implementar modelos a escala de la nube.

Este tutorial es la primera parte de una serie de tres. En este tutorial, le guiamos por los aspectos básicos de Azure Machine Learning Services (versión preliminar). Aprenderá a:

> [!div class="checklist"]
> * Crear un proyecto en Azure Machine Learning Workbench.
> * Generar un paquete de preparación de datos.
> * Generación de código de Python/PySpark para invocar el paquete de preparación de datos

Este tutorial usa el [conjunto de datos Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) atemporal. Las capturas de pantalla son específicas de Windows, pero la experiencia con macOS es casi idéntica.

## <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Para realizar este tutorial, necesitará lo siguiente:
- Una cuenta de Experimentación de Azure Machine Learning.
- Azure Machine Learning Workbench instalado.

Si aún no lo tiene, siga los pasos descritos en la [guía de inicio rápido de instalación e inicio](quickstart-installation.md) para configurar esta cuenta e instalar la aplicación Azure Machine Learning Workbench. 

## <a name="create-a-new-project-in-workbench"></a>Creación de un proyecto nuevo en Workbench

Si ha seguido los pasos descritos en la [guía de inicio rápido de instalación e inicio](quickstart-installation.md), ya tendrá este proyecto y puede ir a la siguiente sección.

1. Abra la aplicación Azure Machine Learning Workbench e inicie sesión si es necesario. 
   
   + En Windows, puede iniciarlo con el acceso directo del escritorio **Machine Learning Workbench**. 
   + En Mac OS, seleccione **Azure ML Workbench** en el Launchpad.

1. En el panel **PROJECTS** (PROYECTOS), seleccione el signo más (+) y elija **New Project** (Nuevo proyecto).  

   ![Nueva área de trabajo](media/tutorial-classifying-iris/new_ws.png)

1. Rellene los campos del formulario y seleccione el botón **Create** (Crear) para crear un nuevo proyecto en Workbench.

   Campo|Valor recomendado para el tutorial|DESCRIPCIÓN
   ---|---|---
   Nombre de proyecto | myIris |Elija un nombre único que identifique la cuenta. Puede usar su propio nombre o el nombre de departamento o proyecto que mejor identifique el experimento. El nombre debe tener entre 2 y 32 caracteres. Debe incluir solo caracteres alfanuméricos y el carácter de guión (-). 
   Directorio del proyecto | c:\Temp\ | Especifique el directorio en el que se creará el proyecto.
   Descripción del proyecto | _déjelo en blanco_ | Campo opcional útil para describir los proyectos.
   Visualstudio.com |_déjelo en blanco_ | Campo opcional. Si lo desea, el proyecto puede asociarse con un repositorio de Git para el control de código fuente y la colaboración en Visual Studio Team Services. [Vea cómo configurarlo](https://docs.microsoft.com/en-us/azure/machine-learning/preview/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo). 
   Área de trabajo | IrisGarden (si existe) | Elija un área de trabajo que ha creado para su cuenta de Experimentación en Azure Portal. <br/>Si ha seguido la guía de inicio rápido, tendrá un área de trabajo con el nombre IrisGarden. Si no es así, seleccione el que creó cuando creó su cuenta de Experimentación o cualquier otro que desee utilizar.
   Plantilla de proyecto | Clasificación de iris | Las plantillas contienen scripts y datos que puede usar para explorar el producto. Esta plantilla contiene los scripts y los datos que necesita para esta guía de inicio rápido y otros tutoriales en este sitio de documentación. 

   ![Nuevo proyecto](media/tutorial-classifying-iris/new_project.png)
 
 Se crea un nuevo proyecto y se abre el panel de proyecto con ese proyecto. En este momento, ya puede explorar la página principal, los orígenes de datos, los cuadernos y los archivos de código fuente del proyecto. 

## <a name="create-a-data-preparation-package"></a>Generar un paquete de preparación de datos.

En esta parte del tutorial, explorará los datos e iniciará el proceso de preparación de datos. Al preparar los datos en Azure Machine Learning Workbench, se guarda una representación JSON de las transformaciones que lleve a cabo en Workbench en un paquete de preparación de datos local (archivo *.dprep). Este paquete de preparación de datos es el contenedor principal del trabajo de preparación de datos en Workbench.

Este paquete de preparación de datos se puede entregar para su ejecución a un entorno de ejecución, por ejemplo, local-C#/CoreCLR, Scala/Spark o Scala/HDI. donde se genera el código para el entorno de ejecución adecuado para su ejecución. 

1. Seleccione el icono de carpeta para abrir la vista de archivo y, a continuación, seleccione **iris.csv** para abrir el archivo.  

   El archivo es una tabla con 5 columnas y 150 filas. Tiene cuatro columnas numéricas de características y una columna de destino de la cadena. No tiene encabezados de columna.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > No incluya archivos de datos en la carpeta del proyecto, especialmente cuando el tamaño del archivo sea grande. Incluimos el archivo **iris.csv** en esta plantilla con fines de demostración porque es pequeño. Para obtener más información, consulte [How to read and write large data files](how-to-read-write-files.md) (Procedimiento para leer y escribir archivos de datos de gran tamaño).

2. En **Data View** (Vista de datos), seleccione el signo más (**+**) para agregar un nuevo origen de datos. Se abre la página **Add Data Source** (Agregar origen de datos). 

   ![Vista de datos](media/tutorial-classifying-iris/data_view.png)

3. Seleccione **Text Files (*.csv, .json, .txt.,...)** (Archivos de texto ) y haga clic en **Next** (Siguiente).
   ![Data Source](media/tutorial-classifying-iris/data-source.png) (Origen de datos)
   

4. Vaya al archivo **iris.csv** y haga clic en **Next** (Siguiente).  
 
   ![Selección de Iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Asegúrese de seleccionar el archivo **iris.csv** desde el directorio del proyecto actual de este ejercicio. De lo contrario, los pasos posteriores podrían dar error.
   
5. Deje los valores predeterminados y haga clic en **Finish** (Finalizar).

6. Se crea un nuevo archivo denominado **iris-1.dsource**. Se asigna un nombre único al archivo que contiene "-1", porque el proyecto de ejemplo ya contiene un archivo **iris.dsource** sin numerar.  

   Se abre el archivo y se muestran los datos. De forma automática, se agrega una serie de encabezados de columna, de **Column1** a **Column5**, a este conjunto de datos. Desplácese hasta el final y observe que la última fila del conjunto de datos está vacía. La fila está vacía porque no hay un salto de línea adicional en el archivo CSV.

   ![Vista de datos de Iris](media/tutorial-classifying-iris/iris_data_view.png)

7. Seleccione el botón **Metrics** (Métricas). Observe los histogramas. Se ha calculado un conjunto completo de las estadísticas para cada columna. También puede hacer clic en el botón **Data** (Datos) para ver los datos de nuevo. 

   ![Vista de datos de Iris](media/tutorial-classifying-iris/iris_metrics_view.png)

8. Seleccione el botón **Prepare** (Preparar). Se abre el cuadro de diálogo **Prepare** (Preparar). 

   El proyecto de ejemplo incluye un archivo **iris.dprep**. De forma predeterminada, le pide que cree un nuevo flujo de datos en el paquete de preparación de datos **iris.dprep** que ya existe. 

   Seleccione **+ New Data Preparation Package** (+ Nuevo paquete de preparación de datos) en el menú desplegable, escriba un nuevo valor para el nombre del paquete, utilice **iris-1** y, a continuación, seleccione **OK** (Aceptar).

   ![Vista de datos de Iris](media/tutorial-classifying-iris/new_dprep.png)

   Se crea un nuevo paquete de preparación de datos denominado **iris-1.dprep** y se abre en el editor de preparación de datos.

9. Ahora vamos a preparar algunos datos básicos. Seleccione cada encabezado de columna para poder modificar su texto y cambie el nombre de cada columna de la siguiente manera: 

   Por orden, escriba **Sepal Length** (Longitud del sépalo), **Sepal Width** (Anchura del sépalo), **Petal Length** (Longitud del pétalo), **Petal Width** (Anchura del pétalo) y **Species** (Especie) para las cinco columnas respectivamente.

   ![Cambio del nombre de las columnas](media/tutorial-classifying-iris/rename_column.png)

10. Para contar valores distintos, seleccione la columna **Species** (Especie) y haga clic con el botón derecho en ella para seleccionarla. Seleccione **Value Counts** (Recuentos de valores) en el menú desplegable. 

   Esta acción abre el panel **Inspectors** (Inspectores) debajo de los datos. Aparece un histograma con cuatro barras. La columna de destino tiene tres valores distintos: **Iris_virginica**, **Iris_versicolor**, **Iris-setosa** y un valor **(null)**.

   ![Selección de Value Counts (Recuentos de valores)](media/tutorial-classifying-iris/value_count.png)

11. Para filtrar los valores null, seleccione la etiqueta "Null" y seleccione el signo menos (**-**). A continuación, el valor Null aparece en gris para indicar que se filtró. 

   ![Histograma de recuento de valores](media/tutorial-classifying-iris/filter_out.png)

12. Tenga en cuenta los pasos individuales que se detallan en el panel **PASOS**. Mientras cambiaba el nombre de las columnas y filtraba las filas con el valor NULL, cada acción se registró como un paso de preparación de datos. Puede editar los pasos individuales para ajustar la configuración, volver a ordenar los pasos y eliminarlos.

   ![Pasos](media/tutorial-classifying-iris/steps.png)

13. Cierre el editor de preparación de datos. Seleccione **Close** (Cerrar) (x) en la pestaña **iris-1** con el icono de gráfico. El trabajo se guarda automáticamente en el archivo **iris-1.dprep** que aparece bajo el encabezado **Data Preparations** (Preparaciones de datos).

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Generación de código de Python/PySpark para invocar el paquete de preparación de datos

<!-- The output/results of a Package can be explored in Python or via a Jupyter Notebook. A Package can be executed across multiple runtimes including local Python, Spark (including in Docker), and HDInsight. A Package contains one or more Dataflows that are the steps and transforms applied to the data. A Package may use another Package as a Data Source (referred to as a Reference Data Flow). -->

1. Busque el archivo **1.dprep iris** en la pestaña Data Preparations (Preparaciones de datos).

1. Haga clic con el botón derecho en el archivo **iris-1.dprep** y seleccione **Generate Data Access Code File** (Generar archivo de código de acceso a datos) en el menú contextual. 

   ![Generación de código](media/tutorial-classifying-iris/generate_code.png)

   Se abre un nuevo archivo llamado **1.py iris** con las siguientes líneas de código, que invocan la lógica creada como paquete de preparación de datos:

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

   En función del contexto en el que se ejecuta este código, `df` representa varios tipos de tramas de datos. Se usa una [trama de datos de Pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) cuando se ejecuta en un entorno de ejecución de Python, o una [trama de datos de Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html) cuando se ejecuta en un contexto de Spark. 
   
   Para más información sobre cómo preparar los datos en Azure Machine Learning Workbench, consulte la guía [de introducción a la preparación de los datos](data-prep-getting-started.md).

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha usado Azure Machine Learning Workbench para:
> [!div class="checklist"]
> * Creación de un nuevo proyecto
> * Generar un paquete de preparación de datos.
> * Generación de código de Python/PySpark para invocar el paquete de preparación de datos

Está listo para pasar a la siguiente parte de la serie de tutoriales, en la que aprenderá a crear un modelo de Azure Machine Learning:
> [!div class="nextstepaction"]
> [Tutorial 2: creación de modelos](tutorial-classifying-iris-part-2.md)
