---
title: "Conmutación por recuperación de máquinas virtuales de VMware de Azure al sitio local | Microsoft Docs"
description: "Aprenda a conmutar por recuperación al sitio local después de conmutar por error las máquinas virtuales de VMware y los servidores físicos a Azure."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 5a47337f-89a9-43e8-8fdc-7da373c0fb0f
ms.service: site-recovery
ms.devlang: na
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/27/2017
ms.author: ruturajd
ms.openlocfilehash: dde0bb6b4f6bc10afdd7d40adc6689d42b37de81
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>Conmutación por recuperación de máquinas virtuales de VMware y servidores físicos al sitio local


En este artículo se describe cómo conmutar por recuperación máquinas virtuales de Azure al sitio local. Siga las instrucciones que se describen aquí cuando esté listo para conmutar por recuperación máquinas virtuales de VMware o servidores físicos de Windows o Linux después de que haya vuelto a proteger las máquinas mediante esta [referencia](site-recovery-how-to-reprotect.md).

>[!NOTE]
>Si usa el Portal de Azure clásico, consulte las instrucciones mencionadas [aquí](site-recovery-failback-azure-to-vmware-classic.md) para obtener una arquitectura mejorada de VMware a Azure y [aquí](site-recovery-failback-azure-to-vmware-classic-legacy.md) para obtener la arquitectura heredada

## <a name="overview"></a>Información general
Los diagramas de esta sección muestran la arquitectura de conmutación por recuperación en este escenario.

Cuando el servidor de procesos sea local y se esté usando una conexión de Azure ExpressRoute, use esta arquitectura:

