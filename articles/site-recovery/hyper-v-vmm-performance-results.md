---
title: "Resultados de pruebas de replicación de máquinas virtuales de Hyper-V en nubes de VMM en un sitio secundario con Azure Site Recovery | Microsoft Docs"
description: "Este artículo proporciona información acerca de la prueba de rendimiento de la replicación de máquinas virtuales de Hyper-V en nubes de VMM en un sitio secundario con Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2018
ms.author: raynew
ms.openlocfilehash: e15f435a3f32b8908b5b93bccc6c57710ab589bc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Resultados de pruebas para la replicación de Hyper-V a un sitio secundario

Este artículo proporciona los resultados de las pruebas de rendimiento al replicar máquinas virtuales de Hyper-V en nubes de System Center Virtual Machine Manager (VMM) en un centro de datos secundario.

## <a name="test-goals"></a>Objetivos de la prueba

El objetivo de la prueba era examinar el rendimiento de Site Recovery durante la replicación de estado estable.

- La replicación de estado estable se produce cuando se ha completado la replicación inicial de las máquinas virtuales y se sincronizan los cambios diferenciales.
- Es importante medir el rendimiento mediante el estado estable porque es el estado en el que la mayoría de las máquinas virtuales permanece, a menos que se produzcan interrupciones inesperadas.
- La implementación de prueba estaba formada por dos sitios locales con un servidor VMM en cada sitio. Esta implementación de prueba es típica de una implementación de oficina central y sucursales, donde la oficina central actúa como el sitio principal y la sucursal como el sitio secundario o de recuperación.

## <a name="what-we-did"></a>¿Qué hicimos?

Aquí está lo que hicimos en la prueba superada:

1. Se crearon las máquinas virtuales con plantillas de VMM.
2. Se iniciaron las máquinas virtuales y se capturaron las métricas de rendimiento de la base de referencia durante más de 12 horas.
3. Se crearon nubes en los servidores de VMM principal y de recuperación.
4. Se configuró la replicación en Site Recovery, incluida la asignación de nubes de origen y de recuperación.
5. Se habilitó la protección de las máquinas virtuales y se permitió finalizar la replicación inicial.
6. Espere un par de horas a que se haga la estabilización del sistema.
7. Se capturaron las métricas de rendimiento durante 12 horas, en las que todas las máquinas virtuales permanecen en el estado de replicación esperado durante esas 12 horas.
8. Se midió la diferencia entre las métricas de rendimiento de la base de referencia y las métricas de rendimiento de la replicación.


## <a name="primary-server-performance"></a>Rendimiento del servidor principal

* La réplica de Hyper-V (utilizada por Site Recovery) realiza un seguimiento asincrónico de los cambios en un archivo de registro con una sobrecarga de almacenamiento mínima en el servidor principal.
* La réplica de Hyper-V utiliza la memoria caché de mantenimiento automático para minimizar la sobrecarga de IOPS para el seguimiento. Almacena escrituras en el archivo VHDX en la memoria y las vacía en el archivo de registro antes de que el registro se envíe al sitio de recuperación. También se produce un vaciado en disco si las escrituras alcanzan un límite predeterminado.
* El gráfico siguiente muestra la sobrecarga IOPS del estado estable para la replicación. Podemos ver que, debido a la replicación, la sobrecarga de IOPS es aproximadamente un 5%, que es bastante bajo.

  ![Resultados principales](./media/hyper-v-vmm-performance-results/IC744913.png)

La réplica de Hyper-V utiliza la memoria del servidor principal para optimizar el rendimiento de disco. Como se muestra en el siguiente gráfico, la sobrecarga de memoria en todos los servidores del clúster principal es marginal. La sobrecarga de memoria mostrada es el porcentaje de memoria utilizado por la replicación con respecto a la memoria total instalada en el servidor de Hyper-V.

![Resultados principales](./media/hyper-v-vmm-performance-results/IC744914.png)

La réplica de Hyper-V tiene una sobrecarga de CPU como mínimo. Como se muestra en el gráfico, la sobrecarga de replicación está en el intervalo de % 2-3%.

