---
title: "Diseñar e implementar una base de datos de Oracle en Azure | Microsoft Docs"
description: "Diseñe e implemente una base de datos de Oracle en el entorno de Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/22/2017
ms.author: rclaus
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e13e61a2d055ce4f9777cea8bf6199f2acf9e7bf
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---


# <a name="design-and-implement-of-oracle-database-on-azure"></a>Diseñar e implementar una base de datos de Oracle en Azure

## <a name="assumptions"></a>Supuestos

- Planear la migración de una base de datos Oracle del entorno local a Azure
- Entender las diversas métricas de los informes de AWR de Oracle
- Tener una base de referencia para el rendimiento de la aplicación y el uso de la plataforma

## <a name="goals"></a>Objetivos

- Entender cómo optimizar la implementación de Oracle en Azure
- Explorar las opciones de optimización del rendimiento de la base de datos de Oracle en el entorno de Azure

### <a name="on-premises-vs-on-azure"></a>Entorno local frente a Azure

A continuación se indican algunas consideraciones importantes que se deben tener en cuenta al migrar aplicaciones locales a Azure. Los recursos de Azure (máquina virtual, discos, red virtual, etc.) se comparten con otros clientes, a diferencia de los recursos locales. Además, los recursos se pueden limitar en función de los requisitos. En lugar de centrarse en evitar errores (MTBF), Azure se centra más bien en superar los errores (MTTR).

En la tabla siguiente se indican algunas de las diferencias.

