---
title: "Azure Event Hubs Capture se habilita a través del portal | Microsoft Docs"
description: Habilite la funcionalidad de captura de Event Hubs mediante Azure Portal.
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1f3d373944b909db290f6cf2da7bf12a8a00e1c5
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017


---

<a id="enable-event-hubs-capture-using-the-azure-portal" class="xliff"></a>

# Habilitación de la funcionalidad de captura de Event Hubs mediante Azure Portal

Puede configurar la funcionalidad de captura en el momento de creación del centro de eventos mediante [Azure Portal](https://portal.azure.com). Para habilitar la captura, haga clic en el botón **On** de la hoja del portal **Crear centro de eventos**. A continuación, para configurar una cuenta de almacenamiento y un contenedor, haga clic en la sección **Contenedor** de la hoja. Dado que la captura de Event Hubs utiliza la autenticación de servicio a servicio con el almacenamiento, no es necesario especificar una cadena de conexión de almacenamiento. El selector de recursos selecciona automáticamente el identificador URI del recurso para la cuenta de almacenamiento. Si se usa Azure Resource Manager, es preciso suministrar explícitamente dicho identificador URI como una cadena.

La ventana de tiempo predeterminada es cinco minutos. El valor mínimo es 1 y el máximo 15. La ventana **Tamaño** tiene un intervalo de 10-500 MB.

![][1]

<a id="adding-capture-to-an-existing-event-hub" class="xliff"></a>

## Incorporación de Capture a un centro de eventos existente

Se pueden configurar Capture en los centros de eventos existentes que se encuentran en los espacios de nombres de Event Hubs. La característica no está disponible para los espacios de nombres anteriores del tipo **Mensajería** o **Mixto**. Para habilitar Capture en un centro de eventos existente o para cambiar la configuración Capture, haga clic en el espacio de nombres para cargar la hoja **Essentials** y, después, haga clic en el centro de eventos para el que desea habilitar o cambiar la configuración de Capture. Por último, haga clic en la sección **Propiedades** de la hoja abierta, tal como se muestra en la ilustración siguiente:

![][2]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png

<a id="next-steps" class="xliff"></a>

## Pasos siguientes

La Event Hubs Capture también se puede configurar a través de las plantillas de Azure Resource Manager. Para más información, consulte [Habilitar Capture mediante la plantilla de Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).

