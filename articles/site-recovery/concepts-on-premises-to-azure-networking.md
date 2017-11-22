---
title: "Configuración de direccionamiento IP para conectar tras la conmutación por error a Azure con Azure Site Recovery | Microsoft Docs"
description: "Se describe cómo configurar el direccionamiento IP para conectarse a máquinas virtuales de Azure tras la conmutación por error desde el entorno local con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: carmonm
editor: 
ms.assetid: f02cdbea-0940-48bf-9fa5-f38d9e584fae
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/14/2017
ms.author: pratshar
ms.openlocfilehash: 5519a965d9828cfa1e73ba12f8acd1d509a36a66
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="set-up-ip-addressing-to-connect-after-failover-to-azure"></a>Configuración del direccionamiento IP para conectarse tras la conmutación por error a Azure

En este artículo se explican los requisitos de redes para conectarse a máquinas virtuales de Azure después de usar el servicio [Azure Site Recovery](site-recovery-overview.md) para replicación y conmutación por error en Azure.

En este artículo, aprenderá sobre:

> [!div class="checklist"]
> * Los métodos de conexión que puede usar
> * Cómo usar una dirección IP distinta para máquinas virtuales de Azure replicadas
> * Cómo conservar las direcciones IP de las máquinas virtuales de Azure después de la conmutación por error

## <a name="connecting-to-replica-vms"></a>Conexión a máquinas virtuales de réplica

Al planear su estrategia de conmutación por error y replicación, una de las preguntas claves es cómo conectarse a la máquina virtual de Azure después de la conmutación por error. Hay un par de opciones para diseñar la estrategia de red para la réplica de máquinas virtuales de Azure:

- **Use different IP address** (Usar una dirección IP distinta): puede seleccionar el uso de otro intervalo de direcciones IP para la red de máquinas virtuales de Azure replicada. En este escenario, la máquina virtual obtiene una nueva dirección IP después de la conmutación por error y se requiere una actualización de DNS.
- **Retain same IP address** (Conservar la misma dirección IP): tal vez prefiera usar el mismo intervalo de direcciones IP que usa en el sitio local principal para la red de Azure después de la conmutación por error. La conservación de las mismas direcciones IP simplifica la recuperación al reducir los problemas relacionados con la red después de la conmutación por error. Pero, cuando replique en Azure, tendrá que actualizar las rutas con la nueva ubicación de las direcciones IP después de la conmutación por error. 

## <a name="retaining-ip-addresses"></a>Conservación de direcciones IP

Site Recovery ofrece la posibilidad de conservar las direcciones IP fijas al conmutar por error a Azure, con una conmutación por error de subredes.

- Con la conmutación por error de subredes, una subred específica está presente en el sitio 1 o en el sitio 2, pero nunca en ambos sitios simultáneamente.
- Para mantener el espacio de direcciones IP en el caso de una conmutación por error, puede organizar mediante programación que la infraestructura de enrutador mueva las subredes de un sitio a otro.
- Durante la conmutación por error, las subredes se mueven con las máquinas virtuales protegidas asociadas. El principal inconveniente es que si se produce un error, tiene que mover la subred completa.


### <a name="failover-example"></a>Ejemplo de conmutación por error

Veamos un ejemplo para una conmutación por error en Azure con una empresa ficticia, Woodgrove Bank.

- Woodgrove Bank hospeda las aplicaciones empresariales en un sitio local y las aplicaciones móviles, en Azure.
- Hay conectividad de sitio a sitio de VPN entre la red perimetral local y la red virtual de Azure. Debido a la conexión VPN, la red virtual de Azure aparece como extensión de la red local.
- Woodgrave quiere replicar las cargas de trabajo locales en Azure con Site Recovery.
 - Woodgrove tiene aplicaciones que dependen de direcciones IP codificadas de forma rígida, por lo que necesitan conservar las direcciones IP para las aplicaciones después de la conmutación por error en Azure.
 - Los recursos que se ejecutan en Azure usan este intervalo de direcciones IP 172.16.1.0/24, 172.16.2.0/24.

![Antes de la conmutación por error de la subred](./media/site-recovery-network-design/network-design7.png)

**Infraestructura antes de la conmutación por error**


Para que Woodgrove pueda replicar sus máquinas virtuales en Azure y conservar las direcciones IP, la empresa debe hacer lo siguiente:


1. Cree una red virtual de Azure en la cual se crearán las máquinas virtuales de Azure después de la conmutación por error de las máquinas locales. Debe ser una extensión de la red local, para que las aplicaciones puedan conmutar por error sin problemas.
2. Antes de la conmutación por error, en Site Recovery se asigna la misma dirección IP en las propiedades de la máquina. Después de la conmutación por error, Site Recovery asigna esta dirección a la máquina virtual de Azure.
3. Una vez que se ejecuta la conmutación por error y que se crean las máquinas virtuales de Azure con la misma dirección IP, se conectan a la red mediante una [conexión de red virtual a red virtual](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Esta acción puede ejecutarse mediante scripts.
4. Se necesitan modificar rutas para reflejar que 192.168.1.0/24 ahora se movió a Azure.


**Infraestructura después de la conmutación por error**

![Después de la conmutación por error de la subred](./media/site-recovery-network-design/network-design9.png)

#### <a name="site-to-site-connection"></a>Conexión de sitio a sitio

Además de la conexión de red virtual a red virtual, después de la conmutación por error, Woodgrove puede configurar la conectividad VPN de sitio a sitio:
- Cuando configura una conexión de sitio a sitio, en la red de Azure solo se puede enrutar el tráfico a la ubicación local (red local) si el intervalo de direcciones IP es distinto del intervalo de direcciones IP local. Esto se debe a que Azure no admite subredes estiradas. Por tanto, si tiene la subred 192.168.1.0/24 en el entorno local, no puede agregar una subred 192.168.1.0/24 de red local en la red de Azure. Esto es normal, ya que Azure no sabe que no hay ninguna máquina virtual activa en la subred y que se está creando la subred solo para fines de recuperación ante desastres.
- Para poder distribuir correctamente el tráfico de red de una red Azure, las subredes de la red y la red local no deben estar en conflicto.




## <a name="assigning-new-ip-addresses"></a>Asignación de direcciones IP nuevas

En esta [entrada de blog](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) se explica cómo configurar la infraestructura de red de Azure cuando no hay que conservar las direcciones IP después de la conmutación por error. Comienza con una descripción de la aplicación, se examina cómo configurar redes en local y en Azure y se concluye con información sobre la ejecución de las conmutaciones por error. 

## <a name="next-steps"></a>Pasos siguientes
[Ejecución de una conmutación por error](site-recovery-failover.md)




