---
title: "Previsión de las series temporales de demanda de energía | Microsoft Docs"
description: "Cómo aplicar el aprendizaje automático a la previsión de las series temporales de demanda de energía en Azure Machine Learning Workbench."
services: machine-learning
documentationcenter: 
author: anta
manager: ireiter
editor: anta
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: anta
ms.openlocfilehash: bd0ddfcffdb6f946f9a3786f3d0add1740be861b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="energy-demand-time-series-forecasting"></a>Previsión de las series temporales de demanda de energía


La previsión de las series temporales es una tarea que consiste en predecir valores futuros mediante una secuencia temporal ordenada de observaciones. Es un problema habitual y tiene aplicaciones en numerosos sectores. Por ejemplo, las empresas minoristas necesitan realizar una previsión de las ventas de productos futuras de forma que puedan organizar eficazmente sus cadenas de suministro para satisfacer la demanda. De forma similar, las empresas de transporte deben realizar una estimación de la demanda de sus servicios para planear los requisitos de personal y las rutas de entrega antes de tiempo. En muchos casos, los riesgos financieros de unas previsiones incorrectas pueden resultar significativos. Por ello, la previsión es una actividad empresarial muy importante.

Este ejemplo muestra cómo se puede realizar una previsión de series temporales mediante la aplicación de técnicas de aprendizaje automático. Se le guiará a través de cada paso del proceso de modelado incluyendo:
- La preparación de los datos para limpiar y dar formato a los datos.
- La creación de funciones para los modelos de aprendizaje automático a partir de los datos de las series temporales sin procesar.
- El entrenamiento de varios modelos de aprendizaje automático.
- La evaluación de los modelos mediante la comparación de su rendimiento con el conjunto de datos de prueba apartado.
- La operacionalización del mejor modelo, y ponerlo a disposición a través de un servicio web para generar previsiones a petición.

Azure Machine Learning Workbench ayuda en cada paso del proceso de modelado: 
- El ejemplo muestra cómo el entorno de Jupyter Notebook, que está disponible directamente desde Workbench, puede hacer que el desarrollo de código de Python sea más fácil. El proceso de desarrollo de modelos se puede explicar a otros usuarios con más claridad mediante anotaciones y gráficos de Markdown. Estos cuadernos se pueden visualizar, editar y ejecutar directamente desde Workbench.
- Los modelos entrenados se pueden almacenar y cargar en Blob Storage. Esto ayuda a los científicos de datos a realizar un seguimiento de los objetos del modelo entrenado y a garantizar que se conservan y se pueden recuperar cuando sea necesario.
- Las métricas se pueden registrar mientras se ejecuta un script de Python, lo cual permite a los científicos de datos mantener un registro de puntuaciones del rendimiento del modelo.
- Workbench genera tablas personalizables de las métricas registradas permitiendo a los científicos de datos una fácil comparación con las métricas de rendimiento del modelo. Los gráficos se muestran automáticamente para que se pueda identificar con facilidad el modelo con el mejor rendimiento.
- Por último, en el ejemplo se muestra cómo un modelo entrenado se puede operacionalizar mediante su implementación en un servicio web en tiempo real.

## <a name="link-to-the-gallery-github-repository"></a>Vínculo al repositorio de GitHub de la galería
El repositorio de GitHub público para este escenario real contiene todos los materiales, incluidos ejemplos de código, necesarios para este ejemplo:

[https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)


## <a name="use-case-overview"></a>Información general del caso de uso

Este escenario se centra en la previsión de la demanda de energía, cuyo objetivo es predecir la futura carga de una red energética. Esta es una operación empresarial vital para las empresas del sector energético, ya que los operadores deben mantener un delicado equilibrio entre la energía que se consume en una red y la que se le suministra a esta. Si se le proporciona demasiada energía a la red, esto puede dar como resultado una pérdida de energía o averías técnicas. Por el contrario, si se proporciona muy poca energía, se pueden producir apagones, lo cual dejaría a los clientes sin electricidad. Normalmente, los operadores de la red pueden tomar decisiones a corto plazo para administrar el suministro energético en la red y mantener el equilibrio de la carga. Por ello, una previsión a corto plazo precisa de la demanda de energía resulta esencial para que el operador pueda tomar estas decisiones con confianza.

