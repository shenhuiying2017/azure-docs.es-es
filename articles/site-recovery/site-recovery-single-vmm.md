
<properties
	pageTitle="Azure Site Recovery: replicación de máquinas virtuales de Hyper-V en un único servidor VMM | Microsoft Azure"
	description="En este artículo se describe cómo replicar máquinas virtuales de Hyper-V cuando solo se cuenta con un servidor VMM."
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
	ms.date="08/24/2016"
	ms.author="raynew"/>

#  Replicación de las máquinas virtuales de Hyper-V en un único servidor VMM

Lea este artículo para descubrir cómo replicar máquinas virtuales Hyper-V ubicadas en un servidor host de Hyper-V situado en una nube VMM si solo tiene un único servidor VMM en su implementación.

Azure tiene dos [modelos de implementación](../resource-manager-deployment-model.md) diferentes para crear y utilizar recursos: Azure Resource Manager y el clásico. Azure también tiene dos portales: el Portal de Azure clásico que admite el modelo de implementación clásico y el Portal de Azure que es compatible con ambos modelos de implementación. Este artículo contiene instrucciones para configurar la replicación en el Portal de Azure.


Si tiene alguna pregunta después de leer el artículo, publíquela en los comentarios de Disqus al final de este artículo o en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Información general

Si desea replicar máquinas virtuales de Hyper-V ubicadas en hosts de Hyper-V de VMM y solo tiene un servidor VMM, puede [replicar a Azure](site-recovery-vmm-to-azure.md) o de una nube a otra en el servidor VMM único.

Se recomienda replicar a Azure, porque la conmutación por error y la recuperación no son perfectas al replicar de una nube a otra y se necesitan algunos pasos manuales. Si desea replicar únicamente con el servidor VMM, puede hacer lo siguiente:

- Replicar con un servidor VMM único e independiente
- Replicar con un único servidor VMM implementado en un clúster de Windows ampliado


## Replicar en los sitios con un servidor VMM único e independiente

![Servidor VMM virtual independiente](./media/site-recovery-single-vmm/single-vmm-standalone.png)

En este escenario, se implementa un solo servidor VMM como máquina virtual en el sitio principal y se replica esta última en uno secundario mediante Site Recovery y Réplica de Hyper-V.

1. Configurar VMM en una máquina virtual de Hyper-V. Para ahorrar tiempo, se recomienda instalar la instancia de SQL Server que usa VMM en la misma máquina virtual. Si quiere usar una instancia remota de SQL Server y se produce una interrupción, debe recuperar esa instancia para poder recuperar VMM.
2. Asegúrese de que el servidor VMM tenga al menos dos nubes configuradas. Una nube contiene las máquinas virtuales que quiera replicar y la otra actuará como ubicación secundaria. La nube que contenga las VM a las que quiere proteger debe contar con los siguientes elementos:

	- Uno o varios grupos host de VMM que contengan uno o varios servidores host de Hyper-V en cada grupo host.
	- Al menos una máquina virtual de Hyper-V en cada servidor host de Hyper-V.

3. Cree un almacén de Servicios de recuperación, genere y descargue una clave de registro de almacén y registre el servidor VMM en el almacén. Durante el registro, se instala el proveedor de Azure Site Recovery en el servidor VMM.
4. Configure una o más nubes en la máquina virtual de VMM y agregue los hosts de Hyper-V a estas nubes.
3. Defina la configuración de la protección para las nubes. Especifique el nombre del servidor VMM único como la ubicación de origen y de destino. Para configurar la asignación de red, se asigna la red de la máquina virtual de la nube que contiene máquinas virtuales que desea proteger a la red de máquina virtual de la nube de la réplica.
4. Habilite la replicación inicial de las máquinas virtuales que desee proteger en la red como, ya que las nubes se encuentran en el mismo servidor.
4. En la consola de administrador de Hyper-V, habilite la réplica de Hyper-V en el host de Hyper-V que contiene la máquina virtual de VMM y habilite la replicación en la máquina virtual. Asegúrese de que no agrega la máquina virtual de VMM a nubes protegidas por Site Recovery. Esto garantiza que Site Recovery no reemplaza la configuración de Réplica de Hyper-V.
5. Si desea crear planes de recuperación, especifique el mismo servidor VMM de origen que de destino.

