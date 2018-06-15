---
title: Disponibilidad de SAP HANA dentro de una región de Azure | Microsoft Docs
description: Describe las operaciones de SAP HANA en máquinas virtuales nativas de Azure en una región de Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 47dba73a6c22d11953485a69435000d3d2fe6f55
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192805"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Disponibilidad de SAP HANA dentro de una región de Azure
En este artículo se describen varios escenarios de disponibilidad dentro de una región de Azure. Azure tiene muchas regiones repartidas por todo el mundo. Para obtener la lista de las regiones de Azure, consulte [Regiones de Azure](https://azure.microsoft.com/regions/). Para implementar SAP HANA en máquinas virtuales dentro de una región de Azure, Microsoft ofrece la implementación de una única máquina virtual con una instancia de HANA. Para una mayor disponibilidad, puede implementar dos máquinas virtuales con dos instancias de HANA dentro de un [conjunto de disponibilidad de Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) que usen la replicación del sistema HANA para la disponibilidad. 

Actualmente, Azure ofrece una versión preliminar pública de [zonas de disponibilidad de Azure (versión preliminar)](https://docs.microsoft.com/azure/availability-zones/az-overview). En este artículo, no se describen las zonas de disponibilidad en detalle. Sin embargo, sí se aborda con carácter general el uso de conjuntos de disponibilidad en comparación con las zonas de disponibilidad.

¿En qué se diferencia un conjunto de disponibilidad de una zona de disponibilidad en Azure? Las regiones de Azure donde se ofrecen zonas de disponibilidad tienen varios centros de datos. La fuente de alimentación, la refrigeración y la red de los centros de datos es independiente. El motivo de ofrecer distintas zonas dentro de una única región de Azure es permitirle implementar aplicaciones en dos o tres de las zonas de disponibilidad ofrecidas. Suponiendo que los problemas en las fuentes de alimentación o la red afectaran únicamente a la infraestructura de una zona de disponibilidad, la implementación de su aplicación dentro de una región de Azure seguiría siendo completamente funcional si usa zonas de disponibilidad. Podría experimentarse una reducción de la capacidad. Por ejemplo, las máquinas virtuales de una zona podrían perderse, pero las máquinas virtuales de las otras dos zonas aún seguirían funcionando. 
 
Un conjunto de disponibilidad de Azure es una funcionalidad de agrupación lógica que sirve para asegurarse de que los recursos de máquina virtual que coloque en el conjunto de disponibilidad estén aislados entre sí cuando se implementen en un centro de datos de Azure. Azure garantiza que las máquinas virtuales colocadas en un conjunto de disponibilidad se ejecuten en varios servidores físicos, grupos de proceso, unidades de almacenamiento y conmutadores de red. En determinada documentación de Azure, a esta configuración se le conoce como ubicaciones en diferentes [dominios de actualización y error](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Estas ubicaciones están normalmente dentro de un centro de datos de Azure. Suponiendo que los problemas en las fuentes de alimentación o la red afectaran al centro de datos que está implementando, toda la capacidad de una región de Azure resultaría afectada.

La ubicación de centros de datos que representan las zonas de disponibilidad de Azure es un equilibrio entre la entrega de latencia de red entre los servicios implementados en diferentes zonas que son aceptables para la mayoría de las aplicaciones y una determinada distancia entre los centros de datos. Idealmente, las catástrofes naturales no afectarían al suministro de energía y red, ni a la infraestructura de todas las zonas de disponibilidad de esta región. Sin embargo, como se ha demostrado en catástrofes naturales a gran escala, las zonas de disponibilidad podrían no ser siempre capaces de proporcionar la disponibilidad dentro de una región. Considérese, por ejemplo, el huracán María que alcanzó la isla de Puerto Rico el 20 de septiembre de 2017. El huracán causó básicamente un apagón casi en el 100 % de la isla de 145 km de superficie.

## <a name="single-vm-scenario"></a>Escenario de una única máquina virtual

En un escenario de una única máquina virtual, se crea una máquina virtual de Azure para la instancia de SAP HANA. Se utiliza Azure Premium Storage para hospedar el disco del sistema operativo y todos los discos de datos. El Acuerdo de Nivel de Servicio de tiempo de actividad del 99,9 % de Azure y los Acuerdos de Nivel de Servicio de otros componentes de Azure son suficientes para cumplir sus Acuerdos de Nivel de Servicio de disponibilidad con sus clientes. En este escenario, no es necesario que aproveche un conjunto de disponibilidad de Azure con máquinas virtuales que ejecutan la capa de DBMS. Usará dos características diferentes:

- Reinicio automático de máquinas virtuales de Azure (también conocido como reparación del servicio de Azure)
- Reinicio automático de SAP HANA

El reinicio automático de máquinas virtuales de Azure, o reparación del servicio, es una funcionalidad de Azure que opera en dos niveles:

- El host de servidor de Azure comprueba el mantenimiento de una máquina virtual hospedada en el host de servidor.
- El controlador de tejido de Azure supervisa el mantenimiento y la disponibilidad del host de servidor.

Una funcionalidad de comprobación de mantenimiento supervisa el mantenimiento de cada máquina virtual hospedada en un host de servidor de Azure. Si una máquina virtual entra en un estado incorrecto, el agente de host de Azure puede comenzar un reinicio de la máquina virtual para comprobar su mantenimiento. El controlador de tejido comprueba el mantenimiento del host mediante la comprobación de muchos parámetros diferentes que podrían indicar problemas con el hardware del host. También comprueba la accesibilidad del host a través de la red. Los indicios de problemas con el host pueden llevar a acciones como las siguientes:

- Si el host indica un estado de mantenimiento incorrecto, reinicio del host y reinicio de las máquinas virtuales que se ejecutaban en él.
- Si el estado de mantenimiento del host no fuera correcto tras reiniciarlo, reinicio del host y reinicio de las máquinas virtuales originalmente hospedadas en él en un host correcto. En este caso, el host se marca como incorrecto. No se usará en las sucesivas implementaciones hasta que se elimine o sustituya.
- En casos donde el host incorrecto tenga problemas con el proceso de reinicio, reinicio inmediato de las máquinas virtuales en un host con un mantenimiento correcto. 

Gracias a la supervisión del host y de las máquinas virtuales que proporciona Azure, las máquinas virtuales de Azure que experimentan problemas del host se reinician automáticamente en un host de Azure correcto. 

La segunda característica en la que se basa un escenario de este tipo, es el hecho de que el servicio HANA que se ejecuta en una máquina virtual reiniciada se inicia automáticamente tras el reinicio de la máquina virtual. El [reinicio automático de servicios HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) se puede configurar mediante los servicios de vigilancia de los diferentes servicios HANA.

Este escenario de máquina virtual única se podría mejorar mediante la adición de un nodo de conmutación por error en frío a una configuración de SAP HANA. En la documentación de SAP HANA, esta configuración se conoce como [conmutación automática por error del host](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Esta configuración puede tener sentido en situaciones de implementación local donde el hardware del servidor está limitado y se dedica un único nodo del servidor como nodo de conmutación automática por error del host para un conjunto de hosts de producción. Sin embargo, en Azure, donde su infraestructura subyacente proporciona un servidor de destino correcto para el reinicio con éxito de una máquina virtual, no tiene sentido implementar la conmutación automática por error del host de SAP HANA. En consecuencia, no tenemos una arquitectura de referencia que prevea un nodo de espera para la conmutación automática por error del host de HANA. Esto también se aplica a las configuraciones de escalabilidad horizontal de SAP HANA.

## <a name="availability-scenarios-for-two-different-vms"></a>Escenarios de disponibilidad con dos máquinas virtuales diferentes

Si usa dos máquinas virtuales de Azure en conjuntos de disponibilidad de Azure, puede aumentar el tiempo de actividad entre estas dos máquinas virtuales si se colocan en un conjunto de disponibilidad de Azure dentro de una única región de Azure. La configuración básica de Azure sería similar a la siguiente:

![Diagrama de dos máquinas virtuales con todas las capas](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Para ilustrar los distintos escenarios de disponibilidad, en el diagrama se han omitido algunas de las capas. El diagrama muestra solo las capas que representan máquinas virtuales, hosts, conjuntos de disponibilidad y regiones de Azure. Las instancias de Azure Virtual Network, los grupos de recursos y las suscripciones no influyen en los escenarios descritos en esta sección.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replicación de copias de seguridad en una segunda máquina virtual

Una de las configuraciones más rudimentarias es utilizar copias de seguridad. En concreto, es posible que envíe copias de seguridad del registro de transacciones desde una máquina virtual a otra máquina virtual de Azure. Puede elegir el tipo de instancia de Azure Storage. En esta configuración, su responsabilidad consiste en crear un script para copiar en la segunda máquina virtual las copias de seguridad programadas realizadas en la primera. En caso de que sea necesario usar las instancias de la segunda máquina virtual, tendría que restaurar la copia de seguridad incremental o diferencial completa y las copias de seguridad del registro de transacciones en el punto que necesite. 

La arquitectura tiene el siguiente aspecto:

![Diagrama de dos máquinas virtuales con replicación de almacenamiento](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Esta configuración no es adecuada para conseguir grandes tiempos de objetivo de punto de recuperación (RPO) y objetivo de tiempo de recuperación (RTO). En especial, los tiempos de RTO resultarían afectados debido a la necesidad de restaurar completamente la base de datos mediante los archivos de copia de seguridad copiados. Sin embargo, esta configuración es útil para recuperarse de la eliminación de datos no intencionada en las instancias principales. Con ella, puede restaurar en cualquier momento a un punto en el tiempo determinado, extraer los datos e importar los datos eliminados en la instancia principal. De ahí que tenga sentido usar este método de copia de seguridad en combinación con otra funcionalidad de alta disponibilidad. 

Mientras se copian las copias de seguridad, es posible que pueda usar una máquina virtual más pequeña que la principal en la que se ejecuta la instancia de SAP HANA. Tenga en cuenta que a las máquinas virtuales más pequeñas puede adjuntar un menor número de VHD. Consulte [Tamaños de las máquinas virtuales Linux en Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) para ver los límites de los tipos de máquina virtual individual.

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Replicación del sistema de SAP HANA sin conmutación automática por error

Los escenarios descritos en esta sección utilizan la replicación del sistema de SAP HANA. Para ver la documentación de SAP, consulte [Replicación del sistema](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Dos máquinas virtuales de Azure en una sola región de Azure tienen configuraciones diferentes, por lo que existen algunas diferencias de RTO. En general, los escenarios sin conmutación por error automática podrían no ser aplicables específicamente a las máquinas virtuales de una única región de Azure. Esto se debe a que, para la mayoría de los errores en la infraestructura de Azure, la reparación del servicio de Azure reinicia la máquina virtual principal en otro host. Solo existen algunos casos extremos en los que esta configuración podría resultar útil en términos de escenarios de error. O bien, en algunos casos, es posible que un cliente desee conseguir una mayor eficiencia.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>Replicación del sistema de SAP HANA sin conmutación por error automática y sin datos precargados

En este escenario, se usa la replicación del sistema de SAP HANA para mover datos de forma sincrónica para alcanzar un RPO de 0. Por otro lado, el RTO es suficientemente prolongado como para no necesitar la conmutación por error ni datos precargados en la memoria caché de la instancia de HANA. En este caso, es posible conseguir una mayor economía en la configuración mediante las acciones siguientes:

- Ejecute otra instancia de SAP HANA en la segunda máquina virtual. La instancia de SAP HANA en la segunda máquina virtual toma la mayor parte de la memoria de la máquina virtual. Normalmente, esto es así por si se produjera una conmutación por error en la segunda máquina virtual. Puede apagar la segunda máquina virtual, de modo que los datos replicados se puedan cargar en la caché de la instancia de HANA de destino de la segunda máquina virtual.
- Use una máquina virtual de menor tamaño en segunda máquina virtual. Si se produce una conmutación por error, habrá que realizar un paso adicional antes de la conmutación por error manual. En este paso, cambiará el tamaño de la máquina virtual al de la máquina virtual de origen. El escenario se parecería a este:

![Diagrama de dos máquinas virtuales con replicación de almacenamiento](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Incluso si no utiliza datos precargados en el destino de replicación del sistema de HANA, necesitará al menos 64 GB de memoria. También necesitará suficiente memoria además de 64 GB para mantener los datos del almacén de filas en la memoria de la instancia de destino.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>Replicación del sistema de SAP HANA sin conmutación por error automática y con datos precargados

En este escenario, se cargan previamente los datos replicados en la instancia de HANA en la segunda máquina virtual. Esto elimina las dos ventajas de no usar datos precargados. En este caso, no puede ejecutar otro sistema SAP HANA en la segunda máquina virtual. Tampoco puede usar un tamaño de máquina virtual más pequeño. Por lo tanto, los clientes raramente implementan este escenario.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Replicación del sistema de SAP HANA con conmutación por error automática

En la configuración de disponibilidad estándar más habitual dentro de una región de Azure, dos máquinas virtuales de Azure que ejecutan Linux SLES tienen definido un clúster de conmutación por error. El clúster de Linux SLES se basa en el marco [Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) en combinación con un dispositivo [STONITH](http://linux-ha.org/wiki/STONITH). 

Desde una perspectiva de SAP HANA, el modo de replicación usado se sincroniza y se configura una conmutación por error automática. En la segunda máquina virtual, la instancia de SAP HANA actúa como un nodo en espera activa. El nodo en espera recibe una secuencia sincrónica de registros de cambios de la instancia principal de SAP HANA. A medida que las transacciones se confirman en la aplicación en el nodo principal de HANA, dicho nodo espera hasta que el nodo secundario de SAP HANA confirma que ha recibido el registro de confirmación. SAP HANA ofrece dos modos diferentes de replicación sincrónica. Para obtener más información y conocer las diferencias entre estos dos modos de replicación sincrónica, lea el artículo de SAP [Replication modes for SAP HANA system replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html) (Modos de replicación en la replicación del sistema de SAP HANA).

La configuración general se parece a esta:

![Diagrama de dos máquinas virtuales con replicación de almacenamiento y conmutación por error](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Es posible que elija esta solución porque le permite conseguir un RPO=0 y tiempos extremadamente bajos de RTO. Configure la conectividad del cliente de SAP HANA de forma que use la dirección IP virtual para conectarse a la configuración de replicación del sistema de HANA. De esta forma, se elimina la necesidad de volver a configurar la aplicación en el caso de una conmutación por error al nodo secundario. En esta solución, las SKU de máquina virtual de Azure para la máquina virtual principal y la secundaria deben ser iguales.

## <a name="next-steps"></a>Pasos siguientes

Para obtener instrucciones paso a paso sobre cómo configurar estas configuraciones en Azure, vea:

- [Configuración de la replicación del sistema de SAP HANA en máquinas virtuales de Azure](sap-hana-high-availability.md)
- [High Availability for SAP HANA using System Replication](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/) (Alta disponibilidad en SAP HANA mediante la replicación del sistema)

Para más información sobre la disponibilidad de SAP HANA entre regiones de Azure, consulte:

- [Disponibilidad de SAP HANA entre regiones de Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