En este escenario se detalla la construcción de una solución de previsión de la demanda de energía mediante aprendizaje automático. La solución se ha entrenado mediante un conjunto de datos público que procede de la compañía [New York Independent System Operator (NYISO)](http://www3.dps.ny.gov/W/PSCWeb.nsf/All/298372E2CE4764E885257687006F39DF?OpenDocument), que opera la red eléctrica del estado de Nueva York. El conjunto incluye datos de la demanda energética por horas de la ciudad de Nueva York durante un período de cinco años. También se tomó un conjunto de datos adicional con la información meteorológica de cada hora en la ciudad de Nueva York durante el mismo período de tiempo de [darksky.net](https://darksky.net).

## <a name="prerequisites"></a>Requisitos previos

- Una [cuenta de Azure](https://azure.microsoft.com/free/) (hay disponibles versiones gratuitas de prueba).
- Una copia instalada de [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) siguiendo la [Guía de instalación de inicio rápido](./quickstart-installation.md) para instalar el programa y crear un área de trabajo.
- En este ejemplo se da por supuesto que está ejecutando Azure Machine Learning Workbench en Windows 10 con el [motor de Docker](https://www.docker.com/) instalado localmente. Si usa macOS, las instrucciones son en gran medida las mismas.
- Azure Machine Learning Operationalization instalado con una configuración de entorno de implementación local y una cuenta de administración de modelos creada tal y como se indica en esta [guía](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md).
- Este ejemplo requiere la actualización de la instalación de Pandas a la versión 0.20.3 o posterior, y la instalación de matplotlib. Haga clic en *Abrir símbolo del sistema* en el menú *Archivo* de Workbench y ejecute los siguientes comandos para instalar estas dependencias:

    ```
    conda install "pandas>=0.20.3"

    conda install matplotlib
    ```
    
## <a name="create-a-new-workbench-project"></a>Creación de un nuevo proyecto de Workbench

Cree un nuevo proyecto usando este ejemplo como plantilla:
1.  Abra Azure Machine Learning Workbench
2.  En la página **Proyectos**, haga clic en el signo **+** y seleccione **Nuevo proyecto**
3.  En el panel **Crear nuevo proyecto**, rellene la información del proyecto nuevo
4.  En el cuadro de búsqueda **Buscar plantillas de proyecto**, escriba "Energy Demand Time Series Forecasting" (Previsión de las series temporales de demanda de energía) y seleccione la plantilla
5.  Haga clic en **Crear**


## <a name="data-description"></a>Descripción de los datos

Hay dos conjuntos de datos: `nyc_demand.csv` y `nyc_weather.csv`:

**nyc_demand.csv** contiene los valores de demanda de energía cada hora de la ciudad de Nueva York durante los años 2012 a 2017. Los datos tienen la siguiente estructura simple:

| Marca de tiempo | Demanda |
| --- | --- |
| 2012-01-01 00:00:00 | 4937,5 |
| 2012-01-01 01:00:00 | 4752,1 |
| 2012-01-01 02:00:00 | 4542,6 |
| 2012-01-01 03:00:00 | 4357,7 |

Los valores de demanda se proporcionan en megavatios-hora (MWh). A continuación, se muestra un gráfico con la demanda energética durante un período de 7 días en julio de 2017:

![Demanda energética](./media/scenario-time-series-forecasting/energy_demand.png  "Demanda energética")

**nyc_weather.csv** contiene los valores meteorológicos de cada hora de la ciudad de Nueva York durante los años 2012 a 2017:

| Marca de tiempo | precipitaciones | temp
| --- | --- | --- |
| 2012-01-01 00:00:00 | 0.0 | 46,13 |
| 2012-01-01 01:00:00 | 0,01 | 45,89 |
| 2012-01-01 02:00:00 | 0,05 | 45,04 |
| 2012-01-01 03:00:00 | 0,02 | 45,03 |

*precipitación* es el porcentaje del nivel de precipitación. Los valores de *temp* (temperatura) se han vuelto a escalar de tal forma que todos los valores se encuentren en el intervalo [0, 100].

## <a name="scenario-structure"></a>Estructura del escenario

Cuando abra este proyecto por primera vez en Azure Machine Learning Workbench, vaya al panel *Archivos* en el lado izquierdo. Se proporcionan los siguientes archivos de código con este ejemplo:
- `1-data-preparation.ipynb`: este cuaderno de Jupyter permite descargar y procesar los datos para prepararlos para el modelado. Este es el primer cuaderno que va a ejecutar.
- `2-linear-regression.ipynb`: este cuaderno permite entrenar un modelo de regresión lineal con los datos de entrenamiento.
- `3-ridge.ipynb`: permite entrenar un modelo de regresión contraída.
- `4-ridge-poly2.ipynb`: permite entrenar un modelo de regresión contraída con funciones polinómicas de nivel 2.
- `5-mlp.ipynb`: permite entrenar una red neuronal de perceptrón multicapa.
- `6-dtree.ipynb`: permite entrenar un modelo de árbol de decisión.
- `7-gbm.ipynb`: permite entrenar un modelo de máquinas impulsadas por gradientes.
- `8-evaluate-model.py`: este script carga un modelo entrenado y lo usa para realizar predicciones sobre el conjunto de datos apartado. Produce métricas de evaluación del modelo para que se puedan realizar comparaciones entre el rendimiento de diferentes modelos.
- `9-forecast-output-exploration.ipynb`: este cuaderno produce visualizaciones de las previsiones generadas por los modelos de aprendizaje automático.
- `10-deploy-model.ipynb`: este cuaderno demuestra cómo un modelo de previsión entrenado se puede operacionalizar en un servicio web en tiempo real.
- `evaluate-all-models.py`: este script evalúa todos los modelos entrenados. Es una alternativa a la ejecución de `8-evaluate-model.py` para cada modelo entrenado individualmente.

### <a name="1-data-preparation-and-cleaning"></a>1. Preparación y limpieza de datos

Para empezar a ejecutar el ejemplo, primero haga clic en `1-data-preparation.ipynb` para abrir el cuaderno en modo de vista previa. Haga clic en ***Start Notebook Server*** (Iniciar servidor de Notebook) para iniciar un servidor de Jupyter Notebook y un kernel de Python en la máquina. Puede ejecutar los cuadernos desde Workbench, o puede usar el explorador para ir a [http://localhost:8888](http://localhost:8888). Tenga en cuenta que debe cambiar el kernel a *NOMBRE_PROYECTO local*. Puede hacer esto desde el menú *Kernel* del cuaderno en *Change kernel* (Cambiar kernel). Presione ***Mayús+Intro*** para ejecutar el código en las celdas individuales del cuaderno, o haga clic en *Ejecutar todo* en el menú *Celda* para ejecutar todo el cuaderno.

En primer lugar, el cuaderno descarga los datos de un contenedor de Blob Storage hospedado en Azure. Los datos, a continuación, se almacenan en la máquina en `AZUREML_NATIVE_SHARE_DIRECTORY`. Esta ubicación es accesible desde todos los cuadernos o scripts que se ejecutan en Workbench por lo que es un buen lugar para almacenar datos y modelos entrenados.

Una vez que se han descargado los datos, el cuaderno los limpia, rellenando los huecos en las series temporales y los valores que faltan mediante interpolación. La limpieza de los datos de la serie temporal de esta manera maximiza la cantidad de datos disponibles para el entrenamiento de los modelos.

Se crean varias características del modelo a partir de los datos limpios sin procesar. Estas características se pueden dividir en dos grupos:

- **Características basadas en el tiempo** que se derivan de la variable *timestamp*, por ejemplo el *mes*, el *día de la semana* y la *hora*.
- **Características con desfase** son valores diferidos de los valores de demanda o temperatura reales. El objetivo de estas características es capturar las dependencias condicionales entre los períodos de tiempo consecutivos del modelo.

El conjunto de datos resultante de la característica se puede usar al desarrollar modelos de previsión.

### <a name="2-model-development"></a>2. Desarrollo del modelo

Un primer enfoque de modelado podría ser un sencillo modelo de regresión lineal. El cuaderno `2-linear-regression.ipynb` muestra cómo entrenar un modelo de previsión de regresión lineal para predecir la demanda energética futura. En concreto, el modelo se va a entrenar para predecir la demanda de energía una hora después (*t + 1*) del período de tiempo actual (*t*). No obstante, se puede aplicar este sencillo modelo de forma recursiva en otros tiempos de prueba para generar previsiones para períodos de tiempo futuro, *t + n*.

Para entrenar un modelo, se crea una canalización predictiva que implica tres pasos distintos:

- **Una transformación de datos**: los formatos necesarios para los datos de entrada pueden variar dependiendo del algoritmo de aprendizaje automático. En este caso, el modelo de regresión lineal requiere que las variables de categorías estén codificadas mediante "one-hot".
- **Una rutina de validación cruzada**: a menudo un modelo de aprendizaje automático tiene uno o varios hiperparámetros que se deben optimizar mediante experimentación. La validación cruzada se puede utilizar para buscar el conjunto óptimo de valores de parámetro. El modelo se entrena repetidamente y se evalúa en los distintos subconjuntos del conjunto de datos. Los mejores parámetros son aquellos que obtienen el mejor rendimiento del modelo cuando realiza el promedio en los subconjuntos de la validación cruzada. Este proceso se explica con más detalle en `2-linear-regression.ipynb`.
- **Entrenamiento del modelo final**: el modelo se entrena en todo el conjunto de datos, empleando el mejor conjunto de hiperparámetros.

Hemos incluido una serie de 6 modelos diferentes en cuadernos numerados del 2 al 7. Cada cuaderno entrena un modelo diferente y lo almacena en la ubicación `AZUREML_NATIVE_SHARE_DIRECTORY`. Una vez que haya entrenado todos los modelos desarrollados para este escenario, podemos evaluarlos y compararlos como se indica en la sección siguiente.

### <a name="3-model-evaluation-and-comparison"></a>3. Evaluación y comparación de modelos

Se puede usar un modelo entrenado para realizar previsiones para períodos de tiempo futuros. Es mejor evaluar estos modelos en el conjunto de datos de prueba apartado. Mediante la evaluación de los distintos modelos en el mismo conjunto de datos no vistos, podemos comparar su rendimiento e identificar el mejor modelo. En este escenario, se aplicará el modelo entrenado de forma recursiva para realizar la previsión de múltiples estadios temporales futuros. En concreto, se generan previsiones de hasta seis horas más tarde *t + 6* de la hora actual, *t*. Posteriormente, se evalúan estas predicciones en comparación con la demanda real observada para cada período de tiempo.

Para evaluar un modelo, abra el script `8-evaluate-model.py` desde el panel *Archivos* de Workbench. Compruebe que *Configuración de ejecución* está establecido en **local** y, a continuación, escriba el nombre del modelo en el campo *Argumentos*. El nombre del modelo debe coincidir exactamente con la variable *model_name* establecida al comienzo del cuaderno en el que se entrena el modelo. Por ejemplo, escriba "linear_regression" para evaluar el modelo de regresión lineal entrenado. O bien, una vez que se hayan entrenado todos los modelos, puede evaluarlos con un comando mediante la ejecución de `evaluate-all-models.py`. Para ejecutar este script, abra un símbolo del sistema en Workbench y ejecute el comando siguiente:

```
python evaluate-all-models.py
```
El script `8-evaluate-model.py` realiza las operaciones siguientes:

- Carga una canalización de modelos entrenados del disco
- Realiza predicciones sobre el conjunto de datos de prueba
- Procesa las métricas de rendimiento del modelo y las registra
- Guarda las predicciones del conjunto de datos de prueba en `AZUREML_NATIVE_SHARE_DIRECTORY` para su posterior inspección y análisis.
- Guarda la canalización de modelos entrenados en la carpeta *outputs*.

> [!Note]
> Si guarda el modelo en la carpeta *outputs* el objeto de modelo se copiará automáticamente en la cuenta de Azure Blob Storage asociada con su cuenta de experimentación. Esto significa que siempre podrá recuperar el objeto de modelo guardado del blob, incluso aunque cambie la máquina o el contexto de proceso.

Vaya al panel *Historial de ejecución* y haga clic en `8-evaluate-model.py`. Verá algunos gráficos que muestran las métricas de rendimiento de varios modelos:

- **ME**: error medio de las previsiones. Esto también se puede interpretar como la desviación media de la previsión.
- **MPE**: [porcentaje de error medio](https://en.wikipedia.org/wiki/Mean_percentage_error) (ME expresado como un porcentaje de la demanda real)
- **MSE**: [error cuadrático medio](https://en.wikipedia.org/wiki/Mean_squared_error)
- **RMSE**: la raíz cuadrada del error cuadrático medio (la raíz cuadrada de MSE)
- **MAPE**: [error porcentual absoluto medio](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
- **sMAPE**: [error porcentual absoluto medio simétrico](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)
- **MAPE_base**: MAPE de una previsión tomada como referencia en la que la predicción es igual al último valor de demanda conocido.
- **MdRAE**: error absoluto de la media relativa. El valor medio del error de previsión con respecto al error de previsión de referencia. Un valor inferior a 1 significa que el rendimiento de la previsión es mejor que el valor de referencia.

Todas las métricas anteriores hacen referencia a la previsión de *t + 1*. Además de las métricas anteriores, también verá un conjunto de métricas correspondientes con el sufijo *_horizon*. Se trata del valor medio de las métricas durante todos los períodos del rango de la previsión, desde el período *t + 1* al período *t + 6*.

Si las métricas no se muestran en el área del gráfico, haga clic en el símbolo de engranaje en la esquina superior derecha. Compruebe que las métricas de rendimiento del modelo que le interesan están activas. Las métricas también aparecerán en una tabla por debajo de los gráficos. Nuevamente, puede personalizar esta tabla si hace clic en el símbolo de engranaje. Ordene la tabla por una métrica de rendimiento para identificar el mejor modelo. Si está interesado en ver cómo varía el rendimiento de la previsión desde el período *t + 1* al período *t + 6*, haga clic en la entrada del modelo en la tabla. Los gráficos que muestran las métricas MAPE, MPE y MdRAE a lo largo del período de previsión, aparecen en *Visualizaciones*.

A la hora de evaluar los modelos de previsión, puede ser muy útil visualizar las predicciones de salida. Esto ayuda a los científicos de datos a determinar si la previsión generada parece realista. También puede ayudar a identificar posibles problemas de la previsión si, por ejemplo, esta tiene un rendimiento bajo en determinados períodos de tiempo. El cuaderno `9-forecast-output-exploration.ipynb` producirá visualizaciones de las previsiones generadas para el conjunto de datos de prueba.

### <a name="4-deployment"></a>4. Implementación

El mejor modelo se puede operacionalizar mediante la implementación como servicio web en tiempo real. Este servicio web se puede invocar posteriormente para generar previsiones a petición a medida que nuevos datos pasan a estar disponibles. En este escenario, se necesita generar una nueva previsión cada hora para predecir la demanda de energía de la hora siguiente. Para llevar a cabo esta tarea, se puede implementar un servicio web que toma una matriz de características para un determinado período de tiempo horario como entrada y devuelve la demanda prevista como salida.

En este ejemplo, se implementa un servicio web en una máquina con Windows 10. Compruebe que ha realizado los pasos previos de la implementación local que se describen en esta [guía de introducción](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md) para la CLI de operacionalización. Una vez que haya configurado el entorno local y la cuenta de administración del modelo, ejecute el cuaderno `10-deploy-model.ipynb` para implementar el servicio web.

## <a name="conclusion"></a>Conclusión

Este ejemplo muestra cómo crear una solución de previsión de series temporales de extremo a extremo con el propósito de realizar una previsión de la demanda de energía. Muchos de los principios explorados en este ejemplo se pueden aplicar a otros escenarios de previsión y a otros sectores.

Este escenario muestra cómo Azure Machine Learning Workbench puede ayudar a un científico de datos a desarrollar soluciones prácticas con características útiles como el entorno de Jupyter Notebook y las funcionalidades de registro de métricas. El ejemplo también indica al lector cómo se puede operacionalizar e implementar un modelo mediante la CLI de operacionalización de Azure Machine Learning. Una vez implementado, la API del servicio web permite a los desarrolladores o ingenieros de datos integrar el modelo de previsión en una canalización de datos más amplia.
