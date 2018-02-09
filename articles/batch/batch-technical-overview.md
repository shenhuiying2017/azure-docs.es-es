---
title: Azure Batch ejecuta trabajos paralelos en la nube | Microsoft Docs
description: "Información acerca del servicio Azure Batch para cargas de trabajo HPC y paralelas a gran escala"
services: batch
documentationcenter: 
author: mscurrell
manager: jkabat
editor: 
ms.assetid: 
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/19/2018
ms.author: mscurrell
ms.custom: mvc
ms.openlocfilehash: 93eabc0bdf4889d89f8dc3fc30f99dafa1b3a47a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="what-is-azure-batch"></a>¿Qué es Azure Batch?

Use Azure Batch para ejecutar aplicaciones de informática de alto rendimiento (HPC) en paralelo y a gran escala, de manera eficaz en Azure. Azure Batch permite crear y administrar un conjunto de nodos de proceso (máquinas virtuales), instalar las aplicaciones que desea ejecutar y programar trabajos para que se ejecuten en los nodos. No hay ningún software de programador de clústeres o trabajos que instalar, administrar o escalar. En su lugar, use [API y herramientas de Batch](batch-apis-tools.md), scripts de línea de comandos o Azure Portal para configurar, administrar y supervisar los trabajos. 

Los desarrolladores pueden usar Batch como un servicio de plataforma para compilar aplicaciones SaaS o aplicaciones cliente en las que se requiere una ejecución a gran escala. Por ejemplo, compile un servicio con Batch para que ejecute una simulación de riesgo Monte Carlo para una empresa de servicios financieros, o un servicio que procese muchas imágenes.

No hay ningún cargo adicional por el uso de Batch. Solo deberá pagar por los recursos subyacentes que utilice como, por ejemplo, las máquinas virtuales, el almacenamiento y las redes.

Para obtener una comparación entre las opciones de soluciones HPC y Batch en Azure, consulte [Soluciones HPC, Batch y Big Compute](../virtual-machines/linux/high-performance-computing.md).

## <a name="run-parallel-workloads"></a>Ejecutar cargas de trabajo paralelas
Batch funciona bien con cargas de trabajo intrínsecamente paralelas (a veces llamadas "embarazosamente paralelas"). Las cargas de trabajo intrínsecamente paralelas son aquellas en las que las aplicaciones se pueden ejecutar de manera independiente y donde cada instancia completa una parte del trabajo. Cuando se ejecutan las aplicaciones, estas pueden acceder a algunos datos comunes, pero no se comunican con otras instancias de la aplicación. Las cargas de trabajo intrínsecamente paralelas pueden por tanto ejecutarse a gran escala en función de la cantidad de recursos de proceso disponibles para ejecutar las aplicaciones de forma simultánea.

A continuación puede ver algunos ejemplos de cargas de trabajo intrínsecamente paralelas con las que puede trabajar en Batch:

* Modelado de riesgos financieros mediante simulaciones Monte Carlo
* Representación de imágenes VFX y 3D
* Análisis y procesamiento de imágenes
* Transcodificación de elementos multimedia
* Análisis de secuencia genética
* Reconocimiento óptico de caracteres (OCR)
* Ingesta, procesamiento, extracción, transformación y carga de datos
* Ejecución de pruebas de software

También puede usar Batch para [ejecutar cargas de trabajo estrechamente acopladas](batch-mpi.md); se trata de cargas de trabajo donde las aplicaciones que se ejecutan necesitan comunicarse entre sí, en lugar de hacerlo de forma independiente. Las aplicaciones estrechamente acopladas normalmente utilizan Message Passing Interface (MPI) API. Puede ejecutar cargas de trabajo estrechamente acopladas con Batch mediante [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) o Intel MPI. Mejore el rendimiento de la aplicación con [informática de alto rendimiento](../virtual-machines/linux/sizes-hpc.md) especializada y tamaños de máquina virtual [optimizados para GPU](../virtual-machines/linux/sizes-gpu.md).

Estos son algunos ejemplos de cargas de trabajo estrechamente acopladas:
* Análisis de elementos finitos
* Dinámica de fluidos
* Aprendizaje de inteligencia artificial multinodo

Muchos trabajos estrechamente acoplados se pueden ejecutar en paralelo mediante Batch. Por ejemplo, realice varias simulaciones con un líquido que fluye a través de tubos de varios diámetros.

## <a name="additional-batch-capabilities"></a>Funcionalidades adicionales de Batch

