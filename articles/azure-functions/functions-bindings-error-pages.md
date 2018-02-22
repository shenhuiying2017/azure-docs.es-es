---
title: Instrucciones de control de errores de Azure Functions | Microsoft Docs
description: "Se proporcionan instrucciones generales para controlar los errores que se producen cuando se ejecutan funciones, y vínculos a temas de errores específicos de enlace."
services: functions
cloud: 
documentationcenter: 
author: ggailey777
manager: cfowler
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/01/2018
ms.author: glenga; cfowler
ms.openlocfilehash: 82cdc62b3070811186583fdf1ce5e6ce421ebc34
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="azure-functions-error-handling"></a>Control de errores de Azure Functions

En este tema se proporcionan instrucciones generales para controlar los errores que se producen cuando se ejecutan funciones. También se proporcionan vínculos a temas que describen los errores específicos de enlace que pueden producirse. 

## <a name="handing-errors-in-functions"></a>Control de errores en funciones
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Códigos de error de enlace

Cuando se realizan integraciones con los servicios de Azure, pueden surgir errores que se originan desde las API de los servicios subyacentes. Encontrará vínculos a la documentación de códigos de error para estos servicios en la sección acerca de las **excepciones y códigos de retorno** de los siguientes temas de referencia de enlaces y desencadenadores:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Almacenamiento de tablas](functions-bindings-storage-table.md#exceptions-and-return-codes)
