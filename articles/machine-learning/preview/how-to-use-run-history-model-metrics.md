---
title: "Cómo usar el historial de ejecución y las métricas de modelo de Azure Machine Learning Workbench | Microsoft Docs"
description: "Guía para usar las funciones de historial de ejecución y métricas de modelo de Azure Machine Learning Workbench"
services: machine-learning
author: jopela
ms.author: jopela
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 1a4f2d69907d220a8bbec5087ba431dc243c46d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>Cómo usar el historial de ejecución y las métricas de modelo de Azure Machine Learning Workbench

Azure Machine Learning Workbench admite experimentación de ciencia de datos a través de las funciones **Historial de ejecución** y **Model Metrics** (Métricas de modelo).
**Historial de ejecución** proporciona un medio para realizar un seguimiento de los resultados de los experimentos de aprendizaje automático y, después, habilita el filtrado y la comparación de los resultados.
**Model Metrics** (Métricas de modelo) se pueden registrar desde cualquier punto de las secuencias de comandos, rastreando los valores más importantes de los experimentos de ciencia de datos.
En este artículo se describe cómo hacer un uso eficaz de estas funciones para aumentar la velocidad y la calidad de la experimentación de ciencia de datos.

## <a name="prerequisites"></a>Requisitos previos
Para seguir esta guía, necesita:
* [Crear e instalar Azure Machine Learning](quickstart-installation.md)
- [Crear un proyecto](quickstart-installation.md)


## <a name="azure-ml-logging-api-overview"></a>Información general de la API de registro de Azure Machine Learning
La API de registro de Azure Machine Learning está disponible a través del módulo **azureml.logging** en Python (que se instala con Azure Machine Learning Workbench). Después de importar este módulo, puede usar el método **get_azureml_logger** para crear instancias de un objeto **registrador**.
A continuación, puede usar el método de **registro** del registrador para almacenar los pares clave-valor que generan las secuencias de comandos de Python.
Actualmente, las métricas del modelo de registro de tipo escalar, diccionario, lista y dataframe se admiten tal como se muestra.

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)

# log dictionary
logger.log("range", {"min":5, "max":7})

# log list
logger.log("all values", [5, 6, 7])

# log dataframe
import pandas
df = pandas.DataFrame.from_records([(1,2),(3,4)],columns=["a","b"])
logger.log("dataframe", df)
```
Es fácil usar el registrador dentro de los proyectos de Azure Machine Learning Workbench, y en este artículo se muestra cómo hacerlo.

## <a name="create-a-project-in-azure-ml-workbench"></a>Creación de un proyecto en Azure ML Workbench
Si todavía no tiene un proyecto, puede crear uno a partir de [Create and Install Quickstart](quickstart-installation.md) (Inicio rápido de crear e instalar) desde el **Panel del proyecto**, puede abrir la secuencia de comandos **iris_sklearn.py** (tal y como se muestra).

![obtener acceso a una secuencia de comandos desde la pestaña archivos](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

Puede usar esta secuencia de comandos como una guía para la implementación esperada del registro de métricas del modelo de Azure Machine Learning.

## <a name="parameterize-and-log-model-metrics-from-script"></a>Parametrizar y registrar las métricas de modelo desde una secuencia de comandos
En la secuencia de comandos **iris_sklearn.py**, el patrón esperado para la importación y construcción del registrador de Python se puede reducir a las siguientes líneas de código.

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

Una vez creado, puede invocar el método de **registro** con cualquier par nombre-valor.

Una vez completado el desarrollo, suele resultar útil parametrizar las secuencias de comandos para que los valores se puedan pasar a través de la línea de comandos.
En el ejemplo siguiente se muestra cómo aceptar parámetros de línea de comandos (si existen) con bibliotecas estándar de Python.
Esta secuencia de comandos toma un parámetro único para la tasa de regularización (*reg*) y lo usa para ajustar un modelo de clasificación en un esfuerzo por aumentar la *precisión* sin sobreajustar.
A continuación, estas variables se registran como *Regularization Rate* (Tasa de regularización) y *Precisión* para poder identificar fácilmente el modelo con resultados óptimos.

```Python
# change regularization rate and you will likely get a different accuracy.
reg = 0.01
# load regularization rate from argument if present
if len(sys.argv) > 1:
    reg = float(sys.argv[1])

print("Regularization rate is {}".format(reg))

# log the regularization rate
run_logger.log("Regularization Rate", reg)

# train a logistic regression model on the training set
clf1 = LogisticRegression(C=1/reg).fit(X_train, Y_train)
print (clf1)

# evaluate the test set
accuracy = clf1.score(X_test, Y_test)
print ("Accuracy is {}".format(accuracy))

