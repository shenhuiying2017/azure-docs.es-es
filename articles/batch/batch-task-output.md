---
title: "Almacenamiento de resultados o registros de trabajos y tareas completados en un almacén de datos: Azure Batch | Microsoft Docs"
description: "Conozca las diferentes opciones para guardar los datos de salida de las tareas y los trabajos del servicio Batch. Puede almacenar datos en Azure Storage o en otro almacén de datos."
services: batch
author: tamram
manager: timlt
editor: 
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3ca93e823f02b1483ed290cf89de191937d1e2c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="persist-job-and-task-output"></a>Trabajo persistente y resultado de la tarea

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Algunos ejemplos comunes de resultados de tareas incluyen:

- Archivos creados cuando la tarea procesa los datos de entrada.
- Archivos de registro asociados con la ejecución de tareas. 

En este artículo se describen diversas opciones para almacenar los resultados de las tareas, así como los escenarios en los que es más adecuada cada opción.   

## <a name="about-the-batch-file-conventions-standard"></a>Acerca del estándar Batch File Conventions

Batch define un conjunto opcional de convenciones para nombrar los archivos de salida de tareas en Azure Storage. El [estándar Batch File Conventions](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) describe estas convenciones. El estándar de convenciones de archivos determina los nombres del contenedor de destino y la ruta de acceso del blob en Azure Storage para un archivo de salida dado según los nombres del trabajo y de la tarea.

Es cosa suya si decide usar el estándar de convenciones de archivos para nombrar los archivos de datos de salida. Si lo desea, puede nombrar también el contenedor de destino y el blob. Si usa el estándar File Conventions para la denominación de archivos de salida, estos se podrán ver en [Azure Portal][portal].

Existen varias maneras diferentes de usar el estándar File Conventions:

- Si usa la API del servicio Batch para almacenar archivos de salida, puede elegir nombrar los contenedores de destino y los blobs de acuerdo con el estándar File Conventions. La API del servicio Batch le permite almacenar archivos de salida desde el código fuente, son modificar la aplicación de tareas.
- Si va a desarrollar con .NET, puede usar la [biblioteca Azure Batch File Conventions para .NET][nuget_package]. Una ventaja de usar esta biblioteca es que admite la consulta de los archivos de salida según su identificador o finalidad. La funcionalidad de consulta integrada facilita el acceso a los archivos de salida desde una aplicación cliente o desde otras tareas. Sin embargo, la aplicación de tareas se debe modificar para que llame a la biblioteca File Conventions. Para más información, consulte la referencia de la [biblioteca File Conventions para .NET](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.aspx).
- Si va a desarrollar con un lenguaje distinto de. NET, puede implementar el estándar File Conventions en su aplicación.

## <a name="design-considerations-for-persisting-output"></a>Consideraciones de diseño para el almacenamiento de la salida 

Cuando diseñe su solución de Batch, tenga en cuenta los siguientes factores relacionados con las salidas de trabajos y tareas.

* **Duración del nodo de proceso**: los nodos de proceso a menudo son transitorios, especialmente en los grupos con escalado automático habilitado. La salida de una tarea que se ejecuta en un nodo solo está disponible mientras existe el nodo, y solo dentro del período de retención de archivos que ha definido para la tarea. Si una tarea produce una salida que puede ser necesaria una vez completada la tarea, la tarea debe cargar sus archivos de salida en un almacén duradero, como Azure Storage.

* **Almacenamiento de la salida**: Azure Storage es el almacén de datos recomendado para la salida de tareas; no obstante, puede usar cualquier almacenamiento duradero. La escritura de la salida de tarea en Azure Storage está integrada en la API del servicio Batch. Si usa otra forma de almacenamiento duradero, deberá escribir la lógica de la aplicación para almacenar la salida de tarea por sí mismo.   

* **Recuperación de la salida**: puede recuperar la salida de tarea directamente de los nodos de proceso del grupo o desde Azure Storage u otro almacén de datos si la ha almacenado. Para recuperar la salida de una tarea directamente desde un nodo de proceso, necesita el nombre del archivo y la ubicación de la salida en el nodo. Si almacena la salida de tarea en Azure Storage, necesitará la ruta de acceso completa al archivo en Azure Storage para descargar los archivos de salida con el SDK de Azure Storage.

