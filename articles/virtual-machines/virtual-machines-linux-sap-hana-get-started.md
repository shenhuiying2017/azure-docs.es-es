---
title: "Guía de inicio rápido: instalación manual de SAP Hana de única instancia en máquinas virtuales de Azure | Microsoft Docs"
description: "Guía de inicio rápido para la instalación manual de SAP Hana de única instancia en máquinas virtuales de Azure"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: bd70596bcc34684a6f751076e71cb3d0aa3877dd
ms.openlocfilehash: 4c40fd95f42f4e89e86d829c8a32583a0398c74e
ms.lasthandoff: 02/22/2017


---
# <a name="quick-start-guide-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Guía de inicio rápido: instalación manual de SAP Hana de única instancia en máquinas virtuales de Azure
## <a name="introduction"></a>Introducción
Esta guía de inicio rápido le ayuda a configurar un prototipo o sistema de demostración de SAP HANA de instancia única en máquinas virtuales de Azure (VM) al instalar SAP NetWeaver 7.5 y SAP HANA SP12 manualmente.

En la guía se da por supuesto que está familiarizado con conceptos básicos de IaaS de Azure como:
 * Cómo implementar máquinas virtuales o redes virtuales a través de Azure Portal o PowerShell.
 * La herramienta de línea de comandos multiplataforma (CLI) de Azure incluye la opción para usar plantillas de notificación de objetos JavaScript (JSON).

En la guía también se da por supuesto que está familiarizado con SAP HANA, SAP NetWeaver y cómo instalarlos en una ubicación local.

Además, debería conocer la documentación sobre Azure para SAP mencionada en la sección de información general al final de la guía.

Dado que el contenido de la guía está restringido a sistemas que no son de producción, no se tratan temas tales como la alta disponibilidad (HA), copia de seguridad, recuperación ante desastres (DR), alto rendimiento o consideraciones de seguridad especiales.

SAP-Linux-Azure solo se admite en Azure Resource Manager y no en el modelo de implementación clásica. Por consiguiente, se ha realizado una instalación de ejemplo con dos máquinas virtuales para realizar una instalación de SAP NetWeaver distribuida mediante el modelo de Azure Resource Manager. Para más información sobre Resource Manager, consulte la sección "Información General" al final de la guía.

Para la instalación de ejemplo, se utilizaron las siguientes dos máquinas virtuales de prueba:

* hana-appsrv (tipo DS3) para hospedar la instancia de NetWeaver 7.5 ABAP SAP Central Services (ASCS) + PAS
* hana-dbsrv (tipo GS4 ) para hospedar HANA SP12

Ambas máquinas virtuales pertenecían a una red virtual de Azure (azure-hana-test-vnet) y, en ambos casos, el sistema operativo era SLES 12 SP1.

A partir de julio de 2016, SAP HANA tiene compatibilidad total con sistemas de producción OLAP (BW) en máquinas virtuales de Azure de tipo GS5. Para realizar pruebas cuando no se espera compatibilidad oficial con SAP, se pueden usar máquinas virtuales más pequeñas, por ejemplo, GS4. Para SAP HANA en Azure use siempre el almacenamiento premium de Azure para los archivos de datos y de registro de HANA (consulte más adelante en la guía la sección "Configuración de disco"). Para más información sobre qué productos SAP son compatibles en Azure, consulte la sección de información general al final de esta guía.

En la guía se describen dos formas diferentes de instalar manualmente SAP HANA en máquinas virtuales de Azure:

* Mediante SAP Software Provisioning Manager (SWPM) como parte de una instalación distribuida de NetWeaver en el paso de "instalar instancia de base de datos".
* Mediante la herramienta hdblcm de administración del ciclo de vida de HANA y, después, instalando NetWeaver.

También puede utilizar SWPM e instalar todos los componentes (SAP HANA, servidor de aplicaciones SAP, instancia de ASCS, SAP GUI) en una sola máquina virtual. Esta opción no se describe en esta guía, pero los elementos que deben tenerse en cuenta son los mismos.

Antes de iniciar una instalación, no olvide leer "Preparación de las máquinas virtuales de Azure para la instalación manual de SAP HANA" que va inmediatamente después de las dos listas de comprobación para la instalación de SAP HANA. Si lo hace, puede ayudar a evitar varios errores básicos que se pueden producir cuando se usa solo una configuración predeterminada de máquina virtual de Azure.

## <a name="checklist-for-sap-hana-installation-using-sap-swpm"></a>Lista de comprobación para la instalación de SAP HANA por medio de SAP SWPM
Esta sección enumera los pasos principales de una instalación manual de SAP HANA de instancia única, para fines de demostración o de creación de prototipos cuando usa SAP SWPM para realizar una instalación distribuida de SAP NetWeaver 7.5. Los elementos individuales se explican con más detalle mediante las capturas de pantalla que aparecen a lo largo de la guía.

