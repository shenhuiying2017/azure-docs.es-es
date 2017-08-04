---
title: "Compatibilidad a la hora de agregar máquinas virtuales de Azure a un conjunto de disponibilidad existente | Microsoft Docs"
description: "Compatibilidad a la hora de agregar máquinas virtuales de Azure a un conjunto de disponibilidad existente."
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/15/2017
ms.author: delhan
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: f07108f5d7a792dcc9aae8a4fe889878db168bc6
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Compatibilidad a la hora de agregar máquinas virtuales de Azure a un conjunto de disponibilidad existente

En ocasiones, puede encontrar limitaciones al agregar nuevas máquinas virtuales (VM) a un conjunto de disponibilidad existente. El siguiente gráfico detalla las series de máquinas virtuales que puede mezclar en el mismo conjunto de disponibilidad.

Esta es la matriz de compatibilidad para mezclar distintos tipos de máquinas virtuales:

Serie y conjunto de disponibilidad|Segunda máquina virtual|Una |Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Primera máquina virtual|||||||
|Una ||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Todas las otras series no pueden estar en el mismo conjunto de disponibilidad porque requieren un hardware específico.
