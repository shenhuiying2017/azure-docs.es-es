---
title: "Estructuración de proyectos con la plantilla de Proceso de ciencia de datos en equipo | Microsoft Docs"
description: "Creación de instancias de plantillas de Proceso de ciencia de datos en equipo (TDSP) en Azure Machine Learning que estructuren proyectos para colaboración"
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
ms.date: 10/16/2017
ms.author: bradsev
ms.openlocfilehash: 0f7a6d0a58e2dcfd94e20a81ae12b42f238016c8
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="structure-projects-with-the-team-data-science-process-template"></a>Estructuración de proyectos con la plantilla de Proceso de ciencia de datos en equipo

En este documento se proporcionan instrucciones acerca de cómo crear proyectos de ciencia de datos en Azure Machine Learning con plantillas de Proceso de ciencia de datos en equipo (TDSP). Estas plantillas ayudan a estructurar los proyectos para colaboración y reproducibilidad. 


## <a name="what-is-the-team-data-science-process"></a>¿Qué es el Proceso de ciencia de datos en equipo (TDSP)?
El Proceso de ciencia de datos en equipo es un proceso de ciencia de datos ágil e iterativo para la ejecución y entrega de soluciones de análisis avanzado. Está diseñado para mejorar la colaboración y la eficacia de los equipos de ciencia de datos en las organizaciones empresariales. Contribuye a estos objetivos con cuatro componentes claves:

   * Una definición de [ciclo de vida de ciencia de datos](../team-data-science-process/lifecycle.md) estándar.
   * Una estructura de proyecto estandarizada, [plantillas de informes y documentación del proyecto](https://github.com/Azure/Azure-TDSP-ProjectTemplate).
   * Una infraestructura y recursos para la ejecución del proyecto, como una infraestructura de cálculo y almacenamiento y repositorios de código, respectivamente.
   * [Herramientas y utilidades](https://github.com/Azure/Azure-TDSP-Utilities) para las tareas del proyecto de ciencia de datos como:
      - Control de versiones colaborativo
      - Revisión de código
      - Exploración y modelado de datos
      - Planeamiento del trabajo

Para obtener una explicación más completa del TDSP, consulte [Introducción al proceso de ciencia de datos en equipo](../team-data-science-process/overview.md).

## <a name="why-should-you-use-the-tdsp-structure-and-templates"></a>¿Por qué debería utilizar las plantillas y la estructura del TDSP?
La normalización de la estructura, el ciclo de vida y la documentación de los proyectos de ciencia de datos es fundamental para facilitar una colaboración eficaz entre los equipos de ciencia de datos. Cree proyectos de aprendizaje automático con la plantilla de TDSP para proporcionar un marco así para el trabajo en equipo coordinado.

Previamente se presentó un [repositorio de GitHub para las plantillas y la estructura del proyecto de TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate) para ayudarle a lograr estos objetivos. Sin embargo, no fue posible hasta ahora crear una instancia de las plantillas y de la estructura de TDSP en una herramienta de ciencia de datos. Ahora, ya es posible crear un proyecto de aprendizaje automático que cree instancias de las plantillas de documentación y estructura de TDSP. 

## <a name="things-to-note-before-creating-a-new-project"></a>Cosas a tener en cuenta antes de crear un nuevo proyecto
Revise los siguientes elementos *antes* de crear un nuevo proyecto:
* Revise la [plantilla](https://aka.ms/tdspamlgithubrepo) de Machine Learning de TDSP.
* El contenido (distinto del que está en la carpeta "docs") no debe superar los 25 MB de tamaño. Consulte la nota que sigue a esta lista.
* La carpeta sample\_data es solo para archivos de datos pequeños (menos de 5 MB) con los que puede probar el código o iniciar un desarrollo temprano.
* Almacenar archivos, como Word y PowerPoint, puede aumentar sustancialmente el tamaño de la carpeta "docs". Le recomendamos que busque una Wiki colaborativa, [SharePoint](https://products.office.com/en-us/sharepoint/collaboration) u otro recurso colaborativo para almacenar estos archivos.
* Para más información sobre cómo tratar con archivos de gran tamaño y salidas en Machine Learning, lea [Guardar los cambios y trabajar con archivos de gran tamaño](http://aka.ms/aml-largefiles).

> [!NOTE]
> Todo el contenido relacionado con la documentación (texto, marcas, imágenes y otros archivos de documento) que *no* se utilice durante la ejecución del proyecto, a excepción del archivo readme.md, debe residir en la carpeta denominada "docs" (todo en minúsculas). Esta es una carpeta especial que la ejecución de Azure Machine Learning ignora para que su contenido no se copie innecesariamente en el destino de proceso. Los objetos de esta carpeta tampoco cuentan para el tamaño límite de 25 MB del proyecto. La carpeta "documentos", por ejemplo, es el lugar indicado para almacenar los archivos de imagen grandes necesarios en la documentación. Git seguirá controlando estos archivos a través del historial de ejecuciones. 

## <a name="instantiate-the-tdsp-structure-and-templates-from-the-machine-learning-template-gallery"></a>Creación de instancias de plantillas y de la estructura de TDSP desde la galería de plantillas de Machine Learning
Para crear un nuevo proyecto con las plantillas de documentación y la estructura del Proceso de ciencia de datos en equipo, complete los siguientes procedimientos.

### <a name="create-a-new-project"></a>Crear un nuevo proyecto
Para crear un nuevo proyecto, abra Azure Machine Learning. En **Proyectos** en el panel superior izquierdo, seleccione el signo más (**+**) y, a continuación, seleccione **Nuevo proyecto**.

![Nuevo proyecto](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="create-a-new-tdsp-structured-project"></a>Creación de un nuevo proyecto con la estructura de TDSP
   1. Especifique los parámetros y la información en el cuadro o lista pertinente:

      - Nombre de proyecto
      - Directorio del proyecto
      - Descripción del proyecto
      - Ruta de acceso del repositorio de GIT vacía
      - Nombre del área de trabajo

   2. En el cuadro **Búsqueda**, escriba **TDSP**. 
   3. Cuando aparezca la opción para **estructurar un proyecto con TDSP**, seleccione esa plantilla. 
   4. Seleccione el botón **Crear** para crear su nuevo proyecto con una estructura de TDSP. Si proporciona un repositorio de Git vacío durante la creación del proyecto (en el cuadro de texto correspondiente), ese repositorio se rellenará con la estructura y el contenido del proyecto después de crearlo.

![Crear un proyecto TDSP](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>Examen de la estructura del proyecto TDSP
Después de crear el nuevo proyecto, puede examinar su estructura (consulte el panel izquierdo de la ilustración siguiente). Contiene todos los aspectos de la documentación estandarizada sobre conocimiento del negocio. Estos elementos incluyen las fases del ciclo de vida del Proceso de ciencia de datos en equipo, la ubicación de los datos, las definiciones y la arquitectura de esta plantilla de documentación. 

La estructura que aparece se deriva de la estructura del TDSP que se publicó en el artículo sobre[estructura de proyectos, documentos y plantillas de artefactos de TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate), con algunas modificaciones. Por ejemplo, varias plantillas del documento se combinan en un marcado, denominado [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Estructura de carpetas del proyecto
La plantilla del proyecto TDSP contiene las siguientes carpetas de nivel superior:
   - **code**: contiene el código.
   - **docs**: contiene la documentación necesaria acerca del proyecto (por ejemplo, archivos Markdown, elementos multimedia relacionados, etc).
   - **sample_data**: contiene datos de **EJEMPLO (pequeño)** que pueden utilizarse para fases tempranas del desarrollo o pruebas. Normalmente, estos conjuntos no llegan a los 5 MB. Esta carpeta no sirve para conjuntos de datos grandes ni completos.

![Datos de ejemplo](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="use-the-tdsp-structure-and-templates"></a>Uso de plantillas y de la estructura del TDSP
Debe agregar información específica del proyecto a la estructura y a las plantillas. Se espera que los rellene con el código y la información necesaria para ejecutar y entregar el proyecto. El archivo [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) es una plantilla que se debe modificar con la información pertinente del proyecto. Se trata de un conjunto de preguntas que le ayudarán a rellenar la información para cada una de las cuatro fases del [ciclo de vida del proceso de ciencia de datos en equipo](../team-data-science-process/lifecycle.md).

Se proporciona un ejemplo del aspecto que puede tener una estructura de proyecto durante la ejecución o una vez finalizada en el panel izquierdo de la ilustración siguiente. Este proyecto pertenece al [Proyecto de ejemplo del Proceso de ciencia de datos en equipo: Clasificación de ingresos a partir de los datos del censo de EE. UU. en Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).

![Ejemplo de estructura del proyecto](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="document-your-project"></a>Documentación del proyecto
Consulte las [plantillas de documentación del TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate) para más información acerca de cómo documentar el proyecto. En la plantilla actual de documentación del TDSP de Machine Learning se recomienda incluir toda la información del archivo [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). Esta plantilla debe rellenarse con información específica del proyecto. 

También proporcionamos una plantilla [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings). Puede usar esta plantilla para incluir toda la información que no se había incluido en el documento del proyecto principal, pero que es útil documentar. 

### <a name="example-project-report"></a>Informe del proyecto de ejemplo
Puede obtener un [informe del proyecto de ejemplo](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Este informe del [proyecto de ejemplo de clasificación de ingresos de EE. UU.](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) muestra la creación de instancias y el uso de la plantilla del TDSP para un proyecto de ciencia de datos.

## <a name="next-steps"></a>pasos siguientes
Para facilitar la comprensión acerca de cómo pueden usarse las plantillas y la estructura del TDSP en proyectos de Machine Learning, se proporcionan varios ejemplos de proyectos completados en la documentación de Azure Machine Learning:

- Para ver un ejemplo que muestra cómo crear un proyecto de TDSP en Machine Learning, consulte el [Proyecto de ejemplo del proceso de ciencia de datos en equipo: Clasificación de ingresos a partir de los datos del censo de EE. UU. en Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).
- Para ver un ejemplo en el que se usa aprendizaje profundo en el procesamiento de lenguaje natural (NLP) en un proyecto con instancias de TDSP en Azure Machine Learning, consulte [Biomedical entity recognition using Natural Language Processing with Deep Learning](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction) (Reconocimiento de entidades biomédicas mediante el procesamiento de lenguaje natural con aprendizaje profundo).

