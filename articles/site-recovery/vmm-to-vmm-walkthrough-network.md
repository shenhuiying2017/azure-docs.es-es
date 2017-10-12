---
title: "Planeamiento de redes para la replicación de Hyper-V en un sitio secundario de VMM con Azure Site Recovery | Microsoft Docs"
description: "En este artículo se trata el planeamiento de redes al replicar máquinas virtuales de Hyper-V en un sitio secundario de System Center VMM con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 095f2d73-994e-4fc0-96f2-e03a7d72e492
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.openlocfilehash: a1f3f6e6cba074647195e2b0cbcdc7b4f3dec475
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-3-plan-networking-for-hyper-v-vm-replication-to-a-secondary-vmm-site"></a>Paso 3: Planeamiento de redes para la replicación de máquinas virtuales de Hyper-V en un sitio secundario de VMM

Después de revisar los requisitos previos de la implementación, lea este artículo para planear redes al replicar máquinas virtuales de Hyper-V administradas en nubes de System Center Virtual Machine Manager (VMM) en un sitio secundario mediante [Azure Site Recovery](site-recovery-overview.md) en Azure Portal. 

Publique cualquier comentario que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="network-mapping-overview"></a>Información general de asignación de red

La asignación de red se usa cuando se replican máquinas virtuales de Hyper-V (administradas en VMM) en un centro de datos secundario. La asignación de red se produce entre redes de máquinas virtuales en un servidor VMM de origen y redes de máquinas virtuales en un servidor VMM de destino. La asignación hace lo siguiente:

- **Conexión de red**: las máquinas virtuales se conectan a las redes adecuadas después de la conmutación por error. La máquina virtual de réplica se conectará a la red de destino que se asigna a la red de origen.
- **Ubicación óptima**: coloca las máquinas virtuales de réplica en los servidores host de Hyper-V de forma óptima. Las máquinas virtuales de réplica se colocarán en los hosts que pueden tener acceso a las redes de máquinas virtuales asignadas.
- **Sin asignación de redes**: si no configura la asignación de redes, las máquinas virtuales de réplica no se conectarán a ninguna red de máquinas virtuales después de la conmutación por error.


### <a name="example"></a>Ejemplo

Este es un ejemplo para ilustrar este mecanismo. Vamos a utilizar una organización con dos ubicaciones en Nueva York y Chicago.

**Ubicación** | **Servidor VMM** | **Redes de máquinas virtuales** | **Asignado a**
---|---|---|---
Nueva York | VMM-NewYork| VMNetwork1-NewYork | Asignado a VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | No asignado
Chicago | VMM-Chicago| VMNetwork1-Chicago | Asignado a VMNetwork1-NewYork
 | | VMNetwork1-Chicago | No asignado

En este ejemplo:

- Cuando se crea una máquina virtual de réplica para cualquier máquina virtual que está conectada a VMNetwork1-NewYork, se conectará a VMNetwork1-Chicago.
- Cuando se crea una máquina virtual de réplica para VMNetwork2-NewYork o VMNetwork2-Chicago, no se conectará a ninguna red.

Aquí es cómo se configuran las nubes de VMM en nuestra organización de ejemplo y las redes lógicas asociadas a las nubes.

#### <a name="cloud-protection-settings"></a>Configuración de la protección de la nube

