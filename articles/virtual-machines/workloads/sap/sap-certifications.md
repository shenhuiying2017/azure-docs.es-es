---
title: Certificaciones de Microsoft Azure para SAP | Microsoft Docs
description: Lista actualizada de las configuraciones y certificaciones actuales de SAP en la plataforma Azure.
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2017
ms.author: rclaus
ms.custom: 
ms.openlocfilehash: 865fa54c908481b3f4c211f12293538c617b6129
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2017
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Configuraciones y certificaciones de SAP que se ejecutan en Microsoft Azure

SAP y Microsoft tienen una larga historia de trabajo conjunto en una asociación segura que tiene ventajas mutuas para sus clientes. Microsoft actualiza constantemente su plataforma y envía nuevos detalles de certificación a SAP, con el fin de asegurarse de que Microsoft Azure es la mejor plataforma para ejecutar las cargas de trabajo de SAP. Las tablas siguientes describen nuestras configuraciones admitidas y una lista de certificaciones en crecimiento. 

## <a name="sap-hana-certifications"></a>Certificaciones SAP HANA
Referencias:

- [SAP Note 2316233 - SAP HANA on Microsoft Azure (Large Instances)](https://launchpad.support.sap.com/#/notes/2316233) (Nota de SAP 2316233: SAP HANA en Microsoft Azure (instancias grandes), que trata las instancias grandes de HANA en relación con el soporte técnico de SAP HANA.
- [SAP HANA certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Amazon%20Web%20Services%2CMicrosoft%20Azure) (Plataformas IaaS con certificación SAP HANA) para soporte técnico para máquinas virtuales de Azure nativas.

| Producto de SAP | SO admitido | Productos de Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (incluido el software cliente de HANA compuesto por los controladores SQLODBC, ODBO solo para Windows, ODBC y JDBC, HANA Studio y la base de datos HANA) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Familia de máquina virtual de la serie D |
| Business One on HANA | SUSE Linux Enterprise | DS14_v2 |
| SAP S/4 HANA |Red Hat Enterprise Linux, SUSE Linux Enterprise | Disponibilidad controlada para GS5, SAP Hana en Azure (instancias grandes) |
| Suite en Hana, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 para implementaciones de nodo único para escenarios que no sean de producción, SAP HANA en Azure (instancias grandes) |
| Hana Enterprise para BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 para implementaciones de nodo único, SAP HANA en Azure (instancias grandes) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 para implementaciones de nodo único, SAP HANA en Azure (instancias grandes) |

## <a name="sap-netweaver-certifications"></a>Certificaciones de SAP NetWeaver
Microsoft Azure cuenta con certificación para los siguientes productos de SAP, con soporte técnico completo de Microsoft y SAP.
Referencias:

- [1928533: SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (Aplicaciones de SAP en Azure: tipos de máquina virtual de Azure y productos compatibles) para todas las aplicaciones basadas en SAP NetWeaver, incluidas SAP TREX, SAP LiveCache y SAP Content Server. Y todas las bases de datos, excluida SAP HANA.


| Producto de SAP | SO invitado | RDBMS | Tipos de máquinas virtuales |
| --- | --- | --- | --- |
| Software SAP Business Suite |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (solo Windows y Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5, serie M |
| SAP Business All-in-One |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle (solo Windows y Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5, serie M |
| SAP BusinessObjects BI |Windows |N/D |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5, serie M |
| SAP NetWeaver |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle (solo Windows y Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5, serie M |

## <a name="other-sap-workload-supported-on-azure"></a>Otras cargas de trabajo de SAP admitidas en Azure

| Producto de SAP | SO invitado | RDBMS | Tipos de máquinas virtuales |
| --- | --- | --- | --- |
| SAP Business One on SQL Server | Windows  | SQL Server | Todos los tipos de máquina virtual con certificado NetWeaver |
| SAP BPC 10.01 MS SP08 | Windows | | Todos los tipos de máquina virtual con certificado NetWeaver<br /> Nota de SAP 2451795 |
| SAP Business Objects BI platform | Windows | | Nota de SAP 2145537 |
| SAP Data Services 4.2 | | | Nota de SAP 2288344 |
| Plataforma de comercio de Hybris SAP 5.x y 6.x | Windows | SQL Server, Oracle | Todos los tipos de máquina virtual con certificado NetWeaver<br /> [Wiki de Hybris](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
