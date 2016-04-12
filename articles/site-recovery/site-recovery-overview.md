<properties
	pageTitle="¿Qué es Site Recovery? | Microsoft Azure" 
	description="Proporciona información general sobre el servicio de Azure Site Recovery y explica cómo se puede implementar el servicio." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="02/22/2016" 
	ms.author="raynew"/>

#  ¿Qué es Site Recovery?

¡Bienvenido a Azure Site Recovery! Puede comenzar con este artículo para obtener una descripción general rápida del servicio Site Recovery y cómo puede este contribuir a su estrategia de continuidad del negocio y recuperación ante desastres (BCDR).

Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [el Administrador de recursos y el clásico](../resource-manager-deployment-model.md). Este artículo se aplica a ambos modelos. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.

## Información general

Una parte importante de la estrategia de BCDR de su organización es averiguar cómo mantener las aplicaciones y cargas de trabajo corporativas listas para su funcionamiento cuando se producen interrupciones planificadas e imprevistas.

Site Recovery le ayuda mediante la coordinación de la replicación, conmutación por error y recuperación de aplicaciones y cargas de trabajo para que estén disponibles desde una ubicación secundaria si la ubicación principal deja de funcionar.

## ¿Por qué usar Site Recovery? 

Esto es lo que Site Recovery puede hacer por su empresa:

- **Simplificar su estrategia de BCDR**: Site Recovery facilita el control de la replicación, la conmutación por error y la recuperación de varias cargas de trabajo y aplicaciones de negocios desde una única ubicación. Site recovery organiza la replicación y la conmutación por error pero no intercepta los datos de su aplicación ni obtiene información alguna sobre ella.
- **Proporcionar replicación flexible**: con Site Recovery puede replicar las cargas de trabajo que se ejecutan en máquinas virtuales de Hyper-V, máquinas virtuales de VMware y servidores físicos de Windows o Linux. 
- **Recuperación y conmutación por error sencillas**: Site Recovery proporciona conmutaciones por error de prueba que admiten maniobras de recuperación ante desastres sin que los entornos de producción se vean afectados. También puede ejecutar conmutaciones por error planeadas sin pérdidas de datos para interrupciones previstas o conmutaciones por error con una pérdida de datos mínima (según la frecuencia de replicación) ante desastres inesperados. Después de la conmutación por error puede ejecutar una conmutación por recuperación a los sitios principales. Site Recovery proporciona planes de recuperación que pueden incluir scripts y libros de Automatización de Azure para que pueda personalizar la conmutación por error y la recuperación de aplicaciones de varios niveles. 
- **Eliminar centros de datos secundarios**: puede replicar en un sitio local secundario o en Azure. Si usa Azure como destino para la recuperación ante desastres, eliminará el costo y la complejidad de mantener un sitio secundario, y los datos replicados se almacenarán en Almacenamiento de Azure, con todas la resistencia que esto proporciona.
- **Integración con tecnologías de BCDR existentes**: Site Recovery se asocia con otras características de BCDR de la aplicación. Por ejemplo, puede utilizar Site Recovery para proteger el back-end de SQL Server de cargas de trabajo corporativas, incluida la compatibilidad nativa para SQL Server AlwaysOn, a fin de administrar la conmutación por error de grupos de disponibilidad. 

## ¿Qué puedo replicar?

Este es un resumen de lo que puede replicar Site Recovery.

![De local a local](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLICATE** | **REPLICACIÓN DESDE** | **REPLICACIÓN EN** | **ARTÍCULO**
---|---|---|---
Cargas de trabajo que se ejecutan en máquinas virtuales de VMware | Servidor VMware local | Almacenamiento de Azure | [Implementación](site-recovery-vmware-to-azure-classic.md)
Cargas de trabajo que se ejecutan en máquinas virtuales de VMware | Servidor VMware local | Sitio secundario de VMware | [Implementación](site-recovery-vmware-to-vmware.md) 
Cargas de trabajo que se ejecutan en máquinas virtuales de Hyper-V | Servidor de host de Hyper-V local en la nube VMM | Almacenamiento de Azure | [Implementación](site-recovery-vmm-to-azure.md)
Cargas de trabajo que se ejecutan en máquinas virtuales de Hyper-V | Servidor de host de Hyper-V local en la nube VMM | Sitio secundario de VMM | [Implementación](site-recovery-vmm-to-vmm.md)
Cargas de trabajo que se ejecutan en máquinas virtuales de Hyper-V | Servidor de host de Hyper-V local en la nube VMM con almacenamiento SAN| Sitio secundario de VMM con almacenamiento SAN | [Implementación](site-recovery-vmm-san.md)
Cargas de trabajo que se ejecutan en máquinas virtuales de Hyper-V | Sitio de Hyper-V local (no VMM) | Almacenamiento de Azure | [Implementación](site-recovery-hyper-v-site-to-azure.md)
Cargas de trabajo que se ejecutan en servidores físicos de Windows o Linux | Servidor físico local | Almacenamiento de Azure | [Implementación](site-recovery-vmware-to-azure-classic.md)
Cargas de trabajo que se ejecutan en servidores físicos de Windows o Linux | Servidor físico local | Centro de datos secundario | [Implementación](site-recovery-vmware-to-vmware.md) 


## ¿Qué cargas de trabajo puedo proteger?

Site Recovery puede ayudarle con la estrategia de BCDR en función de la aplicación para que las cargas de trabajo y aplicaciones sigan ejecutándose de forma coherente cuando se producen interrupciones. Site Recovery proporciona:

- **Instantáneas coherentes con la aplicación**: replicación mediante instantáneas coherentes con la aplicación para aplicaciones únicas o de n niveles. **Replicación casi sincrónica**: frecuencia de replicación de tan solo 30 segundos para Hyper-V y replicación continua para VMware. **Integración con SQL Server AlwaysOn**: puede administrar la conmutación por error de grupos de disponibilidad en los planes de recuperación de Site Recovery. 
- **Planes de recuperación flexibles**: puede crear y personalizar planes de recuperación con scripts externos, acciones manuales y runbooks de Automatización de Azure que permiten recuperar una pila de toda la aplicación con un solo clic.
- **Biblioteca de automatización**: una biblioteca de Automatización de Azure completa proporciona scripts específicos de aplicación, listos para la producción, que se pueden descargar e integrar con Site Recovery. -** Administración de red simple **: administración de red avanzada de Site Recovery y Azure simplifica los requisitos de red de aplicación, incluida la reserva de direcciones IP, la configuración de equilibradores de carga y la integración de Administrador de tráfico de Azure para unas conmutaciones de red eficientes.


## Pasos siguientes

- Más información en [¿Qué cargas de trabajo se pueden proteger con Azure Site Recovery?](site-recovery-workload.md)
- Más información acerca de la arquitectura de Site Recovery en [¿Cómo funciona Azure Site Recovery?](site-recovery-components.md)
 

<!---HONumber=AcomDC_0316_2016-->