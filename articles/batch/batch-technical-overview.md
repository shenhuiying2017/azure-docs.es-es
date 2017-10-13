---
title: Azure Batch ejecuta soluciones de procesos paralelos a gran escala en la nube | Microsoft Docs
description: "Información acerca del servicio Lote de Azure para cargas de trabajo HPC y paralelas a gran escala"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/05/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a99f96db0c1e8bcd0cf29c564e5badf0eb728e56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="run-intrinsically-parallel-workloads-with-batch"></a>Ejecución de cargas de trabajo paralelas intrínsecamente con Batch

Azure Batch es un servicio de plataforma para ejecutar aplicaciones en paralelo a gran escala y de informática de alto rendimiento (HPC) de manera eficaz en la nube. Azure Batch programa el trabajo que conlleva mucho proceso para que se ejecute en una colección administrada de máquinas virtuales y puede escalar automáticamente los recursos de proceso para satisfacer las necesidades de sus trabajos.

Con Azure Batch, se pueden definir fácilmente los recursos de proceso de Azure para ejecutar las aplicaciones en paralelo y a escala. No es preciso crear, configurar ni administrar manualmente un clúster de HPC, máquinas virtuales individuales, redes virtuales ni una infraestructura compleja de programación de tareas y trabajos. Azure Batch automatiza o simplifica estas tareas automáticamente.

## <a name="use-cases-for-batch"></a>Casos de uso de Lote
Batch es un servicio administrado de Azure que se usa para el *procesamiento por lotes* o la *computación por lotes*, es decir, la ejecución de grandes volúmenes de tareas similares para obtener un resultado deseado. La computación por lotes la utilizan normalmente organizaciones que procesan, transforman y analizan grandes volúmenes de datos con regularidad.

Lote funciona bien con cargas de trabajo y aplicaciones intrínsecamente paralelas (a veces llamadas "lamentablemente paralelas"). Las cargas de trabajo intrínsecamente paralelas son las que se dividen fácilmente en varias tareas que trabajan simultáneamente en varios equipos.

![Tareas paralelas][1]<br/>

Algunos ejemplos de cargas de trabajo que normalmente se procesan mediante esta técnica son:

* Modelado de riesgos financieros
* Análisis de datos de clima e hidrología
* Representación, análisis y procesamiento de imágenes
* Codificación y transcodificación multimedia
* Análisis de secuencia genética
* Análisis de esfuerzos en ingeniería
* Pruebas de software

El servicio Lote también puede realizar cálculos paralelos con un paso de reducción al final y ejecutar cargas de trabajo de HPC más complejas, como aplicaciones de la [Interfaz de paso de mensajes (MPI)](batch-mpi.md) .

Para obtener una comparación entre las opciones de soluciones HPC y Batch en Azure, consulte [Soluciones HPC, Batch y Big Compute](../virtual-machines/linux/high-performance-computing.md).

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="scenario-scale-out-a-parallel-workload"></a>Escenario: Escalar horizontalmente una carga de trabajo paralela
Una solución común que usa las API de Lote para interactuar con el servicio Lote implica escalar horizontalmente trabajo intrínsecamente paralelo, como la representación de imágenes para escenas 3D, en un grupo de nodos de proceso. Este grupo de nodos de proceso puede ser la "granja de representación" que le proporciona decenas, cientos o incluso miles de núcleos de su trabajo de representación, por ejemplo.

El siguiente diagrama muestra un flujo de trabajo común de Lote, con una aplicación cliente o un servicio hospedado usando Lote para ejecutar una carga de trabajo paralela.

![Flujo de trabajo de soluciones de Lote][2]

En este escenario común, la aplicación o servicio procesa una carga de trabajo de computación en Lote de Azure realizando los pasos siguientes:

