---
title: "Uso de SAP en máquinas virtuales Windows | Microsoft Docs"
description: "Obtenga información sobre cómo usar SAP en máquinas virtuales (VM) de Windows en Microsoft Azure"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: MSSedusch
manager: timlt
editor: 
tags: azure-service-management
keywords: 
ms.assetid: 1b455be4-c02f-43e3-8d39-c2d5f216e646
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: campaign-page
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/04/2016
ms.author: sedusch
ms.openlocfilehash: 1d6326d5f79ea4e975abd1aa90b0d4a635f4c31a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="using-sap-on-windows-virtual-machines-in-azure"></a>Uso de SAP en máquinas virtuales Windows en Azure
La informática en la nube es un término ampliamente usado que está cobrando cada vez más importancia en la industria de TI, desde pequeñas empresas hasta las grandes corporaciones y multinacionales. Microsoft Azure es la plataforma de servicios en la nube de Microsoft que ofrece una amplia gama de nuevas posibilidades. Ahora los clientes pueden aprovisionar y desaprovisionar rápidamente aplicaciones como servicios en la nube, por lo que no se verán limitados por restricciones técnicas o presupuestarias. En lugar de invertir tiempo y presupuesto en infraestructura de hardware, las empresas pueden centrarse en la aplicación, en los procesos empresariales y en sus ventajas para clientes y usuarios.

Con las máquinas virtuales de Microsoft Azure, Microsoft ofrece una completa plataforma de infraestructura como servicio (IaaS). Las aplicaciones de basadas en SAP NetWeaver son compatibles en las máquinas virtuales de Azure (IaaS). Las notas del producto siguientes describen cómo planear e implementar aplicaciones basadas en SAP NetWeaver en máquinas virtuales Windows en Azure. También puede implementar aplicaciones basadas en SAP NetWeaver en [máquinas virtuales Linux](../../linux/classic/sap-get-started.md).

[!INCLUDE [virtual-machines-common-classic-sap-get-started](../../../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure---ha"></a>SAP NetWeaver en Azure - Alta disponibilidad
Título: SAP NetWeaver en Azure - Instancias de SAP ASCS/SCS de agrupación de clústeres mediante el clúster de conmutación por error de Windows Server en Azure con SIOS DataKeeper

Resumen: En este documento se describe cómo usar SIOS DataKeeper para establecer una configuración de SAP ASCS/SCS de alta disponibilidad en Azure. SAP protege su punto único de componentes de error como SAP ASCS/SCS o Enqueue Replication Services con las configuraciones de clúster de conmutación por error de Windows Server que requieren los discos compartidos. Estos componentes SAP son esenciales para la funcionalidad de un sistema SAP. Por lo tanto, es necesario implementar funcionalidad de alta disponibilidad para asegurarse de que dichos componentes pueden soportar un error de un servidor o una máquina virtual tal como se hizo con las configuraciones de Windows Cluster para entornos Hyper-V y de reconstrucción completa. Desde agosto de 2015, Azure en sí mismo no puede proporcionar los discos compartidos que serían necesarios para las configuraciones de alta disponibilidad basadas en Windows necesarias para estos componentes críticos de SAP. Sin embargo con la ayuda del producto DataKeeper de SIOS, las configuraciones de clúster de conmutación por error de Windows Server que sean necesarias para SAP ASCS/SCS pueden crearse en la plataforma de IaaS de Azure. En este documento se describe paso a paso cómo instalar una configuración de clúster de conmutación por error de Windows Server con el disco compartido que SIOS Datakeeper proporciona en Azure. En el documento se explican detalles de las configuraciones del lado de Azure, Windows y SAP que hacen que la configuración de alta disponibilidad funcione de manera óptima. El documento complementa la Documentación de instalación de SAP y las Notas de SAP, que representan los principales recursos para las instalaciones e implementaciones de software de SAP en las plataformas proporcionadas.

Actualizado: agosto de 2015

[Descargar esta guía ahora](http://go.microsoft.com/fwlink/?LinkId=613056)

