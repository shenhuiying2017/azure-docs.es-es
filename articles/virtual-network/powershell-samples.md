---
title: Ejemplos de Azure PowerShell para red virtual | Microsoft Docs
description: Ejemplos de Azure PowerShell para red virtual
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: ab3a0935f23cda70cfef49af3563f6b1e85d1d8b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841223"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Ejemplos de Azure PowerShell para red virtual

En la tabla siguiente se incluyen vínculos a scripts de Azure PowerShell:

| | |
|----|----|
| [Creación de una red virtual para aplicaciones de niveles múltiples](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Creación de una red virtual con subredes de front-end y back-end. El tráfico a la subred de front-end está limitado a HTTP, mientras que el tráfico a la subred de back-end está limitado a SQL, al puerto 1433. |
| [Emparejamiento de dos redes virtuales](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Creación y conexión de dos redes virtuales en la misma región. |
| [Enrutamiento del tráfico por una aplicación virtual de red](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Creación de una red virtual con subredes de front-end y back-end y una máquina virtual que pueda enrutar el tráfico entre dos subredes. |
| [Filtrado del tráfico de red entrante y saliente de máquina virtual](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Creación de una red virtual con subredes de front-end y back-end. El tráfico de red entrante a la subred de front-end está limitado a HTTP y HTTPS. No se permite el tráfico saliente a Internet desde la subred de back-end. |
