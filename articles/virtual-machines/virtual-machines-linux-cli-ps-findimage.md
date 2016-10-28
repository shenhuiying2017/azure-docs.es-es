<properties
   pageTitle="Selección de las imágenes de máquinas virtuales de Linux con la CLI de Azure | Microsoft Azure"
   description="Infórmese sobre cómo determinar el publicador, la oferta y la SKU de las imágenes al crear una máquina virtual Linux con el modelo de implementación de Resource Manager."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# Selección de imágenes de máquinas virtuales con la CLI de Azure

En este tema se describe cómo buscar publicadores, ofertas, SKU y versiones de cada ubicación en la que puede implementar. Para dar un ejemplo, algunas imágenes de máquina virtual Linux más utilizadas son:

**Tabla de imágenes de Linux más usadas**


| PublisherName | Oferta | SKU |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| Redhat | RHEL | 7,2 |
| credativ | Debian | 8 | 
| SUSE | openSUSE | 13\.2 |
| SUSE | SLES | 12-SP1 |
| OpenLogic | CentOS | 7\.1 |
| Canonical | UbuntuServer | 14\.04.4-LTS |
| CoreOS | CoreOS | Stable |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0824_2016-->