---
title: "Conmutación por error de prueba (VMM a VMM) en Azure Site Recovery | Microsoft Docs"
description: "Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Obtenga información acerca de la conmutación por error en Azure o en un centro de datos secundario."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: pratshar
ms.openlocfilehash: afc4790d5714ce7145c8f4291a05acc2e9882a9b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="test-failover-vmm-to-vmm-in-site-recovery"></a>Conmutación por error de prueba (VMM a VMM) en Site Recovery


En este artículo se proporcionan información e instrucciones para realizar una conmutación por error de prueba o una exploración de recuperación ante desastres de máquinas virtuales y servidores físicos protegidos con Azure Site Recovery. Deberá usar un sitio administrado de System Center Virtual Machine Manager (VMM) local como sitio de recuperación.

La conmutación por error de prueba se ejecuta para validar la estrategia de replicación o para realizar una exploración de la recuperación ante desastres sin perder datos ni tiempo de actividad. La conmutación por error de prueba no afecta a la replicación en curso ni al entorno de producción. Puede ejecutarla en una máquina virtual o en un [plan de recuperación](site-recovery-create-recovery-plans.md). Al desencadenar una conmutación por error de prueba, debe especificar la red a la que deben conectarse las máquinas virtuales de prueba. Puede seguir el progreso de la conmutación por error de prueba en la página **Trabajos**.  

Si tiene cualquier comentario o pregunta, publíquelos en la parte inferior de este artículo o en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-the-infrastructure-for-test-failover"></a>Preparación de la infraestructura para la conmutación por error de prueba
Si desea ejecutar una conmutación por error de prueba con una red existente, prepare Active Directory, DHCP y DNS en esa red.

Si desea ejecutar una conmutación por error de prueba mediante la creación automática de redes de máquinas virtuales, agregue un paso manual antes del grupo 1 en el plan de recuperación que vaya a utilizar para la prueba. A continuación, agregue los recursos de infraestructura a la red creada automáticamente antes de ejecutar la conmutación por error de prueba.

### <a name="things-to-note"></a>Puntos a tener en cuenta
Cuando se replica a un sitio secundario, no es necesario que el tipo de red que utiliza la máquina de réplica coincida con el tipo de red lógica que se usa para la conmutación por error de prueba; sin embargo, es posible que algunas combinaciones no funcionen. Si la réplica utiliza DHCP y aislamiento basado en VLAN, la red de máquina virtual para la réplica no necesita un grupo de direcciones IP estáticas. Por lo tanto, el uso de la virtualización de red de Windows para la conmutación por error de prueba no funcionaría, ya que no hay grupos de direcciones disponibles. 

Además, la conmutación por error de prueba no funcionará si la red de réplica no tiene aislamiento y la red de prueba utiliza la virtualización de red de Windows. Esto se debe a que una red sin aislamiento no tiene las subredes necesarias para crear una red de virtualización de red de Windows.

La manera en que las máquinas virtuales de réplica se conectan a las redes de máquinas virtuales asignadas después de la conmutación por error depende de cómo se configure la red de máquinas virtuales en la consola VMM.

#### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Red de máquinas virtuales configurada sin aislamiento o con aislamiento de VLAN
Si se define DHCP para la red de máquinas virtuales, la máquina virtual de réplica se conectará al identificador de VLAN mediante la configuración especificada para el sitio de red en la red lógica asociada. La máquina virtual recibe su dirección IP del servidor DHCP disponible. 

No es necesario definir un grupo de direcciones IP estáticas para la red de máquinas virtuales de destino. Si se utiliza un grupo de direcciones IP estáticas para la red de máquinas virtuales, la máquina virtual de réplica se conecta al identificador de VLAN mediante la configuración especificada para el sitio de red en la red lógica asociada.

La máquina virtual recibe su dirección IP del grupo definido para la red de máquinas virtuales. Si no se ha definido un grupo de direcciones IP estáticas en la red de máquina virtual de destino, se producirá un error en la asignación de direcciones IP. Cree el grupo de direcciones IP en los servidores VMM de origen y de destino que se vayan a usar para la protección y la recuperación.

