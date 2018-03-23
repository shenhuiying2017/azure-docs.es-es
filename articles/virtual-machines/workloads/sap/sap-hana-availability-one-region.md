---
title: Disponibilidad de SAP HANA dentro de una región de Azure | Microsoft Docs
description: Operaciones de SAP HANA en máquinas virtuales nativas de Azure
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
ms.date: 03/5/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 28fe9bc7c8cb1d59df404b7dd7429def54648a18
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Disponibilidad de SAP HANA dentro de una región de Azure
En esta sección se presentan varios escenarios que describen la disponibilidad dentro de una región de Azure. Azure tiene muchas regiones, que están repartidas por todo el mundo. Para ver la lista de regiones de Azure, consulte el artículo [Regiones de Azure](https://azure.microsoft.com/regions/). Para implementar SAP HANA en máquinas virtuales dentro de una región de Azure, Microsoft ofrece la implementación de una única máquina virtual con una instancia de HANA. También, para aumentar la disponibilidad, puede implementar dos máquinas virtuales con dos instancias de HANA dentro de un [conjunto de disponibilidad de Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) que vayan a usar la replicación del sistema HANA con esta finalidad. Azure dispone de una versión preliminar pública de [zonas de disponibilidad de Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). Estas zonas de disponibilidad no se van a tratar aquí con detalle. Simplemente se expondrán ideas generales en torno al uso de conjuntos de disponibilidad frente a zonas de disponibilidad.

¿Cuál es la diferencia entre los conjuntos de disponibilidad y las zonas de disponibilidad de Azure? En las regiones de Azure donde se van a ofrecer zonas de disponibilidad, hay muchos centros de datos en los que el suministro de la fuente de energía, la refrigeración y la red es independiente. El motivo de ofrecer distintas zonas dentro de una única región de Azure es permitirle implementar aplicaciones en dos o tres de estas zonas. Suponiendo que los problemas en las fuentes de energía o la red afectaran únicamente a la infraestructura de una zona de disponibilidad, la implementación de su aplicación dentro de una región de Azure seguiría siendo completamente funcional. En última instancia, con cierta capacidad reducida, dado que algunas máquinas virtuales de una zona podrían perderse. Pero las máquinas virtuales de las otras dos zonas seguirían funcionando. 
 
Por otro lado, un conjunto de disponibilidad de Azure es una funcionalidad de agrupación lógica que puede usar en Azure para asegurarse de que los recursos de máquina virtual que coloque en dicho conjunto de disponibilidad estén aislados entre sí cuando se implementen en un centro de datos de Azure. Azure garantiza que las máquinas virtuales colocados en un conjunto de disponibilidad se ejecuten en varios servidores físicos, grupos de proceso, unidades de almacenamiento y conmutadores de red. O, lo que se conoce en otra documentación de Azure como ubicaciones en diferentes [dominios de actualización y error](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Estas ubicaciones están normalmente dentro de un centro de datos de Azure. Suponiendo que los problemas en las fuentes de energía o la red afectaran al centro de datos que ha implementado, toda la capacidad de una región de Azure resultaría afectada.

La ubicación de centros de datos que representan las zonas de disponibilidad de Azure es un equilibrio entre la entrega de latencia de red entre los servicios implementados en diferentes zonas que son aceptables para la mayoría de las aplicaciones y una cierta distancia entre los centros de datos. Así que, idealmente, las catástrofes naturales no afectarían al suministro de energía y red, ni a la infraestructura de todas las zonas de disponibilidad de esta región. Sin embargo, como se ha demostrado en catástrofes naturales monumentales, las zonas de disponibilidad podrían no ser siempre capaces de proporcionar la disponibilidad dentro de una región tal y como se desea. Este fue el caso del huracán María que devastó la isla de Puerto Rico el 20 de agosto de 2017 y básicamente provocó un apagón cercano al 100% en sus 145 km de extensión.   
  


## <a name="single-vm-scenario"></a>Escenario de máquina virtual única
En este escenario, ha creado una máquina virtual de Azure para la instancia de SAP HANA. Ha usado Azure Premium Storage para hospedar el disco del sistema operativo y todos los discos de datos. El Acuerdo de Nivel de Servicio del 99,9 % de Azure y los Acuerdos de Nivel de Servicio de otros componentes de Azure son suficientes para cumplir sus Acuerdos de Nivel de Servicio de disponibilidad con sus clientes. En este escenario, no es necesario que aproveche un conjunto de disponibilidad de Azure con máquinas virtuales que ejecutan el nivel de DBMS. Usará dos características diferentes:

- Reinicio automático de máquinas virtuales de Azure (también conocido como reparación de servicios de Azure)
- Reinicio automático de SAP HANA

El reinicio automático de máquinas virtuales de Azure o "reparación de servicios" es una funcionalidad de Azure que opera en dos niveles:

- El host de servidor de Azure comprueba el mantenimiento de una máquina virtual hospedada en el host de servidor.
- El controlador de tejido de Azure supervisa el mantenimiento y la disponibilidad del host de servidor.

En cada máquina virtual hospedada en un host de servidor de Azure, una de las funcionalidades de comprobación de estado es la supervisión del estado de dichas máquinas. Si las máquinas virtuales entran en un estado incorrecto, el agente de host de Azure puede comenzar un reinicio de la máquina virtual para comprobar su estado. Para determinar el mantenimiento del host, el controlador de tejido de Azure comprueba muchos parámetros diferentes que indican problemas con el hardware, pero también comprueba la accesibilidad del host a través de la red. Los indicios de problemas con el host pueden llevar a acciones como:

- Reinicio del host y reinicio de las máquinas virtuales que se ejecutaban en él si se encuentran señales de un estado de mantenimiento incorrecto
- Reinicio del host y reinicio de las máquinas virtuales originalmente hospedadas en él en un host correcto, si se diera el caso de que tras reiniciarlo el estado de mantenimiento no fuera correcto. En este caso, el host se marca como incorrecto y no se usa en las sucesivas implementaciones hasta que se elimina o sustituye.
- Reinicio inmediato de las máquinas virtuales en un host con un mantenimiento correcto en casos donde el host incorrecto tenga problemas con el proceso de reinicio. 

Gracias a la supervisión del host y de las máquinas virtuales que proporciona Azure, las máquinas virtuales de Azure que experimentan problemas del host se reinician automáticamente en un host de Azure correcto. 

La segunda característica en la que se basa un escenario de este tipo, es el hecho de que su servicio HANA que se ejecuta dentro de dicha máquina virtual reiniciada se inicia automáticamente tras el reinicio de la máquina virtual. El [reinicio automático de servicios HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) se puede configurar mediante los servicios de vigilancia de los diferentes servicios HANA.

Este escenario de máquina virtual única se podría mejorar mediante la adición de un nodo de conmutación por error en frío a una configuración de SAP HANA. O, como se nombra en la documentación de SAP HANA, una [conmutación automática por error del host](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Esta configuración puede tener sentido en situaciones de implementación local donde el hardware del servidor está limitado y se dedica un único nodo del servidor como nodo de conmutación automática por error del host para un conjunto de hosts de producción. Sin embargo, en casos donde la infraestructura subyacente de Azure proporciona un servidor de destino correcto para el reinicio con éxito de una máquina virtual, no tiene sentido implementar el escenario de conmutación automática por error del host de SAP HANA. 

Como resultado, no tenemos una arquitectura de referencia que prevea un nodo de espera para la conmutación automática por error del host de HANA. Esto también se aplica a las configuraciones de escalado horizontal de SAP HANA.


## <a name="availability-scenarios-involving-two-different-vms"></a>Escenarios de disponibilidad con dos máquinas virtuales diferentes
El uso de dos máquinas virtuales de Azure en conjuntos de disponibilidad de Azure le permite aumentar el tiempo de actividad entre estas dos máquinas virtuales si se colocan en un conjunto de disponibilidad de Azure dentro de una región de Azure. La configuración básica de Azure se parece a la de los gráficos que se muestran aquí: ![Dos máquinas virtuales con todos los niveles](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Para ilustrar los distintos escenarios de disponibilidad, algunos de los niveles de arriba se han recortado y los gráficos se limitan a los niveles de máquinas virtuales, hosts, conjuntos de disponibilidad y regiones de Azure. Las redes virtuales, los grupos de recursos y las suscripciones de Azure no desempeñan un papel en los escenarios descritos.

### <a name="replicating-backups-to-second-virtual-machine"></a>Replicación de copias de seguridad en una segunda máquina virtual
Una de las configuraciones más rudimentarias es hacer que las copias de seguridad, en especial las copias de seguridad del registro de transacciones, se envíen de una máquina virtual a otra máquina virtual de Azure. Puede elegir cualquier tipo de almacenamiento de Azure. Su responsabilidad sería crear un script para copiar en la segunda máquina virtual las copias de seguridad programadas realizadas en la primera. En caso de que sea necesario usar las instancias de la segunda máquina virtual, tendría que restaurar la copia de seguridad incremental o diferencial completa y las copias de seguridad del registro de transacciones en el punto que necesite. La arquitectura tendría el siguiente aspecto: ![Dos máquinas virtuales con replicación de almacenamiento](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Esta configuración no es demasiado adecuada para lograr buenos tiempos de RPO y RTO. En especial, los tiempos de RTO resultarían afectados debido a la necesidad de restaurar completamente la base de datos con los archivos de copia de seguridad copiados. Sin embargo, esta configuración es útil para recuperarse de la eliminación de datos no intencionada en las instancias principales. Con ella, puede restaurar en cualquier momento a un punto en el tiempo determinado, extraer los datos e importar los datos eliminados en la instancia principal. De ahí que tenga sentido usar este método de copia de seguridad en combinación con otra funcionalidad de alta disponibilidad. Durante el período en que solo se copien los archivos de copia de seguridad, podría irle mejor con una máquina virtual más pequeña que las instancias principales de SAP HANA en las que se ejecuta. Pero tenga en cuenta que las máquinas virtuales más pequeñas tienen un número menor de discos duros virtuales que se pueden asociar. Consulte [Tamaños de las máquinas virtuales Linux en Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) para ver los límites de los tipos de máquina virtual individual.

### <a name="using-sap-hana-system-replication-without-automatic-failover"></a>Uso de replicación del sistema de SAP HANA sin conmutación automática por error
En los escenarios siguientes, usará la replicación del sistema de SAP HANA. Se puede encontrar documentación emitida por SAP comenzando con el artículo [System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) (Replicación del sistema). Entre dos máquinas virtuales de Azure de una sola región de Azure, hay dos configuraciones distintas que presentan algunas diferencias en el objetivo de tiempo de recuperación. En general, los escenarios que no tienen conmutación automática por error podrían no ser demasiado significativos para los escenarios dentro de una región de Azure. El motivo de ello es que en la mayoría de las situaciones de error de la infraestructura de Azure, la reparación del servicio de Azure va a reiniciar la máquina virtual principal en otro host. Solo hay algunos casos de servidor perimetral en los que una configuración de este tipo podría ayudar en términos de escenarios de error. Y otros casos en los que usted, como cliente, se dará cuenta, en especial en lo relativo a la eficiencia.

#### <a name="using-hana-system-replication-without-auto-failover-and-without-data-pre-load"></a>Uso de replicación del sistema HANA sin conmutación automática por error y sin datos precargados 
Se trata de un escenario donde usa la replicación del sistema SAP HANA con el fin de mover datos de forma sincrónica para lograr un objetivo de punto de recuperación (RPO) de 0. Por otro lado, tiene un objetivo de tiempo de recuperación (RTO) lo suficientemente largo, así que no necesita conmutación por error o carga previa de los datos en la memoria caché de instancias de HANA. En tal caso, tiene la posibilidad de abaratar los costos de su configuración de la manera siguiente:

- Puede ejecutar otra instancia de SAP HANA en la segunda máquina virtual que toma la mayor parte de la memoria de la máquina virtual principal. Normalmente, una instancia así sería una que, en caso de una conmutación por error a la segunda máquina virtual, podría apagarse. De modo que, los datos replicados se pueden cargar en la caché de la instancia de HANA de destino de la segunda máquina virtual.
- Podría usar una máquina virtual de menor tamaño como segunda máquina virtual. En caso de una conmutación por error, habría un paso antes de la conmutación por error manual donde ajustaría el tamaño de la máquina virtual al de la máquina virtual de origen. El escenario se parecería a este:

![Dos máquinas virtuales con replicación de almacenamiento](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Incluso sin datos precargados en el destino de replicación del sistema HANA, necesitaría al menos 64 GB de memoria y, además, memoria suficiente para mantener los datos del almacén de filas en la memoria de la instancia de destino.

#### <a name="using-hana-system-replication-without-auto-failover-and-with-data-pre-load"></a>Uso de replicación del sistema HANA sin conmutación automática por error y con datos precargados
La diferencia con el escenario presentado anteriormente es que los datos, que se replican en la instancia de HANA de la segunda máquina virtual, están precargados. Esto eliminaría las dos ventajas que puede tener con el escenario sin datos precargados. En este caso, no puede ejecutar otro sistema SAP HANA en la segunda máquina virtual. Ni puede usar un tamaño de máquina virtual más pequeño. De ahí que sea un escenario que apenas se implementa con los clientes.


### <a name="using-sap-hana-system-replication-with-automatic-failover"></a>Uso de replicación del sistema SAP HANA con conmutación automática por error

La configuración de disponibilidad estándar dentro de una región de Azure, donde la mayoría de los clientes van a realizar la implementando con SAP HANA, es una configuración donde las dos máquinas virtuales de Azure que ejecutan Linux SLES tiene definido un clúster de conmutación por error. El clúster de Linux de SLES se basa en la plataforma [Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) en combinación con un dispositivo [STONITH](http://linux-ha.org/wiki/STONITH). Desde una perspectiva de SAP HANA, el modo de replicación usado se sincroniza y se configura una conmutación automática por error. En la segunda máquina virtual, la instancia de SAP HANA actúa como nodo en espera activo, que recibe una secuencia sincrónica de registros de cambios de la instancia principal de SAP HANA. A medida que las transacciones se confirman en la aplicación en el nodo principal de HANA, dicho nodo espera hasta que el nodo secundario de SAP HANA confirma que ha recibido el registro de confirmación. SAP HANA presenta dos modos diferentes de replicación sincrónica. Para más información y conocer las diferencias entre estos dos modos de replicación sincrónica, lea el artículo [Replication modes for SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html) (Modos de replicación en la replicación del sistema SAP HANA).

La configuración general se parece a esta:

![Dos máquinas virtuales con replicación de almacenamiento y conmutación por error](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Esta solución se elige porque le permite conseguir un RPO=0 y tiempos extremadamente bajos de RTO. Configure la conectividad del cliente de SAP HANA de forma que use la dirección IP virtual para conectarse a la configuración de replicación del sistema HANA. De esta forma, se elimina la necesidad de volver a configurar la aplicación en el caso de una conmutación por error al nodo secundario. En esta solución, las SKU de máquina virtual de Azure para el nodo principal y secundario deben ser iguales.  


## <a name="next-steps"></a>Pasos siguientes
Si necesita instrucciones paso a paso sobre cómo establecer una configuración así en Azure, lea los artículos:

- [Configuración de la replicación del sistema de SAP HANA en máquinas virtuales de Azure](sap-hana-high-availability.md)
- [Your SAP on Azure – Part 4 – High Availability for SAP HANA using System Replication](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/) (Su SAP en Azure [Parte 4]: alta disponibilidad en SAP HANA mediante la replicación del sistema)

Si necesita más información sobre la disponibilidad de SAP HANA en regiones de Azure, lea:

- [Disponibilidad de SAP HANA entre regiones de Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

