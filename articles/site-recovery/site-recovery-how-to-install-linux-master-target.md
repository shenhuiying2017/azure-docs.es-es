---
title: "Cómo instalar el servidor de destino maestro de Linux para realizar una conmutación por error desde Azure a un entorno local | Microsoft Docs"
description: "Antes de volver a proteger una VM de Linux, necesita un servidor de destino principal de Linux. Aquí le mostraremos cómo instalar uno."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: c4fb25880584add0832464d9049dc6c78059c48b
ms.openlocfilehash: 8ac18526e6781f189444233a191aa1d6c5b863ff
ms.lasthandoff: 02/22/2017


---
# <a name="how-to-install-linux-master-target-server"></a>Instalación del servidor de destino maestro de Linux
Una vez que se han conmutado por error las máquinas virtuales, puede conmutarlas por recuperación a un entorno local. Para realizar una conmutación por recuperación, primero debe hacer que la máquina virtual esté en estado protegido volviendo a proteger la máquina virtual desde Azure a un entorno local. Para ello, necesitará un servidor de destino maestro que reciba el tráfico local. Si la máquina virtual protegida es una máquina virtual Windows, necesitará un destino maestro de Windows. Si se trata de una máquina virtual Linux, necesita un servidor de destino maestro de Linux para volver a protegerla. Lea los pasos siguientes sobre cómo crear e instalar un destino maestro de Linux.

## <a name="overview"></a>Información general
Este artículo proporciona información e instrucciones para instalar un destino maestro de Linux.

Publique cualquier comentario o pregunta que tenga al final del artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="pre-requisites"></a>Requisitos previos

* Para elegir correctamente el host en el que tiene que implementar el destino maestro, determine si la conmutación por recuperación va a ser para una máquina virtual existente en un entorno local o para una nueva máquina virtual (porque la máquina virtual local se eliminó). 
    * Para una máquina virtual existente, el host del destino maestro debe tener acceso a almacenes de datos de la máquina virtual.
    * Si la máquina virtual no existe en local, la máquina virtual de conmutación por recuperación se creará en el mismo host del destino maestro. Puede elegir cualquier host ESXi para instalar el destino maestro.
* El destino principal debe estar en una red que pueda comunicarse con el servidor de proceso y el servidor de configuración.
* La versión del destino principal debe ser menor o igual que el servidor de proceso y el servidor de configuración (ejemplo, si el servidor de configuración está en la versión 9.4, el destino principal puede estar en 9.4 y 9.3, pero no en 9.5).
* El destino principal solo puede tener una máquina virtual de VMware y no una máquina virtual física.


## <a name="steps-to-deploy-the-master-target-server"></a>Pasos para implementar el servidor de destino maestro

### <a name="install-centos-66-minimal"></a>Instalación de la versión CentOS 6.6 mínima

Siga los pasos que se mencionan a continuación para instalar CentOS 6.6: sistema operativo de 64 bits.

1. Desde uno de los vínculos siguientes, elija el reflejo más cercano para descargar un ISO de CentOS 6.6 mínimo de 64 bits.

    <http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    Mantenga el ISO de CentOS 6.6 mínimo de 64 bits en la unidad de DVD y arranque el sistema.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

2. Seleccione **Skip** (Omitir) para pasar por alto el proceso de comprobación de los medios.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

3. Ahora puede ver la pantalla de bienvenida de la instalación. A continuación, haga clic en el botón **Next** (Siguiente).

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

4. Seleccione **English** (Inglés) como idioma preferido y haga clic en **Next** (Siguiente) para continuar.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

5. Seleccione **US English** (Inglés de EE. UU.) como distribución del teclado. Haga clic en **Next** (Siguiente) para continuar con la instalación.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

6. Seleccione el tipo de dispositivos en que realizará la instalación. Seleccione **Basic storage Devices** (Dispositivos de almacenamiento básico). Haga clic en **Next** (Siguiente) para continuar con la instalación.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

7. Aparece un mensaje de advertencia que indica que se eliminarán los datos existentes en la unidad de disco duro. Asegúrese de que la unidad de disco duro no tiene ningún dato importante y haga clic en **Yes, discard any data** (Sí, eliminar todos los datos).

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

