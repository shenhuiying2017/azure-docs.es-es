---
title: Ejemplos de la CLI de Azure 2.0 | Microsoft Docs
description: Ejemplos de la CLI de Azure 2.0
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 69fc81d4d0569ee7a66fbda5ab500ef2ee15c694
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246396"
---
# <a name="azure-cli-20-samples-for-virtual-machine-scale-sets"></a>Ejemplos de la CLI de Azure 2.0 para conjuntos de escalado de máquinas virtuales

En la tabla siguiente se incluyen vínculos a scripts de Bash creados con la CLI de Azure 2.0.

| | |
|---|---|
|**Creación y administración de un conjunto de escalado**||
| [Creación de un conjunto de escalado de máquinas virtuales](scripts/cli-sample-create-simple-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un conjunto de escalado de máquinas virtuales Windows con una configuración mínima. |
| [Creación de un conjunto de escalado a partir de una imagen de máquina virtual personalizada](scripts/cli-sample-create-scale-set-from-custom-image.md?toc=%2fcli%2fmodule%2ftoc.json) | Crea un conjunto de escalado de máquinas virtuales que usa una imagen de máquina virtual personalizada. |
| [Instalación de aplicaciones en un conjunto de escalado](scripts/cli-sample-install-apps.md?toc=%2fcli%2fmodule%2ftoc.json) | Utilice la extensión de script personalizado de Azure para instalar una aplicación web básica en un conjunto de escalado. |
|**Administrar el almacenamiento**||
| [Creación y conexión de discos a un conjunto de escalado](scripts/cli-sample-attach-disks.md?toc=%2fcli%2fmodule%2ftoc.json) | Crea un conjunto de escalado de máquinas virtuales con discos de datos conectados. |
|**Administración de la escala y la redundancia**||
| [Habilitación del escalado automático basado en host](scripts/cli-sample-enable-autoscale.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un conjunto de escalado de máquinas virtuales configurado para escalar automáticamente según el uso de la CPU. |
| [Creación de un conjunto de escalado de zona única](scripts/cli-sample-single-availability-zone-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un conjunto de escalado de máquinas virtuales que usa una sola zona de disponibilidad. |
| [Creación de un conjunto de escalado con redundancia de zona](scripts/cli-sample-zone-redundant-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un conjunto de escalado de máquinas virtuales en varias zonas de disponibilidad. |
| | |