# log accuracy
run_logger.log("Accuracy", accuracy)
```

Llevar a cabo estos pasos en las secuencias de comandos le permite hacer un uso óptimo de **Historial de ejecución**.

## <a name="launch-runs-from-project-dashboard"></a>Iniciar ejecuciones desde el panel de proyecto
Si vuelve al **Panel del proyecto**, puede iniciar una **ejecución controlada** mediante la selección de la secuencia de comandos **iris_sklearn.py** y la introducción del parámetro **Regularization Rate**  (Tasa de regularización) en el cuadro de edición **Argumentos**.

![especificar parámetros e iniciar ejecuciones](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

Puesto que el inicio de ejecuciones controladas no bloquea Azure Machine Learning Workbench, pueden iniciarse varias en paralelo.
El estado de cada ejecución controlada es visible en el **panel de trabajos** tal como se muestra.

![seguimiento de ejecuciones en el panel de trabajos](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

Esto permite el uso óptimo de los recursos sin necesidad de que cada trabajo se ejecute en serie.

## <a name="view-results-in-run-history"></a>Ver resultados en el historial de ejecución
El progreso y los resultados de las ejecuciones controladas están disponibles para su análisis en el **Historial de ejecución** de Azure Machine Learning Workbench.
**Historial de ejecución** proporciona tres vistas diferentes:
- Panel
- Detalles
- De comparación

La vista **Panel** muestra los datos a través de todas las ejecuciones de una secuencia de comandos, representados tanto en formato gráfico como tabular.
La vista **Detalles** muestra todos los datos generados a partir de una ejecución específica de una secuencia de comandos determinada, incluidas las métricas registradas y los archivos de salida (representados como trazados). La vista **Comparación** permite visualizar los resultados de dos o tres ejecuciones en paralelo, incluidos también los archivos de salida y las métricas registradas.

A través de ocho ejecuciones controladas de **iris_sklearn.py**, los valores del parámetro de **tasa de regularización** y del resultado de **precisión** se registraron para ilustrar cómo se usan las vistas de Historial de ejecución.

### <a name="run-history-dashboard"></a>Panel del historial de ejecución
Los resultados de las ocho ejecuciones están visibles en el **panel del historial de ejecución**.
Como **iris_sklearn.py** registra los valores de *Regularization Rate* (tasa de regularización) y de *Precisión*, el **panel del Historial de ejecución** muestra los gráficos de estos valores de forma predeterminada.

![panel del historial de ejecución](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

El **panel del Historial de ejecución** puede personalizarse para que los valores registrados también aparezcan en la cuadrícula.  Al hacer clic en el icono **personalizar** se muestra un diálogo de **personalización de la vista de lista** tal como se muestra.

![personalizar la cuadrícula del panel del historial de ejecución](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

Los valores que se registran durante las ejecuciones controladas están disponibles para su presentación y al seleccionar **Regularization Rate** (tasa de regularización) y **Precisión** se agregan a la cuadrícula.

![valores registrados en cuadrícula personalizada](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

Es fácil encontrar ejecuciones interesantes manteniendo el puntero sobre los puntos de los gráficos.  En este caso, la ejecución 7 proporcionó una buena precisión junto con una baja duración.

![buscar una ejecución interesante](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

Al hacer clic en un punto asociado con la ejecución 7 en cualquier gráfico o en el vínculo a la ejecución 7 en la cuadrícula, se muestran los **detalles del historial de ejecución**.

### <a name="run-history-details"></a>Detalles del historial de ejecución
Desde esta vista, se muestran los resultados completos de la ejecución 7 junto con los artefactos generados por la misma.

![detalles del historial de ejecución](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

La vista de **Detalles del historial de ejecución** también proporciona la capacidad de **descargar** los archivos escritos en la carpeta **./outputs** (el almacenamiento en la nube de Azure Machine Learning Workbench realiza una copia de seguridad de estos archivos del historial de ejecución, que es objeto de otro artículo).

Por último, **Detalles del historial de ejecución** proporciona un medio para restaurar el proyecto a su estado en el momento de esta ejecución.
Al hacer clic en el botón **Restaurar** se muestra un cuadro de diálogo de confirmación tal como se muestra a continuación.

![configuración de restaurar ejecución](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

Si confirma, los archivos se sobrescribirán o eliminarán, así que esta función debe usarse con cuidado.

### <a name="run-history-comparison"></a>Comparación del historial de ejecución
Al seleccionar dos o tres ejecuciones del **panel del historial de ejecución** y hacer clic en **Comparar** obtendrá acceso a la vista **Run History Comparison** (Comparación del historial de ejecución).
O bien, si hace clic en **Comparar** y selecciona una ejecución de la vista **Detalles del historial de ejecución**, se le conducirá a la vista **Run History Comparison** (Comparación del historial de ejecución).
En cualquier caso, la vista **Run History Comparison** (Comparación del historial de ejecución) le proporciona un medio para ver los resultados registrados y los artefactos de dos o tres ejecuciones en paralelo.

![comparación del historial de ejecución](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

Esta vista es especialmente útil para la comparación de trazados, pero también permite comparar cualquier propiedad de las ejecuciones.

### <a name="command-line-interface"></a>Interfaz de línea de comandos
Azure Machine Learning Workbench también proporciona acceso al historial de ejecución a través de su **Interfaz de línea de comandos**.
Para tener acceso a la **Interfaz de línea de comandos**, haga clic en el menú **Abrir símbolo del sistema** tal y como se muestra.

![abra el símbolo del sistema](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

Puede acceder a los comandos disponibles para el historial de ejecución a través de `az ml history`, con la ayuda en línea disponible mediante la adición de la marca `-h`.
```
$ az ml history -h

