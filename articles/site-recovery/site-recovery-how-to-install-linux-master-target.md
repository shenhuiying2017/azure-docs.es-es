---
title: "Instalación del servidor de destino principal de Linux | Microsoft Docs"
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
ms.date: 12/20/2016
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: d1a7ed7e182530f81a426a4383297a49505f65ea
ms.openlocfilehash: d76ea0fb27ecece4e8dcd06a2dde9a0794071884


---
# <a name="how-to-install-linux-master-target-server"></a>Instalación del servidor de destino principal de Linux
El servicio Azure Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Las máquinas se pueden replicar a Azure o a un centro de datos secundario local. Para obtener una introducción rápida, lea [¿Qué es Site Recovery?](site-recovery-overview.md)

## <a name="overview"></a>Información general
Este artículo ofrece información e instrucciones para la recuperación (conmutación por error y conmutación por recuperación) de máquinas virtuales y servidores físicos protegidos con Site Recovery.

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="pre-requisites"></a>Requisitos previos

1. Para elegir correctamente el host en el que tiene que implementar el destino principal, determine si la conmutación por recuperación va a ser para una máquina virtual existente en local o para una nueva máquina virtual (porque la máquina virtual local se eliminó). 
    * Para una máquina virtual existente, el host del destino principal debe tener acceso a almacenes de datos de la máquina virtual.
    * Si la máquina virtual no existe en local, la máquina virtual de conmutación por recuperación se creará en el mismo host del destino principal.
2. El destino principal debe estar en una red que pueda comunicarse con el servidor de proceso y el servidor de configuración.
3. La versión del destino principal debe ser menor o igual que el servidor de proceso y el servidor de configuración (ejemplo, si el servidor de configuración está en la versión 9.4, el destino principal puede estar en 9.4 y 9.3, pero no en 9.5).
4. El destino principal solo puede tener una máquina virtual de VMware y no una máquina virtual física.


## <a name="steps-to-deploy-the-master-target-server"></a>Pasos para implementar el servidor de destino principal

### <a name="install-centos-66-minimal"></a>Instalación de la versión CentOS 6.6 mínima

Siga los pasos que se mencionan a continuación para instalar CentOS 6.6: sistema operativo de 64 bits.

**Paso 1:** desde uno de los vínculos siguientes, elija el reflejo más cercano para descargar un ISO de CentOS 6.6 mínimo de 64 bits.

<http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

Mantenga el ISO de CentOS 6.6 mínimo de 64 bits en la unidad de DVD y arranque el sistema.

