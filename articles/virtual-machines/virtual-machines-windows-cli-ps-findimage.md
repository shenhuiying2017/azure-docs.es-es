<properties
   pageTitle="Navegación y selección de imágenes de máquinas virtuales Windows | Microsoft Azure"
   description="Obtenga información sobre cómo determinar el publicador, la oferta y la SKU de las imágenes al crear una máquina virtual Windows con el modelo de implementación del Administrador de recursos."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="12/08/2015"
   ms.author="rasquill"/>

# Navegación y selección de las imágenes de máquina virtual Windows en Azure con Powershell o CLI

Este tema describe cómo buscar publicadores, ofertas, SKU y versiones de cada ubicación en la que puede implementar. Para dar un ejemplo, algunas de las imágenes de máquina virtual Windows más utilizadas son:

## Tabla de imágenes de Windows más usadas


| PublisherName | Oferta | SKU |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| CoreOS | CoreOS | Versión beta |
| CoreOS | CoreOS | Stable |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
| Microsoft | Oracle-Database-12c-Weblogic-Server-12c | Standard |
| Microsoft | Oracle-Database-12c-Weblogic-Server-12c | Enterprise |
| MicrosoftSQLServer | WS2012R2 SQL2014 | Enterprise-Optimized-for-DW |
| MicrosoftSQLServer | WS2012R2 SQL2014 | Enterprise-Optimized-for-OLTP |
| Microsoft Windows Server | Windows Server | Centro de datos de 2012 |
| Microsoft Windows Server | Windows Server | Centro de datos de 2012-R2 |
| Microsoft Windows Server | Windows Server | 2008-R2-SP1 |
| Microsoft Windows Server | Windows Server | Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0601_2016-->