
<properties
	pageTitle="Configuración de la protección con un único servidor VMM"
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales ubicadas en nubes de VMM en Azure o en una nube de VMM secundaria."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="05/04/2015"
	ms.author="raynew"/>

#  Configuración de la protección con un único servidor VMM

## Información general

Azure Site Recovery contribuye a su estrategia de continuidad de negocio y recuperación ante desastres (BCDR) mediante la organización de la replicación, la conmutación por error y la recuperación de máquinas virtuales en varios escenarios de implementación. Para obtener una lista completa de los escenarios de implementación, consulte [Información general sobre Azure Site Recovery](site-recovery-overview.md)

Si solo tiene un único servidor VMM en la infraestructura, puede implementar Site Recovery para replicar máquinas virtuales en nubes de VMM en Azure, o bien puede replicar entre nubes en un único servidor VMM. Se recomienda que solo haga esto si no puede implementar dos servidores VMM (uno en cada sitio) desde la conmutación por error y la recuperación no es perfecta en esta implementación. Para la recuperación deberá conmutar manualmente el servidor VMM desde fuera de la consola de Azure Site Recovery (mediante la réplica de Hyper-V en la consola del Administrador de Hyper-V).

Puede configurar la replicación con un único servidor VMM de dos formas:

### Implementación independiente

Implemente un servidor VMM independiente como una máquina virtual en un sitio principal y replicar esta máquina virtual en un sitio secundario con réplica de Hyper-V y Site Recovery. Para reducir el tiempo de inactividad, SQL Server puede instalarse en la máquina virtual de VMM. Si VMM utiliza un SQL Server remoto necesitará recuperarlo antes de recuperar el servidor VMM.

![Servidor VMM virtual independiente](./media/site-recovery-single-vmm/SingleVMMStandalone.png)

### Implementación del clúster

Para hacer que VMM tenga una alta disponibilidad, se puede implementar como una máquina virtual en un clúster de conmutación por error de Windows. Esto es útil si las cargas de trabajo críticas son administradas por VMM porque asegura la disponibilidad de la carga de trabajo y protege contra la conmutación por error del hardware del host en el que se ejecuta VMM. Para implementar un único servidor VMM con Site Recovery. se debe implementar la máquina virtual de VMM en un clúster ampliado en sitios separados geográficamente. La Base de datos de SQL Server utilizada por VMM debe estar protegida con grupos de disponibilidad AlwaysOn de SQL Server con una réplica en el sitio secundario. Si se produce un el desastre, el servidor VMM y su Base de datos de SQL Server correspondiente se conmutan por error y se tiene acceso a ellos desde el sitio secundario.

![Servidor VMM virtual en clúster](./media/site-recovery-single-vmm/SingleVMMCluster.png)


## Antes de comenzar

- Los pasos del tutorial explican cómo implementar Site Recovery con un servidor VMM único e independiente.
- Asegúrese de que cumplir los [requisitos previos](site-recovery-vmm-to-vmm.md/#before-you-start) antes de empezar la implementación.
- El único servidor VMM debe tener al menos dos nubes configuradas. Una nube actuará como la nube protegida y la otra está realizando la protección.
- Las nubes que desea proteger deben contener lo siguiente:
	- Uno o más grupos de hosts de VMM
	- Uno o más servidores host de Hyper-V en cada grupo de hosts
	- Una o más máquinas virtuales de Hyper-V en cada servidor host

Si tiene problemas al configurar este escenario, publique sus preguntas en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).



## Configuración de una implementación con un único servidor

1. Si no está implementado VMM, configure VMM en una máquina virtual con una Base de datos de SQL Server instalada. Lea los [requisitos del sistema](https://technet.microsoft.com/library/dn771747.aspx) 
2. Configure al menos dos nubes en el servidor VMM. Obtenga más información en:

	- [Novedades de la nube privada con System Center 2012 R2 VMM](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=) y en [VMM 2012 y las nubes](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html). 
	- [Configuración del tejido de nube de VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- [Creación de una nube privada en VMM](https://technet.microsoft.com/library/jj860425.aspx) y [Tutorial: Creación de nubes privadas con System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).
3. Agregue el servidor host de Hyper-V de origen en la que se encuentra la máquina virtual que desea proteger a la nube que va a proteger (la nube de origen). Agregue el servidor host de Hyper-V de destino a la nube en el servidor VMM que va a proporcionar la protección.
4. [Cree](site-recovery-vmm-to-vmm.md/#step-1-create-a-site-recovery-vault) un almacén de Azure Site Recovery y genere una clave de registro del almacén.
4. [Instale](site-recovery-vmm-to-vmm.md/#step-3-install-the-azure-site-recovery-provider) el Proveedor de Azure Site Recovery en el servidor VMM y registre el servidor en el almacén. 
5. Asegúrese de que las nubes aparecen en el portal de Site Recovery y [configure la protección de la nube](site-recovery-vmm-to-vmm.md/#step-4-configure-cloud-protection-settings).
	- En **Ubicación de origen** y **Ubicación de destino**, especifique el nombre del servidor VMM único.
	- En **método de replicación**, seleccione **A través de la red** para la replicación inicial porque las nubes que se encuentran en el mismo servidor.

6. Opcionalmente, [configure la asignación de redes](site-recovery-vmm-to-vmm.md/#step-5-configure-network-mapping):

	- En **Origen** y **Destino**, especifique el nombre del servidor VMM único.
	- En **Red en origen** seleccione la red de máquinas virtuales que está configurada para la nube que protege.
	- En **Red en destino** seleccione la red de la máquina virtual que está configurada para la nube que desea usar para la protección.
	- La asignación de redes se puede configurar entre dos redes de máquinas virtuales (VM) en el mismo servidor VMM. Si la misma red VMM existe en dos sitios distintos, puede asignar entre las mismas redes.
7. [Habilite la protección](site-recovery-vmm-to-vmm.md/#step-7-enable-virtual-machine-protection) de las máquinas virtuales en la nube de VMM que desea proteger. 
7. En la consola del Administrador de Hyper-V, configure la replicación de la máquina virtual VMM con la réplica de Hyper-V. La máquina virtual de VMM no debe agregarse a las nubes de VMM.


## Conmutación por error y recuperación

### Creación de planes de recuperación

Los planes de recuperación agrupan máquinas virtuales que se deben conmutar por error y recuperar.

1. Cuando se crea un plan de recuperación en **Origen** y **Destino**, especifique el nombre del servidor VMM único. En **Seleccionar máquinas virtuales**, se mostrarán las máquinas virtuales que están asociadas a la nube principal.
2. A continuación, [cree y personalice planes de recuperación](https://msdn.microsoft.com/library/azure/dn337331.aspx).


### Recuperación

En caso de desastre, las cargas de trabajo pueden recuperarse mediante los siguientes pasos:

1. Realice una conmutación por error manual de la máquina virtual de VMM de réplica en el sitio de recuperación desde la consola del Administrador de Hyper-V.
2. Una vez recuperada la máquina virtual de VMM, puede iniciar sesión en la consola del Administrador de recuperación de Hyper-V desde el portal y realizar una conmutación por error no planeada de las máquinas virtuales del sitio principal al de recuperación.
3.  Una vez finalizada la conmutación por error no planeada, los usuarios pueden acceder a todos los recursos en el sitio principal.


 

<!---HONumber=July15_HO4-->