Azure Batch también tiene funcionalidades disponibles de nivel más alto, específicas para cargas de trabajo:
* Batch admite [cargas de trabajo de representación a gran escala](batch-rendering-service.md) con herramientas de representación que incluyen Autodesk Maya, 3ds Max, Arnold, y V-Ray. 
* Los usuarios de R pueden instalar el [paquete doAzureParallel](https://github.com/Azure/doAzureParallel) para escalar horizontalmente con facilidad la ejecución de algoritmos R en grupos de Batch.

Azure Batch también se puede ejecutar como parte de un flujo de trabajo mayor de Azure para transformar datos, administrado mediante herramientas como [Azure Data Factory](../data-factory/v1/data-factory-data-processing-using-batch.md).


## <a name="how-it-works"></a>Cómo funciona
Un escenario habitual de Batch implica el escalado horizontal de trabajos intrínsecamente paralelos como la representación de imágenes para escenas 3D, en un grupo de nodos de proceso. Este grupo de nodos de proceso puede ser la "granja de representación" que le proporciona decenas, cientos o incluso miles de núcleos para su trabajo de representación.

El siguiente diagrama muestra los pasos de un flujo de trabajo común de Batch, con una aplicación cliente o un servicio hospedado usando Batch para ejecutar una carga de trabajo paralela.

![Tutorial sobre la solución Batch](./media/batch-technical-overview/tech_overview_03.png)


|Paso  |DESCRIPCIÓN  |
|---------|---------|
|1.  Cargue los **archivos de entrada** y las **aplicaciones** que los procesarán en su cuenta de Azure Storage.     |Los archivos de entrada pueden ser cualquier dato que vaya a procesar la aplicación, como diseños de modelos financieros, o archivos de vídeo que se van a transcodificar. Los archivos de aplicación pueden incluir scripts o aplicaciones que procesen datos, como un transcodificador multimedia.|
|2.  Cree un **grupo** de Batch de nodos de proceso en la cuenta de Batch, un **trabajo** para que ejecute la carga de trabajo en el grupo y **tareas** para ese trabajo.     | Los nodos de grupo son las máquinas virtuales que ejecutan las tareas. Especifique propiedades como el número y el tamaño de los nodos, una imagen de máquina virtual Windows o Linux, y la aplicación que desea instalar cuando se unan los nodos al grupo. Administre el costo y el tamaño del grupo mediante [máquinas virtuales de prioridad baja](batch-low-pri-vms.md) o con el [escalado automático](batch-automatic-scaling.md) del número de nodos si la carga de trabajo cambia. <br/><br/>Al agregar tareas a un trabajo, el servicio Batch programa automáticamente las tareas para su ejecución en los nodos de proceso en el grupo. Cada tarea usa la aplicación que ha cargado para procesar los archivos de entrada. |
|3.  Descargue los **archivos de entrada** y las **aplicaciones** en Batch.     |Antes de que cada tarea se ejecute, esta puede descargar los datos de entrada que va a procesar en el nodo de proceso al que está asignada. Si la aplicación no está ya instalada en los nodos de proceso, se puede descargar aquí. Cuando las descargas de Azure Storage se completan, la tarea se ejecuta en el nodo asignado.|
|4.  Supervise la **ejecución de tareas**.     |Mientras se ejecutan las tareas, puede solicitar a Batch que supervise el progreso del trabajo y sus tareas. Un servicio o una aplicación cliente se comunican con el servicio Batch a través de HTTPS. Dado que puede que supervise miles de tareas que se ejecutan en miles de nodos de proceso, asegúrese de [consultar el servicio Batch de forma eficaz](batch-efficient-list-queries.md).|
|5.  Cargue la **salida de la tarea**.     |Cuando se completan las tareas, estas cargan los datos de sus resultados en Azure Storage. Los archivos también se pueden recuperar directamente del sistema de archivos de un nodo de proceso.|
|6.  Descargue los **archivos de salida**.     |Cuando la supervisión detecta que se han completado las tareas en su trabajo, el servicio o la aplicación de cliente puede descargar los datos de salida para su posterior procesamiento.|




No olvide que esta es simplemente una forma de usar Batch y que este escenario describe solo algunas de sus características. Por ejemplo, puede ejecutar [varias tareas en paralelo](batch-parallel-node-tasks.md) en cada nodo de proceso. O bien, usar [tareas de preparación y finalización de trabajos](batch-job-prep-release.md) para preparar los nodos para los trabajos y, a continuación, limpiarlos. 

Consulte [Introducción a las características de Batch para desarrolladores](batch-api-basics.md) para obtener información más detallada acerca de los grupos, nodos, trabajos y tareas, así como las numerosas características de API que se pueden usar al compilar cualquier aplicación de Batch. 

## <a name="next-steps"></a>pasos siguientes

Comience a usar Azure Batch con alguna de estas guías de inicio rápido:
* [Ejecución del primer trabajo de Batch con la CLI de Azure](quick-create-cli.md)
* [Ejecución del primer trabajo de Batch con Azure Portal](quick-create-portal.md)
* [Ejecución del primer trabajo de Batch con la API de .NET](quick-run-dotnet.md)
* [Ejecución del primer trabajo de Batch con la API de Python](quick-run-python.md)

