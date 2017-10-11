---
title: "Instalación de un servidor de destino maestro de Linux para realizar una conmutación por error de Azure a un entorno local | Microsoft Docs"
description: "Antes de volver a proteger una máquina virtual Linux, necesita un servidor de destino maestro Linux. Aquí le mostraremos cómo instalar uno."
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
ms.date: 08/11/2017
ms.author: ruturajd
ms.openlocfilehash: 5341e3e56e0c366079958dd9a885f6ee3e8436cb
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="install-a-linux-master-target-server"></a>Instalación de un servidor de destino maestro Linux
Después de conmutar por error las máquinas virtuales, puede conmutarlas por recuperación en el sitio local. Para ello, debe volver a proteger la máquina virtual de Azure en el sitio local. Para realizar este proceso, necesitará un servidor de destino maestro local que reciba el tráfico. 

Si la máquina virtual protegida es una máquina virtual Windows, necesitará un destino maestro de Windows. Para una máquina virtual Linux, se necesita un destino maestro de Linux. Lea los pasos siguientes para aprender a crear e instalar un destino maestro de Linux.

> [!IMPORTANT]
> A partir de la versión 9.10.0 del servidor de destino maestro, el servidor de destino maestro más reciente solo puede instalarse en un servidor Ubuntu 16.04. Las nuevas instalaciones no están permitidas en servidores CentOS6.6. Pero puede seguir actualizando los servidores de destino maestros anteriores con la versión 9.10.0.

## <a name="overview"></a>Información general
En este artículo se proporcionan instrucciones para instalar un destino maestro Linux.

Publique cualquier comentario o pregunta que tenga al final del artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Requisitos previos

* Para elegir el host en el que se va a implementar el destino maestro, determine si la conmutación por recuperación va a ser en una máquina virtual local existente o en una nueva máquina virtual. 
    * En el caso de una máquina virtual existente, el host del destino maestro debe tener acceso a los almacenes de datos de la máquina virtual.
    * Si la máquina virtual local no existe, se crea la máquina virtual de conmutación por recuperación en el mismo host que el destino maestro. Puede elegir cualquier host ESXi para instalar el destino maestro.
* El destino maestro debe estar en una red que pueda comunicarse con el servidor de procesos y el servidor de configuración.
* La versión del destino maestro debe ser igual o anterior a las versiones del servidor de procesos y el servidor de configuración. Por ejemplo, si la versión del servidor de configuración es 9.4, la versión del destino maestro puede ser 9.4 o 9.3, pero no 9.5.
* El destino maestro solo puede ser una máquina virtual de VMware y no un servidor físico.

## <a name="create-the-master-target-according-to-the-sizing-guidelines"></a>Creación del destino maestro según las directrices de ajuste de tamaño

Cree el destino maestro conforme a las siguientes directrices de ajuste de tamaño:
- **RAM**: 6 GB o más
- **Tamaño del disco del sistema operativo**: 100 GB o más (para instalar CentOS6.6)
- **Tamaño adicional de disco para la unidad de retención**: 1 TB
- **Núcleos de CPU**: 4 núcleos o más

Se admiten los siguientes kernels de Ubuntu.


|Serie de kernel  |Admite hasta la versión  |
|---------|---------|
|4.4.      |4.4.0-81-generic         |
|4.8      |4.8.0-56-generic         |
|4.10     |4.10.0-24-generic        |


## <a name="deploy-the-master-target-server"></a>Implementación del servidor de destino maestro

### <a name="install-ubuntu-16042-minimal"></a>Instalación de Ubuntu 16.04.2 mínima

Siga los siguientes pasos para instalar el sistema operativo Ubuntu 16.04.2 de 64 bits.

**Paso 1:** vaya al [vínculo de descarga](https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64) y elija el reflejo más cercano para descargar una imagen ISO de 64 bits mínima de Ubuntu 16.04.2.

Coloque una imagen ISO de 64 bits mínima de Ubuntu 16.04.2 en la unidad de DVD e inicie el sistema.

**Paso 2:** seleccione **English** (Inglés) como idioma preferido y luego **Entrar**.

![Selección de un idioma](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image1.png)

**Paso 3:** seleccione **Install Ubuntu Server** (Instalar servidor Ubuntu) y luego **Entrar**.

![Selección de instalación de servidor Ubuntu](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image2.png)

**Paso 4:** seleccione **English** (Inglés) como idioma preferido y luego **Entrar**.

![Selección de inglés como idioma preferido](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image3.png)

