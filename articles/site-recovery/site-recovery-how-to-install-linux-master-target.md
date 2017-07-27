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
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: c7c50c539149a929b15f50e4b52dc48d92534640
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017


---
# <a name="how-to-install-a-linux-master-target-server"></a>Instalación del servidor de destino maestro de Linux
Después de conmutar por error las máquinas virtuales, puede conmutarlas por recuperación en el sitio local. Para ello, debe volver a proteger la máquina virtual de Azure en el sitio local. Para realizar este proceso, necesitará un servidor de destino maestro local que reciba el tráfico. Si la máquina virtual protegida es una máquina virtual Windows, necesitará un destino maestro de Windows. Para una máquina virtual Linux, se necesita un destino maestro de Linux. Lea los pasos siguientes para aprender a crear e instalar un destino maestro de Linux.

> [!IMPORTANT]
> A partir de la versión 9.10.0 del servidor de destino maestro, el servidor de destino maestro más reciente solo puede instalarse en un servidor de Ubuntu 16.04. No se permitirán nuevas instalaciones en los servidores de CentOS 6.6. Pero todavía puede actualizar los servidores de destino maestros anteriores con la versión 9.10.0.

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

**Instalación de Ubuntu 16.04.2 mínimo**

Siga los pasos que se mencionan a continuación para instalar el sistema operativo Ubuntu 16.04.2 de 64 bits.

**Paso 1:** desde el siguiente vínculo, elija el reflejo más cercano para descargar un ISO de Ubuntu 16.04.2 mínimo de 64 bits.

<https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64>

Mantenga el ISO de Ubuntu 16.04.2 mínimo de 64 bits en la unidad de DVD y arranque el sistema.

**Paso 2:** seleccione **English** (Inglés) como idioma preferido y presione Entrar.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image1.png)

**Paso 3:** seleccione **Install Ubuntu Server** (Instalar servidor de Ubuntu) y Entrar.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image2.png)

**Paso 4:** seleccione **English** (Inglés) como idioma preferido y presione Entrar.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image3.png)

**Paso 5:** seleccione la opción adecuada de la lista de opciones **Time Zone** (Zona horaria) y presione Entrar.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image4.png)

**Paso 6:** seleccione la opción predeterminada **NO** y presione Entrar.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image5.png)

**Paso 7:** seleccione **English (US)** [Inglés (EE.UU.)] como país de origen para el teclado y presione Entrar.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image6.png)

**Paso 8:** seleccione **English (US)** [Inglés (EE.UU.)] como distribución del teclado y presione Entrar.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image7.png)

**Paso 9:** escriba el nombre de host para el servidor en el cuadro de texto **Hostname** (Nombre de host) y haga clic en el botón **Continue** (Continuar).

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image8.png)

**Paso 10:** escriba **User name** (Nombre de usuario) para crear una cuenta de usuario en el **Textbox** (Cuadro de texto) y haga clic en el botón **Continue** (Continuar).

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image9.png)

**Paso 11:** escriba **Password** (Contraseña) para la nueva cuenta de usuario en el **Text box** (Cuadro de texto) y haga clic en el botón **Continue** (Continuar).

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image10.png)

**Paso 12:** confirme **Password** (Contraseña) del nuevo usuario en el **Text box** (Cuadro de texto) y haga clic en el botón **Continue** (Continuar).

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image11.png)

**Paso 13:** seleccione la opción predeterminada **NO** y presione **Entrar**.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image12.png)

**Paso 14:** seleccione la opción predeterminada **YES** (SÍ) si la zona horaria que se muestra es correcta y presione **Entrar**.

Puede seleccionar la opción predeterminada **No** si desea volver a configurar la zona horaria.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image13.png)

**Paso 15:** seleccione la opción “**Guided -** **Use entire disk**” (Guiado: usar todo el disco) en las opciones de método de partición y presione **Entrar**.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image14.png)

**Paso 16:** seleccione el disco adecuado en las opciones de **Select disk to partition** (Seleccionar disco para la partición) y presione **Entrar**.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image15.png)

**Paso 17:** seleccione **YES** (SÍ) para escribir los cambios en discos y presione **Entrar**.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image16.png)