#### <a name="vm-network-with-windows-network-virtualization"></a>Red de máquinas virtuales con virtualización de red de Windows
Si se establece una red de máquinas virtuales con virtualización de red de Windows, debe definirse un grupo estático para la red de máquinas virtuales de destino, independientemente de si la red de máquinas virtuales de origen se ha configurado para DHCP o un grupo de direcciones IP estáticas. 

Si se define DHCP, el servidor VMM de destino actúa como servidor DHCP e indica una dirección IP del grupo definido para la red de máquinas virtuales de destino. Si se define el uso de un grupo de direcciones IP estáticas para el servidor de origen, el servidor VMM de destino asigna una dirección IP del grupo. En ambos casos, se producirá un error en la asignación de direcciones IP si no se define un grupo de direcciones IP estáticas.


### <a name="prepare-dhcp"></a>Preparación de DHCP
Si las máquinas virtuales implicadas en la conmutación por error de prueba usan DHCP, cree un servidor DHCP de prueba dentro de la red aislada para la conmutación por error de prueba.

### <a name="prepare-active-directory"></a>Preparación de Active Directory
Para ejecutar una conmutación por error de prueba con el fin de probar la aplicación, necesita una copia del entorno de Active Directory de producción en el entorno de prueba. Para más información, consulte las [consideraciones sobre la conmutación por error de prueba para Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Preparación de DNS
Prepare un servidor DNS para la conmutación por error de prueba de la forma siguiente:

* **DHCP**: si las máquinas virtuales usan DHCP, debe actualizarse la dirección IP del DNS de prueba en el servidor DHCP de prueba. Si utiliza un tipo de red de virtualización de red de Windows, el servidor VMM actúa como servidor DHCP. Por lo tanto, la dirección IP de DNS debe actualizarse en la red de conmutación por error de prueba. En este caso, las máquinas virtuales se registran a sí mismas en el servidor DNS correspondiente.
* **Dirección estática**: si las máquinas virtuales utilizan una dirección IP estática, la dirección IP del servidor DNS de prueba debe actualizarse en la red de conmutación por error de prueba. Es  posible que deba actualizar el DNS con la dirección IP de las máquinas virtuales de prueba. Puede usar el siguiente script de ejemplo para este propósito:

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba
En este procedimiento se describe cómo ejecutar una conmutación por error de prueba para un plan de recuperación. También puede ejecutar la conmutación por error para una única máquina virtual en la pestaña **Máquinas virtuales**.

![Hoja de la conmutación por error de prueba](./media/site-recovery-test-failover-vmm-to-vmm/TestFailover.png)

1. Seleccione **Recovery Plans** > *nombreDePlanDeRecuperación*. Haga clic en **Conmutación por error** > **Test Conmutación por error**.
1. En la hoja **Conmutación por error de prueba**, especifique cómo se deben conectar las máquinas virtuales a las redes después de la conmutación por error de prueba. Para más información, consulte las [opciones de red](#network-options-in-site-recovery).
1. Realice el seguimiento del progreso de la conmutación por error en la pestaña **Trabajos** .
1. Cuando haya terminado la conmutación por error, compruebe que las máquinas virtuales se inician correctamente.
1. Cuando haya terminado, haga clic en **Cleanup test failover** (Limpieza de conmutación por error de prueba) en el plan de recuperación. En **Notas**, registre y guarde las observaciones asociadas a la conmutación por error de prueba. En este paso se eliminan las máquinas virtuales y las redes que se crearon durante la conmutación por error de prueba.


## <a name="network-options-in-site-recovery"></a>Opciones de red en Site Recovery

Al ejecutar una conmutación por error de prueba se le pide que seleccione la configuración de red de las máquinas de réplica de prueba. Existen varias opciones.  

| **Opción de conmutación por error de prueba** | **Descripción** | **Verificación de la conmutación por error** | **Detalles** |
| --- | --- | --- | --- |
| **Fail over to a secondary VMM site--without network** (Conmutación por error a un sitio secundario de VMM, sin red) |No seleccione una red de máquinas virtuales. |La conmutación por error comprueba que se crean las máquinas de prueba.<br/><br/>La máquina virtual de prueba se crea en el host donde existe la máquina virtual de réplica. No se agrega a la nube en la que se encuentra la máquina virtual de réplica. |<p>La máquina que ha conmutado por error no se conecta a ninguna red.<br/><br/>La máquina puede conectarse a una red de máquinas virtuales una vez creada. |
| **Fail over to a secondary VMM site--with network** (Conmutación por error a un sitio secundario de VMM, con red) |Seleccione una red de máquinas virtuales existente. |La conmutación por error comprueba que se crean las máquinas virtuales. |La máquina virtual de prueba se crea en el host donde existe la máquina virtual de réplica. No se agrega a la nube en la que se encuentra la máquina virtual de réplica.<br/><br/>Cree una red de máquinas virtuales aislada de la red de producción.<br/><br/>Si utiliza una red basada en VLAN, se recomienda crear una red lógica independiente en VMM para este propósito (no se utiliza en producción). Esta red lógica se utiliza para crear redes de máquinas virtuales para la conmutación por error de prueba.<br/><br/>La red lógica debe asociarse con al menos uno de los adaptadores de red de todos los servidores de Hyper-V que hospeden máquinas virtuales.<br/><br/>En el caso de las redes lógicas de VLAN, deben aislarse los sitios de red que agregue a la red lógica.<br/><br/>Si utiliza una red lógica basada en virtualización de red de Windows, Azure Site Recovery crea automáticamente redes de máquinas virtuales aisladas. |
| **Fail over to a secondary VMM site--create a network** (Conmutación por error a un sitio secundario de VMM, crear red) |Se crea automáticamente una red de prueba temporal en función de la configuración especificada en **Red lógica** y de sus sitios de red asociados. |La conmutación por error comprueba que se crean las máquinas virtuales. |Utilice esta opción si el plan de recuperación usa más de una red de VM. Si utiliza redes de virtualización de red de Windows, esta opción permite crear automáticamente redes de máquinas virtuales con la misma configuración (subredes y grupos de direcciones IP) en la red de la máquina virtual de réplica. Estas redes de máquinas virtuales se limpian automáticamente una vez completada la conmutación por error de prueba.</p><p>La máquina virtual de prueba se crea en el host donde existe la máquina virtual de réplica. No se agrega a la nube en la que se encuentra la máquina virtual de réplica. |

> [!TIP]
> La dirección IP que se asigna a una máquina virtual durante la conmutación por error de prueba es la misma que obtendría la máquina virtual al realizar una conmutación por error planeada o no planeada (siempre que la dirección IP esté disponible en la red de conmutación por error de prueba). Si la misma dirección IP no está disponible en la red de conmutación por error de prueba, la máquina virtual recibe otra dirección IP que esté disponible en la red de conmutación por error de prueba.
>
>


## <a name="test-failover-to-a-production-network-on-a-recovery-site"></a>Conmutación por error de prueba a una red de producción en un sitio de recuperación
Se recomienda que, al realizar una conmutación por error de prueba, elija una red diferente de la del sitio de recuperación de producción que proporcionara en la [asignación de red](https://docs.microsoft.com/azure/site-recovery/site-recovery-network-mapping). Sin embargo, si realmente desea validar la conectividad de un extremo a otro de la red en una máquina virtual que se ha conmutado por error, tenga en cuenta lo siguiente:

* Asegúrese de que la máquina virtual principal está apagada al realizar la conmutación por error de prueba. Si no lo hace, dos máquinas virtuales con la misma identidad se ejecutarán en la misma red al mismo tiempo. Esta situación puede provocar consecuencias no deseadas.
* Al limpiar las máquinas virtuales de conmutación por error de prueba, se perderán los cambios realizados en las máquinas virtuales de conmutación por error de prueba. Estos cambios no se replican en la máquina virtual principal.
* Esta forma de realizar pruebas provoca un tiempo de inactividad de la aplicación de producción. Solicite a los usuarios de la aplicación que no la usen con la exploración de la recuperación ante desastres en curso.  


## <a name="next-steps"></a>Pasos siguientes
Después de ejecutar correctamente una conmutación por error de prueba, puede intentar una [conmutación por error](site-recovery-failover.md).
