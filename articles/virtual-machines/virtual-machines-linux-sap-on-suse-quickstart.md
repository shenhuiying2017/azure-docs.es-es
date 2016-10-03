<properties
   pageTitle="Pruebas de SAP NetWeaver en máquinas virtuales de SUSE Linux de Microsoft Azure | Microsoft Azure"
   description="Pruebas de SAP NetWeaver en máquinas virtuales de SUSE Linux de Microsoft Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# Ejecución de SAP NetWeaver en máquinas virtuales de SUSE Linux de Microsoft Azure


En este artículo se describen diversos aspectos que deben considerarse al ejecutar SAP NetWeaver en máquinas virtuales (VM) con SUSE Linux de Microsoft Azure. A partir del 19 de mayo de 2016 SAP NetWeaver es compatible oficialmente con máquinas virtuales de SUSE Linux en Azure. Todos los detalles sobre las versiones de Linux, las versiones de kernel SAP etc., se pueden encontrar en la nota de SAP 1928533 "SAP Applications on Azure: Supported Products and Azure VM types" (Aplicaciones SAP en Azure: productos admitidos y tipos de máquina virtual de Azure). Se puede encontrar documentación adicional sobre SAP en máquinas virtuales con Linux aquí: [Uso de SAP en máquinas virtuales de Linux (VM)](virtual-machines-linux-sap-get-started.md).


La siguiente información debería ayudarle a evitar algunos obstáculos potenciales.

## Imágenes SUSE en Azure para ejecutar SAP

