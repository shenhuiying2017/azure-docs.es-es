<properties linkid="manage-linux-howto-linux-agent" urlDisplayName="Prepare a distribution" pageTitle="Prepare a distribution of Linux in Azure" metaKeywords="Azure Git CodePlex, Azure website CodePlex, Azure website Git" description="Learn how to use Git to publish to an Azure website, as well as enable continuous deployment from GitHub and CodePlex." metaCanonical="" services="virtual-machines" documentationCenter="" title="Prepare a Linux Virtual Machine for Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Preparación de una máquina virtual Linux para Azure

Una máquina virtual en Azure ejecuta el sistema operativo que elige cuando crea la máquina virtual. En Azure, el sistema operativo de una máquina virtual se almacena en un disco duro virtual en formato VHD (un archivo .vhd). Un VHD de un sistema operativo que se ha preparado para la duplicación se conoce como imagen. En este artículo se muestra cómo crear su propia imagen mediante la carga de un archivo .vhd con un sistema operativo que ha instalado y generalizado. Para obtener más información acerca de los discos y las imágenes en Azure, consulte [Administrar discos e imágenes][].

**Nota**: al crear una máquina virtual, puede personalizar la configuración del sistema operativo para facilitar la ejecución de su aplicación. La configuración que establezca se almacena en el disco de esa máquina virtual. Para obtener instrucciones, consulte [Creación de una máquina virtual personalizada][].

**Importante**: El contrato de nivel de servicio de la plataforma Azure se aplica a las máquinas virtuales que ejecutan el sistema operativo Linux solo cuando una de las distribuciones aprobadas se use con los detalles de la configuración según se indica en [este artículo][]. Todas las distribuciones de Linux que se ofrezcan en la galería de imágenes de Azure son distribuciones aprobadas con la configuración requerida.

## Requisitos previos

En este artículo se supone que tiene los siguientes elementos:

-   **Un certificado de administración**: Ha creado un certificado de administración para la suscripción para la que desea cargar un VHD, y ha exportado el certificado a un archivo .cer. Para obtener más información acerca de la creación de certificados, consulte [Crear y cargar un certificado de administración para Azure][].

-   **Sistema operativo Linux instalado en un archivo .vhd.** - Ha instalado un sistema operativo Linux compatible en un disco duro virtual. Existen varias herramientas para crear archivos .vhd. Puede utilizar una solución de virtualización como Hyper-V para crear el archivo .vhd e instalar el sistema operativo. Para obtener instrucciones, consulte [Instalar el rol Hyper-V y configurar una máquina virtual][].

    **Importante**: el reciente formato VHDX no se admite en Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el cmdlet Convert-VHD.

    Para ver una lista de distribuciones aprobadas, consulte [Linux en distribuciones aprobadas por Azure][]. También puede consultar la sección que se encuentra al final de este artículo para obtener [información para las distribuciones no aprobadas][].

-   **Herramienta de línea de comandos de Azure para Linux.** Si usa el sistema operativo Linux para crear su imagen, utilizará esta herramienta para cargar el archivo VHD. Para descargar la herramienta, consulte [Herramientas de línea de comandos de Azure para Linux y Mac][].

-   El **cmdlet Add-AzureVHD**, que forma parte del módulo PowerShell de Azure. Para descargar el módulo, consulte la página de [descargas de Azure][]. Para obtener más información, consulte [Add-AzureVhd][].

Tenga en cuenta lo siguiente para todas las distribuciones:

El Agente de Linux de Azure (Waagent) no es compatible con NetworkManager. La configuración de red puede usar el archivo ifcfg-eth0 y se puede controlar mediante los scripts ifup/ifdown. Waagent rechazará la instalación si se detecta el paquete NetworkManager.

NUMA no se admite debido a un error en las versiones del kernel de Linux inferiores a 2.6.37. La instalación de waagent deshabilitará automáticamente NUMA en la configuración GRUB para la línea de comandos del kernel de Linux.

El Agente de Linux de Azure requiere la instalación del paquete python-pyasn1.

Se recomienda que no cree una partición de intercambio en el momento de la instalación. Puede configurar el espacio de intercambio usando el Agente de Linux de Azure. Tampoco se recomienda utilizar el kernel de Linux estándar con una máquina virtual de Azure sin la revisión disponible en el [sitio web de Microsoft][] (es posible que muchas distribuciones/kernels actuales ya incluyan esta revisión).

El tamaño de todos los archivos VHD debe ser múltiplo de 1 MB.

Esta tarea incluye los siguientes pasos:

-   [Paso 1: Preparar la imagen que se va a cargar][]
-   [Paso 2: Crear una cuenta de almacenamiento en Azure][]
-   [Paso 3: Preparar la conexión a Azure][]
-   [Paso 4: Cargar la imagen en Azure][]

## <span id="prepimage"></span> </a>Paso 1: Preparar la imagen que se va a cargar

### Preparación del sistema operativo CentOS 6.2+

Debe completar los pasos de configuración específicos del sistema operativo para que la máquina virtual se ejecute en Azure.

1.  Seleccione la máquina virtual en el panel central del Administrador de Hyper-V.

2.  Haga clic en **Connect** para abrir la ventana de la máquina virtual.

3.  Desinstale NetworkManager ejecutando el comando siguiente:

        rpm -e --nodeps NetworkManager

    **Nota:** si el paquete todavía no está instalado, se producirá un mensaje de error en este comando. Se espera que esto sea así.

4.  Cree un archivo llamado **network** en el directorio `/etc/sysconfig/` que contenga el texto siguiente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  Cree un archivo llamado **ifcfg-eth0** en el directorio `/etc/sysconfig/network-scripts/` que contenga el texto siguiente:

        DEVICE=eth0
        ONBOOT=yes
        DHCP=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Habilite el servicio de red ejecutando el comando siguiente:

        chkconfig network on

7.  CentOS 6.2 o 6.3: Instalar los controladores para los servicios de integración de Linux

    **Nota:** el paso solo es válido para CentOS 6.2 y 6.3. En CentOS 6.4+ los servicios de integración de Linux ya están disponibles en el kernel.

    a) Obtenga el archivo .iso que contiene los controladores para los servicios de integración de Linux en el [Centro de descarga][].

    b) En el Administrador de Hyper-V, en el panel **Actions**, haga clic en **Settings**.

    ![Abrir la configuración de Hyper-V][]

    c) En el panel **Hardware**, haga clic en **IDE Controller 1**.

    ![Agregar una unidad de DVD con medio de instalación][]

    d) En el cuadro **IDE Controller**, haga clic en **DVD Drive** y, a continuación, en **Add**.

    e) Seleccione **Image file**, vaya a **Linux IC v3.2.iso** y, a continuación, haga clic en **Open**.

    f) En la página **Settings**, haga clic en **OK**.

    g) Haga clic en **Connect** para abrir la ventana de la máquina virtual.

    h) En la ventana del símbolo del sistema, escriba los comandos siguientes:

        mount /dev/cdrom /media
        /media/install.sh
        reboot

8.  Instale python-pyasn1 ejecutando el comando siguiente:

        sudo yum install python-pyasn1

9.  Reemplace su archivo /etc/yum.repos.d/CentOS-Base.repo por el texto siguiente:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

10. Agregue las líneas siguientes a /etc/yum.conf

        http_caching=packages
        exclude=kernel*

11. Deshabilite "fastestmirror" del módulo yum mediante la edición del archivo "/etc/yum/pluginconf.d/fastestmirror.conf" y, en [main], escriba lo siguiente:

        set enabled=0

12. Ejecute el comando siguiente para borrar los metadatos de yum actuales:

        yum clean all

13. Para CentOS 6.2 y 6.3, actualice un kernel de la máquina virtual en funcionamiento ejecutando los comandos siguientes:

    En CentOS 6.2, ejecute los siguientes comandos:

        sudo yum remove kernel-firmware
        sudo yum --disableexcludes=all install kernel

    En CentOS 6.3+, escriba lo siguiente:

        sudo yum --disableexcludes=all install kernel

14. Modifique la línea de arranque del kernel en Grub para incluir los parámetros siguientes. Así también se asegurará de que todos los mensajes de la consola se envíen al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

15. Asegúrese de que todos los dispositivos SCSI montados en el kernel incluyen un tiempo de espera de E/S de como mínimo 300 segundos.

16. En /etc/sudoers, convierta en comentarios la línea siguiente, si existe:

        Defaults targetpw

17. Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque.

18. Instale el Agente de Linux de Azure ejecutando el comando siguiente:

        yum install WALinuxAgent

19. No cree un espacio de intercambio en el disco del sistema operativo.

    El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique apropiadamente los parámetros siguientes en /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

20. Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

21. Haga clic en **Shutdown** en el Administrador de Hyper-V.

### Preparación del sistema operativo Ubuntu 12.04+

1.  Seleccione la máquina virtual en el panel central del Administrador de Hyper-V.

2.  Haga clic en **Connect** para abrir la ventana de la máquina virtual.

3.  Reemplace los repositorios actuales de su imagen para que usen los repositorios de Azure que contengan el paquete de agente y kernel que necesitará para actualizar la máquina virtual. Los pasos varían ligeramente en función de la versión de Ubuntu.

    Antes de modificar /etc/apt/sources.list, se recomienda que realice una copia de seguridad de
     sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak.

    Ubuntu 12,04:

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
        sudo apt-get update

    Ubuntu 12.10:

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
        sudo apt-get update

    Ubuntu 13.04+:

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-get update

4.  Actualice el sistema operativo con el kernel más reciente ejecutando los comandos siguientes:

    Ubuntu 12,04:

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

    Ubuntu 12.10:

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

    Ubuntu 13.04 y 13.10:

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

5.  Después de que se produzca un bloqueo del sistema, Ubuntu esperará al aviso de Grub para que el usuario introduzca la información. Para impedir que esto ocurra, realice los pasos siguientes:

    a) Abra el archivo /etc/grub.d/00\_header.

    b) En la función **make\_timeout()**, busque **if ["\\${recordfail}" = 1 ]; then**

    c) Cambie la instrucción que se encuentra debajo de esta línea a **set timeout=5**.

    d) Ejecute update-grub.

6.  Modifique la línea de arranque del kernel en Grub o Grub2 para incluir los parámetros siguientes. Así también se asegurará de que todos los mensajes de la consola se envíen al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

7.  En /etc/sudoers, convierta en comentarios la línea siguiente, si existe:

        Defaults targetpw

8.  Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque.

9.  Instale el agente ejecutando los comandos siguientes con sudo:

        apt-get update
        apt-get install walinuxagent 

10. No cree un espacio de intercambio en el disco del sistema operativo.

    El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique apropiadamente los parámetros siguientes en /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12. Haga clic en **Shutdown** en el Administrador de Hyper-V.

### Preparación del sistema operativo SUSE Linux Enterprise Server 11 SP2 y SP3

**NOTA:** [SUSE Studio][] puede crear y administrar fácilmente sus imágenes de SLES/opeSUSE para Azure y Hyper-V. Además, las imágenes oficiales siguientes de SUSE Studio Gallery se pueden descargar o clonar en su propia cuenta de SUSE Studio para personalizarlas fácilmente:

> -   [SLES 11 SP2 for Azure on SUSE Studio Gallery][]
> -   [SLES 11 SP3 for Azure on SUSE Studio Gallery][]

1.  Seleccione la máquina virtual en el panel central del Administrador de Hyper-V.

2.  Haga clic en **Connect** para abrir la ventana de la máquina virtual.

3.  Agregue el repositorio que contiene el kernel y el Agente de Linux de Azure más recientes. Ejecute el comando `zypper lr`. Por ejemplo, con SLES 11 SP3 la salida debería ser similar a la siguiente:

        # | Alias                        | Name               | Enabled | Refresh
        --+------------------------------+--------------------+---------+--------
        1 | susecloud:SLES11-SP1-Pool    | SLES11-SP1-Pool    | No      | Yes
        2 | susecloud:SLES11-SP1-Updates | SLES11-SP1-Updates | No      | Yes
        3 | susecloud:SLES11-SP2-Core    | SLES11-SP2-Core    | No      | Yes
        4 | susecloud:SLES11-SP2-Updates | SLES11-SP2-Updates | No      | Yes
        5 | susecloud:SLES11-SP3-Pool    | SLES11-SP3-Pool    | Yes     | Yes
        6 | susecloud:SLES11-SP3-Updates | SLES11-SP3-Updates | Yes     | Yes

    En caso de que el comando devuelva un mensaje de error como el siguiente:

        "No repositories defined. Use the 'zypper addrepo' command to add one or more repositories."

    puede que los repositorios tengan que volver a habilitarse o que haya que registrar el sistema. Esto se puede hacer mediante la utilidad suse\_register. Para obtener más información, consulte la [documentación SLES][].

En caso de que no haya un repositorio de actualizaciones relevante habilitado, habilítelo con el comando siguiente:

        zypper mr -e [REPOSITORY NUMBER]

En el caso anterior, el comando correcto sería:

        zypper mr -e 1 2 3 4

1.  Actualice el kernel a la versión más reciente disponible:

        zypper up kernel-default

2.  Instale el Agente de Linux de Azure:

        zypper up WALinuxAgent

    Debería ver un mensaje similar al siguiente:

        "There is an update candidate for 'WALinuxAgent', but it is from different vendor.
        Use 'zypper install WALinuxAgent-1.2-1.1.noarch' to install this candidate."

    Como el proveedor del paquete ha cambiado de "Microsoft Corporation" a "SUSE LINUX Products GmbH, Nuremberg, Germany", uno tiene que instalar explícitamente el paquete como se indica en el mensaje.

    Nota: la versión del paquete WALinuxAgent podría ser ligeramente diferente.

3.  Modifique la línea de arranque del kernel en Grub para incluir los parámetros siguientes. Así también se asegurará de que todos los mensajes de la consola se envíen al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

4.  Se recomienda establecer /etc/sysconfig/network/dhcp o equivalente de DHCLIENT\_SET\_HOSTNAME="yes" a DHCLIENT\_SET\_HOSTNAME="no"

5.  En /etc/sudoers, convierta en comentarios la línea siguiente, si existe:

        Defaults targetpw

6.  Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque.

7.  No cree un espacio de intercambio en el disco del sistema operativo.

    El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique apropiadamente los parámetros siguientes en /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

8.  Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

9.  Haga clic en **Shutdown** en el Administrador de Hyper-V.

### Preparación del sistema operativo openSUSE 12.3

**NOTA:** [SUSE Studio][] puede crear y administrar fácilmente sus imágenes de SLES/opeSUSE para Azure y Hyper-V. Además, las imágenes oficiales siguientes de SUSE Studio Gallery se pueden descargar o clonar en su propia cuenta de SUSE Studio para personalizarlas fácilmente:

> -   [openSUSE 12.3 for Azure on SUSE Studio Gallery][]

1.  Seleccione la máquina virtual en el panel central del Administrador de Hyper-V.

2.  Haga clic en **Connect** para abrir la ventana de la máquina virtual.

3.  Actualice el sistema operativo con el kernel y las revisiones más recientes

4.  En el shell, ejecute el comando '`zypper lr`'. Si este comando devuelve

        # | Alias                     | Name                      | Enabled | Refresh
        --+---------------------------+---------------------------+---------+--------
        1 | Cloud:Tools_openSUSE_12.3 | Cloud:Tools_openSUSE_12.3 | Yes     | Yes
        2 | openSUSE_12.3_OSS         | openSUSE_12.3_OSS         | Yes     | Yes
        3 | openSUSE_12.3_Updates     | openSUSE_12.3_Updates     | Yes     | Yes

entonces los repositorios están configurados según lo previsto y no es necesario realizar ajustes.

En caso de que el comando devuelva "No repositories defined. Use the 'zypper addrepo' command to add one
or more repositories", habrá que volver a habilitar los repositorios:

        zypper ar -f http://download.opensuse.org/distribution/12.3/repo/oss openSUSE_12.3_OSS
        zypper ar -f http://download.opensuse.org/update/12.3 openSUSE_12.3_Updates

Verifique que sus repositorios se hayan agregado llamando a 'zypper lr' de nuevo.

En caso de que no haya un repositorio de actualizaciones relevante habilitado, habilítelo con el comando siguiente:

        zypper mr -e [NUMBER OF REPOSITORY]

1.  Deshabilite el sondeo de DVD ROM automático.

2.  Instale el Agente de Linux de Azure:

    En primer lugar, agregue el repositorio que contiene el nuevo WALinuxAgent:

        zypper ar -f -r http://download.opensuse.org/repositories/Cloud:/Tools/openSUSE_12.3/Cloud:Tools.repo

    Luego, ejecute el siguiente comando:

        zypper up WALinuxAgent

    Después de ejecutar este comando, puede que aparezca un mensaje similar al siguiente:

        "There is an update candidate for 'WALinuxAgent', but it is from different vendor. 
        Use 'zypper install WALinuxAgent' to install this candidate."

    Este mensaje es el previsto. Como el proveedor del paquete ha cambiado de "Microsoft Corporation" a "obs://build.opensuse.org/Cloud", hay que instalar explícitamente el paquete tal como se ha mencionado en el mensaje.

3.  Modifique la línea de arranque del kernel en Grub para incluir los parámetros siguientes. Así también se asegurará de que todos los mensajes de la consola se envíen al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Y en /boot/grub/menu.lst, elimine los parámetros siguientes de la línea de comandos del kernel, si es que están:

        libata.atapi_enabled=0 reserve=0x1f0,0x8

4.  Se recomienda establecer /etc/sysconfig/network/dhcp o equivalente de DHCLIENT\_SET\_HOSTNAME="yes" a DHCLIENT\_SET\_HOSTNAME="no"

5.  En /etc/sudoers, convierta en comentarios la línea siguiente, si existe:

        Defaults targetpw

6.  Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque.

7.  No cree un espacio de intercambio en el disco del sistema operativo.

    El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique apropiadamente los parámetros siguientes en /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

8.  Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

9.  Asegúrese de que el Agente de Linux de Azure se ejecute al inicio:

        systemctl enable waagent.service

10. Haga clic en **Shutdown** en el Administrador de Hyper-V.

## <span id="createstorage"></span> </a>Paso 2: Crear una cuenta de almacenamiento en Azure

Una cuenta de almacenamiento representa el más alto nivel del espacio de nombres para el acceso a los servicios de almacenamiento, y está asociada con la suscripción de Azure. Necesitará una cuenta de almacenamiento en Azure para cargar en Azure el archivo .vhd que se va a utilizar para crear una máquina virtual. Puede crear una cuenta de almacenamiento en el Portal de administración de Azure.

1.  Inicie sesión en el Portal de administración de Azure.

2.  En la barra de comandos, haga clic en **New**.

    ![Crear una cuenta de almacenamiento][]

3.  Haga clic en **Cuenta de almacenamiento** y, a continuación, en **Quick Create**.

    ![Creación rápida de una cuenta de almacenamiento][]

4.  Rellene los campos de la manera siguiente:

    ![Escribir los detalles de la cuenta de almacenamiento][]

-   En **URL**, escriba el nombre de subdominio que vaya usar en la URL para la cuenta de almacenamiento. Esta entrada puede contener de 3 a 24 letras minúsculas y números. Este nombre se convierte en el nombre del host dentro de la URL que se ha usado para direccionar los recursos Blob, Cola o Tabla de la suscripción.

-   Elija la ubicación o el grupo de afinidad para la cuenta de almacenamiento. Al especificar un grupo de afinidad, puede colocar sus servicios en la nube del mismo centro de datos con su almacenamiento.

-   Decida si va a utilizar la replicación geográfica para la cuenta de almacenamiento. La replicación geográfica está activada de forma predeterminada. Esta opción replica los datos en una ubicación secundaria, sin coste para usted, de modo que si se produce un error principal que no se pueda gestionar en la ubicación primaria, su almacenamiento conmuta por error a una ubicación secundaria. La ubicación secundaria se asigna automáticamente y no se puede cambiar. En caso de que se necesite un control más estricto sobre la ubicación de su almacenamiento basado en la nube por exigencias legales o de la política organizativa, puede desactivar la replicación geográfica. Sin embargo, tenga en cuenta que si más tarde activa la replicación geográfica, deberá pagar una cuota de transferencia de datos puntual para replicar los datos existentes en la ubicación secundaria. Los servicios de almacenamiento sin replicación geográfica se ofrecen con descuento.

1.  Haga clic en **Crear cuenta de almacenamiento**.

    La cuenta aparece ahora en **Cuentas de almacenamiento**.

    ![Cuenta de almacenamiento creada correctamente][]

## <span id="#connect"></span> </a>Paso 3: Preparar la conexión a Azure

Antes de cargar el archivo .vhd, debe establecer una conexión segura entre el equipo y la suscripción de Azure.

1.  Abra una ventana de Azure PowerShell.

2.  Tipo:

    `Get-AzurePublishSettingsFile`

    Este comando abre una ventana de explorador y descarga automáticamente un archivo .publishsettings que contiene información y un certificado para su suscripción de Azure.

3.  Guarde el archivo .publishsettings.

4.  Tipo:

    `Import-AzurePublishSettingsFile <PathToFile>`

    Donde `<PathToFile>` es la ruta de acceso completa al archivo .publishsettings.

    Para obtener información, consulte [Introducción a los cmdlets de Azure][].

## <span id="upload"></span> </a>Paso 4: Cargar la imagen en Azure

Cuando carga el archivo .vhd, puede colocarlo en cualquier parte del almacenamiento de blobs. En los siguientes ejemplos de comandos, **BlobStorageURL** es la URL de la cuenta de almacenamiento que ha creado en el paso 2 y **YourImagesFolder** es el contenedor dentro del almacenamiento de blobs donde desea almacenar sus imágenes. **VHDName** es la etiqueta que aparece en el Portal de administración para identificar el disco duro virtual. **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd.

Realice una de las operaciones siguientes:

-   Desde la ventana de Azure PowerShell que ha usado en el paso anterior, escriba:

    `Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

    Para obtener más información, consulte [Add-AzureVhd][].

-   Utilice la herramienta de línea de comandos de Linux para cargar la imagen. Puede cargar una imagen mediante el siguiente comando:

        Azure vm image create <image name> --location <Location of the data center> --OS Linux <Sourcepath to the vhd>

## <span id="nonendorsed"></span> </a>Información para las distribuciones no aprobadas

En esencia, todas las distribuciones que se ejecutan en Azure tendrán que cumplir los siguientes requisitos previos para poder ejecutarse correctamente en la plataforma.

Esta lista no pretende ser exhaustiva ya que cada distribución es diferente; y es bastante posible que aunque cumpla todos los criterios siguientes, todavía tenga que ajustar significativamente la imagen para asegurarse de que se ejecuta correctamente en la plataforma.

Por este motivo, le recomendamos que empiece con una de nuestras [imágenes aprobadas de asociados][].

La lista que aparece a continuación reemplaza el paso 1 del proceso de creación de su propio VHD:

1.  Tendrá que asegurarse de que está ejecutando un kernel que incorpore los últimos controlares LIS para Hyper-V o que los ha compilado correctamente (si eran de código abierto). Podrá encontrar los controladores [en esta ubicación][] (en inglés).

2.  El kernel también debería incluir la versión más reciente del controlador ATA PiiX que se utiliza para aprovisionar las imágenes y tiene las revisiones confirmadas al kernel con confirmación cd006086fa5d91414d8ff9ff2b78fbb593878e3c Date: Fri May 4 22:15:11 2012 +0100 ata\_piix: aplazar discos a los controladores de Hyper-V de manera predeterminada

3.  Su imagen intird comprimida debe tener menos de 40 MB (\* trabajamos constantemente para aumentar esta cantidad por lo que, en el momento en que lea esto, podría estar desactualizada).

4.  Modifique la línea de arranque del kernel en Grub o Grub2 para incluir los parámetros siguientes. Así también se asegurará de que todos los mensajes de la consola se envíen al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

5.  Se recomienda establecer /etc/sysconfig/network/dhcp o equivalente de DHCLIENT\_SET\_HOSTNAME="yes" a DHCLIENT\_SET\_HOSTNAME="no"

6.  Debería asegurarse de que todos los dispositivos SCSI montados en el kernel incluyen un tiempo de espera de E/S de como mínimo 300 segundos.

7.  Tendrá que instalar el Agente de Linux de Azure siguiendo los pasos de la [Guía del Agente de Linux][]. El agente se ha publicado bajo la licencia Apache 2 y puede obtener las secciones más recientes en [Ubicación de GitHub del agente][] (en inglés).

8.  En /etc/sudoers, convierta en comentarios la línea siguiente, si existe:

        Defaults targetpw

9.  Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque.

10. No cree un espacio de intercambio en el disco del sistema operativo.

    El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique apropiadamente los parámetros siguientes en /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Tendrá que ejecutar los comandos siguientes para desaprovisionar la máquina virtual:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12. A continuación, tendrá que apagar la máquina virtual y continuar con la carga.

  [Administrar discos e imágenes]: http://msdn.microsoft.com/es-es/library/windowsazure/jj672979.aspx
  [Creación de una máquina virtual personalizada]: /es-es/manage/windows/how-to-guides/custom-create-a-vm/
  [este artículo]: http://support.microsoft.com/kb/2805216
  [Crear y cargar un certificado de administración para Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/gg551722.aspx
  [Instalar el rol Hyper-V y configurar una máquina virtual]: http://technet.microsoft.com/es-es/library/hh846766.aspx
  [Linux en distribuciones aprobadas por Azure]: ../linux-endorsed-distributions
  [información para las distribuciones no aprobadas]: #nonendorsed
  [Herramientas de línea de comandos de Azure para Linux y Mac]: http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409
  [descargas de Azure]: /es-es/develop/downloads/
  [Add-AzureVhd]: http://msdn.microsoft.com/es-es/library/windowsazure/dn205185.aspx
  [sitio web de Microsoft]: http://go.microsoft.com/fwlink/?LinkID=253692&clcid=0x409
  [Paso 1: Preparar la imagen que se va a cargar]: #prepimage
  [Paso 2: Crear una cuenta de almacenamiento en Azure]: #createstorage
  [Paso 3: Preparar la conexión a Azure]: #connect
  [Paso 4: Cargar la imagen en Azure]: #upload
  [Centro de descarga]: http://www.microsoft.com/es-es/download/details.aspx?id=34603
  [Abrir la configuración de Hyper-V]: ./media/virtual-machines-linux-how-to-prepare/settings.png
  [Agregar una unidad de DVD con medio de instalación]: ./media/virtual-machines-linux-how-to-prepare/installiso.png
  [SUSE Studio]: http://www.susestudio.com
  [SLES 11 SP2 for Azure on SUSE Studio Gallery]: http://susestudio.com/a/02kbT4/sles-11-sp2-for-windows-azure
  [SLES 11 SP3 for Azure on SUSE Studio Gallery]: http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure
  [documentación SLES]: https://www.suse.com/documentation/sles11/
  [openSUSE 12.3 for Azure on SUSE Studio Gallery]: http://susestudio.com/a/02kbT4/opensuse-12-3-for-windows-azure
  [Crear una cuenta de almacenamiento]: ./media/virtual-machines-linux-how-to-prepare/create.png
  [Creación rápida de una cuenta de almacenamiento]: ./media/virtual-machines-linux-how-to-prepare/storage-quick-create.png
  [Escribir los detalles de la cuenta de almacenamiento]: ./media/virtual-machines-linux-how-to-prepare/storage-create-account.png
  [Cuenta de almacenamiento creada correctamente]: ./media/virtual-machines-linux-how-to-prepare/Storagenewaccount.png
  [Introducción a los cmdlets de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/jj554332.aspx
  [imágenes aprobadas de asociados]: https://www.windowsazure.com/es-es/manage/linux/other-resources/endorsed-distributions/
  [en esta ubicación]: http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409
  [Guía del Agente de Linux]: https://www.windowsazure.com/es-es/manage/linux/how-to-guides/linux-agent-guide/
  [Ubicación de GitHub del agente]: http://go.microsoft.com/fwlink/p/?LinkID=250998&clcid=0x409