* **Visualización de la salida**: si navega a una tarea de Batch en Azure Portal y selecciona **Archivos en el nodo**, se mostrarán todos los archivos asociados a la tarea y no solo los archivos de salida que le interesan. Es más, los archivos de un nodo de proceso solo están disponibles mientras existe el nodo y solo durante el tiempo de retención del archivo que se haya establecido para la tarea. Para ver la salida de tarea que ha almacenado en Azure Storage, puede usar Azure Portal o una aplicación cliente de Azure Storage, como el [Explorador de Azure Storage][storage_explorer]. Para ver los datos de salida en Azure Storage con el portal u otra herramienta, debe saber la ubicación del archivo y desplazarse hasta ella directamente.

## <a name="options-for-persisting-output"></a>Opciones para almacenar la salida

Según el escenario, puede adoptar enfoques diferentes para almacenar la salida de tarea:

- Usar la API del servicio Batch.  
- Usar la biblioteca Batch File Conventions para .NET.  
- Implementar el estándar Batch File Conventions en su organización.
- Implementar una solución personalizada de movimiento de archivos.

En las siguientes secciones se describe cada enfoque con más detalle.

### <a name="use-the-batch-service-api"></a>Usar la API del servicio Batch.

Con la versión del 01-05- 2017, el servicio Batch agrega compatibilidad con la especificación de archivos de salida en Azure Storage para los datos de tareas cuando [agrega una tarea a un trabajo](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) o [agrega una colección de tareas a un trabajo](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

La API del servicio Batch admite el almacenamiento de datos de tareas en una cuenta de Azure Storage a partir de los grupos creados con la configuración de máquina virtual. Con la API del servicio Batch, puede almacenar los datos de tareas sin modificar la aplicación que ejecuta la tarea. Opcionalmente, puede seguir el [estándar Batch File Conventions](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) para la denominación de los archivos que almacena en Azure Storage. 

Use la API del servicio Batch para almacenar la salida de tarea en los siguientes casos:

- Quiera almacenar datos de tareas de Batch y tareas del administrador de trabajos en grupos creados con la configuración de máquina virtual.
- Quiera almacenar datos en un contenedor de Azure Storage con un nombre arbitrario.
- Quiera almacenar datos en un contenedor de Azure Storage denominado según el [estándar Batch File Conventions](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

> [!NOTE]
> La API del servicio Batch no admite el almacenamiento de datos de tareas que se ejecutan en grupos creados con la configuración de servicios en la nube. Para información sobre el almacenamiento de salidas de tareas de grupos que ejecutan la configuración de servicios en la nube, consulte [Persist job and task data to Azure Storage with the Batch File Conventions library for .NET to persist ](batch-task-output-file-conventions.md) (Almacenar datos de trabajos y tareas en Azure Storage con la biblioteca Batch File Conventions para .NET para conservarlos).
> 
> 

Para más información sobre el almacenamiento de salidas de tareas con la API del servicio Batch, consulte [Persist task data to Azure Storage with the Batch service API](batch-task-output-files.md) (Almacenamiento de datos de tareas en Azure Storage con la API del servicio Batch). Consulte también el proyecto de ejemplo [PersistOutputs][github_persistoutputs] en GitHub, que demuestra cómo usar la biblioteca de cliente de Batch para .NET con la finalidad de almacenar salidas de tareas en almacenamiento duradero.

### <a name="use-the-batch-file-conventions-library-for-net"></a>Usar la biblioteca Batch File Conventions para .NET

Los desarrolladores que compilan soluciones de Batch con C# y .NET pueden usar la [biblioteca File Conventions para .NET][nuget_package] para almacenar los datos de tareas en una cuenta de Azure Storage, de acuerdo con el [estándar Batch File Conventions](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). La biblioteca File Conventions administra el movimiento de archivos de salida a Azure Storage y nombra los contenedores y blobs de destino de una manera conocida.

Es compatible con la consulta de los archivos de salida bien por el identificador o la finalidad, lo que hace que sea fácil buscarlos sin necesidad de URI de archivo completos. 

Use la biblioteca Batch File Conventions para .NET para almacenar la salida de tarea en los siguientes casos:

- Quiere transmitir datos a Azure Storage mientras la tarea está aún en funcionamiento.
- Quiere almacenar los datos de grupos creados con la configuración de servicios en la nube o la configuración de máquina virtual.
- La aplicación cliente u otras tareas del trabajo deben encontrar y descargar los archivos de salida de tarea por identificador o finalidad. 
- Quiere crear puntos de comprobación o realizar la carga anticipada de los resultados iniciales.
- Quiere ver la salida de tarea en Azure Portal.

Para más información sobre el almacenamiento de salidas de tareas con la biblioteca File Conventions para .NET, consulte [Persist job and task data to Azure Storage with the Batch File Conventions library for .NET to persist ](batch-task-output-file-conventions.md) (Almacenamiento de datos de trabajos y tareas en Azure Storage con la biblioteca Batch File Conventions para .NET para conservarlos). Consulte también el proyecto de ejemplo [PersistOutputs][github_persistoutputs] en GitHub, que demuestra cómo usar la biblioteca File Conventions para .NET con la finalidad de almacenar salidas de tareas en almacenamiento duradero.

El proyecto de ejemplo [PersistOutputs][github_persistoutputs] de GitHub demuestra cómo usar la biblioteca de cliente de Batch para .NET con la finalidad de almacenar salidas de tareas en almacenamiento duradero.

### <a name="implement-the-batch-file-conventions-standard"></a>Implementar el estándar Batch File Conventions

Si va a usar un lenguaje distinto de .NET, puede implementar el [estándar Batch File Conventions](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) en su propia aplicación. 

Quizás quiera implementar el estándar de nomenclatura File Conventions por su cuenta cuando desee un esquema de nomenclatura probado o cuando quiera ver la salida de tarea en Azure Portal.

### <a name="implement-a-custom-file-movement-solution"></a>Implementar una solución personalizada de movimiento de archivos

También puede implementar su propia solución completa de movimiento de archivos. Use este enfoque en los siguientes casos:

- Quiere almacenar datos de tareas en un almacén de datos distinto de Azure Storage. Para cargar archivos en un almacén de datos como Azure SQL o Azure DataLake, puede crear un script personalizado o un ejecutable para cargar en esa ubicación. Luego, puede llamarlo en la línea de comandos después de ejecutar el ejecutable principal. Por ejemplo, en un nodo de Windows, puede llamar a estos dos comandos:`doMyWork.exe && uploadMyFilesToSql.exe`
- Quiere crear puntos de comprobación o realizar la carga anticipada de los resultados iniciales.
- Quiere mantener un control granular sobre el control de errores. Por ejemplo, puede que quiera implementar su propia solución si desea usar acciones de dependencia de tareas para realizar determinadas acciones de carga basadas en códigos específicos de salida de tarea. Para más información sobre las acciones de dependencia de tareas, consulte [Creación de dependencias de tareas para ejecutar las tareas que dependan de otras tareas](batch-task-dependencies.md). 

## <a name="next-steps"></a>Pasos siguientes

- Examine el uso de las nuevas características de la API del servicio Batch para almacenar los datos de tareas en [Persist task data to Azure Storage with the Batch service API](batch-task-output-files.md) (Almacenamiento de datos de tareas en Azure Storage con la API del servicio Batch).
- Aprenda sobre el uso de la biblioteca File Conventions para .NET en [Persist job and task data to Azure Storage with the Batch File Conventions library for .NET to persist ](batch-task-output-file-conventions.md) (Almacenamiento de datos de trabajos y tareas en Azure Storage con la biblioteca Batch File Conventions para .NET para conservarlos).
- Consulte el proyecto de ejemplo [PersistOutputs][github_persistoutputs] en GitHub, que demuestra cómo usar la biblioteca de cliente de Batch para .NET y la biblioteca File Conventions para .NET con la finalidad de almacenar salidas de tareas en almacenamiento duradero.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/
