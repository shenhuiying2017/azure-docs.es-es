---
title: Solución de errores durante la conmutación por recuperación de máquinas virtuales de Azure en instancias de VMware locales con Azure Site Recovery | Microsoft Docs
description: En este artículo se describen maneras de solucionar errores comunes de conmutación por recuperación y reprotección durante la conmutación por recuperación en VMware desde Azure, mediante Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: rajanaki
ms.openlocfilehash: 480c3524ad4fb8a8c6ea02f09b8d27f254da9b08
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
ms.locfileid: "29941046"
---
# <a name="troubleshoot-failback-from-azure-to-vmware"></a>Solución de problemas en la conmutación por recuperación de Azure en VMware

En este artículo se explica cómo solucionar los problemas que se puede encontrar al realizar una conmutación por recuperación de las máquinas virtuales de Azure en la infraestructura local de VMware, después de una conmutación por error en Azure mediante [Azure Site Recovery](site-recovery-overview.md).

La conmutación por recuperación implica básicamente dos pasos principales. En primer lugar, después de la recuperación por error, debe volver a proteger las máquinas virtuales de Azure en las ubicaciones locales para que puedan comenzar la replicación. El segundo paso es ejecutar una conmutación por error desde Azure, para luego ejecutar una conmutación por recuperación en su sitio local.

## <a name="troubleshoot-reprotection-errors"></a>Solución de errores de reprotección

En esta sección se detallan los errores más habituales de reprotección y cómo corregirlos.

### <a name="error-code-95226"></a>Código de error 95226

**No se pudo aplicar la reprotección porque la máquina virtual de Azure no pudo acceder al servidor de configuración local.**

Este error se produce cuando:

* La máquina virtual de Azure no puede llegar al servidor de configuración local. La máquina virtual no se puede detectar ni registrar en el servidor de configuración.
* El servicio de aplicación InMage Scout no se ejecuta en la máquina virtual de Azure después de la conmutación por error. El servicio es necesario para las comunicaciones con el servidor de configuración local.

Para resolver este problema:

* Compruebe que la red de VM de Azure permite que la máquina virtual de Azure se comunique con el servidor de configuración local. Puede configurar una VPN de sitio a sitio en el centro de datos local o una conexión de Azure ExpressRoute con pares privados en la red virtual de la máquina virtual de Azure.
* Si la máquina virtual puede comunicarse con el servidor de configuración local, inicie sesión en la máquina virtual. A continuación, compruebe el servicio de aplicación de InMage Scout. Si ve que no se está ejecutando, inicie el servicio manualmente. Compruebe que el tipo de inicio del servicio está configurado en **Automático**.

### <a name="error-code-78052"></a>Código de error 78052

**No se pudo finalizar la protección de la máquina virtual.**

Este problema puede suceder si ya hay una máquina virtual con el mismo nombre en el servidor de destino maestro en el que se está realizando la conmutación por recuperación.

Para resolver este problema:

* Seleccione otro servidor de destino maestro de un host diferente, con el fin de que la reprotección cree la máquina en otro host, donde los nombres no entren en conflicto.
* También puede usar vMotion para mover el destino maestro a otro host en el que no se produzca la colisión de nombres. Si la máquina virtual existente es una máquina aislada, cámbiele el nombre para que se pueda crear la nueva máquina virtual en el mismo host ESXi.


### <a name="error-code-78093"></a>Código de error 78093

**La VM no se está ejecutando, no responde o no está accesible.**

Para resolver este problema:

Para volver a proteger una máquina virtual que ha conmutado por error, la máquina virtual de Azure debe estar en ejecución para que Mobility Service se registre con el servidor de configuración local y pueda empezar a realizar la replicación mediante la comunicación con el servidor de procesos. Si el equipo está en una red incorrecta o no se ejecuta (bloqueado o apagado), el servidor de configuración no puede acceder a Mobility Service de la máquina virtual para iniciar la reprotección.

* Puede reiniciar la máquina virtual para que pueda comenzar la comunicación con el entorno local.
* Reinicie el trabajo de reprotección después de iniciar la máquina virtual de Azure.

### <a name="error-code-8061"></a>Código de error 8061

**El almacén de datos no es accesible desde el host ESXi.**

Compruebe los [requisitos previos del servidor de destino maestro y los almacenes de datos admitidos](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) para la conmutación por recuperación.


## <a name="troubleshoot-failback-errors"></a>Solución de errores en la conmutación por recuperación

En esta sección se describen los errores habituales que pueden surgir durante una conmutación por recuperación.

### <a name="error-code-8038"></a>Código de error 8038

**No se pudo iniciar la máquina virtual local debido al error.**

Este problema sucede cuando la máquina virtual local se inicia en un host que no tiene suficiente memoria aprovisionada. 

Para resolver este problema:

* Aprovisione más memoria en el host ESXi.
* Además, puede mover la máquina virtual con vMotion a otro host ESXi que tenga memoria suficiente para iniciar la máquina virtual.