**Paso 5:** seleccione la opción adecuada de la lista de opciones **Time Zone** (Zona horaria) y luego **Entrar**.

![Selección de la zona horaria correcta](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image4.png)

**Paso 6:** seleccione **No** (opción predeterminada) y luego **Entrar**.


![Configuración del teclado](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image5.png)

**Paso 7:** seleccione **English (US)** [Inglés (EE. UU.)] como país de origen para el teclado y luego **Entrar**.

![Selección de EE. UU. como país de origen](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image6.png)

**Paso 8:** seleccione **English (US)** [Inglés (EE. UU.)] como distribución del teclado y luego **Entrar**.

![Selección de inglés de EE. UU. como distribución del teclado](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image7.png)

**Paso 9:** escriba el nombre de host del servidor en el cuadro **Hostname** (Nombre de host) y luego seleccione **Continue** (Continuar).

![Especificación del nombre de host del servidor](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image8.png)

**Paso 10:** para crear una cuenta de usuario, escriba el nombre de usuario y luego seleccione **Continue** (Continuar).

![Crea una cuenta de usuario](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image9.png)

**Paso 11:** escriba la contraseña de la nueva cuenta de usuario y luego seleccione **Continue** (Continuar).

![Especificación de la contraseña](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image10.png)

**Paso 12:** confirme la contraseña del nuevo usuario y luego seleccione **Continue** (Continuar).

![Confirmación de las contraseñas](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image11.png)

**Paso 13:** seleccione **No** (opción predeterminada) y luego **Entrar**.

![Configuración de usuarios y contraseñas](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image12.png)

**Paso 14:** si la zona horaria que se muestra es correcta, seleccione **Sí** (opción predeterminada) y luego **Entrar**.

Para volver a configurar la zona horaria, seleccione **No**.

![Configuración del reloj](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image13.png)

**Paso 15:** en las opciones de método de partición, seleccione **Guided - use entire disk** (Guiado: usar todo el disco) y luego **Entrar**.

![Selección de la opción de método de partición](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image14.png)

**Paso 16:** seleccione el disco adecuado en las opciones de **Select disk to partition** (Seleccionar disco para la partición) y luego **Entrar**.


![Selección del disco](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image15.png)

**Paso 17:** seleccione **Sí** para escribir los cambios en el disco y luego **Entrar**.

![Escritura de los cambios en el disco](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image16.png)

**Paso 18:** seleccione la opción predeterminada, seleccione **Continue** (Continuar) y luego **Entrar**.

![Selección de la opción predeterminada](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image17.png)

**Paso 19:** seleccione la opción adecuada para administrar las actualizaciones en el sistema y luego **Entrar**.

![Selección de forma de administrar las actualizaciones](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image18.png)

> [!WARNING]
> Dado que el servidor de destino maestro de Azure Site Recovery exige una versión muy concreta de Ubuntu, tiene que asegurarse de que las actualizaciones del kernel se deshabiliten para la máquina virtual. Si están habilitadas, las actualizaciones normales hacen que el servidor de destino maestro no funcione correctamente. Asegúrese de seleccionar la opción **No automatic updates** (Sin actualizaciones automáticas).


**Paso 20:** seleccione las opciones predeterminadas. Si quiere usar OpenSSH para la conexión SSH, seleccione la opción **OpenSSH server** (Servidor de OpenSSH) y luego **Continue** (Continuar).

![Selección de software](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image19.png)

**Paso 21:** seleccione **Sí** y luego **Entrar**.

![Instalación del cargador de arranque GRUB](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image20.png)

**Paso 22:** seleccione el dispositivo adecuado para la instalación del cargador de arranque (preferiblemente **/dev/sda**) y luego **Entrar**.

![Selección de un dispositivo para la instalación del cargador de arranque](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image21.png)

**Paso 23:** seleccione **Continue** (Continuar) y luego **Entrar** para finalizar la instalación.

![Fin de la instalación](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image22.png)

Una vez finalizada la instalación, inicie sesión en la máquina virtual con las credenciales del nuevo usuario. (Vea el **paso 10** para más información).

Siga los pasos que se indican en la siguiente captura de pantalla para establecer la contraseña del usuario raíz. Luego inicie sesión como usuario raíz.

![Establecimiento de la contraseña del usuario raíz](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image23.png)


### <a name="prepare-the-machine-for-configuration-as-a-master-target-server"></a>Preparación del equipo para configurarlo como servidor de destino maestro
Ahora prepare el equipo para configurarlo como servidor de destino maestro.

