<properties
	pageTitle="Consideraciones de implementación de VMM y Site Recovery | Microsoft Azure"
	description="En este artículo se describen las consideraciones prácticas de diseño para la integración de VMM con Azure Site Recovery"
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
	ms.workload="storage-backup-recovery"
	ms.date="02/15/2016"
	ms.author="raynew"/>

#  Consideraciones de implementación de VMM y Site Recovery

El servicio Azure Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Las máquinas se pueden replicar a Azure o a un centro de datos secundario local. Para obtener una introducción rápida, lea [¿Qué es Azure Site Recovery?](site-recovery-overview.md).


## Información general

Este artículo le ayuda a diseñar e implementar una solución BCDR, junto con la infraestructura que incluye System Center VMM y Azure Site Recovery.

El propósito de la estrategia de BCDR es mantener sus aplicaciones empresariales en funcionamiento, y restaurar las cargas de trabajo y servicios con errores y para que la organización pueda reanudar rápidamente el funcionamiento normal. El desarrollo de estrategias de recuperación ante desastres es un reto, debido a las dificultades inherentes para predecir imprevistos y los altos costos de la implementación de una protección adecuada contra errores de gran alcance. Site Recovery le ayuda a implementar la protección y la conmutación por error desde el centro de datos principal a un centro de datos secundario (o a Azure) copiando inicialmente (replicando) los datos principales y, a continuación, actualizando periódicamente las réplicas.

Como parte fundamental del plan de BCDR tendrá que definir su objetivo de tiempo de recuperación (RTO) y objetivo de punto de recuperación (RPO) para poder obtener en línea la recuperación de los datos de la organización tan pronto como sea posible (con un RTO bajo) y con pérdida mínima de datos (RPO baja). El diseño de la red de su organización es un posible cuello de botella para el RTO, por lo tanto, un plan de diseño sólido y objetivos de RPO pueden ayudar a evitar este cuello de botella.


Si va a implementar Site Recovery para replicar máquinas virtuales de Hyper-V de nubes de VMM, deberá tener en cuenta cómo VMM se integra en la estrategia de conmutación por error y replicación de Site Recovery.

## Integración de un servidor VMM independiente

En esta topología, implementará un servidor VMM independiente en una máquina virtual del sitio principal y después replicará esta máquina virtual en un sitio secundario con Site Recovery y Hyper-V Replica. Tenga en cuenta que instalar el servidor VMM y su servidor SQL Server compatible en la misma máquina virtual puede reducir el tiempo de inactividad, porque solo se tienen que crear instancias de una máquina virtual. Cuando el servicio de VMM utiliza un servidor SQL Server remoto necesitará recuperar la instancia de este servidor antes de recuperar el servidor VMM.

Para implementar un solo VMM en una máquina virtual con Hyper-V Replica:

1. Configure el servidor VMM en una máquina virtual con el servidor SQL Server instalado.
2. Agregue los hosts que se administrarán en las nubes en este servidor VMM.
3. Inicie sesión en el portal de Azure y, a continuación, configure la protección para las nubes.
4. Habilite la replicación de todas las máquinas virtuales que deben estar protegidas por el servidor VMM.
5. Vaya a la consola de administrador de Hyper-V, elija Hyper-V Replica y, a continuación, habilite la replicación en la máquina virtual de VMM.
6. Asegúrese de que la máquina virtual de VMM no se agrega a las nubes que están protegidas por el servicio de ASR para que no se reemplace la configuración de replicación de Hyper-V mediante ASR.

En caso de desastre, las cargas de trabajo pueden recuperarse siguiendo estos pasos:

1. Conmute por error la réplica de la máquina virtual de VMM en el sitio de recuperación con el administrador de Hyper-V.
2. Una vez recuperada la máquina virtual de VMM, el usuario puede iniciar sesión en el administrador de recuperación de Hyper-V desde el sitio secundario.
3. Una vez completada la conmutación por error no planeada, los usuarios pueden acceder a todos los recursos en el sitio principal.
4. Tenga en cuenta que la máquina virtual de VMM debe conmutar por error manualmente en el sitio secundario antes de que las cargas de trabajo pueden conmutar por error.


### Integración de un clúster de VMM


[La implementación de VMM en un clúster](https://technet.microsoft.com/library/gg610675.aspx) proporciona alta disponibilidad y protección contra la conmutación por error de hardware. Si va a implementar el clúster de VMM con Recovery Site tenga en cuenta lo siguiente:

- El servidor VMM debe implementarse en un clúster estirado en sitios separados geográficamente.
- La Base de datos de SQL Server utilizada por VMM debe estar protegida con grupos de disponibilidad AlwaysOn de SQL Server con una réplica en el sitio secundario.
- Si se produce un desastre, el servidor VMM y su correspondiente SQL Server conmutarán por error automáticamente en el sitio de recuperación. A continuación, puede conmutar las cargas de trabajo con Site Recovery.

	![Clúster de VMM estirado](./media/site-recovery-network-design/network-design1.png)


## Pasos siguientes

[Aprenda](site-recovery-network-mapping.md) cómo Site Recovery asigna las redes de origen y de destino.

<!---HONumber=AcomDC_0218_2016-->