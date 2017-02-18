---
title: "Obtención de un identificador de máquina virtual Linux de Azure | Microsoft Docs"
description: "Describe cómo obtener y usar un identificador único de máquina virtual Linux de Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: kmouss
manager: timlt
editor: 
ms.assetid: 136c5d28-ff6b-4466-b27f-7a29785b5d27
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/23/2017
ms.author: kmouss
translationtype: Human Translation
ms.sourcegitcommit: 167863b0de66d89f1cec80b1c6b2aec64f48b790
ms.openlocfilehash: 70d1d3ee35c32bb95306c7ce6089574d035de6a6


---
# <a name="accessing-and-using-azure-vm-unique-id"></a>Acceso y uso del identificador único de máquina virtual de Azure
El identificador único de máquina virtual de Azure es un identificador de 128 bits codificado y almacenado en todas las SMBIOS de la máquina virtual IaaS de Azure; actualmente se puede leer mediante comandos BIOS de la plataforma.

El identificador único de máquina virtual de Azure es una propiedad de solo lectura. El identificador único de máquina virtual de Azure no cambiará durante el cierre de reinicio (tanto si se ha planeado como si no), durante el inicio o la detención de la desasignación, durante la recuperación del servicio o la restauración local. Sin embargo, si la máquina virtual es una instantánea y se copia para crear una instancia, se configurará un nuevo identificador de máquina virtual de Azure.

> [!NOTE]
> Si dispone de máquinas virtuales que se ejecutan desde que se implantó esta nueva característica (18 de septiembre de 2014), reinícielas para obtener automáticamente un identificador único de Azure.
> 
> 

Para obtener acceso al identificador único de la máquina virtual de Azure desde dentro de la máquina virtual:

## <a name="create-a-vm"></a>Crear una VM
Para más información, consulte [Creación de una máquina virtual](virtual-machines-linux-creation-choices.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="connect-to-the-vm"></a>Conexión a la máquina virtual
Para más información, consulte [SSH desde Linux](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="query-vm-unique-id"></a>Consulta de un identificador único de máquina virtual
Comando (en el ejemplo se emplea **Ubuntu**):

```bash
sudo dmidecode | grep UUID
```

Resultados esperados del ejemplo:

```bash
UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A
```

Debido a la ordenación de bits de Big Endian, el identificador único real de la máquina virtual en este caso será:

```bash
DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A
```

El identificador único de máquina virtual de Azure se puede usar en escenarios distintos, tanto si la máquina virtual se ejecuta en Azure como si lo hace de forma local, y puede ayudar en los requisitos de las licencias, de los informes o del seguimiento general que pueda tener en sus implementaciones IaaS de Azure. Muchos proveedores independientes de software que crean aplicaciones y las certifican en Azure podrían solicitar que se identifique una máquina virtual de Azure durante su ciclo de vida y se averigüe si la máquina virtual se ejecuta en Azure, de forma local o en otros proveedores en la nube. Con este identificador de plataforma, por ejemplo, se puede detectar si el software tiene la licencia adecuada o correlacionar los datos de la máquina virtual a su origen (por ejemplo, para ayudar a configurar las métricas correctas para la plataforma correcta, a efectuar un seguimiento y a correlacionar estas métricas, entre otros usos).




<!--HONumber=Jan17_HO4-->


