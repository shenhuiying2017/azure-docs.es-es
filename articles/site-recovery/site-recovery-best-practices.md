---
title: Procedimientos recomendados de Azure Site Recovery | Microsoft Docs
description: "En este artículo se describe los procedimientos recomendados para la implementación de Azure Site Recovery"
services: site-recovery
documentationCenter: 
author: rayne-wiselman"
manager: cfreeman
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: site-recovery-support-matrix-to-azure
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: a53e2a09fb2aabe96dd5347ea7509815d92bfdb8
ms.lasthandoff: 03/15/2017

---

# <a name="get-ready-to-deploy-azure-site-recovery"></a>Preparación para la implementación de Azure Site Recovery

En este artículo se describe cómo preparar la implementación de Azure Site Recovery.

## <a name="protecting-hyper-v-virtual-machines"></a>Protección de las máquinas virtuales de Hyper-V

Dispone de un par opciones para implementar la protección de las máquinas virtuales de Hyper-V. Puede replicar las máquinas virtuales de Hyper-V locales en Azure o en un centro de datos secundario. Cada implementación presenta diferentes requisitos.

**Requisito** | **Replicación en Azure (con VMM)** | **Replicación de máquinas virtuales de Hyper-V en Azure (sin VMM)** | **Replicación de máquinas virtuales de Hyper-V en un sitio secundario (con VMM)** | **Detalles**
---|---|---|---|---
**VMM** | VMM ejecutándose en System Center 2012 R2 <br/><br/>Al menos una nube VMM que contenga uno o más grupos de hosts de VMM. | N/D | Servidores VMM en el sitio principal y el secundario que se ejecuten como mínimo en System Center 2012 SP1 con las últimas actualizaciones <br/><br/> Al menos una nube en cada servidor VMM. Las nubes deben tener establecido el perfil de capacidad de Hyper-V.<br/><br/> La nube de origen debe tener al menos un grupo de hosts de VMM. | Opcional. No es necesario tener implementado System Center VMM para replicar máquinas virtuales de Hyper-V en Azure pero sí para asegurarse de que el servidor VMM esté configurado correctamente, lo que incluye comprobar que se esté ejecutando la versión correcta de VMM y que las nubes estén configuradas.
**Hyper-V** | Al menos un servidor host de Hyper-V en el sitio local que ejecute Windows Server 2012 R2 o posterior | Al menos un servidor de Hyper-V en el sitio de origen y el de destino que ejecute como mínimo Windows Server 2012 con las actualizaciones más recientes instaladas y con conexión a Internet.<br/><br/> Los servidores de Hyper-V deben encontrarse en un grupo de hosts en una nube VMM. | Al menos un servidor de Hyper-V en el sitio de origen y el de destino que ejecute como mínimo Windows Server 2012 con las actualizaciones más recientes instaladas y con conexión a Internet.<br/><br/> Los servidores de Hyper-V deben encontrarse en un grupo de hosts en una nube VMM. |
**Máquinas virtuales** | Al menos una máquina virtual en el servidor host de Hyper-V de origen | Al menos una máquina virtual en el servidor host de Hyper-V en la nube VMM de origen | Al menos una máquina virtual en el servidor host de Hyper-V en la nube VMM de origen |  Las máquinas virtuales que se replican en Azure tienen que cumplir con los requisitos previos de las máquinas virtuales de Azure
**Cuenta de Azure** | Necesitará una cuenta de Azure y una suscripción al servicio Site Recovery. | Necesitará una cuenta de Azure y una suscripción al servicio Site Recovery. | N/D | Si no tiene cuenta, comience con una evaluación gratuita.
**Almacenamiento de Azure** | Necesitará una suscripción para una cuenta de Azure Storage que tenga habilitada la replicación geográfica. | Necesitará una suscripción para una cuenta de Azure Storage que tenga habilitada la replicación geográfica. | N/D | La cuenta debe estar en la misma región que el almacén de Azure Site Recovery y estar asociada a la misma suscripción.
**Redes** | Configure una asignación de red para asegurarse de que todas las máquinas que conmuten por error en la misma red Azure se puedan conectar entre sí, con independencia del plan de recuperación al que pertenezcan. Además, si se configura una puerta de enlace de red en la red Azure de destino, las máquinas virtuales se pueden conectar a otras máquinas virtuales locales. Si no configura la asignación de redes, solo se pueden conectar las máquinas que conmutan por error en el mismo plan de recuperación. | N/D |  <br/><br/>Configure la asignación de red para asegurarse de que las máquinas virtuales se conecten a las redes adecuadas tras la conmutación por error y de que las máquinas virtuales de réplica estén colocadas de manera óptima en los servidores host de Hyper-V. Si no configura la asignación de red, las máquinas replicadas no se conectarán a ninguna red de máquinas virtuales tras la conmutación por error. |  Para configurar la asignación de red con VMM, tiene que asegurarse de que la red lógica de VMM y las redes de máquinas virtuales estén configuradas correctamente.
**Proveedores y agentes** | Durante la implementación, se instala al proveedor de Azure Site Recovery en los servidores VMM. En los servidores de Hyper-V ubicados en nubes VMM, se instala el agente de Servicios de recuperación de Azure. | Durante la implementación, se instalan el proveedor de Azure Site Recovery y el agente de Servicios de recuperación de Azure en el servidor host de Hyper-V o el clúster.| Durante la implementación, se instala al proveedor de Azure Site Recovery en los servidores VMM. En los servidores de Hyper-V ubicados en nubes VMM, se instala el agente de Servicios de recuperación de Azure. | Los proveedores y los agentes se conectan a Site Recovery a través de Internet mediante una conexión HTTPS cifrada. No es necesario agregar excepciones del firewall ni crear un proxy específico para la conexión del proveedor.
**Conectividad de Internet** | Solo los servidores VMM necesitan una conexión a Internet | Solo los servidores host de Hyper-V necesitan una conexión a Internet | Solo los servidores VMM necesitan una conexión a Internet | Las máquinas virtuales no necesitan que se instale nada en ellas y no se conectan directamente a Internet.



