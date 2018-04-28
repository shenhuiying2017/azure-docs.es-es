---
title: archivo de inclusión
description: archivo de inclusión
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0dcb608553d4455403c073e34e83bccb81cc64db
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/20/2018
---
Una tarea que se ejecuta en Azure Batch puede generar datos de salida cuando se ejecuta. Los datos de salida de tareas se suelen almacenar para su recuperación por parte de otras tareas del trabajo, la aplicación cliente que ejecuta el trabajo o ambos. Las tareas escriben datos de salida en el sistema de archivos de un nodo de proceso de Batch, pero todos los datos del nodo se pierden al restablecer su imagen inicial o cuando el nodo abandona el grupo. Las tareas también pueden tener un período de retención de archivos, después del cual se eliminan los archivos creados por la tarea. Por estos motivos, es importante conservar el resultado de la tarea, que podría necesitar más adelante, en un almacén de datos como [Azure Storage](https://docs.microsoft.com/azure/storage/).

Para ver las opciones de cuenta de almacenamiento de Batch, consulte [Información general de las características de Batch](../articles/batch/batch-api-basics.md#azure-storage-account).
