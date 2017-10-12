---
title: "Creación de un espacio de nombres de Service Bus en Azure Portal | Microsoft Docs"
description: "Creación de un espacio de nombres de Service Bus mediante Azure Portal."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/27/2017
ms.author: sethm
ms.openlocfilehash: c8654ed547a9001e2e968d2a45d990a73ef27d3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Creación de un espacio de nombres de Service Bus mediante Azure Portal

Un espacio de nombres es un contenedor con un ámbito para todos los componentes de la mensajería. Varias colas y temas pueden residir en un único espacio de nombres, y los espacios de nombres suelen servir de contenedores de aplicación. Existen dos formas diferentes de crear espacios de nombres de Service Bus:

1. Azure Portal (este artículo)
2. [Plantillas de Resource Manager][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Creación de un espacio de nombres en Azure Portal

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

¡Enhorabuena! Ha creado un espacio de nombres de mensajería de Service Bus.

## <a name="next-steps"></a>Pasos siguientes

Consulte nuestros [ejemplos de GitHub][github-samples], que muestran algunas de las características más avanzadas de la mensajería de Azure Service Bus.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples
