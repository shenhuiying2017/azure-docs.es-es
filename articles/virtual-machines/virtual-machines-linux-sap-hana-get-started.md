---
title: Guía de inicio rápido para la instalación manual de SAP Hana en máquinas virtuales de Azure | Microsoft Docs
description: Guía de inicio rápido para la instalación manual de SAP Hana en máquinas virtuales de Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd

---
# <a name="quickstart-guide-for-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Guía de inicio rápido para la instalación manual de SAP Hana de única instancia en máquinas virtuales de Azure
## <a name="introduction"></a>Introducción
Esta guía de inicio rápido lo ayudará a configurar un sistema de prototipo/demostración de SAP Hana de única instancia en máquinas virtuales de Azure mediante una instalación manual de SAP NetWeaver 7.5 y SAP Hana SP12.
En esta guía se da por supuesto que el lector está familiarizado con los aspectos básicos de IaaS de Azure, por ejemplo, cómo implementar máquinas virtuales o redes virtuales mediante Azure Portal o PowerShell/CLI, incluida la opción de usar plantillas json. Además se espera que el lector esté familiarizado con SAP Hana, SAP NetWeaver y cómo instalarlo de forma local.

Se espera que el lector conozca la documentación general sobre SAP y Azure mencionada en la sección de información general al final del artículo.

Dada la restricción a sistemas que no sean de producción, en esta guía no se tratarán temas como la alta disponibilidad, la copia de seguridad, la recuperación ante desastres, el alto rendimiento ni las consideraciones de seguridad especiales.

La configuración del ejemplo se llevó a cabo con dos máquinas virtuales para lograr una instalación distribuida de SAP NetWeaver por medio del modelo de Azure Resource Manager, puesto que la combinación de SAP, Linux y Azure solo se admite en el modelo de Azure Resource Manager y no en el clásico. Se ofrecen vínculos a información más detallada sobre Azure Resource Manager en la sección de información general al final de este artículo.

Estas fueron las dos máquinas virtuales de prueba usadas para la instalación de ejemplo:

* hana-appsrv (tipo DS3) para hospedar la instancia de ASCS de NW 7.5 + PAS
* hana-dbsrv (tipo GS4 ) para hospedar HANA SP12
* ambas máquinas virtuales pertenecían a una red virtual de Azure (azure-hana-test-vnet)
* el sistema operativo en ambos casos fue SLES 12 SP1

Tenga en cuenta que, a partir de julio de 2016, SAP Hana solo tiene compatibilidad total con sistemas de producción OLAP (BW) en máquinas virtuales de Azure de tipo GS5. Para realizar pruebas cuando no se espera compatibilidad oficial con SAP, se pueden usar máquinas virtuales más pequeñas, por ejemplo, GS4.
Lo que siempre se debe usar para SAP Hana en Azure es almacenamiento premium de Azure para los archivos de datos y de registro de Hana; consulte más adelante la sección "Configuración de disco". Para más detalles sobre qué productos SAP son compatibles en Azure, consulte la sección de información general al final de este artículo.

En la guía se describen dos formas diferentes de instalar manualmente SAP Hana en máquinas virtuales de Azure:

* instalar SAP Hana mediante SAP Software Provisioning Manager (SWPM) como parte de una instalación distribuida de NetWeaver en el paso de "instancia de base de datos"
* instalar SAP Hana mediante la herramienta hdblcm de Hana Life Cycle Manager y después instalar NetWeaver

También es posible utilizar SWPM e instalar todos los componentes (SAP Hana, servidor de aplicaciones SAP, instancia de ASCS, SAP GUI) en una sola máquina virtual. Esta opción no se describe en este artículo, pero los elementos que deben tenerse en cuenta son los mismos.

Antes de iniciar una instalación, debe leer la sección que sigue a las listas de comprobación a continuación, la cual trata sobre la configuración de las máquinas virtuales de prueba de Azure. El objetivo es evitar varios errores básicos que surgen cuando se usa solo una configuración de máquina virtual de Azure predeterminada.

## <a name="checklist-sap-hana-installation-via-sap-swpm"></a>Lista de comprobación para la instalación de SAP Hana por medio de SAP SWPM
Se trata de una lista simple de comprobación con los elementos fundamentales para llevar a cabo una instalación manual de SAP Hana de instancia única para demostración o creación de prototipos por medio de SAP SWPM con una instalación distribuida de SAP NW 7.5. Los elementos individuales se explican y muestran en forma de capturas de pantalla con más detalle a lo largo del artículo:

* Cree una red virtual de Azure que incluirá las dos máquinas virtuales de prueba. 
* Implemente dos máquinas virtuales de Azure con el sistema operativo SLES 12 SP1 mediante el modelo de Azure Resource Manager. 
* Conecte dos discos de almacenamiento estándar a la máquina virtual del servidor de aplicaciones (por ejemplo, de 75 GB y 500 GB).
* Conecte cuatro discos a la máquina virtual del servidor de Hana DB: 2 discos de almacenamiento estándar como para la máquina virtual del servidor de aplicaciones + 2 discos de almacenamiento premium (por ejemplo, 2 de 512 GB).
* Según los requisitos de tamaño o rendimiento, conecte varios discos y cree volúmenes seccionados mediante lvm o mdadm en el nivel de sistema operativo dentro de la máquina virtual.
* Cree sistemas de archivos XFS en los discos conectados o los volúmenes lógicos.
* Monte los nuevos sistemas de archivos XFS en el nivel de sistema operativo. Use un sistema de archivos para mantener todo el software de SAP y el otro, por ejemplo, para el directorio sapmnt y quizás copias de seguridad. En el servidor de SAP Hana DB, monte los sistemas de archivos XFS en los discos de almacenamiento premium como /hana y /usr/sap. Todo esto es necesario para evitar que el sistema de archivos raíz, que no es demasiado grande en las máquinas virtuales Linux de Azure, se llene por completo.
* Escriba las direcciones IP locales de las máquinas virtuales de prueba en /etc/hosts.
* Especifique el parámetro nofail en /etc/fstab.
* Establezca los parámetros de kernel según la nota de SAP sobre Hana SLES 12 (consulte los detalles más abajo en la sección Parámetros de kernel).
* Agregue espacio de intercambio.
* Si lo desea, instale un escritorio gráfico en las máquinas virtuales de prueba. De lo contrario, use una instalación remota con sapinst.
* Descargue el software SAP de SAP Service Marketplace.
* Instale la instancia de ASCS de SAP en la máquina virtual del servidor de aplicaciones.
* Comparta el directorio sapmnt a través de NFS entre las máquinas virtuales de prueba (la máquina virtual del servidor de aplicaciones es el servidor NFS).
* Instale la instancia de base de datos, incluido Hana, por medio de SWPM en la máquina virtual del servidor de bases de datos.
* Instale PAS en la máquina virtual del servidor de aplicaciones.
* Inicie la consola de administración de SAP y conéctese, por ejemplo, mediante SAP GUI/Hana Studio. 

## <a name="checklist-sap-hana-installation-via-hdblcm"></a>Lista de comprobación para la instalación de SAP Hana por medio de hdblcm
Se trata de una lista simple de comprobación con los elementos fundamentales para llevar a cabo una instalación manual de SAP Hana de instancia única para demostración o creación de prototipos por medio de SAP SWPM con una instalación distribuida de SAP NW 7.5. Los elementos individuales se explican y muestran en forma de capturas de pantalla con más detalle a lo largo del artículo:

* Cree una red virtual de Azure que incluirá las dos máquinas virtuales de prueba. 
* Implemente dos máquinas virtuales de Azure con el sistema operativo SLES 12 SP1 mediante el modelo de Azure Resource Manager. 
* Conecte dos discos de almacenamiento estándar a la máquina virtual del servidor de aplicaciones (por ejemplo, de 75 GB y 500 GB).
* Conecte cuatro discos a la máquina virtual del servidor de Hana DB: 2 discos de almacenamiento estándar como para la máquina virtual del servidor de aplicaciones + 2 discos de almacenamiento premium (por ejemplo, 2 de 512 GB).
* Según los requisitos de tamaño o rendimiento, conecte varios discos y cree volúmenes seccionados mediante lvm o mdadm en el nivel de sistema operativo dentro de la máquina virtual.
* Cree sistemas de archivos XFS en los discos conectados o los volúmenes lógicos.
* Monte los nuevos sistemas de archivos XFS en el nivel de sistema operativo. Use un sistema de archivos para mantener todo el software de SAP y el otro, por ejemplo, para el directorio sapmnt y quizás copias de seguridad. En el servidor de SAP Hana DB, monte los sistemas de archivos XFS en los discos de almacenamiento premium como /hana y /usr/sap. Todo esto es necesario para evitar que el sistema de archivos raíz, que no es demasiado grande en las máquinas virtuales Linux de Azure, se llene por completo.
* Escriba las direcciones IP locales de las máquinas virtuales de prueba en /etc/hosts.
* Especifique el parámetro nofail en /etc/fstab.
* Establezca los parámetros de kernel según la nota de SAP sobre Hana SLES 12 (consulte los detalles más abajo en la sección Parámetros de kernel).
* Agregue espacio de intercambio.
* Si lo desea, instale un escritorio gráfico en las máquinas virtuales de prueba. De lo contrario, use una instalación remota con sapinst.
* Descargue el software SAP de SAP Service Marketplace.
* Cree el grupo "sapsys" con el id. de grupo 1001 en la máquina virtual del servidor de Hana DB.
* Instale SAP Hana en la máquina virtual del servidor de bases de datos mediante hdblcm.
* Instale la instancia de ASCS de SAP en la máquina virtual del servidor de aplicaciones.
* Comparta el directorio sapmnt a través de NFS entre las máquinas virtuales de prueba (la máquina virtual del servidor de aplicaciones es el servidor NFS).
* Instale la instancia de base de datos, incluido Hana, por medio de SWPM en la máquina virtual del servidor de bases de datos.
* Instale PAS en la máquina virtual del servidor de aplicaciones.
* Inicie la consola de administración de SAP y conéctese, por ejemplo, mediante SAP GUI/Hana Studio. 

## <a name="prepare-azure-vms-for-manual-installation-of-sap-hana"></a>Preparación de las máquinas virtuales de Azure para la instalación manual de SAP Hana
Este capítulo acerca de la preparación de las máquinas virtuales de Azure para la instalación manual de SAP Hana consta de cinco secciones que abarcan los siguientes temas:

* Configuración de disco
* Parámetros de kernel
* Sistemas de archivos
* /etc/hosts
* /etc/fstab

### <a name="disk-setup"></a>Configuración de disco
El sistema de archivos raíz en una máquina virtual Linux en Azure tiene un tamaño limitado. Por lo tanto, es necesario conectar espacio adicional en disco a una máquina virtual para ejecutar SAP. En el caso de una máquina virtual de servidor de aplicaciones SAP que se use en un entorno puro de prototipo o demostración, usar discos de almacenamiento estándar de Azure es suficiente. Sin embargo, para los archivos de datos y de registro de SAP Hana DB, se deben usar discos de almacenamiento premium de Azure, incluso en un entorno que no sea de producción.

Consulte algunos detalles acerca de cómo conectar discos a una máquina virtual Linux [aquí](virtual-machines-linux-add-disk.md)

Cuando se trata del almacenamiento en caché de disco de Azure, se debe usar "Ninguno" para los discos que se vayan a utilizar para almacenar los registros de transacciones de Hana. Para los archivos de datos de Hana, basta con usar el almacenamiento en caché de lectura. Como Hana es una base de datos en memoria, la medida en que la memoria caché de lectura en el nivel de disco de Azure mejorará el rendimiento depende del patrón de uso general (por ejemplo, iniciando Hana y leyendo datos del disco en memoria).

Consulte detalles acerca de Azure Premium Storage [aquí](../storage/storage-premium-storage.md)

[Aquí](https://github.com/Azure/azure-quickstart-templates) encontrará plantillas json de ejemplo para crear máquinas virtuales.
La plantilla "101-vm-simple-linux" muestra el aspecto típico de una plantilla básica, como la inclusión de sección de almacenamiento que agrega un disco de datos de 100 GB.

[Este artículo](virtual-machines-linux-sap-on-suse-quickstart.md) incluye información sobre cómo buscar una imagen SUSE mediante PowerShell o la CLI y la importancia de conectar un disco mediante UUID.

Según el tamaño del sistema y los requisitos de rendimiento, podría ser necesario conectar varios discos en lugar de uno y después crear un conjunto seccionado en esos discos en el nivel de sistema operativo. Estos son los dos motivos por los que se crearía un conjunto seccionado en varios discos de Azure:

* para aumentar el rendimiento;
* se necesita un único sistema de archivos con más de 1 TB cuando el límite actual de tamaño de disco de Azure es de 1 TB (a partir de julio de 2016).

Se puede obtener más información acerca de las dos herramientas principales para configurar la creación de secciones aquí:

[Artículo sobre cómo usar mdadm para configurar RAID de software en Linux en una máquina virtual de Azure](virtual-machines-linux-configure-raid.md)

[Artículo sobre cómo configurar el administrador de volúmenes lógicos en una máquina virtual Linux de Azure](virtual-machines-linux-configure-lvm.md)

![](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

En el entorno de prueba, se conectaron dos discos de almacenamiento estándar de Azure a la máquina virtual del servidor de aplicaciones SAP. Uno se usó para almacenar todo el software SAP para la instalación (por ejemplo, NetWeaver 7.5, GUI de SAP, SAP HANA... ) y el otro para tener espacio suficiente para todo lo que pueda ser necesario (por ejemplo, realizar una copia de seguridad, probar datos), así como para el directorio sapmnt (por ejemplo, perfiles SAP) que se comparte entre todas las máquinas virtuales que pertenecen al mismo entorno SAP.

![](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

Al contrario que en la máquina virtual del servidor de aplicaciones, se conectaron cuatro discos a la máquina virtual del servidor SAP Hana. Igual que antes, se usaron dos discos para mantener el software de SAP (también se podría compartir el disco de software de SAP por medio de NFS) y proporcionar suficiente espacio, por ejemplo, para la copia de seguridad. Los dos discos adicionales eran discos de almacenamiento premium de Azure para mantener los archivos de registro y datos de SAP Hana, así como el directorio /usr/sap.

### <a name="kernel-parameters"></a>Parámetros de kernel
SAP Hana requiere configuraciones específicas para el kernel de Linux que no forman parte de las imágenes estándar de la galería de Azure y que tienen que establecerse manualmente. Existe una nota de SAP concreta donde se describen estas configuraciones. 

Nota de SAP sobre SAP HANA DB: configuración de sistema operativo recomendada para SLES 12/SLES for SAP Applications 12: [ Nota de SAP 2205917](https://launchpad.support.sap.com/#/notes/2205917)

Puede encontrar otro tema sobre el almacenamiento en caché de páginas en relación con la ejecución de SAP Hana en SLES [aquí](https://www.suse.com/documentation/sles_for_sap/singlehtml/sles_for_sap_guide/sles_for_sap_guide.html#sec.s4s.configure.page-cache) en el capítulo 6.1, que trata sobre el límite de caché de páginas en el kernel.

También existe una nota de SAP sobre el límite de caché de páginas: [Nota de SAP 1557506](https://service.sap.com/sap/support/notes/1557506)

SLES 12 tiene una herramienta nueva que reemplaza a la utilidad sapconf anterior. Es "tuned-adm" y hay disponible un perfil de SAP Hana especial. Puede encontrar más detalles acerca de esta herramienta en los dos vínculos siguientes.

La documentación de SLES sobre tuned-adm y el perfil de SAP Hana se encuentra [aquí](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html)

La documentación de SLES sobre tuned-adm y el perfil de SAP Hana (capítulo 6.2 sobre el ajuste de sistemas para cargas de trabajo de SAP con tuned-adm) se encuentra [aquí](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)

![](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

Aquí se ve cómo "tuned-adm" ha cambiado los valores de transparent_hugepage y de numa_balancing en función de las configuraciones de SAP Hana necesarias.

Para convertir en permanentes las configuraciones de kernel de SAP Hana, se debe usar grub2 en SLES 12. Puede encontrar más información acerca de grub2 [aquí](https://www.suse.com/documentation/sled-12/book_sle_admin/data/sec_grub2_file_structure.html)

![](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

Esta captura de pantalla muestra cómo se han cambiado las configuraciones del kernel en el archivo de configuración y después se han "compilado" mediante grub2-mkconfig.

![](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

Otra opción sería cambiar las configuraciones por medio de Yast y la configuración de parámetros de kernel del cargador de arranque.

### <a name="filesystems"></a>Sistemas de archivos
![](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

Aquí se ven los dos sistemas de archivos que se crearon en la máquina virtual del servidor de aplicaciones SAP usando los dos discos de almacenamiento estándar de Azure conectados. Ambos sistemas de archivos son del tipo XFS y están montados en /sapdata y /sapsoftware.

No es obligatorio hacerlo de esta manera. Existen diferentes opciones para estructurar el espacio en disco.
El aspecto más importante es evitar que el sistema de archivos raíz se quede sin espacio. 

![](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

Con respecto a la máquina virtual de SAP Hana DB, es importante saber que, durante una instalación de base de datos mediante sapinst (swpm) y usando solo la opción de instalación "típica", se instalarán elementos de forma predeterminada en /hana y /usr/sap. La configuración predeterminada para la copia de seguridad de registros de SAP Hana está, por ejemplo, en /usr/sap.
Como antes, es fundamental evitar que el sistema de archivos raíz se quede sin espacio. Por lo tanto, es necesario asegurarse de que haya suficiente espacio disponible en /hana y /usr/sap antes de instalar SAP Hana mediante swpm.

[Este artículo](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) de SAP describe el diseño del sistema de archivos estándar de SAP Hana. 

![](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)

Cuando se instale SAP NetWeaver en una imagen estándar de SLES 12 de la galería de Azure, aparecerá un mensaje que informa de que no hay espacio de intercambio. Para eliminar este mensaje, podría, por ejemplo, agregar manualmente un archivo de intercambio, como se describe en este documento, mediante dd, mkswap y swapon. Busque "Adding a Swap File Manually" (Incorporación manual de un archivo de intercambio) en [this article](https://www.suse.com/documentation/sled-12/book_sle_deployment/data/sec_yast2_i_y2_part_expert.html)

Otra opción consiste en configurar el espacio de intercambio mediante el agente de la máquina virtual Linux. Puede encontrar más información [aquí](virtual-machines-linux-agent-user-guide.md)

### <a name="/etc/hosts"></a>/etc/hosts
![](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

Otro aspecto importante antes de empezar a instalar SAP es incluir los nombres de host y las direcciones IP de las máquinas virtuales de SAP en el archivo /etc/hosts. Se deben implementar todas las máquinas virtuales de SAP dentro de una red virtual Azure y después usar las direcciones IP internas.

### <a name="/etc/fstab"></a>/etc/fstab
![](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

Durante la fase de pruebas, resultó ser buena idea agregar el parámetro nofail a fstab. Si algo fuera mal con los discos, la máquina virtual seguiría funcionando y no se bloquearía en el proceso de arranque. Aun así, hay que mantenerse vigilante porque, en este caso, es posible que el espacio en disco adicional no esté disponible y los procesos podrían llenar totalmente el sistema de archivos raíz. Si faltara /hana, SAP Hana no se iniciaría en absoluto.

## <a name="install-graphical-gnome-desktop-on-sles-12"></a>Instalación del escritorio Gnome gráfico en SLES 12
Este capítulo consta de dos secciones que abarcan los siguientes temas:

* Instalación del escritorio Gnome y xrdp en SLES 12
* Ejecución de la consola de administración de SAP basada en Java con Firefox en SLES 12

También se podrían usar alternativas como Xterminal o VNC pero, a partir de septiembre de 2016, este artículo describe solo xrdp.

### <a name="installation-of-gnome-desktop-and-xrdp-on-sles-12"></a>Instalación del escritorio Gnome y xrdp en SLES 12
Especialmente para aquellos que conozcan Microsoft Windows y deseen usar un escritorio gráfico directamente en las máquinas virtuales Linux de SAP para ejecutar Firefox, Sapinst, SAP GUI, la consola de administración de SAP o Hana Studio y posiblemente conectarse a la máquina virtual a través de RDP desde un equipo Microsoft Windows, existe una manera sencilla de lograrlo. Aunque esto podría no ser adecuado para, por ejemplo, un servidor de bases de datos de producción, sí lo es para un entorno de prototipo o demostración puro. Estos son los pasos para instalar el escritorio Gnome en una máquina virtual SLES 12 de Azure:

Instale el escritorio Gnome con el siguiente comando (por ejemplo, en una ventana de PuTTY):

   zypper in -t pattern gnome-basic

A continuación, instale xrdp para permitir la conexión a la máquina virtual a través de RDP:

   zypper in xrdp

Edite /etc/sysconfig/windowmanager y establezca el administrador de ventanas predeterminado en Gnome:

   DEFAULT_WM="gnome"

Ejecute chkconfig para asegurarse de que xrdp se inicie automáticamente después de un reinicio: 

  chkconfig -level 3 xrdp on

Si hubiera un problema con la conexión RDP, pruebe a reiniciar (quizás fuera de una ventana de PuTTY):

  /etc/xrdp/xrdp.sh restart

Si el reinicio de xrdp mencionado antes no funciona, compruebe si hay un archivo .pid y quítelo:

  check /var/run  and look for xrdp.pid   
  quítelo y, luego, intente reiniciar de nuevo.

### <a name="sap-mc"></a>Consola de administración de SAP
Si inicia la consola de administración de SAP gráfica basada en Java desde Firefox ejecutándose en una máquina virtual SLES 12 de Azure después de instalar al escritorio Gnome tal como se describe en la sección anterior, se producirá un error porque falta el complemento de explorador de Java.

La dirección URL para iniciar la consola de administración de SAP <server>:5<número_de_instancia>13.

 [aquí](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)

![](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

En la captura de pantalla anterior se ve cómo será el mensaje de error cuando falte el complemento de explorador de Java. 

![](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

Una opción para resolver el problema es instalar el complemento que falta por medio de Yast.

![](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

Si se repite la dirección URL de la consola de administración de SAP, la primera vez se abrirá un pequeño cuadro de diálogo donde se le pide que active el complemento.

Otro problema que podría aparecer es un mensaje de error sobre un archivo que falta: javafx.properties. Es muy probable que esto guarde relación con la instalación de Java 1.8, que es necesaria para SAP GUI 7.4.

La versión de IBM Java vista a través de Yast no incluye este archivo. La solución se encuentra una descarga de Java de Oracle.
 [aquí](https://scn.sap.com/thread/3908306)

## <a name="manual-sap-hana-installation-via-swpm-as-part-of-a-netweaver-7.5-installation"></a>Instalación manual de SAP Hana mediante SWPM como parte de una instalación de NetWeaver 7.5
En la siguiente serie de capturas de pantalla, se muestran los pasos clave para instalar SAP NetWeaver 7.5 y SAP Hana SP12 mediante SWPM (sapinst). Como parte de la instalación de NW 7.5, SWPM tiene la funcionalidad necesaria para instalar también la base de datos de Hana como una sola instancia.

![](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

Para el entorno de prueba, solo se instaló un servidor de aplicaciones ABAP. Se usó la opción "Distributed System" (Sistema distribuido) para instalar la instancia de ASCS y la instancia de servidor de aplicaciones principal en una máquina virtual de Azure y SAP Hana como sistema de base de datos en otra máquina virtual de Azure.

![](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

Una vez que la instancia de ASCS esté instalada en la máquina virtual del servidor de aplicaciones y se haya establecido en "verde" en la consola de administración de SAP, el directorio sapmnt, que incluye, por ejemplo, el directorio de perfiles de SAP, debe compartirse con la máquina virtual del servidor de base de datos de SAP HANA.
El paso de instalación de la base de datos necesita acceso a esta información. La mejor manera es utilizar NFS, que se puede configurar mediante Yast.

![](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

En la máquina virtual del servidor de aplicaciones, se debería compartir el directorio sapmnt mediante NFS con las opciones "rw" y "no_root_squash". El valor predeterminado es "ro" y "root_squash", lo que podría producir problemas al instalar la instancia de base de datos.

![](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

En la máquina virtual del servidor de SAP Hana DB, el recurso compartido sapmnt de la máquina virtual del servidor de aplicaciones tiene que configurarse mediante el "cliente NFS" (por ejemplo, con la ayuda de Yast).

![](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

A continuación, se debe iniciar sesión en la máquina virtual del servidor de SAP Hana DB e iniciar SWPM para llevar a cabo el paso siguiente de una instalación distribuida de NW 7.5: la instancia de base de datos.

![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

En cuanto a la instalación de SAP Hana, en realidad no hay mucho que especificar una vez seleccionada la instalación "típica". Aparte de la ruta de acceso a los medios de instalación, se debe escribir un SID de base de datos, el nombre de host, el número de instancia y la contraseña del administrador del sistema de la base de datos.

![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

El siguiente paso consiste en escribir la contraseña para el esquema DBACOCKPIT.

![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Después, aparece la pregunta para la contraseña del esquema SAPABAP1.

![](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

Al final solo se deberían ver marcas de verificación verdes junto a cada fase del proceso de instalación de base de datos y el pequeño cuadro de mensaje que emerge debería decir "Execution of… Database Instance has completed" (Ejecución de la instancia de base de datos… completada).

![](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

Tras una instalación correcta, la consola de administración de SAP también debería mostrar la instancia de base de datos "verde" y la lista completa de los procesos de SAP Hana (por ejemplo, hdbindexserver, hdbcompileserver).

![](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

Esta captura de pantalla muestra partes de la estructura de archivos en /hana/shared que SWPM creó durante la instalación de Hana. No había ninguna opción para especificar una ruta de acceso diferente. Por lo tanto, es muy importante montar espacio en disco adicional en /hana antes de la instalación de SAP Hana mediante SWPM para evitar que el sistema de archivos raíz se quede sin espacio disponible.

![](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

Aquí se puede ver lo mismo que se describió antes para el directorio /usr/sap.

![](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

El último paso de la instalación distribuida de ABAP es la "instancia de servidor de aplicaciones principal".

![](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

Una vez instalados PAS y SAP GUI, se puede comprobar mediante la transacción "dbacockpit" que todo esté bien en la instalación de SAP Hana.

![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Como último paso, podría instalar SAP Hana Studio en la máquina virtual del servidor de aplicaciones SAP y conectarse a la instancia de SAP Hana que se ejecuta en la máquina virtual del servidor de base de datos.

## <a name="manual-sap-hana-installation-via-hana-life-cycle-manager-tool-hdblcm"></a>Instalación manual de SAP Hana mediante la herramienta hdblcm de Hana Life Cycle Manager
Además de instalar SAP Hana como parte de una instalación distribuida mediante SWPM, también es más posible instalarlo de manera independiente mediante hdblcm y luego instalar, por ejemplo, SAP NetWeaver 7.5. La serie de capturas de pantalla siguiente muestra cómo funciona esto.

Existen tres fuentes de información acerca de la herramienta hdblcm de Hana:

[Choosing the Correct SAP HANA HDBLCM for Your Task (Elección de la herramienta HDBLCM de SAP Hana correcta para su tarea)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)

[SAP HANA Lifecycle Management Tools (Herramientas de administración del ciclo de vida SAP Hana)](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)

[SAP HANA Server Installation and Update Guide (Guía de instalación y actualización del servidor SAP Hana)](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

![](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

Para evitar problemas más adelante con una configuración de id. de grupo predeterminada para el usuario \<SID de Hana\>adm (creado por la herramienta hdblcm), se debe definir un nuevo grupo denominado "sapsys" con el id. de grupo 1001 antes de instalar SAP HANA mediante hdblcm.

![](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

Al iniciar hdblcm por primera vez, aparecerá un menú de inicio simple donde se debe seleccionar el elemento 1: "Install new System" (Instalar nuevo sistema).

![](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

En esta captura de pantalla se ven todas las opciones clave que se especificaron antes. Importante: los directorios asignados a los volúmenes de datos y registro de Hana, así como la ruta de instalación (/hana/shared en este ejemplo) y /usr/sap NO deberían formar parte del sistema de archivos raíz, sino que deberían pertenecer a los discos de datos de Azure que estaban conectados a la máquina virtual, como se describe en la sección sobre la configuración de la máquina virtual de Azure. Esto evitará el riesgo de que el sistema de archivos raíz se quede sin espacio.
También se ve que el usuario administrador de Hana tiene el id. de usuario 1005 y forma parte del grupo sapsys (id. 1001) que se definió antes de la instalación.

![](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

Se pueden comprobar los detalles del usuario \<SID de HANA\>adm (azdadm, en este ejemplo) en /etc/passwd.

![](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

Después de instalar SAP Hana con hdblcm, se ve en SAP Hana Studio. El esquema SAPABAP1 que incluye, por ejemplo, todas las tablas de SAP NetWeaver aún no está disponible.

![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Después de instalar SAP Hana, puede instalar SAP NetWeaver sobre él. En este ejemplo, esto se llevó a cabo con una "instalación distribuida" por medio de SWPM, como se describe en la sección anterior correspondiente.
Al instalar la instancia de base de datos mediante SWPM, se deben especificar los mismos datos que antes con hdblcm (por ejemplo, nombre de host, SID de Hana, número de instancia). Después, SWPM usará la instalación existente de Hana y agregará esquemas adicionales.

![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Esta es la imagen del paso de instalación de SWPM donde se deben escribir datos en relación con el esquema DBACOCKPIT.

![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

A continuación, SWPM espera que se especifiquen datos acerca del esquema SAPABAP1.

![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Una vez finalizada la instalación de la instancia de base de datos de SWPM, se ve el esquema SAPABAP1 en Hana Studio.

![](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

Y finalmente, después de la instalación del servidor de aplicaciones SAP y SAP GUI, debería ser posible comprobar la instancia de Hana DB con la transacción "dbacockpit".

## <a name="general-information-related-to-sap-azure-certifications,-running-sap-hana-on-azure-and-sap-software-download"></a>Información general relacionada con certificaciones de Azure de SAP, la ejecución de SAP Hana en Azure y la descarga de software de SAP
* Documento general de Azure de SAP sobre la ejecución de SAP en Azure con el sistema operativo Windows en modo clásico: [Uso de SAP en máquinas virtuales Windows en Azure](virtual-machines-windows-classic-sap-get-started.md)
* Información acerca de las plantillas existentes de SAP para su uso por parte de clientes: [Azure Quickstart Templates for SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)
* Documento general de Azure de SAP sobre la ejecución de SAP en Azure con el sistema operativo Linux en el modelo de Azure Resource Manager: [Uso de SAP en máquinas virtuales Linux (VM)](virtual-machines-linux-sap-get-started.md)
* Directorio de hardware certificado para SAP Hana, donde aparecen los tipos de máquina virtual de Azure que son compatibles para la producción: [Certified SAP HANA® Hardware Directory](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)
* Información sobre los tamaños de máquina virtual, especialmente para cargas de trabajo de Linux: [Tamaños de las máquinas virtuales Linux en Azure](virtual-machines-linux-sizes.md)
* Nota de SAP donde aparecen todos los productos SAP compatibles en Azure y los tipos de máquina virtual de Azure compatibles para SAP: [Nota de SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533/E)
* Nota de SAP sobre la "supervisión mejorada" de SAP con máquinas virtuales Linux en Azure: [Nota de SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)
* Oferta de SAP Hana en "Instancias grandes" de Azure. Es importante entender que no se trata de ejecutar SAP Hana en máquinas virtuales de Azure, sino de un entorno híbrido donde los servidores de aplicaciones SAP se ejecutan en máquinas virtuales de Azure pero SAP Hana se ejecuta en servidores sin sistema operativo: [Nota de SAP 2316233](https://launchpad.support.sap.com/#/notes/2316233/E)
* Nota de SAP con información sobre SAPOSCOL en Linux: [Nota de SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)
* Métricas clave de supervisión para SAP en Microsoft Azure: [Nota de SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)
* Información sobre Azure Resource Manager: [Información general de Azure Resource Manager](../resource-group-overview.md)
* Información sobre la implementación de máquinas virtuales Linux mediante plantillas: [Implementación y administración de máquinas virtuales con plantillas de Azure Resource Manager y la CLI de Azure](virtual-machines-linux-cli-deploy-templates.md)
* Comparación de los modelos de implementación clásica y de Azure Resource Manager: [La implementación de Azure Resource Manager frente a la implementación clásica: los modelos de implementación y el estado de los recursos](../resource-manager-deployment-model.md)
* Descargue NetWeaver 7.5 para Linux/Hana desde SAP Service Marketplace: ![](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)
* Descargue Hana SP12 Platform Edition desde SAP Service Marketplace: ![](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

<!--HONumber=Oct16_HO2-->


