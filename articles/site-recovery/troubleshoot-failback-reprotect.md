---
title: "Solución de errores producidos durante la conmutación por recuperación en el entorno local desde Azure y reprotección posterior en Azure | Documentos de Microsoft"
description: "Este artículo describe maneras de solucionar errores comunes que se presentan en la conmutación por recuperación en el entorno local desde Azure y durante la reprotección"
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: rajanaki
ms.openlocfilehash: d487c1fcf7fb6444c2b8489df839a6450715ae0a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="troubleshoot-errors-reported-during-the-process-of-failback"></a>Solución de errores notificados durante el proceso de conmutación por recuperación
La conmutación por recuperación implica básicamente dos pasos principales. Uno consiste en reproteger máquinas virtuales de Azure en un entorno local, y el segundo es el que realmente lleva a cabo la conmutación por recuperación de Azure en el entorno local.

## <a name="troubleshoot-errors-when-reprotecting-a-virtual-machine-back-to-on-premises-after-failover"></a>Solución de errores al reproteger una máquina virtual en el entorno local, después de la conmutación por error
Es posible que aparezca uno de los errores siguientes mientras se realiza la reprotección de una máquina virtual en Azure. Para solucionar los problemas, use los pasos que se describen para cada condición de error.


### <a name="error-code-95226"></a>Código de error 95226

*No se pudo aplicar la reprotección porque la máquina virtual de Azure no pudo acceder al servidor de configuración local.*

Aparece cuando 
1. La máquina virtual de Azure no ha podido acceder al servidor de configuración local y, por tanto, no se ha podido detectar y registrar en el servidor de configuración. 
2. Es posible que el servicio InMage Scout Application en la máquina virtual de Azure que debe estar en ejecución para poder comunicarse con al servidor de configuración local no ejecute la conmutación por error posterior.

Para resolver este problema
1. Es preciso asegurarse de que la red de la máquina virtual de Azure se configura de modo que la máquina virtual pueden comunicarse con el servidor de configuración local. Para ello, configure una VPN de sitio a sitio en el centro de datos local o una conexión de ExpressRoute con pares privados en la red virtual de la máquina virtual de Azure. 
2. Si ya tiene una red configurada de modo que la máquina virtual de Azure pueda comunicarse con el servidor de configuración local, inicie sesión en la máquina virtual y marque "Servicio InMage Scout Application". Si observa que el servicio InMage Scout Application no se ejecuta, inícielo manualmente y asegúrese de que el tipo de inicio del servicio está establecido Automático.

### <a name="error-code-78052"></a>Código de error 78052
No se puede realizar la reprotección y aparece el mensaje de error: *No se pudo completar la protección de la máquina virtual.*

Dicho error puede aparecer por dos motivos
1. La máquina virtual que va a volver a proteger tiene Windows Server 2016. Actualmente este sistema operativo no es compatible con la conmutación por recuperación, pero lo será pronto.
2. Ya existe una máquina virtual con el mismo nombre en el servidor de destino maestro al que se va a realizar la conmutación por recuperación.

Para resolver este problema puede seleccionar otro servidor de destino de un host diferente, con el fin de que la reprotección cree la máquina en otro host, donde los nombres no entren en conflicto. También puede mover el servidor de destino maestro con vMotion a un host en el que no se produzca la colisión de nombres. Si la máquina virtual existente es una máquina aislada, puede cambiarle el nombre para que se pueda crear la nueva máquina virtual en el mismo host ESXi.

### <a name="error-code-78093"></a>Código de error 78093

*La VM no se está ejecutando, no responde o no está accesible.*

Para volver a proteger una máquina virtual en la que se ha realizado la conmutación por error en la ubicación local, la máquina virtual de Azure debe estar en ejecución. Esto es para que el servicio de movilidad se registre con el servidor de configuración local y puede empezar a realizar la replicación mediante la comunicación con el servidor de procesos. Si el equipo está en una red incorrecta o no se ejecuta (bloqueado o apagado), el servidor de configuración no puede acceder al servicio de movilidad de la máquina virtual para iniciar la reprotección. Puede reiniciar la máquina virtual para que pueda volver a la comunicación con el entorno local. Reinicie el trabajo de reprotección después de iniciar la máquina virtual de Azure

### <a name="error-code-8061"></a>Código de error 8061

*El almacén de datos no es accesible desde el host ESXi.*

Vea los [requisitos previos del destino principal](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) y los [almacenes de datos de soporte](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback) para realizar la conmutación por recuperación.


## <a name="troubleshoot-errors-when-performing-a-failback-of-an-azure-virtual-machine-back-to-on-premises"></a>Solucionar errores al realizar una conmutación por recuperación de una máquina virtual de Azure en un entorno local
Es posible que aparezca uno de los errores siguientes mientras se realiza una conmutación por recuperación de una máquina virtual de Azure en un entorno local. Para solucionar los problemas, use los pasos que se describen para cada condición de error.

### <a name="error-code-8038"></a>Código de error 8038

*No se pudo iniciar la máquina virtual local debido al error*

Esto sucede cuando la máquina virtual local se inicia en un host que no tiene suficiente memoria aprovisionada.

Para resolver este problema

1. Puede aprovisionar más memoria en el host ESXi.
1. Puede mover la máquina virtual con vMotion a otro host ESXi que tenga memoria suficiente para iniciar la máquina virtual.