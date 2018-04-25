---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 5fc9412713bc57c3fd7753e133b2587ea7a68938
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>Escenario
Este documento le guiará a través de una implementación que usa varias NIC en máquinas virtuales en un escenario concreto. En este escenario, tiene una carga de trabajo de IaaS de dos niveles hospedada en Azure. Cada nivel se implementa en su propia subred de una red virtual (VNet). El nivel de front-end se compone de varios servidores web, agrupados en un equilibrador de carga de alta disponibilidad. El nivel de back-end se compone de varios servidores de bases de datos. Estos servidores de bases de datos se implementarán con dos NIC cada uno: una para el acceso a la base de datos y la otra para la administración. El escenario también incluye los grupos de seguridad de red (NSGs) para controlar qué tráfico se permite en cada subred y la NIC de la implementación. La siguiente ilustración muestra la arquitectura básica de este escenario:  

![Escenario de MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

