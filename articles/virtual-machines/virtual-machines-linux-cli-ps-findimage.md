---
title: "Selección de imágenes de máquina virtuale Linux con la CLI de Azure | Microsoft Docs"
description: "Infórmese sobre cómo determinar el publicador, la oferta y la SKU de las imágenes al crear una máquina virtual Linux con el modelo de implementación de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 732767bcece6d892aee7a8e6976f527fc66bb48d


---
# <a name="select-linux-vm-images-with-the-azure-cli"></a>Selección de imágenes de máquinas virtuales con la CLI de Azure
En este tema se describe cómo buscar publicadores, ofertas, SKU y versiones de cada ubicación en la que puede implementar. Para dar un ejemplo, algunas imágenes de máquina virtual Linux más utilizadas son:

**Tabla de imágenes de Linux más usadas**

| PublisherName | Oferta | SKU |
|:--- |:--- |:--- |:--- |
| Redhat |RHEL |7,2 |
| credativ |Debian |8 |
| SUSE |openSUSE |13.2 |
| SUSE |SLES |12-SP1 |
| OpenLogic |CentOS |7.1 |
| Canonical |UbuntuServer |14.04.4-LTS |
| CoreOS |CoreOS |Stable |

[!INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]




<!--HONumber=Nov16_HO3-->


