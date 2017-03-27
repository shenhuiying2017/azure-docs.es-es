---
title: Direcciones IP compartidas en Azure DevTest Labs | Microsoft Docs
description: "Obtenga información sobre cómo Azure DevTest Labs usa direcciones IP compartidas para minimizar las direcciones IP públicas necesarias para acceder a las máquinas virtuales de su laboratorio."
services: devtest-lab
documentationcenter: na
author: camsoper
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: b4ae6b16880e4a5e53c8f94dc814e25254c20d73
ms.lasthandoff: 03/10/2017

---

# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Direcciones IP compartidas en Azure DevTest Labs

Azure DevTest Labs usa direcciones IP compartidas para minimizar las direcciones IP públicas necesarias para acceder a las máquinas virtuales de su laboratorio.  En este artículo se describe cómo funcionan las direcciones IP compartidas y sus opciones de configuración relacionadas.

## <a name="shared-ip-setting"></a>Configuración de IP compartidas

Cuando se crea un laboratorio, reside en una subred de una red virtual.  De forma predeterminada, esta subred se crea con la opción **Habilitar IP pública compartida** establecida en *Sí*.  Esta configuración crea una dirección IP pública para toda la subred.  Puede desactivar esta opción después de crear un laboratorio [siguiendo los pasos descritos en este artículo](devtest-lab-configure-vnet.md).

![Nueva subred de laboratorio](media/devtest-lab-shared-ip/lab-subnet.png)

Las máquinas virtuales creadas en este laboratorio usarán una dirección IP compartida de forma predeterminada.  Al crear la máquina virtual, esta configuración se puede encontrar en la hoja **Configuración avanzada**, bajo **Configuración de dirección IP**.

![Nueva máquina virtual](media/devtest-lab-shared-ip/new-vm.png)

Siempre que una máquina virtual con la dirección IP compartida habilitada se agregue a la subred, se asignará un puerto TCP en la dirección IP pública que reenvía el tráfico al puerto RDP de la máquina virtual.  

## <a name="using-the-shared-ip"></a>Uso de la dirección IP compartida

Se conectará al Escritorio remoto en la máquina virtual de un cliente RDP con la dirección IP o el nombre de dominio completo, seguido de dos puntos y del puerto.  Por ejemplo, en la imagen siguiente, la dirección RDP para conectarse a la máquina virtual es `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.  También, en Azure Portal, seleccione el botón **Conectar** para descargar un archivo RDP configurado previamente.

![Ejemplo de máquina virtual](media/devtest-lab-shared-ip/vm-info.png)

## <a name="next-steps"></a>Pasos siguientes

* [Definición de directivas de laboratorio en Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Configuración de una red virtual en Azure DevTest Labs](devtest-lab-configure-vnet.md)