1. Cargue los **archivos de entrada** y la **aplicación** que los procesará en su cuenta de Azure Storage. Los archivos de entrada pueden ser cualquier dato que vaya a procesar la aplicación, como diseños de modelos financieros, o archivos de vídeo que se van a transcodificar. Los archivos de aplicación pueden ser cualquier aplicación que se use para procesar los datos, como una aplicación de representación de 3D o un transcodificador de medios.
2. Cree un **grupo** de nodos de proceso de Lote en su cuenta de Lote (dichos nodos son las máquinas virtuales que ejecutarán las tareas). Especifique propiedades como el [tamaño del nodo](../cloud-services/cloud-services-sizes-specs.md), su sistema operativo y la ubicación en Azure Storage de la aplicación que se instala cuando los nodos se unen al grupo (la aplicación que cargó en el paso 1). También puede configurar el grupo para que se [escale automáticamente](batch-automatic-scaling.md), en respuesta a la carga de trabajo que generan las tareas. El ajuste de escala automático ajusta dinámicamente el número de nodos de proceso del grupo.
3. Cree un **trabajo** de Lote que ejecute la carga de trabajo en el grupo de nodos de proceso. Cuando crea un trabajo, lo asocia a un grupo de Lote.
4. Agregue **tareas** al trabajo. Al agregar tareas a un trabajo, el servicio Lote programa automáticamente las tareas para su ejecución en los nodos de proceso en el grupo. Cada tarea usa la aplicación que ha cargado para procesar los archivos de entrada.
   
   * 4a. Antes de que la tarea se ejecute, puede descargar los datos (los archivos de entrada) que va a procesar al nodo de proceso al que está asignada. Si la aplicación no se ha instalado aún en el nodo (consulte el paso 2), se puede descargar aquí. Cuando haya completado las descargas, las tareas se ejecutan en sus nodos asignados.
5. Mientras se ejecutan las tareas, puede solicitar a Lote que supervise el progreso del trabajo y sus tareas. Un servicio o una aplicación cliente se comunican con el servicio Batch a través de HTTPS. Dado que puede que supervise miles de tareas que se ejecutan en miles de nodos de proceso, asegúrese de [consultar el servicio Batch de forma eficaz](batch-efficient-list-queries.md).
6. Cuando se completan las tareas, estas cargan los datos de sus resultados en Almacenamiento de Azure. Los archivos también se pueden recuperar directamente del sistema de archivos de un nodo de proceso.
7. Cuando la supervisión detecta que se han completado las tareas en su trabajo, el servicio o la aplicación de cliente puede descargar los datos de salida para su posterior procesamiento o evaluación.

No olvide que esta es simplemente una forma de usar Lote y que este escenario describe solo algunas de sus características. Por ejemplo, puede ejecutar [varias tareas en paralelo](batch-parallel-node-tasks.md) en cada nodo de proceso y puede usar las [tareas de preparación y finalización del trabajo](batch-job-prep-release.md) para preparar los nodos para los trabajos y limpiar después.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha visto información general de alto nivel del servicio Lote, es hora de profundizar en el servicio para aprender cómo puede utilizarlo para procesar las cargas de trabajo paralelas de proceso intensivo.

* Consulte [Información general de las características de Lote para desarrolladores](batch-api-basics.md), donde encontrará información esencial para cualquier persona que vaya a utilizar Lote. El artículo contiene información más detallada acerca de los recursos del servicio de Lote, como grupos, nodos, trabajos y tareas, así como las numerosas características de API que se pueden usar al compilar cualquier aplicación de Lote.
* Obtenga información acerca de las [API y herramientas de Batch](batch-apis-tools.md) disponibles para la creación de soluciones de Batch.
* [Introducción a la biblioteca de Lote de Azure para .NET](batch-dotnet-get-started.md) . Este artículo debería ser una de sus primeras lecturas durante el periodo de aprendizaje de Lote. También hay una [versión para Python](batch-python-tutorial.md) del tutorial.
* Descargue los [ejemplos de código en GitHub][github_samples] para ver la forma en que C# y Python pueden comunicarse con Batch para programar y procesar las cargas de trabajo de ejemplo.
* Consulte la [ruta de aprendizaje de Batch][learning_path] para hacerse una idea de los recursos que tiene a su disposición a medida que aprende a trabajar con Batch.


[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png
