<properties
   pageTitle="Navegación y selección de imágenes de máquina virtual Linux | Microsoft Azure"
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
   ms.date="03/14/2016"
   ms.author="rasquill"/>

# Navegación y selección de las imágenes de máquina virtual Linux en Azure con CLI o Powershell

Este tema describe cómo buscar publicadores, ofertas, SKU y versiones de cada ubicación en la que puede implementar. Para dar un ejemplo, algunas imágenes de máquina virtual Linux más utilizadas son:

**Tabla de imágenes de Linux más usadas**


| PublisherName | Oferta | SKU |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 |
| OpenLogic | CentOS | 7\.1 |
| Canonical | UbuntuServer | 12\.04.5-LTS |
| Canonical | UbuntuServer | 14\.04.2-LTS |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0330_2016-->