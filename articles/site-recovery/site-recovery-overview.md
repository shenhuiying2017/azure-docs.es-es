<properties
    pageTitle="¿Qué es Site Recovery? | Microsoft Azure"
    description="Proporciona información general sobre el servicio de Azure Site Recovery y resume escenarios de implementación."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/13/2016"
    ms.author="raynew"/>


#  <a name="what-is-site-recovery?"></a>¿Qué es Site Recovery?

¡Bienvenido a Azure Site Recovery! En este artículo se proporciona una descripción general del servicio Site Recovery y de cómo contribuye a su negocio.

Su organización necesita una estrategia de recuperación ante desastres y continuidad del negocio (BCDR) que proteja y mantenga disponibles las aplicaciones, las cargas de trabajo y los datos durante los tiempos de inactividad planeados e imprevistos, además de restablecer las condiciones de trabajo normales lo antes posible. Site Recovery es un servicio de Azure que contribuye a esta estrategia.

Site Recovery coordina la replicación de las cargas de trabajo que se ejecutan en servidores físicos locales y máquinas virtuales. Puede replicar VM y servidores de un centro de datos principal en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error al sitio secundario para mantener disponibles y accesibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal.

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery en el Portal de Azure

Azure tiene dos [modelos de implementación](../resource-manager-deployment-model.md) diferentes para crear recursos y utilizarlos. El modelo de Azure Resource Manager y el modelo de administración de servicios clásico. Azure también tiene dos portales: el [Portal de Azure clásico](https://manage.windowsazure.com/), que admite el modelo de implementación clásico, y [Azure Portal](https://portal.azure.com), que es compatible con ambos modelos de implementación.

- Site Recovery está disponible tanto en el portal clásico como en el Portal de Azure.
- En el Portal de Azure clásico, Site Recovery se puede usar con el modelo de administración de servicios clásico.
- En Azure Portal, se pueden usar tanto el modelo de implementación clásica como el modelo de Resource Manager. 

La información de este artículo se aplica tanto a la implementación clásica como a la del Portal de Azure. Las diferencias se indicarán cuando corresponda.


## <a name="why-deploy-site-recovery?"></a>¿Por qué implementar Site Recovery?

Esto es lo que Site Recovery puede hacer por su empresa:

- **Simplificación de la estrategia BCDR**: puede controlar la replicación, la conmutación por error y la recuperación de varias cargas de trabajo desde una sola ubicación de Azure Portal. Site recovery organiza la replicación y la conmutación por error pero no intercepta los datos de su aplicación ni obtiene información alguna sobre ella.
- **Replicación flexible**: con Site Recovery puede replicar las cargas de trabajo que se ejecutan en VM de Hyper-V y VMware, y en servidores físicos de Windows o Linux.
- **Pruebas de replicación sencillas**: Site Recovery proporciona conmutaciones por error de prueba que admiten maniobras de recuperación ante desastres sin que los entornos de producción se vean afectados.
- **Conmutación por error y recuperación**: puede ejecutar conmutaciones por error planeadas para interrupciones previstas sin pérdida de datos o conmutaciones por error imprevistas con una pérdida de datos mínima (según la frecuencia de replicación) ante desastres inesperados. Después de la conmutación por error puede ejecutar una conmutación por recuperación a los sitios principales. Site Recovery proporciona planes de recuperación que pueden incluir scripts y libros de Azure Automation para que pueda personalizar la conmutación por error y la recuperación de aplicaciones de niveles múltiples.
- **Eliminación de un centro de datos secundario**: puede replicar las cargas de trabajo en Azure, en lugar de en un sitio secundario. Esto elimina el costo y la complejidad de mantener un centro de datos secundario. Los datos replicados se almacenan en Almacenamiento de Azure con toda la resistencia que proporciona. Las VM se crean con los datos replicados cuando se produce la conmutación por error.
- **Integración con tecnologías de BCDR existentes**: Site Recovery se integra con otras características de BCDR. Por ejemplo, puede utilizar Site Recovery para proteger el back-end de SQL Server de cargas de trabajo corporativas, incluida la compatibilidad nativa para SQL Server AlwaysOn, a fin de administrar la conmutación por error de grupos de disponibilidad.

## <a name="what-can-i-replicate?"></a>¿Qué puedo replicar?

Este es un resumen de lo que puede replicar con Site Recovery.

![De local a local](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLICATE** | **REPLICACIÓN EN** 
---|---
Cargas de trabajo que se ejecutan en VM de VMware locales | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Sitio secundario](site-recovery-vmware-to-vmware.md)
Cargas de trabajo que se ejecutan en VM de Hyper-V locales administradas en nubes de VMM  | [Azure](site-recovery-vmm-to-azure.md)<br/><br/> [Sitio secundario](site-recovery-vmm-to-vmm.md) 
Cargas de trabajo que se ejecutan en VM de Hyper-V locales administradas en nubes de VMM con almacenamiento SAN|  [Sitio secundario](site-recovery-vmm-san.md)
Cargas de trabajo que se ejecutan en VM de Hyper-V locales sin VMM | [Azure](site-recovery-hyper-v-site-to-azure.md)
Cargas de trabajo que se ejecutan en servidores físicos de Windows o Linux locales | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Sitio secundario](site-recovery-vmware-to-vmware.md)


## <a name="what-workloads-can-i-protect?"></a>¿Qué cargas de trabajo puedo proteger?

Site Recovery habilita la estrategia de BCDR en función de la aplicación para que las cargas de trabajo y aplicaciones sigan ejecutándose de forma coherente cuando se producen interrupciones. Site Recovery proporciona:

- **Instantáneas coherentes con la aplicación**: las máquinas se replican mediante instantáneas coherentes con la aplicación para aplicaciones únicas o de varios niveles. Además de capturar los datos del disco, las instantáneas coherentes con la aplicación capturan todos los datos en memoria y todas las transacciones en curso.
- **Replicación casi sincrónica**: Site Recovery proporciona una frecuencia de replicación de tan solo 30 segundos para Hyper-V y replicación continua para VMware.
- **Planes de recuperación flexibles**: puede crear y personalizar planes de recuperación con scripts externos y acciones manuales. La integración con runbooks de Azure Automation permite recuperar toda una pila de aplicaciones con un solo clic.
- **Integración con SQL Server AlwaysOn**: puede administrar la conmutación por error de grupos de disponibilidad en los planes de recuperación de Site Recovery.
- **Biblioteca de automatización**: una rica biblioteca de Automatización de Azure que proporciona scripts específicos de la aplicación y preparados para la producción que pueden descargarse e integrarse con Site Recovery.
- **Administración de red simple**: la administración de red avanzada en Site Recovery y Azure simplifica los requisitos de red para una aplicación, incluida la reserva de direcciones IP, la configuración de equilibradores de carga o la integración de Azure Traffic Manager para conmutaciones de red eficientes.


## <a name="next-steps"></a>Pasos siguientes

- Más información en [¿Qué cargas de trabajo se pueden proteger con Azure Site Recovery?](site-recovery-workload.md)
- Más información acerca de la arquitectura de Site Recovery en [¿Cómo funciona Azure Site Recovery?](site-recovery-components.md)
 



<!--HONumber=Oct16_HO2-->


