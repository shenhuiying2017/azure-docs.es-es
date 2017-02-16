---
title: "Conmutación por error de prueba a Azure en Site Recovery | Microsoft Docs"
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
ms.sourcegitcommit: f430d81889ecf7f3210358556b99d7d88b0e7a76
ms.openlocfilehash: 8d848385863aeea43dbad3991e7776e399ac3994


---
# <a name="test--failover-to-azure-in-site-recovery"></a>Conmutación por error de prueba a Azure en Site Recovery

En este artículo se proporciona información e instrucciones para realizar una conmutación por error de prueba o una exploración de recuperación ante desastres de máquinas virtuales y servidores físicos que están protegidos con Site Recovery, utilizando para ello Azure como sitio de recuperación. 

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

La conmutación por error de prueba se ejecuta para validar la estrategia de replicación o para realizar una exploración de la recuperación ante desastres sin perder datos ni tiempo de actividad. Realizar una conmutación por error de prueba no afecta a la replicación en curso ni al entorno de producción. La conmutación por error de prueba puede hacerse en una máquina virtual o en un [plan de recuperación](site-recovery-create-recovery-plans.md). Al activar una conmutación por error de prueba, debe especificar la red a la que deben conectarse las máquinas virtuales de prueba. Una vez desencadenada la conmutación por error de prueba, puede seguir su progreso en la página **Trabajos**.  


## <a name="supported-scenarios"></a>Escenarios admitidos
La conmutación por error se admite en todos los escenarios de implementación que no sean desde [sitios de VMware heredados a Azure](site-recovery-vmware-to-azure-classic-legacy.md). La conmutación por error de prueba tampoco se admite si la máquina virtual ha conmutado por error a Azure.  


## <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba
En este procedimiento se describe cómo ejecutar una conmutación por error de prueba para un plan de recuperación. También puede ejecutar una conmutación por error de prueba para una sola máquina usando la opción adecuada en ella. 

1. Seleccione **Recovery Plans** > *nombreDePlanDeRecuperación*. Haga clic en **Probar conmutación por error**.
1. Seleccione el **Punto de recuperación** al que se va a realizar la conmutación por error. Puede seleccionar una de las siguientes opciones:
    1.  **Latest processed** (Procesar más reciente): esta opción conmuta por error todas las máquinas virtuales del plan de recuperación al punto de recuperación más reciente que el servicio Site Recovery haya procesado. Cuando se realiza una conmutación por error de prueba de una máquina virtual, también se muestra la marca de tiempo del último punto de recuperación procesado. Si realiza una conmutación por error de un plan de recuperación, puede ir a cada máquina virtual y consultar el icono **Latest Recovery Points** (Puntos de recuperación más recientes) para obtener esta información. Como no se emplea ningún tiempo en procesar los datos sin procesar, esta es una opción de conmutación por error con un objetivo de tiempo de recuperación bajo. 
    1.    **Latest app-consistent** (Más reciente coherente con la aplicación): esta opción conmuta por error todas las máquinas virtuales del plan de recuperación al último punto de recuperación coherente con la aplicación que haya sido procesado por el servicio Site Recovery. Cuando se realiza una conmutación por error de prueba de una máquina virtual, también se muestra la marca de tiempo del último punto de recuperación coherente con la aplicación procesado. Si realiza una conmutación por error de un plan de recuperación, puede ir a cada máquina virtual y consultar el icono **Latest Recovery Points** (Puntos de recuperación más recientes) para obtener esta información. 
    1.    **Última**: esta opción procesa primero todos los datos que se han enviado al servicio Site Recovery para crear un punto de recuperación para cada máquina virtual antes de conmutarla por error a dicho punto de recuperación. Esta opción ofrece un objetivo de punto de recuperación menor porque la máquina virtual creada después de la conmutación por error tendrá todos los datos que se han replicado en el servicio Site Recovery cuando se activó la conmutación por error. 
    1.  **Custom** (Personalizado): si está realizando una conmutación por error de prueba de una máquina virtual, puede usar esta opción para la conmutación por error a un punto de recuperación determinado.
