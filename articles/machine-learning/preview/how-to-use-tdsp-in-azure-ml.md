---
title: Estructurar proyectos con la plantilla de Proceso de ciencia de datos en equipo | Microsoft Docs
description: "Cómo crear instancias de plantillas de Proceso de ciencia de datos en equipo (TDSP) en Azure Machine Learning que estructuren proyectos para colaboración."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2e03b6bcb1bc5a617234c7801f22f8207a9f5a4e
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="structure-projects-with-team-data-science-process-template"></a>Estructurar proyectos con la plantilla de Proceso de ciencia de datos en equipo

En este documento se proporcionan instrucciones acerca de cómo crear proyectos de ciencia de datos en Azure Machine Learning con plantillas de Proceso de ciencia de datos en equipo (TDSP) que estructuran proyectos para colaboración y reproducibilidad. 


## <a name="what-is-team-data-science-process"></a>¿Qué es Proceso de ciencia de datos en equipo?
Proceso de ciencia de datos en equipo es un proceso de ciencia de datos ágil e iterativo para la ejecución y entrega de soluciones de análisis avanzado. Está diseñado para mejorar la colaboración y la eficacia de los equipos de ciencia de datos en las organizaciones empresariales. Contribuye a estos objetivos con cuatro componentes claves:

1. Una definición de [ciclo de vida de ciencia de datos](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md) estándar.
2. Una estructura de proyecto estandarizada, [plantillas de informes y documentación del proyecto](https://github.com/Azure/Azure-TDSP-ProjectTemplate).
3. Infraestructura y recursos para la ejecución del proyecto, como infraestructura de cálculo y almacenamiento, y repositorios de código.
4. [Herramientas y utilidades](https://github.com/Azure/Azure-TDSP-Utilities) para tareas de proyectos de ciencia de datos, como revisión de código y control de versiones colaborativos, modelado y exploración de datos, y planificación de trabajo.

Para obtener una explicación más completa del TDSP, consulte [Introducción al proceso de ciencia de datos en equipo](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/README.md).

## <a name="why-should-you-use-tdsp-structure-and-templates"></a>¿Por qué debería utilizar plantillas y la estructura del TDSP?
La normalización de la estructura, el ciclo de vida y la documentación de los proyectos de ciencia de datos es fundamental para facilitar una colaboración eficaz entre los equipos de ciencia de datos. Crear proyectos de Azure Machine Learning con la plantilla de TDSP proporciona un marco para conseguir un trabajo en equipo coordinado.

Previamente se presentó un [repositorio de GitHub para las plantillas y la estructura del proyecto de TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate) para ayudarle a lograr estos objetivos. Sin embargo, no fue posible hasta ahora crear una instancia de las plantillas y de la estructura de TDSP en una herramienta de ciencia de datos. Ahora ya es posible crear un proyecto de Azure Machine Learning que cree instancias de las plantillas de documentación y estructura de TDSP. 

## <a name="things-to-note-before-creating-a-new-project"></a>Cosas a tener en cuenta *antes de* crear un nuevo proyecto
Estas son las cosas que debe tener en cuenta o revisar *antes* de crear un nuevo proyecto:
* [Plantilla](https://aka.ms/tdspamlgithubrepo) de Azure Machine Learning de TDSP.
* El contenido (distinto del que está en la carpeta "docs") no debe superar los 25 MB de tamaño. Consulte la **NOTA** siguiente.
* La carpeta sample\_data es solo para archivos de datos pequeños (menos de 5 MB) con los que puede probar el código o hacer un desarrollo temprano.
* Almacenar archivos, como Office Word o PowerPoint, puede aumentar sustancialmente el tamaño de la carpeta "docs". Le recomendamos que busque una Wiki colaborativa, [SharePoint](https://products.office.com/en-us/sharepoint/collaboration) u otro recurso colaborativo para almacenar estos archivos.
* Para administrar archivos y resultados de Azure Machine Learning de gran tamaño, consulte [esto](http://aka.ms/aml-largefiles).

> [!NOTE]
> Asegúrese de que, además del archivo readme.md, todo el contenido relacionado con la documentación (texto, marcas, imágenes y otros archivos de documento) que NO se utilicen durante la ejecución del proyecto resida en la carpeta denominada "docs" (todo en minúsculas). Se trata de una carpeta especial que la ejecución de Azure Machine Learning ignora para que su contenido no se copie innecesariamente en el destino de proceso. Los objetos de esta carpeta tampoco cuentan para el tamaño límite de 25 MB del proyecto, por lo que puede almacenar los archivos de imagen grandes necesarios en la documentación, por ejemplo. Git los seguirá controlando a través del Historial de ejecuciones. 

## <a name="instantiating-tdsp-structure-and-templates-from-the-azure-machine-learning-template-gallery"></a>Creación de instancias de plantillas y de la estructura de TDSP desde la galería de plantillas de Azure Machine Learning
Para crear un nuevo proyecto con las plantillas de documentación y la estructura del Proceso de ciencia de datos en equipo, complete los siguientes procedimientos: 

### <a name="click-on-new-project"></a>Haga clic en "Nuevo proyecto"
Abra Azure Machine Learning. En **Proyectos** en la parte superior izquierda, haga clic en  **+**  y seleccione **Nuevo proyecto** para crear un nuevo proyecto.

![Nuevo proyecto](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="creating-a-new-tdsp-structured-project"></a>Creación de un nuevo proyecto con la estructura de TDSP
Especifique los parámetros y la información en los cuadros pertinentes:

- Nombre de proyecto
- Directorio del proyecto
- Descripción del proyecto
- Ruta de acceso del repositorio de GIT vacía
- Nombre del área de trabajo

Después, en el cuadro **Buscar**, escriba *TDSP*. Cuando aparezca la opción de **estructurar un proyecto con TDSP**, haga clic para seleccionar esa plantilla. A continuación, haga clic en el botón **Crear** para crear su nuevo proyecto con la estructura del TDSP. Si proporciona un repositorio de Git vacío durante la creación del proyecto (en el cuadro correspondiente), ese repositorio se rellenará con la estructura y el contenido del proyecto después de crearlo.

![Crear un proyecto TDSP](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>Examinar la estructura del proyecto TDSP
Una vez creado el proyecto nuevo, puede examinar su estructura (panel izquierdo de la ilustración siguiente). Contiene todos los aspectos de la documentación estandarizada para el conocimiento de la empresa, las fases del ciclo de vida de TDSP, la ubicación de los datos, la definición y la arquitectura de esta plantilla de documentación. Esta estructura se deriva de la estructura del TDSP publicada [aquí](https://github.com/Azure/Azure-TDSP-ProjectTemplate), con algunas modificaciones. Por ejemplo, varias plantillas del documento se combinan en un marcado, denominado [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Estructura de carpetas del proyecto
La plantilla del proyecto TDSP contiene las siguientes carpetas de nivel superior:
1. **code**: contiene el código
2. **docs**: contiene la documentación necesaria acerca del proyecto (por ejemplo, archivos de marcado, elementos multimedia relacionados, etc.)
3. **sample_data**: contiene datos de **EJEMPLO (pequeño)** que pueden utilizarse para fases tempranas del desarrollo o pruebas. Normalmente, no más de 5 MB. No sirve para conjuntos de datos grandes ni completos.

![Datos de ejemplo](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="using-the-tdsp-structure-and-templates"></a>Uso de plantillas y la estructura del TDSP
La estructura y las plantillas deben rellenarse con información específica del proyecto. Se espera que rellene estos con el código y la información necesaria para ejecutar y entregar el proyecto. El archivo [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) es una plantilla que se debe modificar directamente con la información pertinente del proyecto. Se trata de un conjunto de preguntas que le ayudarán a rellenar la información para cada una de las cuatro fases del [ciclo de vida del proceso de ciencia de datos en equipo](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md).

A continuación, se proporciona un ejemplo del aspecto que puede tener una estructura de proyecto durante la ejecución o una vez finalizada (panel izquierdo de la ilustración siguiente). Esto pertenece al [Proyecto de ejemplo del proceso de ciencia de datos en equipo: clasificar ingresos de los datos del censo de EE. UU. en Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).

![Ejemplo de estructura del proyecto](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="documenting-your-project"></a>Documentar el proyecto
Consulte las [plantillas de documentación de TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate) para documentar el proyecto. En la plantilla de documentación del TDSP de Azure Machine Learning actual, se recomienda incluir toda la información del archivo [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). Esta plantilla debe rellenarse con información específica del proyecto. 

También se proporciona otra plantilla [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) que debe incluir toda la información que no se había incluido en el documento del proyecto principal, pero que es útil registrar. 

### <a name="example-project-report"></a>Informe del proyecto de ejemplo
Encontrará un ejemplo del informe del proyecto [aquí](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliveralbe_docs/ProjectReport.md). Este es el informe del [proyecto de ejemplo de clasificación de ingresos de EE. UU.](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome), que muestra cómo la plantilla TDSP puede crear instancias y usar un proyecto de ciencia de datos.

## <a name="next-steps"></a>Pasos siguientes
Para facilitar la comprensión acerca de cómo pueden usarse las plantillas y la estructura del TDSP en proyectos de Azure Machine Learning, se proporcionan varios ejemplos de proyecto en la documentación de Azure Machine Learning.

- Para ver un ejemplo que muestra cómo crear un proyecto TDSP en Azure Machine Learning, consulte el [Proyecto de ejemplo del proceso de ciencia de datos en equipo: Clasificación de ingresos de los datos del censo de EE. UU. en Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome). 
- Para ver un ejemplo en el que se usa aprendizaje profundo en el procesamiento de lenguaje natural en un proyecto con instancias de TDSP en Azure Machine Learning, consulte [Biomedical entity recognition using Natural Language Processing with Deep Learning](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction) (Reconocimiento de entidades biomédicas mediante el procesamiento de lenguaje natural con aprendizaje profundo).

