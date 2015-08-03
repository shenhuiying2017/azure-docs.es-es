<properties
	pageTitle="Asignación de redes de Site Recovery"
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales ubicadas localmente en Azure o en un sitio local secundario."
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
	ms.date="05/08/2015"
	ms.author="raynew"/>


# Asignación de redes de Site Recovery


Azure Site Recovery contribuye a su estrategia de continuidad de negocio y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores virtuales. Obtenga información acerca de los escenarios de implementación posibles en [Información general sobre Site Recovery](site-recovery-overview.md).


## Información acerca de este artículo

La asignación de redes es un elemento importante al implementar VMM y Site Recovery. Coloca de forma óptima las máquinas virtuales replicadas en los servidores host de Hyper-V de destino y garantiza que las máquinas virtuales replicadas se conecten a redes adecuadas después de la conmutación por error. Este artículo describe la asignación de redes y proporciona un par de ejemplos que le ayudarán a entender cómo funciona la asignación de redes.


Publique cualquier pregunta en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Información general

La manera en que se establece la asignación de redes depende de su escenario de implementación de Site Recovery.



- **Servidores VMM de local a local**: la asignación de redes asigna entre redes de máquinas virtuales en un servidor VMM de origen y redes de máquinas virtuales en un servidor VMM de destino para hacer lo siguiente:

	- **Conexión de máquinas virtuales después de la conmutación por error**: asegura que las máquinas virtuales se conectarán a las redes adecuadas después de la conmutación por error. La máquina virtual de réplica se conectará a la red de destino que se asigna a la red de origen.
	- **Colocación de máquinas virtuales de réplica en los servidores host**: coloca máquinas virtuales de réplica en los servidores host de Hyper-V de forma óptima. Las máquinas virtuales de réplica se colocarán en los hosts que pueden tener acceso a las redes de máquinas virtuales asignadas.
	- **Sin asignación de redes**: si no configura la asignación de redes, las máquinas virtuales replicadas no se conectarán a las redes de máquinas virtuales después de la conmutación por error.

- **Servidores VMM de local a Azure**: la asignación de red asigna entre redes de máquinas virtuales en un servidor VMM de origen y redes de Azure de destino para hacer lo siguiente:
	- **Conexión de máquinas virtuales después de la conmutación por error**: todas las máquinas con conmutación por error en la misma red pueden conectarse entre sí, independientemente del plan de recuperación en el que se encuentran.
	- **Puerta de enlace de red**: si se configura una puerta de enlace de red en la red Azure de destino, las máquinas virtuales se pueden conectar a otras máquinas virtuales locales.
	- **Sin asignación de redes**: si no configura la asignación de redes, solo las máquinas virtuales con conmutación por error en el mismo plan de recuperación podrán conectarse entre sí después de la conmutación por error en Azure.

## Redes de máquinas virtuales

La red lógica de VMM proporciona una panorámica de la infraestructura de la red física. Las redes de máquinas virtuales proporcionan una interfaz de red para que las máquinas virtuales puedan conectarse a redes lógicas. Una red lógica necesita al menos una red de máquina virtual. Cuando se coloca una máquina virtual en una nube para su protección, debe estar conectado a redes de máquinas virtuales que se vincula a una red lógica asociada a la nube. Obtenga más información en:

- [Redes lógicas (parte 1)](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx)
- [Redes virtuales en VMM 2012 SP1](http://blogs.technet.com/b/scvmm/archive/2013/01/08/virtual-networking-in-vmm-2012-sp1.aspx)

## Ejemplo

La asignación de redes se puede configurar entre redes de máquinas virtuales en dos servidores VMM o en un único servidor VMM si el mismo servidor administra dos sitios. Cuando se configura correctamente la asignación de redes y se habilita la replicación, se conectará una máquina virtual de la ubicación principal a una red y su réplica de la ubicación de destino se conectará a la red asignada.

Si las redes se han configurado correctamente en VMM, al seleccionar una red de máquinas virtuales de destino durante la asignación de redes, se mostrará las nubes VMM de origen que utilizan la red de máquinas virtuales de origen, junto con las redes de máquinas virtuales de destino disponibles en las nubes de destino que se usan para la protección.

Este es un ejemplo para ilustrar este mecanismo. Vamos a utilizar una organización con dos ubicaciones en Nueva York y Chicago.

**Ubicación** | **Servidor VMM** | **Redes de máquinas virtuales** | **Asignado a**
---|---|---|---
Nueva York | VMM-NewYork| VMNetwork1-NewYork | Asignado a VMNetwork1-Chicago
 | | VMNetwork2-NewYork | No asignado
Chicago | VMM-Chicago| VMNetwork1-Chicago | Asignado a VMNetwork1-NewYork
 | | VMNetwork1-Chicago | No asignado

Con este ejemplo:

- Cuando se crea una máquina virtual de réplica para cualquier máquina virtual que está conectada a VMNetwork1-NewYork, se conectará a VMNetwork1-Chicago.
- Cuando se crea una máquina virtual de réplica para VMNetwork2-NewYork o VMNetwork2-Chicago, no se conectará a ninguna red.

Aquí es cómo se configuran las nubes de VMM en nuestra organización de ejemplo y las redes lógicas asociadas a las nubes.

### Configuración de la protección de la nube

**Nube protegida** | **Proteger nube** | **Red lógica (Nueva York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### Configuración de la red lógica y máquinas virtuales

**Ubicación** | **Red lógica** | **Red de VM asociada**
---|---|---
Nueva York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### Redes de destino

Según esta configuración, cuando selecciona la red de máquinas virtuales de destino, en la siguiente tabla se muestran las opciones que estarán disponibles.

**Selección** | **Nube protegida** | **Proteger nube** | **Red de destino disponible**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponible
 | GoldCloud1 | GoldCloud2 | Disponible
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | No disponible
 | GoldCloud1 | GoldCloud2 | Disponible



## Varias subredes

Si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.


### Conmutación por recuperación

Para ver lo que ocurre en el caso de conmutación por recuperación (replicación inversa), supongamos que VMNetwork1-NewYork se asigna a VMNetwork1-Chicago, con la siguiente configuración.


**Máquina virtual** | **Conectada a la red de VM**
---|---
VM1 | VMNetwork1-Network
VM2 (réplica de VM1) | VMNetwork1-Chicago

Con esta configuración revisemos lo que ocurre en un par de escenarios posibles.

**Escenario** | **Resultado**
---|---
Sin cambios en las propiedades de red de VM-2 después de la conmutación por error | VM-1 sigue estando conectada a la red de origen
Las propiedades de red de VM-2 cambian después de la conmutación por error y está desconectada | VM-1 se desconecta
Las propiedades de red de VM-2 cambian después de la conmutación por error y está conectada a VMNetwork2-Chicago | Si no está asignada VMNetwork2-Chicago, se desconectará VM-1
Se cambia la asignación de redes de VMNetwork1-Chicago | VM-1 se conectará a la red ahora asignada a VMNetwork1-Chicago


## Pasos siguientes

Ahora que tiene una mejor comprensión de la asignación de redes, lea las [prácticas recomendadas](site-recovery-best-practices.md) para preparar la implementación.
 

<!---HONumber=July15_HO4-->