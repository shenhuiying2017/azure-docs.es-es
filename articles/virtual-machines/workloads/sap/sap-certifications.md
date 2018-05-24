---
title: Certificaciones de Microsoft Azure para SAP | Microsoft Docs
description: Lista actualizada de las configuraciones y certificaciones actuales de SAP en la plataforma Azure.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: 1038f0c8a967c05ebf07849619268f22b1eb0c4b
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34160910"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Configuraciones y certificaciones de SAP que se ejecutan en Microsoft Azure

SAP y Microsoft tienen una larga historia de trabajo conjunto en una asociación segura que tiene ventajas mutuas para sus clientes. Microsoft actualiza constantemente su plataforma y envía nuevos detalles de certificación a SAP, con el fin de asegurarse de que Microsoft Azure es la mejor plataforma para ejecutar las cargas de trabajo de SAP. Las tablas siguientes describen nuestras configuraciones admitidas de Azure y una lista de certificaciones SAP en crecimiento. 

## <a name="sap-hana-certifications"></a>Certificaciones SAP HANA
Referencias:

- [SAP HANA certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) (Plataformas IaaS con certificación SAP HANA) para soporte técnico para máquinas virtuales de Azure nativas e instancias grandes de HANA.

| Producto de SAP | SO admitido | Productos de Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (incluido el software cliente de HANA compuesto por los controladores SQLODBC, ODBO solo para Windows, ODBC y JDBC, HANA Studio y la base de datos HANA) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Familia de máquina virtual de la serie D |
| Business One on HANA | SUSE Linux Enterprise | DS14_v2 |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Disponibilidad controlada para GS5, M64s, M64ms, M128s, M128ms, SAP Hana en Azure (instancias grandes) |
| Suite en Hana, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 para escenarios que no son de producción, M64s, M64ms, M128s, M128ms, SAP HANA en Azure (grandes instancias) |
| Hana Enterprise para BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, SAP HANA en Azure (grandes instancias) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, SAP HANA en Azure (grandes instancias) |

Todas las máquinas virtuales de Azure están certificadas para el escalado vertical de SAP HANA hasta ahora.

## <a name="sap-netweaver-certifications"></a>Certificaciones de SAP NetWeaver
Microsoft Azure cuenta con certificación para los siguientes productos de SAP, con soporte técnico completo de Microsoft y SAP.
Referencias:

- [1928533: SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (Aplicaciones de SAP en Azure: tipos de máquina virtual de Azure y productos compatibles) para todas las aplicaciones basadas en SAP NetWeaver, incluidas SAP TREX, SAP LiveCache y SAP Content Server. Y todas las bases de datos, excluida SAP HANA.


| Producto de SAP | SO invitado | RDBMS | Tipos de máquinas virtuales |
| --- | --- | --- | --- |
| Software SAP Business Suite | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (solo Windows y Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5, D2s_v3 a D64s_v3, E2s_v3 a E64s_v3 y M64s a M128ms |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (solo Windows y Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5, D2s_v3 a D64s_v3, E2s_v3 a E64s_v3 y M64s a M128ms |
| SAP BusinessObjects BI | Windows |N/D |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5, D2s_v3 a D64s_v3, E2s_v3 a E64s_v3 y M64s a M128ms |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (solo Windows y Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5, D2s_v3 a D64s_v3, E2s_v3 a E64s_v3 y M64s a M128ms |

## <a name="other-sap-workload-supported-on-azure"></a>Otras cargas de trabajo de SAP admitidas en Azure

| Producto de SAP | SO invitado | RDBMS | Tipos de máquinas virtuales |
| --- | --- | --- | --- |
| SAP Business One on SQL Server | Windows  | SQL Server | Todos los tipos de máquina virtual con certificado NetWeaver |
| SAP BPC 10.01 MS SP08 | Windows y Linux | | Todos los tipos de máquina virtual con certificado NetWeaver<br /> Nota de SAP 2451795 |
| SAP Business Objects BI platform | Windows y Linux | | Nota de SAP 2145537 |
| SAP Data Services 4.2 | | | Nota de SAP 2288344 |
| Plataforma de comercio de Hybris SAP 5.x y 6.x | Windows | SQL Server, Oracle | Todos los tipos de máquina virtual con certificado NetWeaver<br /> [Wiki de Hybris](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