Para obtener el identificador de cada disco duro SCSI de una máquina virtual Linux, habilite el parámetro **disk.EnableUUID = TRUE**.

Para habilitar este parámetro, siga estos pasos:

1. Apague la máquina virtual.

2. Haga clic con el botón derecho en la entrada de la máquina virtual del panel izquierdo y luego seleccione **Editar configuración**.

3. Seleccione la pestaña **Opciones**.

4. En el panel izquierdo, seleccione **Opciones avanzadas** > **General** y luego el botón **Parámetros de configuración** de la parte inferior derecha de la pantalla.

    ![Pestaña de opciones](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

    La opción **Configuration Parameters** (Parámetros de configuración) no está disponible cuando la máquina está en ejecución. Para activar esta pestaña, apague la máquina virtual.

5. Vea si ya existe una fila con **disk.EnableUUID**.

    - Si el valor existe y está establecido en **False**, cámbielo a **True**. (Los valores no distinguen mayúsculas de minúsculas).

    - Si el valor existe y está establecido en **True**, seleccione **Cancelar**.

    - Si el valor no existe, seleccione **Agregar fila**.

    - En la columna de nombre, agregue **disk.EnableUUID** y luego establezca el valor en **TRUE**.

    ![Comprobación de si el disk.EnableUUID existe](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="disable-kernel-upgrades"></a>Deshabilitación de actualizaciones de kernel

El servidor de destino maestro de Azure Site Recovery exige una versión muy concreta de Ubuntu, así que asegúrese de que las actualizaciones del kernel se deshabiliten para la máquina virtual.

Si están habilitadas, las actualizaciones normales hacen que el servidor de destino maestro no funcione correctamente.

#### <a name="download-and-install-additional-packages"></a>Descarga e instalación de paquetes adicionales

> [!NOTE]
> Asegúrese de que tiene conectividad a Internet para descargar e instalar paquetes adicionales. Si no tiene conectividad a Internet, tiene que encontrar manualmente estos paquetes RPM e instalarlos.

```
apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx
```

### <a name="get-the-installer-for-setup"></a>Obtención del instalador para la configuración

Si el destino maestro tiene conectividad a Internet, puede usar los pasos siguientes para descargar el instalador. De lo contrario, puede copiar el instalador del servidor de procesos y luego instalarlo.

#### <a name="download-the-master-target-installation-packages"></a>Descarga de los paquetes de instalación del destino maestro

[Descargue los bits más recientes de instalación del destino maestro de Linux](https://aka.ms/latestlinuxmobsvc).

Para descargarlos mediante Linux, escriba:

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

Asegúrese de que descarga y descomprime el instalador en el directorio principal. Si lo descomprime en **/usr/Local**, se produce un error en la instalación.


#### <a name="access-the-installer-from-the-process-server"></a>Acceso al instalador desde el servidor de procesos

1. En el servidor de procesos, vaya a **C:\Archivos de programa (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Copie el archivo necesario del instalador del servidor de procesos y guárdelo como **latestlinuxmobsvc.tar.gz** en el directorio principal.


### <a name="apply-custom-configuration-changes"></a>Aplicación de cambios en la configuración personalizada

Para aplicar cambios de configuración personalizados, siga estos pasos:


1. Ejecute el siguiente comando para descomprimir el archivo binario.
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```
    ![Captura de pantalla del comando que se va a ejecutar](./media/site-recovery-how-to-install-linux-master-target/image16.png)

2. Ejecute el comando siguiente para conceder permiso.
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. Ejecute el siguiente comando para ejecutar el script.
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> Ejecute el script sólo una vez en el servidor. Cierre el servidor. Después de agregar un disco, reinicie el servidor como se explica en la siguiente sección.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Adición de un disco de retención a la máquina virtual de destino maestro de Linux

Use los pasos siguientes para crear un disco de retención:

1. Conecte un nuevo disco de 1 TB a la máquina virtual de destino maestro Linux y luego iníciela.

2. Use el comando **multipath -ll** para conocer el identificador de múltiples rutas del disco de retención.

    ```
    multipath -ll
    ```
    ![El identificador de múltiples rutas del disco de retención](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. Aplique formato a la unidad y luego cree un sistema de archivos en ella.

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

5. Cree la entrada **fstab** para montar la unidad de retención cada vez que se inicie el sistema.
    ```
    vi /etc/fstab
    ```
    Seleccione **Insertar** para comenzar a editar el archivo. Cree una nueva línea y luego inserte el siguiente texto. Edite el identificador de múltiples rutas del disco basándose en el identificador de múltiples rutas resaltado del comando anterior.

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Seleccione **Esc** y luego escriba **:wq** (escribir y salir) para cerrar la ventana del editor.

### <a name="install-the-master-target"></a>Instalación del destino maestro

> [!IMPORTANT]
> La versión del servidor de destino maestro debe ser igual o anterior a las versiones del servidor de procesos y el servidor de configuración. Si no se cumple esta condición, la reprotección se realiza correctamente, pero se produce un error en la replicación.


> [!NOTE]
> Antes de instalar el servidor de destino maestro, compruebe que el archivo **/etc/hosts** de la máquina virtual contiene entradas que asignan el nombre de host local a las direcciones IP asociadas a todos los adaptadores de red.

1. Copie la frase de contraseña de **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** en el servidor de configuración. Luego guarde como **passphrase.txt** en el mismo directorio local al ejecutar el comando siguiente:

    ```
    echo <passphrase> >passphrase.txt
    ```
    Ejemplo: 
    
    ```
    echo itUx70I47uxDuUVY >passphrase.txt
    ```

2. Anote la dirección IP del servidor de configuración. La necesitará en el paso siguiente.

3. Ejecute el siguiente comando para instalar el servidor de destino maestro y registre el servidor con el servidor de configuración.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Ejemplo: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

    Espere a que finalice el script. Si el destino maestro se registra correctamente, aparece en la página **Infraestructura de Site Recovery** del portal.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Instalación del destino maestro mediante la instalación interactiva

1. Ejecute el siguiente comando para instalar el destino maestro. Para el rol de agente, elija **Destino maestro**.

    ```
    ./install
    ```

2. Elija la ubicación predeterminada para la instalación y luego seleccione **Entrar** para continuar.

    ![Elección de una ubicación predeterminada para la instalación del destino maestro](./media/site-recovery-how-to-install-linux-master-target/image17.png)

Una vez finalizada la instalación, registre el servidor de configuración mediante la línea de comandos.

1. Anote la dirección IP del servidor de configuración. La necesitará en el paso siguiente.

2. Ejecute el siguiente comando para instalar el servidor de destino maestro y registre el servidor con el servidor de configuración.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Ejemplo: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

   Espere a que finalice el script. Si el destino maestro se ha registrado correctamente, aparece en la página **Infraestructura de Site Recovery** del portal.


### <a name="upgrade-the-master-target"></a>Actualización del destino maestro

Ejecute al programa de instalación. Detecta automáticamente que el agente está instalado en el destino maestro. Para actualizar, seleccione **Y**.  Una vez finalizada la instalación, compruebe la versión del destino maestro instalado mediante el siguiente comando.

    ```
    cat /usr/local/.vx_version
    ```

Puede ver que el campo **Versión** indica el número de versión del destino maestro.

### <a name="install-vmware-tools-on-the-master-target-server"></a>Instale las herramientas de VMware en el servidor de destino principal.

Debe instalar las herramientas de VMware en el destino maestro para poder detectar los almacenes de datos. Si las herramientas no están instaladas, la pantalla de reprotección no aparece en los almacenes de datos. Después de la instalación de las herramientas de VMware, tiene que reiniciar.

## <a name="next-steps"></a>Pasos siguientes
Una vez que han terminado la instalación y el registro del destino maestro, puede ver este en la sección **Destino maestro** de **Infraestructura de Site Recovery**, en la información general del servidor de configuración.

Ahora ya puede continuar con la [reprotección](site-recovery-how-to-reprotect.md), seguido de la conmutación por recuperación.

## <a name="common-issues"></a>Problemas comunes

* Asegúrese de no activar Storage vMotion en ningún componente de administración como, por ejemplo, un destino maestro. Si el destino maestro se mueve después de un reprotección correcta, no se pueden desasociar los discos de máquina virtual (VMDK). En este caso se produce un error en la conmutación por recuperación.

* La máquina de destino maestro no debe contener ninguna instantánea en la máquina virtual. Si hay instantáneas, se produce un error en la conmutación por recuperación.

* Debido a ciertas configuraciones de NIC personalizadas en algunos clientes, la interfaz de red está deshabilitada durante el inicio, por lo que el agente del destino maestro no se puede inicializar. Asegúrese de que se han definido correctamente las siguientes propiedades. Compruebe estas propiedades en /etc/sysconfig/network-scripts/ifcfg-eth* del archivo de la tarjeta Ethernet.
    * BOOTPROTO=dhcp
    * ONBOOT=yes
