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
ms.date: 03/29/2017
ms.author: rclaus
ms.custom: 
ms.openlocfilehash: e4d5c78299903659a18aa9b8d04495e215bcca0d
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Configuraciones y certificaciones de SAP que se ejecutan en Microsoft Azure

SAP y Microsoft tienen una larga historia de trabajo conjunto en una asociación segura que tiene ventajas mutuas para sus clientes. Microsoft actualiza constantemente su plataforma y envía nuevos detalles de certificación a SAP, con el fin de asegurarse de que Microsoft Azure es la mejor plataforma para ejecutar las cargas de trabajo de SAP. Las tablas siguientes describen nuestras configuraciones admitidas y una lista de certificaciones en crecimiento. 

## <a name="sap-hana-certifications"></a>Certificaciones SAP HANA

| Producto de SAP | SO admitido | Productos de Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (incluido el software cliente de HANA compuesto por los controladores SQLODBC, ODBO solo para Windows, ODBC y JDBC, HANA Studio y la base de datos HANA) |Red Hat Enterprise Linux, SUSE Linux Enterprise | Familia de máquina virtual de la serie D |
| HANA One |Red Hat Enterprise Linux, SUSE Linux Enterprise |DS14_v2 (con disponibilidad general) |
| SAP S/4 HANA |Red Hat Enterprise Linux, SUSE Linux Enterprise |Disponibilidad controlada para GS5, SAP Hana en Azure (instancias grandes) |
| Suite en Hana, OLTP |Red Hat Enterprise Linux, SUSE Linux Enterprise |GS5 para implementaciones de nodo único para escenarios que no sean de producción, SAP HANA en Azure (instancias grandes) |
| Hana Enterprise para BW, OLAP |Red Hat Enterprise Linux, SUSE Linux Enterprise |GS5 para implementaciones de nodo único, SAP HANA en Azure (instancias grandes) |
| SAP BW/4 HANA |Red Hat Enterprise Linux, SUSE Linux Enterprise |GS5 para implementaciones de nodo único, SAP HANA en Azure (instancias grandes) |

## <a name="sap-netweaver-certifications"></a>Certificaciones de SAP NetWeaver
Microsoft Azure cuenta con certificación para los siguientes productos de SAP, con soporte técnico completo de Microsoft y SAP.

| Producto de SAP | SO invitado | RDBMS | Tipos de máquinas virtuales |
| --- | --- | --- | --- |
| Software SAP Business Suite |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (solo Windows y Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5 |
| SAP Business All-in-One |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle (solo Windows y Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5 |
| SAP BusinessObjects BI |Windows |N/D |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5 |
| SAP NetWeaver |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle (solo Windows y Oracle Linux), DB2, SAP ASE |A5 a A11, D11 a D14, DS11 a DS14, DS11_v2 a DS15_v2, GS1 a GS5 |