## <a name="protect-vmware-virtual-machines-or-physical-servers"></a>Protección de máquinas virtuales de VMware o servidores físicos

Dispone de un par de opciones para implementar la protección de máquinas virtuales de VMware o de servidores físicos Windows o Linux. Puede replicarlos en Azure o en un centro de datos secundario. Cada implementación presenta diferentes requisitos.

**Requisito** | **Replicación de máquinas virtuales de VMware o servidores físicos en Azure** | * **Replicación de máquinas virtuales de VMware o servidores físicos en un sitio secundario**  
---|---|---
**Sitio principal** | **Servidor de procesos**: un servidor de Windows dedicado (físico o virtual) | **Servidor de procesos**: un servidor de Windows dedicado (físico o una máquina virtual de VMware)<br/><br/>  
**Sitio local secundario** | N/D | **Servidor de configuración**: un servidor de Windows dedicado (físico o virtual) <br/><br/> **Servidor de destino maestro**: un servidor dedicado (físico o virtual). Configúrelo con Windows para proteger equipos Windows, o con Linux para proteger equipos Linux.
**Las tablas de Azure** | **Suscripción**: necesitará una suscripción para el servicio Site Recovery. <br/><br/> **Cuenta de almacenamiento**: necesitará una cuenta de almacenamiento con la replicación geográfica habilitada. La cuenta debe estar en la misma región que el almacén de Azure Site Recovery y estar asociada a la misma suscripción. <br/><br/> **Servidor de configuración**: tendrá que configurar el servidor de configuración como una máquina virtual de Azure <br/><br/> **Servidor de destino maestro**: tendrá que configurar el servidor de destino maestro como una máquina virtual de Azure <br/><br/> Configúrelo con Windows para proteger equipos Windows, o con Linux para proteger equipos Linux.<br/><br/> **Red virtual de Azure**: necesitará una red virtual de Azure en la que se implementarán el servidor de configuración y el servidor de destino maestro. Debe estar en la misma región y suscripción que el almacén de Azure Site Recovery | N/D  
**Máquinas virtuales/Servidores físicos** | Al menos una máquina virtual de VMware o un servidor físico de Windows o Linux.<br/><br/>Durante la implementación, se instala Mobility Service en cada máquina| Al menos una máquina virtual de VMware o un servidor físico de Windows o Linux.<br/><br/> Durante la implementación, se instala el agente unificado en cada equipo.