> 
> |  | **Entorno local** | **Las tablas de Azure** |
> | --- | --- | --- |
> | **Redes** |LAN/WAN  |SDN (Redes definidas por software)|
> | **Grupo de seguridad** |Herramientas de restricciones de IP/puerto |[Grupo de seguridad de red (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Resistencia** |MTBF (tiempo medio entre errores) |MTTR (tiempo medio para recuperación)|
> | **Mantenimiento planeado** |Aplicación de revisiones/actualizaciones|[Conjuntos de disponibilidad](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (aplicación de revisiones o actualizaciones administradas por Azure) |
> | **Recurso** |Dedicado  |Compartido con otros clientes|
> | **Regiones** |Centros de datos |[Pares de región](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **Storage** |SAN/discos físicos |[Almacenamiento administrado por Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Escala** |Escala vertical |Escala horizontal|


### <a name="requirements"></a>Requisitos

- Determinar el tamaño de base de datos y la tasa de crecimiento
- Determinar los requisitos de E/S por segundo, que pueden calcularse en función del informe de AWR de Oracle o de otras herramientas de supervisión de red

## <a name="configuration-options"></a>Opciones de configuración

Hay cuatro áreas que se pueden ajustar para mejorar el rendimiento en el entorno de Azure.

- Tamaño de la máquina virtual
- Capacidad de proceso de la red
- Tipos y configuraciones de disco
- Configuración de la caché de disco

### <a name="generate-awr-report"></a>Generar un informe de AWR

Si ya dispone de una base de datos de Oracle y planea migrar a Azure, puede ejecutar el informe de AWR de Oracle para obtener las métricas (E/S por segundo, Mbps, GB, etc.) y, después, elegir la máquina virtual en función de las métricas recopiladas. También puede ponerse en contacto con el equipo de infraestructura para obtener información similar.

Puede considerar la posibilidad de ejecutar el informe de AWR durante la carga de trabajo normal y máxima, para comparar la diferencia. En función de estos informes, puede ajustar el tamaño de las máquinas virtuales según la carga de trabajo media o máxima.

Aquí se muestra un ejemplo de cómo generar un informe de AWR.

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Métricas clave

A continuación se muestran las métricas que se pueden obtener a partir del informe de AWR.

- Número total de núcleos
- Velocidad del reloj de la CPU
- Memoria total en GB
- Uso de CPU
- Velocidad de transferencia de datos máxima
- Frecuencia de cambios de E/S (lectura/escritura)
- Velocidad de los registros de rehacer (Mbps)
- Capacidad de proceso de la red
- Tasa de latencia de red (baja/alta)
- Tamaño de la base de datos en GB
- Bytes recibidos a través de SQL*Net desde/hacia el cliente

### <a name="virtual-machine-size"></a>Tamaño de la máquina virtual

#### <a name="1-initial-estimate-of-vm-size-is-based-on-cpumemoryio-usage-from-the-awr-report"></a>1. La estimación inicial del tamaño de la máquina virtual se basa en el uso de CPU, memoria o E/S según el informe de AWR.

Puede consultar los cinco eventos de primer plano cronometrados de la parte superior, que indican dónde están los cuellos de botella del sistema.

Por ejemplo, en el diagrama siguiente, la sincronización del archivo de registro está en la parte superior, que es el número de esperas para que LGWR escriba el búfer de registro en el archivo de registro de rehacer. Esto indica que es necesario un mejor rendimiento en el almacenamiento o los discos. Además, también se muestra en el diagrama el número de CPU (núcleos) y la memoria.

![Captura de pantalla de la página del informe de AWR](./media/oracle-design/cpu_memory_info.png)

En el diagrama siguiente se muestra el total de E/S de lectura y escritura. Hay 59 GB de lectura y 247,3 GB de escritura durante la realización del informe.

![Captura de pantalla de la página del informe de AWR](./media/oracle-design/io_info.png)

#### <a name="2-estimate-the-vm-size"></a>2. Calcular el tamaño de la máquina virtual

En función de la información recopilada en el informe de AWR, el paso siguiente consiste en elegir una máquina virtual con un tamaño similar que cumpla los requisitos. En este artículo sobre los [tamaños de las máquinas virtuales](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes-memory) se muestra una lista de las máquinas virtuales disponibles.

#### <a name="3-fine-tune-the-vm-sizing-with-similar-vm-series-based-on-the-acu"></a>3. Ajustar el tamaño de la máquina virtual con series de máquinas virtuales similares en función de la ACU

Una vez que haya elegido las máquinas virtuales, preste atención a la ACU (unidad de proceso de Azure) de las máquinas virtuales. Puede elegir una máquina virtual similar en función del valor de ACU que mejor se adapte a sus necesidades. Para obtener más información, vea [Unidad de proceso de Azure (ACU)](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Captura de pantalla de la página de ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Capacidad de proceso de la red

A continuación se muestra la relación que existe entre el rendimiento y la E/S por segundo.

![Captura de pantalla del rendimiento](./media/oracle-design/throughput.png)

El rendimiento de red total se calcula en función de lo siguiente:
- Tráfico de SQL*Net
- Mbps x número de servidores (secuencia de salida, como la protección de datos)
- Otros factores, como la replicación de aplicaciones

![Captura de pantalla del rendimiento de SQL*Net](./media/oracle-design/sqlnet_info.png)

En función de los requisitos de ancho de banda de red, puede elegir diferentes tipos de puerta de enlace, como Basic, VpnGw o ExpressRoute. Puede obtener más información en la [página de precios de VPN Gateway](https://azure.microsoft.com/en-us/pricing/details/vpn-gateway/?v=17.23h).

Recomendaciones

- La latencia de red es superior en comparación con una implementación local. El rendimiento puede mejorar considerablemente si se reducen los recorridos de ida y vuelta de red.
- Consolide las aplicaciones que tengan transacciones elevadas o estén "fragmentadas" en la misma máquina virtual para reducir los recorridos de ida y vuelta.

### <a name="disk-types-and-configurations"></a>Tipos y configuraciones de disco

- Disco predeterminado del sistema operativo: con datos persistentes y almacenamiento en caché. Este disco está optimizado para el acceso del sistema operativo en tiempo de arranque; no está diseñado para cargas de trabajo transaccionales o analíticas (de almacenamiento de datos).

- Discos no administrados: es usted quien administra las cuentas de almacenamiento que usa para almacenar los archivos del disco duro virtual (VHD) que se corresponden con los discos de VM. Los archivos VHD se almacenan como blobs de páginas en las cuentas de Azure Storage.

- Discos administrados: Azure administra las cuentas de almacenamiento que usa para los discos de máquina virtual. Especifique el tipo de disco (Premium o Standard) y el tamaño del disco que necesite. Azure crea y administra el disco en su nombre.

- Discos de Premium Storage (indicados para cargas de trabajo de producción): Premium Storage admite discos de máquina virtual que se pueden conectar a máquinas virtuales de varias series de tamaños concretos, como las series DS, DSv2, GS o F. El disco Premium tiene diferentes tamaños entre los que puede elegir, de 32 GB a 4096 GB. Cada tamaño de disco tiene sus propias especificaciones de rendimiento. En función de los requisitos de la aplicación puede conectar uno o varios discos a la VM.

Cuando se crea un disco administrado desde el portal, puede elegir el tipo de cuenta para el tipo de disco específico que quiere usar. Es importante saber que en el cuadro desplegable no se muestran todos los discos disponibles. Esto se debe a que Azure Storage está integrado con las máquinas virtuales y los límites de las SKU (por ejemplo, número máximo de unidades, E/S por segundo máxima). Por lo tanto, cuando elija un tamaño determinado de máquina virtual, solo se muestran las SKU de Premium Storage disponibles en función de ese tamaño de máquina virtual.

![Captura de pantalla de la página de disco administrado](./media/oracle-design/premium_disk01.png)

Para obtener más información, vea [Premium Storage de Azure](https://docs.microsoft.com/azure/storage/storage-premium-storage).

Una vez configurado el almacenamiento en una máquina virtual, tal vez le interese realizar una prueba de carga en los discos antes de crear una base de datos. Puede ser útil conocer la velocidad de E/S en términos de latencia y rendimiento para ayudarle a determinar si las máquinas virtuales admiten el rendimiento esperado con destinos de latencia.

Existen diversas herramientas para realizar pruebas de carga de aplicaciones, como Oracle Orion, Sysbench, Fio, etc.

Ejecute la prueba de carga de nuevo después de implementar la base de datos de Oracle e inicie las cargas de trabajo normal y máxima. En los resultados se mostrará la base de referencia del entorno.

Puede ser más importante cambiar el tamaño del almacenamiento según la tasa de E/S por segundo, en vez de según el tamaño de almacenamiento. Por ejemplo, si la E/S por segundo requerida es 5000 pero solo necesita 200 GB, puede obtener el disco Premium de clase P30 aunque tenga un tamaño de almacenamiento superior a 200 GB.

La tasa de IOPS puede obtenerse a partir del informe de AWR. Viene determinada por el registro de rehacer, las lecturas físicas y la velocidad de escritura.

![Captura de pantalla de la página del informe de AWR](./media/oracle-design/awr_report.png)

Por ejemplo: el tamaño de rehacer es 12 200 000 bytes por segundo, lo que equivale a 11,63 Mbps. La E/S por segundo es 12 200 000 / 2358 = 5174.

Una vez que se haya hecho una idea de los requisitos de E/S, puede elegir la combinación de unidades más adecuada para cumplirlos.

Recomendaciones

- Para el espacio de tabla de datos, reparta la carga de trabajo de E/S entre varios discos mediante el almacenamiento administrado o la administración automática del almacenamiento (ASM) de Oracle.
- Agregue más discos de datos a medida que aumente el tamaño del bloque de E/S, para las operaciones intensivas de lectura y escritura.
- Aumente el tamaño de bloque para procesos secuenciales de gran tamaño.
- Use la compresión de datos para reducir la E/S (para datos e índices).
- Separe los registros de rehacer y los archivos TS del sistema, temporales y de deshacer en discos de datos independientes.
- Se recomienda no colocar archivos de aplicación en el disco predeterminado del sistema operativo (/dev/sda). Este disco está optimizado para el tiempo de arranque rápido de máquina virtual y podría no proporcionar un buen rendimiento para la aplicación.

### <a name="disk-cache-settings"></a>Configuración de la caché de disco

Existen tres opciones para el almacenamiento en caché de host.

- Solo lectura: todas las solicitudes se almacenan en caché para lecturas futuras. Todas las escrituras se guardan directamente en Windows Azure Storage Blob.

- Lectura/escritura: se trata de un algoritmo de "lectura anticipada". Las lecturas y las escrituras se almacenan en caché para lecturas futuras. Si no son escrituras a través, se guardan primero en la caché local. En el caso de SQL Server, las escrituras se guardan en el almacenamiento de WA, ya que usa la escritura a través y proporciona la menor latencia de disco para cargas de trabajo ligeras.

- Ninguno (deshabilitado): esto permite omitir la caché. Todos los datos se transfieren al disco y se guardan en Microsoft Azure Storage. Este método proporciona la mayor velocidad de E/S para cargas de trabajo intensivas de E/S. También debe tener en cuenta el "costo de transacción".

Recomendaciones

Para maximizar el rendimiento, se recomienda comenzar con la opción "Ninguno" para el almacenamiento en caché de host. En el caso de Premium Storage, es importante tener en cuenta que debe deshabilitar las "barreras" al montar el sistema de archivos con las opciones "Solo lectura" o "Ninguno". Actualice el archivo/etc/fstab con el UUID en los discos.

Para obtener más información, vea [Premium Storage para VM Linux](https://docs.microsoft.com/azure/storage/storage-premium-storage#premium-storage-for-linux-vms).

![Captura de pantalla de la página de disco administrado](./media/oracle-design/premium_disk02.png)

- En los discos del sistema operativo, use el almacenamiento en caché predeterminado "Lectura/escritura".
- En el caso de SYSTEM, TEMP y UNDO, use la opción "Ninguno" para el almacenamiento en caché.
- En el caso de DATA, use la opción "Ninguno" para el almacenamiento en caché, pero si la base de datos es de solo lectura o de lectura intensiva, use el almacenamiento en caché de "Solo lectura".

Una vez que se haya guardado la configuración del disco de datos, no se puede cambiar la configuración de la caché de host, a menos que se desmonte la unidad en el nivel de sistema operativo y se vuelva a montar después de cambiarla.


## <a name="security"></a>Seguridad

Una vez que haya instalado y configurado el entorno de Azure, el paso siguiente es proteger la red. Estas son algunas recomendaciones:

- Directiva del NSG

El grupo de seguridad de red (NSG) se puede definir como una subred o NIC.  Es más fácil controlar el acceso en el nivel de subred para garantizar la seguridad y forzar el enrutamiento de elementos como el firewall de aplicaciones.

- Jumpbox ![Captura de pantalla de la página de topología de Jumpbox](./media/oracle-design/jumpbox.png)

Para un acceso más seguro, los administradores no deben conectarse directamente con el servicio de aplicación o la base de datos. Se usa un Jumpbox como un medio entre la máquina del administrador y los recursos de Azure.

La máquina del administrador debería tener un acceso IP restringido al Jumpbox. El Jumpbox debe tener acceso a la aplicación y la base de datos.

- Red privada (subredes)

Se recomienda tener el servicio de aplicación y la base de datos en subredes separadas, de modo que la directiva del NSG pueda establecer un mejor control.


## <a name="additional-readings"></a>Lecturas adicionales:

- [Configuración de ASM de Oracle](configure-oracle-asm.md)
- [Configuración de la protección de datos de Oracle](configure-oracle-dataguard.md)
- [Configuración de Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Copia de seguridad y recuperación de Oracle](oracle-backup-recovery.md)

## <a name="next-steps"></a>Pasos siguientes

[Tutorial: Creación de máquinas virtuales de alta disponibilidad](../../linux/create-cli-complete.md)

[Ejemplos de la CLI de Azure para implementación de máquinas virtuales](../../linux/cli-samples.md)

