---
title: "Conmutación por error en Site Recovery | Microsoft Docs"
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
ms.date: 07/04/2017
ms.author: pratshar
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: ef586191f0b89dca89810644d45503fe42538635
ms.contentlocale: es-es
ms.lasthandoff: 07/08/2017


---
# <a name="failover-in-site-recovery"></a>Conmutación por error en Site Recovery
En este artículo se describe cómo se realiza la conmutación por error en máquinas virtuales y en servidores físicos protegidos con Site Recovery.

## <a name="prerequisites"></a>Requisitos previos
1. Antes de realizar una conmutación por error, realice una [conmutación por error de prueba](site-recovery-test-failover-to-azure.md) para asegurarse de que todo funciona según lo previsto.
1. [Prepare la red](site-recovery-network-design.md) en la ubicación de destino antes de realizar una conmutación por error.  


## <a name="run-a-failover"></a>Ejecución de la conmutación por error
En este procedimiento se describe cómo ejecutar una conmutación por error para un [plan de recuperación](site-recovery-create-recovery-plans.md). También puede ejecutar la conmutación por error para una única máquina virtual o un único servidor físico desde la pestaña **Elementos replicados**.


![Conmutación por error](./media/site-recovery-failover/Failover.png)

1. Seleccione **Recovery Plans** > *nombreDePlanDeRecuperación*. Haga clic en **Conmutación por error**.
2. En la pantalla **Conmutación por error**, seleccione el **Punto de recuperación** al que se va a realizar la conmutación por error. Puede seleccionar una de las siguientes opciones:
    1.  **Última** (valor predeterminado): esta opción procesa primero todos los datos que se han enviado al servicio Site Recovery para crear un punto de recuperación para cada máquina virtual antes de conmutarla por error a dicho punto de recuperación. Esta opción ofrece el objetivo de punto de recuperación (RPO) mínimo, ya que la máquina virtual creada después de la conmutación por error tiene todos los datos que se han replicado en el servicio Site Recovery cuando se desencadenó la conmutación por error.
    1.  **Latest processed** (Último procesado): esta opción conmuta por error todas las máquinas virtuales del plan de recuperación al punto de recuperación más reciente que el servicio Site Recovery haya procesado. Cuando se realiza una conmutación por error de prueba de una máquina virtual, también se muestra la marca de tiempo del último punto de recuperación procesado. Si realiza una conmutación por error de un plan de recuperación, puede ir a cada máquina virtual y consultar el icono **Latest Recovery Points** (Puntos de recuperación más recientes) para obtener esta información. Como no se emplea ningún tiempo en procesar los datos sin procesar, esta es una opción de conmutación por error con un objetivo de tiempo de recuperación bajo.
    1.  **Latest app-consistent** (Más reciente coherente con la aplicación): esta opción conmuta por error todas las máquinas virtuales del plan de recuperación al último punto de recuperación coherente con la aplicación que haya procesado el servicio Site Recovery. Cuando se realiza una conmutación por error de prueba de una máquina virtual, también se muestra la marca de tiempo del último punto de recuperación coherente con la aplicación procesado. Si realiza una conmutación por error de un plan de recuperación, puede ir a cada máquina virtual y consultar el icono **Latest Recovery Points** (Puntos de recuperación más recientes) para obtener esta información.
    1.  **Latest multi-VM processed** (Últimas máquinas virtuales procesadas): esta opción solo está disponible para planes de recuperación que tienen al menos una máquina virtual con la coherencia para varias máquinas virtuales activada. Las máquinas virtuales que forman parte de un grupo de replicación conmutan por error al último punto de recuperación común coherente con varias máquinas virtuales. Otras máquinas virtuales conmutan por error a su punto de recuperación procesado más reciente.  
    1.  **Latest multi-VM app-consistent** (Últimas máquinas virtuales coherentes con la aplicación): esta opción solo está disponible para planes de recuperación que tienen al menos una máquina virtual con la coherencia para varias máquinas virtuales activada. Las máquinas virtuales que forman parte de un grupo de replicación conmutan por error al último punto de recuperación común coherente con la aplicación de varias máquinas virtuales. Otras máquinas virtuales conmutan por error a su punto de recuperación coherente con la aplicación más reciente.
    1.  **Custom** (Personalizado): si realiza una conmutación por error de prueba con una máquina virtual, puede usar esta opción para conmutar por error a un punto de recuperación determinado.

    > [!NOTE]
    > La opción de elegir un punto de recuperación solo está disponible cuando se conmuta por error a Azure.
    >
    >


1. Si algunas de las máquinas virtuales del plan de recuperación se han conmutado por error en una ejecución anterior y ahora están activas en la ubicación de origen y en la de destino, puede usar la opción **Cambiar dirección** para decidir la dirección en la que debe producirse la conmutación por error.
1. Si realiza la conmutación por error a Azure y en la nube está habilitado el cifrado de datos (aplicable únicamente con máquinas virtuales de Hyper-V desde un servidor de VMM), en **Clave de cifrado**, seleccione el certificado que se emitió al habilitarlo durante la instalación del proveedor en el servidor de VMM.
1. Seleccione **Shut down machine before beginning failover** (Apagar la máquina antes de comenzar la conmutación por error) si desea que Site Recovery intente apagar las máquinas virtuales de origen antes de desencadenar la conmutación por error. La conmutación por error continúa aunque se produzca un error de cierre.  

    > [!NOTE]
    > En el caso de máquinas virtuales de Hyper-V, esta opción también intenta sincronizar los datos locales que aún no se hayan enviado al servicio antes de desencadenar la conmutación por error.
    >
    >

