
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
	ms.date="03/30/2016"
	ms.author="raynew"/>

#  Replicación de las máquinas virtuales de Hyper-V en un único servidor VMM

Lea este artículo para descubrir cómo replicar máquinas virtuales Hyper-V ubicadas en un servidor host de Hyper-V situado en una nube VMM si solo tiene un único servidor VMM en su implementación.

Si tiene alguna pregunta después de leer el artículo, publíquela al final de este o en el [Foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Información general

Puede replicar las VM de Hyper-V ubicadas en hosts de Hyper-V en nubes VMM de dos modos:

- Replicación en Azure. 
- Replicación en un sitio de VMM.

¿Pero qué sucede si quiere replicar las VM en una ubicación de VMM secundaria, pero solo dispone de un solo servidor VMM en su implementación?

En este escenario, tiene dos opciones:

- Replicar las VM de Hyper-V ubicadas en nubes VMM en Azure. [Obtenga más información](site-recovery-vmm-to-azure.md) acerca de este escenario.
- Replicar las VM entre nubes en el único servidor VMM.

Se recomienda la primera opción, ya que la conmutación por error y la recuperación no son perfectas en la segunda opción; además, se deberán llevar a cabo diversos pasos manuales. Si quiere replicar las VM en diversos sitios en lugar de en Azure, tiene un par de opciones.


## Replicar en los sitios con un servidor VMM único e independiente

Para replicar en distintos sitios en este escenario, implementará un solo servidor VMM como una máquina virtual en el sitio primario, y replicará esta última en uno secundario mediante Site Recovery y Réplica de Hyper-V. Para ello, siga estos pasos:

1. Configurar VMM en una máquina virtual de Hyper-V. A la hora de llevar a cabo este paso, le recomendamos que también se plantee colocar la instancia de SQL Server usada por VMM en la misma VM. Esto permite ahorrar tiempo, ya que solo se tienen que crear instancias de una máquina virtual. Si quiere usar una instancia remota de SQL Server y se produce una interrupción, necesitará recuperar esa instancia para poder recuperar VMM.
2. Asegúrese de que el servidor VMM tenga al menos dos nubes configuradas. Una nube contendrá las máquinas virtuales que desee replicar y la otra nube actuará como la ubicación secundaria. La nube que contenga las VM a las que quiere proteger debe contar con los siguientes elementos:

	- Uno o varios grupos host de VMM que contengan uno o varios servidores host de Hyper-V en cada grupo host.
	- Al menos una máquina virtual de Hyper-V en cada servidor host de Hyper-V.
3. Cree un almacén de recuperación del sitio, genere y descargue una clave de registro de almacén y registre el servidor VMM en el almacén. Durante el registro, instalará el proveedor de Azure Site Recovery en el servidor VMM.
4. Configure una o más nubes en la máquina virtual de VMM y agregue los hosts de Hyper-V que contienen máquinas virtuales que desea proteger en estas nubes.
3. En Azure Site Recovery configurar opciones de protección para las nubes. En Ubicación de origen y de destino, especificará el nombre del servidor VMM único. Si configura la asignación de red, se asignará la red de la máquina virtual de la nube que contiene máquinas virtuales que desea proteger a la red de máquina virtual de la nube en la que desea efectuar la replicación.
4. Habilite la replicación de máquinas virtuales que desee proteger mediante **A través de la red** como método de replicación, ya que tanto ambas nubes se encuentran en el mismo servidor.
4. En la consola de administrador de Hyper-V, habilite la réplica de Hyper-V en el host de Hyper-V que contiene la máquina virtual de VMM y habilite la replicación en la máquina virtual. Asegúrese de no agregar la máquina virtual VMM a las nubes que están protegidas por Site Recovery para garantizar que la configuración de la réplica de Hyper-V no la invalide Site Recovery.
5. Si desea crear planes de recuperación, especifique el mismo servidor VMM de origen y de destino.

Siga las instrucciones de [este artículo](site-recovery-vmm-to-vmm.md) para crear un almacén, obtener una clave, registrar el servidor y configurar la protección.

### Después de una interrupción

Cuando se produzca una interrupción, recuperará las cargas de trabajo de las VM de Hyper-V del siguiente modo:

1. Realice manualmente la conmutación por error de la VM de VMM en el sitio secundario utilizando el Administrador de Hyper-V con una conmutación por error planeada. 
2. Una vez recuperada la máquina virtual de VMM, puede iniciar sesión en el Administrador de recuperación de Hyper-V desde el sitio secundario y ejecutar una conmutación por error no planeada de las VM de este al primario. Tenga en cuenta que la VM de VMM debe conmutarse por error manualmente al sitio secundario para que las cargas de trabajo de las VM se puedan conmutar por error.
3. Una vez completada la conmutación por error no planeada, se podrá acceder a todos los recursos de nuevo desde el sitio primario.


![Servidor VMM virtual independiente](./media/site-recovery-single-vmm/single-vmm-standalone.png)

## Replicar en los sitios con un servidor VMM único en un clúster extendido

En lugar de implementar un servidor VMM independiente como una máquina virtual que se replica en un sitio secundario, puede hacer que VMM esté altamente disponible mediante su implementación como una VM en un clúster de conmutación por error de Windows. De esta forma, se garantiza la resistencia de las cargas de trabajo y la protección frente a los errores del hardware. Para efectuar una implementación con Site Recovery, la VM de VMM debe implementarse en un clúster extendido en sitios separados geográficamente. Para ello, siga estos pasos:

1. Instale VMM en una máquina virtual en un clúster de conmutación por error de Windows y seleccione la opción de ejecución del servidor de alta disponibilidad durante la instalación.
2. La instancia de SQL Server usada por VMM se debe replicar con grupos de disponibilidad AlwaysOn de SQL Server para que haya una réplica de la base de datos en el sitio secundario.

Tenga en cuenta que, al configurar Site Recovery, tendrá que registrar cada servidor VMM del clúster en el almacén de Site Recovery. Para ello, instale al proveedor en un nodo activo y finalice la instalación para registrar el servidor VMM en el almacén. Después, instale el proveedor en los demás nodos.
 
### Después de una interrupción 

Si se produce alguna interrupción, el servidor VMM y su base de datos de SQL Server correspondiente se conmutan por error y se tiene acceso a ellos desde el sitio secundario.

![Servidor VMM virtual en clúster](./media/site-recovery-single-vmm/single-vmm-cluster.png)

## Pasos siguientes

[Obtenga más información](site-recovery-vmm-to-vmm.md) sobre la implementación detallada de Site Recovery para la replicación de VMM a VMM.




 

<!---HONumber=AcomDC_0413_2016-->