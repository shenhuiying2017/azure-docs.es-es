---
title: Replicación de Hyper-V con Azure Site Recovery | Microsoft Docs
description: Use este artículo para comprender los conceptos técnicos que le ayudan a instalar, configurar y administrar correctamente Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: Rajani-Janaki-Ram
manager: mkjain
editor: ''

ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/12/2016
ms.author: rajanaki

---
# <a name="hyper-v-replication-with-azure-site-recovery"></a>Replicación de Hyper-V con Azure Site Recovery
En este artículo se describen los conceptos técnicos que le ayudarán a configurar y administrar correctamente la protección de sitios de Hyper-V o de System Center Virtual Machine Manager (VMM) con Azure mediante Azure Site Recovery.

## <a name="setting-up-the-source-environment-for-replication-between-on-premises-and-azure"></a>Configuración del entorno de origen para la replicación entre una ubicación local y Azure
Como parte de la configuración de recuperación ante desastres entre una ubicación local y Azure, asegúrese de descargar e instalar el proveedor de Azure Site Recovery en el servidor VMM. Instale el agente de Azure Recovery Services en cada host de Hyper-V.

![Implementación del sitio de VMM para la replicación entre el sitio local y Azure](media/site-recovery-understanding-site-to-azure-protection/image00.png)

La configuración del entorno de origen en una infraestructura administrada de Hyper-V es similar a la configuración del entorno de origen en una infraestructura administrada de VMM. La única diferencia es que el proveedor y el agente están instalados en el propio host de Hyper-V. En el entorno de VMM, el proveedor está instalado en el servidor VMM y el agente, en los hosts de Hyper-V (en caso de replicación en Azure).

## <a name="workflows"></a>Flujos de trabajo
### <a name="enable-protection"></a>Habilitar protección
Después de proteger una máquina virtual en Azure Portal o de forma local, se inicia un trabajo de Site Recovery denominado **Habilitar protección** . Puede supervisarlo en la pestaña **Trabajos** .

![Trabajo "Habilitar protección" en la lista de trabajos](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

El trabajo **Habilitar protección** comprueba los requisitos previos antes de invocar el método [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) . Este método crea la replicación en Azure mediante el uso de entradas que se configuran durante la protección.

El trabajo **Habilitar protección** comienza la replicación inicial en el entorno local mediante la invocación del método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) . Este método envía los discos virtuales de la máquina virtual a Azure.

![Detalles del trabajo "Habilitar protección"](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### <a name="finalize-protection-on-the-virtual-machine"></a>Finalizar la protección en la máquina virtual
Cuando se desencadena la replicación inicial, se toma una [instantánea de la máquina virtual de Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) . Los discos duros virtuales se procesan uno a uno hasta que se cargan todos los discos en Azure. Normalmente, esta operación tarda un rato en finalizar en función del tamaño del disco y el ancho de banda. Para optimizar el uso de la red, consulte cómo [administrar el uso de ancho de banda de red para la protección en entornos locales en Azure](https://support.microsoft.com/kb/3056159).

Una vez finalizada la replicación inicial, el trabajo **Finalizar la protección en la máquina virtual** configura las opciones de red y posteriores a la replicación. Mientras la replicación inicial está en curso:

* Se realiza un seguimiento de todos los cambios en los discos. 
* Se consume un almacenamiento de disco adicional para la instantánea y los archivos de registro de réplica de Hyper-V (HRL).

Al finalizar la replicación inicial, se elimina la instantánea de máquina virtual de Hyper-V. Esta eliminación genera la combinación de los cambios de datos después de la replicación inicial en el disco primario.

![Trabajo "Finalizar la protección en la máquina virtual" en la lista de trabajos](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### <a name="delta-replication"></a>Replicación diferencial
Hyper-V Replica Replication Tracker, que forma parte del motor de replicación de réplicas de Hyper-V, realiza un seguimiento de los cambios en un disco duro virtual, en forma de archivos de registro de réplica de Hyper-V (*.hrl). Los archivos HRL se encuentran en el mismo directorio que los discos asociados.

Cada disco configurado para la replicación tiene un archivo HRL asociado. Este registro se envía a la cuenta de almacenamiento del cliente una vez completada la replicación inicial. Cuando un registro se encuentra en tránsito hacia Azure, los cambios en el archivo principal se siguen en otro archivo de registro del mismo directorio.

Durante la replicación inicial o la replicación diferencial, puede supervisar el estado de replicación de la máquina virtual en la vista de la máquina virtual, tal como se menciona en [Supervisar el estado de replicación de la máquina virtual](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine).  

### <a name="resynchronization"></a>Resincronización
Una máquina virtual se marca para resincronización cuando la replicación diferencial da error y la replicación inicial completa es costosa en términos de ancho de banda de red o de tiempo. Por ejemplo, cuando el tamaño de archivo HRL llega al 50 por ciento del tamaño total del disco, la máquina virtual se marca para resincronización. La resincronización minimiza la cantidad de datos enviados a través de la red mediante el cálculo de sumas de comprobación de los discos de máquina virtual de origen y destino, y el envío solo de la replicación diferencial.

Una vez finalizada la resincronización, se debe reanudar la replicación diferencial normal. Si se produce una interrupción de red u otro tipo de interrupción, se puede reanudar la resincronización.

De forma predeterminada, la resincronización programada automáticamente se configura para que se lleve a cabo fuera del horario de oficina. Si necesita volver a sincronizar manualmente la máquina virtual, seleccione la máquina virtual en el portal y haga clic en **Volver a sincronizar**.

![Resincronización manual](media/site-recovery-understanding-site-to-azure-protection/image04.png)

La resincronización utiliza un algoritmo de fragmentación de bloques fijos donde los archivos de origen y destino se dividen en fragmentos fijos. Se generan sumas de comprobación para cada fragmento y, luego, se comparan para determinar qué bloques del origen se deben aplicar al destino.

### <a name="retry-logic"></a>Lógica de reintento
Existe una lógica de reintento integrada para los errores de replicación. Esta lógica se puede clasificar en dos categorías:

| Categoría | Escenarios |
| --- | --- |
| Error no recuperable |No se realiza ningún reintento. El estado de replicación de la máquina virtual es **Crítico**y se requiere la intervención de un administrador. Algunos ejemplos son:  <ul><li>Cadena rota de VHD</li><li>Estado no válido para la máquina virtual de réplica</li><li>Error de autenticación de red</li><li>Error de autorización</li><li>Máquina virtual que no se encuentra, en el caso de un servidor de Hyper-V independiente</li></ul> |
| Error recuperable |Los reintentos se producen con cada intervalo de replicación mediante el uso de la interrupción exponencial, lo que aumenta el intervalo de reintentos desde el inicio del primer intento (1, 2, 4, 8, 10 minutos). Si el error persiste, reintente cada 30 minutos. Algunos ejemplos son:  <ul><li>Error de red</li><li>Espacio de disco bajo</li><li>Condición de memoria baja</li></ul> |

## <a name="hyper-v-virtual-machine-protection-and-recovery-life-cycle"></a>Ciclo de vida de protección y recuperación de una máquina virtual de Hyper-V
![Ciclo de vida de protección y recuperación de una máquina virtual de Hyper-V](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## <a name="other-references"></a>Otras referencias
* [Protección de supervisión y solución de problemas para los VMware, VMM, Hyper-V y sitios físicos](site-recovery-monitoring-and-troubleshooting.md)
* [Contacto con el soporte técnico de Microsoft](site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
* [Errores comunes de Azure Site Recovery y sus soluciones](site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)

<!--HONumber=Oct16_HO2-->


