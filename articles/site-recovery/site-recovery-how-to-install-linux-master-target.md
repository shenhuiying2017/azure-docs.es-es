---
title: "Instalación de un servidor de destino maestro de Linux para realizar una conmutación por error de Azure a un entorno local | Microsoft Docs"
description: "Antes de volver a proteger una máquina virtual Linux, necesita un servidor de destino maestro de Linux. Aquí le mostraremos cómo instalar uno."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 60102ebb43efc8710f102450df5b98edcb1d4b39
ms.contentlocale: es-es
ms.lasthandoff: 05/08/2017


---
# <a name="how-to-install-a-linux-master-target-server"></a>Instalación del servidor de destino maestro de Linux
Después de conmutar por error las máquinas virtuales, puede conmutarlas por recuperación en el sitio local. Para ello, debe volver a proteger la máquina virtual de Azure en el sitio local. Para realizar este proceso, necesitará un servidor de destino maestro local que reciba el tráfico. Si la máquina virtual protegida es una máquina virtual Windows, necesitará un destino maestro de Windows. Para una máquina virtual Linux, se necesita un destino maestro de Linux. Lea los pasos siguientes para aprender a crear e instalar un destino maestro de Linux.

## <a name="overview"></a>Información general
En este artículo se proporciona información e instrucciones para instalar un destino maestro de Linux.

Publique cualquier comentario o pregunta que tenga al final del artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Requisitos previos

* Para elegir correctamente el host en el que deben implementar el destino maestro, determine si la conmutación por recuperación va a ser en una máquina virtual local existente o en una nueva máquina virtual debido a que se eliminó la máquina virtual local.
    * Para una máquina virtual existente, el host del destino maestro debe tener acceso a los almacenes de datos de la máquina virtual.
    * Si la máquina virtual local no existe, se crea la máquina virtual de conmutación por recuperación en el mismo host que el destino maestro. Puede elegir cualquier host ESXi para instalar el destino maestro.
* El destino maestro debe estar en una red que pueda comunicarse con el servidor de procesos y el servidor de configuración.
* La versión del destino maestro debe ser igual o anterior a las versiones del servidor de procesos y el servidor de configuración. Por ejemplo, si la versión del servidor de configuración es 9.4, la versión del destino maestro puede ser 9.4 o 9.3, pero no 9.5.
* El destino maestro solo puede ser una máquina virtual de VMware y no un servidor físico.

## <a name="master-target-sizing-guideline"></a>Directriz de ajuste de tamaño de destino maestro

El destino maestro se debe crear siguiendo la directriz de tamaño especificada a continuación:
    * RAM: 6 GB o más
    * Tamaño del disco del sistema operativo: 100 GB o más (para instalar CentOS 6.6)
    * Tamaño adicional de disco para la unidad de retención: 1 TB
    * Núcleos de CPU: 4 núcleos o más


## <a name="steps-to-deploy-the-master-target-server"></a>Pasos para implementar el servidor de destino principal

### <a name="install-centos-66-minimal"></a>Instalación de la versión CentOS 6.6 mínima

Siga estos pasos para instalar el sistema CentOS 6.6 de 64 bits:

