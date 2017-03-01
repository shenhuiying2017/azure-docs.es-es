---
title: WebJobs en Servicio de aplicaciones de Azure
description: Aprenda a crear WebJobs para ejecutar pruebas en segundo plano, interactuar con servicios como Almacenamiento y Bus de servicio, y crear tareas programadas.
services: app-service
documentationcenter: 
author: christopheranderson
manager: erikre
editor: mollybos
ms.assetid: 85975432-04c9-4b83-b937-b30c082d52a1
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2015
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 45016e09e981bab6e4413048838c66f1108dc663
ms.lasthandoff: 11/17/2016


---
# <a name="using-webjobs-in-azure-app-service"></a>Uso de WebJobs en Servicio de aplicaciones de Azure
En este artículo se ofrecen vínculos a recursos de documentación sobre cómo usar WebJobs de Azure y el SDK de WebJobs de Azure. WebJobs de Azure proporciona una manera fácil de ejecutar scripts o programas como procesos en segundo plano en [Aplicaciones web del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714). Puede cargar y ejecutar un archivo ejecutable como cmd, bat, exe (.NET), ps1, sh, php, py, js y jar. Estos programas se ejecutan como WebJobs según una programación (cron) o de forma continua.

El SDK de WebJobs facilita el uso del Almacenamiento de Azure. El SDK de WebJobs tiene un sistema de enlace y desencadenador que funciona con Blobs de almacenamiento, Colas y Tablas de Microsoft Azure, así como con Colas de Bus de servicio.

La creación, implementación y administración de WebJobs se realiza de manera fluida gracias al conjunto de herramientas integradas en Visual Studio. Puede crear WebJobs a partir de plantillas, así como publicarlos y administrarlos (procesos de ejecución, detención, supervisión o implementación).

El panel de WebJobs en el Portal de Azure proporciona capacidades de administración eficaces que ofrecen un control completo sobre la ejecución de WebJobs, incluida la capacidad para invocar funciones individuales dentro de WebJobs. El panel también muestra los tiempos de ejecución de las funciones y el resultado del registro.

[!INCLUDE [app-service-blueprint-webjobs](../../includes/app-service-blueprint-webjobs.md)]


