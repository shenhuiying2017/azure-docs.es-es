---
title: 'Rendimiento y escalado horizontal en Durable Functions: Azure'
description: "Introducción a la extensión Durable Functions de Azure Functions."
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
ms.openlocfilehash: cc4c643b8d0e8de1b5c38ca7bb1b0193d6b0f05b
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Rendimiento y escalado horizontal en Durable Functions (Azure Functions)

Para optimizar el rendimiento y la escalabilidad, es importante comprender las características únicas de escalado de [Durable Functions](durable-functions-overview.md).

Para entender el comportamiento de escalado, tendrá que comprender algunos de los detalles del proveedor de Azure Storage subyacente que utiliza Durable Functions.

## <a name="history-table"></a>Tabla del historial

La tabla del historial es una tabla de Azure Storage que contiene el historial de eventos de todas las instancias de orquestación. Según se ejecutan las instancias, se van agregando nuevas filas a esta tabla. La clave de partición de esta tabla proviene del identificador de instancia de la orquestación. Estos valores son aleatorios en la mayoría de los casos, lo que garantiza una distribución óptima de las particiones internas en Azure Storage.

## <a name="internal-queue-triggers"></a>Desencadenadores de cola interna

Las funciones de orquestador y de actividad se desencadenan mediante colas internas de la cuenta de almacenamiento predeterminada de la aplicación de la función. Hay dos tipos de colas en Durable Functions: **de control** y **de elementos de trabajo**.

### <a name="the-work-item-queue"></a>Cola de elementos de trabajo

En Durable Functions hay una cola de elementos de trabajo por central de tareas. Se trata de una cola básica y se comporta del mismo modo que cualquier otra cola `queueTrigger` en Azure Functions. Esta cola se usa para desencadenar *funciones de actividad* sin estado. Cuando una aplicación de Durable Functions admita el escalado horizontal de varias máquinas virtuales, estas compiten para adquirir el trabajo de la cola de elementos de trabajo.

### <a name="control-queues"></a>Colas de control

Las *colas de control* son más sofisticadas que las de elementos de trabajo, más sencillas. Se utilizan para desencadenar las funciones de orquestador con estado. Dado que las instancias de las funciones de orquestador son las únicas con estado, no es posible utilizar un modelo de consumidor de competencia para distribuir la carga entre máquinas virtuales. En su lugar, se equilibra la carga de los mensajes del orquestador entre varias colas de control. Más detalles sobre esto en las secciones siguientes.

Las colas de control contienen una gran variedad de tipos de mensajes del ciclo de vida de la orquestación. Algunos ejemplos son los [mensajes de control del orquestador](durable-functions-instance-management.md), los mensajes de *respuesta* de la función de actividad y los del temporizador.

## <a name="orchestrator-scale-out"></a>Escalabilidad horizontal del orquestador

Las funciones de actividad no tienen estado y se escalan horizontalmente de manera automática con la incorporación de máquinas virtuales. En cambio, las funciones de orquestador,tienen *particiones* en una o más colas de control. El número de colas de control es fijo y no se puede cambiar una vez iniciada la creación de carga.

Mientras se escala horizontalmente a varias instancias de host de función (normalmente en máquinas virtuales diferentes), cada instancia adquiere un bloqueo en una de las colas de control. Este bloqueo garantiza que solo se ejecuta una instancia de orquestación en una máquina virtual a la vez. Esto significa que si una central de tareas está configurada con tres colas de control, se puede equilibrar la carga de las instancias de orquestación en tres máquinas virtuales como máximo. Se pueden agregar máquinas virtuales adicionales para aumentar la capacidad de ejecución de la función de actividad,  pero no se utilizarán los recursos adicionales para ejecutar funciones de orquestador.

El siguiente diagrama ilustra cómo interactúa el host de Azure Functions con las entidades de almacenamiento en un entorno de escalado horizontal.

![Diagrama de escalado](media/durable-functions-perf-and-scale/scale-diagram.png)

Como puede ver, todas las máquinas virtuales compiten por los mensajes de la cola de elementos de trabajo. Sin embargo, solo tres de ellas pueden adquirir mensajes de las colas de control y cada una bloquea una sola cola de control.

Las instancias de orquestación se distribuyen entre las instancias de cola de control mediante la ejecución de una función hash interna con el identificador de instancia de orquestación. Los identificadores de instancia se generan automática y aleatoriamente de forma predeterminada, lo cual garantiza que las instancias están equilibradas entre todas las colas de control disponibles. A día de hoy, el número predeterminado de particiones de la cola de control admitido es **4**.

> [!NOTE]
> Actualmente no se puede configurar el número de particiones de cola de control en Azure Functions. [Se está realizando el seguimiento del trabajo para admitir esta opción de configuración](https://github.com/Azure/azure-functions-durable-extension/issues/73).

En general, se han diseñado las funciones de orquestador para que sean ligeras, por lo que no necesitan gran cantidad de capacidad de computación. Por esta razón, no es necesario crear un gran número de particiones de cola de control para obtener un rendimiento excelente. En su lugar, la mayor parte del trabajo pesado se realiza en las funciones de la actividad sin estado, las cuales se pueden escalar en horizontal infinitamente.

## <a name="auto-scale"></a>Escalado automático

Al igual que todas las instancias de Azure Functions que se ejecutan en el plan de consumo, Durable Functions admite el escalado automático a través del [controlador de escala de Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-scale#runtime-scaling). El controlador de escala supervisa la longitud de la cola de elementos de trabajo y de las colas de control, y agrega o quita instancias de máquina virtual en consecuencia. Si la longitud de las colas de control aumenta con el tiempo, el controlador de escala continúa agregando instancias de máquina virtual hasta que se alcance el recuento de particiones de la cola de control. Si la longitud de la cola de elementos de trabajo aumenta con el tiempo, el controlador de escala continúa agregando instancias de máquina virtual hasta que se iguale la carga, independientemente del recuento de particiones de la cola de control.

## <a name="thread-usage"></a>Uso de subprocesos

Las funciones de orquestador se ejecutan en un solo subproceso. Esto es necesario para garantizar que la ejecución de las funciones de orquestador sean deterministas. Con esto en mente, es importante no mantener ocupado el subproceso de las funciones de orquestador innecesariamente con tareas como las operaciones de E/S (prohibida por diversos motivos), de bloqueo o de giro. Cualquier trabajo que requiera E/S, bloqueo o varios subprocesos debe derivarse a las funciones de actividad.

Las funciones de actividad tienen el mismo comportamiento que las funciones normales desencadenadas por colas. Esto significa que puede realizar operaciones de E/S, ejecutar operaciones de uso intensivo de CPU y utilizar varios subprocesos con seguridad. Dado que los desencadenadores de actividad no tienen estado, se escalan horizontalmente a un número ilimitado de máquinas virtuales sin problema.

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Instalación de la extensión Durable Functions y ejemplos](durable-functions-install.md)