## <a name="azure-virtual-machine-requirements"></a>Requisitos para las máquinas virtuales de Azure

Puede implementar Site Recovery para replicar máquinas virtuales y servidores físicos que ejecuten cualquier sistema operativo compatible con Azure. Incluye la mayoría de las versiones de Windows y Linux. Debe asegurarse de que las máquinas virtuales locales que desea proteger cumplan los requisitos de Azure.


## <a name="optimizing-your-deployment"></a>Optimización de la implementación

Aplique las siguientes sugerencias como ayuda para optimizar y escalar la implementación.

- **Tamaño del volumen del sistema operativo**: cuando se replica una máquina virtual en Azure, el volumen del sistema operativo tiene que ser inferior a 1 TB. Si tiene más volúmenes, puede moverlos manualmente a otro disco antes de comenzar la implementación.
- **Tamaño de disco de datos**: si se está replicando en Azure, podrá tener hasta 32 discos de datos en una máquina virtual, cada una con un máximo de 1 TB. Puede replicar y conmutar por error de manera eficaz una máquina virtual de ~32 TB.
- **Límites del plan de recuperación**: Site Recovery puede escalar a miles de máquinas virtuales. Los planes de recuperación están diseñados como un modelo para las aplicaciones que deben conmutar por error entre sí por lo que se limita el número de máquinas en un plan de recuperación a 50.
- **Límites de servicio de Azure**: cada suscripción de Azure incluye un conjunto de límites predeterminados sobre núcleos, servicios en la nube, etc. Le recomendamos que ejecute una conmutación por error de prueba para validar la disponibilidad de los recursos de la suscripción. Puede modificar estos límites a través de soporte técnico de Azure.
- **Planeamiento de capacidad**: planee la escalabilidad y el rendimiento.
- **Ancho de banda de replicación**: si tiene poco ancho de banda de replicación, tenga en cuenta lo siguiente:
    - **ExpressRoute**: Site Recovery funciona con los optimizadores de ExpressRoute de Azure y WAN, como Riverbed.
    - **Tráfico de replicación**: Site Recovery realiza una replicación inicial inteligente usando solo bloques de datos y no todo el VHD. Solo se replican los cambios durante la replicación continua.
    - **Tráfico de red**: puede controlar el tráfico de red que se utiliza para la replicación mediante la configuración de Windows QoS con una directiva basada en la dirección IP de destino y el puerto.  Además, si se está replicando en Azure Site Recovery mediante el agente Copia de seguridad de Azure. Puede configurar la limitación para ese agente.
- **RTO**: si desea medir el objetivo de tiempo de recuperación (RTO) que se puede esperar con Site Recovery, le sugerimos que ejecute una conmutación por error de prueba y vea los trabajos de Site Recovery para analizar la cantidad de tiempo que se tarda en completar las operaciones. Si está conmutando por error en Azure, para obtener un mejor RTO se recomienda que automatice todas las acciones manuales mediante la integración con Automatización de Azure y los planes de recuperación.
- **RPO**: Site Recovery admite un objetivo de punto de recuperación casi sincrónico (RPO) al replicar a Azure. Esto supone suficiente ancho de banda entre el centro de datos y Azure.