**Paso 18:** seleccione la opción predeterminada, seleccione Continue (Continuar) y presione Entrar.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image17.png)

**Paso 19:** seleccione la opción adecuada para las actualizaciones de administrador en el sistema y presione **Entrar**.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image18.png)

> [!WARNING]
> Dado que el servidor de destino principal de Azure Site Recovery requiere una versión muy concreta de Ubuntu, tiene que asegurarse de que las actualizaciones de kernel se deshabilitan para la máquina virtual. Si están habilitadas, las actualizaciones normales harán que el servidor de destino principal no funcione correctamente. Asegúrese de seleccionar la opción "No automatic updates" (Sin actualizaciones automáticas).


**Paso 20:** puede continuar con las opciones predeterminadas. Si quiere usar OpenSSH como conexión SSH, seleccione la opción "OpenSSH server" (Servidor de OpenSSH) y haga clic en Continue (Continuar).

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image19.png)

**Paso 21:** seleccione la opción **YES** (SÍ) y presione **Entrar**.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image20.png)

**Paso 22:** seleccione el dispositivo adecuado (preferiblemente /dev/sda) como instalación del cargador de arranque y presione **Entrar**.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image21.png)

**Paso 23:** seleccione el botón **Continue** (Continuar) y presione **Entrar** para **finalizar la instalación**.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image22.png)

Después de que finalice la instalación. Inicie sesión en la máquina virtual con las nuevas credenciales de usuario (consulte **Paso 10**).

Siga los pasos indicados en la siguiente captura de pantalla para establecer la contraseña del usuario RAÍZ e iniciar sesión como usuario RAÍZ para otras operaciones.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image23.png)


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

#### <a name="disable-kernel-upgrades"></a>**Deshabilitación de actualizaciones de kernel**

Dado que el servidor de destino principal de Azure Site Recovery requiere una versión muy concreta de Ubuntu, tiene que asegurarse de que las actualizaciones de kernel se deshabilitan para la máquina virtual. Si están habilitadas, las actualizaciones normales harán que el servidor de destino principal no funcione correctamente. Siga los pasos que se indican a continuación para deshabilitar las actualizaciones de kernel.
> [!IMPORTANT]
> Hay que poner aquí los pasos de script.

#### <a name="download-and-install-additional-packages"></a>Descarga e instalación de paquetes adicionales

> [!NOTE]
> Asegúrese de que tiene conectividad a Internet para descargar e instalar paquetes adicionales. Sin conectividad a Internet, necesitará encontrar manualmente estos paquetes RPM e instalarlos.

```
apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx
```

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
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Ejemplo: Ejemplo: /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt

    Espere a que finalice el script. Si el destino maestro se ha registrado correctamente, aparecerá en la página de infraestructura de Site Recovery en el portal.


#### <a name="install-the-master-target-by-using-interactive-install"></a>Instalación del destino maestro mediante la instalación interactiva

1. Ejecute el siguiente comando para instalar el destino maestro. Elija el rol de agente **Destino maestro**.

    ```
    ./install
    ```

2. Elija la ubicación predeterminada para la instalación y presione **Entrar** para continuar.

    ![Elección de una ubicación predeterminada para la instalación del destino maestro](./media/site-recovery-how-to-install-linux-master-target/image17.png)

Una vez finalizada la instalación, tiene que registrar el servidor de configuración mediante la línea de comandos.

1. Anote la dirección IP del servidor de configuración. La necesitará en el paso siguiente.

2. Ejecute el siguiente comando para instalar el servidor de destino maestro y registre el servidor con el servidor de configuración.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Ejemplo: Ejemplo: /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt

    Espere a que finalice el script. Si el destino maestro se ha registrado correctamente, aparecerá en la página de infraestructura de Site Recovery en el portal.


### <a name="upgrade-the-master-target"></a>Actualización del destino maestro

Ejecute al programa de instalación. Detecta automáticamente que el agente está instalado en el destino maestro. Seleccione "S" para actualizar. Cuando finalice l instalación, puede comprobar la versión del destino maestro instalado con el siguiente comando.

    ```
        cat /usr/local/.vx_version
    ```

Puede ver que el campo VERSIÓN indica el número de versión del destino maestro.

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