8. Escriba el nombre de host para el servidor en el **cuadro de texto Hostname** (Nombre de host). Haga clic en **Configure Network** (Configurar red). En la ventana **Network Connection** (Conexión de red), seleccione la interfaz de red. Haga clic en el botón **Edit** (Editar) para configurar los ajustes de IPV4.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

9. A continuación, aparece la ventana **Editing System etho** (Edición del ethos del sistema). Active la casilla **Connect automatically** (Conectarse automáticamente). En la pestaña "IPv4 Settings" (Configuración de IPv4), elija el método **Manual** y, a continuación, haga clic en el botón **Add** (Agregar). Proporcione los detalles de la dirección IP estática, la máscara de red, la puerta de enlace y el servidor DNS. Haga clic en **Apply** (Aplicar) para guardar los detalles.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

10. Seleccione la zona horaria en el cuadro combinado y haga clic en **Next** (Siguiente) para continuar.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

11. Escriba la contraseña raíz en **Root password** y confirme la contraseña, haga clic en **Next** (Siguiente) para continuar.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

12. Seleccione **Create Custom Layout** (Crear diseño personalizado) como modo de partición y haga clic en **Next** (Siguiente) para continuar.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

13. Seleccione la partición **Free** (Gratis) y haga clic en **Create** (Crear) para crear las particiones **/****/var/crash** y **/home** con el tipo de sistema de archivo **ext4**. Crear **Swap partition** (Intercambiar partición) con **swap** (Intercambiar) como tipo de sistema de archivo. Para asignar el tamaño de la partición, siga la fórmula de la asignación de tamaño como se menciona en la tabla siguiente.

    > [!NOTE]
    > El sistema de destino maestro de Linux (MT) no debe usar LVM para los espacios de almacenamiento raíz o de retención. Destino maestro de Linux configurado para evitar la detección de discos y particiones LVM de forma predeterminada.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

14. Después de la creación de la partición, haga clic en **Next** (Siguiente) para continuar.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

15. Si no se encuentra ningún dispositivo existente previamente, aparece el mensaje de advertencia para dar formato. Haga clic en **Format** (Format) para dar formato al disco duro con la tabla de particiones más reciente.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)

16. Haga clic en **Write change to disk** (Escribir cambios en el disco) para aplicar los cambios de partición en el disco.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

17. Active la opción **Install boot loader** (Instalar cargador de arranque) y haga clic en **Next** (Siguiente) para instalar el cargador de arranque en la partición raíz.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)


18. Se inicia el proceso de instalación. Puede supervisar el progreso de instalación.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

19. Si el proceso de instalación se completa correctamente, aparece la pantalla siguiente. Haga clic en **Reboot** (Reiniciar).

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>Pasos posteriores a la instalación
A continuación, prepararemos la máquina para configurarla como un servidor de destino maestro.

Para obtener los Id. de SCSI de los discos duros SCSI de en una máquina virtual con Linux, debe habilitar el parámetro "disk. EnableUUID = TRUE ".

Para habilitar este parámetro, siga los pasos que se indican a continuación:

a. Apague la máquina virtual.

b. Haga clic con el botón derecho en la entrada de la máquina virtual del panel izquierdo y seleccione **Editar configuración**.

c. Haga clic en la pestaña **Opciones** .

d. Seleccione el elemento **Opciones avanzadas&gt;>Genera**l a la izquierda y haga clic en **Parámetros de configuración** en la parte derecha.

