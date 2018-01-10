---
title: "Clasificación de ingresos: proceso de ciencia de datos en equipo con Azure Machine Learning | Microsoft Docs"
description: "Cómo usar la plantilla de proceso de ciencia de datos en equipo para crear un proyecto de Azure Machine Learning que permita clasificar los ingresos de Estados Unidos."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: bradsev
ms.openlocfilehash: dc6279a1bac230146f4f0cebf2fbdbb6333cb7a1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="income-classification-with-team-data-science-process-tdsp-project"></a>Clasificación de ingresos con el proyecto de proceso de ciencia de datos en equipo (TDSP)

## <a name="introduction"></a>Introducción

La normalización de la estructura y la documentación de los proyectos de ciencia de datos, basada en un [ciclo de vida de los datos](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md) establecido, es fundamental para facilitar una colaboración eficaz entre los equipos de ciencia de datos. La creación de proyectos de Azure Machine Learning con la plantilla de [proceso de ciencia de datos en equipo (TDSP)](https://github.com/Azure/Microsoft-TDSP) proporciona un marco para conseguir esta normalización.

Previamente se presentó un [repositorio de GitHub para las plantillas y la estructura del proyecto de TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Sin embargo, no fue posible hasta ahora crear una instancia de las plantillas y de la estructura de TDSP en una herramienta de ciencia de datos. Ahora ya se ha habilitado la creación de proyectos de Azure Machine Learning instanciados con las [plantillas de estructura y documentación de TDSP para Azure Machine Learning](https://github.com/amlsamples/tdsp). [Aquí](https://aka.ms/how-to-use-tdsp-in-aml) se proporcionan instrucciones sobre cómo usar las plantillas y la estructura de TDSP en Azure Machine Learning. Aquí se muestra un ejemplo de cómo un proyecto real de aprendizaje automático se puede crear mediante la estructura de TDSP, rellenar con códigos, artefactos y documentos específicos del proyecto y ejecutar en Azure Machine Learning.

## <a name="link-to-github-repository"></a>Vínculo al repositorio de GitHub
[Aquí](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) se ofrece documentación de resumen sobre el ejemplo. Puede encontrar una documentación más extensa en el sitio de GitHub.

### <a name="purpose"></a>Propósito
El propósito principal de este ejemplo es mostrar cómo crear una instancia y ejecutar un proyecto de aprendizaje automático mediante la estructura y las plantillas del [proceso de ciencia de datos en equipo (TDSP)](https://github.com/Azure/Microsoft-TDSP) en Azure Machine Learning. Para ello, se han usado los conocidos [datos del censo de EE.UU. de 1994 del repositorio de Machine Learning de UCI](https://archive.ics.uci.edu/ml/datasets/adult). La tarea de modelado consiste en predecir las clases de ingresos anuales de Estados Unidos a partir de la información del censo de EE.UU. (por ejemplo, edad, raza, nivel de educación, país de origen, etc).

### <a name="scope"></a>Scope
 * Exploración de datos, entrenamiento e implementación de un modelo de aprendizaje automático que permita resolver el problema de predicción que se describe en la introducción del caso de uso. 
 * Ejecución del proyecto en Azure Machine Learning mediante la plantilla de proceso de ciencia de datos de equipo (TDSP) para este proyecto. Para la ejecución de proyectos y los informes se va a usar el ciclo de vida de TDSP.
 * Operacionalización de la solución directamente desde Azure Machine Learning en Azure Container Services.

 El proyecto destaca varias características de Azure Machine Learning, tales como la creación de instancias de estructura de TDSP, el uso y ejecución de código en Jupyter Notebooks así como en archivos Python, y la fácil operacionalización en Azure Container Services mediante Docker y Kubernetes.


## <a name="team-data-science-process-tdsp-lifecycle"></a>Ciclo de vida del proceso de ciencia de datos en equipo (TDSP)
Consulte [Ciclo de vida del proceso de ciencia de datos en equipo (TDSP)](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)

![](./media/scenario-tdsp-classifying-us-incomes/tdsp-lifecycle.jpg)

## <a name="prerequisites"></a>requisitos previos
### <a name="required-subscription-hardware-software"></a>Obligatorios: suscripción, hardware, software
1. Una [suscripción](https://azure.microsoft.com) de Azure. Puede obtener una [suscripción gratuita](https://azure.microsoft.com/free/?v=17.16&WT.srch=1&WT.mc_id=AID559320_SEM_cZGgGOIg) para ejecutar este ejemplo también.
2. Una instancia de [Azure Data Science Virtual Machine (DSVM) para Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm), (tamaño de máquina virtual: [DS3_V2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), con 4 CPU virtuales y 14 GB de RAM). Aunque se ha probado en una instancia de Azure DSVM, es probable que funcione en cualquier máquina de Windows 10.
3. Revise la documentación de Azure Machine Learning y sus servicios relacionados (vea los vínculos a continuación).
4. Asegúrese de que ha instalado correctamente Azure Machine Learning mediante la [Guía de instalación de inicio rápido](quickstart-installation.md).

El conjunto de datos de este ejemplo procede del repositorio de aprendizaje automático de UCI [[vínculo]](https://archive.ics.uci.edu/ml/datasets/adult). Los datos se han tomado de la base de datos del censo de EE.UU. de 1994 y contiene información censal y de ingresos de unas 50 000 personas. Se trata de un conjunto de datos estructurado que tiene características numéricas y de categorías, y un destino categórico que consta de dos categorías de ingresos ("> 50 K" o "<= 50 K"). 

### <a name="optional-version-control-repository"></a>Opcional: repositorio de control de versiones
Si quiere guardar y obtener versiones del proyecto y sus contenidos, deberá tener un repositorio de control de versiones donde esto se pueda hacer. Puede escribir la ubicación del repositorio de Git durante la creación del nuevo proyecto mediante la plantilla de TDSP en Azure Machine Learning. Consulte [how to use Git in Azure Machine Learning](using-git-ml-project.md) (Uso de Git en Azure Machine Learning) para obtener más detalles.

### <a name="informational-about-azure-machine-learning"></a>Más información sobre Azure Machine Learning
* [Preguntas frecuentes: Primeros pasos](frequently-asked-questions.md)
* [Información general](overview-what-is-azure-ml.md)
* [Instalación](quickstart-installation.md)
* [Ejecución](experimentation-service-configuration.md)
* [Uso de TDSP](https://aka.ms/how-to-use-tdsp-in-aml)
* [Lectura y escritura de archivos](how-to-read-write-files.md)
* [Uso de Git con Azure Machine Learning](using-git-ml-project.md)
* [Implementación de un modelo de aprendizaje automático como un servicio web](model-management-service-deploy.md)

### <a name="create-a-new-workbench-project"></a>Creación de un nuevo proyecto de Workbench

Cree un nuevo proyecto usando este ejemplo como plantilla:
1.  Abra Azure Machine Learning Workbench
2.  En la página **Proyectos**, haga clic en el signo **+** y seleccione **Nuevo proyecto**
3.  En el panel **Crear nuevo proyecto**, rellene la información del proyecto nuevo
4.  En el cuadro de búsqueda **Buscar plantillas de proyecto**, escriba "Classify US incomes - TDSP project" (Clasificación de ingresos de EE.UU.: proyecto TDSP) y seleccione la plantilla
5.  Haga clic en **Crear**

Si proporciona una ubicación de repositorio de Git vacía durante la creación del proyecto (en el cuadro correspondiente), ese repositorio se rellenará con la estructura y el contenido del proyecto después de crearlo.

## <a name="use-case-overview"></a>Información general del caso de uso
El problema es entender cómo los datos socioeconómicos extraídos del censo de EE.UU. pueden ayudar a predecir los ingresos anuales de los individuos. En función de estas características del censo, la tarea de aprendizaje automático consiste en predecir si los ingresos de una persona están por encima de 50 000 dólares o no (tarea de clasificación binaria).

## <a name="data-description"></a>Descripción de los datos
Para obtener información detallada acerca de los datos, consulte la [descripción](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names) en el repositorio de UCI. 

Estos datos se han extraído de la base de datos de la oficina del censo que se puede visitar en: https://www.census.gov/en.html. 


* Hay un total de 48 842 instancias (antes de realizar ningún filtrado), mezcla de continuas y discretas (entrenamiento = 32 561, prueba = 16 281)
* Probabilidad de la etiqueta "> 50 K": 23,93% / 24,78% (sin valores desconocidos)
* Probabilidad de la etiqueta "<=50 K": 76,07% / 75,22% (sin valores desconocidos)  

* **DESTINO**: clase de ingresos "> 50 K", "< = 50 K". Estos se reemplazan por 1 y 0 respectivamente en la fase de preparación de datos.
* **CARACTERÍSTICAS**: edad,categoría profesional, nivel de educación, raza, sexo, horas de trabajo a la semana, etc.


## <a name="project-structure-execution-and-reporting"></a>Estructura, ejecución e informes del proyecto

### <a name="structure"></a>sección Estructura
Para este proyecto, se usan la estructura de carpetas y las plantillas de documentación de TDSP (a continuación), que siguen el [ciclo de vida de TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). 

El proyecto se crea según las instrucciones proporcionadas [aquí](https://aka.ms/how-to-use-tdsp-in-aml). Una vez que se llena con el código y los artefactos del proyecto, la estructura tiene el siguiente aspecto (vea la estructura del proyecto en un cuadro rojo en la figura siguiente).


<img src="./media/scenario-tdsp-classifying-us-incomes/instantiation-4.png" width="900" height="700">

### <a name="execution"></a>Ejecución
En este ejemplo, se ejecuta código en el **entorno de proceso local**. Consulte los documentos de Azure Machine Learning para más información sobre las [opciones de ejecución](experimentation-service-configuration.md).

La ejecución de un script de Python en un entorno de ejecución de Python local es fácil:

    az ml experiment submit -c local my_script.py

Se puede hacer doble clic en los archivos de cuadernos de IPython desde la estructura del proyecto situada a la izquierda de la interfaz de usuario de Azure Machine Learning y ejecutarlos en el servidor de Jupyter Notebook.


El flujo de trabajo detallado de ciencia de datos fue el siguiente:

* [**Adquisición y comprensión de los datos**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/01_data_acquisition_and_understanding)

Los datos se descargaron en formato .csv de las direcciones URL del repositorio de aprendizaje automático de UCI [[vínculo]](https://archive.ics.uci.edu/ml/datasets/adult). Las características, el destino y sus transformaciones se describen en detalle en el archivo ProjectReport.md.

El código para la adquisición de datos y la comprensión se encuentra en: /code/01_data_acquisition_and_understanding.

La exploración de datos se realiza mediante la [utilidad IDEAR (exploración e informes interactivos de datos)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils/Python) de Python 3 publicada como parte del [conjunto TDSP de herramientas de ciencia de datos](https://github.com/Azure/Azure-TDSP-Utilities). Esta utilidad ayuda a generar informes de exploración de datos normalizados para los datos que contienen características y destinos numéricos y de categorías. A continuación se proporcionan los detalles de cómo se usa la utilidad IDEAR de Python 3. 

La ubicación del informe de exploración de datos final es [IDEAR.html](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/docs/deliverable_docs). A continuación se muestra una vista del informe de IDEAR:

![](./media/scenario-tdsp-classifying-us-incomes/idear.png)

* [**Modelado**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/02_modeling)

Hemos creado dos modelos con validación cruzada de 3 subconjuntos: red elástica y bosque aleatorio. Se ha utilizado el [muestreo de 59 puntos](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf) para la búsqueda en mallas aleatorias como estrategia para la validación cruzada y la optimización de los parámetros del modelo. La precisión de los modelos se midió mediante AUC (área bajo la curva) en el conjunto de datos de prueba. 

El código de modelado se encuentra en: /code/02_modeling.

El AUC de red elástica y bosque aleatorio fue > 0,85. Ambos modelos se guardaron en archivos pickled.pkl y se obtuvo la salida para los trazados ROC de ambos modelos. El AUC del modelo de bosque aleatorio fue 0,92 y el de red elástica 0,90. Además, para la interpretación del modelo, la importancia de las características para el modelo de bosque aleatorio se guarda en un archivo .csv y se traza en un archivo pdf (solo para las 20 características predictivas principales).

La curva ROC del **modelo de bosque aleatorio** de los datos de prueba se muestra a continuación. Este fue el modelo que se implementó:

![](./media/scenario-tdsp-classifying-us-incomes/rf-auc.png)

La importancia de las características (las 20 principales) del modelo de bosque aleatorio se muestra a continuación. Muestra que las características de importe de las ganancias de capital, educación y estado civil tienen la importancia más elevada.

![](./media/scenario-tdsp-classifying-us-incomes/featImportance.png)

* [**Implementación**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/03_deployment)

Hemos implementado el modelo de bosque aleatorio como un servicio web en un clúster en [Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/). El entorno de operacionalización aprovisiona Docker y Kubernetes en el clúster para administrar la implementación del servicio web. [Aquí](model-management-service-deploy.md) encontrará más información sobre el proceso de operacionalización. 

El código de implementación se encuentra en: /code/03_deployment.


### <a name="final-project-reporthttpsgithubcomazuremachinelearningsamples-tdspuciadultincomeblobmasterdocsdeliverabledocsprojectreportmd"></a>[Informe final del proyecto](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)
Se proporcionan detalles sobre cada una de las secciones anteriores en el informe de proyecto final compilado [ProjectReport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). El informe del proyecto también contiene más detalles sobre el caso de uso, las métricas de rendimiento del modelo, la implementación y la infraestructura en la que se ha desarrollado e implementado el proyecto.

Se puede proporcionar al cliente el informe de proyecto, junto con el contenido de toda la carpeta de proyecto y el repositorio de control de versiones.


## <a name="conclusion"></a>Conclusión

En este ejemplo, se ha mostrado cómo usar las plantillas y la estructura de TDSP en Azure Machine Learning. A través de las plantillas de documentos y artefactos puede:
1. Definir correctamente el propósito y el ámbito de un proyecto
2. Crear un equipo de proyecto con responsabilidades y roles distribuidos
3. Estructurar y ejecutar proyectos de acuerdo con las fases del ciclo de vida de TDSP
4. Desarrollar informes normalizados con utilidades de ciencia de datos de TDSP (por ejemplo, la exploración de datos y la visualización de informes de IDEAR).
5. Preparar un informe de proyecto de ciencia de datos final que se puede entregar a un cliente

Esperamos que use esta característica de Azure Machine Learning para facilitar la normalización y la colaboración en los equipos de ciencia de datos.

## <a name="next-steps"></a>Pasos siguientes

Consulte las referencias siguientes a modo de introducción:

[Uso del proceso de ciencia de datos en equipo (TDSP) en Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)

[Proceso de ciencia de datos en equipo (TDSP)](https://github.com/Azure/Microsoft-TDSP)

[Plantilla de proyecto TDSP para Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)

[Conjunto de datos de ingresos de EE. UU. obtenido del repositorio de aprendizaje automático de UCI](https://archive.ics.uci.edu/ml/datasets/adult)