1. Puede seguir el progreso de la conmutación por error en la página **Trabajos**. Aunque se produzcan errores durante una conmutación por error no planeada, el plan de recuperación se ejecuta hasta que se completa.
1. Después de la conmutación por error, valide la máquina virtual iniciando sesión en ella. Si desea volver a otro punto de recuperación para la máquina virtual, puede usar la opción **Cambiar punto de recuperación**.
1. Realice la acción **Confirmar** una vez que quede satisfecho con la conmutación por error de la máquina virtual. Esto elimina todos los puntos de recuperación disponibles con el servicio y la opción **Cambiar punto de recuperación** dejará de estar disponible.

## <a name="planned-failover"></a>Conmutación por error planeada
Además de la conmutación por error, las máquinas virtuales de Hyper-V protegidas con Site Recovery también son compatibles con la **Conmutación por error planeada**. Se trata de una opción de conmutación por error sin pérdida de datos. Cuando se desencadena una conmutación por error planeada, primero se apagan las máquinas virtuales de origen, después se sincronizan los datos que correspondan y, por último, se desencadena la conmutación por error.

> [!NOTE]
> Cuando se conmutan por error máquinas virtuales de Hyper-V un sitio local a otro, para regresar al sitio principal local, primero debe realizar una **replicación inversa** de la máquina virtual al sitio principal y, a continuación, desencadenar la conmutación por error. Si la máquina virtual principal no está disponible, antes de iniciar la **replicación inversa** tiene que restaurar la máquina virtual desde una copia de seguridad.   
>
>

## <a name="failover-job"></a>Trabajo de conmutación por error

![Conmutación por error](./media/site-recovery-failover/FailoverJob.png)

Cuando se desencadena una conmutación por error, se realizan estos pasos:

1. Comprobación de los requisitos previos: en este paso se garantiza que se cumplen todas las condiciones necesarias para la conmutación por error.
1. Conmutación por error: en este paso se procesan los datos y se preparan para que se pueda crear una máquina virtual de Azure a partir de ellos. Si ha elegido el punto de recuperación **más reciente**, en este paso se crea un punto de recuperación de los datos enviados al servicio.
1. Inicio: en este paso se crea una máquina virtual de Azure con los datos procesados en el paso anterior.

> [!WARNING]
> **No cancele una conmutación por error en curso**: antes de iniciar la conmutación por error, se detiene la replicación de la máquina virtual. Si se **cancela** un trabajo en curso, la conmutación por error se detiene, pero la máquina virtual no comenzará la replicación. La replicación no se puede reiniciar.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Tiempo necesario para la conmutación por error en Azure

En algunos casos, la conmutación por error de máquinas virtuales requiere un paso intermedio adicional que normalmente tarda aproximadamente de 8 a 10 minutos en completarse. Estos casos son los siguientes:

* Máquinas virtuales de VMware con el servicio de movilidad de una versión anterior a 9.8
* Servidores físicos 
* Máquinas virtuales de VMware Linux
* Máquinas virtuales Hyper-V protegidas como servidores físicos
* Máquinas virtuales de VMware donde los siguientes controladores no están presentes como controladores de arranque 
    * storvsc 
    * vmbus 
    * storflt 
    * intelide 
    * atapi
* Máquinas virtuales de VMware que no tienen el servicio DHCP habilitado independientemente de si usan direcciones IP estáticas o DHCP

En todos los demás casos, no es necesario este paso intermedio y el tiempo necesario para la conmutación por error es significativamente menor. 





## <a name="using-scripts-in-failover"></a>Uso de scripts en la conmutación por error
Es posible que desee automatizar determinadas acciones de la conmutación por error. Para ello, puede utilizar scripts o [runbooks de Azure Automation](site-recovery-runbook-automation.md) en [planes de recuperación](site-recovery-create-recovery-plans.md).

## <a name="other-considerations"></a>Otras consideraciones
* **Unidad**: para conservar la unidad en las máquinas virtuales después de la conmutación por error, puede establecer la **directiva SAN** para la máquina virtual en **OnlineAll**. [Más información](https://support.microsoft.com/en-us/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).



## <a name="next-steps"></a>Pasos siguientes
Una vez que finalizada la conmutación por error de las máquinas virtuales y cuando el centro de datos local esté disponible, debe [**volver a proteger**](site-recovery-how-to-reprotect.md) las máquinas virtuales de VMware en el centro de datos local.

Use la opción de[**conmutación por error planeada**](site-recovery-failback-from-azure-to-hyper-v.md) para la **conmutación por recuperación** de máquinas virtuales de Hyper-V hacia el entorno local desde Azure.

Si se ha producido una conmutación por error de una máquina virtual de Hyper-V a otro centro de datos local administrado por un servidor de VMM y el centro de datos principal está disponible, use la **replicación inversa** para iniciar la replicación hacia el centro de datos principal.

