---
title: "Replicar una implementación de XenDesktop y XenApp de Citrix de niveles múltiples mediante Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo proteger y recuperar implementaciones de XenDesktop y XenApp de Citrix con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: ponatara
ms.openlocfilehash: 52b123b598226e7b03ea9a31c40dd192fd76b191
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="replicate-a-multi-tier-citrix-xenapp-and-xendesktop-deployment-using-azure-site-recovery"></a>Replicar una implementación de XenApp y XenDesktop de Citrix de niveles múltiples mediante Azure Site Recovery

## <a name="overview"></a>Información general

XenDesktop de Citrix es una solución de virtualización de escritorio que ofrece aplicaciones y escritorios como servicio a petición a cualquier usuario y en cualquier lugar. Gracias a la tecnología de entrega FlexCast, XenDesktop puede entregar de forma rápida y segura aplicaciones y escritorios a los usuarios.
Actualmente, XenApp de Citrix no proporciona funciones de recuperación ante desastres.

Una buena solución de recuperación ante desastres debería permitir que se pudieran desarrollar planes de recuperación para este tipo de arquitecturas de aplicaciones complejas que se describieron anteriormente. También deberían brindar la posibilidad de agregar pasos personalizados para administrar las asignaciones de la aplicación entre diferentes niveles, así como proporcionar una solución confiable de un solo clic en caso de que un desastre produjera un descenso del RTO.

Este documento proporciona instrucciones detalladas para crear una solución de recuperación ante desastres para las implementaciones locales de XenApp de Citrix en plataformas vSphere de Hyper-V y VMware. Además, se describe cómo realizar una conmutación por error de prueba (obtención de detalles de recuperación ante desastres) y una conmutación por error no planeada en Azure con los planes de recuperación, las configuraciones admitidas y los requisitos previos.


## <a name="prerequisites"></a>Requisitos previos

Antes de empezar, no olvide informarse sobre las cuestione siguientes:

