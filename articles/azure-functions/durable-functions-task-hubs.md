---
title: 'Centrales de tareas en Durable Functions: Azure'
description: "Aprenda qué son las centrales de tareas en la extensión Durable Functions para Azure Functions. Obtenga información acerca de cómo configurar las centrales de tareas."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 313daf1c105caa8569ed43e59d9e18f184599214
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Centrales de tareas en Durable Functions (Azure Functions)

Una *central de tareas* para [Durable Functions](durable-functions-overview.md) es un contenedor lógico para las orquestaciones y actividades dentro del contexto de una única cuenta de Azure Storage. Puede haber varias funciones e incluso diversas aplicaciones de función en la misma central de tareas, que a menudo actúa como un contenedor de aplicaciones.

Las centrales de tareas no necesitan crearse explícitamente. Se inicializan automáticamente en tiempo de ejecución, con un nombre declarado en el archivo *host.json*. Cada central de tareas tiene su propio conjunto de colas de almacenamiento, tablas y blobs en una única cuenta de almacenamiento. Todas las aplicaciones de función que se ejecutan en una central de tareas determinada comparten los mismos recursos de almacenamiento. Las funciones de orquestador y actividad solo pueden interactuar entre sí si pertenecen a la misma central de tareas.

## <a name="configuring-a-task-hub-in-hostjson"></a>Configuración de una central de tareas en host.json

El nombre de una central de tareas se puede configurar en el archivo *host.json* de una aplicación de función.

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

Los nombres de la central de tareas deben empezar por una letra y estar formados únicamente por letras y números. Si no se especifica, el nombre predeterminado de la central de tareas de la aplicación de función es **DurableFunctionsHub**.

> [!NOTE]
> Si tiene varias aplicaciones de función que comparten una cuenta de almacenamiento, se recomienda configurar un nombre de central de tareas diferente para cada aplicación de función. Esto garantiza que cada aplicación de función se aísle correctamente de las demás.

## <a name="azure-storage-resources"></a>Recursos de almacenamiento de Azure

Una central de tareas consta de varios recursos de Azure Storage:

* Una o más colas de control.
* Una cola de elementos de trabajo.
* Una tabla de historial.
* Un contenedor de almacenamiento que contiene uno o varios blobs de concesión.

Todos estos recursos se crean automáticamente en la cuenta de Azure Storage predeterminada cuando se ejecutan funciones de orquestador o de actividad o cuando se programa su ejecución. El artículo [Rendimiento y escala](durable-functions-perf-and-scale.md) explica cómo se utilizan estos recursos.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Aprenda a controlar las versiones](durable-functions-versioning.md)

