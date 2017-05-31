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
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 28c2fb5a67eca0c5ab2f0299bba7c11375e10558
ms.contentlocale: es-es
ms.lasthandoff: 04/03/2017


---

# <a name="planned-maintenance-for-linux-virtual-machines"></a>Mantenimiento planeado de máquinas virtuales Linux 

Microsoft Azure realiza periódicamente actualizaciones en todo el planeta para mejorar la fiabilidad, el rendimiento y la seguridad de infraestructuras host que subyacen bajo las máquinas virtuales. Tales actualizaciones van desde la aplicación de revisiones a componentes de software del entorno de hospedaje (sistema operativo, hipervisor y varios agentes implementados en el host) hasta la retirada de hardware, pasando por la actualización de los componentes de red.

La mayoría de estas actualizaciones se realizan sin que ello afecte a las máquinas virtuales ni a los servicios en la nube hospedados.

Sin embargo, hay casos en los que afectan a las máquinas virtuales hospedadas:

-   El mantenimiento de conservación de las máquinas virtuales mediante la migración de máquinas virtuales locales describe una clase de actualizaciones en las que aquellas no se reinician.

-   El mantenimiento de reinicio de las máquinas virtuales que requiere un reinicio o una reimplementación de las máquinas virtuales hospedadas.

Tenga en cuenta que en esta página se describe cómo realiza Microsoft Azure el mantenimiento planeado. Para más información sobre eventos no planeados (interrupciones), consulte [Administración de la disponibilidad de las máquinas virtuales](../windows/manage-availability.md).