* Cree una red virtual de Azure que incluya las dos máquinas virtuales de prueba.
* Implemente dos máquinas virtuales de Azure con OS SLES/SLES para aplicaciones SAP 12 SP1 según el modelo de Azure Resource Manager.
* Conecte dos discos de almacenamiento estándar a la máquina virtual del servidor de aplicaciones (por ejemplo, discos de 75 GB y 500 GB).
* Conecte cuatro discos a la máquina virtual del servidor de HANA DB: dos discos de almacenamiento estándar (como aquellos para la máquina virtual del servidor de aplicaciones) + dos discos de almacenamiento premium (por ejemplo, 2 discos de 512 GB).
* Dependiendo de los requisitos de tamaño o rendimiento, conecte varios discos y cree volúmenes seccionados mediante la administración de volúmenes lógicos (LVM) o una utilidad de administración de varios dispositivos (mdadm) en el nivel de sistema operativo dentro de la máquina virtual.
* Cree sistemas de archivos XFS en los discos conectados o los volúmenes lógicos.
* Monte los nuevos sistemas de archivos XFS en el nivel de sistema operativo. Use un sistema de archivos para mantener todo el software de SAP y el otro, por ejemplo, para el directorio /sapmnt y quizás las copias de seguridad. En el servidor de SAP HANA DB, monte los sistemas de archivos XFS en los discos de almacenamiento premium como /hana y /usr/sap. Este proceso es necesario para impedir que el sistema de archivos raíz, que no es de gran tamaño en máquinas virtuales de Azure de Linux, se llene.
* Escriba las direcciones IP locales de las máquinas virtuales de prueba en /etc/hosts.
* Especifique el parámetro nofail en /etc/fstab.
* Establezca los parámetros de kernel según la nota de SAP HANA-SLES-12. Para más información, consulte la sección "Parámetros de kernel".
* Adición de espacio de intercambio.
* Opcionalmente, instale un escritorio gráfico en las máquinas virtuales de prueba. De lo contrario, use una instalación remota con SAPinst.
* Descargue el software SAP de SAP Service Marketplace.
* Instale la instancia de ASCS de SAP en la máquina virtual del servidor de aplicaciones.
* Comparta el directorio /sapmnt entre las máquinas virtuales de prueba mediante el uso de NFS. La máquina virtual del servidor de aplicaciones es el servidor NFS.
* Instale la instancia de base de datos, incluido HANA, por medio de SWPM en la máquina virtual del servidor de bases de datos.
* Instale al servidor principal de la aplicación (PAS) en la máquina virtual del servidor de aplicaciones.
* Inicie la consola de administración de SAP (MC) y conéctese a, por ejemplo, SAP GUI/HANA Studio.

## <a name="checklist-for-sap-hana-installation-using-hdblcm"></a>Lista de comprobación para la instalación de SAP HANA por medio de hdblcm
Esta sección enumera los pasos principales de una instalación manual de SAP HANA de instancia única, para fines de demostración o de creación de prototipos cuando usa SAP hdblcm para realizar una instalación distribuida de SAP NetWeaver 7.5. Los elementos individuales se explican con más detalle mediante las capturas de pantalla que aparecen a lo largo de la guía.

* Cree una red virtual de Azure que incluya las dos máquinas virtuales de prueba.
* Implemente dos máquinas virtuales de Azure con OS SLES/SLES para aplicaciones SAP 12 SP1 según el modelo de Azure Resource Manager.
* Conecte dos discos de almacenamiento estándar a la máquina virtual del servidor de aplicaciones (por ejemplo, discos de 75 GB y 500 GB).
* Conecte cuatro discos a la máquina virtual del servidor de HANA DB: dos discos de almacenamiento estándar (como aquellos para la máquina virtual del servidor de aplicaciones) + dos discos de almacenamiento premium (por ejemplo, 2 discos de 512 GB).
* Dependiendo de los requisitos de tamaño o rendimiento, conecte varios discos y cree volúmenes seccionados mediante la administración de volúmenes lógicos (LVM) o una utilidad de administración de varios dispositivos (mdadm) en el nivel de sistema operativo dentro de la máquina virtual.
* Cree sistemas de archivos XFS en los discos conectados o los volúmenes lógicos.
* Monte los nuevos sistemas de archivos XFS en el nivel de sistema operativo. Use un sistema de archivos para mantener todo el software de SAP y el otro, por ejemplo, para el directorio /sapmnt y quizás las copias de seguridad. En el servidor de SAP HANA DB, monte los sistemas de archivos XFS en los discos de almacenamiento premium como /hana y /usr/sap. Este proceso es necesario para impedir que el sistema de archivos raíz, que no es de gran tamaño en máquinas virtuales de Azure de Linux, se llene.
* Escriba las direcciones IP locales de las máquinas virtuales de prueba en /etc/hosts.
* Especifique el parámetro nofail en /etc/fstab.
* Establezca los parámetros de kernel según la nota de SAP HANA-SLES-12. Para más información, consulte la sección "Parámetros de kernel".
* Adición de espacio de intercambio.
* Opcionalmente, instale un escritorio gráfico en las máquinas virtuales de prueba. De lo contrario, use una instalación remota con SAPinst.
* Descargue el software SAP de SAP Service Marketplace.
* Cree un grupo "sapsys" con el identificador de grupo 1001 en la máquina virtual del servidor de HANA DB.
* Instale SAP HANA en la máquina virtual del servidor de base de datos mediante el administrador de ciclos de vida de base de datos de HANA (HDBLCM).
* Instale la instancia de ASCS de SAP en la máquina virtual del servidor de aplicaciones.
* Comparta el directorio /sapmnt entre las máquinas virtuales de prueba mediante el uso de NFS. La máquina virtual del servidor de aplicaciones es el servidor NFS.
* Instale la instancia de base de datos, incluido HANA, por medio de SWPM en la máquina virtual del servidor de HANA DB.
* Instale al servidor principal de la aplicación (PAS) en la máquina virtual del servidor de aplicaciones.
* Inicie la consola de administración de SAP y conéctese mediante SAP GUI/HANA Studio.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Preparación de las máquinas virtuales de Azure para una instalación manual de SAP HANA
En esta sección se describen los temas siguientes:

