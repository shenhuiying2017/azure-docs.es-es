---
title: "Mantenimiento planeado de máquinas virtuales Linux en Azure | Microsoft Docs"
description: "Sepa qué es el mantenimiento planeado de Azure y cómo afecta a sus máquinas virtuales Windows que se ejecutan en Azure."
services: virtual-machines-linux
documentationcenter: 
author: zivr
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: f5e52f4642b85a75f0c9f1892fbf0e565aa8b35b
ms.contentlocale: es-es
ms.lasthandoff: 06/03/2017


---

# <a name="planned-maintenance-for-linux-virtual-machines"></a>Mantenimiento planeado de máquinas virtuales Linux 

Microsoft Azure realiza periódicamente actualizaciones en todo el planeta para mejorar la fiabilidad, el rendimiento y la seguridad de infraestructuras host que subyacen bajo las máquinas virtuales. Tales actualizaciones van desde la aplicación de revisiones a componentes de software del entorno de hospedaje (sistema operativo, hipervisor y varios agentes implementados en el host) hasta la retirada de hardware, pasando por la actualización de los componentes de red.

La mayoría de estas actualizaciones se realizan sin que ello afecte a las máquinas virtuales ni a los servicios en la nube hospedados.

Sin embargo, hay casos en los que afectan a las máquinas virtuales hospedadas:

-   El mantenimiento de conservación de las máquinas virtuales mediante la migración local de máquinas virtuales describe una clase de actualizaciones en las que aquellas no se reinician.

-   El mantenimiento de reinicio de las máquinas virtuales que requiere un reinicio o una reimplementación de las máquinas virtuales hospedadas.

Tenga en cuenta que en esta página se describe cómo realiza Microsoft Azure el mantenimiento planeado. Para más información sobre eventos no planeados (interrupciones), consulte [Administración de la disponibilidad de las máquinas virtuales](../linux/manage-availability.md).