![Diagrama de la arquitectura de ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Cuando el servidor de procesos esté en Azure y tenga una VPN o una conexión de ExpressRoute, use esta arquitectura:

![Diagrama de la arquitectura de VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Para ver la lista completa de puertos y el diagrama de la arquitectura de la conmutación por recuperación, consulte la imagen siguiente:

![Lista de conmutación por error/recuperación de todos los puertos](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Después de conmutar por error a Azure, conmuta por recuperación a su sitio local en tres fases:

* **Fase 1**: proteja de nuevo las máquinas virtuales de Azure de modo que comiencen a replicarse otra vez en las de VMware que se ejecutan en el sitio local.
* **Fase 2**: una vez que las máquinas virtuales de Azure se estén replicando en el sitio local, ejecute una conmutación por error para conmutar por recuperación desde Azure.
* **Fase 3**: después de que los datos se hayan conmutado por recuperación, vuelva a proteger las máquinas virtuales locales a las que conmutó por recuperación, para que comiencen a replicarse en Azure.

### <a name="fail-back-to-the-original-location-or-an-alternate-location"></a>Conmutación por recuperación a la ubicación original o a una ubicación alternativa
Después de conmutar por error una máquina virtual de VMware, puede conmutar por recuperación a la misma máquina virtual de origen si aún existe en el entorno local. En este escenario solo se conmutarán por recuperación las diferencias.

Si conmutó por error servidores físicos, la conmutación por recuperación siempre se hace a una nueva máquina virtual de VMware. Antes de conmutar por recuperación una máquina física, tenga en cuenta lo siguiente:
* Las máquinas físicas protegidas volverán como virtuales cuando se conmuten por recuperación de Azure a VMware. Si las máquinas físicas de Windows Server 2008 R2 SP1 están protegidas y conmutan por error a Azure, no se puede realizar la conmutación por recuperación. Las máquinas Windows Server 2008 R2 SP1 que se inicien como máquinas virtuales locales podrán realizar la conmutación por recuperación.
* Asegúrese de detectar al menos un servidor de destino principal junto con los hosts ESX/ESXi a los que necesita conmutar por recuperación.

Si conmuta por recuperación a la máquina virtual original, se necesita lo siguiente:

* Si la máquina virtual está administrada por un servidor vCenter, el host ESX del destino principal debe tener acceso al almacén de datos de máquinas virtuales.
* Si la máquina virtual está en un host ESX, pero no está administrada por vCenter, el disco duro de la máquina virtual debe estar en un almacén de datos accesible para el host de MT.
* Si la máquina virtual está en un host ESX y no usa vCenter, debe completar la detección del host ESX de MT antes de volver a protegerla. Lo mismo se aplica si también conmuta por recuperación a servidores físicos.
* Otra opción (si existe la máquina virtual local) es eliminarla antes de realizar una conmutación por recuperación. En este caso, la conmutación por recuperación crea una nueva máquina virtual en el mismo host que el host ESX de destino maestro.

Cuando se conmuta por recuperación a una ubicación alternativa, los datos se recuperan en el mismo almacén de datos y el mismo host ESX que los usados por el servidor de destino principal local.

## <a name="prerequisites"></a>Requisitos previos
* Para conmutar por recuperación máquinas virtuales de VMware y servidores físicos, necesita un entorno de VMware. No se admite la conmutación por recuperación a un servidor físico.
* Para realizar la conmutación por recuperación, debe haber creado una red de Azure cuando configuró inicialmente la protección. La conmutación por recuperación necesita una conexión VPN o ExpressRoute desde la red de Azure, en la que se encuentran las máquinas virtuales de Azure, hasta el sitio local.
* Si las máquinas virtuales que desea conmutar por recuperación las administra un servidor vCenter, asegúrese de tener los permisos necesarios para la detección de máquinas virtuales en servidores vCenter. Para obtener más información, consulte [Replicación de máquinas virtuales de VMware y servidores físicos en Azure con Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).
* Si existen instantáneas en una máquina virtual, la reprotección dará error. Puede eliminar las instantáneas o los discos.
* Antes de realizar conmutación por recuperación, cree estos componentes:
  * **Cree un servidor de procesos en Azure**. Este componente es una máquina virtual de Azure que creará y mantendrá en ejecución durante la conmutación por recuperación. Puede eliminar la máquina virtual una vez completada la operación.
  * **Cree un servidor de destino maestro**: este servidor envía y recibe datos de conmutación por recuperación. El servidor de administración que creó de forma local tiene instalado de forma predeterminada un servidor de destino principal. Sin embargo, según el volumen de tráfico conmutado por recuperación, puede que deba crear un servidor de destino principal distinto para esta operación.
  * Para crear un servidor de destino principal adicional que se ejecute en Linux, configure la máquina virtual de Linux antes de instalar al servidor de destino principal, como se describe más adelante.
* El servidor de configuración es necesario localmente cuando realiza una conmutación por recuperación. Durante la conmutación por recuperación, la máquina virtual debe encontrarse en la base de datos del servidor de configuración. Si la base de datos del servidor de configuración no contiene ninguna máquina virtual, no se puede realizar la conmutación por recuperación. Por lo tanto, asegúrese de realizar copias de seguridad programadas periódicas del servidor. En caso de desastre, tiene que restaurarla con la misma dirección IP para que funcione la conmutación por recuperación.
* Establezca la opción disk.enableUUID=true de **Parámetros de configuración** de la máquina virtual de destino principal en VMware. Si la fila no existe, agréguela. Esta configuración es necesaria para proporcionar un identificador único universal (UUID) coherente para el archivo de disco de la máquina virtual (VMDK) para que este se monte correctamente.
* Observe si se muestra la condición "Master target server cannot be storage vMotioned" (El servidor de destino principal no puede almacenarse en vMotion), que puede provocar un error en la conmutación por recuperación. La máquina virtual no puede iniciarse porque no tiene disponibles los discos.
* Agregue una unidad, llamada unidad de retención, en el servidor de destino principal. Agregue un nuevo disco y formatee la unidad.

## <a name="failback-policy"></a>Directiva de conmutación por recuperación
Para volver a replicar en el entorno local, necesitará una directiva de conmutación por recuperación. La directiva se crea automáticamente al crear una directiva de dirección de avance y se asocia automáticamente con el servidor de configuración. Esta directiva no es editable y tiene la siguiente configuración de replicación:

* Umbral RPO = 15 minutos
* Retención de punto de recuperación = 24 horas
* Frecuencia de las instantáneas coherentes con la aplicación = 60 minutos

 ![Configuración de replicación de la directiva de conmutación por recuperación](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

## <a name="set-up-a-process-server-in-azure"></a>Configuración de un servidor de procesos en Azure
Instale un servidor de procesos en Azure para que las máquinas virtuales de Azure puedan devolver los datos a un servidor de destino principal local.

Si ha protegido las máquinas virtuales como recursos clásicos (es decir, la máquina virtual recuperada en Azure se creó usando el modelo de implementación clásico), necesitará un servidor de procesos clásico en Azure. Si se han recuperado las máquinas virtuales con Azure Resource Manager como el tipo de implementación, el servidor de procesos debe tener el administrador de recursos como el tipo de implementación. El tipo de implementación se selecciona mediante la red virtual de Azure en la que se implementa el servidor de procesos.

1. En **Almacén** > **Configuración** > **Infraestructura de Site Recovery** (en **Administrar**) > **Servidores de configuración** (en **For VMware and Physical Machines** [Para VMware y máquinas físicas]), seleccione el servidor de configuración.
2. Haga clic en **Servidor de procesos**.

  ![Botón Servidor de procesos](./media/site-recovery-failback-azure-to-vmware-classic/add-processserver.png)
3. Elija la opción de implementación del servidor de procesos **Deploy a failback process server in Azure** (Implementación de un servidor de procesos de conmutación por recuperación en Azure).
4. Seleccione la suscripción en la que se han recuperado las máquinas virtuales.
5. Seleccione la red de Azure en la que se han recuperado las máquinas virtuales. El servidor de procesos debe estar en la misma red para que las máquinas virtuales recuperadas y el servidor de procesos puedan comunicarse.
6. Si ha seleccionado una red de *modelo de implementación clásica*, cree una máquina virtual con Azure Marketplace y después instale en ella el servidor de procesos.

 ![Ventana "Agregar servidores de procesos"](./media/site-recovery-failback-azure-to-vmware-classic/add-classic.png)

 Dado que está creando el servidor de procesos, preste atención a lo siguiente:
 * El nombre de la imagen es *Microsoft Azure Site Recovery Process Server V2*(Servidor de procesos de Microsoft Azure Site Recovery, versión 2). Seleccione **Clásico** como el modelo de implementación.

       ![Select "Classic" as the Process Server deployment model](./media/site-recovery-failback-azure-to-vmware-classic/templatename.png)
 * Instale el servidor de procesos siguiendo las instrucciones de [Replicación de máquinas virtuales de VMware y servidores físicos en Azure con Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).
7. Si selecciona la red de Azure *Resource Manager*, implemente el servidor de procesos indicando la siguiente información:

  * El nombre del grupo de recursos en el que desea implementar el servidor.
  * El nombre del servidor.
  * Un nombre de usuario y una contraseña para iniciar sesión en el servidor.
  * La cuenta de almacenamiento en la que desea implementar el servidor.
  * La subred y la interfaz de red a las que quiere conectarla.
   >[!NOTE]
   >Debe crear su propia [interfaz de red](../virtual-network/virtual-networks-multiple-nics.md) (NIC) y seleccionarla durante la implementación del servidor de procesos.

    ![Escribir la información en el cuadro de diálogo "Agregar servidor de procesos"](./media/site-recovery-failback-azure-to-vmware-classic/psinputsadd.png)

8. Haga clic en **Aceptar**. Esta acción desencadena un trabajo que crea una máquina virtual con el tipo de implementación de Resource Manager durante la instalación del servidor de procesos. Para registrar el servidor en el servidor de configuración, ejecute el programa de instalación dentro de la máquina virtual siguiendo las instrucciones de [Replicación de máquinas virtuales de VMware y servidores físicos en Azure con Azure Site Recovery](site-recovery-vmware-to-azure-classic.md). Se desencadenará también un trabajo para implementar el servidor de procesos.

  El servidor de procesos aparece en la pestaña **Servidores de configuración** > **Servidores asociados** > **Servidores de procesos**.

    ![](./media/site-recovery-failback-azure-to-vmware-new/pslistingincs.png)

    > [!NOTE]
    > El servidor de procesos no está visible en **Propiedades de la máquina virtual**. Solo está visible en la pestaña **Servidores** del servidor de administración en el que está registrado. Puede tardar entre 10 y 15 minutos en aparecer el servidor de procesos.


## <a name="set-up-the-master-target-server-on-premises"></a>Configuración del servidor de destino maestro local
El servidor de destino maestro recibe los datos de conmutación por recuperación. El servidor se instala automáticamente en el servidor de administración local, pero si va a conmutar por recuperación muchos datos, puede que tenga que configurar un servidor de destino principal adicional. Para configurar un servidor de destino maestro local, haga lo siguiente:

> [!NOTE]
> Para configurar un servidor de destino principal en Linux, vaya al procedimiento siguiente. Use solo el sistema operativo mínimo CentOS 6.6 como el sistema operativo de destino principal.

1. Si está configurando el servidor de destino principal en Windows, abra la página de inicio rápido de la máquina virtual en la que está instalando el servidor de destino principal.
2. Descargue el archivo de instalación del Asistente para instalación unificada de Azure Site Recovery.
3. Ejecute el programa de instalación y, en **Antes de comenzar**, seleccione **Add additional process servers to scale out deployment** (Agregar servidores de procesos adicionales para el escalado horizontal de la implementación).
4. Complete el asistente tal y como hizo cuando [instaló el servidor de administración](site-recovery-vmware-to-azure-classic.md). En la página **Configuration Server Details** (Detalles del servidor de configuración), especifique la dirección IP de este servidor de destino principal y una frase de contraseña para acceder a la máquina virtual.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Configuración de una máquina virtual de Linux como servidor de destino maestro
Para configurar el servidor de administración que ejecuta el servidor de destino principal como una máquina virtual de Linux, instale el sistema operativo mínimo CentOS 6.6. A continuación, recupere los identificadores de SCSI para todos los discos duros SCSI, instale paquetes adicionales y aplique cambios personalizados.

#### <a name="install-centos-66"></a>Instalación de CentOS 6.6

1. Instale el sistema operativo mínimo CentOS 6.6 en la máquina virtual del servidor de administración. Guarde la imagen ISO en una unidad de DVD y arranque el sistema. Omita la comprobación de medios físicos. Seleccione **Inglés de Estados Unidos** como el idioma, elija **Basic Storage Devices** (Dispositivos de almacenamiento básico), compruebe que el disco duro no tenga información importante, haga clic en **Sí** y descarte los datos. Escriba el nombre de host del servidor de administración y seleccione el adaptador de red del servidor.  En el cuadro de diálogo **Editing System** (Sistema de edición), seleccione **Conectar automáticamente** y agregue una dirección IP estática, una red y parámetros de configuración de DNS. Especifique una zona horaria. Para tener acceso al servidor de administración, escriba la contraseña raíz.
2. Cuando se le pregunte por el tipo de instalación que quiere, seleccione **Create Custom Layout** (Crear diseño personalizado) como partición. Haga clic en **Siguiente**. Seleccione **Libre** y, a continuación, haga clic en **Crear**. Cree **/**, **/var/crash** y **/home partitions** con **FS Type:** **ext4**. Cree la partición de intercambio como **FS Type: swap**(Tipo FS: intercambio).
3. Si se encuentran dispositivos ya existentes, aparecerá un mensaje de advertencia. Haga clic en **Formato** para formatear la unidad con la configuración de partición. Haga clic en **Write change to disk** (Escribir cambios en el disco) para aplicar los cambios de partición.
4. Seleccione **Install boot loader (Instalar cargador de arranque)** > **Siguiente** para instalar el cargador de arranque en la partición raíz.
5. Cuando la instalación se haya completado, haga clic en **Reiniciar**.

#### <a name="retrieve-the-scsi-ids"></a>Recuperación de los id. SCSI

1. Después de la instalación, recupere los identificadores SCSI de cada disco duro SCSI de la máquina virtual. Para ello, apague la máquina virtual del servidor de administración. En las propiedades de la máquina virtual en VMware, haga clic con el botón derecho en la entrada de la máquina virtual > **Editar configuración** > **Opciones**.
2. Seleccione **Avanzadas** > **General item** (Elemento general) y haga clic en **Configuration Parameters** (Parámetros de configuración). Esta opción no estará disponible cuando se ejecute la máquina. Para que la opción esté disponible, debe apagar la máquina.
3. Realice cualquiera de las siguientes acciones:
 * Si existe la fila **disk.EnableUUID**, asegúrese de que el valor esté establecido en **True** (distingue mayúsculas de minúsculas). Si el valor ya está establecido en True, puede cancelar y probar el comando SCSI en un sistema operativo invitado después de arrancar.
 * Si la fila **disk.EnableUUID** no existe, haga clic en **Agregar fila** y agréguela con el valor **True**. No utilice comillas dobles.

#### <a name="install-additional-packages"></a>Instalación de paquetes adicionales
Descargue e instale los paquetes adicionales.

1. Asegúrese de que el servidor de destino maestro esté conectado a Internet.
2. Para descargar e instalar 15 paquetes desde el repositorio de CentOS, ejecute este comando: `# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools`.
3. Si las máquinas de origen que va a proteger ejecutan Linux con el sistema de archivos Reiser o XFS en el dispositivo raíz o de arranque, descargue e instale paquetes adicionales de la manera siguiente:

   * \# cd /usr/local
   * \# wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
   * \# wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
   * \#rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
   * \#wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
   * \#rpm –ivh xfsprogs-3.1.1-16.el6.x86_64.rpm
   * \# yum install device-mapper-multipath (se requiere para habilitar paquetes de múltiples rutas en el servidor de destino principal)

#### <a name="apply-custom-changes"></a>Aplicación de cambios personalizados
Después de haber completado los pasos posteriores a la instalación y haber instalado los paquetes, aplique los cambios personalizados realizando estos pasos:

1. Copie al binario de RHEL 6-64 Unified Agent en la máquina virtual. Ejecute este comando para descomprimir el archivo binario: `tar –zxvf <file name>`.
2. Para conceder permisos, ejecute este comando: `# chmod 755 ./ApplyCustomChanges.sh`.
3. Ejecute el siguiente script: `# ./ApplyCustomChanges.sh`. Ejecútelo una sola vez. Después de que el script se haya ejecutado correctamente, reinicie el servidor.

## <a name="run-the-failback"></a>Ejecución de la conmutación por recuperación
### <a name="reprotect-the-azure-vms"></a>Reprotección de las máquinas virtuales de Azure
1. En **Almacén** > **Elementos replicados**, haga clic con el botón derecho en máquina virtual que ha sido objeto de la conmutación por error y seleccione **Reproteger**.
2. En la hoja, puede ver que la dirección de protección **De Azure a local** ya está seleccionada.
3. En **Servidor de destino maestro** y **Servidor de procesos**, seleccione el servidor de destino maestro local y el servidor de procesos de la máquina virtual de Azure.
4. Seleccione el almacén de datos en el que desea recuperar los discos en el entorno local. Use esta cuando elimine la máquina virtual local y sea necesario crear discos nuevos. Omita esta opción si los discos ya existen, pero debe especificar un valor.
5. Utilice una unidad de retención para detener los puntos en el tiempo cuando la máquina virtual se replica en local. A continuación se enumeran algunos criterios de una unidad de retención. Sin estos criterios no se muestra la unidad para el servidor de destino maestro.

  * El volumen no debe utilizarse para ningún otro propósito (destino de replicación, etc.).
  * El volumen no debe estar en modo de bloqueo.
  * El volumen no debe ser el volumen de memoria caché. (La instalación del destino maestro no debe existir en dicho volumen. El volumen de instalación personalizada del servidor de procesos con destino maestro no es apto para el volumen de retención. En este caso, el volumen de servidor de procesos con destino maestro es el volumen de caché del destino maestro).
  * El tipo de sistema de archivos de volumen no debe ser FAT ni FAT32.
  * La capacidad del volumen debe ser distinta de cero.
  * El volumen de retención predeterminado para Windows es el volumen R.
  * El volumen de retención predeterminado para Linux es /mnt/retention.

6. La directiva de conmutación por recuperación se selecciona automáticamente.
7. Tras hacer clic en **Aceptar** para comenzar la reprotección, comienza un trabajo para replicar la máquina virtual de Azure en el sitio local. Puede realizar el seguimiento del progreso en la pestaña **Trabajos** .

Si desea recuperar en una ubicación alternativa, seleccione la unidad de retención y el almacén de datos configurado para el servidor de destino maestro. Cuando se conmuta por recuperación al sitio local, las máquinas virtuales de VMware del plan de protección de conmutación por recuperación utilizarán el mismo almacén de datos que el servidor de destino maestro. Si desea recuperar la réplica de la máquina virtual de Azure en la misma máquina virtual local, la máquina virtual local ya debe estar en el mismo almacén de datos que el servidor de destino maestro. Si no hay ninguna máquina virtual local, se creará una nueva durante la reprotección.

![Seleccione "Azure a local" en el menú desplegable](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

También puede volver a proteger a nivel de un plan de recuperación. Si tiene un grupo de replicación, este solo se puede volver a proteger con un plan de recuperación. Cuando vuelva a proteger con un plan de recuperación, utilice los valores anteriores para todos los equipos protegidos.

> [!NOTE]
> Los grupos de replicación deben volver a protegerse con el mismo destino maestro. Si se han vuelto a proteger con servidores de destino maestro distintos, no se puede determinar un momento dado común para ellos.

### <a name="run-a-failover-to-the-on-premises-site"></a>Ejecución de una conmutación por error en el sitio local
Después de volver a proteger una máquina virtual, puede iniciar una conmutación por error desde Azure a un sitio local.

1. En la página de elementos replicados, haga clic con el botón derecho en la máquina virtual y seleccione **Conmutación por error no planeada**.
2. En **Confirmar conmutación por error**, compruebe la dirección de conmutación por error (de Azure) y seleccione el punto de recuperación que quiere usar para la conmutación por error (el último o el último coherente con la aplicación). Un punto de recuperación coherente con la aplicación se produce antes del punto más reciente en el tiempo y provocará pérdida de datos.
3. Durante la conmutación por error, se cerrarán las máquinas virtuales de Azure en Site Recovery. Después de comprobar que la conmutación por recuperación se ha completado como se esperaba, puede comprobar que las máquinas virtuales de Azure se han cerrado según lo previsto.

### <a name="reprotect-the-on-premises-site"></a>Reprotección del sitio local
Una vez finalizada la conmutación por recuperación, confirme la máquina virtual para asegurarse de que se eliminen las máquinas virtuales recuperadas en Azure. Para ello, haga clic con el botón derecho en el elemento protegido y haga clic en **Confirmar**. Esta acción desencadena un trabajo que quita las anteriores máquinas virtuales recuperadas en Azure.

Una vez completada la confirmación, los datos deberían volver al sitio local, pero no estarán protegidos. Para volver a iniciar la replicación en Azure, vuelva a hacer lo siguiente:

1. En **Almacén** > **Configuración** > **Elementos replicados**, seleccione las máquinas virtuales que han sido objeto de la conmutación por recuperación y haga clic en **Reproteger**.
2. Asigne el valor de servidor de procesos que debe utilizarse para volver a enviar datos a Azure.
3. Haga clic en **Aceptar**.

Una vez completada la reprotección, la máquina virtual se vuelve a replicar en Azure y puede realizar una conmutación por error.

### <a name="resolve-common-failback-issues"></a>Resolución de problemas habituales de la conmutación por recuperación
* Si realiza la detección de usuarios de solo lectura de vCenter y protege las máquinas virtuales, se ejecutará correctamente y la conmutación por error funcionará. Durante la reprotección, se produce un error de conmutación por error porque no se pueden detectar los almacenes de datos. El síntoma de este problema es que no se ven los almacenes de datos enumerados durante la reprotección. Para resolverlo, puede actualizar las credenciales de vCenter con la cuenta adecuada que tenga permisos y tratar de realizar el trabajo de nuevo. Para obtener más información, consulte [Replicación de máquinas virtuales de VMware y servidores físicos en Azure con Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).
* Cuando conmuta por recuperación una máquina virtual de Linux y la ejecuta localmente, verá que se ha desinstalado el paquete del administrador de red de la máquina. Esto se debe a que el paquete del administrador de red se elimina cuando se recupera la VM en Azure.
* Cuando una máquina virtual se configura con una dirección IP estática y se conmuta por error a Azure, se adquiere la dirección IP mediante DHCP. Al conmutar por error a un entorno local, la VM seguirá utilizando DHCP para obtener la dirección IP. Inicie sesión manualmente en el equipo y vuelva a establecer la dirección IP en una dirección estática si es necesario.
* Si utiliza las ediciones gratuitas de ESXi 5.5 o vSphere Hypervisor 6, la conmutación por error se llevará a cabo correctamente, pero la conmutación por recuperación, no. Para habilitar la conmutación por recuperación, tendrá que actualizar los programas con una licencia de evaluación.
* Si no puede ponerse en contacto con el servidor de configuración desde el servidor de procesos, compruebe la conectividad con el servidor de configuración por Telnet con el equipo del servidor de configuración en el puerto 443. También puede tratar de hacer ping en el servidor de configuración desde la máquina del servidor de procesos. Un servidor de procesos debe tener también un latido cuando esté conectado al servidor de configuración.
* Si está tratando de realizar la conmutación por recuperación en un vCenter alternativo, asegúrese de que se detectan tanto el nuevo vCenter como el servidor de destino maestro. Un síntoma típico es que los almacenes de datos no están accesibles ni visibles en el cuadro de diálogo de **Reprotect** (Reproteger).
* Las máquinas WS2008R2SP1 protegidas como máquinas físicas locales no pueden conmutar por recuperación desde Azure a un entorno local.

## <a name="fail-back-with-expressroute"></a>Conmutación por recuperación con ExpressRoute
Puede conmutar por recuperación a través de una conexión VPN o usando una conexión ExpressRoute. Si desea usar ExpressRoute, tenga en cuenta lo siguiente:

* La conexión ExpressRoute se debe configurar en la red virtual de Azure a la que conmutarán por error las máquinas de origen, y en la que se encuentran las máquinas virtuales de Azure después de que tenga lugar este proceso.
* Los datos se replican en una cuenta de almacenamiento de Azure en un punto de conexión público. Para usar una conexión ExpressRoute, realice la configuración entre pares públicos en ExpressRoute con el centro de datos de destino para la replicación de Site Recovery.