1. Desde los vínculos siguientes, elija el reflejo más cercano para descargar un ISO de CentOS 6.6 mínimo de 64 bits.

    <http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    Coloque el ISO de CentOS 6.6 mínimo de 64 bits en la unidad de DVD y arranque el sistema.

    ![Cuadro de diálogo de bienvenida a CentoOS 6.6](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

2. Haga clic en **Skip** (Omitir) para pasar por alto el proceso de comprobación de los medios.

    ![Selección de Skip (Omitir) para pasar por alto el proceso de comprobación de los medios](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

3. En la pantalla de bienvenida de la instalación, haga clic en el botón **Next** (Siguiente).

    ![El botón siguiente en la pantalla de bienvenida de la instalación](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

4. Seleccione **English** (Inglés) como idioma preferido y haga clic en **Next** (Siguiente).

    ![Selección de un idioma](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

5. Seleccione **US English** (Inglés [EE. UU.]) como distribución del teclado y luego haga clic en **Next** (Siguiente).

    ![Selección de la distribución del teclado inglés](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

6. Seleccione **Basic storage Devices** (Dispositivos de almacenamiento básico) y luego haga clic en **Next** (Siguiente).

    ![Selección de un dispositivo de almacenamiento](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

7. El mensaje de advertencia que aparece indica que se eliminarán los datos existentes en la unidad de disco duro. Asegúrese de que la unidad de disco duro no tenga ningún dato importante y haga clic en **Yes, discard any data** (Sí, descartar todos los datos).

    ![Advertencia sobre la eliminación de datos si continúa](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

8. Escriba el nombre de host de su servidor en el cuadro **Hostname** (Nombre de host) y luego haga clic en **Configure Network** (Configurar red). En el cuadro de diálogo **Network Connection** (Conexión de red), seleccione su interfaz de red y luego haga clic en el botón **Edit** (Editar) para configurar IPV4Settings.

    ![Selección de un nombre de host y configuración de IPV4](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

9. En el cuadro de diálogo **Editing System eth0** (Sistema de edición eth0), active la casilla **Connect automatically** (Conectar automáticamente). En la pestaña **IPv4 Settings** (Configuración de IPv4), elija **Manual** para **Method** (Método) y luego haga clic en el botón **Add** (Agregar). Proporcione los detalles de la **dirección IP estática**, la **máscara de red**, la **puerta de enlace** y el **servidor DNS**. Haga clic en **Apply** (Aplicar) para guardar los detalles.

    ![Configuración de los valores de red](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

10. Seleccione la zona horaria y, a continuación, haga clic en **Next** (Siguiente).

    ![Selección de una zona horaria](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

11. Escriba la **contraseña raíz**, confirme la contraseña y luego haga clic en **Next** (Siguiente).

    ![Adición de una contraseña](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

12. Seleccione **Create Custom Layout** (Crear diseño personalizado) y luego haga clic en **Next** (Siguiente).

    ![Selección de un tipo de instalación](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

13. Seleccione la partición **Free** (Gratis) y haga clic en **Create** (Crear) para crear las particiones **/****/var/crash** y **/home** con el tipo de sistema de archivo **ext4**. Cree una **partición de intercambio** con **swap** como el tipo de sistema de archivos. Para asignar el tamaño de la partición, siga la fórmula de asignación de tamaño de la siguiente tabla.

    > [!NOTE]
    > El servidor de destino maestro de Linux no debe usar el LVM (Logical Volume Manager) para los espacios de almacenamiento raíz o de retención. El destino maestro de Linux está configurado de forma predeterminada para evitar particiones de LVM y detecciones de disco.

    ![Tabla de nombres de partición, tamaños de partición y tipos de sistema de archivos](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

14. Después de crear la partición, haga clic en **Next** (Siguiente).

    ![Cuadro de diálogo que muestra los valores seleccionados para las particiones](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

15. Si no se encuentra ningún dispositivo existente previamente, aparece el mensaje de advertencia para dar formato. Haga clic en **Format** (Format) para dar formato a la unidad de disco duro con la tabla de particiones más reciente.

    ![Acción de clic en el botón de formato para dar formato al disco](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)

16. Haga clic en **Write changes to disk** (Escribir cambios en el disco) para aplicar al disco los cambios en la partición.

    ![Acción de clic en "Write changes to disk" (Escribir cambios en disco)](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

17. Active la opción **Install boot loader** (Instalar cargador de arranque) y haga clic en **Next** (Siguiente) para instalar el cargador de arranque en la partición raíz.

    ![Instalación del cargador de arranque en la partición raíz](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)


18. Se inicia el proceso de instalación. Puede supervisar el progreso.

    ![Cuadro de diálogo que muestra el progreso de instalación](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

19. Si el proceso de instalación se completa correctamente, aparece la pantalla siguiente. Haga clic en **Reboot** (Reiniciar).

    ![Pantalla de instalación correcta](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>Pasos posteriores a la instalación
A continuación, preparamos la máquina para configurarla como servidor de destino maestro.

Para obtener los identificadores de cada disco duro SCSI en una máquina virtual Linux, debe habilitar el parámetro **disk.EnableUUID = TRUE**.

Para habilitar este parámetro, siga estos pasos:

1. Apague la máquina virtual.

2. Haga clic con el botón derecho en la entrada de la máquina virtual del panel izquierdo y seleccione **Edit Settings.** (Editar configuración).

3. Haga clic en la pestaña **Opciones** .

4. Seleccione **Advanced (Opciones avanzadas) &gt; General** en el panel izquierdo y luego haga clic en el botón **Configuration Parameters** (Parámetros de configuración) de la derecha.

    ![Pestaña de opciones](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

    La opción **Configuration Parameters** (Parámetros de configuración) no está disponible cuando la máquina está en ejecución. Para activar esta pestaña, apague la máquina virtual.

5. Vea si ya existe una fila con **disk.EnableUUID**.

    - Si el valor existe y está establecido en **False**, cambie el valor a **True** (los valores no distinguen mayúsculas de minúsculas).

    - Si el valor existe y está establecido en **True**, haga clic en **Cancel** (Cancelar).

    - Si el valor no existe, haga clic en **Add Row** (Agregar fila).

    - Agregue **disk.EnableUUID** en la columna **Name** (Nombre) y establezca su valor en **TRUE**.

    ![Comprobación de si el disk.EnableUUID existe](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-additional-packages"></a>Descarga e instalación de paquetes adicionales

> [!NOTE]
> Asegúrese de que tiene conectividad a Internet para descargar e instalar paquetes adicionales. Sin conectividad a Internet, necesitará encontrar manualmente estos paquetes RPM e instalarlos.

```
yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

El comando anterior descarga los siguientes 15 paquetes del repositorio de CentOS 6.6 y los instala. Si no tiene acceso a Internet, deberá descargar los siguientes paquetes RPM:


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
> Si las máquinas protegidas de origen usan sistemas de archivos ReiserFS o XFS para el dispositivo raíz o de inicio, debe descargar e instalar los siguientes paquetes adicionales en el destino maestro de Linux antes de la protección.


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
Se requiere para habilitar paquetes de múltiples rutas en el destino maestro.

### <a name="get-the-installer-for-setup"></a>Obtención del instalador para la configuración

Si el destino maestro tiene conectividad a Internet, puede usar los pasos siguientes para descargar el instalador. En caso contrario, puede copiar el instalador del servidor de procesos e instalarlo.

#### <a name="download-the-master-target-installation-packages"></a>Descarga de los paquetes de instalación del destino maestro

[Descargue los bits más recientes de instalación del destino maestro de Linux](https://aka.ms/latestlinuxmobsvc).

Para descargarlos mediante Linux, escriba:

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

Asegúrese de que descarga y descomprime el instalador en el directorio principal. Si lo descomprime en /usr/Local, se producirá un error en la instalación.


#### <a name="access-the-installer-from-the-process-server"></a>Acceso al instalador desde el servidor de procesos

1. Vaya a C:\Archivos de programa (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository en el servidor de procesos.

2. Copie el archivo instalador requerido del servidor de procesos y guárdelo como latestlinuxmobsvc.tar.gz en su directorio principal.


### <a name="apply-custom-configuration-changes"></a>Aplicación de cambios en la configuración personalizada

Para aplicar cambios de configuración personalizados, siga estos pasos:


1. Ejecute el siguiente comando para descomprimir el archivo binario.
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```
    ![Captura de pantalla del comando ejecutado](./media/site-recovery-how-to-install-linux-master-target/image16.png)

2. Ejecute el comando siguiente para conceder permiso.
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. Ejecute el siguiente comando para ejecutar el script.
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> Ejecute el script sólo una vez en el servidor. Cierre el servidor. Reinicie el servidor después de agregar un disco, como se describe en los pasos siguientes.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Adición de un disco de retención a la máquina virtual de destino maestro de Linux

Use los pasos siguientes para crear un disco de retención:

1. Conecte un nuevo disco **1-TB** a la máquina virtual de destino maestro de Linux y **arranque** la máquina.

2. Use el comando **multipath -ll** para conocer el id. de múltiples rutas del disco de retención.

    ```
    multipath -ll
    ```

    ![El identificador de múltiples rutas del disco de retención](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. Formatee la unidad y cree un sistema de archivos en la nueva unidad.

    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![Creación de un sistema de archivos en la unidad](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. Después de crear el sistema de archivos, monte el disco de retención.
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

    ![Montaje del disco de retención](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. Cree la entrada **fstab** para montar la unidad de retención durante cada arranque.
    ```
    vi /etc/fstab
    ```
    Presione **Insert** (Insertar) para comenzar a editar el archivo. Cree una nueva línea e inserte el siguiente texto. Edite el identificador de múltiples rutas del disco basándose en el identificador de múltiples rutas resaltado del comando anterior.

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Presione **Esc** y escriba **:wq** (escribir y salir) para cerrar la ventana del editor.

### <a name="install-the-master-target"></a>Instalación del destino maestro

> [!IMPORTANT]
> La versión del servidor de destino maestro debe ser igual o anterior a las versiones del servidor de procesos y el servidor de configuración. Si no se cumple esta condición, la reprotección se realizará correctamente, pero se producirá un error en la replicación.


> [!NOTE]
> Antes de instalar el servidor de destino maestro, compruebe que el archivo /etc/hosts de la máquina virtual contiene entradas que asignan el nombre de host local a las direcciones IP asociadas con todos los adaptadores de red.

1. Copie la frase de contraseña de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase en el servidor de configuración y guárdela en el archivo passphrase.txt en el mismo directorio local ejecutando el comando siguiente.

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

    Espere a que finalice el script. Si el destino maestro se ha registrado correctamente, aparecerá en la página de infraestructura de Site Recovery en el portal.

#### <a name="install-the-master-target-by-using-interactive-install"></a>Instalación del destino maestro mediante la instalación interactiva

1. Ejecute el siguiente comando para instalar el destino maestro. Elija el rol de agente **Destino maestro**.

    ```
    ./install
    ```

2. Elija la ubicación predeterminada para la instalación y presione **Entrar** para continuar.

    ![Elección de una ubicación predeterminada para la instalación del destino maestro](./media/site-recovery-how-to-install-linux-master-target/image17.png)


3. Elija la configuración **global**.

    ![Configuración global](./media/site-recovery-how-to-install-linux-master-target/image18.png)

4. Especifique la dirección IP del servidor de configuración.

5. Especifique el puerto del servidor de configuración 443.

    ![Especificación de la dirección IP y el puerto del servidor de configuración](./media/site-recovery-how-to-install-linux-master-target/image19.png)

6. Copie la frase de contraseña del servidor de configuración de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase en el servidor de configuración y péguela en el cuadro **Passphrase** (Frase de contraseña). El cuadro estará vacío, incluso después de pegar el texto.

7. Vaya a **Quit** (Salir) en el menú.

8. Espere a que finalice la instalación y el registro.

### <a name="install-vmware-tools-on-the-master-target-server"></a>Instale las herramientas de VMware en el servidor de destino principal.

Debe instalar las herramientas de VMware en el destino maestro para poder detectar los almacenes de datos. Si no se instalan las herramientas, la pantalla de reprotección no mostrará los almacenes de datos. Deberá reiniciar después de la instalación de las herramientas de VMware.

## <a name="next-steps"></a>Pasos siguientes
Una vez que el destino maestro haya finalizado la instalación y el registro, puede verlo en la sección **Destino maestro** de la **infraestructura de Site Recovery**, en la información general del servidor de configuración.

Ahora ya puede continuar con la [reprotección](site-recovery-how-to-reprotect.md), seguido de la conmutación por recuperación.

## <a name="common-issues"></a>Problemas comunes

* Asegúrese de no activar Storage vMotion en ningún componente de administración como, por ejemplo, un destino maestro. Si el destino maestro se mueve después de un reprotección correcta, no se podrán desasociar los discos de máquina virtual (VMDK) y se producirá un error en la conmutación por recuperación.
* La máquina de destino maestro no debe contener ninguna instantánea en la máquina virtual. Si hay instantáneas, se producirá un error en la conmutación por recuperación.
* Debido a ciertas configuraciones de NIC personalizadas en algunos clientes, la interfaz de red está deshabilitada durante el inicio, por lo que el agente del destino maestro no se puede inicializar. Asegúrese de que se han definido correctamente las siguientes propiedades. Compruebe estas propiedades en /etc/sysconfig/network-scripts/ifcfg-eth* del archivo de la tarjeta Ethernet.
    * BOOTPROTO=dhcp
    * ONBOOT=yes

