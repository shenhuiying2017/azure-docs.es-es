---
title: 'Disponibilidad de SAP HANA en máquinas virtuales de Azure: Introducción | Microsoft Docs'
description: Operaciones de SAP HANA en máquinas virtuales nativas de Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9b22f89750234a4715d2b7fd2df6ad8740b1d085
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-high-availability-guide-for-azure-virtual-machines"></a>Alta disponibilidad de SAP HANA para máquinas virtuales de Azure
Azure proporciona numerosas funcionalidades que permiten implementar bases de datos críticas como SAP HANA en máquinas virtuales de Azure. Este documento proporciona una guía sobre cómo lograr disponibilidad para instancias de SAP HANA que se hospedan en máquinas virtuales de Azure. En el documento, se describen varios escenarios que se pueden implementar en la infraestructura de Azure para aumentar la disponibilidad de SAP HANA en Azure. 

## <a name="prerequisites"></a>requisitos previos
En esta guía se da por supuesto que está familiarizado con los conceptos básicos de la infraestructura como servicio (IaaS) en Azure, por ejemplo: 

- Cómo implementar máquinas virtuales o redes virtuales mediante Azure Portal o PowerShell.
- La interfaz de línea de comandos (CLI) multiplataforma de Azure, incluida la opción para usar plantillas de notificación de objetos JavaScript (JSON).

También se da por supuesto que está familiarizado con la instalación de instancias de SAP HANA y así como con su administración y funcionamiento. Especialmente con la configuración y operaciones alrededor de la replicación del sistema de HANA o con tareas como copia de seguridad y restauración de las bases de datos de SAP HANA.

Otros artículos que proporcionan una buena introducción a los temas de SAP HANA en Azure son:

- [Inicio rápido: instalación manual de una única instancia de SAP HANA en máquinas virtuales de Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Configuración de la replicación del sistema de SAP HANA en máquinas virtuales de Azure](sap-hana-high-availability.md)
- [Guía de copia de seguridad de SAP HANA en Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

A continuación le presentamos una lista de artículos y contenidos que debe conocer acerca de SAP HANA:

- [High Availability for SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)(Alta disponibilidad para SAP HANA)
- [FAQ: High Availability for SAP HANA](https://archive.sap.com/documents/docs/DOC-66702) (Preguntas más frecuentes: alta disponibilidad para SAP HANA)
- [How to Perform System Replication for SAP HANA](https://archive.sap.com/documents/docs/DOC-47702) (Cómo realizar la replicación del sistema de SAP HANA)
- [SAP HANA 2.0 SPS 01 What’s New: High Availability](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/) (Novedades de SAP HANA 2.0 SPS 01: alta disponibilidad)
- [Network Recommendations for SAP HANA System Replication](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html) (Recomendaciones de red para la replicación del sistema de SAP HANA)
- [SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) (Replicación del sistema de SAP HANA)
- [SAP HANA Service Auto-Restart](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) (Servicio de reinicio automático de SAP HANA)
- [Configuring SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html) (Configuración de la replicación del sistema de SAP HANA)


Más allá de estar familiarizado con la implementación de máquinas virtuales en Azure, también se recomienda leer el artículo [Administración de la disponibilidad de las máquinas virtuales Windows en Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) antes de continuar con la definición de la arquitectura de disponibilidad en Azure.

## <a name="service-level-agreements-for-different-azure-components"></a>Acuerdos de Nivel de Servicio para los distintos componentes de Azure
Azure tiene diferentes Acuerdos de Nivel de Servicio de disponibilidad para los distintos componentes como redes, almacenamiento y máquinas virtuales. Todos estos Acuerdos de Nivel de Servicio están documentados y pueden encontrarse partiendo de la página de [Acuerdos de Nivel de Servicio de Microsoft Azure](https://azure.microsoft.com/support/legal/sla/). Si comprueba el [Acuerdo de Nivel de Servicio para Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), se dará cuenta de que Azure proporciona dos Acuerdos de Nivel de Servicio diferentes con dos configuraciones distintas. Los Acuerdos de Nivel de Servicio se definen como:

- Una sola máquina virtual con [almacenamiento de Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) para todos los discos del sistema operativos y todos los discos de datos, proporciona un porcentaje de tiempo de actividad mensual del 99,9 %
- Varias máquinas virtuales (al menos dos) que se organizan en una [conjunto de disponibilidad de Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) proporcionan un porcentaje de tiempo de actividad mensual del 99,95 %

Mida sus necesidades de disponibilidad en relación a los componentes que los Acuerdos de Nivel de Servicio de Azure pueden proporcionar, y decida sobre los diferentes situaciones que tiene que implementar con SAP HANA para lograr la disponibilidad que tiene que proporcionar.

## <a name="next-steps"></a>Pasos siguientes
Continuar a los documentos:

- [Disponibilidad de SAP HANA dentro de una región de Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Disponibilidad de SAP HANA entre regiones de Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 















  


