---
title: "Mantenimiento de conservación de máquinas virtuales Windows en Azure | Microsoft Docs"
description: "Migración local de máquinas virtuales para la realización de actualizaciones con conservación de memoria."
services: virtual-machines-windows
documentationcenter: 
author: 
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: 
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 09fc9021e8dfb910d1a81178434ca2e27c0bacf7
ms.contentlocale: es-es
ms.lasthandoff: 05/23/2017


---



# <a name="vm-preserving-maintenance-in-place-vm-migration"></a>Mantenimiento de conservación de máquinas virtuales (migración local de máquinas virtuales)

Aunque la mayoría de las actualizaciones no tienen ningún impacto en las máquinas virtuales hospedadas, hay casos en los que la actualización de componentes o servicios producen interferencias mínimas en las máquinas virtuales que están en ejecución (sin un reinicio completo de ellas).

Estas actualizaciones se realizan con la tecnología que permite la migración local en vivo, también llamada "actualización con conservación de memoria". Al actualizar el host, la máquina virtual se pone en un estado de "en pausa" y conserva la memoria RAM, mientras el entorno de hospedaje (por ejemplo, el sistema operativo subyacente) aplica las actualizaciones y revisiones necesarias.
Tras ello, la máquina virtual se reanuda en un máximo de 30 segundos después de haberse puesto en pausa.
Una vez reanudada, se sincronizará automáticamente el reloj de la máquina virtual.

No todas las actualizaciones pueden implementarse con este mecanismo, pero gracias a su breve período de pausa, implementar las actualizaciones de este modo reduce en gran medida el impacto en las máquinas virtuales.

Las actualizaciones de varias instancias (máquinas virtuales en un conjunto de disponibilidad) se aplican a un dominio de actualización a la vez.

Algunas aplicaciones pueden resultar afectadas por estas actualizaciones más que otras. Por ejemplo, las aplicaciones que realizan procesamiento de eventos en tiempo real, transcodificación y streaming multimedia o escenarios de redes de alto rendimiento pueden no estar diseñadas para tolerar una pausa de 30 segundos. Las aplicaciones que se ejecutan en una máquina virtual pueden conocer las próximas actualizaciones mediante una llamada a la API de [eventos programados](../virtual-machines-scheduled-events.md) de [Azure Metadata Service](../virtual-machines-instancemetadataservice-overview.md).
