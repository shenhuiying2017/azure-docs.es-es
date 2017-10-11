---
title: "Planeamiento de redes de VMware con replicación de Azure | Microsoft Docs"
description: "En este artículo se describe el planeamiento de red necesario cuando se replican máquinas virtuales VMware en Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5578a0b4-0352-41c3-9cce-56beb17a4d97
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: f164ac68ba6ec650bb3996b4aa870e1b98533a23
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="step-4-plan-networking-for-vmware-to-azure-replication"></a>Paso 4: Planeamiento de redes de VMware con replicación de Azure

En este artículo se resumen las consideraciones de planeamiento de red al replicar máquinas virtuales VMware locales en Azure con el servicio [Azure Site Recovery](site-recovery-overview.md).

Publique cualquier comentario en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="connect-to-replica-vms"></a>Conexión a máquinas virtuales se réplica

Al planear su estrategia de conmutación por error y replicación, una de las preguntas claves es cómo conectarse a la máquina virtual de Azure después de la conmutación por error. Hay un par de opciones para diseñar la estrategia de red para la réplica de máquinas virtuales de Azure:

- **Use different IP address** (Usar una dirección IP distinta): puede seleccionar el uso de otro intervalo de direcciones IP para la red de máquinas virtuales de Azure replicada. En este escenario, la máquina virtual obtiene una nueva dirección IP después de la conmutación por error y se requiere una actualización de DNS.
- **Retain same IP address** (Conservar la misma dirección IP): tal vez prefiera usar el mismo intervalo de direcciones IP que usa en el sitio local principal para la red de Azure después de la conmutación por error. La conservación de las mismas direcciones IP simplifica la recuperación al reducir los problemas relacionados con la red después de la conmutación por error. Pero, cuando replique en Azure, tendrá que actualizar las rutas con la nueva ubicación de las direcciones IP después de la conmutación por error. 


## <a name="retain-ip-addresses"></a>Conservación de las direcciones IP

Site Recovery ofrece la posibilidad de conservar las direcciones IP fijas al conmutar por error a Azure, con una conmutación por error de subredes.

Con la conmutación por error de subredes, una subred específica está presente en el sitio 1 o en el sitio 2, pero nunca en ambos sitios simultáneamente. Para mantener el espacio de direcciones IP en el caso de una conmutación por error, puede organizar mediante programación que la infraestructura de enrutador mueva las subredes de un sitio a otro. Durante la conmutación por error, las subredes se mueven con las máquinas virtuales protegidas asociadas. El principal inconveniente es que si se produce un error, tiene que mover la subred completa.


### <a name="failover-example"></a>Ejemplo de conmutación por error

Veamos un ejemplo de conmutación por error a Azure.

- Una compañía ficticia, Woodgrove Bank, tiene una infraestructura local que hospeda sus aplicaciones empresariales. Las aplicaciones móviles se hospedan en Azure.
- La conectividad entre las máquinas virtuales de Woodgrove Bank en los servidores locales y de Azure se proporciona mediante una conexión de red privada virtual (VPN) de sitio a sitio entre la red perimetral local y la red virtual de Azure.
- Esta VPN implica que la red virtual de la compañía en Azure aparece como una extensión de la red privada local.
- Woodgrove quiere usar Site Recovery para replicar las cargas de trabajo locales en Azure.
 - Woodgrove tiene que tratar con aplicaciones y configuraciones que dependen de direcciones IP codificadas de forma rígida, y por lo tanto necesitan conservar las direcciones IP para sus aplicaciones después de la conmutación por error en Azure.
 - Woodgrove tiene direcciones IP asignadas en el rango 172.16.1.0/24, 172.16.2.0/24 a los recursos que se ejecutan en Azure.


Para que Woodgrove pueda replicar sus máquinas virtuales en Azure y conservar las direcciones IP, la empresa debe hacer lo siguiente:

1. Cree una red virtual de Azure. Debe ser una extensión de la red local, para que las aplicaciones puedan conmutar por error sin problemas.
2. Azure permite agregar conectividad VPN de sitio a sitio, además de conectividad de punto a sitio, a las redes virtuales creadas en Azure.
3. Al configurar la conexión de sitio a sitio en la red de Azure, solo puede enrutar el tráfico a la ubicación local (red local) si el intervalo de direcciones IP es distinto del intervalo de direcciones IP local.
    - Esto se debe a que Azure no admite subredes estiradas. Por lo que si tiene una subred local 192.168.1.0/24, no puede agregar una red local 192.168.1.0/24 en la red de Azure.
    - Esto es normal, ya que Azure no sabe que no hay ninguna máquina virtual activa en la subred y que se está creando la subred solo para fines de recuperación ante desastres.
    - Para poder distribuir correctamente el tráfico de red de una red Azure, las subredes de la red y la red local no deben estar en conflicto.

![Antes de la conmutación por error de la subred](./media/site-recovery-network-design/network-design7.png)

### <a name="before-failover"></a>Antes de la conmutación por error

1. Cree una red adicional (por ejemplo, una red de recuperación). Se trata de la red en la que se crean las máquinas virtuales que han conmutado por error.
2. Para asegurarse de que la dirección IP de una máquina virtual se conserva después de una conmutación por error, en las Propiedades de la máquina virtual > **Configurar**, especifique la misma dirección IP que la máquina virtual tiene localmente y haga clic en **Guardar**.
3. Cuando se produzca la conmutación por error de la máquina virtual, Azure Site Recovery le asignará la dirección IP proporcionada.

    ![Propiedades de red](./media/site-recovery-network-design/network-design8.png)

4. Una vez desencadenada la conmutación por error y creadas las máquinas virtuales en Azure con la dirección IP necesaria, puede conectarse a la red con una [conexión Vnet a Vnet](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Esta acción puede ejecutarse mediante script.
5. Las rutas deben modificarse de forma adecuada para que reflejen que 192.168.1.0/24 se ha movido ahora a Azure.

    ![Después de la conmutación por error de la subred](./media/site-recovery-network-design/network-design9.png)

### <a name="after-failover"></a>Después de la conmutación por error

Si no dispone de una red Azure tal y como se ilustra arriba, puede crear una conexión de VPN de sitio a sitio entre el sitio primario y Azure después de la conmutación por error.

## <a name="change-ip-addresses"></a>Cambio de direcciones IP

En esta [entrada de blog](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) se explica cómo configurar la infraestructura de red de Azure cuando no hay que conservar las direcciones IP después de la conmutación por error. Comienza con una descripción de la aplicación, se examina cómo configurar redes en local y en Azure y se concluye con información sobre la ejecución de las conmutaciones por error.  

## <a name="next-steps"></a>Pasos siguientes

Vaya al [Paso 5: Preparación de Azure](vmware-walkthrough-prepare-azure.md).
