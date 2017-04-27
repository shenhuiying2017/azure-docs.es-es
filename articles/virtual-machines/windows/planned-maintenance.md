---
title: "Mantenimiento planeado de máquinas virtuales Windows en Azure | Microsoft Docs"
description: "Entienda en qué consiste el mantenimiento planeado de Azure y cómo afecta a sus máquinas virtuales Windows que se ejecutan en Azure."
services: virtual-machines-windows
documentationcenter: 
author: 
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 198c1804b342b8faf406a08eff7bc42994b9dd0d
ms.lasthandoff: 03/31/2017


---
# <a name="planned-maintenance-for-windows-virtual-machines"></a>Mantenimiento planeado de máquinas virtuales Windows 

Microsoft Azure realiza periódicamente actualizaciones en todo el planeta para mejorar la fiabilidad, el rendimiento y la seguridad de infraestructuras host que subyacen bajo las máquinas virtuales. Tales actualizaciones van desde la aplicación de revisiones de componentes de software en el entorno de hospedaje (sistema operativo, hipervisor y varios agentes implementados en el host) hasta la retirada de hardware, pasando por la actualización de los componentes de red.

La mayoría de estas actualizaciones se realizan sin afectar a las máquinas virtuales ni a los servicios en la nube hospedados.

Sin embargo, hay casos en los que las actualizaciones sí tienen un impacto en las máquinas virtuales hospedadas:

-   El mantenimiento de conservación de las máquinas virtuales mediante la migración local de máquinas virtuales describe una clase de actualizaciones en las que aquellas no se reinician.

-   El mantenimiento de reinicio de las máquinas virtuales que requiere un reinicio o una reimplementación de las máquinas virtuales hospedadas.

Tenga en cuenta que en esta página se describe cómo Microsoft Azure realiza el mantenimiento planeado. Para obtener más información sobre eventos no planeados (interrupciones), consulte [Administración de la disponibilidad de las máquinas virtuales](manage-availability.md).
