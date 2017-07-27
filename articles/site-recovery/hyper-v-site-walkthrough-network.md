---
title: "Planeamiento de las redes para la replicación de Hyper-V (sin System Center VMM) en Azure con Azure Site Recovery | Microsoft Docs"
description: "Este artículo describe el planeamiento de las redes necesarias al replicar máquinas virtuales de Hyper-V (sin VMM) en Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5ca12254-975d-48e8-a84d-422825dac9b2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 4cacf3ca47b215bc23545d8a0b5c9b6fbd66a8df
ms.contentlocale: es-es
ms.lasthandoff: 06/23/2017


---

# <a name="step-4-plan-networking-for-hyper-v-to-azure-replication"></a>Paso 4: planeamiento de las redes para la replicación de Hyper-V en Azure

En este artículo se resumen las consideraciones sobre el planeamiento de red al replicar máquinas virtuales de Hyper-V (sin System Center VMM) locales en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md).

Publique cualquier comentario en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="connecting-to-replica-vms-after-failover"></a>Conexión a máquinas virtuales de réplica después de la conmutación por error

Al planear su estrategia de conmutación por error y replicación, una de las preguntas claves es cómo conectarse a la máquina virtual de Azure después de la conmutación por error. Hay un par de opciones al diseñar la estrategia de red para máquinas virtuales de réplica de Azure:

- **Dirección IP diferente**: puede seleccionar la opción de usar un intervalo de direcciones IP diferente para la red de máquina virtual de Azure replicada. En este escenario, la máquina virtual obtiene una nueva dirección IP después de la conmutación por error y se requiere una actualización DNS. [Más información](site-recovery-test-failover-vmm-to-vmm.md#prepare-the-infrastructure-for-test-failover)
- **Misma dirección IP**: puede utilizar el mismo intervalo de direcciones IP en Azure después de la conmutación por error, como en el sitio principal local. En un escenario normal, deberá actualizar las rutas con la nueva ubicación de las direcciones IP. Sin embargo, si tiene una VLAN implementada entre el sitio primario y Azure, conservar las direcciones IP para las máquinas virtuales se convierte en una opción válida. Mantener las mismas direcciones IP simplifica la recuperación al reducir los problemas relacionados con la red después de la conmutación por error.


## <a name="retain-ip-addresses"></a>Conservación de las direcciones IP

Desde una perspectiva de recuperación ante desastres, el uso de direcciones IP fijas parece ser el método más sencillo, pero hay una serie de desafíos potenciales. Site Recovery ofrece la funcionalidad de conservar las direcciones IP cuando se conmuta a Azure, con conmutación por error de subred.


### <a name="subnet-failover"></a>Conmutación por error de subred

En este escenario, una subred determinada está presente en el sitio 1 o en el sitio 2, pero nunca en ambos sitios simultáneamente. Para mantener el espacio de direcciones IP en el caso de una conmutación por error, puede organizar mediante programación la infraestructura de enrutador para mover las subredes de un sitio a otro. Durante la conmutación por error, las subredes se mueven con las máquinas virtuales protegidas asociadas. La principal desventaja de esta opción es que en caso de error es preciso mover toda la subred, lo que puede afectar a las consideraciones de granularidad de la conmutación por error.


### <a name="failover-example"></a>Ejemplo de conmutación por error

Veamos un ejemplo de conmutación por error a Azure.

- Una organización ficticia, Woodgrove Bank, tiene una infraestructura local que hospeda sus aplicaciones empresariales. Sus aplicaciones móviles se hospedan en Azure.
- Una conexión de sitio a sitio (VPN) entre la red perimetral local y la red virtual de Azure proporciona conectividad entre las máquinas virtuales de Woodgrove Bank en servidores de Azure y las locales.
- Esta VPN significa que la red virtual de la compañía en Azure aparece como una extensión de su red local.
- Woodgrove desea usar Site Recovery para replicar cargas de trabajo locales en Azure.
 - Woodgrove tiene que tratar con aplicaciones y configuraciones que dependen de direcciones IP codificadas de forma rígida, por lo que necesitan conservar las direcciones IP para sus aplicaciones después de la conmutación por error en Azure.
 - Woodgrove ha asignado direcciones IP del rango de direcciones IP 172.16.1.0/24, 172.16.2.0/24 a los recursos que se ejecutan en Azure.


Para que Woodgrove pueda replicar sus máquinas virtuales en Azure y conservar las direcciones IP, la empresa debe hacer lo siguiente:

1. Cree una red virtual de Azure. Debe ser una extensión de la red local, con el fin de que las aplicaciones puedan conmutar por error sin ningún problema.
2. Azure permite agregar conectividad VPN de sitio a sitio, además de conectividad punto a sitio para las redes virtuales creadas en Azure.
3. Al configurar la conexión de sitio a sitio, en la red de Azure, puede enrutar el tráfico a la ubicación local (red local) solo si el intervalo de direcciones IP es diferente del intervalo de direcciones IP local.
    - Esto se debe a que Azure no admite la ampliación de subredes. Por tanto, si tiene una subred local 192.168.1.0/24, no puede agregar una red local 192.168.1.0/24 en la red de Azure.
    - Esto es normal, ya que Azure no sabe que no hay ninguna máquina virtual activa en la subred y que se está creando la subred solo para fines de recuperación ante desastres.
    - Para poder enrutar correctamente el tráfico saliente de una red Azure, las subredes de la red y la red local no deben estar en conflicto.

![Antes de la conmutación por error de la subred](./media/hyper-v-site-walkthrough-network/network-design7.png)

### <a name="before-failover"></a>Antes de la conmutación por error

1. Cree una red adicional (por ejemplo, Red de recuperación). Esta es la red en la que se crean las máquinas virtuales de la conmutación por error.
2. Para asegurarse de que la dirección IP de una máquina virtual se conserva después de una conmutación por error, en las Propiedades de la máquina virtual > **Configurar**, especifique la misma dirección IP que la máquina virtual tiene localmente y haga clic en **Guardar**.
3. Cuando se produzca la conmutación por error de la máquina virtual, Azure Site Recovery le asignará la dirección IP proporcionada.

    ![Propiedades de red](./media/hyper-v-site-walkthrough-network/network-design8.png)

4. Una vez que se desencadena la conmutación por error y las máquinas virtuales se crean en Azure con la dirección IP requerida, puede conectarse a la red mediante una [Conexión Vnet a Vnet](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Esta acción puede ejecutarse mediante scripts.
5. Las rutas deben modificarse de forma adecuada, para reflejar que 192.168.1.0/24 ahora se ha movido a Azure.

    ![Después de la conmutación por error de la subred](./media/hyper-v-site-walkthrough-network/network-design9.png)

### <a name="after-failover"></a>Después de la conmutación por error

Si no dispone de una red de Azure tal y como se ilustra arriba, puede crear una conexión VPN de sitio a sitio entre el sitio primario y Azure después de la conmutación por error.

## <a name="change-ip-addresses"></a>Cambiar las direcciones IP

Esta [entrada del blog](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) explica cómo configurar la infraestructura de red de Azure cuando no es necesario conservar direcciones IP después de la conmutación por error. Comienza con una descripción de la aplicación, se examina cómo configurar redes locales y en Azure y se concluye con información sobre la ejecución de conmutaciones por error.  

## <a name="next-steps"></a>Pasos siguientes

Vaya al [paso 5: preparación de Azure](hyper-v-site-walkthrough-prepare-azure.md)