![Resultados principales](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Rendimiento del servidor secundario

La réplica de Hyper-V usa una pequeña cantidad de memoria en el servidor de recuperación para optimizar el número de operaciones de almacenamiento. El gráfico resume el uso de la memoria en el servidor de recuperación. La sobrecarga de memoria mostrada es el porcentaje de memoria utilizado por la replicación con respecto a la memoria total instalada en el servidor de Hyper-V.

![Resultados secundarios](./media/hyper-v-vmm-performance-results/IC744916.png)

La cantidad de operaciones de E/S en el sitio de recuperación es una función del número de operaciones de escritura en el sitio principal. Veamos el total de operaciones de E/S en el sitio de recuperación en comparación con el total de operaciones de E/S y las operaciones de escritura en el sitio principal. Los gráficos muestran que el total de IOPS en el sitio de recuperación es

* Alrededor de 1,5 veces la IOPS de escritura en el servidor principal.
* Alrededor del 37% del número total de IOPS en el sitio principal.

![Resultados secundarios](./media/hyper-v-vmm-performance-results/IC744917.png)

![Resultados secundarios](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Repercusión en el uso de la red

Se usó un promedio de 275 MB por segundo de ancho de banda de la red entre los nodos principal y de recuperación (con compresión habilitada) con un ancho de banda existente de 5 GB por segundo.

![Utilización de la red de resultados](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Repercusión en el rendimiento de la máquina virtual

Una consideración importante es el impacto de la replicación en las cargas de trabajo de producción que se ejecutan en las máquinas virtuales. Si el sitio principal está adecuadamente aprovisionado para replicación, no debe haber ningún impacto en las cargas de trabajo. El mecanismo de seguimiento ligero de la réplica de Hyper-V garantiza que las cargas de trabajo que se ejecutan en las máquinas virtuales no se vean afectadas durante la replicación en estado estable. Esto se muestra en los siguientes gráficos.

Este gráfico muestra el IOPS realizado por máquinas virtuales que ejecutan distintas cargas de trabajo antes y después de habilitar la replicación. Puede observar que no hay ninguna diferencia entre los dos.

![Resultados del efecto de réplica](./media/hyper-v-vmm-performance-results/IC744920.png)

El siguiente gráfico muestra el rendimiento de las máquinas virtuales que ejecutan distintas cargas de trabajo antes y después de habilitar la replicación. Puede observar que la replicación no tiene ningún impacto significativo.

![Resultado de efectos de la réplica](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Conclusión

Los resultados muestran claramente que Site Recovery, junto con la réplica de Hyper-V, se escala bien con una sobrecarga mínima para un clúster grande. Site Recovery proporciona una implementación, replicación, administración y supervisión simples. La réplica de Hyper-V proporciona la infraestructura necesaria para el escalado de replicación correcta. 

## <a name="test-environment-details"></a>Detalles del entorno de la prueba

### <a name="primary-site"></a>Sitio principal

* El sitio principal tiene un clúster que contiene cinco servidores de Hyper-V que ejecutan 470 máquinas virtuales.
* Las máquinas virtuales ejecutan distintas cargas de trabajo y todas tienen habilitada la protección de Site Recovery.
* Un SAN iSCSI proporciona el almacenamiento para el nodo de clúster. Modelo: Hitachi HUS130.
* Cada servidor del clúster tiene cuatro tarjetas de red (NIC) de un Gbps cada una.
* Dos de las tarjetas de red están conectadas a una red privada iSCSI y dos están conectadas a una red de empresa externa. Una de las redes externas está reservada solo para las comunicaciones del clúster.

![Requisitos de hardware principal](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Modelo | Procesador | Número de procesadores | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servidores de Hyper-V en clúster:  <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128ESTLAB-HOST25 tiene 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 a 2,20 GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + rol de Hyper-V |
| Servidor VMM |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x 64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Sitio secundario

* El sitio secundario tiene un clúster de conmutación por error de seis nodos.
* Un SAN iSCSI proporciona el almacenamiento para el nodo de clúster. Modelo: Hitachi HUS130.

![Especificación de hardware principal](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Modelo | Procesador | Número de procesadores | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servidores de Hyper-V en clúster:  <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 a 2,30 GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + rol de Hyper-V |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 a 2,20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + rol de Hyper-V |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 a 2,20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + rol de Hyper-V |
| Servidor VMM |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x 64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Cargas de trabajo del servidor

* Para fines de prueba, elegimos cargas de trabajo que se utilizan normalmente en escenarios de clientes de empresa.
* Utilizamos [IOMeter](http://www.iometer.org) con la característica de carga de trabajo resumida en la tabla para la simulación.
* Todos los perfiles de IOMeter se establecen para escribir bytes aleatorios para simular el peor de los patrones de escritura de las cargas de trabajo.

| Carga de trabajo | Tamaño de E/S (KB) | % de acceso | % de lectura | Operaciones de E/s pendientes | Patrón de E/S |
| --- | --- | --- | --- | --- | --- |
| Servidor de archivos |48163264 |60% 20 %5 %5% 10% |80% de 80 80% 80% 80% |88888 |Todos 100% aleatorios |
| SQL Server (volumen 1) SQL Server (volumen 2) |864 |100% 100% |70% %0 |88 |100% aleatorio 100% secuencial |
| Exchange |32 |100% |67% |8 |100% aleatorio |
| Estación de trabajo/VDI |464 |66% 34% |70% 95% |11 |Los dos 100% aleatorios |
| Servidor de archivos web |4864 |33% 34% 33% |95% 95 95% |888 |Todos 75% aleatorios |

### <a name="vm-configuration"></a>Configuración de VM

* 470 máquinas virtuales en el clúster principal.
* Todas las máquinas virtuales con disco VHDX.
* Las máquinas virtuales ejecutan las cargas de trabajo resumidas en la tabla. Todas se crearon con plantillas de VMM.

| Carga de trabajo | N.º de máquinas virtuales | RAM mínima (GB) | RAM máxima (GB) | Tamaño de disco lógico (GB) por máquina virtual | Número máximo de IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Servidor de archivos |50 |1 |2 |552 |22 |
| VDI |149 |0,5 |1 |80 |6 |
| Servidor Web |149 |0,5 |1 |80 |6 |
| TOTAL |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Configuración de Site Recovery

* Site Recovery se configuró para la protección de local a local
* El servidor VMM tiene configuradas cuatro nubes, que contienen los servidores del clúster de Hyper-V y sus máquinas virtuales.

| Nube de VMM principal | Máquinas virtuales protegidas | Frecuencia de replicación | Puntos de recuperación adicionales |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minutos |None |
| PrimaryCloudRpo30s |47 |30 segundos |None |
| PrimaryCloudRpo30sArp1 |47 |30 segundos |1 |
| PrimaryCloudRpo5m |235 |5 minutos |None |

### <a name="performance-metrics"></a>Métricas de rendimiento

La tabla resume las métricas de rendimiento y contadores que se midieron en la implementación.

| Métrica | Contador |
| --- | --- |
| CPU |Procesador(_Total)\% Hora del procesador |
| Memoria disponible |\Memoria\MB disponibles |
| E/S |\Disco físico(_Total)\Transferencias de disco/s |
| Operaciones de lectura de máquinas virtuales por segundo (IOPS)/s |\Dispositivo de almacenamiento virtual de Hyper-V(<VHD>)\Operaciones de lectura/s |
| Operaciones de escritura de máquinas virtuales por segundo (IOPS)/s |\Dispositivo de almacenamiento virtual de Hyper-V(<VHD>)\Operaciones de escritura/s |
| Rendimiento de lectura de máquinas virtuales |\Dispositivo de almacenamiento virtual de Hyper-V(<VHD>)\Bytes leídos/s |
| Rendimiento de escritura de máquinas virtuales |\Dispositivo de almacenamiento virtual de Hyper-V(<VHD>)\Bytes escritos/s |

## <a name="next-steps"></a>pasos siguientes

[Configuración de la replicación](hyper-v-vmm-disaster-recovery.md)
