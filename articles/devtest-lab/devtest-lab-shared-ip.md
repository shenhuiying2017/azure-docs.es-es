---
title: Direcciones IP compartidas en Azure DevTest Labs | Microsoft Docs
description: "Obtenga información sobre cómo Azure DevTest Labs usa direcciones IP compartidas para minimizar las direcciones IP públicas necesarias para acceder a las máquinas virtuales de su laboratorio."
services: devtest-lab
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: v-craic
ms.openlocfilehash: 921db397a9a30a102d47d5e6c379e98daba6e5c5
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Direcciones IP compartidas en Azure DevTest Labs

Las máquinas virtuales de laboratorio de Azure DevTest Labs comparten la misma dirección para minimizar el número de direcciones IP públicas necesarias para acceder a estas máquinas virtuales.  En este artículo se describe cómo funcionan las direcciones IP compartidas y sus opciones de configuración relacionadas.

## <a name="shared-ip-setting"></a>Configuración de IP compartidas

Cuando se crea un laboratorio, reside en una subred de una red virtual.  De forma predeterminada, esta subred se crea con la opción **Habilitar IP pública compartida** establecida en *Sí*.  Esta configuración crea una dirección IP pública para toda la subred.  Para obtener más información sobre cómo configurar redes virtuales y subredes, vea [Configuración de una red virtual en Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nueva subred de laboratorio](media/devtest-lab-shared-ip/lab-subnet.png)

Para habilitar esta opción en laboratorios existentes, seleccione **Configuration and policies (Directivas y configuración) > Redes virtuales**. Después, seleccione una red virtual de la lista y elija **HABILITAR IP PÚBLICA COMPARTIDA**  para la subred seleccionada. También puede deshabilitar esta opción en cualquier laboratorio si no quiere compartir una dirección IP pública en todas las máquinas virtuales del laboratorio.

Las máquinas virtuales creadas en este laboratorio usarán una dirección IP compartida de forma predeterminada.  Al crear la máquina virtual, esta configuración se puede encontrar en la hoja **Configuración avanzada**, bajo **Configuración de dirección IP**.

![Nueva máquina virtual](media/devtest-lab-shared-ip/new-vm.png)

- **Compartido:** todas las máquinas virtuales creadas como **Compartido** se colocan en un solo grupo de recursos. Se asigna una sola dirección IP al grupo de recursos y todas las máquinas virtuales usarán esa dirección IP.
- **Público:** todas las máquinas virtuales que se crean tienen su propia dirección IP y se crean en su propio grupo de recursos.
- **Privado:** todas las máquinas virtuales que se crean usan una dirección IP privada. No podrá conectarse a estas máquinas virtuales directamente desde Internet mediante Escritorio remoto.

Siempre que se agrega una máquina virtual con la dirección IP compartida a la subred, DevTest Labs agrega automáticamente la máquina virtual a un equilibrador de carga y le asigna un número de puerto TCP en la dirección IP pública, que se reenvía al puerto RDP de la máquina virtual.  

## <a name="using-the-shared-ip"></a>Uso de la dirección IP compartida

- **Usuarios de Linux:** use SSH para conectarse a la máquina virtual con la dirección IP o el nombre de dominio completo, seguido de dos puntos y del puerto. Por ejemplo, en la imagen siguiente, la dirección RDP para conectarse a la máquina virtual es `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.

  ![Ejemplo de máquina virtual](media/devtest-lab-shared-ip/vm-info.png)

- **Usuarios de Windows:** en Azure Portal, seleccione el botón **Conectar** para descargar un archivo RDP preconfigurado y acceder a la máquina virtual.

## <a name="next-steps"></a>pasos siguientes

* [Definición de directivas de laboratorio en Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Configuración de una red virtual en Azure DevTest Labs](devtest-lab-configure-vnet.md)