![](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

Cuando la máquina esté en ejecución, la opción "Parámetros de configuración" estará en estado desactivado". Para activar esta pestaña, apague la máquina.

e. ¿Ve si existe una fila con **disk.EnableUUID**?

f. Si el valor existe y está establecido en False sobrescriba el valor con True (los valores True y False no distinguen entre mayúsculas y minúsculas).

g. Si el valor existe y está establecido en true, haga clic en Cancelar.
    
h. Si el valor no existe, haga clic en **Agregar fila**.

i. Agregue el disco. Active UUID en la columna Name (Nombre) y establezca su valor en TRUE

   * Si existe y si el valor se establece en False sobrescriba el valor con True (los valores True y False no distinguen entre mayúsculas y minúsculas).

   * Si existe y está establecida en True, haga clic en Cancelar y pruebe el comando SCSI en el sistema operativo invitado después de que haya arrancado.

f. Si no existe, haga clic **Agregar fila**.

  Agregue disk.EnableUUID en la columna Nombre.

  Establezca su valor en TRUE



![](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-the-additional-packages"></a>Descarga e instalación de los paquetes adicionales

> [!NOTE]
> Asegúrese de que el sistema tiene conectividad a Internet antes de descargar e instalar paquetes adicionales; de lo contrario, tendrá que buscar estos paquetes RPM manualmente e instalarlos.

```
yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

El comando anterior descargará los quince paquetes siguientes del repositorio de CentOS 6.6 y los instalará. Si no tiene acceso a Internet, deberá descargar los RPM siguientes.


bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm

perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

wget-1.12-5.el6\_6.1.x86\_64.rpm


#### <a name="install-additional-packages-for-specific-operating-systems"></a>Instalación de paquetes adicionales para sistemas operativos específicos

> [!NOTE]
> Si las máquinas protegidas de origen utilizan los sistemas de archivos Reiser o XFS para el dispositivo raíz o de inicio, se deben descargar e instalar los siguientes paquetes en el destino maestro de Linux antes de la protección.
 

***ReiserFS (si se usa en Suse11SP3. No obstante, ReiserFS no es el sistema de archivos predeterminado en Suse11SP3)***

```
cd /usr/local

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

***XFS (RHEL, CentOS 7 y versiones posteriores)***

```
cd /usr/local

wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

yum install device-mapper-multipath 
```
Se requiere para habilitar paquetes de múltiples rutas en el servidor del destino principal.

### <a name="get-the-installer-for-setup"></a>Obtención del instalador para la configuración

Si tiene acceso a Internet desde el servidor de destino maestro, puede descargar el instalador realizando los pasos que aparecen a continuación. De lo contrario, puede copiar el instalador del servidor de proceso e instalarlo.

#### <a name="download-the-mt-installation-packages"></a>Descarga de los paquetes de instalación del destino principal

Descargue los bits de instalación de destino principal de Linux más recientes desde aquí: [https://aka.ms/latestlinuxmobsvc](https://aka.ms/latestlinuxmobsvc).

Para realizar la descarga a través de Linux, escriba lo siguiente: 

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

Asegúrese de que se descargue y descomprima el programa de instalación solo en el directorio de inicio. Si descomprime en into /usr/Local, se producirá un error en la instalación.


#### <a name="access-the-installer-from-the-process-server"></a>Acceso al instalador desde el servidor de proceso

Vaya al servidor de proceso y navegue hasta el directorio C:\Archivos de programa (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository

Copie el archivo instalador requerido del servidor de proceso y guárdelo como latestlinuxmobsvc.tar.gz en su directorio particular


### <a name="apply-custom-configuration-changes"></a>Aplicación de cambios en la configuración personalizada

Para aplicar los cambios realizados en la configuración personalizada, siga los pasos que se indican a continuación:


1. Ejecute el siguiente comando para descomprimir el binario.
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/image16.png)
    
2. Ejecute el siguiente comando para conceder permisos.
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. Ejecute el siguiente comando para ejecutar el script.
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> Ejecute el script solo una vez en el servidor. Cierre el servidor. Reinicie el servidor después de agregar un disco como se indica en los pasos siguientes.

### <a name="add-retention-disk-to-linux-mt-vm"></a>Adición de disco de retención a la máquina virtual de destino maestro de Linux 

Siga los pasos que se mencionan más abajo para crear un disco de retención.

1. Conecte un nuevo disco de **1 TB** a la máquina virtual de destino maestro de Linux e **inicie** la máquina.

2. Invoque el comando **multipath -ll** para conocer el identificador de múltiples rutas de acceso del disco de retención.

    ```
    multipath -ll
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. Formatee la unidad y cree un sistema de archivos en la nueva unidad. 
    
    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. Una vez que finalice la creación del sistema de archivos, monte el disco de retención.
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. Finalmente, cree la entrada fstab para montar la unidad de retención durante cada arranque
    ```
    vi /etc/fstab 
    ```
    Presione [Insert] para comenzar a editar el archivo. Cree una nueva línea e inserte el siguiente texto en ella. Edite el identificador de múltiples rutas del disco basándose en el identificador de múltiples rutas resaltado del comando anterior.

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Presione [Esc] y escriba :wq (escribir y salir) para cerrar la ventana del editor.

### <a name="install-master-target"></a>Instalación del destino principal

> [!NOTE]
> La versión del servidor de destino principal debe ser menor o igual que el servidor de proceso y el servidor de configuración. Si la versión del servidor de destino es mayor, la reprotección se realizará correctamente pero se producirá un error en la replicación.
 

> [!NOTE]
> Antes de instalar el servidor de destino principal, compruebe que el archivo /etc/hosts de la máquina virtual contiene entradas que asignan el nombre de host local a las direcciones IP asociadas con todos los adaptadores de red.
 
1. Copie la frase de contraseña de **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** en el servidor de configuración y guárdela en el archivo passphrase.txt en el mismo directorio local ejecutando el comando siguiente.

    ```
    echo <passphrase> >passphrase.txt
    ```
    Ejemplo: echo itUx70I47uxDuUVY >passphrase.txt

2. Anote la dirección IP del servidor de configuración. La necesitará en el paso siguiente.

3. Ejecute el siguiente comando para instalar el servidor de destino maestro y registre el servidor con el servidor de configuración.
    
    ```
    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i <Configuration Server IP Address> -p 443 -s y -c https -P passphrase.txt
    ```

    Ejemplo: ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

    Espere hasta que finalice la ejecución del script. Si el destino maestro se ha registrado correctamente aparecerá en la página de la infraestructura de Site Recovery en el portal.

#### <a name="installing-master-target-using-interactive-install"></a>Instalación de destino maestro mediante instalación interactiva

1. Ejecute el comando siguiente para instalar el destino maestro. Elija el rol de agente "Destino principal".

    ```
    ./install
    ```

2. Elija la ubicación predeterminada para la instalación: presione [Entrar] para continuar.
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image17.png)
    

3. Elija la configuración de los valores globales.

    ![](./media/site-recovery-how-to-install-linux-master-target/image18.png)
    
4. Especifique las direcciones IP del servidor de CS.

5. Especifique el puerto del servidor de CS como 443.
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image19.png)
    
6. Copie la frase de contraseña de CS de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase en el servidor de configuración y especifíquela en el cuadro de frase de contraseña. Aparecerá vacío incluso después de copiar y pegar.

7. Vaya a [Quit] (Salir) en el menú

8. Espere a que finalice la instalación y el registro.

### <a name="install-vmware-tools-on-the-master-target-server"></a>Instalación de herramientas de VMware en el servidor de destino maestro.

Las herramientas de VMware deben instalarse en destino principal para que puedan detectar los almacenes de datos. Si no se instalan las herramientas, la pantalla de reprotección no mostrará los almacenes de datos. Debe reiniciar después de la instalación de las herramientas de VMware.

## <a name="next-steps"></a>Pasos siguientes
Una vez que el destino maestro ha completado la instalación y el registro, puede ver este destino maestro en la sección correspondiente de la infraestructura de Site Recovery, en la información general del servidor de configuración.

Ahora ya puede continuar con la [Reprotección](site-recovery-how-to-reprotect.md), seguido de la conmutación por recuperación.

## <a name="common-issues"></a>Problemas comunes

* Asegúrese de no activar Storage vMotion en ningún componente de administración como, por ejemplo, los destinos maestros. Si se mueve el destino maestro después de una reprotección correcta, el VMDK no se podrá desconectar y la conmutación por recuperación producirá un error.
* La máquina de destino maestro no debe contener ninguna instantánea en la máquina virtual. Si hay instantáneas, se producirá un error en la conmutación por recuperación.
* Debido a ciertas configuraciones de NIC personalizadas en algunos clientes, la interfaz de red está deshabilitada durante el arranque, por lo que el agente del destino maestro no se puede inicializar. Asegúrese de que se han definido correctamente las siguientes propiedades.
    * Compruebe las dos propiedades de los archivos de tarjeta de ethernet /etc/sysconfig/network-scripts/ifcfg-eth*
        * BOOTPROTO=dhcp 
        * ONBOOT=yes