**Nube protegida** | **Proteger nube** | **Red lógica (Nueva York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>N/D</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>N/D</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

#### <a name="logical-and-vm-network-settings"></a>Configuración de la red lógica y máquinas virtuales

**Ubicación** | **Red lógica** | **Red de VM asociada**
---|---|---
Nueva York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

#### <a name="target-network-settings"></a>Configuración de red de destino

Según esta configuración, cuando selecciona la red de máquinas virtuales de destino, en la siguiente tabla se muestran las opciones que estarán disponibles.

**Selección** | **Nube protegida** | **Proteger nube** | **Red de destino disponible**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponible
 | GoldCloud1 | GoldCloud2 | Disponible
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | No disponible
 | GoldCloud1 | GoldCloud2 | Disponible


Si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.


#### <a name="failback-behavior"></a>Comportamiento de conmutación por recuperación

Para ver lo que ocurre en el caso de conmutación por recuperación (replicación inversa), supongamos que VMNetwork1-NewYork se asigna a VMNetwork1-Chicago, con la siguiente configuración.


**Máquina virtual** | **Conectada a la red de VM**
---|---
VM1 | VMNetwork1-Network
VM2 (réplica de VM1) | VMNetwork1-Chicago

Con esta configuración, revisemos lo que ocurre en un par de escenarios posibles.

**Escenario** | **Resultado**
---|---
Sin cambios en las propiedades de red de VM-2 después de la conmutación por error. | VM-1 sigue estando conectada a la red de origen
Las propiedades de red de VM-2 cambian después de la conmutación por error y está desconectada. | VM-1 se desconecta.
Las propiedades de red de VM-2 cambian después de la conmutación por error y está conectada a VMNetwork2-Chicago. | Si no está asignada VMNetwork2-Chicago, se desconectará VM-1.
Se cambia la asignación de redes de VMNetwork1-Chicago. | VM-1 se conectará a la red ahora asignada a VMNetwork1-Chicago.



## <a name="prepare-for-network-mapping"></a>Preparar la asignación de red

1. En los servidores VMM de origen y destino, debe tener una red lógica asociada a las nubes de origen y destino. 
2. En los servidores de origen y destino, debe tener una red de máquina virtual vinculada a la red lógica.
3. Máquinas virtuales en hosts de Hyper-V en la ubicación de origen deben vincularse a la red de máquina virtual de origen. Si solo usa un servidor VMM sencillo, puede configurar la asignación entre redes de máquina virtual en el mismo servidor.

Esto es lo que sucede al configurar la asignación de red durante la implementación de Site Recovery:

- Al configurar la asignación de red y seleccionar una red de máquina virtual de destino, se mostrarán las nubes de origen de VMM que usen la red de máquina virtual de origen, junto con las redes de máquina virtual de destino disponibles de las nubes de destino.
- - Cuando se configura correctamente la asignación y se habilita la replicación, se conectará una máquina virtual de origen a su red de máquina virtual de origen y su réplica de la ubicación de destino se conectará a la red de máquina virtual asignada.
- Si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.

## <a name="connect-to-vms-after-failover"></a>Conectarse a las máquinas virtuales después de la conmutación por error

Al planear su estrategia de conmutación por error y replicación, una de las preguntas claves es cómo conectarse a la réplica después de la conmutación por error. Hay dos opciones: 

- **Use different IP address** (Usar una dirección IP distinta): puede seleccionar el uso de otra dirección IP para la máquina virtual replicada. En este escenario, la máquina virtual obtiene una nueva dirección IP después de la conmutación por error y se requiere una actualización DNS.
- **Conservar la misma dirección IP**: es posible que quiera usar la misma dirección IP para la máquina virtual de réplica. La conservación de las mismas direcciones IP simplifica la recuperación al reducir los problemas relacionados con la red después de la conmutación por error. 

## <a name="retain-ip-addresses"></a>Conservación de las direcciones IP

Si desea conservar las direcciones IP del sitio principal después de la conmutación por error al sitio secundario, puede realizar una conmutación por error de subred completa y actualizar las rutas para indicar la nueva ubicación de las direcciones IP, o bien implementar de forma alternativa una subred estirada entre los sitios principales y de recuperación.

### <a name="stretched-subnet"></a>Subred estirada

En una subred estirada, la subred está disponible a la vez en el sitio principal y secundario. Si mueve un servidor y su configuración de IP (nivel 3) al sitio secundario, la red enrutará el tráfico a la nueva ubicación automáticamente. 

Desde una perspectiva del nivel 2 (nivel de vínculo de datos), necesita un equipo de redes que pueda administrar una red VLAN estirada. Además, mediante la expansión de la VLAN, el dominio de error potencial se extiende a ambos sitios, básicamente convirtiéndose en un único punto de error. Aunque es poco probable, podría ocurrir que se iniciara una tormenta de difusión y no se pudiera aislar. 


### <a name="subnet-failover"></a>Conmutación por error de subred

Puede ejecutar una conmutación por error de la subred para disfrutar de las ventajas de la subred estirada, sin estirarla realmente. En esta solución, una subred estará disponible en el sitio de origen o destino, pero no en ambos a la vez. Para mantener el espacio de direcciones IP en el caso de una conmutación por error, puede organizar mediante programación de la infraestructura de enrutador para mover las subredes de un sitio a otro. Tras producirse la conmutación por error, las subredes se moverían con las máquinas virtuales asociadas. El principal inconveniente es que si se produce un error, tiene que mover la subred completa.

### <a name="example"></a>Ejemplo

Este es un ejemplo de conmutación por error de la subred completa. El sitio principal tiene aplicaciones que se ejecutan en la subred 192.168.1.0/24. En la conmutación por error, todas las máquinas virtuales de esta subred se conmutan por error al sitio secundario y conservan sus direcciones IP. Las rutas tienen que modificarse para reflejar el hecho de que todas las máquinas virtuales que pertenezcan a la subred 192.168.1.0/24 se han movido al sitio secundario.

En los gráficos siguientes se muestran las subredes antes y después de la conmutación por error:

- Antes de la conmutación por error, la subred 192.168.0.1/24 está activa en el sitio de origen, volviéndose activa en el sitio secundario después de la conmutación por error.
- Las rutas entre el sitio principal y el sitio de recuperación, el tercer sitio y el sitio principal, y el tercer sitio y el sitio de recuperación tendrán que modificarse correctamente.

**Antes de la conmutación por error**

![Antes de la conmutación por error](./media/vmm-to-vmm-walkthrough-network/network-design2.png)

**Después de la conmutación por error**

![Después de la conmutación por error](./media/vmm-to-vmm-walkthrough-network/network-design3.png)

Después de la conmutación por error, esto es lo que sucede:

- Site Recovery asigna una dirección IP a cada interfaz de red en la máquina virtual del grupo de direcciones IP estáticas en la red correspondiente para cada instancia de VMM.
- Si el grupo de direcciones IP del sitio secundario es el mismo que el del sitio de origen, Site Recovery asigna la misma dirección IP (de la máquina virtual de origen) a la máquina virtual de réplica. La dirección IP está reservada en VMM, pero no está establecida como dirección IP de conmutación por error en el host de Hyper-V. La dirección IP de conmutación por error de un host de Hyper-V se establece inmediatamente antes de la conmutación por error.
- Si no está disponible la misma dirección IP, Site Recovery asigna otra dirección IP disponible del grupo.
- Si las máquinas virtuales usan DHCP, Site Recovery no administra las direcciones IP. Es necesario que compruebe que el servidor DHCP del sitio secundario puede asignar la dirección desde el mismo intervalo que el sitio de origen.

### <a name="validate-the-ip-address"></a>Validar la dirección IP

Después de habilitar la protección para una máquina virtual, puede usar el siguiente script de ejemplo para comprobar la dirección asignada a la máquina virtual. La misma dirección IP se establecerá como dirección IP de conmutación por error y se asignará a la máquina virtual en el momento de la conmutación por error:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="changing-ip-addresses"></a>Cambio de las direcciones IP

En este escenario, se cambian las direcciones IP de las máquinas virtuales que se conmutan por error. El inconveniente de esta solución es el mantenimiento necesario. Normalmente, DNS se actualizará una vez iniciadas las máquinas virtuales de réplica. Es posible que las entradas DNS deban cambiarse o alterarse en la red y que las entradas almacenadas en caché deban actualizarse. Esto puede dar lugar a un tiempo de inactividad. El tiempo de inactividad puede mitigarse del siguiente modo:

- Utilice valores de TTL bajos para las aplicaciones de intranet.
- Utilice el siguiente script en un plan de recuperación de Site Recovery para actualizar el servidor DNS y garantizar una actualización puntual. No necesita el script si usa un registro de DNS dinámico.

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Ejemplo 

Eche un vistazo a un escenario en el que tenga previsto usar otras direcciones IP en los sitios principales y de recuperación. En este ejemplo, tenemos otras direcciones IP en los sitios principales y secundarios, y existe un tercer sitio desde el que se puede tener acceso a las aplicaciones hospedadas en el sitio principal o de recuperación.

- Antes de la conmutación por error, las aplicaciones son la subred hospedada 192.168.1.0/24 del sitio principal y están configuradas para estar en la subred 172.16.1.0/24 del sitio secundario después de una conmutación por error.
- Las rutas de red/conexiones de VPN se han configurado correctamente de forma que los tres sitios son accesibles entre sí.
- Después de la conmutación por error, las aplicaciones se restaurarán en la subred de recuperación. En este escenario no es necesario conmutar por error toda la subred y no se requiere ningún cambio para volver a configurar las rutas de red o VPN. La conmutación por error y algunas actualizaciones de DNS garantizan que las aplicaciones sigan siendo accesibles.
- Si el DNS está configurado para permitir actualizaciones dinámicas, las máquinas virtuales se registrarán a sí mismas con la nueva dirección IP al iniciarse después de la conmutación por error.

**Antes de la conmutación por error**

![Otra dirección IP: antes de la conmutación por error](./media/vmm-to-vmm-walkthrough-network/network-design10.png)

**Después de la conmutación por error**

![Otra dirección IP: después de la conmutación por error](./media/vmm-to-vmm-walkthrough-network/network-design11.png)



## <a name="next-steps"></a>Pasos siguientes

Vaya a [Paso 4: Preparación de VMM y Hyper-V](vmm-to-vmm-walkthrough-vmm-hyper-v.md).


