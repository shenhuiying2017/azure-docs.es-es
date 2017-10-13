---
title: "Introducción a la comprobación de flujo de IP de Azure Network Watcher | Microsoft Docs"
description: "En esta página se proporciona una información general sobre la funcionalidad de la comprobación de flujo de IP de Network Watcher"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b2ad45e76320c59d18dce7b39166679801b4170a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Introducción a la comprobación de flujo de IP de Azure Network Watcher

La comprobación de flujo de IP permite comprobar si se permite o deniega un paquete hacia o desde una máquina virtual basada en la información de 5-tuplas. Esta información está formada por la dirección, el protocolo, la dirección IP local, la dirección IP remota, el puerto local y el puerto remoto. Si un grupo de seguridad deniega un paquete, se devuelve el nombre de la regla que lo deniega. Aunque se puede elegir cualquier dirección IP de origen o destino, esta característica ayuda a los administradores a diagnosticar rápidamente problemas de conectividad desde o hacia Internet y desde o hacia el entorno local.

La comprobación de flujo de IP tiene como destino una interfaz de red de una máquina virtual. El flujo de tráfico hacia o desde esa interfaz de red se comprueba en función de los valores configurados. Esta capacidad es útil para confirmar si una regla de un grupo de seguridad de red está bloqueando el tráfico de entrada o salida de una máquina virtual.

Es necesario crear una instancia de Network Watcher en todas las regiones en las que planea ejecutar la comprobación de flujo de IP. Network Watcher es un servicio regional y solo se puede ejecutar con recursos de la misma región. Esto no afecta a los resultados de la comprobación de flujo de IP ya que, aun así, se devolverá la ruta asociada con la NIC.

![1][1]

## <a name="next-steps"></a>Pasos siguientes

Visite el siguiente artículo para obtener información sobre si un paquete está permitido o no para una máquina virtual específica a través del portal. [Compruebe si se permite el tráfico en una máquina virtual con comprobación de flujo de IP mediante el portal](network-watcher-check-ip-flow-verify-portal.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png












