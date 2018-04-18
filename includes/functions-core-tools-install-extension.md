---
title: archivo de inclusión
description: archivo de inclusión
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4c9b579534d9a7f2c55e9c589b1738fe060b1cf2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
Al desarrollar funciones a nivel local, puede instalar las extensiones necesarias con el uso de Azure Functions Core Tools desde el terminal o desde un símbolo del sistema. El siguiente comando `func extensions install` instala la extensión de enlace de Azure Cosmos DB:

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Reemplace `<taget_version>` con una versión específica del paquete. Las versiones válidas se enumeran en las páginas individuales del paquete en [NuGet.org](https://nuget.org).