1. Seleccione una red virtual de Azure en **Azure virtual network**: indique la red virtual de Azure donde se deberían crear las máquinas virtuales de prueba. Site Recovery intenta crear máquinas virtuales de prueba en una subred del mismo nombre y con la misma dirección IP que se proporcionó en la opción **Proceso y red** de la máquina virtual. Si no hay disponible una subred del mismo nombre en la red virtual de Azure proporcionada para la conmutación por error, la máquina virtual de prueba se crea en la primera subred por orden alfabético. Si la misma dirección IP no está disponible en la subred, la máquina virtual recibirá otra dirección IP que haya disponible en la subred. Consulte esta sección para obtener [más información](#creating-a-network-for-test-failover).
1. Si realiza la conmutación por error a Azure y el cifrado de datos está habilitado, en **Clave de cifrado** seleccione el certificado que se emitió cuando habilitó el cifrado de datos durante la instalación del proveedor. Puede omitir este paso si no se ha habilitado el cifrado en la máquina virtual.
1. Realice el seguimiento del progreso de la conmutación por error en la pestaña **Trabajos** . Debe poder ver la máquina de réplica de prueba en el Portal de Azure.
1. Para iniciar una conexión RDP en la máquina virtual debe [agregar una dirección IP pública](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) en la interfaz de red de la máquina virtual a la que se conmutó por error. Si conmuta por error a una máquina virtual en modo clásico, deberá [agregar un punto de conexión](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md) en el puerto 3389.
1. Una vez que haya terminado, haga clic en **Cleanup test failover** (Limpieza de conmutación por error de prueba) en el plan de recuperación. En **Notas** , registre y guarde las observaciones asociadas a la conmutación por error de prueba. Así se eliminarán las máquinas virtuales que se crearon durante la conmutación por error de prueba. 


> [!TIP]
> Site Recovery intenta crear máquinas virtuales de prueba en una subred del mismo nombre y con la misma dirección IP que se proporcionó en la opción **Proceso y red** de la máquina virtual. Si no hay disponible una subred del mismo nombre en la red virtual de Azure proporcionada para la conmutación por error, la máquina virtual de prueba se crea en la primera subred por orden alfabético. Si la misma dirección IP no está disponible en la subred, la máquina virtual recibirá otra dirección IP que haya disponible en la subred. 
>
> 


## <a name="creating-a-network-for-test-failover"></a>Creación de una red para conmutación por error de prueba 
Se recomienda que, cuando realice una conmutación por error de prueba, elija una red que esté aislada de la red del sitio de recuperación de producción que proporcionó en la configuración **Proceso y red** de la máquina virtual. De forma predeterminada, cualquier red virtual que se cree en Azure está aislada de otras redes. Esta red debe imitar la red de producción:

1. La red de prueba debe tener el mismo número de subredes que la red de producción y con el mismo nombre que las subredes de la red de producción.
1. La red de prueba debe usar el mismo intervalo de IP que la red de producción.
1. Actualice el DNS de la red de prueba con la dirección IP que asignó como IP de destino para la máquina virtual de DNS en la configuración de **Proceso y red**. Examine la sección [Consideraciones sobre la conmutación por error de prueba para Active Directory](site-recovery-active-directory.md#test-failover-considerations) para obtener más información. 


## <a name="test-failover-to-a-production-network-on-recovery-site"></a>Conmutación por error de prueba a una red de producción en el sitio de recuperación 
Se recomienda que, cuando realice una conmutación por error de prueba, elija una red diferente de la red del sitio de recuperación de producción que proporcionó en la configuración **Proceso y red** de la máquina virtual. Sin embargo, si realmente desea validar la conectividad de un extremo a otro de la red en una máquina virtual a la que se conmuta por error, tenga en cuenta lo siguiente:

1. Asegúrese de que la máquina virtual principal está apagada cuando se realice la conmutación por error de prueba. Si no lo está, habrá dos máquinas virtuales con la misma identidad en ejecución en la misma red y al mismo tiempo, lo que puede tener consecuencias no deseadas. 
1. Se perderán los cambios realizados en las máquinas virtuales de conmutación por error de prueba al limpiar las máquinas virtuales de conmutación por error de prueba. Estos cambios no se replicarán de nuevo a la máquina virtual principal.
1. Esta forma de realizar pruebas provoca un tiempo de inactividad de la aplicación de producción. Se deben pedir a los usuarios de la aplicación que no usen la aplicación si la exploración de la recuperación ante desastres está en curso.  



## <a name="prepare-active-directory-and-dns"></a>Preparación de Active Directory y DNS
Para ejecutar una conmutación por error de prueba con el fin de probar la aplicación, necesitará una copia del entorno de Active Directory de producción en el entorno de prueba. Examine la sección [Consideraciones sobre la conmutación por error de prueba para Active Directory](site-recovery-active-directory.md#test-failover-considerations) para obtener más información. 

## <a name="next-steps"></a>Pasos siguientes
Después de ejecutar correctamente una conmutación por error de prueba, puede intentar realizar una [conmutación por error](site-recovery-failover.md).




<!--HONumber=Jan17_HO4-->