![](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

**Paso 2:** seleccione **Skip** (Omitir) para pasar por alto el medio de proceso de comprobación.

![](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

**Paso 3:** ahora puede ver la pantalla de bienvenida de la instalación. A continuación, haga clic en el botón **Next** (Siguiente).

![](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

**Paso 4:** seleccione **English** (Inglés) como idioma preferido y haga clic en **Next** para continuar.

![](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

**Paso 5:** seleccione **US English** (Inglés de EE. UU.) como distribución del teclado. Haga clic en **Next** (Siguiente) para continuar con la instalación.

![](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

**Paso 6:** seleccione el tipo de dispositivos en que realizará la instalación. Seleccione **Basic storage Devices** (Dispositivos de almacenamiento básico).

Haga clic en **Next** (Siguiente) para continuar con la instalación.

![](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

**Paso 7:** aparece un mensaje de advertencia que indica que se eliminarán los datos existentes en la unidad de disco duro. Asegúrese de que la unidad de disco duro no tiene ningún dato importante y haga clic en **Yes, discard any data** (Sí, eliminar todos los datos).

![](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

**Paso 8:** escriba el nombre de host para el servidor en el cuadro de texto **Hostname** (Nombre de host).
Haga clic en **Configure Network** (Configurar red).

En la ventana **Network Connection** (Conexión de red), seleccione la interfaz de red. Haga clic en el botón **Edit** (Editar) para configurar los ajustes de IPV4.

![](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

**Paso 9:** a continuación, aparece la ventana **Editing System etho** (Edición del ethos del sistema). Active la casilla **Connect automatically** (Conectarse automáticamente). En la pestaña "IPv4 Settings" (Configuración de IPv4), elija el método **Manual** y, a continuación, haga clic en el botón **Add** (Agregar). Proporcione los detalles de la dirección IP estática, la máscara de red, la puerta de enlace y el servidor DNS. Haga clic en **Apply** (Aplicar) para guardar los detalles.

![](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

**Paso 10:** seleccione la zona horaria en el cuadro combinado y haga clic en **Next** (Siguiente) para continuar.

![](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

**Paso 11:** escriba la contraseña raíz en **Root password** y confirme la contraseña, haga clic en **Next** (Siguiente) para continuar.

![](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

**Paso 12:** seleccione **Create Custom Layout** (Crear diseño personalizado) como modo de partición y haga clic en **Next** (Siguiente) para continuar.

![](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

**Paso 13:** seleccione la partición **Free** (Gratis) y haga clic en **Create** (Crear) para crear las particiones **/**, **/var/crash** y **/home** con el tipo de sistema de archivo **ext4**. Crear **Swap partition** (Intercambiar partición) con **swap** (Intercambiar) como tipo de sistema de archivo. Para asignar el tamaño de la partición, siga la fórmula de la asignación de tamaño como se menciona en la tabla siguiente.

Nota: el sistema de destino maestro Linux (MT) no debe usar LVM para los espacios de almacenamiento raíz o de retención. Destino maestro de Linux configurado para evitar la detección de discos y particiones LVM de forma predeterminada.

![](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

**Paso 14:** después de la creación de la partición, haga clic en **Next** (Siguiente) para continuar.

![](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

**Paso 15:** si no se encuentra ningún dispositivo existente previamente, aparece el mensaje de advertencia para dar formato.

Haga clic en **Format** (Format) para dar formato al disco duro con la tabla de particiones más reciente.

![](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)



**Paso 16:** haga clic en **Write change to disk** (Escribir cambios en el disco) para aplicar los cambios de partición en el disco.

![](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

**Paso 17:** active la opción **Install boot loader** (Instalar cargador de arranque) y haga clic en **Next** (Siguiente) para instalar el cargador de arranque en la partición raíz.

![](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)



**Paso 18:** inicia el proceso de instalación. Puede supervisar el progreso de instalación.

![](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

**Paso 19:** si el proceso de instalación se completa correctamente, aparece la pantalla siguiente. Haga clic en **Reboot** (Reiniciar).

![](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>Pasos posteriores a la instalación

Para obtener los Id. de SCSI de los discos duros SCSI de en una máquina virtual con Linux, debe habilitar el parámetro "disk. EnableUUID = TRUE ".

Para habilitar este parámetro, siga los pasos que se indican a continuación:

a. Apague la máquina virtual.

b. Haga clic con el botón derecho en la entrada de la máquina virtual del panel izquierdo y seleccione **Editar configuración**.

c. Haga clic en la pestaña **Opciones** .

d. Seleccione el elemento **Opciones avanzadas&gt;>Genera**l a la izquierda y haga clic en **Parámetros de configuración** en la parte derecha.

![](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

Cuando la máquina esté en ejecución, la opción "Parámetros de configuración" estará en estado desactivado". Para activar esta pestaña, apague la máquina.

e. ¿Ve si existe una fila con **disk.EnableUUID**?

  Si existe y si el valor se establece en False sobrescriba el valor con True (los valores True y False no distinguen entre mayúsculas y minúsculas).

  Si existe y está establecida en True, haga clic en Cancelar y pruebe el comando SCSI en el sistema operativo invitado después de que haya arrancado.

f. Si no existe, haga clic **Agregar fila**.

  Agregue disk.EnableUUID en la columna Nombre.

  Establezca su valor en TRUE

Nota: no agregue los valores anteriores entre comillas dobles.

![](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-the-additional-packages"></a>Descarga e instalación de los paquetes adicionales

Nota: asegúrese de que el sistema tiene conectividad a Internet antes de descargar e instalar los paquetes adicionales.

```
# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

El comando anterior descargará los quince paquetes siguientes del repositorio de CentOS 6.6 y los instalará.


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

Nota: si las máquinas protegidas de origen utilizan los sistemas de archivos Reiser o XFS para el dispositivo raíz o de inicio, los siguientes paquetes deben descargarse e instalarse en el sistema de destino principal de Linux antes de la protección.

***ReiserFS (Suse11SP3. No obstante, ReiserFS no es el sistema de archivos predeterminado en Suse11SP3)***

```
# cd /usr/local

# wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

# wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

***XFS (RHEL, CentOS 7 y versiones posteriores)***

```
# cd /usr/local

# wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

# yum install device-mapper-multipath 
```
Se requiere para habilitar paquetes de múltiples rutas en el servidor del destino principal.

### <a name="download-the-mt-installation-packages"></a>Descarga de los paquetes de instalación del destino principal

Descargue los bits de instalación de destino principal de Linux más recientes desde aquí: [https://aka.ms/latestlinuxmobsvc](https://aka.ms/latestlinuxmobsvc).

Para realizar la descarga a través de Linux, escriba lo siguiente: 

```
    # wget https://aka.ms/latestlinuxmobsvc
```

Asegúrese de que se descargue y descomprima el programa de instalación solo en el directorio de inicio. Si descomprime en into /usr/Local, se producirá un error en la instalación.

### <a name="apply-custom-configuration-changes"></a>Aplicación de cambios en la configuración personalizada

Antes de aplicar los cambios realizados en la configuración personalizada, asegúrese de que ha completado los pasos posteriores a la instalación

Para aplicar los cambios realizados en la configuración personalizada, siga los pasos que se indican a continuación:

1. Copie el binario de RHEL6-64 Unified Agent en el sistema operativo recién creado.

2. Ejecute el siguiente comando para descomprimir el binario.
    ```
    tar -zxvf <File name>
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/image16.png)
    
3. Ejecute el siguiente comando para conceder permisos.
    ```
    # chmod 755 ./ApplyCustomChanges.sh
    ```

4. Ejecute el siguiente comando para ejecutar el script.
    ```
    # ./ApplyCustomChanges.sh
    ```
Nota: ejecute el script sólo una vez en el servidor. **REINICIE** el servidor cuando el script anterior se haya ejecutado correctamente.

### <a name="add-retention-disk-to-linux-mt-vm"></a>Adición de disco de retención a la máquina virtual de destino principal de Linux 

Siga los pasos que se mencionan más abajo para crear un disco de retención.

**Paso 1:** adjunte un nuevo disco de **1 TB** a la máquina virtual de destino principal de Linux y averigüe su identificador de múltiples rutas.

Invoque el comando **multipath -ll** para conocer el identificador de múltiples rutas de acceso del disco de retención.

![](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

**Paso 2:** invoque el comando **mkfs.ext4 /dev/mapper/<id. de múltiples rutas del disco de retención>** para crear un sistema de archivos en el disco de retención.

![](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

**Paso 3:** una vez que finalice la creación del archivo de sistema, invoque los comandos siguientes para montar el disco de retención.

![](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

**Paso 4:** por último, cree la entrada fstab:
```
vi /etc/fstab 
```
y anexe la línea

** /dev/mapper/36000c2989daa2fe6dddcde67f2079afe /mnt/retention ext4 rw 0 0 **

### <a name="install-master-target"></a>Instalación del destino principal


> [!NOTE]
> La versión del servidor de destino principal debe ser menor o igual que el servidor de proceso y el servidor de configuración. Si la versión del servidor de destino es mayor, la reprotección se realizará correctamente pero se producirá un error en la replicación.
> 

> [!NOTE]
> Antes de instalar el servidor de destino principal, compruebe que el archivo /etc/hosts de la máquina virtual contiene entradas que asignan el nombre de host local a las direcciones IP asociadas con todos los adaptadores de red.
> 


1. Ejecute el comando siguiente para instalar el destino principal. Elija el rol de agente "Destino principal".
```
# ./install
```

2. Elija la ubicación predeterminada para la instalación.
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image17.png)
    

3. Elija la configuración de los valores globales.

    ![](./media/site-recovery-how-to-install-linux-master-target/image18.png)
    
4. Especifique las direcciones IP del servidor de CS.

5. Especifique el puerto del servidor de CX (normalmente 9443).
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image19.png)
    
6. Copie la frase de contraseña de CS de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase en el servidor de configuración.

7. Espere a que finalice la instalación y el registro.
### <a name="install-vmware-tools-on-the-master-target-server"></a>Instale las herramientas de VMware en el servidor de destino principal.

Las herramientas de VMware deben instalarse en destino principal para que puedan detectar los almacenes de datos. Si no se instalan las herramientas, la pantalla de reprotección no mostrará los almacenes de datos.

## <a name="next-steps"></a>Pasos siguientes
 

## <a name="common-issues"></a>Problemas comunes



<!--HONumber=Feb17_HO3-->


