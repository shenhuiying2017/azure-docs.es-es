---
title: "Conmutación por error de prueba (VMM a VMM) en Site Recovery (Azure) | Microsoft Docs"
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
ms.date: 1/09/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: 1c3a3a3e9381e20382bb537299b04993180ee729
ms.openlocfilehash: 465da7ae1aa3a31400706c28d0516507ddf66ff0


---
# <a name="test-failover-vmm-to-vmm-in-site-recovery"></a>Conmutación por error de prueba (VMM a VMM) en Site Recovery

En este artículo, se proporcionan información e instrucciones para realizar una conmutación por error de prueba o una exploración de recuperación ante desastres de máquinas virtuales y servidores físicos que están protegidos con Site Recovery, utilizando para ello un sitio local administrado con VMM como sitio de recuperación. 

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

La conmutación por error de prueba se ejecuta para validar la estrategia de replicación o para realizar una exploración de la recuperación ante desastres sin perder datos ni tiempo de actividad. Realizar una conmutación por error de prueba no afecta a la replicación en curso ni al entorno de producción. La conmutación por error de prueba puede hacerse en una máquina virtual o en un [plan de recuperación](site-recovery-create-recovery-plans.md). Al activar una conmutación por error de prueba, debe especificar la red a la que deben conectarse las máquinas virtuales de prueba. Una vez desencadenada la conmutación por error de prueba, puede seguir su progreso en la página **Trabajos**.  


## <a name="preparing-infrastructure-for-test-failover"></a>Preparación de la infraestructura para la conmutación por error de prueba
* Si desea ejecutar una conmutación por error de prueba con una red existente, prepare Active Directory, DHCP y DNS en esa red.
* Si desea ejecutar una conmutación por error de prueba con la opción de creación automática de redes de máquina virtual, agregue un paso manual antes de Grupo-1 en el plan de recuperación que va a usar para la conmutación por error de prueba y, a continuación, agregue los recursos de infraestructura a la red creada automáticamente antes de ejecutar la conmutación por error de prueba.

### <a name="things-to-note"></a>Puntos a tener en cuenta
* Cuando se replica a un sitio secundario, no es necesario que el tipo de red que utiliza la máquina de réplica coincida con el tipo de red lógica que se usa para la conmutación por error de prueba; sin embargo, es posible que algunas combinaciones no funcionen. Si la réplica utiliza DHCP y aislamiento basado en VLAN, la red de máquina virtual para la réplica no necesita un grupo de direcciones IP estáticas. Por lo tanto, el uso de virtualización de red de Windows para la conmutación por error de prueba no funcionaría porque no hay grupos de direcciones disponibles. Además, la prueba de conmutación por error no funcionará si la red de réplica está configurada en Sin aislamiento y la red de prueba es de virtualización de red de Windows. Esto se debe a que una red sin aislamiento no tiene las subredes necesarias para crear una red de virtualización de red de Windows.
* La manera en que las máquinas virtuales de réplica se conectan a las redes de máquinas virtuales asignadas después de la conmutación por error depende de cómo se configura la red de máquina virtual en la consola VMM:
  * **Red de máquina virtual configurada sin aislamiento o con aislamiento de VLAN**: si se ha definido DHCP para la red de máquina virtual, la máquina virtual de réplica se conectará al identificador de VLAN utilizando la configuración especificada para el sitio de red en la red lógica asociada. La máquina virtual recibirá su dirección IP del servidor DHCP disponible. No es necesario definir un grupo de direcciones IP estáticas para la red de máquina virtual de destino. Si se utiliza un grupo de direcciones IP estáticas para la red de máquina virtual, la máquina virtual de réplica se conectará al identificador de VLAN mediante la configuración especificada para el sitio de red en la red lógica asociada. La máquina virtual recibirá su dirección IP del grupo definido para la red de máquina virtual. Si no se ha definido un grupo de direcciones IP estáticas en la red de máquina virtual de destino, se producirá un error en la asignación de direcciones IP. El grupo de direcciones IP debe crearse en los servidores VMM de origen y de destino que se van a usar para la protección y la recuperación.
  * **Red de máquina virtual con virtualización de red de Windows**: si se establece una red de máquina virtual con esta configuración, debe definirse un grupo estático para la red de máquina virtual de destino, independientemente de si la red de máquina virtual de origen se ha configurado para utilizar DHCP o un grupo de direcciones IP estáticas. Si define DHCP, el servidor VMM de destino actuará como servidor DHCP e indicará una dirección IP del grupo definido para la red de máquina virtual de destino. Si se define el uso de un grupo de direcciones IP estáticas para el servidor de origen, el servidor VMM de destino asignará una dirección IP del grupo. En ambos casos, se producirá un error en la asignación de direcciones IP si no se define un grupo de direcciones IP estáticas.


