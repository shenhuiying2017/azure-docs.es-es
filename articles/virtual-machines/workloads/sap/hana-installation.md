---
title: "Instalación de SAP HANA en SAP HANA en Azure (instancias grandes) | Microsoft Docs"
description: "Instalación de SAP HANA en SAP HANA en Azure (instancia grande)."
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 89356670737ed7b9adc5df51466ce71eb73b3cd4
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Procedimiento para instalar y configurar SAP HANA en Azure (instancias grandes)

La instalación de SAP HANA es su responsabilidad y puede iniciar la actividad después de la entrega de un SAP HANA nuevo en el servidor de Azure (instancias grandes) y después de la conectividad entre las redes virtuales de Azure y las unidades de instancia grande establecidas de HANA. Tenga en cuenta que, según las directivas de SAP, la instalación de SAP HANA la debe realizar un instalador de SAP HANA certificado (alguien que haya superado el examen de certificación de Certified SAP Technology Associate - Instalación de SAP HANA), o un integrador de sistemas (SI) certificado de SAP.

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>Primeros pasos después de recibir las unidades de instancia grande de HANA

El **primer paso** después de recibir la instancia grande de HANA y establecer el acceso y la conectividad a las instancias consiste en registrar el sistema operativo de la instancia con el proveedor de este; lo cual incluye el registro del sistema operativo SUSE Linux en una instancia de SUSE SMT que debe haber implementado en una máquina virtual de Azure a la que puede conectarse la unidad de instancia grande de HANA. O bien, debe registrar el sistema operativo RedHat con la instancia de RedHat Subscription Manager a la que necesite conectarse. Vea también los comentarios de este [documento](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Este paso también es necesario para poder aplicar la revisión del sistema operativo en el futuro. Una tarea que es responsabilidad del cliente. Para SUSE, encuentre documentación para instalar y configurar SMT [aquí](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

El **segundo paso** es comprobar si hay nuevas revisiones y correcciones de la versión del sistema operativo específica. Compruebe si la versión de la instancia grande de HANA es la más reciente. En función del momento en el que Microsoft implemente las versiones/revisiones del sistema operativo y los cambios de imagen, podrían no incluirse las revisiones más recientes. Por lo tanto, comprobar si el proveedor particular de Linux ha publicado revisiones importantes para la seguridad, la funcionalidad, la disponibilidad y el rendimiento que necesiten implementarse es un paso obligatorio después de asumir una unidad de la instancia grande de HANA y haber registrado la instalación del sistema operativo con el distribuidor de Linux.

El **tercer paso** consiste en comprobar las notas de SAP correspondientes a la instalación y configuración de SAP HANA en la versión concreta del sistema operativo. Por cambios en las recomendaciones o en las notas de SAP o las configuraciones que dependen de los distintos escenarios de instalación, Microsoft no siempre podrá tener una unidad de instancia grande de HANA configurada a la perfección. Por lo tanto, es obligatorio que, como cliente, lea las notas de SAP (las siguientes como mínimo), compruebe las configuraciones de la versión del sistema operativo necesarias e implemente la configuración donde no se haya actualizado aún.

Para casos particulares, compruebe los siguientes parámetros y, si es necesario, ajústelos a:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

A partir de SP1 SLES12 y RHEL 7.2, se deben establecer estos parámetros en un archivo de configuración en el directorio /etc/sysctl.d. Por ejemplo, se debe crear un archivo de configuración con el nombre 91-NetApp-HANA.conf. Para las versiones anteriores de SLES y RHEL, estos parámetros deben establecerse en in/etc/sysctl.conf.

Para todas las versiones de RHEL y a partir de SLES12, el parámetro 
- sunrpc.tcp_slot_table_entries = 128

se debe establecer en in/etc/modprobe.d/sunrpc-local.conf. Si el archivo no existe, primero debe crearse; para ello, agregue la entrada siguiente: 
- options sunrpc tcp_max_slot_table_entries=128

El **cuarto paso** consiste en comprobar la hora del sistema de la unidad de instancia grande de HANA. Las instancias se implementarán con una zona horaria del sistema que represente la ubicación de la región de Azure donde se encuentre la marca de la instancia grande de HANA. Es libre de cambiar la hora de su sistema o la zona horaria de sus instancias. Si lo hace, al pedir más instancias para su inquilino, necesitará adaptar la zona horaria de las instancias recién entregadas. Las operaciones de Microsoft no tienen ninguna información sobre la zona horaria del sistema donde instale las instancias tras la entrega. Por lo tanto, las instancias recién implementadas pueden no tener la misma zona horaria que a las que cambió. Como resultado, es su responsabilidad como cliente comprobarlo y, si es necesario, adaptar la zona horaria de las instancias entregadas. 

El **quinto paso** consiste en comprobar etc/hosts. A medida que van entregando las hojas, se les asignan direcciones IP diferentes con fines distintos (consulte la sección siguiente). Debe comprobar etc/hosts. Cuando se agreguen unidades a un inquilino existente, no espere que etc/hosts en los sistemas recién implementados se mantenga correctamente con las direcciones IP de los sistemas entregados anteriormente. Depende de usted como cliente comprobar que la configuración sea la correcta, de manera que una instancia recién implementada pueda interactuar y resolver los nombres de las unidades implementadas anteriormente en el inquilino. 

## <a name="networking"></a>Redes
Se supone que ha seguido las recomendaciones de diseño de las redes virtuales de Azure y de conexión a las instancias grandes de HANA tal y como se describe en estos documentos:

- [Introducción y arquitectura de SAP HANA en Azure (instancias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infraestructura y conectividad con SAP HANA en Azure (instancias grandes)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Hay algunos detalles que vale la pena mencionar acerca de las funciones de red de las unidades individuales. Cada unidad de instancia grande de HANA viene con dos o tres direcciones IP asignadas a dos o tres puertos NIC de la unidad. En las configuraciones de escalado horizontal de HANA y el escenario de replicación del sistema de HANA se utilizan tres direcciones IP. Una de las direcciones IP asignadas a la NIC de la unidad está fuera del grupo de direcciones IP del servidor que se describió en la [Introducción y arquitectura de SAP HANA en Azure (instancias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

La distribución para las unidades con dos direcciones IP asignadas debería ser como lo siguiente:

- eth0.xx debe tener una dirección IP asignada fuera del intervalo de direcciones del grupo de direcciones IP de servidor que envió a Microsoft. Esta dirección IP se usará para el mantenimiento en /etc/hosts del sistema operativo.
- eth1.xx debe tener una dirección IP asignada, que se usa para la comunicación con NFS. Por lo tanto, estas direcciones **NO** necesitan permanecer en etc/hosts para permitir el tráfico de instancia a instancia dentro del inquilino.

Para los casos de implementación de la replicación del sistema de HANA o el escalado horizontal de HANA, una configuración de hoja con dos direcciones IP asignadas no es adecuada. En el caso de solo dos direcciones IP asignadas, si quiere implementar este tipo de configuración, póngase en contacto con el equipo de Microsoft Service Management para obtener una tercera dirección IP en otra red VLAN asignada. Para las unidades de instancia grande de HANA con tres direcciones IP asignadas en tres puertos NIC, se aplica lo siguiente:

- eth0.xx debe tener una dirección IP asignada fuera del intervalo de direcciones del grupo de direcciones IP de servidor que envió a Microsoft. Por lo tanto, esta dirección IP no se usará para el mantenimiento en /etc/hosts del sistema operativo.
- eth1.xx debe tener una dirección IP asignada, que se usa para la comunicación con el almacenamiento NFS. Por lo tanto, este tipo de direcciones no debe permanecer en etc/hosts.
- eth2.xx debe usarse exclusivamente para el mantenimiento en etc/hosts, para la comunicación entre las distintas instancias. Estas también serían las direcciones IP que deben mantenerse en las configuraciones de escalado horizontal de HANA como las direcciones IP que HANA usa para la configuración entre nodos.



## <a name="storage"></a>Storage

El diseño de almacenamiento para SAP HANA en Azure (instancias grandes) lo configura SAP HANA en Azure Service Management a través de procedimientos recomendados de SAP. Consulte las notas del producto [SAP HANA Storage Requirements](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Requisitos de almacenamiento de SAP HANA). Según vaya leyendo el documento y buscando una unidad de instancia grande de HANA, se dará cuenta de que las unidades vienen con un volumen de disco bastante amplio para HANA/data y que tenemos un volumen HANA/log/backup. El motivo por el que el tamaño de HANA/data es tan grande es que las instantáneas de almacenamiento que le ofrecemos como cliente usan el mismo volumen de disco. Esto significa cuantas más instantáneas de almacenamiento realice, más espacio necesitan. El volumen de HANA/log/backup no se considera el volumen donde colocar las copias de seguridad de base de datos, sino que se aprovecha como volumen de copia de seguridad para el registro de transacciones de HANA. En futuras versiones del autoservicio de instantáneas de almacenamiento se aplicará a este volumen específico para que tenga instantáneas con mayor frecuencia y con replicaciones más frecuentes en el sitio de recuperación ante desastres (si desea participar en la funcionalidad de recuperación ante desastres de la infraestructura de la instancia grande de HANA). Consulte los detalles en [Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes)](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Además del almacenamiento proporcionado, puede adquirir capacidad de almacenamiento adicional en incrementos de 1 TB. Este almacenamiento adicional se puede agregar como nuevos volúmenes a instancias grandes de HANA.

Durante la incorporación de Microsoft Service Management, el cliente especifica un identificador de usuario (UID) e Id. de grupo (GID) para el <SID>grupo de usuario y sapsys ADM (p. ej.: 1000,500). Es necesario que durante la instalación del sistema SAP HANA, se utilicen estos mismos valores.

El controlador de almacenamiento y los nodos de las marcas de instancia grandes se sincronizan con los servidores NTP. Mediante la sincronización de SAP HANA en unidades de Azure (instancias grandes) y Azure Virtual Machines con un servidor NTP, no debería haber un desfase de tiempo significativo entre la infraestructura y las unidades de proceso de Azure o las marcas de instancias grandes.

Con el fin de optimizar SAP HANA para el almacenamiento subyacente, también debe establecer los parámetros de configuración de SAP HANA siguientes:

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
En las versiones de SAP HANA 1.0 hasta SPS12, estos parámetros se pueden establecer durante la instalación de la base de datos de SAP HANA, tal y como se describe en la [nota de SAP n.º 2267798: Configuración de la base de datos de SAP HANA](https://launchpad.support.sap.com/#/notes/2267798)

También puede configurar los parámetros después de la instalación de la base de datos de SAP HANA mediante el marco de hdbparam. 

Con SAP HANA 2.0, el marco de hdbparam está en desuso. Como resultado, los parámetros deben establecerse mediante comandos SQL. Para más información, consulte la [nota de SAP n.º 2399079: Eliminación de hdbparam en HANA 2](https://launchpad.support.sap.com/#/notes/2399079).


## <a name="operating-system"></a>Sistema operativo

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana) es la distribución de Linux instalada para SAP HANA en Azure (instancias grandes). Esta distribución particular proporciona funcionalidades específicas de SAP &quot;listas para usar&quot; (incluidos los parámetros predefinidos para ejecutar SAP en SLES de forma eficaz).

Vea [Biblioteca de recursos/Informes oficiales](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) en el sitio web de SUSE y [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) (SAP en SUSE) en la red de la comunidad de SAP (SCN) para varios recursos útiles relacionados con la implementación de SAP HANA en SLES (incluida la configuración de alta disponibilidad, el endurecimiento de la seguridad específico de las operaciones de SAP etc.).

Vínculos adicionales y útiles relacionados con SLES:

- [SAP HANA on SUSE Linux Site](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) (Sitio de SAP HANA en SUSE Linux)
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113) (Procedimiento recomendado para SAP: poner en cola la replicación - SAP NetWeaver en SUSE Linux Enterprise 12)
- [ClamSAP – SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (ClamSAP: protección contra virus de SLES para SAP), incluido SLES 12 para SAP Applications

Notas de compatibilidad con SAP aplicables a la implementación de SAP HANA en SLES 12 SP1:

- [SAP Support Note #1944799 – SAP HANA Guidelines for SLES Operating System Installation](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html) (Nota de compatibilidad con SAP nº 1944799: instrucciones de SAP HANA para la instalación de sistema operativo)
- [SAP Support Note #2205917 – SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E) (Nota de compatibilidad con SAP nº 2205917: configuración de SO recomendada de BD de SAP HANA para SLES 12 para aplicaciones SAP)
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12:  Installation Notes](https://launchpad.support.sap.com/#/notes/1984787) (Nota de compatibilidad con SAP nº 1984787 - SUSE Linux Enterprise Server 12:  notas de instalación)
- [SAP Support Note #171356 – SAP Software on Linux:  General Information](https://launchpad.support.sap.com/#/notes/1984787) (Nota de compatibilidad con SAP nº 171356 - Software SAP en Linux: información general)
- [SAP Support Note #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070) (Nota de compatibilidad con SAP nº 1391070: soluciones UUID de Linux)

## <a name="time-synchronization"></a>Sincronización de la hora

SAP es sumamente sensible a las diferencias horarias para los distintos componentes que forman el sistema SAP. Los volcados cortos de ABAP de SAP con el título de error de ZDATE\_LARGE\_TIME\_DIFF probablemente le resulten familiares si ha estado trabajando con SAP (base) durante mucho tiempo, ya que estos volcados cortos aparecen cuando la hora del sistema de diferentes servidores o VM se distancia demasiado.

Para SAP HANA en Azure (instancias grandes), la sincronización de la hora que se realiza en Azure no se aplica a las unidades de proceso en las marcas de instancia grande. Esto no se aplica para ejecutar aplicaciones de SAP de forma nativa en Azure (en VM), ya que Azure garantiza que la hora del sistema está correctamente sincronizada. Como consecuencia de esto, se debe configurar un servidor horario independiente, que lo puedan usar servidores de aplicaciones SAP que se ejecutan en VM de Azure y las instancias de base de datos de SAP HANA que se ejecutan en instancias grandes de HANA. La infraestructura de almacenamiento en marcas de instancias grandes se sincronizado con los servidores NTP.