Para ejecutar SAP NetWeaver en Azure, utilice solo SUSE Linux Enterprise Server SLES 12 (SPx). Consulte también la nota SAP 1928533. Se puede encontrar una imagen SUSE especial en Azure Marketplace ("SLES 11 SP3 para SAP CAL"), pero no está pensado para uso general. No use esta imagen, ya que está reservada para la solución de [biblioteca de aplicaciones en la nube de SAP](https://cal.sap.com/).

Todas las nuevas pruebas e instalaciones en Azure deben realizarse con Azure Resource Manager. Para buscar imágenes y versiones de SUSE SLES mediante Azure PowerShell o la interfaz de la línea de comandos de Azure (CLI), utilice los siguientes comandos. El resultado puede utilizarse, por ejemplo, para definir la imagen del sistema operativo en una plantilla JSON para implementar una nueva máquina virtual de SUSE Linux. Estos comandos de PowerShell son válidos para la versión de Azure PowerShell 1.0.1 y posterior.

* Busque publicadores existentes que incluyan SUSE:

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* Busque ofertas existentes de SUSE:

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* Busque ofertas de SUSE SLES:

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* Busque una versión específica de una SKU de SLES:

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## Instalación de WALinuxAgent en una máquina virtual de SUSE

El agente denominado WALinuxAgent forma parte de las imágenes de SLES en Azure Marketplace. Para más información sobre cómo instalarlo manualmente (por ejemplo, al cargar un disco duro virtual (VHD) del sistema operativo de SLES desde una ubicación local), consulte:

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [Las tablas de Azure](virtual-machines-linux-endorsed-distros.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## "Supervisión mejorada" de SAP

La "supervisión mejorada" de SAP es un requisito obligatorio para ejecutar SAP en Azure. Compruebe los detalles en la nota de SAP 2191498 "SAP on Linux with Azure: Enhanced Monitoring" (SAP en Linux con Azure: supervisión mejorada).

## Conexión de discos de datos de Azure a una máquina virtual Linux de Azure

Jamás debe montar discos de datos de Azure en una máquina virtual con Linux de Azure mediante el identificador de dispositivo. En su lugar, use el identificador único universal (UUID). Tenga cuidado al utilizar herramientas gráficas para montar discos de datos de Azure, por ejemplo. Compruebe las entradas de /etc/fstab.

El problema con el identificador del dispositivo es que puede cambiar y la máquina virtual de Azure podría bloquearse en el proceso de arranque. Para mitigar el problema, puede agregar el parámetro nofail en /etc/fstab. Pero tenga cuidado con nofail, pues las aplicaciones pueden usar el punto de montaje como antes y quizá escribir en el sistema de archivos raíz en caso de que no se haya montado un disco de datos externos de Azure durante el arranque.

La única excepción al montaje a través de UUID consiste en conectar un disco del sistema operativo para solucionar problemas, tal como se describe en la sección siguiente.

## Solución de problemas con una máquina virtual de SUSE a la que ya no es posible tener acceso

Puede haber situaciones en las que una máquina virtual de SUSE en Azure se bloquea en el proceso de arranque (por ejemplo, con un error relacionado con el montaje de los discos). Puede comprobar este problema mediante la característica de diagnóstico de arranque para las máquinas virtuales v2 de Azure en el Portal de Azure. Para más información, consulte [Boot diagnostics](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) (Diagnóstico de arranque).

Un modo de resolver el problema es conectando el disco del sistema operativo de la máquina virtual dañada a otra máquina virtual de SUSE en Azure. A continuación, realice los cambios adecuados (modificar /etc/fstab o quitar reglas de udev de red), como se describe en la sección siguiente.

Hay un aspecto importante que debe considerar. La implementación de varias máquinas virtuales de SUSE desde la misma imagen de Azure Marketplace (por ejemplo, SLES 11 SP4) hace que el mismo UUID monte siempre el disco del sistema operativo. Por lo tanto, el uso de UUID para conectar un disco del sistema operativo desde una máquina virtual distinta que se implementó mediante la misma imagen de Azure Marketplace generará dos UUID idénticos. Esto provoca problemas y podría significar que la máquina virtual destinada a la solución de problemas arranque, de hecho, desde el disco del sistema operativo conectado y dañado, en lugar de hacerlo desde el original.

Existen dos formas de evitarlo:

* Use una imagen de Azure Marketplace distinta para la solución de problemas de la máquina virtual (por ejemplo, SLES 11 SPx en lugar de SLES 12).
* No conecte el disco del sistema operativo dañado desde otra máquina virtual mediante UUID; hágalo de una manera distinta.

## Carga de una máquina virtual de SUSE desde una instalación local a Azure

A fin de obtener una descripción de los pasos para cargar una máquina virtual de SUSE del entorno local en Azure, consulte [Preparación de una máquina virtual SLES u openSUSE para Azure](virtual-machines-linux-create-upload-vhd-suse.md).

Si desea cargar una máquina virtual sin el paso de desaprovisionamiento al final (por ejemplo, para mantener una instalación de SAP existente, así como el nombre de host), compruebe los elementos siguientes:

* Asegúrese de que el disco del sistema operativo se monte a través de UUID y no del identificador de dispositivo. Cambiar a UUID solo en /etc/fstab no es suficiente para el disco de sistema operativo. Además, no olvide adaptar el cargador de arranque a través de YaST o mediante la edición de /boot/grub/menu.lst.
* Si usa el formato VHDX para el disco del sistema operativo de SUSE y lo convierte en VHD para cargarlo en Azure, es muy probable que el dispositivo de red cambie de eth0 a eth1. Para evitar problemas al arrancar después en Azure, hay que devolverlo a eth0, tal como se describe en [Fixing eth0 in cloned SLES 11 VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/) (Corrección de eth0 en VMware de SLES 11 clonado).

Además de lo que se describe en el artículo le recomendamos que quite también lo siguiente:

   /lib/udev/rules.d/75-persistent-net-generator.rules

También puede instalar el Agente de Linux de Azure (waagent) para ayudarle a evitar cualquier posible problema siempre y cuando no haya varias tarjetas NIC.

## Implementación de una máquina virtual de SUSE en Azure

Debe crear nuevas máquinas virtuales de SUSE mediante archivos de plantilla JSON en el nuevo modelo de Azure Resource Manager. Tras crearse el archivo de plantilla JSON, puede implementar la máquina virtual mediante el siguiente comando CLI como alternativa a PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Para más información sobre los archivos de plantilla JSON, consulte [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md) y [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/).

Para más información sobre la CLI y Azure Resource Manager, consulte [Uso de la CLI de Azure para Mac, Linux y Windows con Azure Resource Manager](xplat-cli-azure-resource-manager.md).

## Clave de licencia y hardware de SAP

Para la certificación oficial SAP-Azure se introdujo un nuevo mecanismo para calcular la clave de hardware de SAP, que se usa para la licencia SAP. Había que adaptar el núcleo SAP para usarlo. Las versiones anteriores de kernel de SAP para Linux no incluían este cambio en el código. Por lo tanto, en determinadas situaciones (por ejemplo, el cambio de tamaño de la máquina virtual de Azure), es posible que los cambios en la clave de hardware de SAP y en la licencia de SAP ya no fueran válidos. Esto se soluciona en los kernels de Linux de SAP más recientes. Para más información, consulte la nota de SAP 1928533.

## Paquete sapconf de SUSE/tuned-adm

SUSE ofrece un paquete denominado "sapconf" que administra un conjunto de opciones específicas de SAP. Para más información sobre lo que hace este paquete y cómo instalarlo y usarlo, consulte [Using sapconf to prepare a SUSE Linux Enterprise Server to run SAP systems](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) (Uso de sapconf a fin de preparar SUSE Linux Enterprise Server para ejecutar sistemas SAP) y [What is sapconf or how to prepare a SUSE Linux Enterprise Server for running SAP systems?](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems) (¿Qué es sapconf o cómo preparar SUSE Linux Enterprise Server para ejecutar sistemas SAP?).

Mientras tanto, hay una herramienta nueva que reemplaza a sapconf - tuned-adm. Puede encontrar más detalles acerca de esta herramienta en los dos vínculos siguientes.

La documentación de SLES sobre tuned-adm y el perfil de SAP Hana se encuentra [aquí](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html).

Puede encontrar información sobre el ajuste de sistemas para cargas de trabajo SAP con tuned-adm [aquí](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) (capítulo 6.2).


## Recursos compartidos de NFS en instalaciones de SAP distribuidas

Si tiene una instalación distribuida (por ejemplo, donde desea instalar la base de datos y los servidores de aplicaciones SAP en máquinas virtuales independientes), puede compartir el directorio /sapmnt a través de Network File System (NFS). Si tiene problemas con los pasos de instalación después de crear el recurso compartido NFS para /sapmnt, compruebe si se ha establecido "no\_root\_squash" para el recurso compartido.

## Volúmenes lógicos

En el pasado, si se necesitaba un gran volumen lógico en varios discos de datos de Azure (por ejemplo, para la base de datos SAP), se recomendaba usar mdadm dado que lvm no estaba aún completamente validado en Azure. Para más información sobre cómo configurar RAID de Linux en Azure mediante mdadm, consulte [Configuración del software RAID en Linux](virtual-machines-linux-configure-raid.md). Mientras tanto, desde principios de mayo de 2016 también lvm es totalmente compatible con Azure y puede utilizarse como alternativa a mdadm. Para más información sobre lvm en Azure, consulte [Configuración del LVM en una máquina virtual Linux en Azure](virtual-machines-linux-configure-lvm.md).


## Repositorio SUSE de Azure

Si tiene un problema con el acceso al repositorio SUSE de Azure estándar, podrá usar un comando simple para restablecerlo. Esto podría suceder si crea una imagen de sistema operativo privada en una región de Azure y después copia la imagen en una región diferente donde desea implementar nuevas máquinas virtuales que se basan en esta imagen de sistema operativo privada. Solo ejecute los siguientes comandos dentro de la máquina virtual:

   ```
   service guestregister restart
   ```

## Escritorio Gnome

Si desea usar el escritorio Gnome para instalar un sistema completo de demostración SAP dentro de una máquina virtual única, incluidos GUI de SAP, el explorador y la consola de administración de SAP, use esta sugerencia para su instalación en las imágenes de SLES Azure:

   Para SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   Para SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## Compatibilidad entre SAP y Oracle en Linux en la nube

Hay una restricción de soporte técnico de Oracle en Linux en entornos virtualizados. Aunque no es un tema específico de Azure, es importante comprenderlo. SAP no admite Oracle en SUSE ni en Red Hat en una nube pública como Azure. Para tratar este tema, póngase en contacto con Oracle directamente.

<!---HONumber=AcomDC_0921_2016-->