### <a name="prepare-dhcp"></a>Preparación de DHCP
Si las máquinas virtuales implicadas en la conmutación por error de prueba usan DHCP, se debe crear un servidor DHCP de prueba dentro de la red aislada que se crea para la conmutación por error de prueba.

### <a name="prepare-active-directory"></a>Preparación de Active Directory
Para ejecutar una conmutación por error de prueba con el fin de probar la aplicación, necesitará una copia del entorno de Active Directory de producción en el entorno de prueba. Examine la sección [Consideraciones sobre la conmutación por error de prueba para Active Directory](site-recovery-active-directory.md#test-failover-considerations) para obtener más información. 

### <a name="prepare-dns"></a>Preparación de DNS
Prepare un servidor DNS para la conmutación por error de prueba de la forma siguiente:

* **DHCP**: si las máquinas virtuales usan DHCP, debe actualizarse la dirección IP del DNS de prueba en el servidor DHCP de prueba. Si utiliza un tipo de red de virtualización de red de Windows, el servidor VMM actúa como servidor DHCP. Por lo tanto, la dirección IP de DNS debe actualizarse en la red de conmutación por error de prueba. En este caso, las máquinas virtuales se registrarán a sí mismas en el servidor DNS correspondiente.
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
En este procedimiento se describe cómo ejecutar una conmutación por error de prueba para un plan de recuperación. También puede ejecutar la conmutación por error para una única máquina virtual o un único servidor físico en la pestaña **Máquinas virtuales** .

1. Seleccione **Recovery Plans** > *nombreDePlanDeRecuperación*. Haga clic en **Conmutación por error** > **Test Conmutación por error**.
1. En la hoja **Conmutación por error de prueba**, especifique cómo se deben conectar las máquinas virtuales a las redes después de la conmutación por error de prueba. Consulte las [opciones de red](#network-options-in-site-recovery) para más información.
1. Realice el seguimiento del progreso de la conmutación por error en la pestaña **Trabajos** . 
1. Cuando haya terminado, compruebe que las máquinas virtuales se inician correctamente.
1. Una vez que haya terminado, haga clic en **Cleanup test failover** (Limpieza de conmutación por error de prueba) en el plan de recuperación. En **Notas** , registre y guarde las observaciones asociadas a la conmutación por error de prueba. Así se eliminarán las máquinas virtuales y las redes que se crearon durante la conmutación por error de prueba. 


## <a name="network-options-in-site-recovery"></a>Opciones de red en Site Recovery

Al ejecutar una prueba de conmutación por error se le pedirá que seleccione la configuración de red de las máquinas de réplica de prueba. Tiene varias opciones.  

| **Opción de conmutación por error de prueba** | **Descripción** | **Verificación de la conmutación por error** | **Detalles** |
| --- | --- | --- | --- |
| **Conmutación por error a un sitio secundario de VMM, sin red** |No seleccione una red de máquina virtual. |La conmutación por error comprueba que se crean las máquinas de prueba.<br/><br/>La máquina virtual de prueba se creará en el mismo host en el que existe la máquina virtual de réplica. No se agregará a la nube en la que se encuentra la máquina virtual de réplica. |<p>El equipo que ha conmutado por error no se conectará a ninguna red.<br/><br/>La máquina puede conectarse a una red de máquina virtual una vez creada. |
| **Conmutación por error a un sitio secundario de VMM, con red** |Seleccione una red de máquina virtual existente. |La conmutación por error comprueba que se crean las máquinas virtuales. |La máquina virtual de prueba se creará en el mismo host en el que existe la máquina virtual de réplica. No se agregará a la nube en la que se encuentra la máquina virtual de réplica.<br/><br/>Creación de una red de máquina virtual que esté aislada de la red de producción<br/><br/>Si utiliza una red basada en VLAN, se recomienda crear una red lógica independiente (no se utiliza en producción) en VMM para este propósito. Esta red lógica se utiliza para crear redes de máquinas virtuales con el fin de probar la conmutación por error.<br/><br/>La red lógica debe asociarse con al menos uno de los adaptadores de red de todos los servidores de Hyper-V que hospedan máquinas virtuales.<br/><br/>En el caso de las redes lógicas de VLAN, los sitios de red que agregue a la red lógica deben aislarse.<br/><br/>Si utiliza una red lógica basada en virtualización de red de Windows, Azure Site Recovery crea automáticamente redes de máquinas virtuales aisladas. |
| **Conmutación por error a un sitio secundario de VMM, crear red** |Se creará automáticamente una red de prueba temporal en función de la configuración especificada en **Red lógica** y de sus sitios de red relacionados. |La conmutación por error comprueba que se crean las máquinas virtuales. |Utilice esta opción si el plan de recuperación usa más de una red de VM. Si utiliza redes de virtualización de red de Windows, esta opción permite crear automáticamente redes de máquinas virtuales con la misma configuración (subredes y grupos de direcciones IP) en la red de la máquina virtual de réplica. Estas redes de máquinas virtuales se limpian automáticamente una vez completada la conmutación por error de prueba.</p><p>La máquina virtual de prueba se creará en el mismo host en el que existe la máquina virtual de réplica. No se agregará a la nube en la que se encuentra la máquina virtual de réplica. |

> [!TIP]
> La dirección IP que se asigna a una máquina virtual durante la conmutación por error de prueba es la misma dirección IP que obtendría al realizar una conmutación por error planeada o no planeada (siempre que la dirección IP esté disponible en la red de conmutación por error de prueba). Si la misma dirección IP no está disponible en la red de conmutación por error de prueba, la máquina virtual obtendrá otra dirección IP disponible en la red de conmutación por error de prueba.
>
>


## <a name="test-failover-to-a-production-network-on-recovery-site"></a>Conmutación por error de prueba a una red de producción en el sitio de recuperación 
Se recomienda que, cuando realice una conmutación por error de prueba, elija una red diferente de la red del sitio de recuperación de producción que proporcionó en la **asignación de red**. Sin embargo, si realmente desea validar la conectividad de un extremo a otro de la red en una máquina virtual a la que se conmuta por error, tenga en cuenta lo siguiente:

1. Asegúrese de que la máquina virtual principal está apagada cuando se realice la conmutación por error de prueba. Si no lo está, habrá dos máquinas virtuales con la misma identidad en ejecución en la misma red y al mismo tiempo, lo que puede tener consecuencias no deseadas. 
1. Se perderán los cambios realizados en las máquinas virtuales de conmutación por error de prueba al limpiar las máquinas virtuales de conmutación por error de prueba. Estos cambios no se replicarán de nuevo a la máquina virtual principal.
1. Esta forma de realizar pruebas provoca un tiempo de inactividad de la aplicación de producción. Se deben pedir a los usuarios de la aplicación que no usen la aplicación si la exploración de la recuperación ante desastres está en curso.  


## <a name="next-steps"></a>Pasos siguientes
Después de ejecutar correctamente una conmutación por error de prueba, puede intentar realizar una [conmutación por error](site-recovery-failover.md).



<!--HONumber=Jan17_HO4-->