Siga las instrucciones de [este artículo](site-recovery-vmm-to-vmm.md) para crear un almacén, registrar el servidor y establecer la protección.

### En caso de interrupción

Si se produce una interrupción total y necesita trabajar desde el sitio secundario, haga lo siguiente:

1.	En la consola de administrador de Hyper-V del sitio secundario, ejecute una conmutación por error no planeada para conmutar la máquina virtual de VMM desde el sitio principal al secundario.
2.	Compruebe que la máquina virtual de VMM está en funcionamiento en el sitio secundario.
3.	En el almacén de Servicios de recuperación, ejecute una conmutación por error no planeada para conmutar las máquinas virtuales de carga de trabajo de las nubes principal y secundaria. Para completar la conmutación por error no planeada de las máquinas virtuales, confirme la conmutación por error o seleccione otro punto de recuperación, según sea necesario.
4.	Una vez completada la conmutación por error no planeada, los usuarios podrán acceder a los recursos de la carga de trabajo del sitio secundario.

Cuando el sitio principal funcione normalmente nuevo, haga lo siguiente:

1.	En la consola de administrador de Hyper-V, habilite la replicación inversa para la máquina virtual de VMM para iniciar la replicación del sitio secundario al principal.
2.	En la consola de administrador de Hyper-V, ejecute una conmutación por error planeada para conmutar la máquina virtual de VMM con el sitio principal. Confirme la conmutación por error para finalizarla. A continuación, habilite la replicación inversa para iniciar la replicación del VMM del sitio principal al secundario.
3.	En el almacén de Servicios de recuperación, habilite la replicación inversa para las máquinas virtuales de carga de trabajo e iniciar la replicación del sitio secundario al principal.
4.	En el almacén de Servicios de recuperación, ejecute una conmutación por error planeada para conmutar las máquinas virtuales de carga de trabajo al sitio principal. Confirme la conmutación por error para finalizarla. A continuación, habilite la replicación inversa para iniciar la replicación de las máquinas virtuales de carga de trabajo del sitio principal al secundario.



## Replicar en los sitios con un servidor VMM único en un clúster extendido

![Servidor VMM virtual en clúster](./media/site-recovery-single-vmm/single-vmm-cluster.png)

En lugar de implementar un servidor VMM independiente como máquina virtual que se replica en un sitio secundario, puede hacer que VMM esté altamente disponible mediante su implementación como máquina virtual en un clúster de conmutación por error de Windows. De esta forma, se garantiza la resistencia de las cargas de trabajo y la protección frente a los errores del hardware. Para efectuar una implementación con Site Recovery, la VM de VMM debe implementarse en un clúster extendido en sitios separados geográficamente. Para ello, siga estos pasos:

1. Instale VMM en una máquina virtual en un clúster de conmutación por error de Windows y seleccione la opción de ejecución del servidor de alta disponibilidad durante la instalación.
2. La instancia de SQL Server usada por VMM se debe replicar con grupos de disponibilidad AlwaysOn de SQL Server para que haya una réplica de la base de datos en el sitio secundario.
3. Siga las instrucciones de [este artículo](site-recovery-vmm-to-vmm.md) para crear un almacén, registrar el servidor y establecer la protección. Debe registrar cada servidor VMM del clúster en el almacén de Servicios de recuperación. Para ello, instale el proveedor en un nodo activo y registre el servidor VMM. Después, instale el proveedor en los demás nodos.

### En caso de interrupción

Si se produce alguna interrupción, el servidor VMM y su base de datos de SQL Server correspondiente se conmutan por error y se accede a ellos desde el sitio secundario.


## Pasos siguientes

[Más información](site-recovery-vmm-to-vmm.md) sobre la implementación detallada de Site Recovery para la replicación de VMM a VMM.

<!---HONumber=AcomDC_0824_2016-->