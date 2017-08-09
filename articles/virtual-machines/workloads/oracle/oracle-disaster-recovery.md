---
title: "Introducción a un escenario de recuperación ante desastres de Oracle en el entorno de Azure | Microsoft Docs"
description: "Un escenario de recuperación ante desastres de Oracle Database 12c en el entorno de Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/2/2017
ms.author: rclaus
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4f6fc6ed79606f0510bc3d6258205d1e6105de25
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="disaster-recovery-dr-of-oracle-12c-database-on-an-azure-environment"></a>Escenario de recuperación ante desastres (DR) de Oracle Database 12c en el entorno de Azure

## <a name="assumptions"></a>Supuestos

- Comprensión del diseño de Oracle Data Guard y el entorno de Azure


## <a name="goals"></a>Objetivos
- Diseñar la topología y configuración que satisfacen las necesidades de recuperación ante desastres.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Escenario 1 (sitios principal y de recuperación ante desastres en Azure)

Un cliente tiene una instalación de base de datos de Oracle en el sitio principal. El sitio de recuperación ante desastres (DR) se encuentra en una región diferente. Oracle Data Guard se usa para la recuperación rápida entre estos sitios. El sitio principal tiene también una base de datos secundaria para informes y otros usos. 

### <a name="topology"></a>Topología

A continuación un resumen de la configuración de Azure.

- Dos sitios ( principal y de recuperación ante desastres)
- Dos redes virtuales (VNet)
- Dos bases de datos de Oracle con Data Guard (principal y en espera)
- Dos bases de datos de Oracle con Golden Gate o Data Guard (solo para el sitio principal)
- Dos servicios de aplicación en el sitio principal y uno en el sitio de recuperación ante desastres
- Conjuntos de disponibilidad que se usan para la base de datos y el servicio de aplicación en el sitio principal
- Un Jumpbox en cada sitio, que restringe el acceso a la red privada, solo permite el inicio de sesión al administrador
- Jumpbox, servicio de aplicación, base de datos y puerta de enlace VPN se encuentran en subredes independientes
- El grupo de seguridad de red se aplica en las subredes de la aplicación y de base de datos

![Captura de pantalla de la página de la topología de recuperación ante desastres](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Escenario 2 (sitio principal en ubicación local y sitio de recuperación ante desastres en Azure)

Un cliente tiene una instalación de base de datos de Oracle en un sitio local (sitio principal). Un sitio de recuperación ante desastres en Azure. Oracle Data Guard se usa para la recuperación rápida entre estos sitios. El sitio principal tiene también una base de datos secundaria para informes y otros usos. 

Existen dos enfoques para esta instalación.

### <a name="1-direct-connections-between-on-premises-and-azure-required-open-tcp-ports-on-firewall-this-approach-is-not-recommended-as-it-expose-the-tcp-ports-to-outside-world"></a>1. Conexiones directas entre el sitio local y Azure, lo que requiere los puertos TCP abiertos en el firewall. No se recomienda este enfoque ya que expone los puertos TCP al exterior.

### <a name="topology"></a>Topología

A continuación un resumen de la configuración de Azure.

- Un sitio (sitio de recuperación ante desastres)
- Una red virtual (VNet)
- Una base de datos de Oracle con Data Guard (activo)
- Un servicio de aplicación en el sitio de recuperación ante desastres
- Se usa un Jumpbox que restringe el acceso a la red privada y solo permite el inicio de sesión al administrador
- Jumpbox, servicio de aplicación, base de datos y puerta de enlace VPN se encuentran en subredes independientes
- El grupo de seguridad de red se aplica en las subredes de la aplicación y de base de datos
- Agregar directiva o regla de NSG para permitir el tráfico entrante en el puerto TCP 1521 (o el definido por el usuario)
- Agregar directiva o regla NSG para restringir solo la dirección o direcciones de IP local (base de datos o aplicación) para tener acceso a la red virtual.

![Captura de pantalla de la página de la topología de recuperación ante desastres](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="2-a-better-approach-is-using-site-to-site-vpn-more-information-about-setting-up-vpn-is-available-from-this-linkhttpsdocsmicrosoftcomen-usazurevpn-gatewayvpn-gateway-howto-site-to-site-resource-manager-cli"></a>2. Un mejor enfoque consiste en el uso de VPN de sitio a sitio. Hay disponible más información sobre la configuración de VPN en este [vínculo](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)

### <a name="topology"></a>Topología

A continuación un resumen de la configuración de Azure.

- Un sitio (sitio de recuperación ante desastres)
- Una red virtual (VNet)
- Una base de datos de Oracle con Data Guard (activo)
- Un servicio de aplicación en el sitio de recuperación ante desastres
- Se usa un Jumpbox que restringe el acceso a la red privada y solo permite el inicio de sesión al administrador
- Jumpbox, servicio de aplicación, base de datos y puerta de enlace VPN se encuentran en subredes independientes
- El grupo de seguridad de red se aplica en las subredes de la aplicación y de base de datos
- Conexión VPN de sitio a sitio entre sistemas locales y Azure

![Captura de pantalla de la página de la topología de recuperación ante desastres](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-readings"></a>Lecturas adicionales:

- [Diseñar e implementar una base de datos de Oracle en Azure](oracle-design.md)
- [Configuración de Oracle Data Guard](configure-oracle-dataguard.md)
- [Configuración de Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Copia de seguridad y recuperación de Oracle](oracle-backup-recovery.md)


## <a name="next-steps"></a>Pasos siguientes

[Tutorial: Creación de máquinas virtuales de alta disponibilidad](../../linux/create-cli-complete.md)

[Ejemplos de la CLI de Azure para implementación de máquinas virtuales](../../linux/cli-samples.md)