Group
    az ml history: View run history of machine learning experiments.

Commands:
    compare : Compare two runs.
    download: Download all the artifacts from a run into the destination path.
    info    : Details about one run.
    last    : Get detail about most recent run.
    list    : List runs.
    promote : Promote Artifacts.
```
Estos comandos proporcionan las mismas funciones y devuelven los mismos datos que muestran las **vistas del historial de ejecución**.
Por ejemplo, los resultados de la última ejecución pueden mostrarse como un objeto JSON.
```
$ az ml history last
{
  "Accuracy": 0.6792452830188679,
  "Regularization Rate": 0.078125,
  "attachments": "control_log, control_log.txt, driver_log, driver_log.txt, pid.txt, dataprep/backgroundProcess.log, dataprep/backgroundProcess_Engine.log, dataprep/backgroundProcess_EngineHost.log, dataprep/backgroundProcess_ProjectProvider.log, dataprep/backgroundProcess_Sampling.log, dataprep/backgroundProcess_Telemetry.log, outputs/cm.png, outputs/model.pkl, outputs/sdk_debug.txt, outputs/roc.png",
  "created_utc": "2017-09-08T00:58:01.611105+00:00",
  "description": null,
  "duration": "0:00:04.892389",
  "end_time_utc": "2017-09-08T00:58:07.951403+00:00",
  "experiment_id": "ce92d0a9-3e2c-4d51-85de-93ef22249ce1",
  "heartbeat_enabled": true,
  "hidden": false,
  "name": null,
  "parent_run_id": null,
  "properties": {
    "CommitId": "e77a5f0df2af1a482bbe39b70bfbb16b62228cb3"
  },
  "run_id": "IrisDemo_1504832281116",
  "run_number": 8,
  "script_name": "iris_sklearn.py",
  "start_time_utc": "2017-09-08T00:58:03.059014+00:00",
  "status": "Completed",
  "target": "local",
  "user_id": "e9fafe06-b0e4-4154-8374-aae34f9977b2"
}
```
Además, la lista de todas las ejecuciones se puede mostrar en un formato tabular.
```
$ az ml history list -o table
  Accuracy    Regularization Rate  Duration        Run_id                  Script_name      Start_time_utc                    Status
----------  ---------------------  --------------  ----------------------  ---------------  --------------------------------  ---------
  0.679245               0.078125  0:00:04.892389  IrisDemo_1504832281116  iris_sklearn.py  2017-09-08T00:58:03.059014+00:00  Completed
  0.679245               0.15625   0:00:04.734956  IrisDemo_1504832255198  iris_sklearn.py  2017-09-08T00:57:38.507196+00:00  Completed
  0.660377               0.3125    0:00:04.913762  IrisDemo_1504832234904  iris_sklearn.py  2017-09-08T00:57:16.849881+00:00  Completed
  0.660377               0.625     0:00:06.107764  IrisDemo_1504832210767  iris_sklearn.py  2017-09-08T00:56:54.602813+00:00  Completed
  0.641509               1.25      0:00:04.742727  IrisDemo_1504832171373  iris_sklearn.py  2017-09-08T00:56:13.879280+00:00  Completed
  0.660377               2.5       0:00:04.915401  IrisDemo_1504832148526  iris_sklearn.py  2017-09-08T00:55:52.937083+00:00  Completed
  0.641509               5         0:00:04.730627  IrisDemo_1504832127172  iris_sklearn.py  2017-09-08T00:55:29.612382+00:00  Completed
  0.641509              10         0:00:06.059082  IrisDemo_1504832109906  iris_sklearn.py  2017-09-08T00:55:14.739806+00:00  Completed

```
La **Interfaz de línea de comandos** es una ruta alternativa para tener acceso al potencial de Azure Machine Learning Workbench.

## <a name="next-steps"></a>Pasos siguientes
Estas funciones están disponibles para ayudarle con el proceso de experimentación de ciencia de datos.
Esperamos que las encuentre útiles y agradeceríamos mucho sus comentarios.
Esta es nuestra implementación inicial, pero tenemos una gran cantidad de mejoras planeadas.
Esperamos poder implementarlas de forma continua en Azure Machine Learning Workbench. 
