---
title: Navegación y selección de imágenes de máquinas virtuales Windows | Microsoft Docs
description: Obtenga información sobre cómo determinar el publicador, la oferta y la SKU de las imágenes al crear una máquina virtual Windows con el modelo de implementación del Administrador de recursos.
services: virtual-machines-windows
documentationcenter: ''
author: squillace
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill

---
# Navegación y selección de las imágenes de máquina virtual Windows en Azure con Powershell o CLI
En este tema se describe cómo buscar publicadores de imágenes de máquina virtual, ofertas, SKU y versiones de cada ubicación en la que podría realizar una implementación. Para dar un ejemplo, algunas de las imágenes de máquina virtual Windows más utilizadas son:

## Tabla de imágenes de Windows más usadas
| PublisherName | Oferta | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftDynamicsNAV |DynamicsNAV |2015 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2013 |
| MicrosoftSQLServer |WS2012R2 SQL2014 |Enterprise-Optimized-for-DW |
| MicrosoftSQLServer |WS2012R2 SQL2014 |Enterprise-Optimized-for-OLTP |
| Microsoft Windows Server |Windows Server |Centro de datos de 2012-R2 |
| Microsoft Windows Server |Windows Server |Centro de datos de 2012 |
| Microsoft Windows Server |Windows Server |2008-R2-SP1 |
| Microsoft Windows Server |Windows Server |Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |

[!INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0824_2016-->