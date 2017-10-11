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
ms.openlocfilehash: 280001f9057825b9dcd98c5180340a54e2e239cf
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Procedimiento para instalar y configurar SAP HANA en Azure (instancias grandes)

A continuación se muestran algunas definiciones importantes que debe conocer antes de leer esta guía. En [Introducción y arquitectura de SAP HANA en Azure (instancias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) se presentaron dos clases distintas de unidades de instancia grande de HANA con:

- S72, S72m, S144, S144m, S192 y S192m, a las que se hace referencia como "clase de tipo I" de SKU.
- S384, S384m, S384xm, S576, S768 y S960, a las que se hace referencia como "clase de tipo II" de SKU.

El especificador de clase se va a usar en toda la documentación sobre Instancia grande de HANA para hacer referencia a diferentes capacidades y requisitos en función de las SKU de Instancia grande de HANA.

Otras definiciones que se usan con frecuencia son:
- **Sello de instancias grandes:** una pila de infraestructura de hardware que está certificada para SAP HANA TDI y se dedica a ejecutar instancias de SAP HANA dentro de Azure.
- **SAP HANA en Azure (Instancias grandes):** nombre oficial de la oferta de Azure para ejecutar instancias de HANA en hardware con certificación SAP HANA TDI que se implementa en sellos de instancias grandes en diferentes regiones de Azure. El término relacionado **Instancia grande de HANA** es la versión abreviada de SAP HANA en Azure (instancias grandes) y se usa con frecuencia en esta guía de implementación técnica.


La instalación de SAP HANA es su responsabilidad y puede hacerlo inmediatamente después de la entrega de un nuevo servidor de SAP HANA en Azure (instancias de grandes). Y después de establecer la conectividad entre las redes virtuales de Azure y las unidades de instancia grande de HANA. 

> [!Note]
> Según la directiva de SAP, la instalación de SAP HANA debe realizarla una persona certificada para realizar instalaciones de SAP HANA. Una persona que haya superado el examen de Certified SAP Technology Associate, el examen de certificación Instalación de SAP HANA o un integrador de sistemas (SI) certificado de SAP.

Vuelva a comprobar, en especial si tiene pensado instalar HANA 2.0, [Nota de compatibilidad de SAP n.º 2235581 - SAP HANA: Sistemas operativos admitidos](https://launchpad.support.sap.com/#/notes/2235581/E) que el sistema operativo es compatible con la versión de SAP HANA que ha decidido instalar. Comprobará que el sistema operativo compatible con HANA 2.0 tiene más restricciones que el sistema operativo compatible con HANA 1.0. 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>Primeros pasos después de recibir las unidades de instancia grande de HANA

El **primer paso** después de recibir la instancia grande de HANA y establecer el acceso y la conectividad a las instancias consiste en registrar el sistema operativo de la instancia con el proveedor de este. Este paso incluye el registro del sistema operativo SUSE Linux en una instancia de SUSE SMT que debe haber implementado en una máquina virtual en Azure. La unidad de instancia grande de HANA puede conectarse a esta instancia de SMT (como verá más adelante en esta documentación). O bien, debe registrar el sistema operativo RedHat con la instancia de RedHat Subscription Manager a la que necesite conectarse. Vea también los comentarios de este [documento](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Este paso también es necesario para poder aplicar la revisión del sistema operativo. Una tarea que es responsabilidad del cliente. Para SUSE, encuentre documentación para instalar y configurar SMT [aquí](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

El **segundo paso** es comprobar si hay nuevas revisiones y correcciones de la versión del sistema operativo específica. Compruebe si la versión de la instancia grande de HANA es la más reciente. En función del momento en el que Microsoft implemente las versiones o revisiones del sistema operativo, y los cambios de imagen, es posible que haya casos en los que no se incluyan las revisiones más recientes. Por tanto, comprobar si el proveedor particular de Linux ha publicado revisiones importantes para la seguridad, la funcionalidad, la disponibilidad y el rendimiento que necesiten implementarse es un paso obligatorio después de asumir una unidad de instancia grande de HANA.

El **tercer paso** consiste en comprobar las notas de SAP correspondientes a la instalación y configuración de SAP HANA en la versión concreta del sistema operativo. Por cambios en las recomendaciones o en las notas de SAP o las configuraciones que dependen de los distintos escenarios de instalación, Microsoft no siempre podrá tener una unidad de instancia grande de HANA configurada a la perfección. Por tanto, como cliente es obligatorio que lea las notas de SAP relacionadas con SAP HANA en la versión exacta de Linux. Compruebe también las configuraciones de la versión del sistema operativo necesaria y aplique los valores de configuración donde no se haya hecho.

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

El **cuarto paso** consiste en comprobar la hora del sistema de la unidad de instancia grande de HANA. Las instancias se implementan con una zona horaria del sistema que represente la ubicación de la región de Azure donde se encuentre el sello de la instancia grande de HANA. Es libre de cambiar la hora de su sistema o la zona horaria de sus instancias. Si lo hace, al pedir más instancias para su inquilino, necesitará adaptar la zona horaria de las instancias recién entregadas. Las operaciones de Microsoft no tienen ninguna información sobre la zona horaria del sistema donde instale las instancias tras la entrega. Por lo tanto, las instancias recién implementadas pueden no tener la misma zona horaria que a las que cambió. Como resultado, es su responsabilidad como cliente comprobarlo y, si es necesario, adaptar la zona horaria de las instancias entregadas. 

El **quinto paso** consiste en comprobar etc/hosts. A medida que van entregando las hojas, se les asignan direcciones IP diferentes con fines distintos (consulte la sección siguiente). Compruebe el archivo /etc/hosts. Cuando se agreguen unidades a un inquilino existente, no espere que etc/hosts en los sistemas recién implementados se mantenga correctamente con las direcciones IP de los sistemas entregados anteriormente. Depende de usted como cliente comprobar que la configuración sea la correcta, de manera que una instancia recién implementada pueda interactuar y resolver los nombres de las unidades implementadas anteriormente en el inquilino. 

## <a name="networking"></a>Redes
Se supone que ha seguido las recomendaciones de diseño de las redes virtuales de Azure y de conexión de esas redes a las instancias grandes de HANA tal y como se describe en estos documentos:

- [Introducción y arquitectura de SAP HANA en Azure (instancias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infraestructura y conectividad con SAP HANA en Azure (instancias grandes)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Hay algunos detalles que vale la pena mencionar acerca de las funciones de red de las unidades individuales. Cada unidad de instancia grande de HANA viene con dos o tres direcciones IP asignadas a dos o tres puertos NIC de la unidad. En las configuraciones de escalado horizontal de HANA y el escenario de replicación del sistema de HANA se utilizan tres direcciones IP. Una de las direcciones IP asignadas a la NIC de la unidad está fuera del grupo de direcciones IP del servidor que se describió en la [Introducción y arquitectura de SAP HANA en Azure (instancias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

La distribución para las unidades con dos direcciones IP asignadas debería ser como lo siguiente:

- eth0.xx debe tener una dirección IP asignada fuera del intervalo de direcciones del grupo de direcciones IP de servidor que envió a Microsoft. Esta dirección IP se usará para el mantenimiento en /etc/hosts del sistema operativo.
- eth1.xx debe tener una dirección IP asignada, que se usa para la comunicación con NFS. Por lo tanto, estas direcciones **NO** necesitan permanecer en etc/hosts para permitir el tráfico de instancia a instancia dentro del inquilino.

Para los casos de implementación de la replicación del sistema de HANA o el escalado horizontal de HANA, una configuración de hoja con dos direcciones IP asignadas no es adecuada. Si solo tiene dos direcciones IP asignadas y quiere implementar este tipo de configuración, póngase en contacto con SAP HANA en Azure Service Management para obtener una tercera dirección IP en otra red VLAN asignada. Para las unidades de instancia grande de HANA con tres direcciones IP asignadas en tres puertos de NIC, se aplican las reglas de uso siguientes:

- eth0.xx debe tener una dirección IP asignada fuera del intervalo de direcciones del grupo de direcciones IP de servidor que envió a Microsoft. Por lo tanto, esta dirección IP no se usará para el mantenimiento en /etc/hosts del sistema operativo.
- eth1.xx debe tener una dirección IP asignada, que se usa para la comunicación con el almacenamiento NFS. Por lo tanto, este tipo de direcciones no debe permanecer en etc/hosts.
- eth2.xx debe usarse exclusivamente para el mantenimiento en etc/hosts, para la comunicación entre las distintas instancias. Estas direcciones también serían las direcciones IP que deben mantenerse en las configuraciones de escalado horizontal de HANA como las direcciones IP que HANA usa para la configuración entre nodos.



## <a name="storage"></a>Almacenamiento

El diseño de almacenamiento para SAP HANA en Azure (instancias grandes) lo configura SAP HANA en Azure Service Management a través de directrices recomendadas de SAP, como se documenta en las notas del producto [SAP HANA Storage Requirements](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Requisitos de almacenamiento de SAP HANA). Los tamaños aproximados de los diferentes volúmenes con las diferentes SKU de grandes instancias de HANA se documentan en [Introducción y arquitectura de SAP HANA en Azure (instancias grandes)](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Las convenciones de nomenclatura de los volúmenes de almacenamiento se muestran en la tabla siguiente:

| Uso del almacenamiento | Nombre de montaje | Nombre del volumen | 
| --- | --- | ---|
| Datos de HANA | /hana/data/SID/mnt0000<m> | Almacenamiento IP: /hana_data_SID_mnt00001_tenant_vol |
| Registro HANA | /hana/log/SID/mnt0000<m> | Almacenamiento IP: /hana_log_SID_mnt00001_tenant_vol |
| Copia de seguridad del registro de HANA | /hana/log/backups | Almacenamiento IP: /hana_log_backups_SID_mnt00001_tenant_vol |
| HANA compartido | /hana/shared/SID | Almacenamiento IP: IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | Almacenamiento IP: /hana_shared_SID_mnt00001_tenant_vol/usr_sap |

Donde "SID" = el identificador de sistema de la instancia de HANA 

Y "tenant" = una enumeración interna de operaciones al implementar un inquilino.

Como puede ver, HANA compartido y usr/sap están compartiendo el mismo volumen. La nomenclatura de los puntos de montaje incluye el identificador de sistema de las instancias de HANA así como el número de montaje. En las implementaciones de escalado vertical solo hay un montaje, como mnt00001. Al mismo tiempo, en la implementación de escalado horizontal puede que vea tantos montajes como nodos de trabajo y principales tiene. Para el entorno de escalado horizontal, los datos, el registro y los volúmenes de copia de seguridad del registro se comparten y se adjuntan a cada nodo en la configuración de escalado horizontal. Para las configuraciones que ejecutan varias instancias de SAP, se crea un conjunto diferente de volúmenes y se adjunta a la unidad de instancia grande de HANA.

Según vaya leyendo el documento y buscando una unidad de instancia grande de HANA, se dará cuenta de que las unidades vienen con un volumen de disco bastante amplio para HANA/data y que tenemos un volumen HANA/log/backup. El motivo por el que el tamaño de HANA/data es tan grande es que las instantáneas de almacenamiento que le ofrecemos como cliente usan el mismo volumen de disco. Esto significa que cuantas más instantáneas de almacenamiento realice, más espacio consumen las instantáneas en los volúmenes de almacenamiento asignados. HANA/log/backup no se considera el volumen en el que colocar las copias de seguridad de base de datos. Su tamaño se ajusta para usarlo como volumen de copia de seguridad para las copias de seguridad del registro de transacciones de HANA. En futuras versiones del autoservicio de almacenamiento de instantáneas, este volumen específico será el destino para tener instantáneas más frecuentes. Y con eso, replicaciones más frecuentes en el sitio de recuperación ante desastres si quiere participar en la funcionalidad de recuperación ante desastres que proporciona la infraestructura de instancias grandes de HANA. Vea los detalles en [Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes)](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Además del almacenamiento proporcionado, puede adquirir capacidad de almacenamiento adicional en incrementos de 1 TB. Este almacenamiento adicional se puede agregar como nuevos volúmenes a instancias grandes de HANA.

Durante la incorporación de SAP HANA en Azure Service Management, el cliente especifica un identificador de usuario (UID) e Id. de grupo (GID) para el usuario sidadm y el grupo sapsys (p. ej.: 1000,500). Es necesario que durante la instalación del sistema SAP HANA se usen estos mismos valores. Cuando quiera implementar varias instancias de HANA en una unidad, obtendrá varios conjuntos de volúmenes (un conjunto por cada instancia). Como resultado, durante la implementación tendrá que definir:

- El SID de las distintas instancias de HANA (del que se deriva sidadm).
- Tamaños de memoria de las distintas instancias de HANA. Dado que el tamaño de memoria por instancia define el tamaño de los volúmenes en cada conjunto de volúmenes individuales.

En función de las recomendaciones del proveedor de almacenamiento se configuran las siguientes opciones de montaje para todos los volúmenes montados (se excluyen los LUN de inicio):

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Estos puntos de montaje se configuran en /etc/fstab como se muestra en los gráficos siguientes:

![fstab de volúmenes montados en la unidad de instancia grande de HANA](./media/hana-installation/image1_fstab.PNG)

La salida del comando df -h en una unidad de instancia grande de HANA S72m tendría este aspecto:

![fstab de volúmenes montados en la unidad de instancia grande de HANA](./media/hana-installation/image2_df_output.PNG)


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

El espacio de intercambio de la imagen del sistema operativo proporcionada está establecido en 2 GB de acuerdo con la [SAP Support Note #1999997 - FAQ: SAP HANA Memory](https://launchpad.support.sap.com/#/notes/1999997/E) (Nota de compatibilidad de SAP 1999997: Preguntas más frecuentes sobre la memoria de SAP HANA). Cualquier valor diferente deseado debe establecerlo el cliente.

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana) es la distribución de Linux instalada para SAP HANA en Azure (instancias grandes). Esta distribución particular proporciona funcionalidades específicas de SAP &quot;listas para usar&quot; (incluidos los parámetros predefinidos para ejecutar SAP en SLES de forma eficaz).

Vea [Biblioteca de recursos/Informes oficiales](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) en el sitio web de SUSE y [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) (SAP en SUSE) en la red de la comunidad de SAP (SCN) para varios recursos útiles relacionados con la implementación de SAP HANA en SLES (incluida la configuración de alta disponibilidad, el endurecimiento de la seguridad específico de las operaciones de SAP etc.).

Vínculos adicionales y útiles relacionados sobre SAP en SUSE:

- [SAP HANA on SUSE Linux Site](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) (Sitio de SAP HANA en SUSE Linux)
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113) (Procedimiento recomendado para SAP: poner en cola la replicación - SAP NetWeaver en SUSE Linux Enterprise 12)
- [ClamSAP – SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (ClamSAP: protección contra virus de SLES para SAP), incluido SLES 12 para SAP Applications

Notas de compatibilidad de SAP aplicables a la implementación de SAP HANA en SLES 12:

- [Nota de compatibilidad de SAP n.º 1944799: Instrucciones de SAP HANA para la instalación de sistema operativo](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [Nota de compatibilidad de SAP n.º 2205917: Configuración recomendada del sistema operativo de SAP HANA DB para SLES 12 en aplicaciones SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
- [Nota de compatibilidad de SAP n.º 1984787: Notas de instalación de SUSE Linux Enterprise Server 12](https://launchpad.support.sap.com/#/notes/1984787)
- [Nota de compatibilidad de SAP n.º 171356: Información general del software SAP en Linux](https://launchpad.support.sap.com/#/notes/1984787)
- [Nota de compatibilidad de SAP n.º 1391070: Soluciones UUID de Linux](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux para SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) es otra oferta para ejecutar SAP HANA en instancias grandes de HANA. Ya están disponibles las versiones de RHEL 6.7 y 7.2. 

Vínculos adicionales y útiles relacionados sobre SAP en Red Hat:
- [Sitio de SAP HANA en Red Hat Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Notas de compatibilidad de SAP aplicables a la implementación de SAP HANA en Red Hat:

- [Nota de compatibilidad de SAP n.º 2009879: Directrices de SAP HANA para el sistema operativo Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E)
- [2292690 - SAP HANA DB: Configuración recomendada del sistema operativo para RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [Nota de compatibilidad de SAP n.º 2247020: Configuración recomendada del sistema operativo para RHEL 6.7 en SAP HANA DB](https://launchpad.support.sap.com/#/notes/2247020)
- [Nota de compatibilidad de SAP n.º 1391070: Soluciones UUID de Linux](https://launchpad.support.sap.com/#/notes/1391070)
- [Nota de compatibilidad de SAP n.º 2228351: Linux: Revisión 110 (o superior) de SAP HANA Database SPS 11 en RHEL 6 o SLES 11](https://launchpad.support.sap.com/#/notes/2228351).
- [Nota de compatibilidad de SAP n.º2397039: P+F sobre SAP en RHEL](https://launchpad.support.sap.com/#/notes/2397039).
- [Nota de compatibilidad de SAP n.º 1496410: Instalación y actualización de Red Hat Enterprise Linux 6.x](https://launchpad.support.sap.com/#/notes/1496410).
- [Nota de compatibilidad de SAP n.º 2002167: Instalación y actualización de Red Hat Enterprise Linux 7.x](https://launchpad.support.sap.com/#/notes/2002167).

## <a name="time-synchronization"></a>Sincronización de la hora

Las aplicaciones de SAP basadas en la arquitectura de SAP NetWeaver son sensibles a las diferencias de hora para los distintos componentes que conforman el sistema SAP. Los volcados cortos de ABAP de SAP con el título de error de ZDATE\_LARGE\_TIME\_DIFF probablemente le resulten familiares, ya que estos volcados cortos aparecen cuando la hora del sistema de diferentes servidores o máquinas virtuales se distancia demasiado.

Para SAP HANA en Azure (instancias grandes), la sincronización de la hora que se realiza en Azure no se aplica a las unidades de proceso en las marcas de instancia grande. Esta sincronización no se aplica para ejecutar aplicaciones de SAP en máquinas virtuales nativas de Azure, ya que Azure garantiza que la hora del sistema está correctamente sincronizada. Como consecuencia de esto, se debe configurar un servidor horario independiente, que lo puedan usar servidores de aplicaciones SAP que se ejecutan en VM de Azure y las instancias de base de datos de SAP HANA que se ejecutan en instancias grandes de HANA. La infraestructura de almacenamiento en marcas de instancias grandes se sincronizado con los servidores NTP.

## <a name="setting-up-smt-server-for-suse-linux"></a>Configuración de servidor SMT para SUSE Linux
Las instancias grandes de SAP HANA no tienen conectividad directa a Internet. Por tanto, no es un proceso sencillo registrar una de estas unidades con el proveedor de sistema operativo y descargar y aplicar las revisiones. En el caso de SUSE Linux, una solución podría ser configurar un servidor SMT en una máquina virtual de Azure. Al mismo tiempo, la máquina virtual de Azure debe estar hospedada en una red virtual de Azure, que está conectada a la instancia grande de HANA. Con este tipo de servidor SMT, la unidad de instancia grande de HANA podría registrar y descargar las revisiones. 

SUSE proporciona una guía más extensa en [Subscription Management Tool for SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf) (Herramienta de administración de suscripciones para SLES 12 SP2). 

Como condición previa para la instalación de un servidor SMT que realice la tarea para la instancia grande de HANA, necesitará:

- Una red virtual de Azure conectada al circuito ER de instancias grandes de HANA.
- Una cuenta de SUSE que esté asociada a una organización. La organización necesitaría tener alguna suscripción válida de SUSE.

### <a name="installation-of-smt-server-on-azure-vm"></a>Instalación del servidor SMT en la máquina virtual de Azure

En este paso, se instala al servidor SMT en una máquina virtual de Azure. La primera medida consiste en iniciar sesión en [SUSE Customer Center](https://scc.suse.com/) (Centro de servicios al cliente de SUSE).

Después de iniciar sesión, vaya a Organización--> Credenciales de organización. En esa sección debería encontrar las credenciales necesarias para configurar el servidor SMT.

El tercer paso consiste en instalar una máquina virtual de SUSE Linux en la red virtual de Azure. Para implementar la máquina virtual, tome una imagen de SLES 12 SP2 de la galería de Azure. En el proceso de implementación, no defina un nombre DNS y no use direcciones IP estáticas, tal como se muestra en esta captura de pantalla

![implementación de VM para servidor SMT](./media/hana-installation/image3_vm_deployment.png)

La máquina virtual implementada era una máquina virtual más pequeña y tenía la dirección IP interna 10.34.1.4 en la red virtual de Azure. El nombre de la máquina virtual era smtserver. Después de la instalación, se comprobó la conectividad con las unidades de instancia grande de HANA. En función de cómo haya organizado la resolución de nombres, es posible que tenga que configurar la resolución de las unidades de instancia grande de HANA en etc/hosts en la máquina virtual de Azure. Agregue un disco adicional a la máquina virtual que se va a usar para contener las revisiones. El propio disco de arranque podría ser demasiado pequeño. En el caso del ejemplo, el disco se montó en /srv/www/htdocs, como se muestra en la captura de pantalla siguiente. Un disco de 100 GB debería ser suficiente.

![implementación de VM para servidor SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Inicie sesión en las unidades de instancia grande de HANA, mantenga /etc/hosts y compruebe si puede tener acceso a la máquina virtual de Azure en la que supuestamente se ejecuta el servidor SMT a través de la red.

Después de realizar correctamente esta comprobación, debe iniciar sesión en la máquina virtual de Azure en la que se debe ejecutar el servidor SMT. Si usa putty para iniciar sesión en la máquina virtual, debe ejecutar esta secuencia de comandos en la ventana de Bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Después de ejecutar estos comandos, reinicie Bash para activar la configuración. Después, inicie YAST.

En YAST, vaya a Mantenimiento de software y busque smt. Seleccione smt, que cambia automáticamente a yast2-smt, como se muestra a continuación

![SMT en yast](./media/hana-installation/image5_smt_in_yast.PNG)


Acepte la selección para la instalación en el servidor SMT. Una vez instalado, vaya a la configuración del servidor SMT y escriba las credenciales de la organización que recuperó anteriormente desde el Centro de servicios al cliente de SUSE. Escriba también el nombre de host de la máquina virtual de Azure y la dirección URL del servidor SMT. En este ejemplo era https://smtserver, como se muestra en el gráfico siguiente.

![Configuración del servidor SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Como paso siguiente, debe comprobar si funciona la conexión al Centro de servicios al cliente de SUSE. Como se ve en los gráficos siguientes, en el caso del ejemplo, ha funcionado.

![Probar la conexión al Centro de servicios al cliente de SUSE](./media/hana-installation/image7_test_connect.png)

Una vez se inicia el programa de instalación de SMT, debe proporcionar una contraseña de base de datos. Como es una instalación nueva, debe definir esa contraseña como se muestra en el gráfico siguiente.

![Definir la contraseña de la base de datos](./media/hana-installation/image8_define_db_passwd.PNG)

La interacción siguiente tiene lugar cuando se crea un certificado. Recorra el cuadro de diálogo como se muestra a continuación y continúe con el siguiente paso.

![Crear certificado para el servidor SMT](./media/hana-installation/image9_certificate_creation.PNG)

Es posible que se tarden varios minutos en el paso de "Ejecutar comprobación de sincronización" al final de la configuración. Después de la instalación y configuración del servidor SMT, debería encontrar el repositorio de directorio bajo el punto de montaje /srv/www/htdocs/ junto con varios subdirectorios. 

Reinicie el servidor SMT y sus servicios relacionados con estos comandos.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>Descarga de paquetes al servidor SMT

Después de reiniciar todos los servicios, seleccione los paquetes adecuados en la administración de SMT con Yast. La selección de paquetes depende de la imagen del sistema operativo del servidor de instancias grandes de HANA y no de la versión de SLES de la máquina virtual en la que se ejecuta el servidor SMT. A continuación se muestra un ejemplo de la pantalla de selección.

![Seleccionar paquetes](./media/hana-installation/image10_select_packages.PNG)

Una vez haya terminado con la selección de paquetes, debe iniciar la copia inicial de los paquetes seleccionados en el servidor SMT que configuró. Esta copia se desencadena en el shell mediante el comando smt-mirror como se muestra a continuación


![Descargar los paquetes al servidor SMT](./media/hana-installation/image11_download_packages.PNG)

Como puede ver arriba, los paquetes se deben copiar en los directorios creados bajo el punto de montaje /srv/www/htdocs. Este proceso puede tardar unos minutos. Según el número de paquetes que seleccione, se puede tardar una hora o más.
Cuando finalice este proceso, debe pasar a la configuración del cliente SMT. 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Configurar el cliente SMT en unidades de instancia grande de HANA

En este caso, los clientes son las unidades de instancia grande de HANA. El programa de instalación del servidor SMT copió el script clientSetup4SMT.sh en la máquina virtual de Azure. Copie ese script en la unidad de instancia grande de HANA que quiere conectar a su servidor SMT. Inicie el script con la opción -h y asígnele como parámetro el nombre del servidor SMT. En este ejemplo es smtserver.

![Configurar el cliente SMT](./media/hana-installation/image12_configure_client.PNG)

Es posible que haya un escenario en el que la carga del certificado desde el servidor por el cliente se realice correctamente, pero como se muestra a continuación no se pudo realizar el registro.

![Se produce un error en el registro de cliente](./media/hana-installation/image13_registration_failed.PNG)

Si se produce un error en el registro, lea este [documento de soporte de SUSE](https://www.suse.com/de-de/support/kb/doc/?id=7006024) y ejecute los pasos que describe.

> [!IMPORTANT] 
> Como nombre del servidor debe proporcionar el nombre de la máquina virtual, en este caso smtserver, sin el nombre de dominio completo. Con el nombre de la máquina virtual es suficiente. 

Después de ejecutar estos pasos, debe ejecutar el comando siguiente en la unidad de instancia grande de HANA.

```
SUSEConnect –cleanup
```

> [!Note] 
> En nuestras pruebas siempre se tuvieron que esperar unos minutos después de ese paso. La ejecución inmediata de clientSetup4SMT.sh, después de las medidas correctoras que se describen en el artículo de SUSE, finalizó con mensajes que indicaban que el certificado todavía no era válido. Para configurar correctamente el cliente normalmente se tenía que esperar 5-10 minutos y ejecutar clientSetup4SMT.sh.

Si se produce el problema que tuvo que corregir según los pasos descritos en el artículo de SUSE, debe reiniciar clientSetup4SMT.sh en la unidad de instancia grande de HANA de nuevo. Ahora debería finalizar correctamente como se muestra a continuación.

![Registro correcto del cliente](./media/hana-installation/image14_finish_client_config.PNG)

Con este paso, ha configurado el cliente SMT de la unidad de instancia grande de HANA para conectarse con el servidor SMT instalado en la máquina virtual de Azure. Ahora puede ejecutar "zypper up" o "zypper in" para instalar revisiones del sistema operativo en instancias grandes de HANA o instalar paquetes adicionales. Se entiende que solo puede obtener las revisiones que descargó antes en el servidor SMT.


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Ejemplo de una instalación de SAP HANA en instancias grandes de HANA
En esta sección se muestra cómo instalar SAP HANA en una unidad de instancia grande de HANA. El estado de inicio que tenemos tiene el siguiente aspecto:

- Ha proporcionado a Microsoft todos los datos para implementar una instancia grande de SAP HANA.
- Ha recibido la instancia grande de SAP HANA de Microsoft.
- Ha creado una red virtual de Azure que está conectada a la red local.
- Ha conectado el circuito ExpressRotue para instancias grandes de HANA a la misma red virtual de Azure.
- Ha instalado una máquina virtual de Azure que se usa como host administrativo seguro para instancias grandes de HANA.
- Se ha asegurado de que puede conectarse desde el host administrativo seguro a la unidad de instancia grande de HANA y viceversa.
- Ha comprobado si están instalados todos los paquetes y revisiones necesarios.
- Ha leído las notas de SAP y la documentación relativa a la instalación de HANA en el sistema operativo que usa y se ha asegurado de que la versión de HANA seleccionada es compatible con la versión del sistema operativo.

En las secuencias siguientes se muestra la descarga de los paquetes de instalación de HANA a la máquina virtual de host administrativo seguro, que en este caso se ejecuta en un sistema operativo de Windows, la copia de los paquetes a la unidad de instancia grande de HANA y la secuencia de la instalación.

### <a name="download-of-the-sap-hana-installation-bits"></a>Descarga de los elementos de instalación de SAP HANA
Puesto que las unidades de instancia grande de HANA no tienen conectividad directa a Internet, no puede descargar directamente los paquetes de instalación desde SAP en la máquina virtual de la instancia grande de HANA. Para solucionar la falta de conectividad directa a Internet, se necesita el host administrativo seguro. Descargue los paquetes a la máquina virtual de host administrativo seguro.

Para descargar los paquetes de instalación de HANA, necesitará un usuario S de SAP u otro usuario, lo que le permite obtener acceso a la tienda de SAP. Después de iniciar sesión, recorra esta secuencia de pantallas:

Vaya a [SAP Service Marketplace](https://support.sap.com/en/index.html) (Tienda del servicio SAP) > haga clic en Descargar Software > Instalaciones y actualización > Por índice alfabético > En H: SAP HANA Platform Edition > SAP HANA Platform Edition 2.0 > Instalación > descargue los archivos siguientes.

![Descargar la instalación de HANA](./media/hana-installation/image16_download_hana.PNG)

En el caso de ejemplo, se descargan los paquetes de instalación de SAP HANA 2.0. En la máquina virtual de host administrativo seguro de Azure, expanda los archivos autoextraíbles en el directorio tal y como se muestra a continuación.

![Extraer la instalación de HANA](./media/hana-installation/image17_extract_hana.PNG)

Una vez extraídos los archivos, copie el directorio creado por la extracción, 51052030 en el caso anterior, a la unidad de instancia grande de HANA en el volumen /hana/shared en un directorio que haya creado.

> [!Important]
> No copie los paquetes de instalación en la raíz o el LUN de inicio dado que el espacio es limitado y también debe usarse por otros procesos.


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instalar SAP HANA en la unidad de instancia grande de HANA
Para poder instalar SAP HANA, debe iniciar sesión como raíz del usuario. Solo la raíz tiene los permisos suficientes para instalar SAP HANA.
Lo primero que debe hacer es establecer permisos en el directorio que copió en /hana/shared. Los permisos deben establecerse de esta forma

```
chmod –R 744 <Installation bits folder>
```

Si quiere instalar SAP HANA mediante el programa de instalación gráfico, es necesario instalar el paquete gtk2 en las instancias grandes de HANA. Compruebe si está instalado con el comando

```
rpm –qa | grep gtk2
```

En los pasos siguientes se describe la instalación de SAP HANA con la interfaz gráfica de usuario. Como paso siguiente, vaya al directorio de instalación y navegue al subdirectorio HDB_LCM_LINUX_X86_64. Iniciar

```
./hdblcmgui 
```
desde ese directorio. Ahora se le guiará por una secuencia de pantallas donde debe proporcionar los datos de la instalación. En el caso del ejemplo, se instalan los componentes de servidor de base de datos y cliente de SAP HANA. Por tanto, nuestra selección es "SAP HANA Database" (Base de datos de SAP HANA) como se muestra a continuación

![Seleccionar HANA en la instalación](./media/hana-installation/image18_hana_selection.PNG)

En la siguiente pantalla, elija la opción "Install New System" (Instalar nuevo sistema).

![Seleccionar la nueva instalación de HANA](./media/hana-installation/image19_select_new.PNG)

Después de este paso, debe seleccionar entre varios componentes adicionales que también se pueden instalar en el servidor de base de datos de SAP HANA.

![Seleccionar componentes adicionales de HANA](./media/hana-installation/image20_select_components.PNG)

En esta documentación, elegimos el cliente de SAP HANA y SAP HANA Studio. También se instala una instancia de escalado vertical. Por tanto, en la siguiente pantalla, debe elegir "Single-Host System" (Sistema de un único host). 

![Seleccionar la instalación de escala vertical](./media/hana-installation/image21_single_host.PNG)

En la siguiente pantalla, debe proporcionar algunos datos.

![Proporcionar el SID de SAP HANA](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Como Id. de sistema de HANA (SID), debe proporcionar el mismo SID que proporcionó a Microsoft cuando solicitó la implementación de la instancia grande de HANA. Elegir un SID distinto hace que se produzca un error en la instalación debido a problemas de permisos de acceso en los diferentes volúmenes.

Como directorio de instalación use el directorio /hana/shared. En el paso siguiente, debe proporcionar las ubicaciones para los archivos de datos y de registro de HANA.


![Proporcionar la ubicación del registro de HANA](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Como archivos de datos y de registro debe definir los volúmenes que ya se incluían con los puntos de montaje que contienen al SID que eligió en la selección de la pantalla anterior a esta. Si el SID no coincide con el que escribió en la pantalla anterior, vuelva atrás y ajuste el SID en el valor de los puntos de montaje.

En el paso siguiente, revise el nombre de host y corríjalo si es necesario. 

![Revisar el nombre de host](./media/hana-installation/image24_review_host_name.PNG)

En el paso siguiente, también debe recuperar los datos que proporcionó a Microsoft cuando solicitó la implementación de la instancia grande de HANA. 

![Proporcionar el UID y GID del usuario del sistema](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Debe proporcionar el mismo Id. de usuario del sistema y el mismo Id. de grupo de usuarios que proporcionó a Microsoft cuando solicitó la implementación de la unidad. Si no proporciona los mismos identificadores, se produce un error en la instalación de SAP HANA en la unidad de instancia grande de HANA.

En las dos pantallas siguientes, que no se muestran en esta documentación, tiene que proporcionar la contraseña para el usuario SYSTEM de la base de datos de SAP HANA y la contraseña para el usuario sapadm, que se usa para el agente de host de SAP que se instala como parte de la instancia de base de datos de SAP HANA.

Después de definir la contraseña, aparece una pantalla de confirmación. Compruebe todos los datos que se muestran y continúe con la instalación. Llegará a una pantalla de progreso en la que se documenta el progreso de la instalación, como la siguiente:

![Comprobar el progreso de la instalación](./media/hana-installation/image27_show_progress.PNG)

Cuando finalice la instalación, debería ver una imagen como la siguiente:

![La instalación ha finalizado](./media/hana-installation/image28_install_finished.PNG)

En este momento, la instancia de SAP HANA debe estar en ejecución y lista para su uso. Debe poder conectarse a ella desde SAP HANA Studio. Asegúrese también de buscar y aplicar las últimas revisiones de SAP HANA.
























































 







 