1. [Replicación de una máquina virtual en Azure](site-recovery-vmware-to-azure.md)
1. [Diseño de una red de recuperación](site-recovery-network-design.md)
1. [Realización de una conmutación por error de prueba en Azure](site-recovery-test-failover-to-azure.md)
1. [Ejecución de una conmutación por error en Azure](site-recovery-failover.md)
1. [Replicación de un controlador de dominio](site-recovery-active-directory.md)
1. [Replicación de SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Modelos de implementación

Una granja de XenApp y XenDesktop de Citrix suele tener el siguiente patrón de implementación:

**Patrón de implementación**

Implementación de XenApp y XenDesktop de Citrix con servidor DNS de AD, servidor de SQL Database, controlador de entrega de Citrix, servidor de StoreFront, XenApp Master (VDA) y servidor de licencias de XenApp de Citrix

![Modelo de implementación 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Compatibilidad de Site Recovery

Para este artículo, se han usado implementaciones de Citrix en máquinas virtuales de VMware administradas por vSphere 6.0 / System Center VMM 2012 R2 para la configuración de la recuperación ante desastres.

### <a name="source-and-target"></a>Origen y destino

**Escenario** | **En un sitio secundario** | **En Azure**
--- | --- | ---
**Hyper-V** | Fuera del ámbito | Sí
**VMware** | Fuera del ámbito | Sí
**Servidor físico** | Fuera del ámbito | Sí

### <a name="versions"></a>Versiones
Los clientes pueden implementar componentes de XenApp como máquinas virtuales que se ejecutan en Hyper-V o VMware o como servidores físicos. Azure Site Recovery puede proteger las implementaciones físicas y virtuales en Azure.
Dado que en Azure se admite XenApp 7.7 o una versión posterior, solo se pueden conmutar por error a Azure las implementaciones con estas versiones para la migración o la recuperación ante desastres.

### <a name="things-to-keep-in-mind"></a>Aspectos que debe tener en cuenta

1. No se admite la protección y la recuperación de implementaciones locales mediante equipos con sistema operativo de servidor para entregar aplicaciones y escritorios de XenApp publicados.

2. No se admite la protección y la recuperación de implementaciones locales mediante equipos con sistema operativo de escritorio para entregar VDI para escritorios virtuales de cliente, incluido Windows 10. Esto se debe a que ASR no admite la recuperación de equipos con sistemas operativos de escritorio.  Además, todavía no se admiten algunos sistemas operativos de escritorio virtual de cliente (por ejemplo, Windows 7) para la concesión de licencias en Azure. Aquí encontrará [más información](https://azure.microsoft.com/pricing/licensing-faq/) acerca de la concesión de licencias a escritorios de cliente/servidor de Azure.

3.  Azure Site Recovery no puede replicar y proteger clones locales de MCS o PVS existentes.
Debe volver a crear estos clones mediante el aprovisionamiento de Azure RM desde el controlador de entrega.

4. No se puede proteger NetScaler con Azure Site Recovery, ya que NetScaler se basa en FreeBSD y Azure Site Recovery no admite la protección del sistema operativo de FreeBSD. Debe implementar y configurar una nueva aplicación NetScaler desde Azure Marketplace después de la conmutación por error a Azure.


## <a name="replicating-virtual-machines"></a>Replicación de máquinas virtuales

Es necesario proteger los siguientes componentes de la implementación de XenApp de Citrix para habilitar la replicación y la recuperación.

* Protección del servidor DNS de AD
* Protección del servidor de SQL Database
* Protección del controlador de entrega de Citrix
* Protección del servidor de StoreFront
* Protección de XenApp Master (VDA)
* Protección del servidor de licencias de XenApp de Citrix


**Replicación del servidor DNS de AD**

Vea [Protección de Active Directory y DNS con Azure Site Recovery](site-recovery-active-directory.md) para obtener instrucciones sobre cómo replicar y configurar un controlador de dominio en Azure.

**Replicación del servidor de SQL Database**

Vea [Proteger SQL Server con la recuperación ante desastres de SQL Server y Azure Site Recovery](site-recovery-sql.md) para obtener instrucciones técnicas detalladas sobre las opciones recomendadas para proteger servidores SQL Server.

Siga [estas instrucciones](site-recovery-vmware-to-azure.md) para empezar a replicar en Azure las demás máquinas virtuales de los componentes.

![Protección de componentes de XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Configuración de proceso y red**

Una vez que las máquinas estén protegidas (en Elementos replicados se muestra el estado "Protegido"), debe configurar los valores de Proceso y red.
En Proceso y red > Propiedades de Proceso, puede especificar el nombre y el tamaño de destino de la máquina virtual de Azure.
Modifique el nombre para que cumpla con los requisitos de Azure si es necesario. También puede ver y agregar la información sobre la red, la subred y la dirección IP de destino que se asignarán a la máquina virtual de Azure.

Tenga en cuenta lo siguiente:

* Puede establecer la dirección IP de destino. Si no proporciona una dirección, la máquina conmutada por error usará DHCP. Si establece una dirección que no está disponible en el momento de la conmutación por error, la conmutación no funcionará. Se puede utilizar la misma dirección IP de destino para la conmutación por error de prueba si la dirección está disponible en la red.

* En el caso del servidor AD/DNS, si conserva la dirección local, puede especificar la misma dirección del servidor DNS para Azure Virtual Network.

El número de adaptadores de red viene determinado por el tamaño que especifique para la máquina virtual de destino, de la siguiente manera:

*   Si el número de adaptadores de red en el equipo de origen es menor o igual al número de adaptadores permitido para el tamaño de la máquina de destino, el destino tendrá el mismo número de adaptadores que el origen.
*   Si el número de adaptadores para la máquina virtual de origen supera el número permitido para el tamaño de destino, entonces se utilizará el tamaño máximo de destino.
* Por ejemplo, si una máquina de origen tiene dos adaptadores de red y el tamaño de la máquina de destino admite cuatro, el equipo de destino tendrá dos adaptadores. Si el equipo de origen tiene dos adaptadores pero el tamaño de destino compatible solo admite uno, el equipo de destino tendrá solo un adaptador.
*   Si la máquina virtual tiene varios adaptadores de red, todos ellos se conectarán a la misma red.
*   Si la máquina virtual tiene varios adaptadores de red, el primero que se muestre en la lista se convertirá en el predeterminado en la máquina virtual de Azure.


## <a name="creating-a-recovery-plan"></a>Creación de un plan de recuperación

Una vez que se haya habilitado la replicación para las máquinas virtuales del componente XenApp, el paso siguiente consiste en crear un plan de recuperación.
Un plan de recuperación agrupa las máquinas virtuales con requisitos similares para la conmutación por error y la recuperación.  

**Pasos para crear un plan de recuperación**

1. Agregue las máquinas virtuales del componente XenApp al plan de recuperación.
2. Haga clic en Planes de recuperación > + Plan de recuperación. Proporcione un nombre intuitivo para el plan de recuperación.
3. Para las máquinas virtuales de VMware, seleccione como origen un servidor de procesos de VMware, como destino Microsoft Azure y como modelo de implementación el Administrador de recursos. Después, haga clic en Seleccionar elementos.
4. Para las máquinas virtuales de Hyper-V, seleccione como origen un servidor VMM, como destino Microsoft Azure y como modelo de implementación el Administrador de recursos. Después, haga clic en Seleccionar elementos y elija las máquinas virtuales de implementación de XenApp.

### <a name="adding-virtual-machines-to-failover-groups"></a>Incorporación de máquinas virtuales a grupos de conmutación por error

Puede personalizar los planes de recuperación para agregar grupos de conmutación por error para especificar un orden de inicio, scripts o acciones manuales. Debe agregar los grupos siguientes al plan de recuperación.

1. Grupo de conmutación por error 1: DNS de AD
2. Grupo de conmutación por error 2: máquinas virtuales con SQL Server
2. Grupo de conmutación por error 3: máquina virtual de imagen maestra de VDA
3. Grupo de conmutación por error 4: máquinas virtuales del controlador de entrega y del servidor de StoreFront


### <a name="adding-scripts-to-the-recovery-plan"></a>Incorporación de scripts al plan de recuperación

Puede ejecutar los scripts antes o después de un grupo específico en un plan de recuperación. También puede incluir y realizar acciones manuales durante la conmutación por error.

El plan de recuperación personalizado es similar al siguiente:

1. Grupo de conmutación por error 1: DNS de AD
2. Grupo de conmutación por error 2: máquinas virtuales con SQL Server
3. Grupo de conmutación por error 3: máquina virtual de imagen maestra de VDA

   >[!NOTE]     
   >Los pasos 4, 6 y 7, que contienen acciones manuales o de script, solo son aplicables a un entorno de XenApp local con catálogos MCS/PVS.

4. Acción manual o de script del grupo 3: apagado de la máquina virtual del VDA maestro. Esta máquina, cuando se realice la conmutación por error a Azure, se encontrará en el estado "En ejecución". Para crear catálogos MCS con el hospedaje de Azure ARM, la máquina virtual del VDA maestro debe encontrarse en el estado "Detenido" (desasignada). Apague la máquina virtual desde Azure Portal.

5. Grupo de conmutación por error 4: máquinas virtuales del controlador de entrega y del servidor de StoreFront
6. Acción manual o de script 1 del grupo 3:

    ***Agregar conexión de host de Azure RM***

    Cree una conexión de host de Azure ARM en la máquina del controlador de entrega para aprovisionar nuevos catálogos MCS en Azure. Siga los pasos que se describen en este [artículo](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Acción manual o de script 2 del grupo 3:

    ***Volver a crear catálogos MCS en Azure***

    Los clones MCS o PVS existentes en el sitio principal no se replicarán en Azure. Debe volver a crear estos clones mediante el VDA maestro replicado y el aprovisionamiento de Azure ARM desde el controlador de entrega. Siga los pasos que se describen en este [artículo](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) para crear catálogos MCS en Azure.

![Plan de recuperación para componentes de XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Puede usar scripts en la [ubicación](https://github.com/Azure/azure-quickstart-templates/blob/>master/asr-automation-recovery/scripts) para actualizar el DNS con las nuevas direcciones IP de las máquinas virtuales conmutadas por error o para asociar un equilibrador de carga a la máquina virtual conmutada por error, si es necesario.


## <a name="doing-a-test-failover"></a>Realización de una conmutación por error de prueba

Siga [estas directrices](site-recovery-test-failover-to-azure.md) para llevar a cabo una conmutación por error de prueba.

![Plan de recuperación](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Realización de una conmutación por error

Siga [estas directrices](site-recovery-failover.md) cuando realice una conmutación por error.

## <a name="next-steps"></a>Pasos siguientes

Puede obtener [más información](https://aka.ms/citrix-xenapp-xendesktop-with-asr) sobre la replicación de implementaciones de XenApp y XenDesktop de Citrix en estas notas del producto. Consulte las instrucciones para la [replicación de otras aplicaciones](site-recovery-workload.md) mediante Site Recovery.