* Actualizaciones de SO
* Configuración de disco
* Parámetros de kernel
* Sistemas de archivos
* /etc/hosts
* /etc/fstab

### <a name="os-updates"></a>Actualizaciones de SO
Como SUSE proporciona actualizaciones y revisiones para el SO que ayudan a mantener la seguridad y el funcionamiento sin problemas, es bueno preguntar si hay actualizaciones disponibles antes de instalar software adicional.
Muchas veces, se podría evitar una llamada de soporte técnico si el sistema está en el nivel de revisión real.

Las imágenes a petición de Azure están conectadas automáticamente a la infraestructura de actualización de SUSE donde existen software y actualizaciones adicionales.
Las imágenes de BYOS se deben registrar en SUSE Customer Center (https://scc.suse.com )

Compruebe de forma sencilla las revisiones disponibles con:

 `sudo zypper list-patches`

Según el tipo de defecto, las revisiones se clasifican por categoría y gravedad.

Los valores usados normalmente para categoría son: seguridad, recomendada, opcional, característica, documento o yast.

Los valores usados normalmente para gravedad son: crítica, importante, moderada, baja o no especificada.

zypper solo busca las actualizaciones necesarias para los paquetes instalados.

Así que un comando de ejemplo podría ser:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Si agrega el parámetro *--dry-run*, puede probar la actualización, pero realmente no actualiza el sistema.


### <a name="disk-setup"></a>Configuración de disco
El sistema de archivos raíz en una máquina virtual Linux en Azure tiene un tamaño limitado. Por lo tanto, es necesario agregar espacio adicional en disco a una máquina virtual para ejecutar SAP. Si la máquina virtual de servidor de aplicaciones SAP se usa en un entorno completamente de prototipo o demostración, usar discos de almacenamiento estándar de Azure es suficiente. Para los archivos de datos y de registro de SAP HANA DB, use discos de almacenamiento premium de Azure, incluso en entornos que no sean de producción.

Para más información, consulte el artículo que describe [cómo conectar discos a una máquina virtual Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para almacenar en caché de disco de Azure, escriba **Ninguno** para los discos que se van a utilizar para almacenar los registros de transacciones de HANA. Para los archivos de datos de HANA, es correcto usar el almacenamiento en caché de lectura. Como HANA es una base de datos en memoria, la medida en que la memoria caché de lectura en el nivel de disco de Azure mejorará el rendimiento depende del patrón de uso general (por ejemplo, iniciando HANA y leyendo datos del disco en memoria).

Para más información, consulte [Almacenamiento premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../storage/storage-premium-storage.md).

Para buscar plantillas de JSON de ejemplo para crear máquinas virtuales, vaya a [Plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates).
La plantilla "vm-simple-sles" muestra una plantilla básica, que incluye una sección de almacenamiento con un disco de datos adicional de 100 GB.

Para más información acerca de cómo buscar una imagen de SUSE mediante PowerShell o CLI y entender la importancia de conectar un disco mediante UUID, consulte [Ejecución de SAP NetWeaver en máquinas virtuales de SUSE Linux de Microsoft Azure](virtual-machines-linux-sap-on-suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Según los requisitos de tamaño y rendimiento del sistema, puede que necesite conectar varios discos en lugar de uno y después crear un conjunto seccionado en esos discos en el nivel de sistema operativo. Es posible que desee crear un conjunto seccionado en varios discos de Azure por dos motivos:

* Para aumentar el rendimiento.
* Necesita un único sistema de archivos que sea mayor de 1 TB, ya que el límite actual de tamaño de disco de Azure es 1 TB (a partir de julio de 2016).

Para más información acerca de las dos herramientas principales para configurar el seccionamiento, consulte los siguientes artículos:

* [Configuración del software RAID en Linux](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Configuración del LVM en una máquina virtual Linux en Azure](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

En el entorno de prueba, se conectaron dos discos de almacenamiento estándar de Azure a la máquina virtual del servidor de aplicaciones SAP, como se indica en la siguiente captura de pantalla. Uno se usó para almacenar todo el software SAP para la instalación (es decir, NetWeaver 7.5, GUI de SAP, SAP HANA, etc). El segundo disco garantiza que haya suficiente espacio libre disponible para requisitos adicionales (por ejemplo, para los datos de copia de seguridad y prueba) y para que el directorio de /sapmnt (es decir, los perfiles de SAP) se puedan compartir entre todas las máquinas virtuales que pertenecen a la misma infraestructura de SAP.

![Ventana de discos de servidor de aplicaciones de SAP HANA que muestra dos discos de datos y sus tamaños](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

A diferencia del escenario con máquina virtual del servidor de aplicaciones, se han conectado cuatro discos a la máquina virtual del servidor SAP HANA, como se muestra en la captura de pantalla siguiente. Dos discos se utilizan para almacenar el software SAP (también puede compartir el disco de software SAP mediante el uso de NFS) y disponer de suficiente espacio libre (para copia de seguridad, por ejemplo). Los dos discos adicionales eran discos de almacenamiento premium de Azure para almacenar los archivos de registro y datos de SAP HANA, así como el directorio /usr/sap.

![Ventana de discos de servidor de aplicaciones de SAP HANA que muestra cuatro discos de datos y sus tamaños](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

### <a name="kernel-parameters"></a>Parámetros de kernel
SAP HANA requiere configuraciones específicas para el kernel de Linux que no forman parte de las imágenes estándar de la galería de Azure y que se deben establecer manualmente. Una nota de SAP HANA describe la configuración de sistema operativo recomendada para SLES 12/SLES for SAP Applications 12: [ Nota de SAP 2205917](https://launchpad.support.sap.com/#/notes/2205917).


SLES-for-SAP Applications 12 GA y SP1 tienen una nueva herramienta que sustituye a la utilidad sapconf anterior. Se llama tuned-adm y hay un perfil de SAP HANA especial disponible para ella. Para optimizar el sistema para SAP HANA, escriba como usuario raíz: "tuned-adm profile sap-hana".

Para más información sobre tuned-adm, consulte la documentación de SUSE en:

* [Documentación de SLES-for-SAP Applications 12 SP1 sobre tuned-adm profile sap-hana.](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)

En la siguiente captura de pantalla se ve cómo "tuned-adm" ha cambiado los valores de transparent_hugepage y de numa_balancing en función de las configuraciones de SAP HANA necesarias.

![La herramienta tuned-adm cambia los valores en función de la configuración necesaria de SAP HANA](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

Para convertir en permanentes las configuraciones de kernel de SAP HANA, se debe usar grub2 en SLES 12. Para más información sobre grub2, vaya a la [sección sobre "estructura de archivos de configuración" de la documentación de SUSE](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

La siguiente captura de pantalla muestra cómo se han cambiado las configuraciones del kernel en el archivo de configuración y después se han "compilado" mediante grub2-mkconfig.

![Configuraciones de kernel cambiadas en el archivo de configuración y compiladas mediante grub2 mkconfig](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

Otra opción sería cambiar las configuraciones por medio de YaST y la configuración de  > **parámetros de kernel** del **cargador de arranque**.

![Pestaña de configuración de parámetros de kernel en el cargador de arranque de YaST](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

### <a name="file-systems"></a>Sistemas de archivos
La siguiente captura de pantalla muestra los dos sistemas de archivos que se crearon en la máquina virtual del servidor de aplicaciones SAP usando los dos discos de almacenamiento estándar de Azure conectados. Ambos sistemas de archivos son del tipo XFS y están montados en /sapdata y /sapsoftware.

No es obligatorio para estructurar los sistemas de archivos de esta manera. Tiene otras opciones para estructurar el espacio en disco. La consideración más importante que debe tener es la de evitar que el sistema de archivos raíz se quede sin espacio libre.

![Se crearon dos sistemas de archivos en la máquina virtual del servidor de aplicaciones SAP](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

Con respecto a la máquina virtual de SAP HANA DB, es importante saber que, si usa SAPinst (SWPM) y la opción de instalación "típica", se instalarán todos los elementos de forma predeterminada en /hana y /usr/sap. La configuración predeterminada para la copia de seguridad de registros de SAP HANA está, por ejemplo, en /usr/sap. De nuevo, es importante evitar que el sistema de archivos raíz se quede sin espacio de almacenamiento. Por ello, asegúrese de que hay suficiente espacio libre en /hana y en /usr/sap antes de instalar SAP HANA mediante SWPM.

Para ver una descripción del diseño del sistema de archivos estándar de SAP HANA, consulte la [guía de instalación y actualización del servidor de SAP HANA](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).
![Se crearon dos sistemas de archivos adicionales en la máquina virtual del servidor de aplicaciones SAP](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

Cuando instala SAP NetWeaver en una imagen estándar de la galería de Azure de SLES/SLES-for-SAP Applications 12, se muestra un mensaje que dice que no hay espacio de intercambio. Para descartar este mensaje, puede agregar manualmente un archivo de intercambio mediante dd, mkswap y swapon. Para obtener información sobre cómo hacerlo, busque "Adding a Swap File Manually" (Incorporación manual de un archivo de intercambio) en la [sección "Using the YaST Partitioner" (Uso del particionador YaST) de la documentación de SUSE](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Otra opción consiste en configurar el espacio de intercambio mediante el agente de la máquina virtual Linux. Consulte la [Guía de usuario del Agente de Linux de Azure](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para más información.

![Mensaje emergente que avisa de que no hay espacio de intercambio suficiente](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)


### <a name="etchosts"></a>/etc/hosts
Otro aspecto importante a tener en cuenta antes de empezar a instalar SAP es incluir los nombres de host y las direcciones IP de las máquinas virtuales de SAP en el archivo /etc/hosts. Implemente todas las máquinas virtuales de SAP dentro de una red virtual de Azure y después use las direcciones IP internas.

![Nombres de host y direcciones IP de las máquinas virtuales de SAP enumeradas en el archivo /etc/hosts](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

### <a name="etcfstab"></a>/etc/fstab

Durante la fase de pruebas, se observó que era buena idea agregar el parámetro nofail a fstab. Si algo fuera mal con los discos, la máquina virtual seguiría funcionando y no se bloquearía en el proceso de arranque. Pero preste especial atención, porque, en este escenario, puede que el espacio de disco adicional no esté disponible y los procesos llenen el sistema de archivos raíz. Si falta /hana, SAP HANA no se iniciará.

![Adición del parámetro nofail a fstab](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

## <a name="install-graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Instalación del escritorio gráfico Gnome en SLES 12/SLES-for-SAP Applications 12
En esta sección se describen los temas siguientes:

* Instalación del escritorio Gnome y xrdp en SLES 12/SLES-for-SAP Applications 12
* Ejecución de la consola de administración de SAP basada en Java mediante Firefox en SLES 12/SLES-for-SAP Applications 12

También puede utilizar otras alternativas, como Xterminal o VNC, pero, a partir de septiembre de 2016, la guía describe solo xrdp.

### <a name="installing-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Instalación del escritorio Gnome y xrdp en SLES 12/SLES-for-SAP Applications 12
Si conoce Microsoft Windows, puede usar fácilmente un escritorio gráfico directamente dentro de las máquinas virtuales Linux de SAP para ejecutar Firefox, Sapinst, GUI de SAP, SAP MC o HANA Studio y conectarse a la máquina virtual a través de RDP desde un equipo Windows. Aunque este procedimiento podría no ser adecuado para un servidor de bases de datos de producción, sí lo es para un entorno de prototipo o demostración puro. Así es como se instala el escritorio Gnome en una máquina virtual SLES 12/SLES-for-SAP Applications 12 de Azure:

Instale el escritorio Gnome y, para ello, escriba el siguiente comando (por ejemplo, en una ventana de PuTTY):

`zypper in -t pattern gnome-basic`

Instale xrdp para permitir una conexión a la máquina virtual a través de RDP:

`zypper in xrdp`

Edite /etc/sysconfig/windowmanager y establezca el administrador de ventanas predeterminado en Gnome:

`DEFAULT_WM="gnome"`

Ejecute chkconfig para asegurarse de que xrdp se inicie automáticamente después de un reinicio:

`chkconfig -level 3 xrdp on`

Si hay un problema con la conexión RDP, pruebe a reiniciar (quizás desde una ventana de PuTTY):

`/etc/xrdp/xrdp.sh restart`

Si el reinicio de xrdp mencionado antes no funciona, compruebe si hay un archivo .pid y quítelo:

`check /var/run` y busque `xrdp.pid`

Quítelo y, luego, intente reiniciar de nuevo.

### <a name="sap-mc"></a>Consola de administración de SAP
Después de instalar el escritorio Gnome, al iniciarse la consola de administración gráfica de SAP basada en Java desde Firefox que se ejecuta en una máquina virtual SLES 12/SLES-for-SAP Applications 12 de Azure, podría mostrarse un error porque falta el complemento del explorador de Java.

La dirección URL para iniciar la consola de administración de SAP <server>:5<número_de_instancia>13.

Para más información, consulte [Starting the Web-Based SAP Management Console](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm) (Inicio de la consola de administración de SAP basada en web).

La captura de pantalla siguiente muestra el mensaje de error que aparece cuando el complemento del explorador de Java falta.

![Mensaje de error que indica que falta el complemento del explorador de Java](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

Una manera de resolver el problema es instalar el complemento que falta mediante YaST, tal y como se muestra en la captura de pantalla siguiente.

![Uso de YaST para instalar el complemento que falta](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

Si repite la URL de la consola de administración de SAP, aparecerá un cuadro de diálogo que le pide que active el complemento.

![Cuadro de diálogo que solicita la activación del complemento](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

Otro problema que podría producirse es la aparición de un mensaje de error acerca de un archivo que falta, javafx.properties. Este problema está relacionado con el requisito de Oracle Java 1.8 for SAP GUI 7.4. [Consulte la nota de SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424). Ni la versión de IBM Java ni el paquete openjdk que se proporciona con SLES/SLES-for-SAP Applications 12 incluyen la plataforma JavaFX necesaria. La solución es descargar e instalar Java SE8 desde Oracle.

Se puede encontrar un artículo que trata sobre un problema similar en openSUSE con openjdk en [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="install-sap-hana-manually-by-using-swpm-as-part-of-a-netweaver-75-installation"></a>Instalación manual de SAP HANA mediante SWPM como parte de una instalación de NetWeaver 7.5
La serie de capturas de pantalla de esta sección muestra los pasos principales para la instalación de SAP NetWeaver 7.5 y SAP HANA SP12 cuando usa SWPM (SAPinst). Como parte de la instalación de NetWeaver 7.5, SWPM también puede instalar la base de datos de HANA como una sola instancia.

En el entorno de prueba del ejemplo se instaló un solo servidor de aplicaciones de programación de aplicaciones empresariales avanzadas (ABAP). Como se muestra en la siguiente captura de pantalla, hemos usado la opción "Distributed System" (Sistema distribuido) para instalar ASCS, instancias del servidor de aplicaciones principal en una máquina virtual de Azure y SAP HANA como sistema de la base de datos en otra máquina virtual de Azure.

![ASCS e instancias del servidor de aplicaciones principal instaladas con la opción de "Distributed System" (Sistema distribuido)](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

Una vez que la instancia de ASCS esté instalada en la máquina virtual del servidor de aplicaciones y se haya establecido en "verde" en la consola de administración de SAP, el directorio sapmnt, que incluye el directorio de perfiles de SAP, debe compartirse con la máquina virtual del servidor de base de datos de SAP HANA. El paso de instalación de la base de datos necesita acceso a esta información. La mejor manera de proporcionar acceso es utilizar NFS, que se puede configurar mediante YaST.

![Consola de administración de SAP que muestra la instancia de ASCS instalada en la máquina virtual del servidor de aplicaciones establecida en "verde"](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

En la máquina virtual del servidor de aplicaciones, se debería compartir el directorio sapmnt mediante NFS con las opciones **rw** y **no_root_squash**. Los valores predeterminados son "ro" y "root_squash", lo que podría producir problemas al instalar la instancia de base de datos.

![Uso compartido del directorio /sapmnt a través de NFS mediante el uso de las opciones "rw" y "no_root_squash"](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

Como muestra la siguiente captura de pantalla, el recurso compartido /sapmnt de la máquina virtual del servidor de aplicaciones tiene que configurarse en la máquina virtual del servidor de SAP HANA DB mediante el **cliente NFS** (por ejemplo, con la ayuda de YaST).

![El recurso compartido /sapmnt configurado mediante el uso del "cliente NFS"](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

Para realizar una instalación distribuida de NetWeaver 7.5, **Instancia de base de datos**, como se muestra en la siguiente captura de pantalla, inicie sesión en la máquina virtual del servidor de SAP HANA DB e inicie SWPM.

![Instalación de una instancia de base de datos mediante el inicio de sesión en la máquina virtual del servidor de SAP HANA DB y el inicio de SWPM](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

Después de seleccionar una instalación "típica" y la ruta de acceso a los medios de instalación, escriba un SID de la base de datos, el nombre de host, el número de instancia y la contraseña de administrador del sistema de base de datos.

![La página de inicio de sesión del administrador del sistema de base de datos de SAP HANA](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Escriba la contraseña para el esquema DBACOCKPIT.

![El cuadro para escribir la contraseña para el esquema DBACOCKPIT](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Escriba una pregunta para la contraseña del esquema SAPABAP1.

![Escriba una pregunta para la contraseña del esquema SAPABAP1](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Una vez completada la tarea, aparece una marca de verificación verde junto a cada fase del proceso de instalación de la base de datos. Una ventana de cuadro de mensaje aparecerá con un mensaje que dice: "Execution of ... Database Instance has completed" (Ejecución de la instancia de base de datos… completada).

![Ventana de tarea completada con el mensaje de confirmación](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

Tras una instalación correcta, la consola de administración de SAP también debería mostrar la instancia de base de datos en "verde" y la lista completa de los procesos de SAP HANA (hdbindexserver, hdbcompileserver, etc).

![Ventana de la consola de administración de SAP con la lista de procesos de SAP HANA](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

La siguiente captura de pantalla muestra las partes de la estructura de archivos en /hana/shared que SWPM creó durante la instalación de HANA. Como no hay ninguna opción para especificar una ruta de acceso diferente, es importante agregar espacio de disco adicional en /hana antes de la instalación de SAP HANA mediante SWPM para evitar que el sistema de archivos raíz se quede sin espacio libre.

![La estructura de archivos /hana/shared creada durante la instalación de HANA](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

Esta captura de pantalla muestra la estructura de archivos del directorio /usr/sap.

![La estructura de archivos del directorio /usr/sap](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

El último paso de la instalación distribuida de ABAP es la "instancia del servidor de aplicaciones principal".

![Instalación de ABAP que muestra la instancia del servidor de aplicaciones principal como último paso](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

Después de que se instalen la instancia del servidor de aplicaciones principal y la GUI de SAP, use la transacción "dbacockpit" para confirmar que se ha completado correctamente la instalación de SAP HANA.

![Ventana DBA Cockpit que confirma la instalación correcta](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

Como último paso, puede que desee instalar primero SAP HANA Studio en la máquina virtual del servidor de aplicaciones SAP y, a continuación, conectarse a la instancia de SAP HANA que se ejecuta en la máquina virtual del servidor de base de datos.

![Instalación de SAP HANA Studio en la máquina virtual del servidor de aplicaciones SAP](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

## <a name="install-sap-hana-manually-by-using-the-hana-lifecycle-management-tool-hdblcm"></a>Instalación manual de SAP HANA mediante la herramienta de administración del ciclo de vida de HANA (hdblcm)
Además de instalar SAP HANA como parte de una instalación distribuida mediante el uso de SWPM, puede instalar primero HANA de forma independiente mediante el uso de hdblcm. A continuación, puede instalar, por ejemplo, SAP NetWeaver 7.5. Las capturas de pantalla siguientes muestran cómo funciona este proceso.

Existen tres fuentes de información acerca de la herramienta hdblcm de HANA:

* [Choosing the Correct SAP HANA HDBLCM for Your Task (Elección de la herramienta HDBLCM de SAP Hana correcta para su tarea)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA Lifecycle Management Tools (Herramientas de administración del ciclo de vida SAP Hana)](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [SAP HANA Server Installation and Update Guide (Guía de instalación y actualización del servidor SAP Hana)](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Para evitar problemas con una configuración de id. de grupo predeterminada para el usuario \<SID de Hana\>adm (creado por la herramienta hdblcm), se debe definir un nuevo grupo denominado "sapsys" con el id. de grupo 1001 antes de instalar SAP HANA mediante hdblcm.

![Nuevo grupo "sapsys" definido mediante el uso del identificador de grupo 1001](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

Al iniciar hdblcm la primera vez, aparece un sencillo menú de inicio. Seleccione el elemento 1, **Install new system** (Instalar nuevo sistema), tal y como se muestra en la captura de pantalla siguiente.

![Opción "Install new system" (Instalar nuevo sistema) en la ventana de inicio de hdblcm](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

La captura de pantalla siguiente muestra todas las opciones principales que seleccionó anteriormente.

> [!IMPORTANT]
> Los directorios con nombre para los registros y volúmenes de datos de HANA, así como la ruta de acceso de instalación (/hana/shared en este ejemplo) y /usr/sap, no deben formar parte del sistema de archivos raíz. Los directorios pertenecen a los discos de datos de Azure que se conectaron a la máquina virtual, como se describió en la sección de configuración de la máquina virtual de Azure. Este enfoque le ayudará a evitar que el sistema de archivos raíz se quede sin espacio. También se ve que el administrador de HANA tiene el id. de usuario 1005 y forma parte del grupo sapsys (id. 1001) que se definió antes de la instalación.

![Lista de todos los componentes de SAP HANA principales seleccionados anteriormente](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

Los detalles del usuario \<SID de HANA\>adm de HANA (azdadm, en la captura de pantalla siguiente) se pueden comprobar en /etc/passwd.

![Detalles del usuario \<SID de HANA\>adm de HANA en/etc/passwd](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

Después de instalar SAP HANA mediante hdblcm, puede ver la estructura de archivos en SAP HANA Studio. El esquema SAPABAP1 que incluye todas las tablas de SAP NetWeaver aún no está disponible.

![Estructura de archivos de SAP HANA en SAP HANA Studio](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

Después de instalar SAP HANA, puede instalar SAP NetWeaver sobre él. Como se muestra en esta captura de pantalla, la instalación se realizó como una "instalación distribuida" usando SWPM (tal y como se describe en la sección anterior). Cuando instale la instancia de base de datos mediante SWPM, escriba los mismos datos mediante hdblcm (por ejemplo, nombre de host, SID de HANA y número de instancia). Posteriormente, SWPM usa la instalación existente de HANA y agrega más esquemas.

![Una instalación distribuida que se ha realizado mediante SWPM](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

La captura de pantalla siguiente muestra el paso de instalación SWPM donde escribir los datos acerca del esquema DBACOCKPIT.

![El paso de instalación de SWPM donde se escriben los datos del esquema DBACOCKPIT](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Escriba los datos acerca del esquema SAPABAP1.

![Escritura de los datos acerca del esquema SAPABAP1](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Una vez finalizada la instalación de la instancia de base de datos de SWPM, puede ver el esquema SAPABAP1 en SAP HANA Studio.

![El esquema de SAPABAP1 en SAP HANA Studio](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Finalmente, después de la instalación del servidor de aplicaciones SAP y la GUI de SAP, podrá comprobar la instancia de HANA DB con la transacción "dbacockpit".

![La instancia de HANA DB comprobada con la transacción de "dbacockpit"](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

## <a name="about-sap-azure-certifications-and-running-sap-hana-on-azure"></a>Acerca de las certificaciones de Azure de SAP y la ejecución de SAP HANA en Azure
Para más información, consulte la siguiente documentación:
* Información general de Azure de SAP sobre la ejecución de SAP en Azure con el sistema operativo Windows en modo clásico: [Uso de SAP en máquinas virtuales Windows en Azure](virtual-machines-windows-classic-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Información acerca de las plantillas existentes de SAP para su uso por parte de clientes: [Azure Quickstart Templates for SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/) (Plantillas de inicio rápido de Azure para SAP).
* Información general de Azure de SAP sobre la ejecución de SAP en Azure con el sistema operativo Linux en el modelo de Azure Resource Manager: [Uso de SAP en máquinas virtuales Linux (VM)](virtual-machines-linux-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Directorio de hardware certificado para SAP HANA, donde aparecen los tipos de máquina virtual de Azure que son compatibles para la producción: [Certified SAP HANA® Hardware Directory](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) (Directorio de hardware certificado para SAP HANA®).
* Información sobre los tamaños de máquina virtual, especialmente para cargas de trabajo de Linux: [Tamaños de las máquinas virtuales Linux en Azure](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Nota de SAP donde aparecen todos los productos SAP compatibles en Azure y los tipos de máquina virtual de Azure compatibles para SAP: [Nota de SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533/E).
* Nota de SAP sobre la "supervisión mejorada" de SAP con máquinas virtuales Linux en Azure: [Nota de SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498/E).
* Oferta de SAP HANA en "Instancias grandes" de Azure. Es importante comprender que esta información no trata sobre la ejecución de SAP HANA en máquinas virtuales de Azure. Trata acerca de un entorno híbrido en el que se ejecutan los servidores de aplicaciones SAP en máquinas virtuales de Azure pero en el que SAP HANA se ejecuta en servidores sin sistema operativo: [Nota de SAP 2316233](https://launchpad.support.sap.com/#/notes/2316233/E).
* Nota de SAP con información sobre SAPOSCOL en Linux: [Nota de SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E).
* Métricas clave de supervisión para SAP en Microsoft Azure: [Nota de SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E).
* Información sobre Azure Resource Manager: [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Información sobre la implementación de máquinas virtuales Linux mediante plantillas: [Implementación y administración de máquinas virtuales con plantillas de Azure Resource Manager y la CLI de Azure](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Comparación de los modelos de implementación clásica y de Azure Resource Manager: [La implementación de Azure Resource Manager frente a la implementación clásica: los modelos de implementación y el estado de los recursos](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="sap-software-downloads"></a>Descargas de software de SAP
Puede descargar el software desde SAP Service Marketplace, como se muestra en las siguientes capturas de pantalla.

* Descargue NetWeaver 7.5 para Linux/HANA:

 ![Ventana de instalación y actualización de SAP Service para la descarga de NetWeaver 7.5](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)
* Descargue HANA SP12 Platform Edition:

 ![Ventana de instalación y actualización de SAP Service para la descarga de HANA SP12 Platform Edition](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

