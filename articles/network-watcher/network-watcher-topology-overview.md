---
title: "Introducción a la topología de Azure Network Watcher | Microsoft Docs"
description: "En esta página se proporciona una introducción a las funcionalidades de la topología de Network Watcher."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: e753a435-38e0-482b-846b-121cb547555c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: d5cb5ba431eeae1956a9dbf1d48561c66faef9a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-topology-in-azure-network-watcher"></a>Introducción a la topología de Azure Network Watcher

La topología devuelve un gráfo de recursos de red de una red virtual. El grafo traza la interconexión entre los recursos para representar la conectividad de red de un extremo a otro.

![información general de la topología][1]

En el portal, la topología devuelve los objetos de recursos por cada red virtual. Las relaciones se representan mediante líneas entre los recursos. No se mostrarán los recursos fuera de la región de Network Watcher, aunque estén en el grupo de recursos. Los recursos que se devuelven en la vista de portal son un subconjunto de los componentes de red que se representan. Para ver la lista completa de los recursos de red, puede usar [PowerShell](network-watcher-topology-powershell.md) o [REST](network-watcher-topology-rest.md).

> [!NOTE]
> Se requiere una instancia de Network Watcher en cada región en que desea ejecutar la topología.

Mientras los recursos devuelven la conexión entre ellos, se modelan con dos relaciones.

- **Contención**: por ejemplo, la red virtual contiene un subconjunto que incluye una NIC.
- **Asociación**: por ejemplo, una NIC está asociada a una máquina virtual.

### <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo usar PowerShell para recuperar la vista de topología, visite [Visualización de la topología de Network Watcher con PowerShell](network-watcher-topology-powershell.md).

<!--Image references-->

[1]: ./media/network-watcher-topology-overview/topology.png
